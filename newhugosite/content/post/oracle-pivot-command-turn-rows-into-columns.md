+++
categories = ["oracle", "oracle-functions", "sql", "oracle-sql"]
date = 2023-08-06T23:13:31Z
description = ""
draft = false
image = "https://res-4.cloudinary.com/dgoa5llvh/image/upload/q_auto/ORACLE-PIVOT-COVER.png"
slug = "oracle-pivot-command-turn-rows-into-columns"
tags = ["oracle", "oracle-functions", "sql", "oracle-sql"]
title = "Oracle PIVOT Command - Turn Rows into Columns"

+++


Discover the technical prowess of Oracle's PIVOT command, a robust feature that efficiently converts rows into columns.

This blog post delves into the syntax, use cases, and practical examples to illustrate how PIVOT simplifies data analysis and reporting in Oracle databases.

## Understanding the PIVOT Command

The PIVOT command is an extension of the SQL SELECT statement in Oracle. It lets you rotate rows into columns, effectively transposing data to achieve a more structured and readable output.

The PIVOT command works by aggregating data based on specified criteria and then rotating the unique values from one column into multiple columns.

```sql
SELECT *
FROM 
(
   SELECT column1, column2, column3
   FROM your_table_name
)
PIVOT
(
   aggregate_function (column_to_aggregate)
   FOR pivot_column
   IN (list_of_values)
)
```

* `aggregate_function`: This is the SQL aggregate function used to summarize the data. Commonly used functions include _SUM_, _COUNT_, _AVG_, _MAX_, _MIN_, etc.
* `column_to_aggregate`: The column whose values will be aggregated based on the specified aggregate function.
* `pivot_column`: The column containing unique values will be rotated into new column headers.
* `list_of_values`: A list of distinct values from the **pivot_column** that you want to become new columns.

### Use Case Scenario

Assume we have a table called "PRODUCTS" with the following structure.

```sql
SELECT * FROM PRODUCTS;

DATE          PRODUCT       SALES_QUANTITY
----------    -----------   ----------------
2023-07-01    Keyboard      10
2023-07-01    Mouse         15
2023-07-02    Keyboard      8 
2023-07-02    Mouse         12
```

We can use the Oracle **PIVOT** command to pivot this data and obtain a summary of sales quantities for each product on different columns:

```sql
SELECT *
FROM 
(
    SELECT DATE, PRODUCT, SALES_QUANTITY
    FROM PRODUCTS
)
PIVOT
(
    SUM (SALES_QUANTITY)
    FOR Product
    IN  ('Keyboard', 'Mouse')
);

```

In this example, the PIVOT command will transform the original data, displaying the sales quantities of each product as separate columns.

```sql
SELECT * FROM PRODUCTS;

DATE          Keyboard      Mouse
----------    -----------   ----------------
2023-07-01    10            15
2023-07-02    8             12
```

Now, you have a more structured and readable output, making it easier to analyze and interpret the sales quantities for each product on different dates.

The Oracle PIVOT command simplifies this data transformation process, enhancing the efficiency of your data analysis and reporting tasks.

## Conclusion

The Oracle **PIVOT** command is a powerful tool for data transformation, simplifying the process of converting rows into columns in a query result.

So, next time you find yourself facing the challenge of transposing data, consider harnessing the power of Oracle's PIVOT command to make your work more efficient and effective.

