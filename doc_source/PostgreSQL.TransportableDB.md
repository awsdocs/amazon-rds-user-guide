# Transporting PostgreSQL databases between DB instances<a name="PostgreSQL.TransportableDB"></a>

By using PostgreSQL transportable databases for Amazon RDS, you can move a PostgreSQL database between two DB instances\. This is a very fast way to migrate large databases between different DB instances\. To use this approach, your DB instances must both run the same major version of PostgreSQL\. 

This capability requires that you install the `pg_transport` extension on both the source and the destination DB instance\. The `pg_transport` extension provides a physical transport mechanism that moves the database files with minimal processing\. This mechanism moves data much faster than traditional dump and load processes, with less downtime\. 

**Note**  
PostgreSQL transportable databases are available in RDS for PostgreSQL 11\.5 and higher, and RDS for PostgreSQL version 10\.10 and higher\.

To transport a PostgreSQL DB instance from one RDS for PostgreSQL DB instance to another, you first set up the source and destination instances as detailed in [ Setting up a DB instance for transport](#PostgreSQL.TransportableDB.Setup)\. You can then transport the database by using the function described in [ Transporting a PostgreSQL database](#PostgreSQL.TransportableDB.Transporting)\. 

**Topics**
+ [Limitations for using PostgreSQL transportable databases](#PostgreSQL.TransportableDB.Limits)
+ [Setting up to transport a PostgreSQL database](#PostgreSQL.TransportableDB.Setup)
+ [Transporting a PostgreSQL database to the destination from the source](#PostgreSQL.TransportableDB.Transporting)
+ [What happens during database transport](#PostgreSQL.TransportableDB.DuringTransport)
+ [Transportable databases function reference](#PostgreSQL.TransportableDB.transport.import_from_server)
+ [Transportable databases parameter reference](#PostgreSQL.TransportableDB.Parameters)

## Limitations for using PostgreSQL transportable databases<a name="PostgreSQL.TransportableDB.Limits"></a>

Transportable databases have the following limitations:
+ **Read replicas ** – You can't use transportable databases on read replicas or parent instances of read replicas\.
+ **Unsupported column types** – You can't use the `reg` data types in any database tables that you plan to transport with this method\. These types depend on system catalog object IDs \(OIDs\), which often change during transport\.
+ **Tablespaces** – All source database objects must be in the default `pg_default` tablespace\. 
+ **Compatibility** – Both the source and destination DB instances must run the same major version of PostgreSQL\. 
+ **Extensions** – The source DB instance can have only the `pg_transport` installed\. 
+ **Roles and ACLs** – The source database's access privileges and ownership information aren't carried over to the destination database\. All database objects are created and owned by the local destination user of the transport\.
+ **Concurrent transports** – A single DB instance can support up to 32 concurrent transports, including both imports and exports, if worker processes have been configured properly\. 
+ **RDS for PostgreSQL DB instances only** – PostgreSQL transportable databases are supported on RDS for PostgreSQL DB instances only\. You can't use it with on\-premises databases or databases running on Amazon EC2\.

## Setting up to transport a PostgreSQL database<a name="PostgreSQL.TransportableDB.Setup"></a>

Before you begin, make sure that your RDS for PostgreSQL DB instances meet the following requirements:
+ The RDS for PostgreSQL DB instances for source and destination must run the same version of PostgreSQL\.
+ The destination DB can't have a database of the same name as the source DB that you want to transport\.
+ The account you use to run the transport needs `rds_superuser` privileges on both the source DB and the destination DB\. 
+ The security group for the source DB instance must allow inbound access from the destination DB instance\. This might already be the case if your source and destination DB instances are located in the VPC\. For more information about security groups, see [Controlling access with security groups](Overview.RDSSecurityGroups.md)\.

Transporting databases from a source DB instance to a destination DB instance requires several changes to the DB parameter group associated with each instance\. That means that you must create a custom DB parameter group for the source DB instance and create a custom DB parameter group for the destination DB instance\.

**Note**  
If your DB instances are already configured using custom DB parameter groups, you can start with step 2 in the following procedure\. 

**To configure the custom DB group parameters for transporting databases**

For the following steps, use an account that has `rds_superuser` privileges\. 

1. If the source and destination DB instances use a default DB parameter group, you need to create a custom DB parameter using the appropriate version for your instances\. You do this so you can change values for several parameters\. For more information, see [Working with parameter groups](USER_WorkingWithParamGroups.md)\. 

1. In the custom DB parameter group, change values for the following parameters:
   + `shared_preload_libraries` – Add `pg_transport` to the list of libraries\. 
   + `pg_transport.num_workers` – The default value is 3\. Increase or reduce this value as needed for your database\. For a 200 GB database, we recommend no larger than 8\. Keep in mind that if you increase the default value for this parameter, you should also increase the value of `max_worker_processes`\. 
   + `pg_transport.work_mem` – The default value is either 128 MB or 256 MB, depending on the PostgreSQL version\. The default setting can typically be left unchanged\. 
   + `max_worker_processes` – The value of this parameter needs to be set using the following calculation:

     ```
     3 * pg_transport.num_workers) + 9
     ```

     This value is required on the destination to handle various background worker processes involved in the transport\. To learn more about `max_worker_processes,` see [Resource Consumption](https://www.postgresql.org/docs/current/runtime-config-resource.html) in the PostgreSQL documentation\. 

   For more information about `pg_transport` parameters, see [Transportable databases parameter reference ](#PostgreSQL.TransportableDB.Parameters)\.

1. Reboot the source RDS for PostgreSQL DB instance and the destination instance so that the settings for the parameters take effect\.

1. Connect to your RDS for PostgreSQL source DB instance\.

   ```
   psql --host=source-instance.111122223333.aws-region.rds.amazonaws.com --port=5432 --username=postgres --password
   ```

1. Remove extraneous extensions from the public schema of the DB instance\. Only the `pg_transport` extension is allowed during the actual transport operation\.

1. Install the `pg_transport` extension as follows:

   ```
   postgres=> CREATE EXTENSION pg_transport;
   CREATE EXTENSION
   ```

1. Connect to your RDS for PostgreSQL destination DB instance\. Remove any extraneous extensions, and then install the `pg_transport` extension\.

   ```
   postgres=> CREATE EXTENSION pg_transport;
   CREATE EXTENSION
   ```

## Transporting a PostgreSQL database to the destination from the source<a name="PostgreSQL.TransportableDB.Transporting"></a>

After you complete the process described in [Setting up to transport a PostgreSQL database](#PostgreSQL.TransportableDB.Setup), you can start the transport\. To do so, run the `transport.import_from_server` function on the destination DB instance\. In the syntax following you can find the function parameters\.

```
SELECT transport.import_from_server( 
   'source-db-instance-endpoint', 
    source-db-instance-port, 
   'source-db-instance-user', 
   'source-user-password', 
   'source-database-name', 
   'destination-user-password', 
   false);
```

The `false` value shown in the example tells the function that this is not a dry run\. To test your transport setup, you can specify `true` for the `dry_run` option when you call the function, as shown following:

```
postgres=> SELECT transport.import_from_server(
    'docs-lab-source-db.666666666666aws-region.rds.amazonaws.com', 5432,
    'postgres', '********', 'labdb', '******', true);
INFO:  Starting dry-run of import of database "labdb".
INFO:  Created connections to remote database        (took 0.03 seconds).
INFO:  Checked remote cluster compatibility          (took 0.05 seconds).
INFO:  Dry-run complete                         (took 0.08 seconds total).
 import_from_server
--------------------

(1 row)
```

The INFO lines are output because the `pg_transport.timing` parameter is set to its default value, `true`\. Set the `dry_run` to `false` when you run the command and the source database is imported to the destination, as shown following:

```
INFO:  Starting import of database "labdb".
INFO:  Created connections to remote database        (took 0.02 seconds).
INFO:  Marked remote database as read only           (took 0.13 seconds).
INFO:  Checked remote cluster compatibility          (took 0.03 seconds).
INFO:  Signaled creation of PITR blackout window     (took 2.01 seconds).
INFO:  Applied remote database schema pre-data       (took 0.50 seconds).
INFO:  Created connections to local cluster          (took 0.01 seconds).
INFO:  Locked down destination database              (took 0.00 seconds).
INFO:  Completed transfer of database files          (took 0.24 seconds).
INFO:  Completed clean up                            (took 1.02 seconds).
INFO:  Physical transport complete              (took 3.97 seconds total).
import_from_server
--------------------
(1 row)
```

This function requires that you provide database user passwords\. Thus, we recommend that you change the passwords of the user roles you used after transport is complete\. Or, you can use SQL bind variables to create temporary user roles\. Use these temporary roles for the transport and then discard the roles afterwards\. 

If your transport isn't successful, you might see an error message similar to the following:

```
pg_transport.num_workers=8 25% of files transported failed to download file data
```

The "failed to download file data" error message indicates that the number of worker processes isn't set correctly for the size of the database\. You might need to increase or decrease the value set for `pg_transport.num_workers`\. Each failure reports the percentage of completion, so you can see the impact of your changes\. For example, changing the setting from 8 to 4 in one case resulted in the following:

```
pg_transport.num_workers=4 75% of files transported failed to download file data
```

Keep in mind that the `max_worker_processes` parameter is also taken into account during the transport process\. In other words, you might need to modify both `pg_transport.num_workers` and `max_worker_processes` to successfully transport the database\. The example shown finally worked when the `pg_transport.num_workers` was set to 2:

```
pg_transport.num_workers=2 100% of files transported
```

For more information about the `transport.import_from_server` function and its parameters, see [Transportable databases function reference](#PostgreSQL.TransportableDB.transport.import_from_server)\. 

## What happens during database transport<a name="PostgreSQL.TransportableDB.DuringTransport"></a>

The PostgreSQL transportable databases feature uses a pull model to import the database from the source DB instance to the destination\. The `transport.import_from_server` function creates the in\-transit database on the destination DB instance\. The in\-transit database is inaccessible on the destination DB instance for the duration of the transport\.

When transport begins, all current sessions on the source database are ended\. Any databases other than the source database on the source DB instance aren't affected by the transport\. 

The source database is put into a special read\-only mode\. While it's in this mode, you can connect to the source database and run read\-only queries\. However, write\-enabled queries and some other types of commands are blocked\. Only the specific source database that is being transported is affected by these restrictions\. 

During transport, you can't restore the destination DB instance to a point in time\. This is because the transport isn't transactional and doesn't use the PostgreSQL write\-ahead log to record changes\. If the destination DB instance has automatic backups enabled, a backup is automatically taken after transport completes\. Point\-in\-time restores are available for times *after* the backup finishes\.

If the transport fails, the `pg_transport` extension attempts to undo all changes to the source and destination DB instances\. This includes removing the destination's partially transported database\. Depending on the type of failure, the source database might continue to reject write\-enabled queries\. If this happens, use the following command to allow write\-enabled queries\.

```
ALTER DATABASE db-name SET default_transaction_read_only = false;
```

## Transportable databases function reference<a name="PostgreSQL.TransportableDB.transport.import_from_server"></a>

The `transport.import_from_server` function transports a PostgreSQL database by importing it from a source DB instance to a destination DB instance\. It does this by using a physical database connection transport mechanism\.

Before starting the transport, this function verifies that the source and the destination DB instances are the same version and are compatible for the migration\. It also confirms that the destination DB instance has enough space for the source\. 

**Syntax**

```
transport.import_from_server(
   host text,
   port int,
   username text,
   password text,
   database text,
   local_password text,
   dry_run bool
)
```

**Return Value**

None\.

**Parameters**

You can find descriptions of the `transport.import_from_server` function parameters in the following table\.


****  

| Parameter | Description | 
| --- | --- | 
| host |  The endpoint of the source DB instance\.  | 
| port | An integer representing the port of the source DB instance\. PostgreSQL DB instances often use port 5432\. | 
| username |  The user of the source DB instance\. This user must be a member of the `rds_superuser` role\.  | 
| password |  The user password of the source DB instance\.  | 
| database |  The name of the database in the source DB instance to transport\.  | 
| local\_password |  The local password of the current user for the destination DB instance\. This user must be a member of the `rds_superuser` role\.  | 
| dry\_run | An optional Boolean value specifying whether to perform a dry run\. The default is `false`, which means the transport proceeds\.To confirm compatibility between the source and destination DB instances without performing the actual transport, set dry\_run to true\. | 

**Example**

For an example, see [ Transporting a PostgreSQL database to the destination from the source](#PostgreSQL.TransportableDB.Transporting)\.

## Transportable databases parameter reference<a name="PostgreSQL.TransportableDB.Parameters"></a>

Several parameters control the behavior of the `pg_transport` extension\. Following, you can find descriptions of these parameters\. 

**`pg_transport.num_workers`**  
The number of workers to use for the transport process\. The default is 3\. Valid values are 1–32\. Even the largest database transports typically require fewer than 8 workers\. The value of this setting on the destination DB instance is used by both destination and source during transport\.

**`pg_transport.timing` **  
Specifies whether to report timing information during the transport\. The default is `true`, meaning that timing information is reported\. We recommend that you leave this parameter set to `true` so you can monitor progress\. For example output, see [ Transporting a PostgreSQL database to the destination from the source](#PostgreSQL.TransportableDB.Transporting)\.

**`pg_transport.work_mem`**  
The maximum amount of memory to allocate for each worker\. The default is 131072 kilobytes \(KB\) or 262144 KB \(256 MB\), depending on the PostgreSQL version\. The minimum value is 64 megabytes \(65536 KB\)\. Valid values are in kilobytes \(KBs\) as binary base\-2 units, where 1 KB = 1024 bytes\.   
The transport might use less memory than is specified in this parameter\. Even large database transports typically require less than 256 MB \(262144 KB\) of memory per worker\.