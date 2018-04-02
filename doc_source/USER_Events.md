# Using Amazon RDS Event Notification<a name="USER_Events"></a>

**Topics**
+ [Amazon RDS Event Categories and Event Messages](#USER_Events.Messages)
+ [Subscribing to Amazon RDS Event Notification](#USER_Events.Subscribing)
+ [Listing Your Amazon RDS Event Notification Subscriptions](#USER_Events.ListSubscription)
+ [Modifying an Amazon RDS Event Notification Subscription](#USER_Events.Modifying)
+ [Adding a Source Identifier to an Amazon RDS Event Notification Subscription](#USER_Events.AddingSource)
+ [Removing a Source Identifier from an Amazon RDS Event Notification Subscription](#USER_Events.RemovingSource)
+ [Listing the Amazon RDS Event Notification Categories](#USER_Events.ListingCategories)
+ [Deleting an Amazon RDS Event Notification Subscription](#USER_Events.Deleting)

 Amazon RDS uses the Amazon Simple Notification Service \(Amazon SNS\) to provide notification when an Amazon RDS event occurs\. These notifications can be in any notification form supported by Amazon SNS for an AWS region, such as an email, a text message, or a call to an HTTP endpoint\. 

Amazon RDS groups these events into categories that you can subscribe to so that you can be notified when an event in that category occurs\. You can subscribe to an event category for a DB instance, DB cluster, DB snapshot, DB cluster snapshot, DB security group, or for a DB parameter group\. For example, if you subscribe to the Backup category for a given DB instance, you will be notified whenever a backup\-related event occurs that affects the DB instance\. If you subscribe to a Configuration Change category for a DB security group, you will be notified when the DB security group is changed\. You will also receive notification when an event notification subscription changes\.

Note that for Amazon Aurora, events occur at the cluster rather than instance level, so you won't receive events if you subscribe to an Aurora DB instance\. Subscribe to the DB cluster instead\.

Event notifications are sent to the addresses you provide when you create the subscription\. You may want to create several different subscriptions, such as one subscription receiving all event notifications and another subscription that includes only critical events for your production DB instances\. You can easily turn off notification without deleting a subscription by setting the **Enabled** radio button to `No` in the Amazon RDS console or by setting the `Enabled` parameter to `false` using the CLI or Amazon RDS API\. 

**Note**  
Amazon RDS event notifications using SMS text messages are currently available for topic ARNs and Amazon RDS resources in the US\-East \(Northern Virginia\) Region\. For more information on using text messages with SNS, see [Sending and Receiving SMS Notifications Using Amazon SNS](http://docs.aws.amazon.com/sns/latest/dg//SMSMessages.html)\.

Amazon RDS uses the Amazon Resource Name \(ARN\) of an Amazon SNS topic to identify each subscription\. The Amazon RDS console will create the ARN for you when you create the subscription\. If you use the CLI or API, you have to create the ARN by using the Amazon SNS console or the Amazon SNS API when you create a subscription\.

Billing for Amazon RDS event notification is through the Amazon Simple Notification Service \(Amazon SNS\)\. Amazon SNS fees apply when using event notification; for more information on Amazon SNS billing, see [ Amazon Simple Notification Service Pricing](http://aws.amazon.com/sns/#pricing)\.

The process for subscribing to Amazon RDS event notification is as follows:

1. Create an Amazon RDS event notification subscription by using the Amazon RDS console, AWS CLI, or API\.

1. Amazon RDS sends an approval email or SMS message to the addresses you submitted with your subscription\. To confirm your subscription, choose the link in the notification you were sent\.

1. When you have confirmed the subscription, the status of your subscription is updated in the Amazon RDS console's **My Event Subscriptions** section\.

1. You will begin to receive event notifications\.

The following section lists all categories and events that you can be notified of\. It also provides information about subscribing to and working with Amazon RDS event subscriptions\.

## Amazon RDS Event Categories and Event Messages<a name="USER_Events.Messages"></a>

 Amazon RDS generates a significant number of events in categories that you can subscribe to using the Amazon RDS Console, AWS CLI, or the API\. Each category applies to a source type, which can be a DB instance, DB snapshot, DB security group, or DB parameter group\.

The following table shows the event category and a list of events when a DB instance is the source type\.

**Categories and Events for the DB Instance Source Type**


|  Category  | Amazon RDS Event ID |  Description  | 
| --- | --- | --- | 
|  availability  | RDS\-EVENT\-0006 |  The DB instance is restarting due to a previous controlled shutdown, or a recovery\. The DB instance will be unavailable until the restart completes\.  | 
|  availability  | RDS\-EVENT\-0004 |  The DB instance is undergoing a controlled shutdown\.  | 
|  availability  | RDS\-EVENT\-0022 |  An error has occurred while restarting MySQL or MariaDB\.  | 
|  backup  | RDS\-EVENT\-0001 |  A backup of the DB instance has started\.   | 
|  backup  | RDS\-EVENT\-0002 |  A backup of the DB instance is complete\.  | 
|  configuration change  | RDS\-EVENT\-0009 |  The DB instance has been added to a security group\.  | 
|  configuration change  | RDS\-EVENT\-0024 |  The DB instance is being converted to a Multi\-AZ DB instance\.  | 
|  configuration change  | RDS\-EVENT\-0030 |  The DB instance is being converted to a Single\-AZ DB instance\.  | 
|  configuration change  | RDS\-EVENT\-0012 |  The DB instance class for this DB instance is being changed\.   | 
|  configuration change  | RDS\-EVENT\-0018 |  The current storage settings for this DB instance are being changed\.  | 
|  configuration change  | RDS\-EVENT\-0011 |  A parameter group for this DB instance has changed\.  | 
|  configuration change  | RDS\-EVENT\-0092 |  A parameter group for this DB instance has finished updating\.  | 
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
|  creation  | RDS\-EVENT\-0005 |  A DB instance is being created\.  | 
|  deletion  | RDS\-EVENT\-0003 |  The DB instance is being deleted\.  | 
|  failover  | RDS\-EVENT\-0034 |  Amazon RDS is not attempting a requested failover because a failover recently occurred on the DB instance\.  | 
|  failover  | RDS\-EVENT\-0013 |  A Multi\-AZ failover that resulted in the promotion of a standby instance has started\.  | 
|  failover  | RDS\-EVENT\-0015 |  A Multi\-AZ failover that resulted in the promotion of a standby instance is complete\. It may take several minutes for the DNS to transfer to the new primary DB instance\.  | 
|  failover  | RDS\-EVENT\-0065 |  The instance has recovered from a partial failover\.  | 
| failover | RDS\-EVENT\-0049 | A Multi\-AZ failover has completed\. | 
|  failover  | RDS\-EVENT\-0050 |  A Multi\-AZ activation has started after a successful instance recovery\.  | 
|  failover  | RDS\-EVENT\-0051 |  A Multi\-AZ activation is complete\. Your database should be accessible now\.  | 
|  failure  | RDS\-EVENT\-0031 |  The DB instance has failed due to an incompatible configuration or an underlying storage issue\. Begin a point\-in\-time\-restore for the DB instance\.  | 
|  failure  | RDS\-EVENT\-0036 |  The DB instance is in an incompatible network\. Some of the specified subnet IDs are invalid or do not exist\.  | 
|  failure  | RDS\-EVENT\-0035 |  The DB instance has invalid parameters\. For example, MySQL could not start because a memory\-related parameter is set too high for this instance class, so the customer action would be to modify the memory parameter and reboot the DB instance\.  | 
|  failure  | RDS\-EVENT\-0058 |  Error while creating Statspack user account PERFSTAT\. Please drop the account before adding the Statspack option\.  | 
|  failure  | RDS\-EVENT\-0079 |  Enhanced Monitoring cannot be enabled without the enhanced monitoring IAM role\. For information on creating the enhanced monitoring IAM role, see [To create an IAM role for Amazon RDS Enhanced Monitoring](USER_Monitoring.OS.md#USER_Monitoring.OS.IAMRole)\.  | 
|  failure  | RDS\-EVENT\-0080 |  Enhanced Monitoring was disabled due to an error making the configuration change\. It is likely that the enhanced monitoring IAM role is configured incorrectly\. For information on creating the enhanced monitoring IAM role, see [To create an IAM role for Amazon RDS Enhanced Monitoring](USER_Monitoring.OS.md#USER_Monitoring.OS.IAMRole)\.  | 
|  failure  | RDS\-EVENT\-0081 |  The IAM role that you use to access your Amazon S3 bucket for SQL Server native backup and restore is configured incorrectly\. For more information, see [Setting Up for Native Backup and Restore](SQLServer.Procedural.Importing.md#SQLServer.Procedural.Importing.Native.Enabling)\.  | 
|  failure  | RDS\-EVENT\-0082 |  Amazon Aurora was unable to copy backup data from an Amazon S3 bucket\. It is likely that the permissions for Aurora to access the Amazon S3 bucket are configured incorrectly\. For more information, see [Migrating Data from MySQL by Using an Amazon S3 Bucket](AuroraMySQL.Migrating.ExtMySQL.md#AuroraMySQL.Migrating.ExtMySQL.S3)\.  | 
|  low storage  | RDS\-EVENT\-0089 |  The DB instance has consumed more than 90% of its allocated storage\. You can monitor the storage space for a DB instance using the **Free Storage Space** metric\. For more information, see [Viewing DB Instance Metrics](CHAP_Monitoring.md#USER_Monitoring)\.  | 
|  low storage  | RDS\-EVENT\-0007 |  The allocated storage for the DB instance has been exhausted\. To resolve this issue, you should allocate additional storage for the DB instance\. For more information, see the [RDS FAQ](https://aws.amazon.com/rds/faqs/#20)\. You can monitor the storage space for a DB instance using the **Free Storage Space** metric\. For more information, see [Viewing DB Instance Metrics](CHAP_Monitoring.md#USER_Monitoring)\.  | 
|  maintenance  | RDS\-EVENT\-0026 |  Offline maintenance of the DB instance is taking place\. The DB instance is currently unavailable\.  | 
|  maintenance  | RDS\-EVENT\-0027 |  Offline maintenance of the DB instance is complete\. The DB instance is now available\.  | 
| notification | RDS\-EVENT\-0044 | Operator\-issued notification\. For more information, see the event message\. | 
| notification | RDS\-EVENT\-0047 | Patching of the DB instance has completed\. | 
| notification | RDS\-EVENT\-0048 | Patching of the DB instance has been delayed\. | 
| notification | RDS\-EVENT\-0054 | The MySQL storage engine you are using is not InnoDB, which is the recommended MySQL storage engine for Amazon RDS\. For information about MySQL storage engines, see [Supported Storage Engines for MySQL on Amazon RDS](CHAP_MySQL.md#MySQL.Concepts.Storage)\. | 
| notification | RDS\-EVENT\-0055 | The number of tables you have for your DB instance exceeds the recommended best practices for Amazon RDS\. Please reduce the number of tables on your DB instance\. For information about recommended best practices, see [Amazon RDS Basic Operational Guidelines](CHAP_BestPractices.md#CHAP_BestPractices.DiskPerformance)\. | 
| notification | RDS\-EVENT\-0056 | The number of databases you have for your DB instance exceeds the recommended best practices for Amazon RDS\. Please reduce the number of databases on your DB instance\. For information about recommended best practices, see [Amazon RDS Basic Operational Guidelines](CHAP_BestPractices.md#CHAP_BestPractices.DiskPerformance)\. | 
| notification | RDS\-EVENT\-0064 | The TDE key has been rotated\. For more information about Oracle TDE, see [Oracle Transparent Data Encryption](Appendix.Oracle.Options.AdvSecurity.md)\. For more information about SQL Server TDE, see [Microsoft SQL Server Transparent Data Encryption Support](Appendix.SQLServer.Options.TDE.md)\.  | 
| notification | RDS\-EVENT\-0084 |  You attempted to convert a DB instance to Multi\-AZ, but it contains in\-memory file groups that are not supported for Multi\-AZ\. For more information, see [Multi\-AZ Deployments for Microsoft SQL Server with Database Mirroring](USER_SQLServerMultiAZ.md)\.   | 
| notification | RDS\-EVENT\-0087 |  The DB instance has been stopped\.   | 
| notification | RDS\-EVENT\-0088 |  The DB instance has been started\.  | 
| read replica | RDS\-EVENT\-0045 | An error has occurred in the read replication process\. For more information, see the event message\. For information on troubleshooting Read Replica errors, see [Troubleshooting a MySQL or MariaDB Read Replica Problem](USER_ReadRepl.md#USER_ReadRepl.Troubleshooting)\. | 
| read replica | RDS\-EVENT\-0046 | The Read Replica has resumed replication\. This message appears when you first create a Read Replica, or as a monitoring message confirming that replication is functioning properly\. If this message follows an RDS\-EVENT\-0045 notification, then replication has resumed following an error or after replication was stopped\. | 
|  read replica  | RDS\-EVENT\-0057 |  Replication on the Read Replica was terminated\.  | 
|  read replica  | RDS\-EVENT\-0062 |  Replication on the Read Replica was manually stopped\.  | 
|  read replica  | RDS\-EVENT\-0063 |  Replication on the Read Replica was reset\.  | 
|  recovery  | RDS\-EVENT\-0020 |  Recovery of the DB instance has started\. Recovery time will vary with the amount of data to be recovered\.  | 
|  recovery  | RDS\-EVENT\-0021 |  Recovery of the DB instance is complete\.  | 
|  recovery  | RDS\-EVENT\-0023 |  A manual backup has been requested but Amazon RDS is currently in the process of creating a DB snapshot\. Submit the request again after Amazon RDS has completed the DB snapshot\.  | 
|  recovery  | RDS\-EVENT\-0052 |  Recovery of the Multi\-AZ instance has started\. Recovery time will vary with the amount of data to be recovered\.  | 
|  recovery  | RDS\-EVENT\-0053 |  Recovery of the Multi\-AZ instance is complete\.  | 
|  recovery  | RDS\-EVENT\-0066 |  The SQL Server DB instance is re\-establishing its mirror\. Performance will be degraded until the mirror is reestablished\. A database was found with non\-FULL recovery model\. The recovery model was changed back to FULL and mirroring recovery was started\. \(<dbname>: <recovery model found>\[,…\]\)”  | 
|  restoration  | RDS\-EVENT\-0008 |  The DB instance has been restored from a DB snapshot\.  | 
|  restoration  | RDS\-EVENT\-0019 |  The DB instance has been restored from a point\-in\-time backup\.  | 
|  security  | RDS\-EVENT\-0068 |  The CloudHSM Classic partition password was decrypted by the system\.  | 

The following table shows the event category and a list of events when a DB parameter group is the source type\.

**Categories and Events for the DB Parameter Group Source Type**


|  Category  | RDS Event ID |  Description  | 
| --- | --- | --- | 
|  configuration change  | RDS\-EVENT\-0037 |  The parameter group was modified\.  | 

The following table shows the event category and a list of events when a DB security group is the source type\.

**Categories and Events for the DB Security Group Source Type**


|  Category  | RDS Event ID |  Description  | 
| --- | --- | --- | 
|  configuration change  | RDS\-EVENT\-0038 |  The security group has been modified\.  | 
|  failure  | RDS\-EVENT\-0039 |  The Amazon EC2 security group owned by \[user\] does not exist; authorization for the security group has been revoked\.  | 

The following table shows the event category and a list of events when a DB snapshot is the source type\.

**Categories and Events for the DB Snapshot Source Type**


|  Category  | RDS Event ID |  Description  | 
| --- | --- | --- | 
|  creation  | RDS\-EVENT\-0040 |  A manual DB snapshot is being created\.  | 
|  deletion  | RDS\-EVENT\-0041 |  A DB snapshot has been deleted\.  | 
|  creation  | RDS\-EVENT\-0042 |  A manual DB snapshot has been created\.  | 
|  restoration  | RDS\-EVENT\-0043 |  A DB instance is being restored from a DB snapshot\.  | 
|  notification  | RDS\-EVENT\-0059 |  Started the copy of the cross region DB snapshot \[DB snapshot name\] from source region \[region name\]\.  | 
|  notification  | RDS\-EVENT\-0060 |  Finished the copy of the cross region DB snapshot \[DB snapshot name\] from source region \[region name\] in \[time\] minutes\.  | 
|  notification  | RDS\-EVENT\-0061 |  The copy of a cross region DB snapshot failed\.  | 
| creation | RDS\-EVENT\-0090 | An automated DB snapshot is being created\. | 
| creation | RDS\-EVENT\-0091 | An automated DB snapshot has been created\. | 

The following table shows the event category and a list of events when a DB cluster is the source type\.

**Categories and Events for the DB Cluster Source Type**


|  Category  | RDS Event ID |  Description  | 
| --- | --- | --- | 
|  failover  | RDS\-EVENT\-0069 |  A failover for the DB cluster has failed\.  | 
|  failover  | RDS\-EVENT\-0070 |  A failover for the DB cluster has restarted\.  | 
|  failover  | RDS\-EVENT\-0071 |  A failover for the DB cluster has finished\.  | 
|  failover  | RDS\-EVENT\-0072 |  A failover for the DB cluster has begun within the same Availability Zone\.  | 
|  failover  | RDS\-EVENT\-0073 |  A failover for the DB cluster has begun across Availability Zones\.  | 
|  failure  | RDS\-EVENT\-0083 |  Amazon Aurora was unable to copy backup data from an Amazon S3 bucket\. It is likely that the permissions for Aurora to access the Amazon S3 bucket are configured incorrectly\. For more information, see [Migrating Data from MySQL by Using an Amazon S3 Bucket](AuroraMySQL.Migrating.ExtMySQL.md#AuroraMySQL.Migrating.ExtMySQL.S3)\.  | 
|  notification  | RDS\-EVENT\-0076 |  Migration to an Amazon Aurora DB cluster failed\.  | 
|  notification  | RDS\-EVENT\-0077 |  An attempt to convert a table from the source database to InnoDB failed during the migration to an Amazon Aurora DB cluster\.  | 

The following table shows the event category and a list of events when a DB cluster snapshot is the source type\.

**Categories and Events for the DB Cluster Snapshot Source Type**


|  Category  | RDS Event ID |  Description  | 
| --- | --- | --- | 
|  backup  | RDS\-EVENT\-0074 |  Creation of a manual DB cluster snapshot has started\.  | 
|  backup  | RDS\-EVENT\-0075 |  A manual DB cluster snapshot has been created\.  | 

## Subscribing to Amazon RDS Event Notification<a name="USER_Events.Subscribing"></a>

You can create an Amazon RDS event notification subscription so you can be notified when an event occurs for a given DB instance, DB snapshot, DB security group, or DB parameter group\. The simplest way to create a subscription is with the RDS console\. If you choose to create event notification subscriptions using the CLI or API, you must create an Amazon Simple Notification Service topic and subscribe to that topic with the Amazon SNS console or Amazon SNS API\. You will also need to retain the Amazon Resource Name \(ARN\) of the topic because it is used when submitting CLI commands or API actions\. For information on creating an SNS topic and subscribing to it, see [Getting Started with Amazon SNS](http://docs.aws.amazon.com/sns/latest/dg/GettingStarted.html)\.

You can specify the type of source you want to be notified of and the Amazon RDS source that triggers the event\. These are defined by the **SourceType** \(type of source\) and the **SourceIdentifier** \(the Amazon RDS source generating the event\)\. If you specify both the **SourceType** and **SourceIdentifier**, such as `SourceType = db-instance` and `SourceIdentifier = myDBInstance1`, you will receive all the DB\_Instance events for the specified source\. If you specify a **SourceType** but do not specify a **SourceIdentifier**, you will receive notice of the events for that source type for all your Amazon RDS sources\. If you do not specify either the **SourceType** nor the **SourceIdentifier**, you will be notified of events generated from all Amazon RDS sources belonging to your customer account\.

### AWS Management Console<a name="USER_Events.Subscribing.Console"></a>

**To subscribe to RDS event notification**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1.  In navigation pane, choose **Event Subscriptions**\. 

1.  In the **Event subscriptions** pane, choose **Create event subscription**\. 

1.  In the **Create event subscription** dialog box, do the following:

   1. Type a name for the event notification subscription for **Name**\.

   1. For **Send notifications to**, choose an existing Amazon SNS Amazon Resource Name \(ARN\) for an Amazon SNS topic, or choose **create topic** to enter the name of a topic and a list of recipients\.

   1. For **Source type**, choose a source type\.

   1. Choose **Yes** to enable the subscription\. If you want to create the subscription but to not have notifications sent yet, choose **No**\.

   1. Depending on the source type you selected, choose the event categories and sources that you want to receive event notifications for\.

   1. Choose **Create**\.

The Amazon RDS console indicates that the subscription is being created\.

![\[List DB event notification subscriptions\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/EventNotification-Create2.png)

### CLI<a name="USER_Events.Subscribing.CLI"></a>

To subscribe to RDS event notification, use the AWS CLI [http://docs.aws.amazon.com/cli/latest/reference/rds/create-event-subscription.html](http://docs.aws.amazon.com/cli/latest/reference/rds/create-event-subscription.html) command\. Include the following required parameters:
+ `--subscription-name`
+ `--sns-topic-arn`

**Example**  
For Linux, OS X, or Unix:  

```
aws rds create-event-subscription \
    --subscription-name myeventsubscription \
    --sns-topic-arn arn:aws:sns:us-east-1:802#########:myawsuser-RDS \
    --enabled
```
For Windows:  

```
aws rds create-event-subscription ^
    --subscription-name myeventsubscription ^
    --sns-topic-arn arn:aws:sns:us-east-1:802#########:myawsuser-RDS ^
    --enabled
```

### API<a name="USER_Events.Subscribing.API"></a>

To subscribe to Amazon RDS event notification, call the Amazon RDS API function [ `CreateEventSubscription`](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateEventSubscription.html)\. Include the following required parameters: 
+ `SubscriptionName`
+ `SnsTopicArn`

**Example**  

```
https://rds.us-east-1.amazonaws.com/
   ?Action=CreateEventSubscription
   &Enabled=true
   &SignatureMethod=HmacSHA256
   &SignatureVersion=4
   &SnsTopicArn=arn%3Aaws%3Asns%3Aus-east-1%3A802#########%3Amyawsuser-RDS
   &SourceType=db-security-group
   &SubscriptionName=myeventsubscription
   &Version=2014-09-01
   &X-Amz-Algorithm=AWS4-HMAC-SHA256
   &X-Amz-Credential=AKIADQKE4SARGYLE/20140425/us-east-1/rds/aws4_request
   &X-Amz-Date=20140425T214325Z
   &X-Amz-SignedHeaders=content-type;host;user-agent;x-amz-content-sha256;x-amz-date
   &X-Amz-Signature=7045960f6ab15609571fb05278004256e186b7633ab2a3ae46826d7713e0b461
```

## Listing Your Amazon RDS Event Notification Subscriptions<a name="USER_Events.ListSubscription"></a>

You can list your current Amazon RDS event notification subscriptions\.

### AWS Management Console<a name="USER_Events.ListSubscription.Console"></a>

**To list your current Amazon RDS event notification subscriptions**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1.  In the navigation pane, choose **Event subscriptions**\. The **Event subscriptions** pane shows all your event notification subscriptions\.  
![\[List DB event notification subscriptions\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/EventNotification-ListSubs.png)

### CLI<a name="USER_Events.ListSubscription.CLI"></a>

To list your current Amazon RDS event notification subscriptions, use the AWS CLI [http://docs.aws.amazon.com/cli/latest/reference/rds/describe-event-subscriptions.html](http://docs.aws.amazon.com/cli/latest/reference/rds/describe-event-subscriptions.html) command\. 

**Example**  
The following example describes all event subscriptions\.  

```
aws rds describe-event-subscriptions
```
The following example describes the `myfirsteventsubscription`\.  

```
aws rds describe-event-subscriptions --subscription-name myfirsteventsubscription
```

### API<a name="USER_Events.ListSubscription.API"></a>

To list your current Amazon RDS event notification subscriptions, call the Amazon RDS API [ `DescribeEventSubscriptions`](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeEventSubscriptions.html) action\. 

**Example**  
The following code example lists up to 100 event subscriptions\.  

```
https://rds.us-east-1.amazonaws.com/
   ?Action=DescribeEventSubscriptions
   &MaxRecords=100
   &SignatureMethod=HmacSHA256
   &SignatureVersion=4
   &Version=2014-09-01
   &X-Amz-Algorithm=AWS4-HMAC-SHA256
   &X-Amz-Credential=AKIADQKE4SARGYLE/20140428/us-east-1/rds/aws4_request
   &X-Amz-Date=20140428T161907Z
   &X-Amz-SignedHeaders=content-type;host;user-agent;x-amz-content-sha256;x-amz-date
   &X-Amz-Signature=4208679fe967783a1a149c826199080a066085d5a88227a80c6c0cadb3e8c0d4
```
The following example describes the `myfirsteventsubscription`\.  

```
https://rds.us-east-1.amazonaws.com/
   ?Action=DescribeEventSubscriptions
   &SignatureMethod=HmacSHA256
   &SignatureVersion=4
   &SubscriptionName=myfirsteventsubscription
   &Version=2014-09-01
   &X-Amz-Algorithm=AWS4-HMAC-SHA256
   &X-Amz-Credential=AKIADQKE4SARGYLE/20140428/us-east-1/rds/aws4_request
   &X-Amz-Date=20140428T161907Z
   &X-Amz-SignedHeaders=content-type;host;user-agent;x-amz-content-sha256;x-amz-date
   &X-Amz-Signature=4208679fe967783a1a149c826199080a066085d5a88227a80c6c0cadb3e8c0d4
```

## Modifying an Amazon RDS Event Notification Subscription<a name="USER_Events.Modifying"></a>

After you have created a subscription, you can change the subscription name, source identifier, categories, or topic ARN\.

### AWS Management Console<a name="USER_Events.Modifying.Console"></a>

**To modify an Amazon RDS event notification subscription**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1.  In the navigation pane, choose **Event subscriptions**\. 

1.  In the **Event subscriptions** pane, choose the subscription that you want to modify and choose **Edit**\. 

1.  Make your changes to the subscription in either the **Target** or **Source** sections\.

1. Choose **Edit**\. The Amazon RDS console indicates that the subscription is being modified\.  
![\[List DB event notification subscriptions\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/EventNotification-Modify2.png)

### CLI<a name="USER_Events.Modifying.CLI"></a>

To modify an Amazon RDS event notification subscription, use the AWS CLI [http://docs.aws.amazon.com/cli/latest/reference/rds/modify-event-subscription.html](http://docs.aws.amazon.com/cli/latest/reference/rds/modify-event-subscription.html) command\. Include the following required parameter:
+ `--subscription-name`

**Example**  
The following code enables `myeventsubscription`\.  
For Linux, OS X, or Unix:  

```
1. aws rds modify-event-subscription \
2.     --subscription-name myeventsubscription \
3.     --enabled
```
For Windows:  

```
1. aws rds modify-event-subscription ^
2.     --subscription-name myeventsubscription ^
3.     --enabled
```

### API<a name="USER_Events.Modifying.API"></a>

To modify an Amazon RDS event, call the Amazon RDS API action [http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyEventSubscription.html](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyEventSubscription.html)\. Include the following required parameter:
+ `SubscriptionName`

**Example**  
The following code enables `myeventsubscription`\.  

```
 1. https://rds.us-west-2.amazonaws.com/
 2.    ?Action=ModifyEventSubscription
 3.    &Enabled=true
 4.    &SignatureMethod=HmacSHA256
 5.    &SignatureVersion=4
 6.    &SnsTopicArn=arn%3Aaws%3Asns%3Aus-west-2%3A802#########%3Amy-rds-events
 7.    &SubscriptionName=myeventsubscription
 8.    &Version=2014-09-01
 9.    &X-Amz-Algorithm=AWS4-HMAC-SHA256
10.    &X-Amz-Credential=AKIADQKE4SARGYLE/20140428/us-west-2/rds/aws4_request
11.    &X-Amz-Date=20140428T183020Z
12.    &X-Amz-SignedHeaders=content-type;host;user-agent;x-amz-content-sha256;x-amz-date
13.    &X-Amz-Signature=3d85bdfaf13861e93a9528824d9876ed87e6e01aaf43a962ce6f2a39247cf33a
```

## Adding a Source Identifier to an Amazon RDS Event Notification Subscription<a name="USER_Events.AddingSource"></a>

You can add a source identifier \(the Amazon RDS source generating the event\) to an existing subscription\.

### AWS Management Console<a name="USER_Events.AddingSource.Console"></a>

You can easily add or remove source identifiers using the Amazon RDS console by selecting or deselecting them when modifying a subscription\. For more information, see [Modifying an Amazon RDS Event Notification Subscription](#USER_Events.Modifying)\.

### CLI<a name="USER_Events.AddingSource.CLI"></a>

To add a source identifier to an Amazon RDS event notification subscription, use the AWS CLI [http://docs.aws.amazon.com/](http://docs.aws.amazon.com/) command\. Include the following required parameters:
+ `--subscription-name`
+ `--source-identifier`

**Example**  
The following example adds the source identifier `mysqldb` to the `myrdseventsubscription` subscription\.  
For Linux, OS X, or Unix:  

```
1. aws rds add-source-identifier-to-subscription \
2.     --subscription-name myrdseventsubscription \
3.     --source-identifier mysqldb
```
For Windows:  

```
1. aws rds add-source-identifier-to-subscription ^
2.     --subscription-name myrdseventsubscription ^
3.     --source-identifier mysqldb
```

### API<a name="USER_Events.AddingSource.API"></a>

To add a source identifier to an Amazon RDS event notification subscription, call the Amazon RDS API [http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_AddSourceIdentifierToSubscription.html](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_AddSourceIdentifierToSubscription.html)\. Include the following required parameters:
+ `SubscriptionName`
+ `SourceIdentifier`

**Example**  

```
https://rds.us-east-1.amazonaws.com/
   ?Action=AddSourceIdentifierToSubscription
   &SignatureMethod=HmacSHA256
   &SignatureVersion=4
   &SourceIdentifier=mysqldb
   &SubscriptionName=myrdseventsubscription
   &Version=2014-09-01
   &X-Amz-Algorithm=AWS4-HMAC-SHA256
   &X-Amz-Credential=AKIADQKE4SARGYLE/20140422/us-east-1/rds/aws4_request
   &X-Amz-Date=20140422T230442Z
   &X-Amz-SignedHeaders=content-type;host;user-agent;x-amz-content-sha256;x-amz-date
   &X-Amz-Signature=347d5e788e809cd06c50214b12750a3c39716bf65b239bb6f7ee8ff5374e2df9
```

## Removing a Source Identifier from an Amazon RDS Event Notification Subscription<a name="USER_Events.RemovingSource"></a>

You can remove a source identifier \(the Amazon RDS source generating the event\) from a subscription if you no longer want to be notified of events for that source\. 

### AWS Management Console<a name="USER_Events.RemovingSource.Console"></a>

You can easily add or remove source identifiers using the Amazon RDS console by selecting or deselecting them when modifying a subscription\. For more information, see [Modifying an Amazon RDS Event Notification Subscription](#USER_Events.Modifying)\.

### CLI<a name="USER_Events.RemovingSource.CLI"></a>

To remove a source identifier from an Amazon RDS event notification subscription, use the AWS CLI [http://docs.aws.amazon.com/cli/latest/reference/rds/remove-source-identifier-from-subscription.html](http://docs.aws.amazon.com/cli/latest/reference/rds/remove-source-identifier-from-subscription.html) command\. Include the following required parameters:
+ `--subscription-name`
+ `--source-identifier`

**Example**  
The following example removes the source identifier `mysqldb` from the `myrdseventsubscription` subscription\.  
For Linux, OS X, or Unix:  

```
1. aws rds remove-source-identifier-from-subscription \
2.     --subscription-name myrdseventsubscription \
3.     --source-identifier mysqldb
```
For Windows:  

```
1. aws rds remove-source-identifier-from-subscription ^
2.     --subscription-name myrdseventsubscription ^
3.     --source-identifier mysqldb
```

### API<a name="USER_Events.RemovingSource.API"></a>

To remove a source identifier from an Amazon RDS event notification subscription, use the Amazon RDS API [http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RemoveSourceIdentifierFromSubscription.html](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RemoveSourceIdentifierFromSubscription.html) command\. Include the following required parameters:
+ `SubscriptionName`
+ `SourceIdentifier`

**Example**  
The following example removes the source identifier `mysqldb` from the `myrdseventsubscription` subscription\.  

```
 1. https://rds.us-east-1.amazonaws.com/
 2.    ?Action=RemoveSourceIdentifierFromSubscription
 3.    &SignatureMethod=HmacSHA256
 4.    &SignatureVersion=4
 5.    &SourceIdentifier=mysqldb
 6.    &SubscriptionName=myrdseventsubscription
 7.    &Version=2014-09-01
 8.    &X-Amz-Algorithm=AWS4-HMAC-SHA256
 9.    &X-Amz-Credential=AKIADQKE4SARGYLE/20140428/us-east-1/rds/aws4_request
10.    &X-Amz-Date=20140428T222718Z
11.    &X-Amz-SignedHeaders=content-type;host;user-agent;x-amz-content-sha256;x-amz-date
12.    &X-Amz-Signature=4419f0015657ee120d781849ffdc6642eeafeee42bf1d18c4b2ed8eb732f7bf8
```

## Listing the Amazon RDS Event Notification Categories<a name="USER_Events.ListingCategories"></a>

All events for a resource type are grouped into categories\. To view the list of categories available, use the following procedures\.

### AWS Management Console<a name="USER_Events.ListingCategories.Console"></a>

 When you create or modify an event notification subscription, the event categories are displayed in the Amazon RDS console\. See the topic [Modifying an Amazon RDS Event Notification Subscription](#USER_Events.Modifying) for more information\. 

![\[List DB event notification categories\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/EventNotification-Categories.png)

### CLI<a name="USER_Events.ListingCategories.CLI"></a>

To list the Amazon RDS event notification categories, use the AWS CLI [http://docs.aws.amazon.com/cli/latest/reference/rds/describe-event-categories.html](http://docs.aws.amazon.com/cli/latest/reference/rds/describe-event-categories.html) command\. This command has no required parameters\.

**Example**  

```
1. aws rds describe-event-categories
```

### API<a name="USER_Events.ListingCategories.API"></a>

To list the Amazon RDS event notification categories, use the Amazon RDS API [http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeEventCategories.html](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeEventCategories.html) command\. This command has no required parameters\.

**Example**  

```
https://rds.us-west-2.amazonaws.com/
   ?Action=DescribeEventCategories
   &SignatureMethod=HmacSHA256
   &SignatureVersion=4
   &Version=2014-09-01
   &X-Amz-Algorithm=AWS4-HMAC-SHA256
   &X-Amz-Credential=AKIADQKE4SARGYLE/20140421/us-west-2/rds/aws4_request
   &X-Amz-Date=20140421T194732Z
   &X-Amz-SignedHeaders=content-type;host;user-agent;x-amz-content-sha256;x-amz-date
   &X-Amz-Signature=6e25c542bf96fe24b28c12976ec92d2f856ab1d2a158e21c35441a736e4fde2b
```

## Deleting an Amazon RDS Event Notification Subscription<a name="USER_Events.Deleting"></a>

You can delete a subscription when you no longer need it\. All subscribers to the topic will no longer receive event notifications specified by the subscription\.

### AWS Management Console<a name="USER_Events.Deleting.Console"></a>

**To delete an Amazon RDS event notification subscription**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1.  In the navigation pane, choose **DB Event Subscriptions**\. 

1.  In the **My DB Event Subscriptions** pane, choose the subscription that you want to delete\. 

1. Choose **Delete**\.

1. The Amazon RDS console indicates that the subscription is being deleted\.  
![\[Delete an event notification subscription\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/EventNotification-Delete.png)

### CLI<a name="USER_Events.Deleting.CLI"></a>

To delete an Amazon RDS event notification subscription, use the AWS CLI [http://docs.aws.amazon.com/cli/latest/reference/rds/delete-event-subscription.html](http://docs.aws.amazon.com/cli/latest/reference/rds/delete-event-subscription.html) command\. Include the following required parameter:
+ `--subscription-name`

**Example**  
The following example deletes the subscription `myrdssubscription`\.  

```
1. delete-event-subscription --subscription-name myrdssubscription
```

### API<a name="USER_Events.Deleting.API"></a>

To delete an Amazon RDS event notification subscription, use the RDS API [http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DeleteEventSubscription.html](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DeleteEventSubscription.html) command\. Include the following required parameter:
+ `SubscriptionName`

**Example**  
The following example deletes the subscription `myrdssubscription`\.  

```
 1. https://rds.us-east-1.amazonaws.com/
 2.    ?Action=DeleteEventSubscription
 3.    &SignatureMethod=HmacSHA256 
 4.    &SignatureVersion=4
 5.    &SubscriptionName=myrdssubscription
 6.    &Version=2014-09-01
 7.    &X-Amz-Algorithm=AWS4-HMAC-SHA256
 8.    &X-Amz-Credential=AKIADQKE4SARGYLE/20140423/us-east-1/rds/aws4_request
 9.    &X-Amz-Date=20140423T203337Z
10.    &X-Amz-SignedHeaders=content-type;host;user-agent;x-amz-content-sha256;x-amz-date
11.    &X-Amz-Signature=05aa834e364a9e1a279d44cc955694518fc96fff638c74faa2be45783102e785
```