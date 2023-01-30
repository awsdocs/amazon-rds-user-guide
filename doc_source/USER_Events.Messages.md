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
+ [Message attributes](#USER_Events.Messages.Attributes)

## DB cluster events<a name="USER_Events.Messages.cluster"></a>

The following table shows the event category and a list of events when a DB cluster is the source type\.

For more information about Multi\-AZ DB cluster deployments, see [Multi\-AZ DB cluster deployments](multi-az-db-clusters-concepts.md)


|  Category  | RDS event ID |  Description  | 
| --- | --- | --- | 
| creation | RDS\-EVENT\-0170 |  DB cluster created\.  | 
|  failover  | RDS\-EVENT\-0069 |  A failover for the DB cluster has failed\.  | 
|  failover  | RDS\-EVENT\-0070 |  A failover for the DB cluster has restarted\.  | 
|  failover  | RDS\-EVENT\-0071 |  A failover for the DB cluster has finished\.  | 
|  failover  | RDS\-EVENT\-0072 |  A failover for the DB cluster has begun within the same Availability Zone\.  | 
|  failover  | RDS\-EVENT\-0073 |  A failover for the DB cluster has begun across Availability Zones\.  | 
|  global failover  | RDS\-EVENT\-0181 |  The failover of the global database has started\. The process can be delayed because other operations are running on the DB cluster\.  | 
|  global failover  | RDS\-EVENT\-0182 |  The old primary instance in the global database isn't accepting writes\. All volumes are synchronized\.  | 
|  global failover  | RDS\-EVENT\-0183 |  A replication lag is occurring during the synchronization phase of the global database failover\.  | 
|  global failover  | RDS\-EVENT\-0184 |  The volume topology of the global database is reestablished with the new primary volume\.  | 
|  global failover  | RDS\-EVENT\-0185 |  The global database failover is finished on the primary DB cluster\. Replicas might take long to come online after the failover completes\.  | 
|  global failover  | RDS\-EVENT\-0186 |  The global database failover is canceled\.  | 
|  global failover  | RDS\-EVENT\-0187 |  The global failover to the DB cluster failed\.  | 
| notification | RDS\-EVENT\-0172 |  Renamed DB cluster from \[old DB cluster name\] to \[new DB cluster name\]\.  | 

## DB instance events<a name="USER_Events.Messages.instance"></a>

The following table shows the event category and a list of events when a DB instance is the source type\.


|  Category  | RDS event ID |  Description  | 
| --- | --- | --- | 
|  availability  | RDS\-EVENT\-0006 |  The DB instance restarted\.  | 
|  availability  | RDS\-EVENT\-0004 |  DB instance shutdown\.  | 
|  availability  | RDS\-EVENT\-0022 |  An error has occurred while restarting MySQL\.  | 
|  availability  | RDS\-EVENT\-0221 |  The DB instance has reached the storage\-full threshold, and the database has been shut down\. You can increase the allocated storage to address this issue\.  | 
|  availability  | RDS\-EVENT\-0222 |  Free storage capacity for DB instance \[instance name\] is low at \[percentage\]% of the allocated storage \(allocated storage: \[value\], free storage: \[value\]\)\. The database will be shut down to prevent corruption if free storage is lower than \[value\]\. You can increase the allocated storage to address this issue\.  | 
|  backup  | RDS\-EVENT\-0001 |  Backing up the DB instance\.  | 
|  backup  | RDS\-EVENT\-0002 |  Finished DB Instance backup\.  | 
|  backup  | RDS\-EVENT\-0086 |  RDS was unable to associate the option group with the database instance\. Confirm that the option group is supported on your DB instance class and configuration\. For more information see [Working with option groups](USER_WorkingWithOptionGroups.md)\.  | 
|  configuration change  | RDS\-EVENT\-0009 |  The DB instance has been added to a security group\.  | 
|  configuration change  | RDS\-EVENT\-0024 |  The DB instance is being converted to a Multi\-AZ DB instance\.  | 
|  configuration change  | RDS\-EVENT\-0030 |  The DB instance is being converted to a Single\-AZ DB instance\.  | 
|  configuration change  | RDS\-EVENT\-0012 |  Applying modification to database instance class\.   | 
|  configuration change  | RDS\-EVENT\-0018 |  The current storage settings for this DB instance are being changed\.  | 
|  configuration change  | RDS\-EVENT\-0011 |  Updated to use DBParameterGroup *name*\.  | 
|  configuration change  | RDS\-EVENT\-0092 |  Finished updating DB parameter group\.  | 
|  configuration change  | RDS\-EVENT\-0028 |  Automatic backups for this DB instance have been disabled\.  | 
|  configuration change  | RDS\-EVENT\-0032 |  Automatic backups for this DB instance have been enabled\.  | 
|  configuration change  | RDS\-EVENT\-0033 |  There are \[count\] users that match the master user name\. Users not tied to a specific host have been reset\.  | 
|  configuration change  | RDS\-EVENT\-0025 |  The DB instance has been converted to a Multi\-AZ DB instance\.  | 
|  configuration change  | RDS\-EVENT\-0029 |  The DB instance has been converted to a Single\-AZ DB instance\.  | 
|  configuration change  | RDS\-EVENT\-0014 |  The DB instance class for this DB instance has changed\.  | 
|  configuration change  | RDS\-EVENT\-0017 |  The storage settings for this DB instance have changed\.  | 
|  configuration change  | RDS\-EVENT\-0010 |  The DB instance has been removed from a security group\.  | 
|  configuration change  | RDS\-EVENT\-0016 |  The master password for the DB instance has been reset\.  | 
|  configuration change  | RDS\-EVENT\-0067 |  An attempt to reset the master password for the DB instance has failed\.  | 
|  configuration change  | RDS\-EVENT\-0078 |  The Enhanced Monitoring configuration has been changed\.  | 
|  configuration change  | RDS\-EVENT\-0217 |  Applying autoscaling\-initiated modification to allocated storage\.  | 
|  configuration change  | RDS\-EVENT\-0218 |  Finished applying autoscaling\-initiated modification to allocated storage\.  | 
|  creation  | RDS\-EVENT\-0005 |  DB instance created\.  | 
|  deletion  | RDS\-EVENT\-0003 |  The DB instance has been deleted\.  | 
|  failover  | RDS\-EVENT\-0013 |  A Multi\-AZ failover that resulted in the promotion of a standby instance has started\.  | 
|  failover  | RDS\-EVENT\-0015 |  A Multi\-AZ failover that resulted in the promotion of a standby instance is complete\. It may take several minutes for the DNS to transfer to the new primary DB instance\.  | 
|  failover  | RDS\-EVENT\-0034 |  Amazon RDS is not attempting a requested failover because a failover recently occurred on the DB instance\.  | 
|  failover  | RDS\-EVENT\-0049 | A Multi\-AZ failover has completed\. | 
|  failover  | RDS\-EVENT\-0050 |  A Multi\-AZ activation has started after a successful instance recovery\.  | 
|  failover  | RDS\-EVENT\-0051 |  A Multi\-AZ activation is complete\. Your database should be accessible now\.  | 
|  failover  | RDS\-EVENT\-0065 |  The instance has recovered from a partial failover\.  | 
|  failure  | RDS\-EVENT\-0031 |  The DB instance has failed due to an incompatible configuration or an underlying storage issue\. Begin a point\-in\-time\-restore for the DB instance\.  | 
|  failure  | RDS\-EVENT\-0035 |  The DB instance has invalid parameters\. For example, if the DB instance could not start because a memory\-related parameter is set too high for this instance class, the customer action would be to modify the memory parameter and reboot the DB instance\.  | 
|  failure  | RDS\-EVENT\-0036 |  The DB instance is in an incompatible network\. Some of the specified subnet IDs are invalid or do not exist\.  | 
|  failure  | RDS\-EVENT\-0058 |  Error while creating Statspack user account PERFSTAT\. Drop the account before you add the Statspack option\.  | 
|  failure  | RDS\-EVENT\-0079 |  Enhanced Monitoring cannot be enabled without the enhanced monitoring IAM role\. For information on creating the enhanced monitoring IAM role, see [To create an IAM role for Amazon RDS enhanced monitoring](USER_Monitoring.OS.Enabling.md#USER_Monitoring.OS.IAMRole)\.  | 
|  failure  | RDS\-EVENT\-0080 |  Enhanced Monitoring was disabled due to an error making the configuration change\. It is likely that the enhanced monitoring IAM role is configured incorrectly\. For information on creating the enhanced monitoring IAM role, see [To create an IAM role for Amazon RDS enhanced monitoring](USER_Monitoring.OS.Enabling.md#USER_Monitoring.OS.IAMRole)\.  | 
|  failure  | RDS\-EVENT\-0081 |  The IAM role that you use to access your Amazon S3 bucket for SQL Server native backup and restore is configured incorrectly\. For more information, see [Setting up for native backup and restore](SQLServer.Procedural.Importing.md#SQLServer.Procedural.Importing.Native.Enabling)\.  | 
|  failure  | RDS\-EVENT\-0165 |  The RDS Custom DB instance is outside the support perimeter\.  | 
|  failure  | RDS\-EVENT\-0188 |  Amazon RDS was unable to upgrade a MySQL DB instance from version 5\.7 to version 8\.0 because of incompatibilities related to the data dictionary\. The DB instance was rolled back to MySQL version 5\.7\. For more information, see [Rollback after failure to upgrade from MySQL 5\.7 to 8\.0](USER_UpgradeDBInstance.MySQL.md#USER_UpgradeDBInstance.MySQL.Major.RollbackAfterFailure)\.  | 
|  failure  | RDS\-EVENT\-0219 |  The DB instance is in an invalid state\. No actions are necessary\. Autoscaling will retry later\.  | 
|  failure  | RDS\-EVENT\-0220 |  The DB instance is in the cooling\-off period for a previous scale storage operation\. We are optimizing your instance\. This takes at least six hours\. No actions are necessary\. Autoscaling will retry after the cooling\-off period\.  | 
|  failure  | RDS\-EVENT\-0223 |  Storage autoscaling is unable to scale the storage for the reason: \[reason\]\.  | 
|  failure  | RDS\-EVENT\-0224 |  Storage autoscaling has triggered a pending scale storage task that would reach the maximum storage threshold\. Increase the maximum storage threshold\.  | 
|  failure  | RDS\-EVENT\-0237 |  The DB instance has a storage type that's currently unavailable in the Availability Zone\. Autoscaling will retry later\.  | 
| failure | RDS\-EVENT\-0254 |  The storage for your AWS account has exceeded the allowed storage quota\. Increase the quota to let the autoscaling operation proceed\.  | 
|  low storage  | RDS\-EVENT\-0007 |  The allocated storage for the DB instance has been consumed\. To resolve this issue, allocate additional storage for the DB instance\. For more information, see the [RDS FAQ](https://aws.amazon.com/rds/faqs/#20)\. You can monitor the storage space for a DB instance using the **Free Storage Space** metric\.  | 
|  low storage  | RDS\-EVENT\-0089 |  The DB instance has consumed more than 90% of its allocated storage\. You can monitor the storage space for a DB instance using the **Free Storage Space** metric\.  | 
|  low storage  | RDS\-EVENT\-0227 |  The Aurora storage subsystem is running low on space\.  | 
|  maintenance  | RDS\-EVENT\-0026 |  Offline maintenance of the DB instance is taking place\. The DB instance is currently unavailable\.  | 
|  maintenance  | RDS\-EVENT\-0027 |  Offline maintenance of the DB instance is complete\. The DB instance is now available\.  | 
|  maintenance  | RDS\-EVENT\-0047 |  The DB instance was patched\.  | 
|  maintenance  | RDS\-EVENT\-0155 |  The DB instance has a DB engine minor version upgrade required\.  | 
|  maintenance  | RDS\-EVENT\-0264 |  The pre\-check started for the DB engine version upgrade\.  | 
|  maintenance  | RDS\-EVENT\-0265 |  The pre\-check finished for the DB engine version upgrade\.  | 
|  maintenance  | RDS\-EVENT\-0266 |  The downtime started for the DB instance\.  | 
|  maintenance  | RDS\-EVENT\-0267 |  The engine version upgrade started\.  | 
|  maintenance  | RDS\-EVENT\-0268 |  The engine version upgrade finished\. | 
|  maintenance  | RDS\-EVENT\-0269 |  The post\-upgrade tasks are in progress\. | 
|  maintenance  | RDS\-EVENT\-0270 |  The DB engine version upgrade failed\. The engine version upgrade rollback succeeded\. | 
|  maintenance, notification  | RDS\-EVENT\-0191 |  An Oracle time zone file update is available\. If you update your Oracle engine, Amazon RDS generates this event if you haven't chosen a time zone file upgrade and the database doesn’t use the latest DST time zone file available on the instance\.  For more information, see [Oracle time zone file autoupgrade](Appendix.Oracle.Options.Timezone-file-autoupgrade.md)\.  | 
|  maintenance, notification  | RDS\-EVENT\-0192 |  The upgrade of your Oracle time zone file has begun\.  For more information, see [Oracle time zone file autoupgrade](Appendix.Oracle.Options.Timezone-file-autoupgrade.md)\.  | 
|  maintenance, notification  | RDS\-EVENT\-0193 |  Your Oracle DB instance is using latest time zone file version, and either of the following is true: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_Events.Messages.html) For more information, see [Oracle time zone file autoupgrade](Appendix.Oracle.Options.Timezone-file-autoupgrade.md)\.  | 
|  maintenance, notification  | RDS\-EVENT\-0194 |  The upgrade of your Oracle time zone file has completed\.  For more information, see [Oracle time zone file autoupgrade](Appendix.Oracle.Options.Timezone-file-autoupgrade.md)\.  | 
|  maintenance, failure  | RDS\-EVENT\-0195 |  The upgrade of the time zone file failed\. For more information, see [Oracle time zone file autoupgrade](Appendix.Oracle.Options.Timezone-file-autoupgrade.md)\.  | 
|  notification  | RDS\-EVENT\-0044 | Operator\-issued notification\. For more information, see the event message\. | 
|  notification  | RDS\-EVENT\-0048 | Patching of the DB instance has been delayed\. | 
|  notification  | RDS\-EVENT\-0054 | The MySQL storage engine you are using is not InnoDB, which is the recommended MySQL storage engine for Amazon RDS\. For information about MySQL storage engines, see [Supported storage engines for RDS for MySQL](MySQL.Concepts.FeatureSupport.md#MySQL.Concepts.Storage)\. | 
|  notification  | RDS\-EVENT\-0055 |  The number of tables you have for your DB instance exceeds the recommended best practices for Amazon RDS\. Reduce the number of tables on your DB instance\. For information about recommended best practices, see [Amazon RDS basic operational guidelines](CHAP_BestPractices.md#CHAP_BestPractices.DiskPerformance)\.  | 
|  notification  | RDS\-EVENT\-0056 |  The number of databases you have for your DB instance exceeds the recommended best practices for Amazon RDS\. Reduce the number of databases on your DB instance\.   For information about recommended best practices, see [Amazon RDS basic operational guidelines](CHAP_BestPractices.md#CHAP_BestPractices.DiskPerformance)\.  | 
|  notification  | RDS\-EVENT\-0064 | The TDE key has been rotated\. For information about recommended best practices, see [Amazon RDS basic operational guidelines](CHAP_BestPractices.md#CHAP_BestPractices.DiskPerformance)\.  | 
|  notification  | RDS\-EVENT\-0084 |  You attempted to convert a DB instance to Multi\-AZ, but it contains in\-memory file groups that are not supported for Multi\-AZ\. For more information, see [Multi\-AZ deployments for Amazon RDS for Microsoft SQL Server](USER_SQLServerMultiAZ.md)\.   | 
|  notification  | RDS\-EVENT\-0087 |  The DB instance has been stopped\.   | 
|  notification  | RDS\-EVENT\-0088 |  The DB instance has been started\.  | 
|  notification  | RDS\-EVENT\-0154 |  The DB instance is being started due to it exceeding the maximum allowed time being stopped\.  | 
|  notification  | RDS\-EVENT\-0157 |  RDS can't modify the DB instance class because the target instance class can't support the number of databases that exist on the source DB instance\. The error message appears as: "The instance has *N* databases, but after conversion it would only support *N*"\. For more information, see [Limitations for Microsoft SQL Server DB instances](CHAP_SQLServer.md#SQLServer.Concepts.General.FeatureSupport.Limits)\.  | 
|  notification  | RDS\-EVENT\-0158 |  DB instance is in a state that can't be upgraded\.  | 
|  notification  | RDS\-EVENT\-0167 |  The RDS Custom support perimeter configuration has changed\.  | 
|  notification  | RDS\-EVENT\-0189 |  The gp2 burst balance credits for the RDS database instance are low\. To resolve this issue, reduce IOPS usage or modify your storage settings to enable higher performance\. For more information, see [I/O credits and burst performance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-volume-types.html#EBSVolumeTypes_gp2) in the *Amazon Elastic Compute Cloud User Guide*\.  | 
|  notification  | RDS\-EVENT\-0225 |  Storage size \[value\] GB is approaching the maximum storage threshold \[value\] GB\. This event is invoked when storage reaches 80% of the maximum storage threshold\. To avoid the event, increase the maximum storage threshold\.  | 
|  notification  | RDS\-EVENT\-0231 |  Your DB instance's storage modification encountered an internal error\. The modification request is pending and will be retried later\.  | 
|  notification  | RDS\-EVENT\-0253 |  The database is using the doublewrite buffer\. RDS Optimized Writes is incompatible with the instance storage configuration\. For more information, see [Improving write performance with Amazon RDS Optimized Writes](rds-optimized-writes.md)\.  | 
|  read replica  | RDS\-EVENT\-0045 |  An error has occurred in the read replication process\. For more information, see the event message\.  In addition, see the troubleshooting section for read replicas for your DB engine\.  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_Events.Messages.html)   | 
|  read replica  | RDS\-EVENT\-0046 | The read replica has resumed replication\. This message appears when you first create a read replica, or as a monitoring message confirming that replication is functioning properly\. If this message follows an RDS\-EVENT\-0045 notification, then replication has resumed following an error or after replication was stopped\. | 
|  read replica  | RDS\-EVENT\-0057 |  Replication on the read replica was terminated\.  | 
|  read replica  | RDS\-EVENT\-0062 |  Replication on the read replica was manually stopped\.  | 
|  read replica  | RDS\-EVENT\-0063 |  Replication on the read replica was reset\.  | 
|  read replica  | RDS\-EVENT\-0202 |  Read replica creation failed\.  | 
|  recovery  | RDS\-EVENT\-0020 |  Recovery of the DB instance has started\. Recovery time will vary with the amount of data to be recovered\.  | 
|  recovery  | RDS\-EVENT\-0021 |  Recovery of the DB instance is complete\.  | 
|  recovery  | RDS\-EVENT\-0023 |  A manual backup has been requested but Amazon RDS is currently in the process of creating a DB snapshot\. Submit the request again after Amazon RDS has completed the DB snapshot\.  | 
|  recovery  | RDS\-EVENT\-0052 |  Recovery of the Multi\-AZ instance has started\. Recovery time will vary with the amount of data to be recovered\.  | 
|  recovery  | RDS\-EVENT\-0053 |  Recovery of the Multi\-AZ instance is complete\.  | 
|  recovery  | RDS\-EVENT\-0066 |  The SQL Server DB instance is re\-establishing its mirror\. Performance will be degraded until the mirror is reestablished\. A database was found with non\-FULL recovery model\. The recovery model was changed back to FULL and mirroring recovery was started\. \(<dbname>: <recovery model found>\[,\.\.\.\]\)"  | 
|  recovery  | RDS\-EVENT\-0166 |  The RDS Custom DB instance is inside the support perimeter\.  | 
|  restoration  | RDS\-EVENT\-0019 |  The DB instance has been restored from a point\-in\-time backup\.  | 
|  restoration  | RDS\-EVENT\-0043 |  Restored from snapshot \[snapshot\_name\]\. The DB instance has been restored from a DB snapshot\.  | 
|  security  | RDS\-EVENT\-0068 |  RDS is decrypting the CloudHSM partition password to make updates to the instance\. For more information see [Oracle Database Transparent Data Encryption \(TDE\) with AWS CloudHSM](https://docs.aws.amazon.com/cloudhsm/latest/userguide/oracle-tde.html) in the *AWS CloudHSM User Guide*\.  | 
|  security patching  | RDS\-EVENT\-0230 |  The operating system update is available for your DB instance\. For information about applying updates, see [Maintaining a DB instance](USER_UpgradeDBInstance.Maintenance.md)\.  | 

## DB parameter group events<a name="USER_Events.Messages.parameter-group"></a>

The following table shows the event category and a list of events when a DB parameter group is the source type\.


|  Category  | RDS event ID |  Description  | 
| --- | --- | --- | 
|  configuration change  | RDS\-EVENT\-0037 |  Updated parameter *name* to *value* with apply method *method*\.   | 

## DB security group events<a name="USER_Events.Messages.security-group"></a>

The following table shows the event category and a list of events when a DB security group is the source type\.

**Note**  
DB security groups are resources for EC2\-Classic\. EC2\-Classic was retired on August 15, 2022\. If you haven't migrated from EC2\-Classic to a VPC, we recommend that you migrate as soon as possible\. For more information, see [Migrate from EC2\-Classic to a VPC](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/vpc-migrate.html) in the *Amazon EC2 User Guide* and the blog [ EC2\-Classic Networking is Retiring – Here’s How to Prepare](http://aws.amazon.com/blogs/aws/ec2-classic-is-retiring-heres-how-to-prepare/)\.


|  Category  | RDS event ID |  Description  | 
| --- | --- | --- | 
|  configuration change  | RDS\-EVENT\-0038 |  The security group has been modified\.  | 
|  failure  | RDS\-EVENT\-0039 |  The security group owned by \[user\] does not exist\. Authorization for the security group has been revoked\.  | 

## DB snapshot events<a name="USER_Events.Messages.snapshot"></a>

The following table shows the event category and a list of events when a DB snapshot is the source type\.


|  Category  | RDS event ID |  Description  | 
| --- | --- | --- | 
|  creation  | RDS\-EVENT\-0040 |  A manual DB snapshot is being created\.  | 
|  creation  | RDS\-EVENT\-0042 |  A manual DB snapshot has been created\.  | 
|  creation  | RDS\-EVENT\-0090 | An automated DB snapshot is being created\. | 
|  creation  | RDS\-EVENT\-0091 | An automated DB snapshot has been created\. | 
|  deletion  | RDS\-EVENT\-0041 |  A DB snapshot has been deleted\.  | 
|  notification  | RDS\-EVENT\-0059 |  Started copy of snapshot \[DB snapshot name\] from region \[region name\]\.  This is a cross\-Region snapshot copy\.   | 
|  notification  | RDS\-EVENT\-0060 |  Finished copy of snapshot \[DB snapshot name\] from region \[region name\] in \[time\] minutes\.  This is a cross\-Region snapshot copy\.   | 
|  notification  | RDS\-EVENT\-0061 |  Canceled snapshot copy request of \[DB snapshot name\] from region \[region name\]\.  This is a cross\-Region snapshot copy\.   | 
|  notification  | RDS\-EVENT\-0159 |  The DB snapshot export task failed\.  | 
|  notification  | RDS\-EVENT\-0160 |  The DB snapshot export task was canceled\.  | 
|  notification  | RDS\-EVENT\-0161 |  The DB snapshot export task completed\.  | 
|  notification  | RDS\-EVENT\-0196 |  Started copy of snapshot \[DB snapshot name\] in region \[region name\]\.  This is a local snapshot copy\.   | 
|  notification  | RDS\-EVENT\-0197 |  Finished copy of snapshot \[DB snapshot name\] in region \[region name\]\.  This is a local snapshot copy\.   | 
|  notification  | RDS\-EVENT\-0190 |  Canceled snapshot copy request of \[DB snapshot name\] in region \[region name\]\.  This is a local snapshot copy\.   | 
|  restoration  | RDS\-EVENT\-0043 |  Restored from snapshot \[snapshot\_name\]\. A DB instance is being restored from a DB snapshot\.  | 

## DB cluster snapshot events<a name="USER_Events.Messages.cluster-snapshot"></a>

The following table shows the event category and a list of events when a DB cluster snapshot is the source type\.


|  Category  | RDS event ID |  Description  | 
| --- | --- | --- | 
|  backup  | RDS\-EVENT\-0074 |  Creation of a manual DB cluster snapshot has started\.  | 
|  backup  | RDS\-EVENT\-0075 |  A manual DB cluster snapshot has been created\.  | 
| backup | RDS\-EVENT\-0168 |  Creating automated cluster snapshot\.  | 
| backup | RDS\-EVENT\-0169 |  Automated cluster snapshot created\.  | 

## RDS Proxy events<a name="USER_Events.Messages.rds-proxy"></a>

The following table shows the event category and a list of events when an RDS Proxy is the source type\.


|  Category  | RDS event ID |  Description  | 
| --- | --- | --- | 
| configuration change | RDS\-EVENT\-0204 |  RDS modified the DB proxy \(RDS Proxy\)\.  | 
|  configuration change  | RDS\-EVENT\-0207 |  RDS modified the endpoint of the DB proxy \(RDS Proxy\)\.    | 
|  configuration change  | RDS\-EVENT\-0213 | RDS detected the addition of the DB instance and automatically added it to the target group of the DB proxy \(RDS Proxy\)\.  | 
|  configuration change  | RDS\-EVENT\-0214 |  RDS detected the deletion of the DB instance and automatically removed it from the target group of the DB proxy \(RDS Proxy\)\.  | 
|  configuration change  | RDS\-EVENT\-0215 |  RDS detected the deletion of the DB cluster and automatically removed it from the target group of the DB proxy \(RDS Proxy\)\.  | 
|  creation  | RDS\-EVENT\-0203 |  RDS created the DB proxy \(RDS Proxy\)\.  | 
|  creation  | RDS\-EVENT\-0206 |  RDS created the endpoint for the DB proxy \(RDS Proxy\)\.  | 
| deletion | RDS\-EVENT\-0205 |  RDS deleted the DB proxy \(RDS Proxy\)\.  | 
|  deletion  | RDS\-EVENT\-0208 |  RDS deleted the endpoint of DB proxy \(RDS Proxy\)\.  | 
|  failure  | RDS\-EVENT\-0243 |  RDS couldn't provision capacity for the proxy because there aren't enough IP addresses available in your subnets\. To fix the issue, make sure that your subnets have the minimum number of unused IP addresses\. To determine the recommended number for your instance class, see [Planning for IP address capacity](rds-proxy-setup.md#rds-proxy-network-prereqs.plan-ip-address)\.  | 

## Blue/green deployment events<a name="USER_Events.Messages.BlueGreenDeployments"></a>

The following table shows the event category and a list of events when a blue/green deployment is the source type\.

For more information about blue/green deployments, see [Using Amazon RDS Blue/Green Deployments for database updates](blue-green-deployments.md)\.


|  Category  | Amazon RDS event ID |  Description  | 
| --- | --- | --- | 
|  creation  | RDS\-EVENT\-0244 |  Blue/green deployment tasks completed\. You can make more modifications to the green environment databases or switch over the deployment\.  | 
|  deletion  | RDS\-EVENT\-0246 |  Blue/green deployment deleted\.  | 
|  failure  | RDS\-EVENT\-0245 |  Creation of blue/green deployment failed\.  | 
|  failure  | RDS\-EVENT\-0249 |  Switchover canceled on blue/green deployment\.  | 
|  failure  | RDS\-EVENT\-0252 |  Switchover from primary source to target canceled\.  | 
|  failure  | RDS\-EVENT\-0261 |  Switchover from source to target canceled\.  | 
|  notification  | RDS\-EVENT\-0247 |  Switchover started on blue/green deployment\.  | 
|  notification  | RDS\-EVENT\-0248 |  Switchover completed on blue/green deployment\.  | 
|  notification  | RDS\-EVENT\-0250 |  Switchover from primary source to target started\.  | 
|  notification  | RDS\-EVENT\-0251 |  Switchover from primary source to target completed and databases renamed\.  | 
|  notification  | RDS\-EVENT\-0259 |  Switchover from source to target started\.  | 
|  notification  | RDS\-EVENT\-0260 |  Switchover from source to target completed\.  | 

## Custom engine version events<a name="USER_Events.Messages.CEV"></a>

The following table shows the event category and a list of events when a custom engine version is the source type\.


|  Category  | Amazon RDS event ID |  Description  | 
| --- | --- | --- | 
|  failure  | RDS\-EVENT\-0198 |  Creation failed for custom engine version\. The message includes details about the failure, such as missing files\.  | 

## Message attributes<a name="USER_Events.Messages.Attributes"></a>

The following table shows the message attribute for RDS events\.


| Amazon RDS event attribute |  Description  | 
| --- | --- | 
| Event ID |  Identifier for the RDS event message\. For example, RDS\-EVENT\-0006\.  | 
| Resource |  The ARN identifier for the resource emitting the event\. For example, `arn:aws:rds:ap-southeast-2:123456789012:db:database-1`\.  | 