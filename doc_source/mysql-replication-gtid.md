# Using GTID\-based replication for Amazon RDS for MySQL<a name="mysql-replication-gtid"></a>

Following, you can learn how to use global transaction identifiers \(GTIDs\) with binary log \(binlog\) replication among Amazon RDS for MySQL DB instances\. 

If you use binlog replication and aren't familiar with GTID\-based replication with MySQL, see [Replication with global transaction identifiers](https://dev.mysql.com/doc/refman/5.7/en/replication-gtids.html) in the MySQL documentation for background\.

GTID\-based replication is supported for all RDS for MySQL 5\.7 versions, and RDS for MySQL version 8\.0\.26 and higher MySQL 8\.0 versions\. All MySQL DB instances in a replication configuration must meet this requirement\.

**Topics**
+ [Overview of global transaction identifiers \(GTIDs\)](#mysql-replication-gtid.overview)
+ [Parameters for GTID\-based replication](#mysql-replication-gtid.parameters)
+ [Configuring GTID\-based replication for new read replicas](#mysql-replication-gtid.configuring-new-read-replicas)
+ [Configuring GTID\-based replication for existing read replicas](#mysql-replication-gtid.configuring-existing-read-replicas)
+ [Disabling GTID\-based replication for a MySQL DB instance with read replicas](#mysql-replication-gtid.disabling)

**Note**  
For information about configuring GTID\-based replication with an external database, see [Configuring GTID\-based replication with an external source instance](MySQL.Procedural.Importing.External.Repl.GTIDProcedure.md)\.

## Overview of global transaction identifiers \(GTIDs\)<a name="mysql-replication-gtid.overview"></a>

*Global transaction identifiers \(GTIDs\)* are unique identifiers generated for committed MySQL transactions\. You can use GTIDs to make binlog replication simpler and easier to troubleshoot\.

MySQL uses two different types of transactions for binlog replication:
+ *GTID transactions* – Transactions that are identified by a GTID\.
+ *Anonymous transactions* – Transactions that don't have a GTID assigned\.

In a replication configuration, GTIDs are unique across all DB instances\. GTIDs simplify replication configuration because when you use them, you don't have to refer to log file positions\. GTIDs also make it easier to track replicated transactions and determine whether the source instance and replicas are consistent\.

You can use GTID\-based replication to replicate data with RDS for MySQL read replicas\. You can configure GTID\-based replication when you are creating new read replicas, or you can convert existing read replicas to use GTID\-based replication\.

You can also use GTID\-based replication in a delayed replication configuration with RDS for MySQL\. For more information, see [Configuring delayed replication with MySQL](USER_MySQL.Replication.ReadReplicas.md#USER_MySQL.Replication.ReadReplicas.DelayReplication)\.

## Parameters for GTID\-based replication<a name="mysql-replication-gtid.parameters"></a>

Use the following parameters to configure GTID\-based replication\.


****  

| Parameter | Valid values | Description | 
| --- | --- | --- | 
|  `gtid_mode`  |  `OFF`, `OFF_PERMISSIVE`, `ON_PERMISSIVE`, `ON`  |  `OFF` specifies that new transactions are anonymous transactions \(that is, don't have GTIDs\), and a transaction must be anonymous to be replicated\.  `OFF_PERMISSIVE` specifies that new transactions are anonymous transactions, but all transactions can be replicated\.  `ON_PERMISSIVE` specifies that new transactions are GTID transactions, but all transactions can be replicated\.  `ON` specifies that new transactions are GTID transactions, and a transaction must be a GTID transaction to be replicated\.   | 
|  `enforce_gtid_consistency`  |  `OFF`, `ON`, `WARN`  |  `OFF` allows transactions to violate GTID consistency\.  `ON` prevents transactions from violating GTID consistency\.  `WARN` allows transactions to violate GTID consistency but generates a warning when a violation occurs\.   | 

**Note**  
In the AWS Management Console, the `gtid_mode` parameter appears as `gtid-mode`\.

For GTID\-based replication, use these settings for the parameter group for your DB instance or read replica:
+ `ON` and `ON_PERMISSIVE` apply only to outgoing replication from an RDS DB instance\. Both of these values cause your RDS DB instance to use GTIDs for transactions that are replicated\. `ON` requires that the target database also use GTID\-based replication\. `ON_PERMISSIVE` makes GTID\-based replication optional on the target database\. 
+ `OFF_PERMISSIVE`, if set, means that your RDS DB instances can accept incoming replication from a source database\. They can do this regardless of whether the source database uses GTID\-based replication\.
+ `OFF`, if set, means that your RDS DB instance only accepts incoming replication from source databases that don't use GTID\-based replication\. 

For more information about parameter groups, see [Working with parameter groups](USER_WorkingWithParamGroups.md)\.

## Configuring GTID\-based replication for new read replicas<a name="mysql-replication-gtid.configuring-new-read-replicas"></a>

When GTID\-based replication is enabled for an RDS for MySQL DB instance, GTID\-based replication is configured automatically for read replicas of the DB instance\.

**To enable GTID\-based replication for new read replicas**

1. Make sure that the parameter group associated with the DB instance has the following parameter settings:
   + `gtid_mode` – `ON` or `ON_PERMISSIVE`
   + `enforce_gtid_consistency` – `ON`

   For more information about setting configuration parameters using parameter groups, see [Working with parameter groups](USER_WorkingWithParamGroups.md)\.

1. If you changed the parameter group of the DB instance, reboot the DB instance\. For more information on how to do so, see [Rebooting a DB instance](USER_RebootInstance.md)\.

1.  Create one or more read replicas of the DB instance\. For more information on how to do so, see [Creating a read replica](USER_ReadRepl.md#USER_ReadRepl.Create)\. 

Amazon RDS attempts to establish GTID\-based replication between the MySQL DB instance and the read replicas using the `MASTER_AUTO_POSITION`\. If the attempt fails, Amazon RDS uses log file positions for replication with the read replicas\. For more information about the `MASTER_AUTO_POSITION`, see [ GTID auto\-positioning](https://dev.mysql.com/doc/refman/5.7/en/replication-gtids-auto-positioning.html) in the MySQL documentation\.

## Configuring GTID\-based replication for existing read replicas<a name="mysql-replication-gtid.configuring-existing-read-replicas"></a>

For an existing MySQL DB instance with read replicas that doesn't use GTID\-based replication, you can configure GTID\-based replication between the DB instance and the read replicas\.

**To enable GTID\-based replication for existing read replicas**

1. If the DB instance or any read replica is using an 8\.0 version of RDS for MySQL version lower than 8\.0\.26, upgrade the DB instance or read replica to 8\.0\.26 or a higher MySQL 8\.0 version\. All RDS for MySQL 5\.7 versions support GTID\-based replication\.

   For more information, see [Upgrading the MySQL DB engine](USER_UpgradeDBInstance.MySQL.md)\.

1. \(Optional\) Reset the GTID parameters and test the behavior of the DB instance and read replicas:

   1. Make sure that the parameter group associated with the DB instance and each read replica has the `enforce_gtid_consistency` parameter set to `WARN`\.

      For more information about setting configuration parameters using parameter groups, see [Working with parameter groups](USER_WorkingWithParamGroups.md)\.

   1. If you changed the parameter group of the DB instance, reboot the DB instance\. If you changed the parameter group for a read replica, reboot the read replica\.

      For more information, see [Rebooting a DB instance](USER_RebootInstance.md)\.

   1. Run your DB instance and read replicas with your normal workload and monitor the log files\.

      If you see warnings about GTID\-incompatible transactions, adjust your application so that it only uses GTID\-compatible features\. Make sure that the DB instance is not generating any warnings about GTID\-incompatible transactions before proceeding to the next step\.

1. Reset the GTID parameters for GTID\-based replication that allows anonymous transactions until the read replicas have processed all of them\.

   1. Make sure that the parameter group associated with the DB instance and each read replica has the following parameter settings:
      + `gtid_mode` – `ON_PERMISSIVE`
      + `enforce_gtid_consistency` – `ON`

   1. If you changed the parameter group of the DB instance, reboot the DB instance\. If you changed the parameter group for a read replica, reboot the read replica\.

1. Wait for all of your anonymous transactions to be replicated\. To check that these are replicated, do the following:

   1. Run the following statement on your source DB instance\.

      ```
      SHOW MASTER STATUS;
      ```

      Note the values in the `File` and `Position` columns\.

   1. On each read replica, use the file and position information from its source instance in the previous step to run the following query\.

      ```
      SELECT MASTER_POS_WAIT('file', position);
      ```

      For example, if the file name is `mysql-bin-changelog.000031` and the position is `107`, run the following statement\.

      ```
      SELECT MASTER_POS_WAIT('mysql-bin-changelog.000031', 107);
      ```

      If the read replica is past the specified position, the query returns immediately\. Otherwise, the function waits\. Proceed to the next step when the query returns for all read replicas\.

1. Reset the GTID parameters for GTID\-based replication only\.

   1. Make sure that the parameter group associated with the DB instance and each read replica has the following parameter settings:
      + `gtid_mode` – `ON`
      + `enforce_gtid_consistency` – `ON`

   1. Reboot the DB instance and each read replica\.

1. On each read replica, run the following procedure\.

   ```
   CALL mysql.rds_set_master_auto_position(1);
   ```

## Disabling GTID\-based replication for a MySQL DB instance with read replicas<a name="mysql-replication-gtid.disabling"></a>

You can disable GTID\-based replication for a MySQL DB instance with read replicas\. 

**To disable GTID\-based replication for a MySQL DB instance with read replicas**

1. On each read replica, run the following procedure\.

   ```
   CALL mysql.rds_set_master_auto_position(0); (Aurora MySQL version 1 and 2)
   CALL mysql.rds_set_source_auto_position(0); (Aurora MySQL version 3 and higher)
   ```

1. Reset the `gtid_mode` to `ON_PERMISSIVE`\.

   1. Make sure that the parameter group associated with the MySQL DB instance and each read replica has `gtid_mode` set to `ON_PERMISSIVE`\.

      For more information about setting configuration parameters using parameter groups, see [Working with parameter groups](USER_WorkingWithParamGroups.md)\.

   1. Reboot the MySQL DB instance and each read replica\. For more information about rebooting, see [Rebooting a DB instance](USER_RebootInstance.md)\.

1. Reset the `gtid_mode` to `OFF_PERMISSIVE`:

   1. Make sure that the parameter group associated with the MySQL DB instance and each read replica has `gtid_mode` set to `OFF_PERMISSIVE`\.

   1. Reboot the MySQL DB instance and each read replica\.

1. Wait for all of the GTID transactions to be applied on all of the read replicas\. To check that these are applied, do the following:

   Wait for all of the GTID transactions to be applied on the Aurora primary instance\. To check that these are applied, do the following:

   1. On the MySQL DB instance, run the `SHOW MASTER STATUS` command\.

      Your output should be similar to the following\.

      ```
      File                        Position
      ------------------------------------
      mysql-bin-changelog.000031      107
      ------------------------------------
      ```

      Note the file and position in your output\.

   1. On each read replica, use the file and position information from its source instance in the previous step to run the following query\.

      ```
      SELECT MASTER_POS_WAIT('file', position);
      ```

      For example, if the file name is `mysql-bin-changelog.000031` and the position is `107`, run the following statement\.

      ```
      SELECT MASTER_POS_WAIT('mysql-bin-changelog.000031', 107);
      ```

      If the read replica is past the specified position, the query returns immediately\. Otherwise, the function waits\. When the query returns for all read replicas, go to the next step\. 

1. Reset the GTID parameters to disable GTID\-based replication:

   1. Make sure that the parameter group associated with the MySQL DB instance and each read replica has the following parameter settings:
      + `gtid_mode` – `OFF`
      + `enforce_gtid_consistency` – `OFF`

   1. Reboot the MySQL DB instance and each read replica\.