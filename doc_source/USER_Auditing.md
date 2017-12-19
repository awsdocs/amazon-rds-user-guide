# Logging Amazon RDS API Calls Using AWS CloudTrail<a name="USER_Auditing"></a>

AWS CloudTrail is a service that logs all Amazon RDS API calls made by or on behalf of your AWS account\. The logging information is stored in an Amazon S3 bucket\. You can use the information collected by CloudTrail to monitor activity for your Amazon RDS DB instances\. For example, you can determine whether a request completed successfully and which user made the request\. To learn more about CloudTrail, see the [AWS CloudTrail User Guide](http://docs.aws.amazon.com/awscloudtrail/latest/userguide/)\.

If an action is taken on behalf of your AWS account using the Amazon RDS console or the Amazon RDS command line interface, then AWS CloudTrail will log the action as calls made to the Amazon RDS API\. For example, if you use the Amazon RDS console to modify a DB instance, or call the AWS CLI [modify\-db\-instance](http://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) command, then the AWS CloudTrail log will show a call to the Amazon RDS API [ModifyDBInstance](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html) action\. For a list of the Amazon RDS API actions that are logged by AWS CloudTrail, go to [Amazon RDS API Reference](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/Welcome.html)\.

**Note**  
AWS CloudTrail only logs events for Amazon RDS API calls\. If you want to audit actions taken on your database that are not part of the Amazon RDS API, such as when a user connects to your database or when a change is made to your database schema, then you will need to use the monitoring capabilities provided by your DB engine\.

## Configuring CloudTrail Event Logging<a name="USER_Auditing.CloudTrail"></a>

CloudTrail creates audit trails in each region separately and stores them in an Amazon S3 bucket\. You can configure CloudTrail to use Amazon SNS to notify you when a log file is created, but that is optional\. CloudTrail will notify you frequently, so we recommend that you use Amazon SNS in conjunction with an Amazon SQS queue and handle notifications programmatically\.

You can enable CloudTrail using the AWS Management Console, AWS CLI, or API\. When you enable CloudTrail logging, you can have the CloudTrail service create an Amazon S3 bucket for you to store your log files\. For details, see [Creating and Updating Your Trail](http://docs.aws.amazon.com/awscloudtrail/latest/userguide/setupyourtrail.html) in the *AWS CloudTrail User Guide*\. The *AWS CloudTrail User Guide* also contains information on how to [aggregate CloudTrail logs from multiple regions into a single Amazon S3 bucket](http://docs.aws.amazon.com/awscloudtrail/latest/userguide/aggregatinglogs.html)\.

There is no cost to use the CloudTrail service\. However, standard rates for Amazon S3 usage apply as well as rates for Amazon SNS usage should you include that option\. For pricing details, see the [Amazon S3](http://aws.amazon.com/s3/pricing/) and [Amazon SNS](http://aws.amazon.com/sns/pricing/) pricing pages\.

## Amazon RDS Event Entries in CloudTrail Log Files<a name="USER_Auditing.CloudTrail_Events"></a>

CloudTrail log files contain event information formatted using JSON\. An event record represents a single AWS API call and includes information about the requested action, the user that requested the action, the date and time of the request, and so on\. 

CloudTrail log files include events for all AWS API calls for your AWS account, not just calls to the Amazon RDS API\. However, you can read the log files and scan for calls to the Amazon RDS API using the `eventName` element\.

The following example shows a CloudTrail log for a user that created a snapshot of a DB instance and then deleted that instance using the Amazon RDS console\. The console is identified by the `userAgent` element\. The requested API calls made by the console \(`CreateDBSnapshot` and `DeleteDBInstance`\) are found in the `eventName` element for each record\. Information about the user \(`Alice`\) can be found in the `userIdentity` element\. 

```
{
  Records:[
  {
    "awsRegion":"us-west-2",
    "eventName":"CreateDBSnapshot",
    "eventSource":"rds.amazonaws.com",
    "eventTime":"2014-01-14T16:23:49Z",
    "eventVersion":"1.0",
    "sourceIPAddress":"192.0.2.01",
    "userAgent":"AWS Console, aws-sdk-java\/unknown-version Linux\/2.6.18-kaos_fleet-1108-prod.2 Java_HotSpot(TM)_64-Bit_Server_VM\/24.45-b08",
    "userIdentity":
    {
      "accessKeyId":"AKIADQKE4SARGYLE",
      "accountId":"123456789012",
      "arn":"arn:aws:iam::123456789012:user/Alice",
      "principalId":"AIDAI2JXM4FBZZEXAMPLE",
      "sessionContext":
      {
        "attributes":
        {
          "creationDate":"2014-01-14T15:55:59Z",
          "mfaAuthenticated":false
        }
      },
      "type":"IAMUser",
      "userName":"Alice"
    }
  },
  {
    "awsRegion":"us-west-2",
    "eventName":"DeleteDBInstance",
    "eventSource":"rds.amazonaws.com",
    "eventTime":"2014-01-14T16:28:27Z",
    "eventVersion":"1.0",
    "sourceIPAddress":"192.0.2.01",
    "userAgent":"AWS Console, aws-sdk-java\/unknown-version Linux\/2.6.18-kaos_fleet-1108-prod.2 Java_HotSpot(TM)_64-Bit_Server_VM\/24.45-b08",
    "userIdentity":
    {
      "accessKeyId":"AKIADQKE4SARGYLE",
      "accountId":"123456789012",
      "arn":"arn:aws:iam::123456789012:user/Alice",
      "principalId":"AIDAI2JXM4FBZZEXAMPLE",
      "sessionContext":
      {
        "attributes":
        {
          "creationDate":"2014-01-14T15:55:59Z",
          "mfaAuthenticated":false
        }
      },
      "type":"IAMUser",
      "userName":"Alice"
    }
  }
  ]
}
```

For more information about the different elements and values in CloudTrail log files, see [CloudTrail Event Reference](http://docs.aws.amazon.com/awscloudtrail/latest/userguide/eventreference.html) in the *AWS CloudTrail User Guide*\.

You may also want to make use of one of the Amazon partner solutions that integrate with CloudTrail to read and analyze your CloudTrail log files\. For options, see the [AWS partners](http://aws.amazon.com/cloudtrail/partners/) page\.