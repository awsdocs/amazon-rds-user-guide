# Starting a database activity stream<a name="DBActivityStreams.Enabling"></a>

When you start an activity stream for an Oracle DB instance, each database activity event, such as a change or access, generates an activity stream event\. SQL commands such as `CONNECT` and `SELECT` generate access events\. SQL commands such as `CREATE` and `INSERT` generate change events\.

**Important**  
Turning on an activity stream for an Oracle DB instance clears existing audit data\. It also revokes audit trail privileges\. When the stream is enabled, RDS for Oracle can no longer do the following:  
Purge unified audit trail records\.
Add, delete, or modify the unified audit policy\.
Update the last archived time stamp\.

## Console<a name="DBActivityStreams.Enabling-collapsible-section-E1"></a>

**To start a database activity stream**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose the RDS for Oracle instance on which you want to start an activity stream\. In a Multi\-AZ deployment, start the stream on only the primary instance\. The activity stream audits both the primary and the standby instances\.

1. For **Actions**, choose **Start activity stream**\. 

   The **Start database activity stream: *name*** window appears, where *name* is your RDS instance\.

1. Enter the following settings:
   + For **AWS KMS key**, choose a key from the list of AWS KMS keys\.

     RDS for Oracle uses the KMS key to encrypt the key that in turn encrypts database activity\. Choose a KMS key other than the default key\. For more information about encryption keys and AWS KMS, see [What is AWS Key Management Service?](https://docs.aws.amazon.com/kms/latest/developerguide/overview.html) in the *AWS Key Management Service Developer Guide\.*
   + For **Database activity events**, choose **Include Oracle native audit fields** to include Oracle\-specific audit fields in the stream\.
   + Choose **Immediately**\.

     When you choose **Immediately**, the RDS instance restarts right away\. If you choose **During the next maintenance window**, the RDS instance doesn't restart right away\. In this case, the database activity stream doesn't start until the next maintenance window\.

1. Choose **Start database activity stream**\.

   The status for the Oracle database shows that the activity stream is starting\.
**Note**  
If you get the error `You can't start a database activity stream in this configuration`, check [Supported DB instance classes for database activity streams](DBActivityStreams.Overview.md#DBActivityStreams.Overview.requirements.classes) to see whether your RDS instance is using a supported instance class\.

## AWS CLI<a name="DBActivityStreams.Enabling-collapsible-section-E2"></a>

To start database activity streams for an Oracle DB instance, configure the database using the [start\-activity\-stream](https://docs.aws.amazon.com/cli/latest/reference/rds/start-activity-stream.html) AWS CLI command\.
+ `--resource-arn arn` – Specifies the Amazon Resource Name \(ARN\) of the DB instance\.
+ `--kms-key-id key` – Specifies the KMS key identifier for encrypting messages in the database activity stream\. The AWS KMS key identifier is the key ARN, key ID, alias ARN, or alias name for the AWS KMS key\.
+ `--engine-native-audit-fields-included` – Includes engine\-specific unified auditing fields in the data stream\. To exclude these fields, specify `--no-engine-native-audit-fields-included` \(default\)\.

The following example starts a database activity stream for an Oracle DB instance in asynchronous mode\.

For Linux, macOS, or Unix:

```
aws rds start-activity-stream \
    --mode async \
    --kms-key-id my-kms-key-arn \
    --resource-arn my-instance-arn \
    --engine-native-audit-fields-included \
    --apply-immediately
```

For Windows:

```
aws rds start-activity-stream ^
    --mode async ^
    --kms-key-id my-kms-key-arn ^
    --resource-arn my-instance-arn ^
    --engine-native-audit-fields-included ^
    --apply-immediately
```

## RDS API<a name="DBActivityStreams.Enabling-collapsible-section-E3"></a>

To start database activity streams for an Oracle DB instance, configure the instance using the [StartActivityStream](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_StartActivityStream.html) operation\.

Call the action with the parameters below:
+ `Region`
+ `KmsKeyId`
+ `ResourceArn`
+ `Mode`
+ `EngineNativeAuditFieldsIncluded`