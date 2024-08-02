+++
categories = ["oracle", "oracle-sql", "oracle-functions"]
date = 2024-06-25T19:40:32Z
description = ""
draft = false
image = "https://res-4.cloudinary.com/dgoa5llvh/image/upload/q_auto/oracle-cover-gather-stats.png"
slug = "gather-oracle-stats-for-schema-tables-indexes"
tags = ["oracle", "oracle-sql", "oracle-functions"]
title = "Using dbms_stats to Gather Oracle Stats for Schema, Table and Indexes"

+++


In the world of Oracle Database Management, gathering statistics plays a crucial role in optimizing query performance. Accurate statistics help the query optimizer make informed decisions about execution plans.

In this introduction let's explore how to gather statistics for **Schemas**, **Tables**, and **Indexes** using Oracle simple commands.

### Table of Contents

* [Gather Schema Stats](#gather-schema-stats-with-gatherschemastats)
* [Gather Table Stats](#gather-table-stats-with-gathertablestats)
* [Gather Index Stats](#gather-index-stats-with-gatherindexstats)

## Gather Schema Stats with GATHER_SCHEMA_STATS

To collect statistics for an entire schema, use the following command.

```sql
-- GATHER STATS FROM SCHEMA USING DEFAULT OPTIONS
EXEC DBMS_STATS.GATHER_SCHEMA_STATS('SCHEMA_NAME'); 
```

### Considerations

* For partitioned tables, use `CASCADE=>TRUE` to gather statistics for all partitions

### Full Syntax of GATHER_SCHEMA_STATS

```sql
DBMS_STATS.GATHER_SCHEMA_STATS ( 
   ownname          VARCHAR2, 
   estimate_percent NUMBER   DEFAULT to_estimate_percent_type 
                                                (get_param('ESTIMATE_PERCENT')), 
   block_sample     BOOLEAN  DEFAULT FALSE, 
   method_opt       VARCHAR2 DEFAULT get_param('METHOD_OPT'),
   degree           NUMBER   DEFAULT to_degree_type(get_param('DEGREE')), 
   granularity      VARCHAR2 DEFAULT GET_PARAM('GRANULARITY'), 
   cascade          BOOLEAN  DEFAULT to_cascade_type(get_param('CASCADE')), 
   stattab          VARCHAR2 DEFAULT NULL, 
   statid           VARCHAR2 DEFAULT NULL, 
   options          VARCHAR2 DEFAULT 'GATHER', 
   objlist          OUT      ObjectTab,
   statown          VARCHAR2 DEFAULT NULL, 
   no_invalidate    BOOLEAN  DEFAULT to_no_invalidate_type (
                                     get_param('NO_INVALIDATE')),
  force             BOOLEAN DEFAULT FALSE,
  obj_filter_list  ObjectTab DEFAULT NULL);
```

#### 

## Gather Table Stats with GATHER_TABLE_STATS

To gather statistics of individual tables use the following command.

```sql
EXEC DBMS_STATS.GATHER_TABLE_STATS(
    ownname     => 'SCHEMA_NAME',
    tabname     => 'TABLE_NAME'
);

```

### Considerations

* Small tables: Use a larger sample size
* Large tables: Consider parallelism and incremental statistics

### Full Syntax of GATHER_TABLE_STATS

```sql
DBMS_STATS.GATHER_TABLE_STATS (
   ownname          VARCHAR2, 
   tabname          VARCHAR2, 
   partname         VARCHAR2 DEFAULT NULL,
   estimate_percent NUMBER   DEFAULT to_estimate_percent_type 
                                                  (get_param('ESTIMATE_PERCENT')),
   block_sample     BOOLEAN  DEFAULT FALSE,
   method_opt       VARCHAR2 DEFAULT get_param('METHOD_OPT'),
   degree           NUMBER   DEFAULT to_degree_type(get_param('DEGREE')),
   granularity      VARCHAR2 DEFAULT GET_PARAM('GRANULARITY'), 
   cascade          BOOLEAN  DEFAULT to_cascade_type(get_param('CASCADE')),
   stattab          VARCHAR2 DEFAULT NULL, 
   statid           VARCHAR2 DEFAULT NULL,
   statown          VARCHAR2 DEFAULT NULL,
   no_invalidate    BOOLEAN  DEFAULT  to_no_invalidate_type (
                                                      get_param('NO_INVALIDATE')),
   stattype         VARCHAR2 DEFAULT 'DATA',
   force            BOOLEAN  DEFAULT FALSE,
   context          DBMS_STATS.CCONTEXT DEFAULT NULL, -- non operative
   options          VARCHAR2 DEFAULT get_param('OPTIONS'));
```

## Gather Index Stats with GATHER_INDEX_STATS

To gather Indexes statistics use the following command.

```sql
EXEC DBMS_STATS.GATHER_INDEX_STATS(
    ownname     => 'SCHEMA_NAME',
    indname     => 'INDEX_NAME'
);

```

### Considerations

* Unique indexes: Gather statistics for uniqueness checks
* Bitmap indexes: Handle them differently due to their nature

### Full Syntax of GATHER_INDEX_STATS

```sql
DBMS_STATS.GATHER_INDEX_STATS (
   ownname          VARCHAR2, 
   indname          VARCHAR2, 
   partname         VARCHAR2 DEFAULT NULL,
   estimate_percent NUMBER   DEFAULT to_estimate_percent_type 
                                                (GET_PARAM('ESTIMATE_PERCENT')),
   stattab          VARCHAR2 DEFAULT NULL, 
   statid           VARCHAR2 DEFAULT NULL,
   statown          VARCHAR2 DEFAULT NULL,
   degree           NUMBER   DEFAULT to_degree_type(get_param('DEGREE')),
   granularity      VARCHAR2 DEFAULT GET_PARAM('GRANULARITY'),
   no_invalidate    BOOLEAN  DEFAULT to_no_invalidate_type 
                                               (GET_PARAM('NO_INVALIDATE')),
   force            BOOLEAN DEFAULT FALSE);
```

## Conclusion

Gathering statistics is not just a routine task—it’s a critical part of maintaining a healthy database. By following best practices and understanding the nuances, you’ll ensure optimal query performance.

For further details check the [official documentation](https://docs.oracle.com/en/database/oracle/oracle-database/19/arpls/DBMS_STATS.html).

Remember to apply these techniques in your database environment, and happy querying! 🚀

