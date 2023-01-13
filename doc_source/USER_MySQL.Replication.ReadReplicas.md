# Working with MySQL read replicas<a name="USER_MySQL.Replication.ReadReplicas"></a>

Following, you can find specific information about working with read replicas on RDS for MySQL\. For general information about read replicas and instructions for using them, see [Working with read replicas](USER_ReadRepl.md)\.

**Topics**
+ [Configuring read replicas with MySQL](#USER_MySQL.Replication.ReadReplicas.Configuration)
+ [Configuring replication filters with MySQL](#USER_MySQL.Replication.ReadReplicas.ReplicationFilters)
+ [Configuring delayed replication with MySQL](#USER_MySQL.Replication.ReadReplicas.DelayReplication)
+ [Updating read replicas with MySQL](#USER_MySQL.Replication.ReadReplicas.Updates)
+ [Working with Multi\-AZ read replica deployments with MySQL](#USER_MySQL.Replication.ReadReplicas.MultiAZ)
+ [Using cascading read replicas with RDS for MySQL](#USER_MySQL.Replication.ReadReplicas.Cascading)
+ [Monitoring MySQL read replicas](#USER_MySQL.Replication.ReadReplicas.Monitor)
+ [Starting and stopping replication with MySQL read replicas](#USER_MySQL.Replication.ReadReplicas.StartStop)
+ [Troubleshooting a MySQL read replica problem](#USER_ReadRepl.Troubleshooting)

## Configuring read replicas with MySQL<a name="USER_MySQL.Replication.ReadReplicas.Configuration"></a>

Before a MySQL DB instance can serve as a replication source, make sure to enable automatic backups on the source DB instance\. To do this, set the backup retention period to a value other than 0\. This requirement also applies to a read replica that is the source DB instance for another read replica\. Automatic backups are supported for read replicas running any version of MySQL\. You can configure replication based on binary log coordinates for a MySQL DB instance\. 

On RDS for MySQL version 5\.7\.23 and higher MySQL 5\.7 versions and RDS for MySQL 8\.0\.26 and higher 8\.0 versions, you can configure replication using global transaction identifiers \(GTIDs\)\. For more information, see [Using GTID\-based replication for Amazon RDS for MySQL](mysql-replication-gtid.md)\.

You can create up to 15 read replicas from one DB instance within the same Region\. For replication to operate effectively, each read replica should have the same amount of compute and storage resources as the source DB instance\. If you scale the source DB instance, also scale the read replicas\. 

RDS for MySQL supports cascading read replicas\. To learn how to configure cascading read replicas, see [Using cascading read replicas with RDS for MySQL](#USER_MySQL.Replication.ReadReplicas.Cascading)\.

You can run multiple read replica create and delete actions at the same time that reference the same source DB instance\. When you perform these actions, stay within the limit of 15 read replicas for each source instance\.

A read replica of a MySQL DB instance can't use a lower DB engine version than its source DB instance\.

### Preparing MySQL DB instances that use MyISAM<a name="USER_MySQL.Replication.ReadReplicas.Configuration-MyISAM-Instances"></a>

If your MySQL DB instance uses a nontransactional engine such as MyISAM, you need to perform the following steps to successfully set up your read replica\. These steps are required to make sure that the read replica has a consistent copy of your data\. These steps are not required if all of your tables use a transactional engine such as InnoDB\. 

1. Stop all data manipulation language \(DML\) and data definition language \(DDL\) operations on non\-transactional tables in the source DB instance and wait for them to complete\. SELECT statements can continue running\. 

1. Flush and lock the tables in the source DB instance\.

1. Create the read replica using one of the methods in the following sections\.

1. Check the progress of the read replica creation using, for example, the `DescribeDBInstances` API operation\. Once the read replica is available, unlock the tables of the source DB instance and resume normal database operations\. 

## Configuring replication filters with MySQL<a name="USER_MySQL.Replication.ReadReplicas.ReplicationFilters"></a>

You can use replication filters to specify which databases and tables are replicated with a read replica\. Replication filters can include databases and tables in replication or exclude them from replication\.

The following are some use cases for replication filters:
+ To reduce the size of a read replica\. With replication filtering, you can exclude the databases and tables that aren't needed on the read replica\.
+ To exclude databases and tables from read replicas for security reasons\.
+ To replicate different databases and tables for specific use cases at different read replicas\. For example, you might use specific read replicas for analytics or sharding\.
+ For a DB instance that has read replicas in different AWS Regions, to replicate different databases or tables in different AWS Regions\.

**Note**  
You can also use replication filters to specify which databases and tables are replicated with a primary MySQL DB instance that is configured as a replica in an inbound replication topology\. For more information about this configuration, see [Configuring binary log file position replication with an external source instance](MySQL.Procedural.Importing.External.Repl.md)\.

**Topics**
+ [Setting replication filtering parameters for RDS for MySQL](#USER_MySQL.Replication.ReadReplicas.ReplicationFilters.Configuring)
+ [Replication filtering limitations for RDS for MySQL](#USER_MySQL.Replication.ReadReplicas.ReplicationFilters.Limitations)
+ [Replication filtering examples for RDS for MySQL](#USER_MySQL.Replication.ReadReplicas.ReplicationFilters.Examples)
+ [Viewing the replication filters for a read replica](#USER_MySQL.Replication.ReadReplicas.ReplicationFilters.Viewing)

### Setting replication filtering parameters for RDS for MySQL<a name="USER_MySQL.Replication.ReadReplicas.ReplicationFilters.Configuring"></a>

To configure replication filters, set the following replication filtering parameters on the read replica:
+ `replicate-do-db` – Replicate changes to the specified databases\. When you set this parameter for a read replica, only the databases specified in the parameter are replicated\.
+ `replicate-ignore-db` – Don't replicate changes to the specified databases\. When the `replicate-do-db` parameter is set for a read replica, this parameter isn't evaluated\.
+ `replicate-do-table` – Replicate changes to the specified tables\. When you set this parameter for a read replica, only the tables specified in the parameter are replicated\. Also, when the `replicate-do-db` or `replicate-ignore-db` parameter is set, make sure to include the database that includes the specified tables in replication with the read replica\.
+ `replicate-ignore-table` – Don't replicate changes to the specified tables\. When the `replicate-do-table` parameter is set for a read replica, this parameter isn't evaluated\.
+ `replicate-wild-do-table` – Replicate tables based on the specified database and table name patterns\. The `%` and `_` wildcard characters are supported\. When the `replicate-do-db` or `replicate-ignore-db` parameter is set, make sure to include the database that includes the specified tables in replication with the read replica\.
+ `replicate-wild-ignore-table` – Don't replicate tables based on the specified database and table name patterns\. The `%` and `_` wildcard characters are supported\. When the `replicate-do-table` or `replicate-wild-do-table` parameter is set for a read replica, this parameter isn't evaluated\.

The parameters are evaluated in the order that they are listed\. For more information about how these parameters work, see the MySQL documentation:
+ For general information, see [ Replica Server Options and Variables](https://dev.mysql.com/doc/refman/8.0/en/replication-options-replica.html)\.
+ For information about how database replication filtering parameters are evaluated, see [ Evaluation of Database\-Level Replication and Binary Logging Options](https://dev.mysql.com/doc/refman/8.0/en/replication-rules-db-options.html)\.
+ For information about how table replication filtering parameters are evaluated, see [ Evaluation of Table\-Level Replication Options](https://dev.mysql.com/doc/refman/8.0/en/replication-rules-table-options.html)\.

By default, each of these parameters has an empty value\. On each read replica, you can use these parameters to set, change, and delete replication filters\. When you set one of these parameters, separate each filter from others with a comma\.

You can use the `%` and `_` wildcard characters in the `replicate-wild-do-table` and `replicate-wild-ignore-table` parameters\. The `%` wildcard matches any number of characters, and the `_` wildcard matches only one character\. 

The binary logging format of the source DB instance is important for replication because it determines the record of data changes\. The setting of the `binlog_format` parameter determines whether the replication is row\-based or statement\-based\. For more information, see [Configuring MySQL binary logging](USER_LogAccess.MySQL.BinaryFormat.md)\.

**Note**  
All data definition language \(DDL\) statements are replicated as statements, regardless of the `binlog_format` setting on the source DB instance\. 

### Replication filtering limitations for RDS for MySQL<a name="USER_MySQL.Replication.ReadReplicas.ReplicationFilters.Limitations"></a>

The following limitations apply to replication filtering for RDS for MySQL:
+ Each replication filtering parameter has a 2,000\-character limit\.
+ Commas aren't supported in replication filters\.
+ The MySQL `--binlog-do-db` and `--binlog-ignore-db` options for binary log filtering aren't supported\.
+ Replication filtering doesn't support XA transactions\.

  For more information, see [ Restrictions on XA Transactions](https://dev.mysql.com/doc/refman/8.0/en/xa-restrictions.html) in the MySQL documentation\.

### Replication filtering examples for RDS for MySQL<a name="USER_MySQL.Replication.ReadReplicas.ReplicationFilters.Examples"></a>

To configure replication filtering for a read replica, modify the replication filtering parameters in the parameter group associated with the read replica\.

**Note**  
You can't modify a default parameter group\. If the read replica is using a default parameter group, create a new parameter group and associate it with the read replica\. For more information on DB parameter groups, see [Working with parameter groups](USER_WorkingWithParamGroups.md)\.

You can set parameters in a parameter group using the AWS Management Console, AWS CLI, or RDS API\. For information about setting parameters, see [Modifying parameters in a DB parameter group](USER_WorkingWithDBInstanceParamGroups.md#USER_WorkingWithParamGroups.Modifying)\. When you set parameters in a parameter group, all of the DB instances associated with the parameter group use the parameter settings\. If you set the replication filtering parameters in a parameter group, make sure that the parameter group is associated only with read replicas\. Leave the replication filtering parameters empty for source DB instances\.

The following examples set the parameters using the AWS CLI\. These examples set `ApplyMethod` to `immediate` so that the parameter changes occur immediately after the CLI command completes\. If you want a pending change to be applied after the read replica is rebooted, set `ApplyMethod` to `pending-reboot`\. 

The following examples set replication filters:
+ [Including databases in replication](#rep-filter-in-dbs-mysql)
+ [Including tables in replication](#rep-filter-in-tables-mysql)
+ [Including tables in replication with wildcard characters](#rep-filter-in-tables-wildcards-mysql)
+ [Escaping wildcard characters in names](#rep-filter-escape-wildcards-mysql)
+ [Excluding databases from replication](#rep-filter-ex-dbs-mysql)
+ [Excluding tables from replication](#rep-filter-ex-tables-mysql)
+ [Excluding tables from replication using wildcard characters](#rep-filter-ex-tables-wildcards-mysql)<a name="rep-filter-in-dbs-mysql"></a>

**Example Including databases in replication**  
The following example includes the `mydb1` and `mydb2` databases in replication\.  
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
```<a name="rep-filter-in-tables-mysql"></a>

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
```<a name="rep-filter-in-tables-wildcards-mysql"></a>

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
```<a name="rep-filter-escape-wildcards-mysql"></a>

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
```<a name="rep-filter-ex-dbs-mysql"></a>

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
```<a name="rep-filter-ex-tables-mysql"></a>

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
```<a name="rep-filter-ex-tables-wildcards-mysql"></a>

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

### Viewing the replication filters for a read replica<a name="USER_MySQL.Replication.ReadReplicas.ReplicationFilters.Viewing"></a>

You can view the replication filters for a read replica in the following ways:
+ Check the settings of the replication filtering parameters in the parameter group associated with the read replica\.

  For instructions, see [Viewing parameter values for a DB parameter group](USER_WorkingWithDBInstanceParamGroups.md#USER_WorkingWithParamGroups.Viewing)\.
+ In a MySQL client, connect to the read replica and run the `SHOW REPLICA STATUS` statement\.

  In the output, the following fields show the replication filters for the read replica:
  + `Replicate_Do_DB`
  + `Replicate_Ignore_DB`
  + `Replicate_Do_Table`
  + `Replicate_Ignore_Table`
  + `Replicate_Wild_Do_Table`
  + `Replicate_Wild_Ignore_Table`

  For more information about these fields, see [Checking Replication Status](https://dev.mysql.com/doc/refman/8.0/en/replication-administration-status.html) in the MySQL documentation\.
**Note**  
Previous versions of MySQL used `SHOW SLAVE STATUS` instead of `SHOW REPLICA STATUS`\. If you are using a MySQL version before 8\.0\.23, then use `SHOW SLAVE STATUS`\. 

## Configuring delayed replication with MySQL<a name="USER_MySQL.Replication.ReadReplicas.DelayReplication"></a>

You can use delayed replication as a strategy for disaster recovery\. With delayed replication, you specify the minimum amount of time, in seconds, to delay replication from the source to the read replica\. In the event of a disaster, such as a table deleted unintentionally, you complete the following steps to recover from the disaster quickly:
+ Stop replication to the read replica before the change that caused the disaster is sent to it\.

  Use the [mysql\.rds\_stop\_replication](mysql_rds_stop_replication.md) stored procedure to stop replication\.
+ Start replication and specify that replication stops automatically at a log file location\.

  You specify a location just before the disaster using the [mysql\.rds\_start\_replication\_until](mysql_rds_start_replication_until.md) stored procedure\.
+ Promote the read replica to be the new source DB instance by using the instructions in [Promoting a read replica to be a standalone DB instance](USER_ReadRepl.md#USER_ReadRepl.Promote)\.

**Note**  
On RDS for MySQL 8\.0, delayed replication is supported for MySQL 8\.0\.26 and higher\. On RDS for MySQL 5\.7, delayed replication is supported for MySQL 5\.7\.22 and higher\.
Use stored procedures to configure delayed replication\. You can't configure delayed replication with the AWS Management Console, the AWS CLI, or the Amazon RDS API\.
On RDS for MySQL 5\.7\.23 and higher MySQL 5\.7 versions and RDS for MySQL 8\.0\.26 and higher 8\.0 versions, you can use replication based on global transaction identifiers \(GTIDs\) in a delayed replication configuration\. If you use GTID\-based replication, use the [mysql\.rds\_start\_replication\_until\_gtid](mysql_rds_start_replication_until_gtid.md) stored procedure instead of the [mysql\.rds\_start\_replication\_until](mysql_rds_start_replication_until.md) stored procedure\. For more information about GTID\-based replication, see [Using GTID\-based replication for Amazon RDS for MySQL](mysql-replication-gtid.md)\.

**Topics**
+ [Configuring delayed replication during read replica creation](#USER_MySQL.Replication.ReadReplicas.DelayReplication.ReplicaCreation)
+ [Modifying delayed replication for an existing read replica](#USER_MySQL.Replication.ReadReplicas.DelayReplication.ExistingReplica)
+ [Setting a location to stop replication to a read replica](#USER_MySQL.Replication.ReadReplicas.DelayReplication.StartUntil)
+ [Promoting a read replica](#USER_MySQL.Replication.ReadReplicas.DelayReplication.Promote)

### Configuring delayed replication during read replica creation<a name="USER_MySQL.Replication.ReadReplicas.DelayReplication.ReplicaCreation"></a>

To configure delayed replication for any future read replica created from a DB instance, run the [mysql\.rds\_set\_configuration](mysql_rds_set_configuration.md) stored procedure with the `target delay` parameter\.

**To configure delayed replication during read replica creation**

1. Using a MySQL client, connect to the MySQL DB instance to be the source for read replicas as the master user\.

1. Run the [mysql\.rds\_set\_configuration](mysql_rds_set_configuration.md) stored procedure with the `target delay` parameter\.

   For example, run the following stored procedure to specify that replication is delayed by at least one hour \(3,600 seconds\) for any read replica created from the current DB instance\.

   ```
   call mysql.rds_set_configuration('target delay', 3600);
   ```
**Note**  
After running this stored procedure, any read replica you create using the AWS CLI or Amazon RDS API is configured with replication delayed by the specified number of seconds\.

### Modifying delayed replication for an existing read replica<a name="USER_MySQL.Replication.ReadReplicas.DelayReplication.ExistingReplica"></a>

To modify delayed replication for an existing read replica, run the [mysql\.rds\_set\_source\_delay](mysql_rds_set_source_delay.md) stored procedure\.

**To modify delayed replication for an existing read replica**

1. Using a MySQL client, connect to the read replica as the master user\.

1. Use the [mysql\.rds\_stop\_replication](mysql_rds_stop_replication.md) stored procedure to stop replication\.

1. Run the [mysql\.rds\_set\_source\_delay](mysql_rds_set_source_delay.md) stored procedure\.

   For example, run the following stored procedure to specify that replication to the read replica is delayed by at least one hour \(3600 seconds\)\.

   ```
   call mysql.rds_set_source_delay(3600);
   ```

1. Use the [mysql\.rds\_start\_replication](mysql_rds_start_replication.md) stored procedure to start replication\.

### Setting a location to stop replication to a read replica<a name="USER_MySQL.Replication.ReadReplicas.DelayReplication.StartUntil"></a>

After stopping replication to the read replica, you can start replication and then stop it at a specified binary log file location using the [mysql\.rds\_start\_replication\_until](mysql_rds_start_replication_until.md) stored procedure\.

**To start replication to a read replica and stop replication at a specific location**

1. Using a MySQL client, connect to the source MySQL DB instance as the master user\.

1. Run the [mysql\.rds\_start\_replication\_until](mysql_rds_start_replication_until.md) stored procedure\.

   The following example initiates replication and replicates changes until it reaches location `120` in the `mysql-bin-changelog.000777` binary log file\. In a disaster recovery scenario, assume that location `120` is just before the disaster\.

   ```
   call mysql.rds_start_replication_until(
     'mysql-bin-changelog.000777',
     120);
   ```

Replication stops automatically when the stop point is reached\. The following RDS event is generated: `Replication has been stopped since the replica reached the stop point specified by the rds_start_replication_until stored procedure`\.

### Promoting a read replica<a name="USER_MySQL.Replication.ReadReplicas.DelayReplication.Promote"></a>

After replication is stopped, in a disaster recovery scenario, you can promote a read replica to be the new source DB instance\. For information about promoting a read replica, see [Promoting a read replica to be a standalone DB instance](USER_ReadRepl.md#USER_ReadRepl.Promote)\.

## Updating read replicas with MySQL<a name="USER_MySQL.Replication.ReadReplicas.Updates"></a>

Read replicas are designed to support read queries, but you might need occasional updates\. For example, you might need to add an index to optimize the specific types of queries accessing the replica\. You can enable updates by setting the `read_only` parameter to `0` in the DB parameter group for the read replica\. Be careful when disabling read\-only on a read replica because it can cause problems if the read replica becomes incompatible with the source DB instance\. Change the value of the `read_only` parameter back to `1` as soon as possible\. 

## Working with Multi\-AZ read replica deployments with MySQL<a name="USER_MySQL.Replication.ReadReplicas.MultiAZ"></a>

You can create a read replica from either single\-AZ or Multi\-AZ DB instance deployments\. You use Multi\-AZ deployments to improve the durability and availability of critical data, but you can't use the Multi\-AZ secondary to serve read\-only queries\. Instead, you can create read replicas from high\-traffic Multi\-AZ DB instances to offload read\-only queries\. If the source instance of a Multi\-AZ deployment fails over to the secondary, any associated read replicas automatically switch to use the secondary \(now primary\) as their replication source\. For more information, see [Multi\-AZ deployments for high availability](Concepts.MultiAZ.md)\. 

You can create a read replica as a Multi\-AZ DB instance\. Amazon RDS creates a standby of your replica in another Availability Zone for failover support for the replica\. Creating your read replica as a Multi\-AZ DB instance is independent of whether the source database is a Multi\-AZ DB instance\. 

## Using cascading read replicas with RDS for MySQL<a name="USER_MySQL.Replication.ReadReplicas.Cascading"></a>

RDS for MySQL supports cascading read replicas\. With *cascading read replicas*, you can scale reads without adding overhead to your source RDS for MySQL DB instance\.

With cascading read replicas, your RDS for MySQL DB instance sends data to the first read replica in the chain\. That read replica then sends data to the second replica in the chain, and so on\. The end result is that all read replicas in the chain have the changes from the RDS for MySQL DB instance, but without the overhead solely on the source DB instance\.

You can create a series of up to three read replicas in a chain from a source RDS for MySQL DB instance\. For example, suppose that you have an RDS for MySQL DB instance, `mysql-main`\. You can do the following:
+ Starting with `mysql-main`, create the first read replica in the chain, `read-replica-1`\.
+ Next, from `read-replica-1`, create the next read replica in the chain, `read-replica-2`\.
+ Finally, from `read-replica-2`, create the third read replica in the chain, `read-replica-3`\.

You can't create another read replica beyond this third cascading read replica in the series for `mysql-main`\. A complete series of instances from an RDS for MySQL source DB instance through to the end of a series of cascading read replicas can consist of at most four DB instances\.

For cascading read replicas to work, each source RDS for MySQL DB instance must have automated backups turned on\. To turn on automatic backups on a read replica, first create the read replica, and then modify the read replica to turn on automatic backups\. For more information, see [Creating a read replica](USER_ReadRepl.md#USER_ReadRepl.Create)\.

As with any read replica, you can promote a read replica that's part of a cascade\. Promoting a read replica from within a chain of read replicas removes that replica from the chain\. For example, suppose that you want to move some of the workload from your `mysql-main` DB instance to a new instance for use by the accounting department only\. Assuming the chain of three read replicas from the example, you decide to promote `read-replica-2`\. The chain is affected as follows:
+ Promoting `read-replica-2` removes it from the replication chain\.
  + It is now a full read/write DB instance\.
  + It continues replicating to `read-replica-3`, just as it was doing before promotion\.
+ Your `mysql-main` continues replicating to `read-replica-1`\.

For more information about promoting read replicas, see [Promoting a read replica to be a standalone DB instance](USER_ReadRepl.md#USER_ReadRepl.Promote)\.

## Monitoring MySQL read replicas<a name="USER_MySQL.Replication.ReadReplicas.Monitor"></a>

For MySQL read replicas, you can monitor replication lag in Amazon CloudWatch by viewing the Amazon RDS `ReplicaLag` metric\. The `ReplicaLag` metric reports the value of the `Seconds_Behind_Master` field of the `SHOW REPLICA STATUS` command\. 

**Note**  
Previous versions of MySQL used `SHOW SLAVE STATUS` instead of `SHOW REPLICA STATUS`\. If you are using a MySQL version before 8\.0\.23, then use `SHOW SLAVE STATUS`\. 

Common causes for replication lag for MySQL are the following: 
+ A network outage\.
+ Writing to tables that have different indexes on a read replica\. If the `read_only` parameter is set to `0` on the read replica, replication can break if the read replica becomes incompatible with the source DB instance\. After you've performed maintenance tasks on the read replica, we recommend that you set the `read_only` parameter back to `1`\.
+ Using a nontransactional storage engine such as MyISAM\. Replication is only supported for the InnoDB storage engine on MySQL\.

When the `ReplicaLag` metric reaches 0, the replica has caught up to the source DB instance\. If the `ReplicaLag` metric returns \-1, then replication is currently not active\. `ReplicaLag` = \-1 is equivalent to `Seconds_Behind_Master` = `NULL`\. 

## Starting and stopping replication with MySQL read replicas<a name="USER_MySQL.Replication.ReadReplicas.StartStop"></a>

You can stop and restart the replication process on an Amazon RDS DB instance by calling the system stored procedures [mysql\.rds\_stop\_replication](mysql_rds_stop_replication.md) and [mysql\.rds\_start\_replication](mysql_rds_start_replication.md)\. You can do this when replicating between two Amazon RDS instances for long\-running operations such as creating large indexes\. You also need to stop and start replication when importing or exporting databases\. For more information, see [Importing data to an Amazon RDS MariaDB or MySQL database with reduced downtime](MySQL.Procedural.Importing.NonRDSRepl.md) and [Exporting data from a MySQL DB instance by using replication](MySQL.Procedural.Exporting.NonRDSRepl.md)\. 

If replication is stopped for more than 30 consecutive days, either manually or due to a replication error, Amazon RDS terminates replication between the source DB instance and all read replicas\. It does so to prevent increased storage requirements on the source DB instance and long failover times\. The read replica DB instance is still available\. However, replication can't be resumed because the binary logs required by the read replica are deleted from the source DB instance after replication is terminated\. You can create a new read replica for the source DB instance to reestablish replication\. 

## Troubleshooting a MySQL read replica problem<a name="USER_ReadRepl.Troubleshooting"></a>

For MySQL DB instances, in some cases read replicas present replication errors or data inconsistencies \(or both\) between the read replica and its source DB instance\. This problem occurs when some binary log \(binlog\) events or InnoDB redo logs aren't flushed during a failure of the read replica or the source DB instance\. In these cases, manually delete and recreate the read replicas\. You can reduce the chance of this happening by setting the following parameter values: `sync_binlog=1` and `innodb_flush_log_at_trx_commit=1`\. These settings might reduce performance, so test their impact before implementing the changes in a production environment\.

**Warning**  
In the parameter group associated with the source DB instance, we recommend keeping these parameters values: `sync_binlog=1` and `innodb_flush_log_at_trx_commit=1`\. These parameters are dynamic\. If you don't want to use these settings, we recommend temporarily setting those values before executing any operation on the source DB instance that might cause it to restart\. These operations include, but are not limited to, rebooting, rebooting with failover, upgrading the database version, and changing the DB instance class or its storage\. The same recommendation applies to creating new read replicas for the source DB instance\.  
Failure to follow this guidance increases the risk of read replicas presenting replication errors or data inconsistencies \(or both\) between the read replica and its source DB instance\.

The replication technologies for MySQL are asynchronous\. Because they are asynchronous, occasional `BinLogDiskUsage` increases on the source DB instance and `ReplicaLag` on the read replica are to be expected\. For example, a high volume of write operations to the source DB instance can occur in parallel\. In contrast, write operations to the read replica are serialized using a single I/O thread, which can lead to a lag between the source instance and read replica\. For more information about read\-only replicas in the MySQL documentation, see [Replication implementation details](https://dev.mysql.com/doc/refman/8.0/en/replication-implementation-details.html)\.

You can do several things to reduce the lag between updates to a source DB instance and the subsequent updates to the read replica, such as the following:
+ Sizing a read replica to have a storage size and DB instance class comparable to the source DB instance\.
+ Ensuring that parameter settings in the DB parameter groups used by the source DB instance and the read replica are compatible\. For more information and an example, see the discussion of the `max_allowed_packet` parameter later in this section\.

Amazon RDS monitors the replication status of your read replicas and updates the `Replication State` field of the read replica instance to `Error` if replication stops for any reason\. An example might be if DML queries run on your read replica conflict with the updates made on the source DB instance\. 

You can review the details of the associated error thrown by the MySQL engine by viewing the `Replication Error` field\. Events that indicate the status of the read replica are also generated, including [RDS-EVENT-0045](USER_Events.Messages.md#RDS-EVENT-0045), [RDS-EVENT-0046](USER_Events.Messages.md#RDS-EVENT-0046), and [RDS-EVENT-0047](USER_Events.Messages.md#RDS-EVENT-0047)\. For more information about events and subscribing to events, see [Working with Amazon RDS event notification](USER_Events.md)\. If a MySQL error message is returned, review the error number in the [MySQL error message documentation](https://dev.mysql.com/doc/mysql-errors/8.0/en/server-error-reference.html)\.

One common issue that can cause replication errors is when the value for the `max_allowed_packet` parameter for a read replica is less than the `max_allowed_packet` parameter for the source DB instance\. The `max_allowed_packet` parameter is a custom parameter that you can set in a DB parameter group\. You use `max_allowed_packet` to specify the maximum size of DML code that can be run on the database\. In some cases, the `max_allowed_packet` value in the DB parameter group associated with a read replica is smaller than the `max_allowed_packet` value in the DB parameter group associated with the source DB instance\. In these cases, the replication process can throw the error `Packet bigger than 'max_allowed_packet' bytes` and stop replication\. To fix the error, have the source DB instance and read replica use DB parameter groups with the same `max_allowed_packet` parameter values\. 

Other common situations that can cause replication errors include the following:
+ Writing to tables on a read replica\. In some cases, you might create indexes on a read replica that are different from the indexes on the source DB instance\. If you do, set the `read_only` parameter to `0` to create the indexes\. If you write to tables on the read replica, it might break replication if the read replica becomes incompatible with the source DB instance\. After you perform maintenance tasks on the read replica, we recommend that you set the `read_only` parameter back to `1`\.
+  Using a non\-transactional storage engine such as MyISAM\. Read replicas require a transactional storage engine\. Replication is only supported for the InnoDB storage engine on MySQL\.
+  Using unsafe nondeterministic queries such as `SYSDATE()`\. For more information, see [ Determination of safe and unsafe statements in binary logging](https://dev.mysql.com/doc/refman/8.0/en/replication-rbr-safe-unsafe.html)\. 

If you decide that you can safely skip an error, you can follow the steps described in the section [Skipping the current replication error](Appendix.MySQL.CommonDBATasks.md#Appendix.MySQL.CommonDBATasks.SkipError)\. Otherwise, you can first delete the read replica\. Then you create an instance using the same DB instance identifier so that the endpoint remains the same as that of your old read replica\. If a replication error is fixed, the `Replication State` changes to *replicating*\.