# Using GTID\-Based Replication for Amazon RDS MySQL<a name="mysql-replication-gtid"></a>

*Global transaction identifiers \(GTIDs\)* are unique identifiers generated for committed MySQL transactions\. MySQL uses two different types of transactions for replication:
+ *GTID transactions* – Transactions that are identified by a GTID\.
+ *Anonymous transactions* – Transactions that do not have a GTID assigned\.

In a replication configuration, GTIDs are unique across all DB instances\. GTIDs simplify replication configuration because when you use them, you don't have to refer to log file positions\. GTIDs also make it easier to track replicated transactions and determine whether masters and replicas are consistent\.

You can use GTID\-based replication to replicate data with Amazon RDS MySQL Read Replicas or with an external MySQL database\. For Amazon RDS MySQL Read Replicas, you can configure GTID\-based replication when you are creating new Read Replicas, or you can convert existing Read Replicas to use GTID\-based replication\.

You can also use GTID\-based replication in a delayed replication configuration with Amazon RDS MySQL\. For more information, see [Configuring Delayed Replication with MySQL](USER_MySQL.Replication.ReadReplicas.md#USER_MySQL.Replication.ReadReplicas.DelayReplication)\.

For more information about GTID\-based replication with MySQL, see [ Replication with Global Transaction Identifiers](https://dev.mysql.com/doc/refman/5.7/en/replication-gtids.html) in the MySQL documentation\.

**Note**  
GTID\-based replication is supported for Amazon RDS MySQL version 5\.7\.23 and later MySQL 5\.7 versions\. All Amazon RDS MySQL DB instances in a replication configuration must meet this requirement\. GTID\-based replication is not supported for Amazon RDS MySQL 5\.5, 5\.6, or 8\.0\.

**Topics**
+ [Parameters for GTID\-Based Replication](#mysql-replication-gtid.parameters)
+ [Configuring GTID\-Based Replication for New Read Replicas](#mysql-replication-gtid.configuring-new-read-replicas)
+ [Configuring GTID\-Based Replication for Existing Read Replicas](#mysql-replication-gtid.configuring-existing-read-replicas)
+ [Disabling GTID\-Based Replication for an Amazon RDS MySQL DB Instance with Read Replicas](#mysql-replication-gtid.disabling)

**Note**  
For information about configuring GTID\-based replication with an external database, see [Replication with a MySQL or MariaDB Instance Running External to Amazon RDS](MySQL.Procedural.Importing.External.Repl.md)\.

## Parameters for GTID\-Based Replication<a name="mysql-replication-gtid.parameters"></a>

Use the following parameters to configure GTID\-based replication\.


****  

| Parameter | Valid Values | Description | 
| --- | --- | --- | 
|  `gtid_mode`  |  `OFF`, `OFF_PERMISSIVE`, `ON_PERMISSIVE`, `ON`  |  `OFF` specifies that new transactions are anonymous transactions \(that is, don't have GTIDs\), and a transaction must be anonymous to be replicated\.  `OFF_PERMISSIVE` specifies that new transactions are anonymous transactions, but all transactions can be replicated\.  `ON_PERMISSIVE` specifies that new transactions are GTID transactions, but all transactions can be replicated\.  `ON` specifies that new transactions are GTID transactions, and a transaction must be a GTID transaction to be replicated\.   | 
|  `enforce_gtid_consistency`  |  `OFF`, `ON`, `WARN`  |  `OFF` allows transactions to violate GTID consistency\.  `ON` prevents transactions from violating GTID consistency\.  `WARN` allows transactions to violate GTID consistency but generates a warning when a violation occurs\.   | 

To use GTID\-based replication with an Amazon RDS MySQL DB instance or Read Replica, make sure that parameter group for the DB instance or Read Replica has these parameters set to enable GTID\-based replication\. For more information about parameter groups, see [Working with DB Parameter Groups](USER_WorkingWithParamGroups.md)\.

**Note**  
In the AWS Management Console, the `gtid_mode` parameter appears as `gtid-mode`\.

## Configuring GTID\-Based Replication for New Read Replicas<a name="mysql-replication-gtid.configuring-new-read-replicas"></a>

When GTID\-based replication is enabled for an Amazon RDS MySQL DB instance, GTID\-based replication is configured automatically for Read Replicas of the DB instance\.

**To enable GTID\-based replication for new Read Replicas**

1. Make sure that the parameter group associated with the DB instance has the following parameter settings:
   + `gtid_mode` – `ON` or `ON_PERMISSIVE`
   + `enforce_gtid_consistency` – `ON`

   For more information about parameter groups, see [Working with DB Parameter Groups](USER_WorkingWithParamGroups.md)\.

1. If you changed the parameter group of the DB instance, reboot the DB instance\. For more information on how to do so, see [Rebooting a DB Instance](USER_RebootInstance.md)\.

1. Create one or more Read Replicas of the DB instance\. For more information on how to do so, see [Creating a Read Replica](USER_ReadRepl.md#USER_ReadRepl.Create)\.

Amazon RDS attempts to establish GTID\-based replication between the MySQL DB instance and the Read Replicas using the `MASTER_AUTO_POSITION`\. If the attempt fails, Amazon RDS uses log file positions for replication with the Read Replicas\. For more information about the `MASTER_AUTO_POSITION`, see [ GTID Auto\-Positioning](https://dev.mysql.com/doc/refman/5.7/en/replication-gtids-auto-positioning.html) in the MySQL documentation\.

## Configuring GTID\-Based Replication for Existing Read Replicas<a name="mysql-replication-gtid.configuring-existing-read-replicas"></a>

If you have an Amazon RDS MySQL DB instance with Read Replicas, and data is not being replicated using GTID\-based replication, you can configure GTID\-based replication between the DB instance and the Read Replicas\.

**To enable GTID\-based replication for existing Read Replicas**

1. If the DB instance or any Read Replica is using Amazon RDS MySQL version 5\.7\.22 or lower, upgrade the DB instance or Read Replica to Amazon RDS MySQL version 5\.7\.23 or later MySQL 5\.7 version\.

   For more information, see [Upgrading the MySQL DB Engine](USER_UpgradeDBInstance.MySQL.md)\.

1. \(Optional\) Reset the GTID parameters and test the behavior of the DB instance and Read Replicas:

   1. Make sure that the parameter group associated with the DB instance and each Read Replica has the `enforce_gtid_consistency` parameter set to `WARN`\.

      For more information about parameter groups, see [Working with DB Parameter Groups](USER_WorkingWithParamGroups.md)\.

   1. If you changed the parameter group of the DB instance, reboot the DB instance\. If you changed the parameter group for a Read Replica, reboot the Read Replica\.

      For more information, see [Rebooting a DB Instance](USER_RebootInstance.md)\.

   1. Run your DB instance and Read Replicas with your normal workload and monitor the log files\.

      If you see warnings about GTID\-incompatible transactions, adjust your application so that it only uses GTID\-compatible features\. Make sure that the DB instance is not generating any warnings about GTID\-incompatible transactions before proceeding to the next step\.

1. Reset the GTID parameters for GTID\-based replication that allows anonymous transactions until the Read Replicas have processed all of them\.

   1. Make sure that the parameter group associated with the DB instance and each Read Replica has the following parameter settings:
      + `gtid_mode` – `ON_PERMISSIVE`
      + `enforce_gtid_consistency` – `ON`

   1. If you changed the parameter group of the DB instance, reboot the DB instance\. If you changed the parameter group for a Read Replica, reboot the Read Replica\.

1. Wait for all of your anonymous transactions to be replicated\. To check that these are replicated, do the following:

   1. Run the following statement on your primary DB instance\.

      ```
      SHOW MASTER STATUS;
      ```

      Note the values in the `File` and `Position` columns\.

   1. On each Read Replica, use the file and position information from its master in the previous step to run the following query\.

      ```
      SELECT MASTER_POS_WAIT(file, position);
      ```

      For example, if the file name is `mysql-bin-changelog.000031` and the position is `107`, run the following statement\.

      ```
      SELECT MASTER_POS_WAIT(mysql-bin-changelog.000031, 107);
      ```

      If the Read Replica is past the specified position, the query returns immediately\. Otherwise, the function waits\. Proceed to the next step when the query returns for all Read Replicas\.

1. Reset the GTID parameters for GTID\-based replication only\.

   1. Make sure that the parameter group associated with the DB instance and each Read Replica has the following parameter settings:
      + `gtid_mode` – `ON`
      + `enforce_gtid_consistency` – `ON`

   1. Reboot the DB instance and each Read Replica\.

1. On each Read Replica, run the following procedure\.

   ```
   CALL mysql.rds_set_master_auto_position(1);
   ```

## Disabling GTID\-Based Replication for an Amazon RDS MySQL DB Instance with Read Replicas<a name="mysql-replication-gtid.disabling"></a>

You can disable GTID\-based replication for an Amazon RDS MySQL DB instance with Read Replicas\.

**To disable GTID\-based replication for an RDS MySQL DB instance with Read Replicas**

1. On each Read Replica, run the following procedure\.

   ```
   CALL mysql.rds_set_master_auto_position(0);
   ```

1. Reset the `gtid_mode` to `ON_PERMISSIVE`\.

   1. Make sure that the parameter group associated with the Amazon RDS MySQL DB instance and each Read Replica has `gtid_mode` set to `ON_PERMISSIVE`\.

      For more information about parameter groups, see [Working with DB Parameter Groups](USER_WorkingWithParamGroups.md)\.

   1. Reboot the Amazon RDS MySQL DB instance and each Read Replica\. For more information about rebooting, see [Rebooting a DB Instance](USER_RebootInstance.md)\.

1. Reset the `gtid_mode` to `OFF_PERMISSIVE`:

   1. Make sure that the parameter group associated with the Amazon RDS MySQL DB instance and each Read Replica has `gtid_mode` set to `OFF_PERMISSIVE`\.

   1. Reboot the Amazon RDS MySQL DB instance and each Read Replica\.

1. Wait for all of the GTID transactions to be applied on all of the Read Replicas\. To check that these are applied, do the following:

   1. On the Amazon RDS MySQL DB instance, run the `SHOW MASTER STATUS` command\. 

      Your output is similar to the following\.

      ```
      File                        Position  
      ------------------------------------
       mysql-bin-changelog.000031      107   
      ------------------------------------
      ```

      Note the file and position in your output\.

   1. On each Read Replica, use the file and position information from its master in the previous step to run the following query\.

      ```
      SELECT MASTER_POS_WAIT(file, position);
      ```

      For example, if the file name is `mysql-bin-changelog.000031` and the position is `107`, run the following statement\.

      ```
      SELECT MASTER_POS_WAIT(mysql-bin-changelog.000031, 107);
      ```

      If the Read Replica is past the specified position, the query returns immediately\. Otherwise, the function waits\. Proceed to the next step when the query returns for all Read Replicas\.

1. Reset the GTID parameters to disable GTID\-based replication\.

   1. Make sure that the parameter group associated with the Amazon RDS MySQL DB instance and each Read Replica has the following parameter settings:
      + `gtid_mode` – `OFF`
      + `enforce_gtid_consistency` – `OFF`

   1. Reboot the Amazon RDS MySQL DB instance and each Read Replica\.