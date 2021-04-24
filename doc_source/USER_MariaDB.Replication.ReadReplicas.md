# Working with MariaDB read replicas<a name="USER_MariaDB.Replication.ReadReplicas"></a>

This section contains specific information about working with read replicas on Amazon RDS for MariaDB\. For general information about read replicas and instructions for using them, see [Working with read replicas](USER_ReadRepl.md)\.

**Topics**
+ [Read replica configuration with MariaDB](#USER_MariaDB.Replication.ReadReplicas.Configuration)
+ [Configuring replication filters with MariaDB](#USER_MariaDB.Replication.ReadReplicas.ReplicationFilters)
+ [Read replica updates with MariaDB](#USER_MariaDB.Replication.ReadReplicas.Updates)
+ [Multi\-AZ read replica deployments with MariaDB](#USER_MariaDB.Replication.ReadReplicas.MultiAZ)
+ [Monitoring MariaDB read replicas](#USER_MariaDB.Replication.ReadReplicas.Monitor)
+ [Starting and stopping replication with MariaDB read replicas](#USER_MariaDB.Replication.ReadReplicas.StartStop)
+ [Troubleshooting a MariaDB read replica problem](#USER_ReadRepl.Troubleshooting.MariaDB)

## Read replica configuration with MariaDB<a name="USER_MariaDB.Replication.ReadReplicas.Configuration"></a>

Before a MariaDB DB instance can serve as a replication source, you must enable automatic backups on the source DB instance by setting the backup retention period to a value other than 0\. This requirement also applies to a read replica that is the source DB instance for another read replica\. 

You can create up to five read replicas from one DB instance\. For replication to operate effectively, each read replica should have as the same amount of compute and storage resources as the source DB instance\. If you scale the source DB instance, also scale the read replicas\. 

If a read replica is running any version of MariaDB, you can specify it as the source DB instance for another read replica\. For example, you can create ReadReplica1 from MyDBInstance, and then create ReadReplica2 from ReadReplica1\. Updates made to MyDBInstance are replicated to ReadReplica1 and then replicated from ReadReplica1 to ReadReplica2\. You can't have more than four instances involved in a replication chain\. For example, you can create ReadReplica1 from MySourceDBInstance, and then create ReadReplica2 from ReadReplica1, and then create ReadReplica3 from ReadReplica2, but you can't create a ReadReplica4 from ReadReplica3\. 

If you promote a MariaDB read replica that is in turn replicating to other read replicas, those read replicas remain active\. Consider an example where MyDBInstance1 replicates to MyDBInstance2, and MyDBInstance2 replicates to MyDBInstance3\. If you promote MyDBInstance2, replication from MyDBInstance1 to MyDBInstance2 no longer occurs, but MyDBInstance2 still replicates to MyDBInstance3\. 

To enable automatic backups on a read replica for Amazon RDS for MariaDB, first create the read replica, then modify the read replica to enable automatic backups\. 

You can run multiple concurrent read replica create or delete actions that reference the same source DB instance, as long as you stay within the limit of five read replicas for the source instance\. 

## Configuring replication filters with MariaDB<a name="USER_MariaDB.Replication.ReadReplicas.ReplicationFilters"></a>

You can use replication filters to specify which databases and tables are replicated with a read replica\. Replication filters can include databases and tables in replication or exclude them from replication\.

The following are some use cases for replication filters:
+ To reduce the size of a read replica\. With replication filtering, you can exclude the databases and tables that aren't needed on the read replica\.
+ To exclude databases and tables from read replicas for security reasons\.
+ To replicate different databases and tables for specific use cases at different read replicas\. For example, you might use specific read replicas for analytics or sharding\.
+ For a DB instance that has read replicas in different AWS Regions, to replicate different databases or tables in different AWS Regions\.

**Topics**
+ [Replication filtering parameters for Amazon RDS for MariaDB](#USER_MariaDB.Replication.ReadReplicas.ReplicationFilters.Configuring)
+ [Replication filtering limitations for Amazon RDS for MariaDB](#USER_MariaDB.Replication.ReadReplicas.ReplicationFilters.Limitations)
+ [Replication filtering examples for Amazon RDS for MariaDB](#USER_MariaDB.Replication.ReadReplicas.ReplicationFilters.Examples)
+ [Viewing the replication filters for a read replica](#USER_MariaDB.Replication.ReadReplicas.ReplicationFilters.Viewing)

### Replication filtering parameters for Amazon RDS for MariaDB<a name="USER_MariaDB.Replication.ReadReplicas.ReplicationFilters.Configuring"></a>

To configure replication filters, set the following replication filtering parameters on the read replica:
+ `replicate-do-db` – Replicate changes to the specified databases\. When you set this parameter for a read replica, only the databases specified in the parameter are replicated\.
+ `replicate-ignore-db` – Don't replicate changes to the specified databases\. When the `replicate-do-db` parameter is set for a read replica, this parameter isn't evaluated\.
+ `replicate-do-table` – Replicate changes to the specified tables\. When you set this parameter for a read replica, only the tables specified in the parameter are replicated\. Also, when the `replicate-do-db` or `replicate-ignore-db` parameter is set, the database that includes the specified tables must be included in replication with the read replica\.
+ `replicate-ignore-table` – Don't replicate changes to the specified tables\. When the `replicate-do-table` parameter is set for a read replica, this parameter isn't evaluated\.
+ `replicate-wild-do-table` – Replicate tables based on the specified database and table name patterns\. The `%` and `_` wildcard characters are supported\. When the `replicate-do-db` or `replicate-ignore-db` parameter is set, make sure to include the database that includes the specified tables in replication with the read replica\.
+ `replicate-wild-ignore-table` – Don't replicate tables based on the specified database and table name patterns\. The `%` and `_` wildcard characters are supported\. When the `replicate-do-table` or `replicate-wild-do-table` parameter is set for a read replica, this parameter isn't evaluated\.

The parameters are evaluated in the order that they are listed\. For more information about how these parameters work, see [the MariaDB documentation](https://mariadb.com/kb/en/replication-filters/#replication-filters-for-replication-slaves)\.

By default, each of these parameters has an empty value\. On each read replica, you can use these parameters to set, change, and delete replication filters\. When you set one of these parameters, separate each filter from others with a comma\.

You can use the `%` and `_` wildcard characters in the `replicate-wild-do-table` and `replicate-wild-ignore-table` parameters\. The `%` wildcard matches any number of characters, and the `_` wildcard matches only one character\. 

The binary logging format of the source DB instance is important for replication because it determines the record of data changes\. The setting of the `binlog_format` parameter determines whether the replication is row\-based or statement\-based\. For more information, see [Binary logging format](USER_LogAccess.Concepts.MariaDB.md#USER_LogAccess.MariaDB.BinaryFormat)\.

**Note**  
All data definition language \(DDL\) statements are replicated as statements, regardless of the `binlog_format` setting on the source DB instance\. 

### Replication filtering limitations for Amazon RDS for MariaDB<a name="USER_MariaDB.Replication.ReadReplicas.ReplicationFilters.Limitations"></a>

The following limitations apply to replication filtering for Amazon RDS for MariaDB:
+ Each replication filtering parameter has a 2,000\-character limit\.
+ Commas aren't supported in replication filters\.
+ The MariaDB `binlog_do_db` and `binlog_ignore_db` options for binary log filtering aren't supported\.
+ Replication filtering doesn't support XA transactions\.

  For more information, see [ Restrictions on XA Transactions](https://dev.mysql.com/doc/refman/8.0/en/xa-restrictions.html) in the MySQL documentation\.
+ Replication filtering is supported for Amazon RDS for MariaDB version 10\.3\.13 and higher 10\.3 versions, all 10\.4 versions, and all 10\.5 versions\.
+ Replication filtering isn't supported for Amazon RDS for MariaDB version 10\.0, 10\.1, and 10\.2\.

### Replication filtering examples for Amazon RDS for MariaDB<a name="USER_MariaDB.Replication.ReadReplicas.ReplicationFilters.Examples"></a>

To configure replication filtering for a read replica, modify the replication filtering parameters in the parameter group associated with the read replica\.

**Note**  
You can't modify a default parameter group\. If the read replica is using a default parameter group, create a new parameter group and associate it with the read replica\. For more information on DB parameter groups, see [Working with DB parameter groups](USER_WorkingWithParamGroups.md)\.

You can set parameters in a parameter group using the AWS Management Console, AWS CLI, or RDS API\. For information about setting parameters, see [Modifying parameters in a DB parameter group](USER_WorkingWithParamGroups.md#USER_WorkingWithParamGroups.Modifying)\. When you set parameters in a parameter group, all of the DB instances associated with the parameter group use the parameter settings\. If you set the replication filtering parameters in a parameter group, make sure that the parameter group is associated only with read replicas\. Leave the replication filtering parameters empty for source DB instances\.

The following examples set the parameters using the AWS CLI\. These examples set `ApplyMethod` to `immediate` so that the parameter changes occur immediately after the CLI command completes\. If you want a pending change to be applied after the read replica is rebooted, set `ApplyMethod` to `pending-reboot`\. 

The following examples set replication filters:
+ [Including databases in replication](#rep-filter-in-dbs-mariadb)
+ [Including tables in replication](#rep-filter-in-tables-mariadb)
+ [Including tables in replication with wildcard characters](#rep-filter-in-tables-wildcards-mariadb)
+ [Escaping wildcard characters in names](#rep-filter-escape-wildcards-mariadb)
+ [Excluding databases from replication](#rep-filter-ex-dbs-mariadb)
+ [Excluding tables from replication](#rep-filter-ex-tables-mariadb)
+ [Excluding tables from replication using wildcard characters](#rep-filter-ex-tables-wildcards-mariadb)<a name="rep-filter-in-dbs-mariadb"></a>

**Example Including databases in replication**  
The following example includes the `mydb1` and `mydb2` databases in replication\. When you set `replicate-do-db` for a read replica, only the databases specified in the parameter are replicated\.  
For Linux, macOS, or Unix:  

```
aws rds modify-db-parameter-group \
  --db-parameter-group-name myparametergroup \
  --parameters "[{"ParameterName": "replicate-do-db", "ParameterValue": "mydb1,mydb2", "ApplyMethod":"immediate"}]"
```
For Windows:  

```
aws rds modify-db-parameter-group ^
  --db-parameter-group-name myparametergroup ^
  --parameters "[{"ParameterName": "replicate-do-db", "ParameterValue": "mydb1,mydb2", "ApplyMethod":"immediate"}]"
```<a name="rep-filter-in-tables-mariadb"></a>

**Example Including tables in replication**  
The following example includes the `table1` and `table2` tables in database `mydb1` in replication\.  
For Linux, macOS, or Unix:  

```
aws rds modify-db-parameter-group \
  --db-parameter-group-name myparametergroup \
  --parameters "[{"ParameterName": "replicate-do-table", "ParameterValue": "mydb1.table1,mydb1.table2", "ApplyMethod":"immediate"}]"
```
For Windows:  

```
aws rds modify-db-parameter-group ^
  --db-parameter-group-name myparametergroup ^
  --parameters "[{"ParameterName": "replicate-do-table", "ParameterValue": "mydb1.table1,mydb1.table2", "ApplyMethod":"immediate"}]"
```<a name="rep-filter-in-tables-wildcards-mariadb"></a>

**Example Including tables in replication using wildcard characters**  
The following example includes tables with names that begin with `orders` and `returns` in database `mydb` in replication\.  
For Linux, macOS, or Unix:  

```
aws rds modify-db-parameter-group \
  --db-parameter-group-name myparametergroup \
  --parameters "[{"ParameterName": "replicate-wild-do-table", "ParameterValue": "mydb.orders%,mydb.returns%", "ApplyMethod":"immediate"}]"
```
For Windows:  

```
aws rds modify-db-parameter-group ^
  --db-parameter-group-name myparametergroup ^
  --parameters "[{"ParameterName": "replicate-wild-do-table", "ParameterValue": "mydb.orders%,mydb.returns%", "ApplyMethod":"immediate"}]"
```<a name="rep-filter-escape-wildcards-mariadb"></a>

**Example Escaping wildcard characters in names**  
The following example shows you how to use the escape character `\` to escape a wildcard character that is part of a name\.   
Assume that you have several table names in database `mydb1` that start with `my_table`, and you want to include these tables in replication\. The table names include an underscore, which is also a wildcard character, so the example escapes the underscore in the table names\.  
For Linux, macOS, or Unix:  

```
aws rds modify-db-parameter-group \
  --db-parameter-group-name myparametergroup \
  --parameters "[{"ParameterName": "replicate-wild-do-table", "ParameterValue": "my\_table%", "ApplyMethod":"immediate"}]"
```
For Windows:  

```
aws rds modify-db-parameter-group ^
  --db-parameter-group-name myparametergroup ^
  --parameters "[{"ParameterName": "replicate-wild-do-table", "ParameterValue": "my\_table%", "ApplyMethod":"immediate"}]"
```<a name="rep-filter-ex-dbs-mariadb"></a>

**Example Excluding databases from replication**  
The following example excludes the `mydb1` and `mydb2` databases from replication\.  
For Linux, macOS, or Unix:  

```
aws rds modify-db-parameter-group \
  --db-parameter-group-name myparametergroup \
  --parameters "[{"ParameterName": "replicate-ignore-db", "ParameterValue": "mydb1,mydb2", "ApplyMethod":"immediate"}]"
```
For Windows:  

```
aws rds modify-db-parameter-group ^
  --db-parameter-group-name myparametergroup ^
  --parameters "[{"ParameterName": "replicate-ignore-db", "ParameterValue": "mydb1,mydb2", "ApplyMethod":"immediate"}]"
```<a name="rep-filter-ex-tables-mariadb"></a>

**Example Excluding tables from replication**  
The following example excludes tables `table1` and `table2` in database `mydb1` from replication\.  
For Linux, macOS, or Unix:  

```
aws rds modify-db-parameter-group \
  --db-parameter-group-name myparametergroup \
  --parameters "[{"ParameterName": "replicate-ignore-table", "ParameterValue": "mydb1.table1,mydb1.table2", "ApplyMethod":"immediate"}]"
```
For Windows:  

```
aws rds modify-db-parameter-group ^
  --db-parameter-group-name myparametergroup ^
  --parameters "[{"ParameterName": "replicate-ignore-table", "ParameterValue": "mydb1.table1,mydb1.table2", "ApplyMethod":"immediate"}]"
```<a name="rep-filter-ex-tables-wildcards-mariadb"></a>

**Example Excluding tables from replication using wildcard characters**  
The following example excludes tables with names that begin with `orders` and `returns` in database `mydb` from replication\.  
For Linux, macOS, or Unix:  

```
aws rds modify-db-parameter-group \
  --db-parameter-group-name myparametergroup \
  --parameters "[{"ParameterName": "replicate-wild-ignore-table", "ParameterValue": "mydb.orders%,mydb.returns%", "ApplyMethod":"immediate"}]"
```
For Windows:  

```
aws rds modify-db-parameter-group ^
  --db-parameter-group-name myparametergroup ^
  --parameters "[{"ParameterName": "replicate-wild-ignore-table", "ParameterValue": "mydb.orders%,mydb.returns%", "ApplyMethod":"immediate"}]"
```

### Viewing the replication filters for a read replica<a name="USER_MariaDB.Replication.ReadReplicas.ReplicationFilters.Viewing"></a>

You can view the replication filters for a read replica in the following ways:
+ Check the settings of the replication filtering parameters in the parameter group associated with the read replica\.

  For instructions, see [Viewing parameter values for a DB parameter group](USER_WorkingWithParamGroups.md#USER_WorkingWithParamGroups.Viewing)\.
+ In a MariaDB client, connect to the read replica and run the `SHOW SLAVE STATUS` statement\.

  In the output, the following fields show the replication filters for the read replica:
  + `Replicate_Do_DB`
  + `Replicate_Ignore_DB`
  + `Replicate_Do_Table`
  + `Replicate_Ignore_Table`
  + `Replicate_Wild_Do_Table`
  + `Replicate_Wild_Ignore_Table`

  For more information about these fields, see [Checking Replication Status](https://dev.mysql.com/doc/refman/8.0/en/replication-administration-status.html) in the MySQL documentation\.

## Read replica updates with MariaDB<a name="USER_MariaDB.Replication.ReadReplicas.Updates"></a>

Read replicas are designed to support read queries, but you might need occasional updates\. For example, you might need to add an index to speed the specific types of queries accessing the replica\. You can enable updates by setting the `read_only` parameter to **0** in the DB parameter group for the read replica\. 

## Multi\-AZ read replica deployments with MariaDB<a name="USER_MariaDB.Replication.ReadReplicas.MultiAZ"></a>

You can create a read replica from either single\-AZ or Multi\-AZ DB instance deployments\. You use Multi\-AZ deployments to improve the durability and availability of critical data, but you can't use the Multi\-AZ secondary to serve read\-only queries\. Instead, you can create read replicas from high\-traffic Multi\-AZ DB instances to offload read\-only queries\. If the source instance of a Multi\-AZ deployment fails over to the secondary, any associated read replicas automatically switch to use the secondary \(now primary\) as their replication source\. For more information, see [High availability \(Multi\-AZ\) for Amazon RDS](Concepts.MultiAZ.md)\. 

You can create a read replica as a Multi\-AZ DB instance\. Amazon RDS creates a standby of your replica in another Availability Zone for failover support for the replica\. Creating your read replica as a Multi\-AZ DB instance is independent of whether the source database is a Multi\-AZ DB instance\. 

## Monitoring MariaDB read replicas<a name="USER_MariaDB.Replication.ReadReplicas.Monitor"></a>

For MariaDB read replicas, you can monitor replication lag in Amazon CloudWatch by viewing the Amazon RDS `ReplicaLag` metric\. The `ReplicaLag` metric reports the value of the `Seconds_Behind_Master` field of the `SHOW SLAVE STATUS` command\. 

Common causes for replication lag for MariaDB are the following: 
+ A network outage\.
+ Writing to tables with indexes on a read replica\. If the `read_only` parameter is not set to 0 on the read replica, it can break replication\.
+ Using a nontransactional storage engine such as MyISAM\. Replication is only supported for the InnoDB storage engine on MariaDB 10\.2 and later and the XtraDB storage engine on MariaDB 10\.1 and earlier\.

When the `ReplicaLag` metric reaches 0, the replica has caught up to the source DB instance\. If the `ReplicaLag` metric returns \-1, then replication is currently not active\. `ReplicaLag` = \-1 is equivalent to `Seconds_Behind_Master` = `NULL`\. 

## Starting and stopping replication with MariaDB read replicas<a name="USER_MariaDB.Replication.ReadReplicas.StartStop"></a>

You can stop and restart the replication process on an Amazon RDS DB instance by calling the system stored procedures [mysql\.rds\_stop\_replication](mysql_rds_stop_replication.md) and [mysql\.rds\_start\_replication](mysql_rds_start_replication.md)\. You can do this when replicating between two Amazon RDS instances for long\-running operations such as creating large indexes\. You also need to stop and start replication when importing or exporting databases\. For more information, see [Importing data to an Amazon RDS MySQL or MariaDB DB instance with reduced downtime](MySQL.Procedural.Importing.NonRDSRepl.md) and [Exporting data from a MySQL DB instance by using replication](MySQL.Procedural.Exporting.NonRDSRepl.md)\. 

If replication is stopped for more than 30 consecutive days, either manually or due to a replication error, Amazon RDS ends replication between the source DB instance and all read replicas\. It does so to prevent increased storage requirements on the source DB instance and long failover times\. The read replica DB instance is still available\. However, replication can't be resumed because the binary logs required by the read replica are deleted from the source DB instance after replication is ended\. You can create a new read replica for the source DB instance to reestablish replication\. 

## Troubleshooting a MariaDB read replica problem<a name="USER_ReadRepl.Troubleshooting.MariaDB"></a>

The replication technologies for MariaDB are asynchronous\. Because they are asynchronous, occasional `BinLogDiskUsage` increases on the source DB instance and `ReplicaLag` on the read replica are to be expected\. For example, a high volume of write operations to the source DB instance can occur in parallel\. In contrast, write operations to the read replica are serialized using a single I/O thread, which can lead to a lag between the source instance and read replica\. For more information about read\-only replicas in the MariaDB documentation, go to [Replication overview](http://mariadb.com/kb/en/mariadb/replication-overview/)\.

You can do several things to reduce the lag between updates to a source DB instance and the subsequent updates to the read replica, such as the following:
+ Sizing a read replica to have a storage size and DB instance class comparable to the source DB instance\.
+ Ensuring that parameter settings in the DB parameter groups used by the source DB instance and the read replica are compatible\. For more information and an example, see the discussion of the `max_allowed_packet` parameter later in this section\.

Amazon RDS monitors the replication status of your read replicas and updates the `Replication State` field of the read replica instance to `Error` if replication stops for any reason\. An example might be if DML queries run on your read replica conflict with the updates made on the source DB instance\. 

You can review the details of the associated error thrown by the MariaDB engine by viewing the `Replication Error` field\. Events that indicate the status of the read replica are also generated, including [RDS-EVENT-0045](USER_Events.md#RDS-EVENT-0045), [RDS-EVENT-0046](USER_Events.md#RDS-EVENT-0046), and [RDS-EVENT-0047](USER_Events.md#RDS-EVENT-0047)\. For more information about events and subscribing to events, see [Using Amazon RDS event notification](USER_Events.md)\. If a MariaDB error message is returned, review the error in the [MariaDB error message documentation](http://mariadb.com/kb/en/mariadb/mariadb-error-codes/)\.

One common issue that can cause replication errors is when the value for the `max_allowed_packet` parameter for a read replica is less than the `max_allowed_packet` parameter for the source DB instance\. The `max_allowed_packet` parameter is a custom parameter that you can set in a DB parameter group that is used to specify the maximum size of DML code that can be run on the database\. In some cases, the `max_allowed_packet` parameter value in the DB parameter group associated with a source DB instance is smaller than the `max_allowed_packet` parameter value in the DB parameter group associated with the source's read replica\. In these cases, the replication process can throw an error \(Packet bigger than 'max\_allowed\_packet' bytes\) and stop replication\. You can fix the error by having the source and read replica use DB parameter groups with the same `max_allowed_packet` parameter values\. 

Other common situations that can cause replication errors include the following:
+ Writing to tables on a read replica\. If you are creating indexes on a read replica, you need to have the `read_only` parameter set to **0** to create the indexes\. If you are writing to tables on the read replica, it might break replication\. 
+ Using a non\-transactional storage engine such as MyISAM\. read replicas require a transactional storage engine\. Replication is only supported for the InnoDB storage engine on MariaDB 10\.2 and later and the XtraDB storage engine on MariaDB 10\.1 and earlier\.
+ Using unsafe nondeterministic queries such as `SYSDATE()`\. For more information, see [Determination of safe and unsafe statements in binary logging](https://dev.mysql.com/doc/refman/8.0/en/replication-rbr-safe-unsafe.html)\. 

If you decide that you can safely skip an error, you can follow the steps described in the section [Skipping the current replication error](Appendix.MySQL.CommonDBATasks.md#Appendix.MySQL.CommonDBATasks.SkipError)\. Otherwise, you can delete the read replica and create an instance using the same DB instance identifier so that the endpoint remains the same as that of your old read replica\. If a replication error is fixed, the `Replication State` changes to *replicating*\.

For MariaDB DB instances, in some cases read replicas can't be switched to the secondary if some binlog events aren't flushed during the failure\. In these cases, you must manually delete and recreate the read replicas\. You can reduce the chance of this happening by setting the following parameter values: `sync_binlog=1` and `innodb_flush_log_at_trx_commit=1`\. These settings might reduce performance, so test their impact before implementing the changes in a production environment\.