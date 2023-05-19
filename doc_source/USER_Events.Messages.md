# Amazon RDS event categories and event messages<a name="USER_Events.Messages"></a>

Amazon RDS generates a significant number of events in categories that you can subscribe to using the Amazon RDS Console, AWS CLI, or the API\.

**Topics**
+ [DB cluster events](#USER_Events.Messages.cluster)
+ [DB instance events](#USER_Events.Messages.instance)
+ [DB parameter group events](#USER_Events.Messages.parameter-group)
+ [DB security group events](#USER_Events.Messages.security-group)
+ [DB snapshot events](#USER_Events.Messages.snapshot)
+ [DB cluster snapshot events](#USER_Events.Messages.cluster-snapshot)
+ [RDS Proxy events](#USER_Events.Messages.rds-proxy)
+ [Blue/green deployment events](#USER_Events.Messages.BlueGreenDeployments)
+ [Custom engine version events](#USER_Events.Messages.CEV)

## DB cluster events<a name="USER_Events.Messages.cluster"></a>

The following table shows the event category and a list of events when a DB cluster is the source type\.

For more information about Multi\-AZ DB cluster deployments, see [Multi\-AZ DB cluster deployments](multi-az-db-clusters-concepts.md)


|  Category  | RDS event ID |  Message  |  Notes  | 
| --- | --- | --- | --- | 
| creation | RDS\-EVENT\-0170 |  DB cluster created\.  |    | 
|  failover  | RDS\-EVENT\-0069 |  Cluster failover failed, check the health of your cluster instances and try again\.  |    | 
|  failover  | RDS\-EVENT\-0070 |  Promoting previous primary again: *name*\.  |    | 
|  failover  | RDS\-EVENT\-0071 |  Completed failover to DB instance: *name*\.  |    | 
|  failover  | RDS\-EVENT\-0072 |  Started same AZ failover to DB instance: *name*\.  |    | 
|  failover  | RDS\-EVENT\-0073 |  Started cross AZ failover to DB instance: *name*\.  |    | 
|  global failover  | RDS\-EVENT\-0181 |  Global failover to DB cluster *name* in Region *name* started\.  |  The process can be delayed because other operations are running on the DB cluster\.  | 
|  global failover  | RDS\-EVENT\-0182 |  Old primary DB cluster *name* in Region *name* successfully shut down\.  |  The old primary instance in the global database isn't accepting writes\. All volumes are synchronized\.  | 
|  global failover  | RDS\-EVENT\-0183 |  Waiting for data synchronization across global cluster members\. Current lags behind primary DB cluster: *reason*\.  |  A replication lag is occurring during the synchronization phase of the global database failover\.  | 
|  global failover  | RDS\-EVENT\-0184 |  New primary DB cluster *name* in Region *name* was successfully promoted\.  |  The volume topology of the global database is reestablished with the new primary volume\.  | 
|  global failover  | RDS\-EVENT\-0185 |  Global failover to DB cluster *name* in Region *name* finished\.  |  The global database failover is finished on the primary DB cluster\. Replicas might take long to come online after the failover completes\.  | 
|  global failover  | RDS\-EVENT\-0186 |  Global failover to DB cluster *name* in Region *name* is cancelled\.  |    | 
|  global failover  | RDS\-EVENT\-0187 |  Global failover to DB cluster *name* in Region *name* failed\.  |    | 
|  maintenance  | RDS\-EVENT\-0176 |  Database cluster engine major version has been upgraded\.  |  | 
|  maintenance  | RDS\-EVENT\-0286 |  Database cluster engine version upgrade started\.  |  | 
|  maintenance  | RDS\-EVENT\-0287 |  Operating system upgrade requirement detected\.  |  | 
|  maintenance  | RDS\-EVENT\-0288 |  Cluster operating system upgrade starting\.  |  | 
|  maintenance  | RDS\-EVENT\-0289 |  Cluster operating system upgrade completed\.  |  | 
|  maintenance  | RDS\-EVENT\-0290 |  Database cluster has been patched: source version *version\_number* => *new\_version\_number*\.  |  | 
| notification | RDS\-EVENT\-0172 |  Renamed cluster from *name* to *name*\.  |    | 

## DB instance events<a name="USER_Events.Messages.instance"></a>

The following table shows the event category and a list of events when a DB instance is the source type\.


|  Category  | RDS event ID |  Message  |  Notes  | 
| --- | --- | --- | --- | 
|  availability  | RDS\-EVENT\-0006 |  DB instance restarted\.  |  | 
|  availability  | RDS\-EVENT\-0004 |  DB instance shutdown\.  |  | 
|  availability  | RDS\-EVENT\-0022 |  Error restarting mysql: *message*\.  | An error has occurred while restarting MySQL\. | 
|  availability  | RDS\-EVENT\-0221 |  DB instance has reached the storage\-full threshold, and the database has been shut down\. You can increase the allocated storage to address this issue\.  |  | 
|  availability  | RDS\-EVENT\-0222 |  Free storage capacity for DB instance *name* is low at *percentage* of the allocated storage \[Allocated storage: *amount*, Free storage: *amount*\]\. The database will be shut down to prevent corruption if free storage is lower than *amount*\. You can increase the allocated storage to address this issue\.  |  | 
|  backup  | RDS\-EVENT\-0001 |  Backing up DB instance\.  |  | 
|  backup  | RDS\-EVENT\-0002 |  Finished DB instance backup\.  |  | 
|  backup  | RDS\-EVENT\-0086 |  We are unable to associate the option group *name* with the database instance *name*\. Confirm that option group *name* is supported on your DB instance class and configuration\. If so, verify all option group settings and retry\.  |  For more information see [Working with option groups](USER_WorkingWithOptionGroups.md)\. | 
|  configuration change  | RDS\-EVENT\-0009 |  Added security group *name* to DB instance\.  |  | 
|  configuration change  | RDS\-EVENT\-0024 |  Applying modification to convert to a Multi\-AZ DB instance\.  |  | 
|  configuration change  | RDS\-EVENT\-0030 |  Applying modification to convert to a standard \(Single\-AZ\) DB instance\.  |  | 
|  configuration change  | RDS\-EVENT\-0012 |  Applying modification to database instance class\.   |  | 
|  configuration change  | RDS\-EVENT\-0018 |  Applying modification to allocated storage\.  |  | 
|  configuration change  | RDS\-EVENT\-0011 |  Updated to use DBParameterGroup *name*\.  |  | 
|  configuration change  | RDS\-EVENT\-0092 |  Finished updating DB parameter group\.  |  | 
|  configuration change  | RDS\-EVENT\-0028 |  Disabled automated backups\.  |  | 
|  configuration change  | RDS\-EVENT\-0032 |  Enabled automated backups\.  |  | 
|  configuration change  | RDS\-EVENT\-0033 |  There are *number* users matching the master username; only resetting the one not tied to a specific host\.  |  | 
|  configuration change  | RDS\-EVENT\-0025 |  Finished applying modification to convert to a Multi\-AZ DB instance\.  |  | 
|  configuration change  | RDS\-EVENT\-0029 |  Finished applying modification to convert to a standard \(Single\-AZ\) DB instance\.  |  | 
|  configuration change  | RDS\-EVENT\-0014 |  Finished applying modification to DB instance class\.  |  | 
|  configuration change  | RDS\-EVENT\-0017 |  Finished applying modification to allocated storage\.  |  | 
|  configuration change  | RDS\-EVENT\-0010 |  Removed security group *name* from DB instance\.  |  | 
|  configuration change  | RDS\-EVENT\-0016 |  Reset master credentials\.  |  | 
|  configuration change  | RDS\-EVENT\-0067 |  Unable to reset your password\. Error information: *message*\.  |  | 
|  configuration change  | RDS\-EVENT\-0078 |  Monitoring Interval changed to *number*\.  |  The Enhanced Monitoring configuration has been changed\. | 
|  configuration change  | RDS\-EVENT\-0217 |  Applying autoscaling\-initiated modification to allocated storage\.  |  | 
|  configuration change  | RDS\-EVENT\-0218 |  Finished applying autoscaling\-initiated modification to allocated storage\.  |  | 
|  creation  | RDS\-EVENT\-0005 |  DB instance created\.  |  | 
|  deletion  | RDS\-EVENT\-0003 |  DB instance deleted\.  |  | 
|  failover  | RDS\-EVENT\-0013 |  Multi\-AZ instance failover started\.  | A Multi\-AZ failover that resulted in the promotion of a standby DB instance has started\. | 
|  failover  | RDS\-EVENT\-0015 |  Multi\-AZ failover to standby complete \- DNS propagation may take a few minutes\.  | A Multi\-AZ failover that resulted in the promotion of a standby DB instance is complete\. It may take several minutes for the DNS to transfer to the new primary DB instance\. | 
|  failover  | RDS\-EVENT\-0034 |  Abandoning user requested failover since a failover recently occurred on the database instance\.  | Amazon RDS isn't attempting a requested failover because a failover recently occurred on the DB instance\. | 
|  failover  | RDS\-EVENT\-0049 | Multi\-AZ instance failover completed\. |  | 
|  failover  | RDS\-EVENT\-0050 |  Multi\-AZ instance activation started\.  | A Multi\-AZ activation has started after a successful DB instance recovery | 
|  failover  | RDS\-EVENT\-0051 |  Multi\-AZ instance activation completed\.  | A Multi\-AZ activation is complete\. Your database should be accessible now\. | 
|  failover  | RDS\-EVENT\-0065 |  Recovered from partial failover\.  |  | 
|  failure  | RDS\-EVENT\-0031 |  DB instance put into *name* state\. RDS recommends that you initiate a point\-in\-time\-restore\.  | The DB instance has failed due to an incompatible configuration or an underlying storage issue\. Begin a point\-in\-time\-restore for the DB instance\. | 
|  failure  | RDS\-EVENT\-0035 |  Database instance put into *state*\. *message*\.  | The DB instance has invalid parameters\. For example, if the DB instance could not start because a memory\-related parameter is set too high for this instance class, your action would be to modify the memory parameter and reboot the DB instance\. | 
|  failure  | RDS\-EVENT\-0036 |  Database instance in *state*\. *message*\.  | The DB instance is in an incompatible network\. Some of the specified subnet IDs are invalid or do not exist\. | 
|  failure  | RDS\-EVENT\-0058 |  The Statspack installation failed\. *message*\.  | Error while creating Oracle Statspack user account `PERFSTAT`\. Drop the account before you add the `STATSPACK` option\. | 
|  failure  | RDS\-EVENT\-0079 |  Amazon RDS has been unable to create credentials for enhanced monitoring and this feature has been disabled\. This is likely due to the rds\-monitoring\-role not being present and configured correctly in your account\. Please refer to the troubleshooting section in the Amazon RDS documentation for further details\.  |  Enhanced Monitoring can't be enabled without the Enhanced Monitoring IAM role\. For information about creating the IAM role, see [To create an IAM role for Amazon RDS enhanced monitoring](USER_Monitoring.OS.Enabling.md#USER_Monitoring.OS.IAMRole)\.  | 
|  failure  | RDS\-EVENT\-0080 |  Amazon RDS has been unable to configure enhanced monitoring on your instance: *name* and this feature has been disabled\. This is likely due to the rds\-monitoring\-role not being present and configured correctly in your account\. Please refer to the troubleshooting section in the Amazon RDS documentation for further details\.  |  Enhanced Monitoring was disabled because an error occurred during the configuration change\. It is likely that the Enhanced Monitoring IAM role is configured incorrectly\. For information about creating the enhanced monitoring IAM role, see [To create an IAM role for Amazon RDS enhanced monitoring](USER_Monitoring.OS.Enabling.md#USER_Monitoring.OS.IAMRole)\.  | 
|  failure  | RDS\-EVENT\-0081 |  Amazon RDS has been unable to create credentials for *name* option\. This is due to the *name* IAM role not being configured correctly in your account\. Please refer to the troubleshooting section in the Amazon RDS documentation for further details\.  |  The IAM role that you use to access your Amazon S3 bucket for SQL Server native backup and restore is configured incorrectly\. For more information, see [Setting up for native backup and restore](SQLServer.Procedural.Importing.md#SQLServer.Procedural.Importing.Native.Enabling)\.  | 
|  failure  | RDS\-EVENT\-0165 |  The RDS Custom DB instance is outside the support perimeter\.  |  It's your responsibility to fix configuration issues that put your RDS Custom DB instance into the `unsupported-configuration` state\. If the issue is with the AWS infrastructure, you can use the console or the AWS CLI to fix it\. If the issue is with the operating system or the database configuration, you can log in to the host to fix it\.For more information, see [RDS Custom support perimeter and unsupported configurations](custom-troubleshooting.md#custom-troubleshooting.support-perimeter)\. | 
|  failure  | RDS\-EVENT\-0188 |  The DB instance is in a state that can't be upgraded\. *message*  |  Amazon RDS was unable to upgrade a MySQL DB instance from version 5\.7 to version 8\.0 because of incompatibilities related to the data dictionary\. The DB instance was rolled back to MySQL version 5\.7\. For more information, see [Rollback after failure to upgrade from MySQL 5\.7 to 8\.0](USER_UpgradeDBInstance.MySQL.md#USER_UpgradeDBInstance.MySQL.Major.RollbackAfterFailure)\.  | 
|  failure  | RDS\-EVENT\-0219 |  DB instance is in an invalid state\. No actions are necessary\. Autoscaling will retry later\.  |  | 
|  failure  | RDS\-EVENT\-0220 |  DB instance is in the cooling\-off period for a previous scale storage operation\. We're optimizing your DB instance\. This takes at least 6 hours\. No actions are necessary\. Autoscaling will retry after the cooling\-off period\.  |  | 
|  failure  | RDS\-EVENT\-0223 |  Storage autoscaling is unable to scale the storage for the reason: *reason*\.  |  | 
|  failure  | RDS\-EVENT\-0224 |  Storage autoscaling has triggered a pending scale storage task that will reach or exceed the maximum storage threshold\. Increase the maximum storage threshold\.  |  | 
|  failure  | RDS\-EVENT\-0237 |  DB instance has a storage type that's currently unavailable in the Availability Zone\. Autoscaling will retry later\.  |  | 
| failure | RDS\-EVENT\-0254 |  Underlying storage quota for this customer account has exceeded the limit\. Please increase the allowed storage quota to let the scaling go through on the instance\.  |  | 
|  low storage  | RDS\-EVENT\-0007 |  Allocated storage has been exhausted\. Allocate additional storage to resolve\.  |  The allocated storage for the DB instance has been consumed\. To resolve this issue, allocate additional storage for the DB instance\. For more information, see the [RDS FAQ](https://aws.amazon.com/rds/faqs)\. You can monitor the storage space for a DB instance using the **Free Storage Space** metric\.  | 
|  low storage  | RDS\-EVENT\-0089 |  The free storage capacity for DB instance: *name* is low at *percentage* of the provisioned storage \[Provisioned Storage: *size*, Free Storage: *size*\]\. You may want to increase the provisioned storage to address this issue\.  |  The DB instance has consumed more than 90% of its allocated storage\. You can monitor the storage space for a DB instance using the **Free Storage Space** metric\.  | 
|  low storage  | RDS\-EVENT\-0227 |  Your Aurora cluster's storage is dangerously low with only *amount* terabytes remaining\. Please take measures to reduce the storage load on your cluster\.  |  The Aurora storage subsystem is running low on space\.  | 
|  maintenance  | RDS\-EVENT\-0026 |  Applying off\-line patches to DB instance\.  |  Offline maintenance of the DB instance is taking place\. The DB instance is currently unavailable\.  | 
|  maintenance  | RDS\-EVENT\-0027 |  Finished applying off\-line patches to DB instance\.  |  Offline maintenance of the DB instance is complete\. The DB instance is now available\.  | 
|  maintenance  | RDS\-EVENT\-0047 |  Database instance patched\.  |  | 
|  maintenance  | RDS\-EVENT\-0155 |  The DB instance has a DB engine minor version upgrade available\.  |  | 
|  maintenance  | RDS\-EVENT\-0264 |  The pre\-check started for the DB engine version upgrade\.  |  | 
|  maintenance  | RDS\-EVENT\-0265 |  The pre\-check finished for the DB engine version upgrade\.  |  | 
|  maintenance  | RDS\-EVENT\-0266 |  The downtime started for the DB instance\.  |  | 
|  maintenance  | RDS\-EVENT\-0267 |  The engine version upgrade started\.  |  | 
|  maintenance  | RDS\-EVENT\-0268 |  The engine version upgrade finished\. |  | 
|  maintenance  | RDS\-EVENT\-0269 |  The post\-upgrade tasks are in progress\. |  | 
|  maintenance  | RDS\-EVENT\-0270 |  The DB engine version upgrade failed\. The engine version upgrade rollback succeeded\. |  | 
|  maintenance, notification  | RDS\-EVENT\-0191 |  A new version of the time zone file is available for update\.  |  If you update your RDS for Oracle DB engine, Amazon RDS generates this event if you haven't chosen a time zone file upgrade and the database doesn’t use the latest DST time zone file available on the instance\. For more information, see [Oracle time zone file autoupgrade](Appendix.Oracle.Options.Timezone-file-autoupgrade.md)\.  | 
|  maintenance, notification  | RDS\-EVENT\-0192 |  The update of your time zone file has started\.  |  The upgrade of your Oracle time zone file has begun\. For more information, see [Oracle time zone file autoupgrade](Appendix.Oracle.Options.Timezone-file-autoupgrade.md)\.  | 
|  maintenance, notification  | RDS\-EVENT\-0193 |  No update is available for the current time zone file version\.  |  Your Oracle DB instance is using latest time zone file version, and either of the following statements is true: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_Events.Messages.html) For more information, see [Oracle time zone file autoupgrade](Appendix.Oracle.Options.Timezone-file-autoupgrade.md)\.  | 
|  maintenance, notification  | RDS\-EVENT\-0194 |  The update of your time zone file has finished\.  |  The update of your Oracle time zone file has completed\. For more information, see [Oracle time zone file autoupgrade](Appendix.Oracle.Options.Timezone-file-autoupgrade.md)\.  | 
|  maintenance, failure  | RDS\-EVENT\-0195 |  *message*  |  The update of the Oracle time zone file failed\. For more information, see [Oracle time zone file autoupgrade](Appendix.Oracle.Options.Timezone-file-autoupgrade.md)\.  | 
|  notification  | RDS\-EVENT\-0044 |  *message*  | This is an operator\-issued notification\. For more information, see the event message\. | 
|  notification  | RDS\-EVENT\-0048 |  Delaying database engine upgrade since this instance has read replicas that need to be upgraded first\.  | Patching of the DB instance has been delayed\. | 
|  notification  | RDS\-EVENT\-0054 |  *message*  | The MySQL storage engine you are using is not InnoDB, which is the recommended MySQL storage engine for Amazon RDS\. For information about MySQL storage engines, see [Supported storage engines for RDS for MySQL](MySQL.Concepts.FeatureSupport.md#MySQL.Concepts.Storage)\. | 
|  notification  | RDS\-EVENT\-0055 |  *message*  |  The number of tables you have for your DB instance exceeds the recommended best practices for Amazon RDS\. Reduce the number of tables on your DB instance\. For information about recommended best practices, see [Amazon RDS basic operational guidelines](CHAP_BestPractices.md#CHAP_BestPractices.DiskPerformance)\.  | 
|  notification  | RDS\-EVENT\-0056 |  *message*  |  The number of databases you have for your DB instance exceeds the recommended best practices for Amazon RDS\. Reduce the number of databases on your DB instance\. For information about recommended best practices, see [Amazon RDS basic operational guidelines](CHAP_BestPractices.md#CHAP_BestPractices.DiskPerformance)\.  | 
|  notification  | RDS\-EVENT\-0064 |  The TDE encryption key was rotated successfully\.  | For information about recommended best practices, see [Amazon RDS basic operational guidelines](CHAP_BestPractices.md#CHAP_BestPractices.DiskPerformance)\.  | 
|  notification  | RDS\-EVENT\-0084 |  Unable to convert the DB instance to Multi\-AZ: *message*\.  |  You attempted to convert a DB instance to Multi\-AZ, but it contains in\-memory file groups that are not supported for Multi\-AZ\. For more information, see [Multi\-AZ deployments for Amazon RDS for Microsoft SQL Server](USER_SQLServerMultiAZ.md)\.   | 
|  notification  | RDS\-EVENT\-0087 |  DB instance stopped\.   |  | 
|  notification  | RDS\-EVENT\-0088 |  DB instance started\.  |  | 
|  notification  | RDS\-EVENT\-0154 |  DB instance is being started due to it exceeding the maximum allowed time being stopped\.  |  | 
|  notification  | RDS\-EVENT\-0157 |  Unable to modify the DB instance class\. *message*\.  |  RDS can't modify the DB instance class because the target instance class can't support the number of databases that exist on the source DB instance\. The error message appears as: "The instance has *N* databases, but after conversion it would only support *N*"\. For more information, see [Limitations for Microsoft SQL Server DB instances](CHAP_SQLServer.md#SQLServer.Concepts.General.FeatureSupport.Limits)\.  | 
|  notification  | RDS\-EVENT\-0158 |  Database instance is in a state that cannot be upgraded: *message*\.  |  | 
|  notification  | RDS\-EVENT\-0167 |  *message*  |  The RDS Custom support perimeter configuration has changed\.  | 
|  notification  | RDS\-EVENT\-0189 |  The gp2 burst balance credits for the RDS database instance are low\. To resolve this issue, reduce IOPS usage or modify your storage settings to enable higher performance\.  |  The gp2 burst balance credits for the RDS database instance are low\. To resolve this issue, reduce IOPS usage or modify your storage settings to enable higher performance\. For more information, see [I/O credits and burst performance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-volume-types.html#EBSVolumeTypes_gp2) in the *Amazon Elastic Compute Cloud User Guide*\.  | 
|  notification  | RDS\-EVENT\-0225 |  Storage size *amount* GB is approaching the maximum storage threshold *amount* GB\. Increase the maximum storage threshold\.  |  This event is invoked when storage reaches 80% of the maximum storage threshold\. To avoid the event, increase the maximum storage threshold\.  | 
|  notification  | RDS\-EVENT\-0231 |  Your DB instance's storage modification encountered an internal error\. The modification request is pending and will be retried later\.  |  An error has occurred in the read replication process\. For more information, see the event message\.  In addition, see the troubleshooting section for read replicas for your DB engine\.  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_Events.Messages.html)   | 
|  notification  | RDS\-EVENT\-0253 |  The database is using the doublewrite buffer\. *message*\. For more information see the RDS Optimized Writes for *name* documentation\.  | RDS Optimized Writes is incompatible with the instance storage configuration\. For more information, see [Improving write performance with Amazon RDS Optimized Writes for MySQL](rds-optimized-writes.md) and [Improving write performance with Amazon RDS Optimized Writes for MariaDB](rds-optimized-writes-mariadb.md)\. | 
|  read replica  | RDS\-EVENT\-0045 |  Replication has stopped\.  |  Replication on your DB instance has been stopped due to insufficient storage\. Scale storage or reduce the maximum size of your redo logs to let replication continue\. To accommodate redo logs of size %d MiB you need at least %d MiB free storage\.  | 
|  read replica  | RDS\-EVENT\-0046 |  Replication for the Read Replica resumed\.  | This message appears when you first create a read replica, or as a monitoring message confirming that replication is functioning properly\. If this message follows an `RDS-EVENT-0045` notification, then replication has resumed following an error or after replication was stopped\. | 
|  read replica  | RDS\-EVENT\-0057 |  Replication streaming has been terminated\.  |  | 
|  read replica  | RDS\-EVENT\-0062 |  Replication for the Read Replica has been manually stopped\.  |  | 
|  read replica  | RDS\-EVENT\-0063 |  Replication from Non RDS instance has been reset\.  |  | 
|  read replica  | RDS\-EVENT\-0202 |  Read replica creation failed\.  |  | 
|  recovery  | RDS\-EVENT\-0020 |  Recovery of the DB instance has started\. Recovery time will vary with the amount of data to be recovered\.  |  | 
|  recovery  | RDS\-EVENT\-0021 |  Recovery of the DB instance is complete\.  |  | 
|  recovery  | RDS\-EVENT\-0023 |  Emergent Snapshot Request: *message*\.  |  A manual backup has been requested but Amazon RDS is currently in the process of creating a DB snapshot\. Submit the request again after Amazon RDS has completed the DB snapshot\.  | 
|  recovery  | RDS\-EVENT\-0052 |  Multi\-AZ instance recovery started\.  | Recovery time will vary with the amount of data to be recovered\. | 
|  recovery  | RDS\-EVENT\-0053 |  Multi\-AZ instance recovery completed\. Pending failover or activation\.  |  | 
|  recovery  | RDS\-EVENT\-0066 |  Instance will be degraded while mirroring is reestablished: *message*\.  |  The SQL Server DB instance is re\-establishing its mirror\. Performance will be degraded until the mirror is reestablished\. A database was found with non\-FULL recovery model\. The recovery model was changed back to FULL and mirroring recovery was started\. \(<dbname>: <recovery model found>\[,\.\.\.\]\)"  | 
|  recovery  | RDS\-EVENT\-0166 |  *message*  |  The RDS Custom DB instance is inside the support perimeter\.  | 
|  restoration  | RDS\-EVENT\-0019 |  Restored from DB instance *name* to *name*\.  |  The DB instance has been restored from a point\-in\-time backup\.  | 
|  restoration  | RDS\-EVENT\-0043 |  Restored from snapshot *snapshot\_name*  |  The DB instance has been restored from a DB snapshot\.  | 
|  security  | RDS\-EVENT\-0068 |  Decrypting hsm partition password to update instance\.  |  RDS is decrypting the AWS CloudHSM partition password to make updates to the DB instance\. For more information see [Oracle Database Transparent Data Encryption \(TDE\) with AWS CloudHSM](https://docs.aws.amazon.com/cloudhsm/latest/userguide/oracle-tde.html) in the *AWS CloudHSM User Guide*\.  | 
|  security patching  | RDS\-EVENT\-0230 |  A system update is available for your DB instance\. For information about applying updates, see 'Maintaining a DB instance' in the RDS User Guide\.  |    A new Operating System update is available\. A new, minor version, operating system update is available for your DB instance\. For information about applying updates, see [Working with operating system updates](USER_UpgradeDBInstance.Maintenance.md#OS_Updates)\.  | 

## DB parameter group events<a name="USER_Events.Messages.parameter-group"></a>

The following table shows the event category and a list of events when a DB parameter group is the source type\.


|  Category  | RDS event ID |  Message  |  Notes  | 
| --- | --- | --- | --- | 
|  configuration change  | RDS\-EVENT\-0037 |  Updated parameter *name* to *value* with apply method *method*\.   |    | 

## DB security group events<a name="USER_Events.Messages.security-group"></a>

The following table shows the event category and a list of events when a DB security group is the source type\.

**Note**  
DB security groups are resources for EC2\-Classic\. EC2\-Classic was retired on August 15, 2022\. If you haven't migrated from EC2\-Classic to a VPC, we recommend that you migrate as soon as possible\. For more information, see [Migrate from EC2\-Classic to a VPC](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/vpc-migrate.html) in the *Amazon EC2 User Guide* and the blog [ EC2\-Classic Networking is Retiring – Here’s How to Prepare](http://aws.amazon.com/blogs/aws/ec2-classic-is-retiring-heres-how-to-prepare/)\.


|  Category  | RDS event ID |  Message  |  Notes  | 
| --- | --- | --- | --- | 
|  configuration change  | RDS\-EVENT\-0038 |  Applied change to security group\.  |    | 
|  failure  | RDS\-EVENT\-0039 |  Revoking authorization as *user*\.  |  The security group owned by *user* doesn't exist\. The authorization for the security group has been revoked because it is invalid\.  | 

## DB snapshot events<a name="USER_Events.Messages.snapshot"></a>

The following table shows the event category and a list of events when a DB snapshot is the source type\.


|  Category  | RDS event ID |  Message  |  Notes  | 
| --- | --- | --- | --- | 
|  creation  | RDS\-EVENT\-0040 |  Creating manual snapshot\.  |  | 
|  creation  | RDS\-EVENT\-0042 |  Manual snapshot created\.  |  | 
|  creation  | RDS\-EVENT\-0090 | Creating automated snapshot\. |  | 
|  creation  | RDS\-EVENT\-0091 | Automated snapshot created\. |  | 
|  deletion  | RDS\-EVENT\-0041 |  Deleted user snapshot\.  |  | 
|  notification  | RDS\-EVENT\-0059 |  Started copy of snapshot*name* from region *name*\.  |  This is a cross\-Region snapshot copy\.  | 
|  notification  | RDS\-EVENT\-0060 |  Finished copy of snapshot *name* from region *name* in *number* minutes\.  |  This is a cross\-Region snapshot copy\.  | 
|  notification  | RDS\-EVENT\-0061 |  Canceled snapshot copy request of *name* from region *name*\.  |  This is a cross\-Region snapshot copy\.  | 
|  notification  | RDS\-EVENT\-0159 |  The snapshot export task failed\.  |  | 
|  notification  | RDS\-EVENT\-0160 |  The snapshot export task was canceled\.  |  | 
|  notification  | RDS\-EVENT\-0161 |  The snapshot export task completed\.  |  | 
|  notification  | RDS\-EVENT\-0196 |  Started copy of snapshot *name* in region *name*\.  |  This is a local snapshot copy\.  | 
|  notification  | RDS\-EVENT\-0197 |  Finished copy of snapshot *name* in region *name*\.  |  This is a local snapshot copy\.  | 
|  notification  | RDS\-EVENT\-0190 |  Canceled snapshot copy request of *name* in region *name*\.  |  This is a local snapshot copy\.  | 
|  restoration  | RDS\-EVENT\-0043 |  Restored from snapshot *name*\.  |  A DB instance is being restored from a DB snapshot\.  | 

## DB cluster snapshot events<a name="USER_Events.Messages.cluster-snapshot"></a>

The following table shows the event category and a list of events when a DB cluster snapshot is the source type\.


|  Category  | RDS event ID |  Message  |  Notes  | 
| --- | --- | --- | --- | 
|  backup  | RDS\-EVENT\-0074 |  Creating manual cluster snapshot\.  |  | 
|  backup  | RDS\-EVENT\-0075 |  Manual cluster snapshot created\.  |  | 
| backup | RDS\-EVENT\-0168 |  Creating automated cluster snapshot\.  |  | 
| backup | RDS\-EVENT\-0169 |  Automated cluster snapshot created\.  |  | 

## RDS Proxy events<a name="USER_Events.Messages.rds-proxy"></a>

The following table shows the event category and a list of events when an RDS Proxy is the source type\.


|  Category  | RDS event ID |  Message  |  Notes  | 
| --- | --- | --- | --- | 
| configuration change | RDS\-EVENT\-0204 |  RDS modified DB proxy *name*\.  |  | 
| configuration change | RDS\-EVENT\-0207 |  RDS modified the end point of the DB proxy *name*\.  |  | 
| configuration change | RDS\-EVENT\-0213 |  RDS detected the addition of the DB instance and automatically added it to the target group of the DB proxy *name*\.  |  | 
|  configuration change  | RDS\-EVENT\-0214 |  RDS detected deletion of DB instance *name* and automatically removed it from target group *name* of DB proxy *name*\.  |  | 
|  configuration change  | RDS\-EVENT\-0215 |  RDS detected deletion of DB cluster *name* and automatically removed it from target group *name* of DB proxy *name*\.  |  | 
|  creation  | RDS\-EVENT\-0203 |  RDS created DB proxy *name*\.  |  | 
|  creation  | RDS\-EVENT\-0206 |  RDS created endpoint *name* for DB proxy *name*\.  |  | 
| deletion | RDS\-EVENT\-0205 |  RDS deleted DB proxy *name*\.  |  | 
|  deletion  | RDS\-EVENT\-0208 |  RDS deleted endpoint *name* for DB proxy *name*\.  |  | 
|  failure  | RDS\-EVENT\-0243 |  RDS failed to provision capacity for proxy *name* because there aren't enough IP addresses available in your subnets: *name*\. To fix the issue, make sure that your subnets have the minimum number of unused IP addresses as recommended in the RDS Proxy documentation\.  |  To determine the recommended number for your instance class, see [Planning for IP address capacity](rds-proxy-setup.md#rds-proxy-network-prereqs.plan-ip-address)\.  | 

## Blue/green deployment events<a name="USER_Events.Messages.BlueGreenDeployments"></a>

The following table shows the event category and a list of events when a blue/green deployment is the source type\.

For more information about blue/green deployments, see [Using Amazon RDS Blue/Green Deployments for database updates](blue-green-deployments.md)\.


|  Category  | Amazon RDS event ID |  Message  |  Notes  | 
| --- | --- | --- | --- | 
|  creation  | RDS\-EVENT\-0244 |  Blue/green deployment tasks completed\. You can make more modifications to the green environment databases or switch over the deployment\.  |  | 
|  deletion  | RDS\-EVENT\-0246 |  Blue/green deployment deleted\.  |  | 
|  failure  | RDS\-EVENT\-0245 |  Creation of blue/green deployment failed because the \(source/target\) DB \(instance/cluster\) wasn't found\.   |  | 
|  failure  | RDS\-EVENT\-0249 |  Switchover canceled on blue/green deployment\.  |  | 
|  failure  | RDS\-EVENT\-0252 |  Switchover from primary source to target canceled due to *reason*\.  |  | 
|  failure  | RDS\-EVENT\-0261 |  Switchover from source to target was canceled\.  |  | 
|  notification  | RDS\-EVENT\-0247 |  Switchover started on blue/green deployment\.  |  | 
|  notification  | RDS\-EVENT\-0248 |  Switchover completed on blue/green deployment\.  |  | 
|  notification  | RDS\-EVENT\-0250 |  Switchover from primary source to target started\.  |  | 
|  notification  | RDS\-EVENT\-0251 |  Switchover from primary source to target completed\. Renamed databases\.  |  | 
|  notification  | RDS\-EVENT\-0259 |  Switchover from source to target started\.  |  | 
|  notification  | RDS\-EVENT\-0260 |  Switchover from source to target completed\. Renamed databases\.  |  | 

## Custom engine version events<a name="USER_Events.Messages.CEV"></a>

The following table shows the event category and a list of events when a custom engine version is the source type\.


|  Category  | Amazon RDS event ID |  Message  |  Notes  | 
| --- | --- | --- | --- | 
|  failure  | RDS\-EVENT\-0198 |  Creation failed for custom engine version *name*\. *message*  | The message includes details about the failure, such as missing files\. | 