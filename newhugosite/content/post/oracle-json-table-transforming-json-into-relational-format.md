+++
categories = ["oracle", "sql", "oracle-sql", "json_table", "oracle-functions"]
date = 2023-05-24T17:35:29Z
description = ""
draft = false
image = "https://res-3.cloudinary.com/dgoa5llvh/image/upload/q_auto/json_table-yellow-background-cover.webp"
slug = "oracle-json-table-transforming-json-into-relational-format"
summary = "The function JSON_TABLE allows us to transform JSON data into a table relational format. \nThis article will explore the Oracle JSON_TABLE function and its syntax and provide examples to demonstrate its usage and capabilities."
tags = ["oracle", "sql", "oracle-sql", "json_table", "oracle-functions"]
title = "Oracle JSON_TABLE Transforming JSON into Table"

+++


The function **JSON_TABLE** allows us to transform JSON data into a table relational format.

This article will explore the Oracle **JSON_TABLE** function and its syntax and provide examples to demonstrate its usage and capabilities.

## Table of Contents

* [Understanding the Oracle function JSON_TABLE](#understanding-the-oracle-function-jsontable)
* [Example Using Oracle JSON_TABLE](#example-using-oracle-jsontable)
* [Additional Considerations](#additional-considerations)
* [Conclusion](#conclusion)

## Understanding the Oracle function JSON_TABLE

The JSON_TABLE function in Oracle converts JSON data into a relational format by extracting specified JSON data elements and returning them as rows and columns.

It enables you to query and process JSON data using SQL techniques, making integrating JSON and relational data easier.

The Syntax of Oracle JSON_TABLE:

```sql
JSON_TABLE(
	json_data
    , path_expression COLUMNS (column_name data_type PATH json_path_exp) [, ...])

```

* `json_data`: The JSON document or column from which to extract data.
* `path_expression`: The JSON path expression specifies the elements to extract.
* `column_name`: The name to assign to the extracted JSON element.
* `data_type`: The data type of the extracted JSON element.
* `json_path_exp`: The JSON path expression to access the specific element within the JSON structure.

## Example Using Oracle JSON_TABLE

Let's consider a practical example to illustrate the usage of Oracle **JSON_TABLE**. Suppose we have a JSON document representing a list of employees and their details:

```json
{
  "employees": [
    { "name": "John Doe", "age": 30, "position": "Software Engineer" },
    { "name": "Jane Smith", "age": 35, "position": "Project Manager" } 
  ]
}
```

We can use JSON_TABLE to extract the employee details and transform them into a relational format:

```sql
SELECT A.NAME, A.AGE, A.POSITION
FROM JSON_TABLE(
    JSON_VALUE
    , '$.employees[*]' COLUMNS (
        NAME VARCHAR PATH '$.name',
        AGE NUMBER PATH '$.age',
        POSITION VARCHAR PATH '$.position'
    )
) A


NAME         AGE   POSITION
-----------  ----  ------------------
John Doe     30    Software Engineer
Jane Smith   35    Project Manager

```

In the above example, we use the JSON_TABLE function to extract the "name," "age," and "position" elements from the "employees" array.

Using the COLUMNS clause, we assign them to corresponding columns with appropriate data types.

The resulting query retrieves the employee details in a relational format.

## Additional Considerations

* The JSON_TABLE function supports advanced features such as nested JSON structures, array handling, and object flattening.
* You can use SQL functions and expressions within the JSON_TABLE statement to perform calculations or transformations on extracted data.
* JSON_TABLE also supports error handling and handling missing or optional JSON elements.

## Conclusion

Oracle JSON_TABLE is a powerful function that enables the transformation of JSON data into a relational format.

By extracting specified JSON elements and returning them as rows and columns, JSON_TABLE allows you to integrate JSON and relational data seamlessly.

Understanding the syntax and usage of Oracle JSON_TABLE empowers developers to efficiently query and process JSON data within an Oracle database environment.

