# Working with PostgreSQL features supported by Amazon RDS for PostgreSQL<a name="PostgreSQL.Concepts.General.FeatureSupport"></a>

Amazon RDS for PostgreSQL supports many of the most common PostgreSQL features\. For example, PostgreSQL has an autovacuum feature that performs routine maintenance on the database\. The autovacuum feature is active by default\. Although you can turn off this feature, we highly recommend that you keep it on\. Understanding this feature and what you can do to make sure it works as it should is a basic task of any DBA\. For more information about the autovacuum, see [Working with the PostgreSQL autovacuum on Amazon RDS for PostgreSQL](Appendix.PostgreSQL.CommonDBATasks.Autovacuum.md)\. To learn more about other common DBA tasks, [Common DBA tasks for Amazon RDS for PostgreSQL](Appendix.PostgreSQL.CommonDBATasks.md)\. 

RDS for PostgreSQL also supports extensions that add important functionality to the DB instance\. For example, you can use the PostGIS extension to work with spatial data, or use the pg\_cron extension to schedule maintenance from within the instance\. For more information about PostgreSQL extensions, see [Using PostgreSQL extensions with Amazon RDS for PostgreSQL](Appendix.PostgreSQL.CommonDBATasks.Extensions.md)\. 

Foreign data wrappers are a specific type of extension designed to let your RDS for PostgreSQL DB instance work with other commercial databases or data types\. For more information about foreign data wrappers supported by RDS for PostgreSQL, see [Working with the supported foreign data wrappers for Amazon RDS for PostgreSQL](Appendix.PostgreSQL.CommonDBATasks.Extensions.foreign-data-wrappers.md)\. 

Following, you can find information about some other features supported by RDS for PostgreSQL\. 

