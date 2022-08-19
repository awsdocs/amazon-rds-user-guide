# Modifying a database activity stream<a name="DBActivityStreams.Modifying"></a>

You might want to customize your RDS for Oracle audit policy when your activity stream is started\. If you don't want to lose time and data by stopping your activity stream, you can change the *audit policy state* to either of the following settings:

**Locked \(default\)**  
The audit policies in your database are read\-only\.

**Unlocked**  
The audit policies in your database are read/write\.

The basic steps are as follows:

1. Modify the audit policy state to unlocked\.

1. Customize your audit policy\.

1. Modify the audit policy state to locked\.

## Console<a name="DBActivityStreams.Modifying-collapsible-section-E1"></a>

**To modify the audit policy state of your activity stream**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. For **Actions**, choose **Modify database activity stream**\. 

   The **Modify database activity stream: *name*** window appears, where *name* is your RDS instance\.

1. Choose either of the following options:  
**Locked**  
When you lock your audit policy, it becomes read\-only\. You can't edit your audit policy unless you unlock the policy or stop the activity stream\.  
**Unlocked**  
When you unlock your audit policy, it becomes read/write\. You can edit your audit policy while the activity stream is started\.

1. Choose **Modify DB activity stream**\.

   The status for the Oracle database shows **Configuring activity stream**\.

1. \(Optional\) Choose the DB instance link\. Then choose the **Configuration** tab\.

   The **Audit policy status** field shows one of the following values:
   + **Locked**
   + **Unlocked**
   + **Locking policy**
   + **Unlocking policy**

## AWS CLI<a name="DBActivityStreams.Modifying-collapsible-section-E2"></a>

To modify the activity stream state for an Oracle DB instance, use the [modify\-activity\-stream](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-activity-stream.html) AWS CLI command\.


****  

| Option | Required? | Description | 
| --- | --- | --- | 
|  `--resource-arn my-instance-ARN`  |  Yes  |  The Amazon Resource Name \(ARN\) of your RDS for Oracle DB instance\.  | 
|  `--audit-policy-state`  |  No  |  The new state of the audit policy for the database activity stream on your instance: `locked` or `unlocked`\.  | 

The following example unlocks the audit policy for the activity stream started on *my\-instance\-ARN*\.

For Linux, macOS, or Unix:

```
aws rds modify-activity-stream \
    --resource-arn my-instance-ARN \
    --audit-policy-state unlocked
```

For Windows:

```
aws rds modify-activity-stream ^
    --resource-arn my-instance-ARN ^
    --audit-policy-state unlocked
```

The following example describes the instance *my\-instance*\. The partial sample output shows that the audit policy is unlocked\.

```
aws rds describe-db-instances --db-instance-identifier my-instance

{
    "DBInstances": [
        {
            ...
            "Engine": "oracle-ee",
            ...
            "ActivityStreamStatus": "started",
            "ActivityStreamKmsKeyId": "ab12345e-1111-2bc3-12a3-ab1cd12345e",
            "ActivityStreamKinesisStreamName": "aws-rds-das-db-AB1CDEFG23GHIJK4LMNOPQRST",
            "ActivityStreamMode": "async",
            "ActivityStreamEngineNativeAuditFieldsIncluded": true, 
            "ActivityStreamPolicyStatus": "unlocked",
            ...
        }
    ]
}
```

## RDS API<a name="DBActivityStreams.Modifying-collapsible-section-E3"></a>

To modify the policy state of your database activity stream, use the [ModifyActivityStream](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyActivityStream.html) operation\.

Call the action with the parameters below:
+ `AuditPolicyState`
+ `ResourceArn`