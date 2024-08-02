+++
categories = ["oracle", "oracle-functions", "oracle-sql", "sql"]
date = 2024-06-27T21:20:45Z
description = ""
draft = false
image = "https://images.unsplash.com/photo-1619431843665-54babc76ac8c?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=M3wxMTc3M3wwfDF8c2VhcmNofDV8fGNvcHl8ZW58MHx8fHwxNzE5NTIzMzM3fDA&ixlib=rb-4.0.3&q=80&w=2000"
slug = "oracle-remove-duplicate-from-table"
tags = ["oracle", "oracle-functions", "oracle-sql", "sql"]
title = "Oracle Remove Duplicate from Table"

+++


Let's explore a one effective method to remove duplicate rows from an Oracle table.

Duplicate data can be a common problem when working with databases, and it's crucial to eliminate such redundancy to ensure data accuracy and improve query performance.

Using this simple query we can find and **DELETE** duplicate rows from Oracle TABLE.

```sql
DELETE FROM your_table
WHERE rowid NOT IN 
(
    SELECT MIN(rowid)
    FROM your_table
    GROUP BY column1
    	   , column2
           , column3
);
```

This solution uses the `GROUP BY` clause, which clusters the records based on the uniqueness of specified columns. Selecting the `MIN(rowid)` for each group, we ensure that one original record remains, while the rest are deleted.

## Example Using a Dummy Table

Here is our sample table.

<table>
    <thead>
        <tr>
            <th>ID</th>
            <th>
                FIRST_NAME</th>
            <th>
                LAST_NAME</th>
            <th>
                DEPT_ID</th>
        </tr>
    </thead>
    <tbody>
        <tr style="background-color: whitesmoke;">
            <td>
                1</td>
            <td>
                John</td>
            <td>
                Doe</td>
            <td>
                101</td>
        </tr>
        <tr style="background-color: orange;">
            <td>
                2</td>
            <td>
                Jane</td>
            <td>
                Smith</td>
            <td>
                102</td>
        </tr>
        <tr style="background-color: whitesmoke;">
            <td>
                3</td>
            <td>
                John</td>
            <td>
                Doe</td>
            <td>
                101</td>
        </tr>
        <tr>
            <td>
                4</td>
            <td>
                Alex</td>
            <td>
                Johnson</td>
            <td>
                103</td>
        </tr>
        <tr style="background-color: orange;">
            <td>
                5</td>
            <td>
                Jane</td>
            <td>
                Smith</td>
            <td>
                102</td>
        </tr>
    </tbody>
</table>

To remove the duplicates from this table we simply execute the following SQL.

```sql
DELETE FROM EMPLOYEE
WHERE ROWID NOT IN 
(
    SELECT MIN(ROWID)
    FROM EMPLOYEE
    GROUP BY FIRST_NAME
           , LAST_NAME
           , DEPT_ID
)
```

Stay tuned for more insights and tips to keep your Oracle databases lean and performant!

