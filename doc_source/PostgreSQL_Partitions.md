# Managing PostgreSQL partitions with the pg\_partman extension<a name="PostgreSQL_Partitions"></a>

PostgreSQL table partitioning provides a framework for high\-performance handling of data input and reporting\. Use partitioning for databases that require very fast input of large amounts of data\. Partitioning also provides for faster queries of large tables\. Partitioning helps maintain data without impacting the database instance because it requires less I/O resources\.

By using partitioning, you can split data into custom\-sized chunks for processing\. For example, you can partition time\-series data for ranges such as hourly, daily, weekly, monthly, quarterly, yearly, custom, or any combination of these\. For a time\-series data example, if you partition the table by hour, each partition contains one hour of data\. If you partition the time\-series table by day, the partitions holds one day's worth of data, and so on\. The partition key controls the size of a partition\. 

When you use an `INSERT` or `UPDATE` SQL command on a partitioned table, the database engine routes the data to the appropriate partition\. PostgreSQL table partitions that store the data are child tables of the main table\. 

During database query reads, the PostgreSQL optimizer examines the `WHERE` clause of the query and, if possible, directs the database scan to only the relevant partitions\.

Starting with version 10, PostgreSQL uses declarative partitioning to implement table partitioning\. This is also known as native PostgreSQL partitioning\. Before PostgreSQL version 10, you used triggers to implement partitions\. 

PostgreSQL table partitioning provides the following features:
+ Creation of new partitions at any time\.
+ Variable partition ranges\.
+ Detachable and reattachable partitions using data definition language \(DDL\) statements\.

  For example, detachable partitions are useful for removing historical data from the main partition but keeping historical data for analysis\.
+ New partitions inherit the parent database table properties, including the following:
  + Indexes
  + Primary keys, which must include the partition key column
  + Foreign keys
  + Check constraints
  + References
+ Creating indexes for the full table or each specific partition\.

You can't alter the schema for an individual partition\. However, you can alter the parent table \(such as adding a new column\), which propagates to partitions\. 

