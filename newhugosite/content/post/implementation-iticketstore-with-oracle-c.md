+++
date = 2022-10-19T13:13:56Z
description = ""
draft = true
slug = "implementation-iticketstore-with-oracle-c"
title = "Implementation ITicketStore with Oracle C#"

+++




```sql
CREATE TABLE "MET_TICKET" 
(	
  "ID_TICKET" NUMBER, 
  "BB_TICKET" BLOB, 
  "NM_TICKET" VARCHAR2(120 BYTE), 
  "DT_INS" TIMESTAMP (6), 
  "DT_UPD" TIMESTAMP (6), 
  "DT_DEL" TIMESTAMP (6), 
  "BL_DEL" NUMBER
)
```

```csharp
using System.Globalization;

namespace TicketStore.Oracle;

internal sealed class SqlQueries
{
    const string _storeOrRenewFormat = @"
MERGE INTO {0} M USING  
(
    SELECT :NM_TICKET AS NM_TICKET FROM DUAL
) S ON (M.NM_TICKET = S.NM_TICKET AND BL_DEL = 0)
WHEN MATCHED THEN 
UPDATE SET M.BB_TICKET = :BB_TICKET, M.DT_UPD = SYSDATE
WHEN NOT MATCHED THEN INSERT 
(
    ID_TICKET
    , BB_TICKET
    , NM_TICKET
    , DT_INS
    , BL_DEL
) VALUES 
(
    {1}.NEXTVAL
    , :BB_TICKET
    , :NM_TICKET
    , SYSDATE
    , 0
)";

    const string _removeFormat = @"
UPDATE {0}
SET BL_DEL = 1, DT_DEL = SYSDATE
WHERE BL_DEL = 0 
AND NM_TICKET = :NM_TICKET";

    const string _retrive = @"
SELECT BB_TICKET FROM {0} WHERE NM_TICKET = :NM_TICKET AND BL_DEL = 0";

    public SqlQueries(string schemaName, string tableName, string sequenceName)
    {
        var tableNameWithSchema = string.Format(
            CultureInfo.InvariantCulture,
            "{0}.{1}", DelimitIdentifier(schemaName), DelimitIdentifier(tableName));

        // when retrieving an item, we do an UPDATE first and then a SELECT
        Store = string.Format(CultureInfo.InvariantCulture, _storeOrRenewFormat, tableNameWithSchema, sequenceName);

        Renew = string.Format(CultureInfo.InvariantCulture, _storeOrRenewFormat, tableNameWithSchema, sequenceName);

        Remove = string.Format(CultureInfo.InvariantCulture, _removeFormat, tableNameWithSchema);

        Retrive = string.Format(CultureInfo.InvariantCulture, _retrive, tableNameWithSchema);
    }

    public string Store { get; private set; }

    public string Renew { get; private set; }

    public string Remove { get; private set; }

    public string Retrive { get; private set; }

    // From EF's SqlServerQuerySqlGenerator
    private string DelimitIdentifier(string identifier)
    {
        return identifier;
    }
}

```

```csharp
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.Cookies;
using System.Security.Claims;

namespace TicketStore.Oracle;

public class OracleTicketStore : ITicketStore
{
    readonly Func<ClaimsPrincipal, string> _idSelector;

    readonly DatabaseOperations _dbOperation;

    public OracleTicketStore(OracleTicketStoreOptions options)
    {
        var sequenceName = options.SequenceName ?? throw new ArgumentNullException(nameof(options.SequenceName));
        var tableName = options.TableName ?? throw new ArgumentNullException(nameof(options.TableName));
        var schemaName = options.SchemaName ?? throw new ArgumentNullException(nameof(options.SchemaName));
        var connString = options.ConnectionString ?? throw new ArgumentNullException(nameof(options.ConnectionString));

        _idSelector = options.FnKeySelector;

        _dbOperation = new DatabaseOperations(connString, schemaName, tableName, sequenceName);
    }

    static byte[] Serialize(AuthenticationTicket ticket)
    {
        var byteTicket = TicketSerializer.Default.Serialize(ticket);
        return byteTicket;
    }

    static AuthenticationTicket Deserialize(byte[] rawTicket)
    {
        var ticket = TicketSerializer.Default.Deserialize(rawTicket);
        return ticket;
    }

    public async Task<string> StoreAsync(AuthenticationTicket ticket)
    {
        var id = _idSelector(ticket.Principal);
        var blobTicket = Serialize(ticket);

        return await _dbOperation.StoreAsync(id, blobTicket);
    }

    public async Task RenewAsync(string key, AuthenticationTicket ticket)
    {
        var id = _idSelector(ticket.Principal);
        var blobTicket = Serialize(ticket);

        await _dbOperation.RenewAsync(id, blobTicket);
    }

    public async Task<AuthenticationTicket> RetrieveAsync(string key)
    {
        byte[] blobTicket = await _dbOperation.RetrieveAsync(key);

        if (blobTicket.LongLength == 0) return default;

        var ticket = Deserialize(blobTicket);

        return ticket;
    }

    public async Task RemoveAsync(string key)
    {
        await _dbOperation.RemoveAsync(key);
    }
}

```



