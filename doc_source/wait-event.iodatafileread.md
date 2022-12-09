# IO:DataFileRead<a name="wait-event.iodatafileread"></a>

The `IO:DataFileRead` event occurs when a connection waits on a backend process to read a required page from storage because the page isn't available in shared memory\.

**Topics**
+ [Supported engine versions](#wait-event.iodatafileread.context.supported)
+ [Context](#wait-event.iodatafileread.context)
+ [Likely causes of increased waits](#wait-event.iodatafileread.causes)
+ [Actions](#wait-event.iodatafileread.actions)

## Supported engine versions<a name="wait-event.iodatafileread.context.supported"></a>

This wait event information is supported for all versions of RDS for PostgreSQL\.

## Context<a name="wait-event.iodatafileread.context"></a>

All queries and data manipulation \(DML\) operations access pages in the buffer pool\. Statements that can induce reads include `SELECT`, `UPDATE`, and `DELETE`\. For example, an `UPDATE` can read pages from tables or indexes\. If the page being requested or updated isn't in the shared buffer pool, this read can lead to the `IO:DataFileRead` event\.

Because the shared buffer pool is finite, it can fill up\. In this case, requests for pages that aren't in memory force the database to read blocks from disk\. If the `IO:DataFileRead` event occurs frequently, your shared buffer pool might be too small to accommodate your workload\. This problem is acute for `SELECT` queries that read a large number of rows that don't fit in the buffer pool\. For more information about the buffer pool, see [Resource Consumption](https://www.postgresql.org/docs/current/runtime-config-resource.html) in the PostgreSQL documentation\.

## Likely causes of increased waits<a name="wait-event.iodatafileread.causes"></a>

Common causes for the `IO:DataFileRead` event include the following:

**Connection spikes**  
You might find multiple connections generating the same number of IO:DataFileRead wait events\. In this case, a spike \(sudden and large increase\) in `IO:DataFileRead` events can occur\. 

**SELECT and DML statements performing sequential scans**  
Your application might be performing a new operation\. Or an existing operation might change because of a new execution plan\. In such cases, look for tables \(particularly large tables\) that have a greater `seq_scan` value\. Find them by querying `pg_stat_user_tables`\. To track queries that are generating more read operations, use the extension `pg_stat_statements`\.

**CTAS and CREATE INDEX for large data sets**  
A *CTAS* is a `CREATE TABLE AS SELECT` statement\. If you run a CTAS using a large data set as a source, or create an index on a large table, the `IO:DataFileRead` event can occur\. When you create an index, the database might need to read the entire object using a sequential scan\. A CTAS generates `IO:DataFile` reads when pages aren't in memory\.

**Multiple vacuum workers running at the same time**  
Vacuum workers can be triggered manually or automatically\. We recommend adopting an aggressive vacuum strategy\. However, when a table has many updated or deleted rows, the `IO:DataFileRead` waits increase\. After space is reclaimed, the vacuum time spent on `IO:DataFileRead` decreases\.

**Ingesting large amounts of data**  
When your application ingests large amounts of data, `ANALYZE` operations might occur more often\. The `ANALYZE` process can be triggered by an autovacuum launcher or invoked manually\.  
The `ANALYZE` operation reads a subset of the table\. The number of pages that must be scanned is calculated by multiplying 30 by the `default_statistics_target` value\. For more information, see the [PostgreSQL documentation](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-DEFAULT-STATISTICS-TARGET)\. The `default_statistics_target` parameter accepts values between 1 and 10,000, where the default is 100\.

**Resource starvation**  
If instance network bandwidth or CPU are consumed, the `IO:DataFileRead` event might occur more frequently\.

## Actions<a name="wait-event.iodatafileread.actions"></a>

We recommend different actions depending on the causes of your wait event\.

**Topics**
+ [Check predicate filters for queries that generate waits](#wait-event.iodatafileread.actions.filters)
+ [Minimize the effect of maintenance operations](#wait-event.iodatafileread.actions.maintenance)
+ [Respond to high numbers of connections](#wait-event.iodatafileread.actions.connections)

### Check predicate filters for queries that generate waits<a name="wait-event.iodatafileread.actions.filters"></a>

Assume that you identify specific queries that are generating `IO:DataFileRead` wait events\. You might identify them using the following techniques:
+ Performance Insights
+ Catalog views such as the one provided by the extension `pg_stat_statements`
+ The catalog view `pg_stat_all_tables`, if it periodically shows an increased number of physical reads
+ The `pg_statio_all_tables` view, if it shows that `_read` counters are increasing

We recommend that you determine which filters are used in the predicate \(`WHERE` clause\) of these queries\. Follow these guidelines:
+ Run the `EXPLAIN` command\. In the output, identify which types of scans are used\. A sequential scan doesn't necessarily indicate a problem\. Queries that use sequential scans naturally produce more `IO:DataFileRead` events when compared to queries that use filters\.

  Find out whether the column listed in the `WHERE` clause is indexed\. If not, consider creating an index for this column\. This approach avoids the sequential scans and reduces the `IO:DataFileRead` events\. If a query has restrictive filters and still produces sequential scans, evaluate whether the proper indexes are being used\.
+ Find out whether the query is accessing a very large table\. In some cases, partitioning a table can improve performance, allowing the query to only read necessary partitions\.
+ Examine the cardinality \(total number of rows\) from your join operations\. Note how restrictive the values are that you're passing in the filters for your `WHERE` clause\. If possible, tune your query to reduce the number of rows that are passed in each step of the plan\.

### Minimize the effect of maintenance operations<a name="wait-event.iodatafileread.actions.maintenance"></a>

Maintenance operations such as `VACUUM` and `ANALYZE` are important\. We recommend that you don't turn them off because you find `IO:DataFileRead` wait events related to these maintenance operations\. The following approaches can minimize the effect of these operations:
+ Run maintenance operations manually during off\-peak hours\. This technique prevents the database from reaching the threshold for automatic operations\.
+ For very large tables, consider partitioning the table\. This technique reduces the overhead of maintenance operations\. The database only accesses the partitions that require maintenance\.
+ When you ingest large amounts of data, consider disabling the autoanalyze feature\.

The autovacuum feature is automatically triggered for a table when the following formula is true\.

```
pg_stat_user_tables.n_dead_tup > (pg_class.reltuples x autovacuum_vacuum_scale_factor) + autovacuum_vacuum_threshold
```

The view `pg_stat_user_tables` and catalog `pg_class` have multiple rows\. One row can correspond to one row in your table\. This formula assumes that the `reltuples` are for a specific table\. The parameters `autovacuum_vacuum_scale_factor` \(0\.20 by default\) and `autovacuum_vacuum_threshold` \(50 tuples by default\) are usually set globally for the whole instance\. However, you can set different values for a specific table\.

**Topics**
+ [Find tables consuming space unnecessarily](#wait-event.iodatafileread.actions.maintenance.tables)
+ [Find indexes consuming space unnecessarily](#wait-event.iodatafileread.actions.maintenance.indexes)
+ [Find tables that are eligible to be autovacuumed](#wait-event.iodatafileread.actions.maintenance.autovacuumed)

#### Find tables consuming space unnecessarily<a name="wait-event.iodatafileread.actions.maintenance.tables"></a>

To find tables consuming space unnecessarily, you can use functions from the PostgreSQL `pgstattuple` extension\. This extension \(module\) is available by default on all RDS for PostgreSQL DB instances and can be instantiated on the instance with the following command\.

```
CREATE EXTENSION pgstattuple;
```

For more information about this extension, see [pgstattuple](https://www.postgresql.org/docs/current/pgstattuple.html) in the PostgreSQL documentation\.

The query shown in the listing returns results from tables for which your database user \(role\) has read permissions\. 

```
SELECT current_database(), schemaname, tblname, bs*tblpages AS real_size,
  (tblpages-est_tblpages)*bs AS extra_size,
  CASE WHEN tblpages - est_tblpages > 0
    THEN 100 * (tblpages - est_tblpages)/tblpages::float
    ELSE 0
  END AS extra_ratio, fillfactor, (tblpages-est_tblpages_ff)*bs AS bloat_size,
  CASE WHEN tblpages - est_tblpages_ff > 0
    THEN 100 * (tblpages - est_tblpages_ff)/tblpages::float
    ELSE 0
  END AS bloat_ratio, is_na
  -- , (pst).free_percent + (pst).dead_tuple_percent AS real_frag
FROM (
  SELECT 
    ceil( reltuples / ( (bs-page_hdr)/tpl_size ) ) + ceil( toasttuples / 4 ) 
      AS est_tblpages,
    ceil( reltuples / ( (bs-page_hdr)*fillfactor/(tpl_size*100) ) ) + ceil( toasttuples / 4 ) 
      AS est_tblpages_ff,
    tblpages, fillfactor, bs, tblid, schemaname, tblname, heappages, toastpages, is_na
    -- , stattuple.pgstattuple(tblid) AS pst
  FROM (
    SELECT
      ( 4 + tpl_hdr_size + tpl_data_size + (2*ma)
        - CASE WHEN tpl_hdr_size%ma = 0 THEN ma ELSE tpl_hdr_size%ma END
        - CASE WHEN ceil(tpl_data_size)::int%ma = 0 THEN ma ELSE ceil(tpl_data_size)::int%ma END
      ) AS tpl_size, bs - page_hdr AS size_per_block, (heappages + toastpages) AS tblpages, heappages,
      toastpages, reltuples, toasttuples, bs, page_hdr, tblid, schemaname, tblname, fillfactor, is_na
    FROM (
      SELECT
        tbl.oid AS tblid, ns.nspname AS schemaname, tbl.relname AS tblname, tbl.reltuples,
        tbl.relpages AS heappages, coalesce(toast.relpages, 0) AS toastpages,
        coalesce(toast.reltuples, 0) AS toasttuples,
        coalesce(substring(
          array_to_string(tbl.reloptions, ' ')
          FROM 'fillfactor=([0-9]+)')::smallint, 100) AS fillfactor,
        current_setting('block_size')::numeric AS bs,
        CASE WHEN version()~'mingw32' OR version()~'64-bit|x86_64|ppc64|ia64|amd64' 
          THEN 8 
          ELSE 4 
        END AS ma,
        24 AS page_hdr,
        23 + CASE WHEN MAX(coalesce(null_frac,0)) > 0 THEN ( 7 + count(*) ) / 8 ELSE 0::int END
          + CASE WHEN tbl.relhasoids THEN 4 ELSE 0 END AS tpl_hdr_size,
        sum( (1-coalesce(s.null_frac, 0)) * coalesce(s.avg_width, 1024) ) AS tpl_data_size,
        bool_or(att.atttypid = 'pg_catalog.name'::regtype)
          OR count(att.attname) <> count(s.attname) AS is_na
      FROM pg_attribute AS att
        JOIN pg_class AS tbl ON att.attrelid = tbl.oid
        JOIN pg_namespace AS ns ON ns.oid = tbl.relnamespace
        LEFT JOIN pg_stats AS s ON s.schemaname=ns.nspname
          AND s.tablename = tbl.relname AND s.inherited=false AND s.attname=att.attname
        LEFT JOIN pg_class AS toast ON tbl.reltoastrelid = toast.oid
      WHERE att.attnum > 0 AND NOT att.attisdropped
        AND tbl.relkind = 'r'
      GROUP BY 1,2,3,4,5,6,7,8,9,10, tbl.relhasoids
      ORDER BY 2,3
    ) AS s
  ) AS s2
) AS s3 ;
-- WHERE NOT is_na
--   AND tblpages*((pst).free_percent + (pst).dead_tuple_percent)::float4/100 >= 1
```

#### Find indexes consuming space unnecessarily<a name="wait-event.iodatafileread.actions.maintenance.indexes"></a>

To find bloated indexes and estimate the amount of space consumed unnecessarily on the tables for which you have read privileges, you can run the following query\.

```
-- WARNING: rows with is_na = 't' are known to have bad statistics ("name" type is not supported).
-- This query is compatible with PostgreSQL 8.2 and later.

SELECT current_database(), nspname AS schemaname, tblname, idxname, bs*(relpages)::bigint AS real_size,
  bs*(relpages-est_pages)::bigint AS extra_size,
  100 * (relpages-est_pages)::float / relpages AS extra_ratio,
  fillfactor, bs*(relpages-est_pages_ff) AS bloat_size,
  100 * (relpages-est_pages_ff)::float / relpages AS bloat_ratio,
  is_na
  -- , 100-(sub.pst).avg_leaf_density, est_pages, index_tuple_hdr_bm, 
  -- maxalign, pagehdr, nulldatawidth, nulldatahdrwidth, sub.reltuples, sub.relpages 
  -- (DEBUG INFO)
FROM (
  SELECT coalesce(1 +
       ceil(reltuples/floor((bs-pageopqdata-pagehdr)/(4+nulldatahdrwidth)::float)), 0 
       -- ItemIdData size + computed avg size of a tuple (nulldatahdrwidth)
    ) AS est_pages,
    coalesce(1 +
       ceil(reltuples/floor((bs-pageopqdata-pagehdr)*fillfactor/(100*(4+nulldatahdrwidth)::float))), 0
    ) AS est_pages_ff,
    bs, nspname, table_oid, tblname, idxname, relpages, fillfactor, is_na
    -- , stattuple.pgstatindex(quote_ident(nspname)||'.'||quote_ident(idxname)) AS pst, 
    -- index_tuple_hdr_bm, maxalign, pagehdr, nulldatawidth, nulldatahdrwidth, reltuples 
    -- (DEBUG INFO)
  FROM (
    SELECT maxalign, bs, nspname, tblname, idxname, reltuples, relpages, relam, table_oid, fillfactor,
      ( index_tuple_hdr_bm +
          maxalign - CASE -- Add padding to the index tuple header to align on MAXALIGN
            WHEN index_tuple_hdr_bm%maxalign = 0 THEN maxalign
            ELSE index_tuple_hdr_bm%maxalign
          END
        + nulldatawidth + maxalign - CASE -- Add padding to the data to align on MAXALIGN
            WHEN nulldatawidth = 0 THEN 0
            WHEN nulldatawidth::integer%maxalign = 0 THEN maxalign
            ELSE nulldatawidth::integer%maxalign
          END
      )::numeric AS nulldatahdrwidth, pagehdr, pageopqdata, is_na
      -- , index_tuple_hdr_bm, nulldatawidth -- (DEBUG INFO)
    FROM (
      SELECT
        i.nspname, i.tblname, i.idxname, i.reltuples, i.relpages, i.relam, a.attrelid AS table_oid,
        current_setting('block_size')::numeric AS bs, fillfactor,
        CASE -- MAXALIGN: 4 on 32bits, 8 on 64bits (and mingw32 ?)
          WHEN version() ~ 'mingw32' OR version() ~ '64-bit|x86_64|ppc64|ia64|amd64' THEN 8
          ELSE 4
        END AS maxalign,
        /* per page header, fixed size: 20 for 7.X, 24 for others */
        24 AS pagehdr,
        /* per page btree opaque data */
        16 AS pageopqdata,
        /* per tuple header: add IndexAttributeBitMapData if some cols are null-able */
        CASE WHEN max(coalesce(s.null_frac,0)) = 0
          THEN 2 -- IndexTupleData size
          ELSE 2 + (( 32 + 8 - 1 ) / 8) 
          -- IndexTupleData size + IndexAttributeBitMapData size ( max num filed per index + 8 - 1 /8)
        END AS index_tuple_hdr_bm,
        /* data len: we remove null values save space using it fractionnal part from stats */
        sum( (1-coalesce(s.null_frac, 0)) * coalesce(s.avg_width, 1024)) AS nulldatawidth,
        max( CASE WHEN a.atttypid = 'pg_catalog.name'::regtype THEN 1 ELSE 0 END ) > 0 AS is_na
      FROM pg_attribute AS a
        JOIN (
          SELECT nspname, tbl.relname AS tblname, idx.relname AS idxname, 
            idx.reltuples, idx.relpages, idx.relam,
            indrelid, indexrelid, indkey::smallint[] AS attnum,
            coalesce(substring(
              array_to_string(idx.reloptions, ' ')
               from 'fillfactor=([0-9]+)')::smallint, 90) AS fillfactor
          FROM pg_index
            JOIN pg_class idx ON idx.oid=pg_index.indexrelid
            JOIN pg_class tbl ON tbl.oid=pg_index.indrelid
            JOIN pg_namespace ON pg_namespace.oid = idx.relnamespace
          WHERE pg_index.indisvalid AND tbl.relkind = 'r' AND idx.relpages > 0
        ) AS i ON a.attrelid = i.indexrelid
        JOIN pg_stats AS s ON s.schemaname = i.nspname
          AND ((s.tablename = i.tblname AND s.attname = pg_catalog.pg_get_indexdef(a.attrelid, a.attnum, TRUE)) 
          -- stats from tbl
          OR  (s.tablename = i.idxname AND s.attname = a.attname))
          -- stats from functional cols
        JOIN pg_type AS t ON a.atttypid = t.oid
      WHERE a.attnum > 0
      GROUP BY 1, 2, 3, 4, 5, 6, 7, 8, 9
    ) AS s1
  ) AS s2
    JOIN pg_am am ON s2.relam = am.oid WHERE am.amname = 'btree'
) AS sub
-- WHERE NOT is_na
ORDER BY 2,3,4;
```

#### Find tables that are eligible to be autovacuumed<a name="wait-event.iodatafileread.actions.maintenance.autovacuumed"></a>

To find tables that are eligible to be autovacuumed, run the following query\.

```
--This query shows tables that need vacuuming and are eligible candidates.
--The following query lists all tables that are due to be processed by autovacuum. 
-- During normal operation, this query should return very little.
WITH  vbt AS (SELECT setting AS autovacuum_vacuum_threshold 
              FROM pg_settings WHERE name = 'autovacuum_vacuum_threshold')
    , vsf AS (SELECT setting AS autovacuum_vacuum_scale_factor 
              FROM pg_settings WHERE name = 'autovacuum_vacuum_scale_factor')
    , fma AS (SELECT setting AS autovacuum_freeze_max_age 
              FROM pg_settings WHERE name = 'autovacuum_freeze_max_age')
    , sto AS (SELECT opt_oid, split_part(setting, '=', 1) as param, 
                split_part(setting, '=', 2) as value 
              FROM (SELECT oid opt_oid, unnest(reloptions) setting FROM pg_class) opt)
SELECT
    '"'||ns.nspname||'"."'||c.relname||'"' as relation
    , pg_size_pretty(pg_table_size(c.oid)) as table_size
    , age(relfrozenxid) as xid_age
    , coalesce(cfma.value::float, autovacuum_freeze_max_age::float) autovacuum_freeze_max_age
    , (coalesce(cvbt.value::float, autovacuum_vacuum_threshold::float) + 
         coalesce(cvsf.value::float,autovacuum_vacuum_scale_factor::float) * c.reltuples) 
         as autovacuum_vacuum_tuples
    , n_dead_tup as dead_tuples
FROM pg_class c 
JOIN pg_namespace ns ON ns.oid = c.relnamespace
JOIN pg_stat_all_tables stat ON stat.relid = c.oid
JOIN vbt on (1=1) 
JOIN vsf ON (1=1) 
JOIN fma on (1=1)
LEFT JOIN sto cvbt ON cvbt.param = 'autovacuum_vacuum_threshold' AND c.oid = cvbt.opt_oid
LEFT JOIN sto cvsf ON cvsf.param = 'autovacuum_vacuum_scale_factor' AND c.oid = cvsf.opt_oid
LEFT JOIN sto cfma ON cfma.param = 'autovacuum_freeze_max_age' AND c.oid = cfma.opt_oid
WHERE c.relkind = 'r' 
AND nspname <> 'pg_catalog'
AND (
    age(relfrozenxid) >= coalesce(cfma.value::float, autovacuum_freeze_max_age::float)
    or
    coalesce(cvbt.value::float, autovacuum_vacuum_threshold::float) + 
      coalesce(cvsf.value::float,autovacuum_vacuum_scale_factor::float) * c.reltuples <= n_dead_tup
    -- or 1 = 1
)
ORDER BY age(relfrozenxid) DES;
```

### Respond to high numbers of connections<a name="wait-event.iodatafileread.actions.connections"></a>

When you monitor Amazon CloudWatch, you might find that the `DatabaseConnections` metric spikes\. This increase indicates an increased number of connections to your database\. We recommend the following approach:
+ Limit the number of connections that the application can open with each instance\. If your application has an embedded connection pool feature, set a reasonable number of connections\. Base the number on what the vCPUs in your instance can parallelize effectively\.

  If your application doesn't use a connection pool feature, considering using Amazon RDS Proxy or an alternative\. This approach lets your application open multiple connections with the load balancer\. The balancer can then open a restricted number of connections with the database\. As fewer connections are running in parallel, your DB instance performs less context switching in the kernel\. Queries should progress faster, leading to fewer wait events\. For more information, see [Using Amazon RDS Proxy](rds-proxy.md)\.
+ Whenever possible, take advantage of read replicas for RDS for PostgreSQL\. When your application runs a read\-only operation, send these requests to the read replica\(s\)\. This technique reduces the I/O pressure on the primary \(writer\) node\.
+ Consider scaling up your DB instance\. A higher\-capacity instance class gives more memory, which gives RDS for PostgreSQL a larger shared buffer pool to hold pages\. The larger size also gives the DB instance more vCPUs to handle connections\. More vCPUs are particularly helpful when the operations that are generating `IO:DataFileRead` wait events are writes\.