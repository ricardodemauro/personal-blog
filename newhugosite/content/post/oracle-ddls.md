+++
date = 2023-04-27T15:15:06Z
description = ""
draft = true
slug = "oracle-ddls"
title = "Oracle DDLs"

+++


```sql
SELECT * FROM USER_INDEXES WHERE TABLE_NAME = 'MAT_ROUTE';

SELECT DBMS_METADATA.GET_DDL('INDEX', 'MAT_ROUTE_IDX01') FROM DUAL;


SELECT *
FROM all_ind_columns
WHERE table_name = 'MAT_ROUTE'
ORDER BY TABLE_NAME, INDEX_NAME, COLUMN_NAME

```