```csharp
using Oracle.ManagedDataAccess.Client;
using System.Data;

namespace TicketStore.Oracle;

public class DatabaseOperations
{
    internal SqlQueries SqlQueries { get; }

    readonly string _connectionString;

    public DatabaseOperations(string connectionString, string schemaName, string tableName, string sequenceName)
    {
        _connectionString = connectionString ?? throw new ArgumentNullException(nameof(connectionString));

        SqlQueries = new SqlQueries(schemaName, tableName, sequenceName);
    }

    OracleConnection Connection() => new OracleConnection(_connectionString);

    OracleCommand Command(string query, OracleConnection connection) => new OracleCommand(query, connection);

    internal Task<string> StoreAsync(string id, byte[] blobTicket)
    {
        using var conn = Connection();
        using var cmd = Command(SqlQueries.Store, conn);

        var pId = cmd.CreateParameter();
        pId.ParameterName = ":NM_TICKET";
        pId.Value = id;
        pId.DbType = DbType.String;
        pId.OracleDbType = OracleDbType.Varchar2;
        pId.Direction = ParameterDirection.Input;

        cmd.Parameters.Add(pId);

        var pBlob = cmd.CreateParameter();
        pBlob.ParameterName = ":BB_TICKET";
        pBlob.Value = blobTicket;
        pBlob.Direction = ParameterDirection.Input;
        pBlob.DbType = DbType.Binary;
        pBlob.OracleDbType = OracleDbType.Blob;

        cmd.Parameters.Add(pBlob);

        cmd.Connection.Open();

        cmd.ExecuteNonQuery();

        cmd.Connection.Close();

        return Task.FromResult(id);
    }

    internal Task RenewAsync(string id, byte[] blobTicket)
    {
        using var conn = Connection();
        using var cmd = Command(SqlQueries.Renew, conn);

        var pId = cmd.CreateParameter();
        pId.ParameterName = ":NM_TICKET";
        pId.Value = id;
        pId.DbType = DbType.String;
        pId.OracleDbType = OracleDbType.Varchar2;
        pId.Direction = ParameterDirection.Input;

        cmd.Parameters.Add(pId);

        var pBlob = cmd.CreateParameter();
        pBlob.ParameterName = ":BB_TICKET";
        pBlob.Value = blobTicket;
        pBlob.Direction = ParameterDirection.Input;
        pBlob.DbType = DbType.Binary;
        pBlob.OracleDbType = OracleDbType.Blob;

        cmd.Parameters.Add(pBlob);

        cmd.Connection.Open();

        cmd.ExecuteNonQuery();

        cmd.Connection.Close();

        return Task.CompletedTask;
    }

    internal Task<byte[]> RetrieveAsync(string key)
    {
        using var conn = Connection();
        using var cmd = Command(SqlQueries.Retrive, conn);

        var pId = cmd.CreateParameter();
        pId.ParameterName = ":NM_TICKET";
        pId.Value = key;
        pId.DbType = DbType.String;
        pId.OracleDbType = OracleDbType.Varchar2;
        pId.Direction = ParameterDirection.Input;

        cmd.Parameters.Add(pId);

        cmd.Connection.Open();

        using var reader = cmd.ExecuteReader();
        if (!reader.HasRows)
        {
            cmd.Connection.Close();

            return Task.FromResult(Array.Empty<byte>());
        }
        else
        {
            reader.Read();

            var binary = reader.GetFieldValue<byte[]>(0);

            cmd.Connection.Close();

            return Task.FromResult(binary);
        }
    }

    internal Task RemoveAsync(string key)
    {
        using var conn = Connection();
        using var cmd = Command(SqlQueries.Remove, conn);

        var pId = cmd.CreateParameter();
        pId.ParameterName = ":NM_TICKET";
        pId.Value = key;
        pId.DbType = DbType.String;
        pId.OracleDbType = OracleDbType.Varchar2;
        pId.Direction = ParameterDirection.Input;

        cmd.Parameters.Add(pId);

        cmd.Connection.Open();

        cmd.ExecuteNonQuery();

        cmd.Connection.Close();

        return Task.CompletedTask;
    }
}

```



```csharp
using System.Security.Claims;

namespace WScoreboard.TicketStore.Oracle;

public sealed class OracleTicketStoreOptions
{
    public string SequenceName { get; init; }

    public string TableName { get; init; }

    public string SchemaName { get; init; }

    public string ConnectionString { get; init; }

    public Func<ClaimsPrincipal, string> FnKeySelector { get; init; }
}

```

```csharp
services.AddAuthentication(options =>
{
	//omited for simplicit
})
.AddCookie(CookieAuthenticationDefaults.AuthenticationScheme, opt =>
{
	opt.SessionStore = new OracleTicketStore(new OracleTicketStoreOptions
    {
    	SequenceName = "DB_SEQUENCE",
        TableName = "TBL_TICKET",
        SchemaName = "DB_SCHEMA",
        ConnectionString = "MY_CONNECTION_STRING",
        FnKeySelector = x => $"WEBAPP_{x.UserId()}",
     });
})
```



