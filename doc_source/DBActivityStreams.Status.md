# Getting the status of a database activity stream<a name="DBActivityStreams.Status"></a>

You can get the status of an activity stream for your RDS for Oracle DB instance using the console or AWS CLI\.

## Console<a name="DBActivityStreams.Status-collapsible-section-S1"></a>

**To get the status of a database activity stream**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**, and then choose the DB instance link\.

1. Choose the **Configuration** tab, and check **Database activity stream** for status\.

## AWS CLI<a name="DBActivityStreams.Status-collapsible-section-S2"></a>

You can get the activity stream configuration for a database as the response to a [describe\-db\-instances](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-instances.html) CLI request\.

The following example describes *my\-instance*\.

```
aws rds --region my-region describe-db-instances --db-instance-identifier my-db
```

The following example shows a JSON response\. The following fields are shown:
+ `ActivityStreamKinesisStreamName`
+ `ActivityStreamKmsKeyId`
+ `ActivityStreamStatus`
+ `ActivityStreamMode`
+ `ActivityStreamPolicyStatus`



```
{
    "DBInstances": [
        {
            ...
            "Engine": "oracle-ee",
            ...
            "ActivityStreamStatus": "starting",
            "ActivityStreamKmsKeyId": "ab12345e-1111-2bc3-12a3-ab1cd12345e",
            "ActivityStreamKinesisStreamName": "aws-rds-das-db-AB1CDEFG23GHIJK4LMNOPQRST",
            "ActivityStreamMode": "async",
            "ActivityStreamEngineNativeAuditFieldsIncluded": true, 
            "ActivityStreamPolicyStatus": locked",
            ...
        }
    ]
}
```

## RDS API<a name="DBActivityStreams.Status-collapsible-section-S3"></a>

You can get the activity stream configuration for a database as the response to a [DescribeDBInstances](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBInstances.html) operation\.