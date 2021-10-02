# Transporting PostgreSQL databases between DB instances<a name="PostgreSQL.TransportableDB"></a>

By using PostgreSQL Transportable Databases for Amazon RDS, you can transport a PostgreSQL database between two DB instances\. This provides an extremely fast method of migrating large databases between separate DB instances\. To transport databases using this method, your DB instances must both run the same major version of PostgreSQL\.

To use transportable databases, install the `pg_transport` extension\. This extension provides a physical transport mechanism to move each database\. By streaming the database files with minimal processing, physical transport moves data much faster than traditional dump and load processes and takes minimal downtime\. PostgreSQL transportable databases use a pull model where the destination DB instance imports the database from the source DB instance\.

**Note**  
PostgreSQL transportable databases are available in RDS for PostgreSQL versions 10\.10 and later, and 11\.5 and later\. 

**Topics**
+ [Limitations for using PostgreSQL transportable databases](#PostgreSQL.TransportableDB.Limits)
+ [Setting up to transport PostgreSQL databases](#PostgreSQL.TransportableDB.Setup)
+ [Transporting a PostgreSQL database using the transport\.import\_from\_server function](#PostgreSQL.TransportableDB.Transporting)
+ [What happens during database transport](#PostgreSQL.TransportableDB.DuringTransport)
+ [transport\.import\_from\_server function reference](#PostgreSQL.TransportableDB.transport.import_from_server)
+ [Configuration parameters for the pg\_transport extension](#PostgreSQL.TransportableDB.Parameters)

## Limitations for using PostgreSQL transportable databases<a name="PostgreSQL.TransportableDB.Limits"></a>

Transportable databases have the following limitations:
+ **Read replicas ** – You can't use transportable databases on read replicas or parent instances of read replicas\.
+ **Unsupported column types** – You can't use the `reg` data types in any database tables that you plan to transport with this method\. These types depend on system catalog object IDs \(OIDs\), which often change during transport\.
+ **Tablespaces** – All source database objects must be in the default `pg_default` tablespace\. 
+ **Compatibility** – Both the source and destination DB instances must run the same major version of PostgreSQL\. 

  Before transport begins, the `transport.import_from_server` function compares the source and destination DB instances to ensure database compatibility\. This includes verifying PostgreSQL major version compatibility\. Also, the function verifies that the destination DB instance likely has enough space to receive the source database\. The function performs several additional checks to make sure that the transport is smooth\.
+ **Extensions** – The only extension that you can install on the source DB instance during transport is `pg_transport`\.
+ **Roles and ACLs** – The source database's access privileges and ownership information aren't carried over to the destination database\. All database objects are created and owned by the local destination user of the transport\.
+ **Concurrent transports** – You can run up to 32 total transports at the same time on a DB instance, including both imports and exports\. To define the worker processes used for each transport, use the `pg_transport.work_mem` and `pg_transport.num_workers` parameters\. To accommodate concurrent transports, you might need to increase the `max_worker_processes` parameter quite a bit\. For more information, see [ Configuration parameters for the pg\_transport extension](#PostgreSQL.TransportableDB.Parameters)\.

## Setting up to transport PostgreSQL databases<a name="PostgreSQL.TransportableDB.Setup"></a>

To prepare to transport a PostgreSQL database from one DB instance to another, take the following steps\. 

**To set up for transporting a PostgreSQL database**

1. Make sure that the source DB instance's security group allows inbound traffic from the destination DB instance\. This is required because the destination DB instance starts the database transport with an import call to the source DB instance\. For information about how to use security groups, see [Controlling access with security groups](Overview.RDSSecurityGroups.md)\.

1. For both the source and destination DB instances, add `pg_transport` to the `shared_preload_libraries` parameter for each parameter group\. The `shared_preload_libraries` parameter is static and requires a database restart for changes to take effect\. For information about parameter groups, see [Working with DB parameter groups](USER_WorkingWithParamGroups.md)\.

1. For both the source and destination DB instances, install the required `pg_transport` PostgreSQL extension\.

   To do so, start psql as a user with the `rds_superuser` role for each DB instance, and then run the following command\.

   ```
   psql=> CREATE EXTENSION pg_transport;
   ```

## Transporting a PostgreSQL database using the transport\.import\_from\_server function<a name="PostgreSQL.TransportableDB.Transporting"></a>

After you complete the process described in [ Setting up to transport PostgreSQL databases](#PostgreSQL.TransportableDB.Setup), you can start the transport\. To do so, run the [transport\.import\_from\_server](#PostgreSQL.TransportableDB.transport.import_from_server) function on the destination DB instance\. 

**Note**  
Both the destination user for transport and the source user for the connection must be members of the `rds_superuser` role\.   
The destination DB instance can't already contain a database with the same name as the source database to be transported, or the transport fails\.

The following shows an example transport\.

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

This function requires that you provide database user passwords\. Thus, we recommend that you change the passwords of the user roles you used after transport is complete\. Or, you can use SQL bind variables to create temporary user roles\. Use these temporary roles for the transport and then discard the roles afterwards\. 

For details of the `transport.import_from_server` function and its parameters, see [ transport\.import\_from\_server function reference](#PostgreSQL.TransportableDB.transport.import_from_server)\.

## What happens during database transport<a name="PostgreSQL.TransportableDB.DuringTransport"></a>

The `transport.import_from_server` function creates the in\-transit database on the destination DB instance\. The in\-transit database is inaccessible on the destination DB instance for the duration of the transport\.

When transport begins, all current sessions on the source database are ended\. Any databases other than the source database on the source DB instance aren't affected by the transport\. 

The source database is put into a special read\-only mode\. While it's in this mode, you can connect to the source database and run read\-only queries\. However, write\-enabled queries and some other types of commands are blocked\. Only the specific source database that is being transported is affected by these restrictions\. 

During transport, you can't restore the destination DB instance to a point in time\. This is because the transport isn't transactional and doesn't use the PostgreSQL write\-ahead log to record changes\. If the destination DB instance has automatic backups enabled, a backup is automatically taken after transport completes\. Point\-in\-time restores are available for times after the backup finishes\.

If the transport fails, the `pg_transport` extension attempts to undo all changes to the source and destination DB instances\. This includes removing the destination's partially transported database\. Depending on the type of failure, the source database might continue to reject write\-enabled queries\. If this happens, use the following command to allow write\-enabled queries\.

```
ALTER DATABASE my-database SET default_transaction_read_only = false;
```

## transport\.import\_from\_server function reference<a name="PostgreSQL.TransportableDB.transport.import_from_server"></a>

The `transport.import_from_server` function transports a PostgreSQL database by importing it from a source DB instance to a destination DB instance\. It does this by using a physical database connection transport mechanism\.

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

For an example, see [ Transporting a PostgreSQL database using the transport\.import\_from\_server function](#PostgreSQL.TransportableDB.Transporting)\.

## Configuration parameters for the pg\_transport extension<a name="PostgreSQL.TransportableDB.Parameters"></a>

Use the following parameters to configure the `pg_transport` extension behavior\.

```
SET pg_transport.num_workers = integer; 
SET pg_transport.work_mem = kilobytes;
SET pg_transport.timing = Boolean;
```

You can find descriptions of these parameters in the following table\.


****  

| Parameter | Description | 
| --- | --- | 
| pg\_transport\.num\_workers |  The number of workers to use for a physical transport\. The default is 3\. Valid values are 1–32\. Even large transports typically reach their maximum throughput with fewer than 8 workers\. During transport, the `pg_transport.num_workers` setting on the destination DB instance is used on both the destination and source DB instances\.  A related parameter is the PostgreSQL `max_worker_processes` parameter\. The transport process creates several background worker processes\. Thus, your setting for the `pg_transport.num_workers` parameter might require you to set the `max_worker_processes` parameter significantly higher on both the source and destination DB instances\.  We recommend that you set `max_worker_processes` on both the source and destination DB instances to at least three times the destination DB instance's setting for the `pg_transport.num_workers` parameter\. Add a few more to provide nontransport background worker processes\. For more information about the `max_worker_processes` parameter, see the PostgreSQL documentation about [Asynchronous behavior](https://www.postgresql.org/docs/devel/runtime-config-resource.html#RUNTIME-CONFIG-RESOURCE-ASYNC-BEHAVIOR)\.  | 
| pg\_transport\.timing  |  A Boolean value that specifies whether to report timing information during the transport\. The default is `true`\. Valid values are `true` to report timing information and `false` to disable the reporting of timing information\. We don't recommend that you set this parameter to `false`\. Disabling `pg_transport.timing` significantly reduces your ability to track the progress of transports\.  | 
| pg\_transport\.work\_mem  |  The maximum amount of memory to allocate for each worker\. The default is 131,072 kilobytes \(KB\)\. The minimum value is 64 megabytes \(65,536 KB\)\. Valid values are in kilobytes \(KBs\) as binary base\-2 units, where 1 KB = 1,024 bytes\.  The transport might use less memory than is specified in this parameter\. Even large transports typically reach their maximum throughput with less than 256 MB \(262,144 KB\) of memory per worker\.  | 