**Topics**
+ [Custom data types and enumerations with RDS for PostgreSQL](#PostgreSQL.Concepts.General.FeatureSupport.AlterEnum)
+ [Event triggers for RDS for PostgreSQL](#PostgreSQL.Concepts.General.FeatureSupport.EventTriggers)
+ [Huge pages for RDS for PostgreSQL](#PostgreSQL.Concepts.General.FeatureSupport.HugePages)
+ [Performing logical replication for Amazon RDS for PostgreSQL](#PostgreSQL.Concepts.General.FeatureSupport.LogicalReplication)
+ [RAM disk for the stats\_temp\_directory](#PostgreSQL.Concepts.General.FeatureSupport.RamDisk)
+ [Tablespaces for RDS for PostgreSQL](#PostgreSQL.Concepts.General.FeatureSupport.Tablespaces)
+ [RDS for PostgreSQL collations for EBCDIC and other mainframe migrations](#PostgreSQL.Collations.mainframe.migration)

## Custom data types and enumerations with RDS for PostgreSQL<a name="PostgreSQL.Concepts.General.FeatureSupport.AlterEnum"></a>

PostgreSQL supports creating custom data types and working with enumerations\. For more information about creating and working with enumerations and other data types, see [Enumerated types](https://www.postgresql.org/docs/14/datatype-enum.html) in the PostgreSQL documentation\. 

The following is an example of creating a type as an enumeration and then inserting values into a table\. 

```
CREATE TYPE rainbow AS ENUM ('red', 'orange', 'yellow', 'green', 'blue', 'purple');
CREATE TYPE
CREATE TABLE t1 (colors rainbow);
CREATE TABLE
INSERT INTO t1 VALUES ('red'), ( 'orange');
INSERT 0 2
SELECT * from t1;
colors
--------
red
orange
(2 rows)
postgres=> ALTER TYPE rainbow RENAME VALUE 'red' TO 'crimson';
ALTER TYPE
postgres=> SELECT * from t1;
colors
---------
crimson
orange
(2 rows)
```

## Event triggers for RDS for PostgreSQL<a name="PostgreSQL.Concepts.General.FeatureSupport.EventTriggers"></a>

All current PostgreSQL versions support event triggers, and so do all available versions of RDS for PostgreSQL\. You can use the main user account \(default, `postgres`\) to create, modify, rename, and delete event triggers\. Event triggers are at the DB instance level, so they can apply to all databases on an instance\.

For example, the following code creates an event trigger that prints the current user at the end of every data definition language \(DDL\) command\.

```
CREATE OR REPLACE FUNCTION raise_notice_func()
    RETURNS event_trigger
    LANGUAGE plpgsql AS
$$
BEGIN
    RAISE NOTICE 'In trigger function: %', current_user;
END;
$$;

CREATE EVENT TRIGGER event_trigger_1 
    ON ddl_command_end
EXECUTE PROCEDURE raise_notice_func();
```

For more information about PostgreSQL event triggers, see [Event triggers](https://www.postgresql.org/docs/current/static/event-triggers.html) in the PostgreSQL documentation\.

There are several limitations to using PostgreSQL event triggers on Amazon RDS\. These include the following:
+ You can't create event triggers on read replicas\. You can, however, create event triggers on a read replica source\. The event triggers are then copied to the read replica\. The event triggers on the read replica don't fire on the read replica when changes are pushed from the source\. However, if the read replica is promoted, the existing event triggers fire when database operations occur\.
+ To perform a major version upgrade to a PostgreSQL DB instance that uses event triggers, make sure to delete the event triggers before you upgrade the instance\.

## Huge pages for RDS for PostgreSQL<a name="PostgreSQL.Concepts.General.FeatureSupport.HugePages"></a>

*Huge pages* are a memory management feature that reduces overhead when a DB instance is working with large contiguous chunks of memory, such as that used by shared buffers\. This PostgreSQL feature is supported by all currently available RDS for PostgreSQL versions\. You allocate huge pages for your application by using calls to `mmap` or `SYSV` shared memory\. RDS for PostgreSQL supports both 4\-KB and 2\-MB page sizes\. 

You can turn huge pages on or off by changing the value of the `huge_pages` parameter\. The feature is turned on by default for all the DB instance classes other than micro, small, and medium DB instance classes\.

RDS for PostgreSQL uses huge pages based on the available shared memory\. If the DB instance can't use huge pages due to shared memory constraints, Amazon RDS prevents the DB instance from starting\. In this case, Amazon RDS sets the status of the DB instance to an incompatible parameters state\. If this occurs, you can set the `huge_pages` parameter to `off` to allow Amazon RDS to start the DB instance\.

The `shared_buffers` parameter is key to setting the shared memory pool that is required for using huge pages\. The default value for the `shared_buffers` parameter uses a database parameters macro\. This macro sets a percentage of the total 8 KB pages available for the DB instance's memory\. When you use huge pages, those pages are located with the huge pages\. Amazon RDS puts a DB instance into an incompatible parameters state if the shared memory parameters are set to require more than 90 percent of the DB instance memory\.

To learn more about PostgreSQL memory management, see [Resource Consumption](https://www.postgresql.org/docs/current/static/runtime-config-resource.html) in the PostgreSQL documentation\.

## Performing logical replication for Amazon RDS for PostgreSQL<a name="PostgreSQL.Concepts.General.FeatureSupport.LogicalReplication"></a>

Starting with version 10\.4, RDS for PostgreSQL supports the publication and subscription SQL syntax that was introduced in PostgreSQL 10\. To learn more, see [Logical replication](https://www.postgresql.org/docs/current/logical-replication.html) in the PostgreSQL documentation\. 

**Note**  
In addition to the native PostgreSQL logical replication feature introduced in PostgreSQL 10, RDS for PostgreSQL also supports the `pglogical` extension\. For more information, see [Using pglogical to synchronize data across instances](Appendix.PostgreSQL.CommonDBATasks.Extensions.md#Appendix.PostgreSQL.CommonDBATasks.pglogical)\. 

Following, you can find information about setting up logical replication for an RDS for PostgreSQL DB instance\. 

**Topics**
+ [Understanding logical replication and logical decoding](#PostgreSQL.Concepts.General.FeatureSupport.LogicalDecoding)
+ [Working with logical replication slots](#PostgreSQL.Concepts.General.FeatureSupport.LogicalReplicationSlots)

### Understanding logical replication and logical decoding<a name="PostgreSQL.Concepts.General.FeatureSupport.LogicalDecoding"></a>

RDS for PostgreSQL supports the streaming of write\-ahead log \(WAL\) changes using PostgreSQL's logical replication slots\. It also supports using logical decoding\. You can set up logical replication slots on your instance and stream database changes through these slots to a client such as `pg_recvlogical`\. You create logical replication slots at the database level, and they support replication connections to a single database\. 

The most common clients for PostgreSQL logical replication are AWS Database Migration Service or a custom\-managed host on an Amazon EC2 instance\. The logical replication slot has no information about the receiver of the stream\. Also, there's no requirement that the target be a replica database\. If you set up a logical replication slot and don't read from the slot, data can be written and quickly fill up your DB instance's storage\.

You turn on PostgreSQL logical replication and logical decoding for Amazon RDS with a parameter, a replication connection type, and a security role\. The client for logical decoding can be any client that can establish a replication connection to a database on a PostgreSQL DB instance\. 

**To turn on logical decoding for an RDS for PostgreSQL DB instance**

1. Make sure that the user account that you're using has these roles:
   + The `rds_superuser` role so you can turn on logical replication 
   + The `rds_replication` role to grant permissions to manage logical slots and to stream data using logical slots

1. Set the `rds.logical_replication` static parameter to 1\. As part of applying this parameter, also set the parameters `wal_level`, `max_wal_senders`, `max_replication_slots`, and `max_connections`\. These parameter changes can increase WAL generation, so set the `rds.logical_replication` parameter only when you are using logical slots\.

1. Reboot the DB instance for the static `rds.logical_replication` parameter to take effect\.

1. Create a logical replication slot as explained in the next section\. This process requires that you specify a decoding plugin\. Currently, RDS for PostgreSQL supports the test\_decoding and wal2json output plugins that ship with PostgreSQL\.

For more information on PostgreSQL logical decoding, see the [ PostgreSQL documentation](https://www.postgresql.org/docs/current/static/logicaldecoding-explanation.html)\.

### Working with logical replication slots<a name="PostgreSQL.Concepts.General.FeatureSupport.LogicalReplicationSlots"></a>

You can use SQL commands to work with logical slots\. For example, the following command creates a logical slot named `test_slot` using the default PostgreSQL output plugin `test_decoding`\.

```
SELECT * FROM pg_create_logical_replication_slot('test_slot', 'test_decoding');
slot_name    | xlog_position
-----------------+---------------
regression_slot | 0/16B1970
(1 row)
```

To list logical slots, use the following command\.

```
SELECT * FROM pg_replication_slots;
```

To drop a logical slot, use the following command\.

```
SELECT pg_drop_replication_slot('test_slot');
pg_drop_replication_slot
-----------------------
(1 row)
```

For more examples on working with logical replication slots, see [ Logical decoding examples](https://www.postgresql.org/docs/9.5/static/logicaldecoding-example.html) in the PostgreSQL documentation\.

After you create the logical replication slot, you can start streaming\. The following example shows how logical decoding is controlled over the streaming replication protocol\. This example uses the program pg\_recvlogical, which is included in the PostgreSQL distribution\. Doing this requires that client authentication is set up to allow replication connections\.

```
pg_recvlogical -d postgres --slot test_slot -U postgres
    --host -instance-name.111122223333.aws-region.rds.amazonaws.com 
    -f -  --start
```

To see the contents of the `pg_replication_origin_status` view, query the `pg_show_replication_origin_status` function\.

```
SELECT * FROM pg_show_replication_origin_status();
local_id | external_id | remote_lsn | local_lsn
----------+-------------+------------+-----------
(0 rows)
```

## RAM disk for the stats\_temp\_directory<a name="PostgreSQL.Concepts.General.FeatureSupport.RamDisk"></a>

You can use the RDS for PostgreSQL parameter `rds.pg_stat_ramdisk_size` to specify the system memory allocated to a RAM disk for storing the PostgreSQL `stats_temp_directory`\. The RAM disk parameter is available for all PostgreSQL versions on Amazon RDS\. 

Under certain workloads, setting this parameter can improve performance and decrease I/O requirements\. For more information about the `stats_temp_directory`, see [ the PostgreSQL documentation\.](https://www.postgresql.org/docs/current/static/runtime-config-statistics.html#GUC-STATS-TEMP-DIRECTORY)\.

To set up a RAM disk for your `stats_temp_directory`, set the `rds.pg_stat_ramdisk_size` parameter to an integer literal value in the parameter group used by your DB instance\. This parameter denotes MB, so you must use an integer value\. Expressions, formulas, and functions aren't valid for the `rds.pg_stat_ramdisk_size` parameter\. Be sure to reboot the DB instance so that the change takes effect\. For information about setting parameters, see [Working with parameter groups](USER_WorkingWithParamGroups.md)\.

For example, the following AWS CLI command sets the RAM disk parameter to 256 MB\.

```
aws rds modify-db-parameter-group \
    --db-parameter-group-name pg-95-ramdisk-testing \
    --parameters "ParameterName=rds.pg_stat_ramdisk_size, ParameterValue=256, ApplyMethod=pending-reboot"
```

After you reboot, run the following command to see the status of the `stats_temp_directory`\.

```
postgres=> SHOW stats_temp_directory;
```

 The command should return the following\.

```
stats_temp_directory
---------------------------
/rdsdbramdisk/pg_stat_tmp
(1 row)
```

## Tablespaces for RDS for PostgreSQL<a name="PostgreSQL.Concepts.General.FeatureSupport.Tablespaces"></a>

RDS for PostgreSQL supports tablespaces for compatibility\. Because all storage is on a single logical volume, you can't use tablespaces for I/O splitting or isolation\. Our benchmarks and experience indicate that a single logical volume is the best setup for most use cases\. 

To create and use tablespaces with your RDS for PostgreSQL DB instance requires the `rds_superuser` role\. Your RDS for PostgreSQL DB instance's main user account \(default name, `postgres`\) is a member of this role\. For more information, see [Understanding PostgreSQL roles and permissions](Appendix.PostgreSQL.CommonDBATasks.Roles.md)\. 

If you specify a file name when you create a tablespace, the path prefix is `/rdsdbdata/db/base/tablespace`\. The following example places tablespace files in `/rdsdbdata/db/base/tablespace/data`\. This example assumes that a `dbadmin` user \(role\) exists and that it's been granted the `rds_superuser` role needed to work with tablespaces\.

```
postgres=> CREATE TABLESPACE act_data
  OWNER dbadmin
  LOCATION '/data';
CREATE TABLESPACE
```

To learn more about PostgreSQL tablespaces, see [Tablespaces](https://www.postgresql.org/docs/current/manage-ag-tablespaces.html) in the PostgreSQL documentation\.

## RDS for PostgreSQL collations for EBCDIC and other mainframe migrations<a name="PostgreSQL.Collations.mainframe.migration"></a>

RDS for PostgreSQL versions 10 and higher include ICU version 60\.2, which is based on Unicode 10\.0 and includes collations from the Unicode Common Locale Data Repository, CLDR 32\. These software internationalization libraries ensure that character encodings are presented in a consistent way, regardless of operating system or platform\. For more information about Unicode CLDR\-32, see the [CLDR 32 Release Note](https://cldr.unicode.org/index/downloads/cldr-32) on the Unicode CLDR website\. You can learn more about the internationalization components for Unicode \(ICU\) at the [ICU Technical Committee \(ICU\-TC\)](https://icu.unicode.org/home) website\. For information about ICU\-60, see [Download ICU 60](https://icu.unicode.org/download/60)\. 

Starting with version 14\.3, RDS for PostgreSQL also includes collations that help with data integration and conversion from EBCDIC\-based systems\. The extended binary coded decimal interchange code or *EBCDIC* encoding is commonly used by mainframe operating systems\. These Amazon RDS\-provided collations are narrowly defined to sort only those Unicode characters that directly map to EBCDIC code pages\. The characters are sorted in EBCDIC code\-point order to allow for data validation after conversion\. These collations don't include denormalized forms, nor do they include Unicode characters that don't directly map to a character on the source EBCDIC code page\.

The character mappings between EBCDIC code pages and Unicode code points are based on tables published by IBM\. The complete set is available from IBM as a [compressed file](http://download.boulder.ibm.com/ibmdl/pub/software/dw/java/cdctables.zip) for download\. RDS for PostgreSQL used these mappings with tools provided by the ICU to create the collations listed in the tables in this section\. The collation names include a language and country as required by the ICU\. However, EBCDIC code pages don't specify languages, and some EBCDIC code pages cover multiple countries\. That means that the language and country portion of the collation names in the table are arbitrary, and they don't need to match the current locale\. In other words, the code page number is the most important part of the collation name in this table\. You can use any of the collations listed in the following tables in any RDS for PostgreSQL database\. 
+ [Unicode to EBCDIC collations table](#ebcdic-table) – Some mainframe data migration tools internally use LATIN1 or LATIN9 to encode and process data\. Such tools use round\-trip schemes to preserve data integrity and support reverse conversion\.The collations in this table can be used by tools that process data using LATIN1 encoding, which doesn't require special handling\. 
+ [Unicode to LATIN9 collations table](#latin9-table) – You can use these collations in any RDS for PostgreSQL database\. 

 

In the following table, you find collations available in RDS for PostgreSQL that map EBCDIC code pages to Unicode code points\. We recommend that you use the collations in this table for application development that requires sorting based on the ordering of IBM code pages\. <a name="ebcdic-table"></a>


| PostgreSQL collation name | Description of code\-page mapping and sort order | 
| --- | --- | 
| da\-DK\-cp277\-x\-icu | Unicode characters that directly map to IBM EBCDIC Code Page 277 \(per conversion tables\) are sorted in IBM CP 277 code point order | 
| de\-DE\-cp273\-x\-icu | Unicode characters that directly map to IBM EBCDIC Code Page 273 \(per conversion tables\) are sorted in IBM CP 273 code point order | 
| en\-GB\-cp285\-x\-icu | Unicode characters that directly map to IBM EBCDIC Code Page 285 \(per conversion tables\) are sorted in IBM CP 285 code point order | 
| en\-US\-cp037\-x\-icu | Unicode characters that directly map to IBM EBCDIC Code Page 037 \(per conversion tables\) are sorted in IBM CP 37 code point order | 
| es\-ES\-cp284\-x\-icu | Unicode characters that directly map to IBM EBCDIC Code Page 284 \(per conversion tables\) are sorted in IBM CP 284 code point order | 
| fi\-FI\-cp278\-x\-icu | Unicode characters that directly map to IBM EBCDIC Code Page 278 \(per conversion tables\) are sorted in IBM CP 278 code point order | 
| fr\-FR\-cp297\-x\-icu | Unicode characters that directly map to IBM EBCDIC Code Page 297 \(per conversion tables\) are sorted in IBM CP 297 code point order | 
| it\-IT\-cp280\-x\-icu | Unicode characters that directly map to IBM EBCDIC Code Page 280 \(per conversion tables\) are sorted in IBM CP 280 code point order | 
| nl\-BE\-cp500\-x\-icu | Unicode characters that directly map to IBM EBCDIC Code Page 500 \(per conversion tables\) are sorted in IBM CP 500 code point order | 

Amazon RDS provides a set of additional collations that sort Unicode code points that map to LATIN9 characters using the tables published by IBM, in the order of the original code points according to the EBCDIC code page of the source data\. <a name="latin9-table"></a>


| PostgreSQL collation name | Description of code\-page mapping and sort order | 
| --- | --- | 
| da\-DK\-cp1142m\-x\-icu | Unicode characters that map to LATIN9 characters originally converted from IBM EBCDIC Code Page 1142 \(per conversion tables\) are sorted in IBM CP 1142 code point order | 
| de\-DE\-cp1141m\-x\-icu | Unicode characters that map to LATIN9 characters originally converted from IBM EBCDIC Code Page 1141 \(per conversion tables\) are sorted in IBM CP 1141 code point order | 
| en\-GB\-cp1146m\-x\-icu | Unicode characters that map to LATIN9 characters originally converted from IBM EBCDIC Code Page 1146 \(per conversion tables\) are sorted in IBM CP 1146 code point order | 
| en\-US\-cp1140m\-x\-icu | Unicode characters that map to LATIN9 characters originally converted from IBM EBCDIC Code Page 1140 \(per conversion tables\) are sorted in IBM CP 1140 code point order | 
| es\-ES\-cp1145m\-x\-icu | Unicode characters that map to LATIN9 characters originally converted from IBM EBCDIC Code Page 1145 \(per conversion tables\) are sorted in IBM CP 1145 code point order | 
| fi\-FI\-cp1143m\-x\-icu | Unicode characters that map to LATIN9 characters originally converted from IBM EBCDIC Code Page 1143 \(per conversion tables\) are sorted in IBM CP 1143 code point order | 
| fr\-FR\-cp1147m\-x\-icu | Unicode characters that map to LATIN9 characters originally converted from IBM EBCDIC Code Page 1147 \(per conversion tables\) are sorted in IBM CP 1147 code point order | 
| it\-IT\-cp1144m\-x\-icu | Unicode characters that map to LATIN9 characters originally converted from IBM EBCDIC Code Page 1144 \(per conversion tables\) are sorted in IBM CP 1144 code point order | 
| nl\-BE\-cp1148m\-x\-icu | Unicode characters that map to LATIN9 characters originally converted from IBM EBCDIC Code Page 1148 \(per conversion tables\) are sorted in IBM CP 1148 code point order | 

In the following, you can find an example of using an RDS for PostgreSQL collation\.

```
db1=> SELECT pg_import_system_collations('pg_catalog');
 pg_import_system_collations
-----------------------------
                          36
db1=> SELECT '¤' < 'a' col1;
 col1
------
 t  
db1=> SELECT '¤' < 'a' COLLATE "da-DK-cp277-x-icu" col1;
 col1
------
 f
```

We recommend that you use the collations in the [Unicode to EBCDIC collations table](#ebcdic-table) and in the [Unicode to LATIN9 collations table](#latin9-table) for application development that requires sorting based on the ordering of IBM code pages\. The following collations \(suffixed with the letter “b”\) are also visible in `pg_collation`, but are intended for use by mainframe data integration and migration tools at AWS that map code pages with specific code point shifts and require special handling in collation\. In other words, the following collations aren't recommended for use\. 
+ da\-DK\-277b\-x\-icu
+ da\-DK\-1142b\-x\-icu
+ de\-DE\-cp273b\-x\-icu
+ de\-DE\-cp1141b\-x\-icu
+ en\-GB\-cp1146b\-x\-icu
+ en\-GB\-cp285b\-x\-icu
+ en\-US\-cp037b\-x\-icu
+ en\-US\-cp1140b\-x\-icu
+ es\-ES\-cp1145b\-x\-icu
+ es\-ES\-cp284b\-x\-icu
+ fi\-FI\-cp1143b\-x\-icu
+ fr\-FR\-cp1147b\-x\-icu
+ fr\-FR\-cp297b\-x\-icu
+ it\-IT\-cp1144b\-x\-icu
+ it\-IT\-cp280b\-x\-icu
+ nl\-BE\-cp1148b\-x\-icu
+ nl\-BE\-cp500b\-x\-icu

To learn more about migrating applications from mainframe environments to AWS, see [What is AWS Mainframe Modernization?](https://docs.aws.amazon.com/m2/latest/userguide/what-is-m2.html)\.

For more information about managing collations in PostgreSQL, see [Collation Support](https://www.postgresql.org/docs/current/collation.html) in the PostgreSQL documentation\.