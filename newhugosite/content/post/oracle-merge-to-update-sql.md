+++
categories = ["oracle", "sql", "oracle-sql", "oracle-functions", "merge", "oracle-merge"]
date = 2023-05-21T18:45:58Z
description = ""
draft = false
image = "https://res-1.cloudinary.com/dgoa5llvh/image/upload/q_auto/oracle-merge-cover-2.png"
slug = "oracle-merge-to-update-sql"
summary = "Oracle MERGE statement is one of those powerful commands which allows you to perform INSERT and UPDATE operations in a single statement based on specific conditions. "
tags = ["oracle", "sql", "oracle-sql", "oracle-functions", "merge", "oracle-merge"]
title = "Oracle MERGE Statement SQL"

+++


Oracle MERGE statement is one of those powerful commands which allows you to perform **INSERT** and **UPDATE** operations in a single statement based on specific conditions.

This article will explore the **MERGE** command in **Oracle**.

## Table of Contents

* [What is the MERGE to UPDATE Command](#what-is-the-merge-to-update-command)
* [Example Using MERGE to UPDATE](#example-using-merge-to-update)
* [Additional Considerations](#additional-considerations)
* [Conclusion](#conclusion)

## What is the MERGE Command

The MERGE statement in Oracle combines the INSERT and UPDATE operations into a single statement.

It compares the data in the **Source Table** (or staging table) with the data in the **Target Table** (table to be updated). Then, based on specified conditions, the MERGE statement either inserts new rows or updates existing rows in the target table.

The basic syntax of the MERGE UPDATE command in Oracle:

```sql
MERGE INTO target_table a
USING source_table b
ON (condition)
WHEN MATCHED THEN
    UPDATE SET a.column1 = b.value1, a.column2 = b.value2, ...
WHEN NOT MATCHED THEN 
    INSERT (a.column1, a.column2, ...)
    VALUES (b.value1, b.value2, ...)

```

* `target_table`: The table to be updated.
* `source_table`: The table containing the source data.
* `condition`: The condition used to match rows between the source and target tables.
* `column1, column2`: The columns in the target table to be updated.
* `value1, value2`: The new values to be set for the corresponding columns.

## Example Using MERGE to UPDATE

Let's consider a practical example to demonstrate MERGE UPDATE command.

In this example, we have defined two tables:

```sql
CREATE TABLE CUSTOMER (
	ID_CUSTOMER NUMBER
    , NAME VARCHAR(100)
    , EMAIL VARCHAR(100)
);

SELECT * FROM CUSTOMER;

ID_CUSTOMER  NAME                   EMAIL
-----------  ---------------------  -------------------------
1            Robert L. Brown        robert.l.brown@sample.com
2            John A. Wong           johna@sample.com
3            Alexander W. Barclay   alexwb@sample.com
```

```sql
CREATE TABLE LEAD (
	ID_LEAD NUMBER
    , NAME VARCHAR(200)
    , EMAIL VARCHAR(100)
    , ID_CUSTOMER NUMBER
);

SELECT * FROM LEAD;

ID_LEAD      NAME                   EMAIL                      ID_CUSTOMER
-----------  ---------------------  -------------------------- -----------
1            John                   johna@sample.com           2
2            Alexander Barclay      alexwb@sample.com          3
3            Robert B.              robert.l.brown@sample.com  1
```

We will update the LEAD table with existing CUSTOMER information and create when it does not exist in LEAD.

```sql
/* CREATE A SEQUENCE HERE FOR SUPORTING THE INSERT CONDITION
CREATE SEQUENCE LEAD_SEQ;*/

MERGE INTO LEAD A
USING (SELECT ID_CUSTOMER, NAME, EMAIL FROM CUSTOMER) B
ON (B.ID_CUSTOMER = A.ID_CUSTOMER)
WHEN MATCHED THEN
    UPDATE SET A.NAME = B.NAME, A.EMAIL = B.EMAIL
WHEN NOT MATCHED THEN 
    INSERT (A.ID_LEAD, A.NAME, A.EMAIL, A.ID_CUSTOMER)
    VALUES (LEAD_SEQ.NEXTVAL, B.NAME, B.EMAIL, B.ID_CUSTOMER);
```

In the above example, we have merged the "CUSTOMER" table into the "LEAD" table based on matching **ID_CUSTOMER**.

When a match is found, the corresponding NAME and EMAIL columns in the "LEAD" table are updated with the values from the "CUSTOMER" table.

When no match is found, a new record is created.

## Additional Considerations

* It is essential to ensure that the conditions used in the **ON** clause are accurate and precise to avoid unintended updates or insertions.
* Proper indexing on columns involved in the merge condition can significantly improve performance.

## Conclusion

The MERGE statement simplifies data manipulation tasks by combining the INSERT and UPDATE operations into a single statement.

Understanding the syntax and usage of the MERGE UPDATE command empowers Oracle developers to perform complex data updates with ease and efficiency.



