# Modifying a DB Instance Running the Oracle Database Engine<a name="USER_ModifyInstance.Oracle"></a>

You can change the settings of a DB instance to accomplish tasks such as adding additional storage or changing the DB instance class\. In this topic, you learn how to modify an Amazon RDS Oracle DB instance, and about the settings for Oracle instances\. We recommend that you test any changes on a test instance before modifying a production instance, so that you fully understand the impact of each change\. This practice is especially important when upgrading database versions\. 

After you modify your DB instance settings, you can apply the changes immediately, or apply them during the next maintenance window for the DB instance\. Some modifications cause an interruption by restarting the DB instance\. 

In addition to modifying Oracle instances as described directly following, you can also change settings for sqlnet\.ora parameters for an Oracle DB instance as described in [Modifying Oracle sqlnet\.ora Parameters](#USER_ModifyInstance.Oracle.sqlnet), at the end of this topic\.

## Console<a name="USER_ModifyInstance.Oracle.Console"></a>

**To modify an Oracle DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**, and then choose the DB instance that you want to modify\. 

1. Choose **Modify**\. The **Modify DB Instance** page appears\.

1. Change any of the settings that you want\. For information about each setting, see [Settings for Oracle DB Instances](#USER_ModifyInstance.Oracle.Settings)\. 

1. When all the changes are as you want them, choose **Continue** and check the summary of modifications\. 

1. To apply the changes immediately, choose **Apply immediately**\. Choosing this option can cause an outage in some cases\. For more information, see [Using the Apply Immediately Parameter](Overview.DBInstance.Modifying.md#USER_ModifyInstance.ApplyImmediately)\. 

1. On the confirmation page, review your changes\. If they are correct, choose **Modify DB Instance** to save your changes\. 

   Alternatively, choose **Back** to edit your changes, or choose **Cancel** to cancel your changes\. 

## AWS CLI<a name="USER_ModifyInstance.Oracle.CLI"></a>

To modify an Oracle DB instance by using the AWS CLI, call the [modify\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) command\. Specify the DB instance identifier, and the parameters for the settings that you want to modify\. For information about each parameter, see [Settings for Oracle DB Instances](#USER_ModifyInstance.Oracle.Settings)\. 

**Example**  
The following code modifies `mydbinstance` by setting the backup retention period to 1 week \(7 days\)\. The code enables automatic minor version upgrades by using `--auto-minor-version-upgrade`\. To disable automatic minor version upgrades, use `--no-auto-minor-version-upgrade`\. The changes are applied during the next maintenance window by using `--no-apply-immediately`\. Use `--apply-immediately` to apply the changes immediately\. For more information, see [Using the Apply Immediately Parameter](Overview.DBInstance.Modifying.md#USER_ModifyInstance.ApplyImmediately)\.   
For Linux, OS X, or Unix:  

```
aws rds modify-db-instance \
    --db-instance-identifier mydbinstance \
    --backup-retention-period 7 \
    --auto-minor-version-upgrade \
    --no-apply-immediately
```
For Windows:  

```
aws rds modify-db-instance ^
    --db-instance-identifier mydbinstance ^
    --backup-retention-period 7 ^
    --auto-minor-version-upgrade ^
    --no-apply-immediately
```

## RDS API<a name="USER_ModifyInstance.Oracle.API"></a>

To modify an Oracle DB instance by using the Amazon RDS API, call the [ModifyDBInstance](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html) operation\. Specify the DB instance identifier, and the parameters for the settings that you want to modify\. For information about each parameter, see [Settings for Oracle DB Instances](#USER_ModifyInstance.Oracle.Settings)\. 

**Example**  
The following code modifies `mydbinstance` by setting the backup retention period to 1 week \(7 days\) and enabling automatic minor version upgrades\. These changes are applied during the next maintenance window\.   

```
 1. https://rds.amazonaws.com/
 2.     ?Action=ModifyDBInstance
 3.     &ApplyImmediately=false
 4.     &AutoMinorVersionUpgrade=true
 5.     &BackupRetentionPeriod=7
 6.     &DBInstanceIdentifier=mydbinstance
 7.     &SignatureMethod=HmacSHA256
 8.     &SignatureVersion=4
 9.     &Version=2014-10-31
10.     &X-Amz-Algorithm=AWS4-HMAC-SHA256
11.     &X-Amz-Credential=AKIADQKE4SARGYLE/20131016/us-west-1/rds/aws4_request
12.     &X-Amz-Date=20131016T233051Z
13.     &X-Amz-SignedHeaders=content-type;host;user-agent;x-amz-content-sha256;x-amz-date
14.     &X-Amz-Signature=087a8eb41cb1ab0fc9ec1575f23e73757ffc6a1e42d7d2b30b9cc0be988cff97
```

## Settings for Oracle DB Instances<a name="USER_ModifyInstance.Oracle.Settings"></a>

The following table contains details about which settings you can modify, which settings you can't modify, when the changes can be applied, and whether the changes cause downtime for the DB instance\. 


****  

| Setting | Setting Description | When the Change Occurs | Downtime Notes | 
| --- | --- | --- | --- | 
|  Allocated storage  |  The storage, in gigabytes, that you want to allocate for your DB instance\. You can only increase the allocated storage, you can't reduce the allocated storage\.  You can't modify allocated storage if the DB instance status is **storage\-optimization** or if the allocated storage for the DB instance has been modified in the last six hours\. The maximum storage allowed depends on the storage type\. For more information, see [Amazon RDS DB Instance Storage](CHAP_Storage.md)\.   |  If **Apply immediately** is set to true, the change occurs immediately\.  If **Apply immediately** is set to false, the change occurs during the next maintenance window\.   |  No downtime\. Performance might be degraded during the change\.   | 
|  Auto minor version upgrade  |  **Enable auto minor version upgrade** to enable your DB instance to receive preferred minor DB engine version upgrades automatically when they become available\. Amazon RDS performs automatic minor version upgrades in the maintenance window\.  | – | – | 
|  Backup retention period  |  The number of days that automatic backups are retained\. To disable automatic backups, set the backup retention period to 0\.  For more information, see [Working With Backups](USER_WorkingWithAutomatedBackups.md)\.   |  If **Apply immediately** is set to true, the change occurs immediately\.  If **Apply immediately** is set to false and you change the setting from a nonzero value to another nonzero value, the change is applied asynchronously, as soon as possible\. Otherwise, the change occurs during the next maintenance window\.   |  An outage occurs if you change from 0 to a nonzero value, or from a nonzero value to 0\.   | 
|  Backup window  |  The time range during which automated backups of your databases occur\. The backup window is a start time in Universal Coordinated Time \(UTC\), and a duration in hours\.  For more information, see [Working With Backups](USER_WorkingWithAutomatedBackups.md)\.   |  The change is applied asynchronously, as soon as possible\.   | – | 
|  Certificate authority  |  The certificate that you want to use\.   |  If **Apply Immediately** is set to true, the change occurs immediately\.  If **Apply Immediately** is set to false, the change occurs during the next maintenance window\.  | An outage occurs during this change\. | 
|  Copy tags to snapshots  |  If you have any DB instance tags, this option copies them when you create a DB snapshot\.  For more information, see [Tagging Amazon RDS Resources](USER_Tagging.md)\.   |  The change occurs immediately\. This setting ignores the **Apply immediately** setting\.   | – | 
|  Database authentication  |  The database authentication option you want to use\. Choose **Password authentication** to authenticate database users with database passwords only\. Choose **Password and Kerberos authentication** to authenticate database users with database passwords and Kerberos authentication through an AWS Managed Microsoft AD created with AWS Directory Service\. Next, choose the directory or choose **Create a new Directory**\. For more information, see [Using Kerberos Authentication with Amazon RDS for Oracle](oracle-kerberos.md)\.  |  If **Apply immediately** is set to true, the change occurs immediately\.  If **Apply immediately** is set to false, the change occurs during the next maintenance window\.   | When you enable **Password and Kerberos authentication**, a brief outage occurs\. | 
|  Database port  |  The port that you want to use to access the database\.  The port value must not match any of the port values specified for options in the option group for the DB instance\.   |  The change occurs immediately\. This setting ignores the **Apply immediately** setting\.   |  The DB instance is rebooted immediately\.  | 
|  DB engine version  |  The version of the Oracle database engine that you want to use\. Before you upgrade your production DB instances, we recommend that you test the upgrade process on a test instance to verify its duration and to validate your applications\.  We do not recommend upgrading micro DB instances because they have limited CPU resources and the upgrade process may take hours to complete\. An alternative to upgrading micro DB instances with small storage \(10\-20 GiB\) is to copy your data using Data Pump, where we also recommend testing before migrating your production instances\.  For more information, see [Upgrading the Oracle DB Engine](USER_UpgradeDBInstance.Oracle.md)\.   |  If **Apply immediately** is set to true, the change occurs immediately\.  If **Apply immediately** is set to false, the change occurs during the next maintenance window\.   |  An outage occurs during this change\.  | 
|  DB instance class  |  The DB instance class that you want to use\.  For more information, see [Choosing the DB Instance Class](Concepts.DBInstanceClass.md) and [DB Instance Class Support for Oracle](CHAP_Oracle.md#Oracle.Concepts.InstanceClasses)\.   |  If **Apply immediately** is set to true, the change occurs immediately\.  If **Apply immediately** is set to false, the change occurs during the next maintenance window\.   |  An outage occurs during this change\.  | 
|  DB instance identifier  |  The DB instance identifier\. This value is stored as a lowercase string\.  For more information about the effects of renaming a DB instance, see [Renaming a DB Instance](USER_RenameInstance.md)\.   |  If **Apply immediately** is set to true, the change occurs immediately\.  If **Apply immediately** is set to false, the change occurs during the next maintenance window\.   |  An outage occurs during this change\. The DB instance is rebooted\.   | 
|  DB parameter group  |  The parameter group that you want associated with the DB instance\.  For more information, see [Working with DB Parameter Groups](USER_WorkingWithParamGroups.md) and [Modifying Oracle sqlnet\.ora Parameters](#USER_ModifyInstance.Oracle.sqlnet)\.   |  The parameter group change occurs immediately\.   |  An outage doesn't occur during this change\. When you change the parameter group, changes to some parameters are applied to the DB instance immediately without a reboot\. Changes to other parameters are applied only after the DB instance is rebooted\. For more information, see [Rebooting a DB Instance](USER_RebootInstance.md)\.   | 
| Deletion protection | **Enable deletion protection** to prevent your DB instance from being deleted\. For more information, see [Deleting a DB Instance](USER_DeleteInstance.md)\. | – | – | 
|  Enhanced Monitoring  |  **Enable enhanced monitoring** to enable gathering metrics in real time for the operating system that your DB instance runs on\.   For more information, see [Enhanced Monitoring](USER_Monitoring.OS.md)\.   |  –  |  –  | 
|  License model  |  **license\-included** to use the general license agreement for Oracle\. **bring\-your\-own\-license** to use your existing Oracle license\.  For more information, see [Oracle Licensing](CHAP_Oracle.md#Oracle.Concepts.Licensing)\.   |  If **Apply immediately** is set to true, the change occurs immediately\.  If **Apply immediately** is set to false, the change occurs during the next maintenance window\.   |  An outage occurs during this change\.  | 
|  Log exports  |  The types of Oracle database log files to publish to Amazon CloudWatch Logs\.  For more information, see [Oracle Database Log Files](USER_LogAccess.Concepts.Oracle.md)\.   |  The change occurs immediately\. This setting ignores the **Apply immediately** setting\.   |  –  | 
|  Maintenance Window  |  The time range during which system maintenance occurs\. System maintenance includes upgrades, if applicable\. The maintenance window is a start time in Universal Coordinated Time \(UTC\), and a duration in hours\.  If you set the window to the current time, there must be at least 30 minutes between the current time and end of the window to ensure any pending changes are applied\.  For more information, see [The Amazon RDS Maintenance Window](USER_UpgradeDBInstance.Maintenance.md#Concepts.DBMaintenance)\.   |  The change occurs immediately\. This setting ignores the **Apply immediately** setting\.   |  If there are one or more pending actions that cause an outage, and the maintenance window is changed to include the current time, then those pending actions are applied immediately, and an outage occurs\.   | 
|  Multi\-AZ deployment  |  **Yes** to deploy your DB instance in multiple Availability Zones\. Otherwise, **No**\. For more information, see [Regions and Availability Zones](Concepts.RegionsAndAvailabilityZones.md)\.   |  If **Apply immediately** is set to true, the change occurs immediately\.  If **Apply immediately** is set to false, the change occurs during the next maintenance window\.   |  –  | 
|  New master password  |  The password for your master user\. The password must contain from 8 to 30 alphanumeric characters\.   |  The change is applied asynchronously, as soon as possible\. This setting ignores the **Apply immediately** setting\.   |  –  | 
|  Option group  |  The option group that you want associated with the DB instance\.  For more information, see [Working with Option Groups](USER_WorkingWithOptionGroups.md)\.   |  If **Apply immediately** is set to true, the change occurs immediately\.  If **Apply immediately** is set to false, the change occurs during the next maintenance window\.   |  When you add the APEX options to an existing DB instance, a brief outage occurs while your DB instance is automatically restarted\.  When you add the OEM option to an existing DB instance, the change can cause a brief \(sub\-second\) period during which new connections are rejected\. Existing connections are not interrupted\.   | 
| Performance Insights |  **Enable Performance Insights** to monitor your DB instance load so that you can analyze and troubleshoot your database performance\. Choose a retention period to determine how much rolling data history to keep\. The default of seven days is in the free tier\. Long\-term retention \(two years\) is priced per vCPU per month\. You can't change the master key after the database is created\. For more information, see [Using Amazon RDS Performance Insights](USER_PerfInsights.md)\.  |  The change occurs immediately\. This setting ignores the **Apply immediately** setting\.   | – | 
|  Public accessibility  |  **Yes** to give the DB instance a public IP address, meaning that it's accessible outside the VPC\. To be publicly accessible, the DB instance also has to be in a public subnet in the VPC\. **No** to make the DB instance accessible only from inside the VPC\. For more information, see [Hiding a DB Instance in a VPC from the Internet](USER_VPC.WorkingWithRDSInstanceinaVPC.md#USER_VPC.Hiding)\.   |  The change occurs immediately\. This setting ignores the **Apply immediately** setting\.   |  –  | 
|  Security group  |  The security group that you want associated with the DB instance\.  For more information, see [Working with DB Security Groups \(EC2\-Classic Platform\)](USER_WorkingWithSecurityGroups.md)\.   |  The change is applied asynchronously, as soon as possible\. This setting ignores the **Apply immediately** setting\.   |  –  | 
| Storage autoscaling |  **Enable storage autoscaling** to enable Amazon RDS to automatically increase storage when needed to avoid having your DB instance run out of storage space\. Use **Maximum storage threshold** to set the upper limit for Amazon RDS to automatically increase storage for your DB instance\. The default is 1,000 GiB\. For more information, see [Managing Capacity Automatically with Amazon RDS Storage Autoscaling](USER_PIOPS.StorageTypes.md#USER_PIOPS.Autoscaling)\.   |  The change occurs immediately\. This setting ignores the **Apply immediately** setting\.  |  –  | 
|  Storage type  |  The storage type that you want to use\.  For more information, see [Amazon RDS Storage Types](CHAP_Storage.md#Concepts.Storage)\.   |  If **Apply immediately** is set to true, the change occurs immediately\.  If **Apply immediately** is set to false, the change occurs during the next maintenance window\.   |  The following changes all result in a brief outage while the process starts\. After that, you can use your database normally while the change takes place\.  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_ModifyInstance.Oracle.html)  | 
|  Subnet group  |  The subnet group for the DB instance\. You can use this setting to move your DB instance to a different VPC\.  If your DB instance isn't in a VPC, you can use this setting to move your DB instance into a VPC\.  For more information, see [Moving a DB Instance Not in a VPC into a VPC](USER_VPC.md#USER_VPC.Non-VPC2VPC)\.   |  If **Apply Immediately** is set to true, the change occurs immediately\.  If **Apply Immediately** is set to false, the change occurs during the next maintenance window\.   |  An outage occurs during this change\. The DB instance is rebooted\.   | 

## Modifying Oracle sqlnet\.ora Parameters<a name="USER_ModifyInstance.Oracle.sqlnet"></a>

The sqlnet\.ora file includes parameters that configure Oracle Net features on Oracle database servers and clients\. Using the parameters in the sqlnet\.ora file, you can modify properties for connections in and out of the database\. 

For more information about why you might set sqlnet\.ora parameters, see [Configuring Profile Parameters](https://docs.oracle.com/database/121/NETAG/profile.htm#NETAG009) in the Oracle documentation\.

### Setting sqlnet\.ora Parameters<a name="USER_ModifyInstance.Oracle.sqlnet.Setting"></a>

Amazon RDS Oracle parameter groups include a subset of sqlnet\.ora parameters\. You set them in the same way that you set other Oracle parameters\. The `sqlnetora.` prefix identifies which parameters are sqlnet\.ora parameters\. For example, in an Oracle parameter group in Amazon RDS, the `default_sdu_size` sqlnet\.ora parameter is `sqlnetora.default_sdu_size`\.

For information about managing parameter groups and setting parameter values, see [Working with DB Parameter Groups](USER_WorkingWithParamGroups.md)\.

### Supported sqlnet\.ora Parameters<a name="USER_ModifyInstance.Oracle.sqlnet.Supported"></a>

Amazon RDS supports the following sqlnet\.ora parameters\. Changes to dynamic sqlnet\.ora parameters take effect immediately\.


****  

| Parameter | Valid Values | Static/Dynamic | Description | 
| --- | --- | --- | --- | 
|  `sqlnetora.default_sdu_size`  |  Oracle 11g – `512` to `65535`  Oracle 12c – `512` to `2097152`   |  Dynamic  |  The session data unit \(SDU\) size, in bytes\.  The SDU is the amount of data that is put in a buffer and sent across the network at one time\.  | 
|  `sqlnetora.diag_adr_enabled`  |  `ON`, `OFF`   |  Dynamic  |  A value that enables or disables Automatic Diagnostic Repository \(ADR\) tracing\.  `ON` specifies that ADR file tracing is used\. `OFF` specifies that non\-ADR file tracing is used\.  | 
|  `sqlnetora.recv_buf_size`  |  `8192` to `268435456`   |  Dynamic  |  The buffer space limit for receive operations of sessions, supported by the TCP/IP, TCP/IP with SSL, and SDP protocols\.   | 
|  `sqlnetora.send_buf_size`  |  `8192` to `268435456`   |  Dynamic  |  The buffer space limit for send operations of sessions, supported by the TCP/IP, TCP/IP with SSL, and SDP protocols\.   | 
|  `sqlnetora.sqlnet.allowed_logon_version_client`  |  `8`, `10`, `11`, `12`   |  Dynamic  |  Minimum authentication protocol version allowed for clients, and servers acting as clients, to establish a connection to Oracle DB instances\.  | 
|  `sqlnetora.sqlnet.allowed_logon_version_server`  |  `8`, `9`, `10`, `11`, `12`, `12a`   |  Dynamic  |  Minimum authentication protocol version allowed to establish a connection to Oracle DB instances\.  | 
|  `sqlnetora.sqlnet.expire_time`  |  `0` to `1440`   |  Dynamic  |  Time interval, in minutes, to send a check to verify that client\-server connections are active\.   | 
|  `sqlnetora.sqlnet.inbound_connect_timeout`  |  `0` or `10` to `7200`   |  Dynamic  |  Time, in seconds, for a client to connect with the database server and provide the necessary authentication information\.   | 
|  `sqlnetora.sqlnet.outbound_connect_timeout`  |  `0` or `10` to `7200`   |  Dynamic  |  Time, in seconds, for a client to establish an Oracle Net connection to the DB instance\.   | 
|  `sqlnetora.sqlnet.recv_timeout`  |  `0` or `10` to `7200`   |  Dynamic  |  Time, in seconds, for a database server to wait for client data after establishing a connection\.   | 
|  `sqlnetora.sqlnet.send_timeout`  |  `0` or `10` to `7200`   |  Dynamic  |  Time, in seconds, for a database server to complete a send operation to clients after establishing a connection\.   | 
|  `sqlnetora.tcp.connect_timeout`  |  `0` or `10` to `7200`   |  Dynamic  |  Time, in seconds, for a client to establish a TCP connection to the database server\.   | 
|  `sqlnetora.trace_level_server`  |  `0`, `4`, `10`, `16`, `OFF`, `USER`, `ADMIN`, `SUPPORT`   |  Dynamic  |  For non\-ADR tracing, turns server tracing on at a specified level or turns it off\.   | 

The default value for each supported sqlnet\.ora parameter is the Oracle default for the release\. For information about default values for Oracle 12c, see [Parameters for the sqlnet\.ora File](https://docs.oracle.com/database/121/NETRF/sqlnet.htm#NETRF006) in the 12c Oracle documentation\. For information about default values for Oracle 11g, see [Parameters for the sqlnet\.ora File](https://docs.oracle.com/cd/E11882_01/network.112/e10835/sqlnet.htm#NETRF006) in the 11g Oracle documentation\.

### Viewing sqlnet\.ora Parameters<a name="USER_ModifyInstance.Oracle.sqlnet.Viewing"></a>

You can view sqlnet\.ora parameters and their settings using the AWS Management Console, the AWS CLI, or a SQL client\.

#### Viewing sqlnet\.ora Parameters Using the Console<a name="USER_ModifyInstance.Oracle.sqlnet.Viewing.Console"></a>

For information about viewing parameters in a parameter group, see [Working with DB Parameter Groups](USER_WorkingWithParamGroups.md)\.

In Oracle parameter groups, the `sqlnetora.` prefix identifies which parameters are sqlnet\.ora parameters\.

#### Viewing sqlnet\.ora Parameters Using the AWS CLI<a name="USER_ModifyInstance.Oracle.sqlnet.Viewing.CLI"></a>

To view the sqlnet\.ora parameters that were configured in an Oracle parameter group, use the AWS CLI [describe\-db\-parameters](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-parameters.html) command\.

To view the all of the sqlnet\.ora parameters for an Oracle DB instance, call the AWS CLI [download\-db\-log\-file\-portion](https://docs.aws.amazon.com/cli/latest/reference/rds/download-db-log-file-portion.html) command\. Specify the DB instance identifier, the log file name, and the type of output\. 

**Example**  
The following code lists all of the sqlnet\.ora parameters for `mydbinstance`\.   
For Linux, OS X, or Unix:  

```
aws rds download-db-log-file-portion \
--db-instance-identifier mydbinstance \
--log-file-name trace/sqlnet-parameters \
--output text
```
For Windows:  

```
aws rds download-db-log-file-portion ^
--db-instance-identifier mydbinstance ^
--log-file-name trace/sqlnet-parameters ^
--output text
```

#### Viewing sqlnet\.ora Parameters Using a SQL Client<a name="USER_ModifyInstance.Oracle.sqlnet.Viewing.SQL"></a>

After you connect to the Oracle DB instance in a SQL client, the following query lists the sqlnet\.ora parameters\.

```
1. SELECT * FROM TABLE
2.    (rdsadmin.rds_file_util.read_text_file(
3.         p_directory => 'BDUMP',
4.         p_filename  => 'sqlnet-parameters'));
```

For information about connecting to an Oracle DB instance in a SQL client, see [Connecting to a DB Instance Running the Oracle Database Engine](USER_ConnectToOracleInstance.md)\.