**Topics**
+ [Overview of the PostgreSQL pg\_partman extension](#PostgreSQL_Partitions.pg_partman)
+ [Enabling the pg\_partman extension](#PostgreSQL_Partitions.enable)
+ [Configuring partitions using the create\_parent function](#PostgreSQL_Partitions.create_parent)
+ [Configuring partition maintenance using the run\_maintenance\_proc function](#PostgreSQL_Partitions.run_maintenance_proc)

## Overview of the PostgreSQL pg\_partman extension<a name="PostgreSQL_Partitions.pg_partman"></a>

You can use the PostgreSQL `pg_partman` extension to automate the creation and maintenance of table partitions\. For more general information, see [PG Partition Manager](https://github.com/pgpartman/pg_partman) in the `pg_partman` documentation\.

**Note**  
The `pg_partman` extension is supported on RDS for PostgreSQL versions 12\.5 and higher\.

Instead of having to manually create each partition, you configure `pg_partman` with the following settings: 
+ Table to be partitioned
+ Partition type
+ Partition key
+ Partition granularity
+ Partition precreation and management options

After you create a PostgreSQL partitioned table, you register it with `pg_partman` by calling the `create_parent` function\. Doing this creates the necessary partitions based on the parameters you pass to the function\.

The `pg_partman` extension also provides the `run_maintenance_proc` function, which you can call on a scheduled basis to automatically manage partitions\. To ensure that the proper partitions are created as needed, schedule this function to run periodically \(such as hourly\)\. You can also ensure that partitions are automatically dropped\.

## Enabling the pg\_partman extension<a name="PostgreSQL_Partitions.enable"></a>

If you have multiple databases inside the same PostgreSQL DB instance for which you want to manage partitions, enable the `pg_partman` extension separately for each database\. To enable the `pg_partman` extension for a specific database, create the partition maintenance schema and then create the `pg_partman` extension as follows\.

```
CREATE SCHEMA partman;
CREATE EXTENSION pg_partman WITH SCHEMA partman;
```

**Note**  
To create the `pg_partman` extension, make sure that you have `rds_superuser` privileges\. 

If you receive an error such as the following, grant the `rds_superuser` privileges to the account or use your superuser account\. 

```
ERROR: permission denied to create extension "pg_partman"
HINT: Must be superuser to create this extension.
```

To grant `rds_superuser` privileges, connect with your superuser account and run the following command\.

```
GRANT rds_superuser TO user-or-role;
```

For the examples that show using the pg\_partman extension, we use the following sample database table and partition\. This database uses a partitioned table based on a timestamp\. A schema `data_mart` contains a table named `events` with a column named `created_at`\. The following settings are included in the `events` table:
+  Primary keys `event_id` and `created_at`, which must have the column used to guide the partition\.
+ A check constraint `ck_valid_operation` to enforce values for an `operation` table column\.
+ Two foreign keys, where one \(`fk_orga_membership)` points to the external table `organization` and the other \(`fk_parent_event_id`\) is a self\-referenced foreign key\. 
+ Two indexes, where one \(`idx_org_id`\) is for the foreign key and the other \(`idx_event_type`\) is for the event type\.

The follow DDL statements create these objects, which are automatically included on each partition\.

```
CREATE SCHEMA data_mart;
CREATE TABLE data_mart.organization ( org_id BIGSERIAL,
        org_name TEXT,
        CONSTRAINT pk_organization PRIMARY KEY (org_id)  
    );

CREATE TABLE data_mart.events(
        event_id        BIGSERIAL, 
        operation       CHAR(1), 
        value           FLOAT(24), 
        parent_event_id BIGINT, 
        event_type      VARCHAR(25), 
        org_id          BIGSERIAL, 
        created_at      timestamp, 
        CONSTRAINT pk_data_mart_event PRIMARY KEY (event_id, created_at), 
        CONSTRAINT ck_valid_operation CHECK (operation = 'C' OR operation = 'D'), 
        CONSTRAINT fk_orga_membership 
            FOREIGN KEY(org_id) 
            REFERENCES data_mart.organization (org_id),
        CONSTRAINT fk_parent_event_id 
            FOREIGN KEY(parent_event_id, created_at) 
            REFERENCES data_mart.events (event_id,created_at)
    ) PARTITION BY RANGE (created_at);

CREATE INDEX idx_org_id     ON  data_mart.events(org_id);
CREATE INDEX idx_event_type ON  data_mart.events(event_type);
```



## Configuring partitions using the create\_parent function<a name="PostgreSQL_Partitions.create_parent"></a>

After you enable the `pg_partman` extension, use the `create_parent` function to configure partitions inside the partition maintenance schema\. The following example uses the `events` table example created in [Enabling the pg\_partman extensionConfiguring partition maintenance using the run\_maintenance\_proc function](#PostgreSQL_Partitions.enable)\. Call the `create_parent` function as follows\.

```
SELECT partman.create_parent( p_parent_table => 'data_mart.events',
 p_control => 'created_at',
 p_type => 'native',
 p_interval=> 'daily',
 p_premake => 30);
```

The parameters are as follows:
+ `p_parent_table` – The parent partitioned table\. This table must already exist and be fully qualified, including the schema\. 
+ `p_control` – The column on which the partitioning is to be based\. The data type must be an integer or time\-based\.
+ `p_type` – The type is either `'native'` or `'partman'`\. You typically use the `native` type for its performance improvements and flexibility\. The `partman` type relies on inheritance\.
+ `p_interval` – The time interval or integer range for each partition\. Example values include `daily`, hourly, and so on\.
+ `p_premake` – The number of partitions to create in advance to support new inserts\.

For a complete description of the `create_parent` function, see [Creation Functions](https://github.com/pgpartman/pg_partman/blob/master/doc/pg_partman.md#user-content-creation-functions) in the `pg_partman` documentation\.

## Configuring partition maintenance using the run\_maintenance\_proc function<a name="PostgreSQL_Partitions.run_maintenance_proc"></a>

You can run partition maintenance operations to automatically create new partitions, detach partitions, or remove old partitions\. Partition maintenance relies on the `run_maintenance_proc` function of the `pg_partman` extension and the `pg_cron` extension, which initiates an internal scheduler\. The `pg_cron` scheduler automatically executes SQL statements, functions, and procedures defined in your databases\. 

The following example uses the `events` table example created in [Enabling the pg\_partman extensionConfiguring partition maintenance using the run\_maintenance\_proc function](#PostgreSQL_Partitions.enable) to set partition maintenance operations to run automatically\. As a prerequisite, add `pg_cron` to the `shared_preload_libraries` parameter in the DB instance's parameter group\.

```
CREATE EXTENSION pg_cron;

UPDATE partman.part_config 
SET infinite_time_partitions = true,
    retention = '3 months', 
    retention_keep_table=true 
WHERE parent_table = 'data_mart.events';
SELECT cron.schedule('@hourly', $$CALL partman.run_maintenance_proc()$$);
```

Following, you can find a step\-by\-step explanation of the preceding example: 

1. Modify the parameter group associated with your DB instance and add `pg_cron` to the `shared_preload_libraries` parameter value\. This change requires a DB instance restart for it to take effect\. For more information, see [Modifying parameters in a DB parameter group](USER_WorkingWithDBInstanceParamGroups.md#USER_WorkingWithParamGroups.Modifying)\. 

1. Run the command `CREATE EXTENSION pg_cron;` using an account that has the `rds_superuser` permissions\. Doing this enables the `pg_cron` extension\. For more information, see [Scheduling maintenance with the PostgreSQL pg\_cron extension](PostgreSQL_pg_cron.md)\.

1. Run the command `UPDATE partman.part_config` to adjust the `pg_partman` settings for the `data_mart.events` table\. 

1. Run the command `SET` \. \. \. to configure the `data_mart.events` table, with these clauses:

   1. `infinite_time_partitions = true,` – Configures the table to be able to automatically create new partitions without any limit\.

   1. `retention = '3 months',` – Configures the table to have a maximum retention of three months\. 

   1. `retention_keep_table=true `– Configures the table so that when the retention period is due, the table isn't deleted automatically\. Instead, partitions that are older than the retention period are only detached from the parent table\.

1. Run the command `SELECT cron.schedule` \. \. \. to make a `pg_cron` function call\. This call defines how often the scheduler runs the `pg_partman` maintenance procedure, `partman.run_maintenance_proc`\. For this example, the procedure runs every hour\. 

For a complete description of the `run_maintenance_proc` function, see [Maintenance Functions](https://github.com/pgpartman/pg_partman/blob/master/doc/pg_partman.md#maintenance-functions) in the `pg_partman` documentation\. 