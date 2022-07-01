# Security considerations for Amazon RDS Custom<a name="custom-security"></a>

When you create an Amazon RDS Custom for Oracle custom engine version \(CEV\) or an RDS Custom for SQL Server DB instance, RDS Custom creates an Amazon S3 bucket\. The S3 bucket stores files such as CEV artifacts, redo \(transaction\) logs, configuration items for the support perimeter, and AWS CloudTrail logs\.

You can make these S3 buckets more secure by using the global condition context keys to prevent the *confused deputy problem*\. For more information, see [Preventing cross\-service confused deputy problems](cross-service-confused-deputy-prevention.md)\.

The following RDS Custom for Oracle example shows the use of the `aws:SourceArn` and `aws:SourceAccount` global condition context keys in an S3 bucket policy\. For RDS Custom for Oracle, make sure to include the Amazon Resource Names \(ARNs\) for the CEVs and the DB instances\. For RDS Custom for SQL Server, make sure to include the ARN for the DB instances\.

```
...
{
  "Sid": "AWSRDSCustomForOracleInstancesObjectLevelAccess",
  "Effect": "Allow",
  "Principal": {
     "Service": "custom.rds.amazonaws.com"
  },
  "Action": [
     "s3:GetObject",
     "s3:GetObjectVersion",
     "s3:DeleteObject",
     "s3:DeleteObjectVersion",
     "s3:GetObjectRetention",
     "s3:BypassGovernanceRetention"
  ],
  "Resource": "arn:aws:s3:::do-not-delete-rds-custom-123456789012-us-east-2-c8a6f7/RDSCustomForOracle/Instances/*",
  "Condition": {
     "ArnLike": {
        "aws:SourceArn": [
            "arn:aws:rds:us-east-2:123456789012:db:*",
            "arn:aws:rds:us-east-2:123456789012:cev:*/*"
        ]
     },
     "StringEquals": {
        "aws:SourceAccount": "123456789012"
    }
  }
},
...
```