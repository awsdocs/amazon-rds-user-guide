# Exporting DB snapshot data to Amazon S3<a name="USER_ExportSnapshot"></a>

You can export DB snapshot data to an Amazon S3 bucket\. The export process runs in the background and doesn't affect the performance of your active DB instance\.

When you export a DB snapshot, Amazon RDS extracts data from the snapshot and stores it in an Amazon S3 bucket\. The data is stored in an Apache Parquet format that is compressed and consistent\.

You can export all types of DB snapshots—including manual snapshots, automated system snapshots, and snapshots created by the AWS Backup service\. By default, all data in the snapshot is exported\. However, you can choose to export specific sets of databases, schemas, or tables\.

After the data is exported, you can analyze the exported data directly through tools like Amazon Athena or Amazon Redshift Spectrum\. For more information on using Athena to read Parquet data, see [Parquet SerDe](https://docs.aws.amazon.com/athena/latest/ug/parquet-serde.html) in the *Amazon Athena User Guide*\. For more information on using Redshift Spectrum to read Parquet data, see [COPY from columnar data formats](https://docs.aws.amazon.com/redshift/latest/dg/copy-usage_notes-copy-from-columnar.html) in the *Amazon Redshift Database Developer Guide*\.

**Topics**
+ [Region and version availability](#USER_ExportSnapshot.RegionVersionAvailability)
+ [Limitations](#USER_ExportSnapshot.Limits)
+ [Overview of exporting snapshot data](#USER_ExportSnapshot.Overview)
+ [Setting up access to an Amazon S3 bucket](#USER_ExportSnapshot.Setup)
+ [Exporting a DB snapshot to an Amazon S3 bucket](#USER_ExportSnapshot.Exporting)
+ [Monitoring snapshot exports](#USER_ExportSnapshot.Monitoring)
+ [Canceling a snapshot export task](#USER_ExportSnapshot.Canceling)
+ [Failure messages for Amazon S3 export tasks](#USER_ExportSnapshot.failure-msg)
+ [Troubleshooting PostgreSQL permissions errors](#USER_ExportSnapshot.postgres-permissions)
+ [File naming convention](#USER_ExportSnapshot.FileNames)
+ [Data conversion when exporting to an Amazon S3 bucket](#USER_ExportSnapshot.data-types)

## Region and version availability<a name="USER_ExportSnapshot.RegionVersionAvailability"></a>

Feature availability and support varies across specific versions of each database engine and across AWS Regions\. For more information on version and Region availability with exporting snapshots to S3, see [Export snapshots to S3](Concepts.RDS_Fea_Regions_DB-eng.Feature.ExportSnapshotToS3.md)\.

## Limitations<a name="USER_ExportSnapshot.Limits"></a>

Exporting DB snapshot data to Amazon S3 has the following limitations:
+ Exporting snapshots from DB instances that use magnetic storage isn't supported\.
+ The following characters in the S3 file path are converted to underscores \(\_\) during export:

  ```
  \ ` " (space)
  ```
+ If a database, schema, or table has characters in its name other than the following, partial export isn't supported\. However, you can export the entire DB snapshot\.
  + Latin letters \(A–Z\)
  + Digits \(0–9\)
  + Dollar symbol \($\)
  + Underscore \(\_\)
+ Spaces \( \) and certain characters aren't supported in database table column names\. Tables with the following characters in column names are skipped during export:

  ```
  , ; { } ( ) \n \t = (space)
  ```
+ Tables with slashes \(/\) in their names are skipped during export\.
+ If the data contains a large object, such as a BLOB or CLOB, that is close to or greater than 500 MB, then the export fails\.
+ If a table contains a large row that is close to or greater than 2 GB, then the table is skipped during export\.
+ We strongly recommend that you use a unique name for each export task\. If you don't use a unique task name, you might receive the following error message:

  ExportTaskAlreadyExistsFault: An error occurred \(ExportTaskAlreadyExists\) when calling the StartExportTask operation: The export task with the ID *xxxxx* already exists\.
+ You can delete a snapshot while you're exporting its data to S3, but you're still charged for the storage costs for that snapshot until the export task has completed\.
+ You can't restore exported snapshot data from S3 to a new DB instance\.

## Overview of exporting snapshot data<a name="USER_ExportSnapshot.Overview"></a>

You use the following process to export DB snapshot data to an Amazon S3 bucket\. For more details, see the following sections\.

1. Identify the snapshot to export\.

   Use an existing automated or manual snapshot, or create a manual snapshot of a DB instance\.

1. Set up access to the Amazon S3 bucket\.

   A *bucket* is a container for Amazon S3 objects or files\. To provide the information to access a bucket, take the following steps:

   1. Identify the S3 bucket where the snapshot is to be exported to\. The S3 bucket must be in the same AWS Region as the snapshot\. For more information, see [Identifying the Amazon S3 bucket for export](#USER_ExportSnapshot.SetupBucket)\.

   1. Create an AWS Identity and Access Management \(IAM\) role that grants the snapshot export task access to the S3 bucket\. For more information, see [Providing access to an Amazon S3 bucket using an IAM role](#USER_ExportSnapshot.SetupIAMRole)\. 

1. Create a symmetric encryption AWS KMS key for the server\-side encryption\. The KMS key is used by the snapshot export task to set up AWS KMS server\-side encryption when writing the export data to S3\. The KMS key policy must include both the `kms:Encrypt` and `kms:Decrypt` permissions\. For more information on using KMS keys in Amazon RDS, see [AWS KMS key management](Overview.Encryption.Keys.md)\.

   If you have a deny statement in your KMS key policy, make sure to explicitly exclude the AWS service principal `export.rds.amazonaws.com`\.

   You can use a KMS key within your AWS account, or you can use a cross\-account KMS key\. For more information, see [Using a cross\-account AWS KMS key for encrypting Amazon S3 exports](#USER_ExportSnapshot.CMK)\.

1. Export the snapshot to Amazon S3 using the console or the `start-export-task` CLI command\. For more information, see [Exporting a DB snapshot to an Amazon S3 bucket](#USER_ExportSnapshot.Exporting)\. 

1. To access your exported data in the Amazon S3 bucket, see [Uploading, downloading, and managing objects](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/upload-download-objects.html) in the *Amazon Simple Storage Service User Guide*\.

## Setting up access to an Amazon S3 bucket<a name="USER_ExportSnapshot.Setup"></a>

To export DB snapshot data to an Amazon S3 file, you first give the snapshot permission to access the Amazon S3 bucket\. You then create an IAM role to allow the Amazon RDS service to write to the Amazon S3 bucket\.

**Topics**
+ [Identifying the Amazon S3 bucket for export](#USER_ExportSnapshot.SetupBucket)
+ [Providing access to an Amazon S3 bucket using an IAM role](#USER_ExportSnapshot.SetupIAMRole)
+ [Using a cross\-account Amazon S3 bucket](#USER_ExportSnapshot.Setup.XAcctBucket)
+ [Using a cross\-account AWS KMS key for encrypting Amazon S3 exports](#USER_ExportSnapshot.CMK)

### Identifying the Amazon S3 bucket for export<a name="USER_ExportSnapshot.SetupBucket"></a>

Identify the Amazon S3 bucket to export the DB snapshot to\. Use an existing S3 bucket or create a new S3 bucket\.

**Note**  
The S3 bucket to export to must be in the same AWS Region as the snapshot\.

For more information about working with Amazon S3 buckets, see the following in the *Amazon Simple Storage Service User Guide*:
+ [ How do I view the properties for an S3 bucket?](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/view-bucket-properties.html)
+ [ How do I enable default encryption for an Amazon S3 bucket?](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/default-bucket-encryption.html)
+ [ How do I create an S3 bucket?](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/create-bucket.html)

### Providing access to an Amazon S3 bucket using an IAM role<a name="USER_ExportSnapshot.SetupIAMRole"></a>

Before you export DB snapshot data to Amazon S3, give the snapshot export tasks write\-access permission to the Amazon S3 bucket\. 

To grant this permission, create an IAM policy that provides access to the bucket, then create an IAM role and attach the policy to the role\. You later assign the IAM role to your snapshot export task\.

**Important**  
If you plan to use the AWS Management Console to export your snapshot, you can choose to create the IAM policy and the role automatically when you export the snapshot\. For instructions, see [Exporting a DB snapshot to an Amazon S3 bucket](#USER_ExportSnapshot.Exporting)\.

**To give DB snapshot tasks access to Amazon S3**

1. Create an IAM policy\. This policy provides the bucket and object permissions that allow your snapshot export task to access Amazon S3\. 

   In the policy, include the following required actions to allow the transfer of files from Amazon RDS to an S3 bucket: 
   + `s3:PutObject*`
   + `s3:GetObject*` 
   + `s3:ListBucket` 
   + `s3:DeleteObject*`
   +  `s3:GetBucketLocation`

   In the policy, include the following resources to identify the S3 bucket and objects in the bucket\. The following list of resources shows the Amazon Resource Name \(ARN\) format for accessing Amazon S3\.
   + `arn:aws:s3:::your-s3-bucket`
   + `arn:aws:s3:::your-s3-bucket/*`

   For more information on creating an IAM policy for Amazon RDS, see [Creating and using an IAM policy for IAM database access](UsingWithRDS.IAMDBAuth.IAMPolicy.md)\. See also [Tutorial: Create and attach your first customer managed policy](https://docs.aws.amazon.com/IAM/latest/UserGuide/tutorial_managed-policies.html) in the *IAM User Guide*\.

   The following AWS CLI command creates an IAM policy named `ExportPolicy` with these options\. It grants access to a bucket named `your-s3-bucket`\. 
**Note**  
After you create the policy, note the ARN of the policy\. You need the ARN for a subsequent step when you attach the policy to an IAM role\. 

   ```
   aws iam create-policy  --policy-name ExportPolicy --policy-document '{
       "Version": "2012-10-17",
       "Statement": [
           {
               "Sid": "ExportPolicy",
               "Effect": "Allow",
               "Action": [
                   "s3:PutObject*",
                   "s3:ListBucket",
                   "s3:GetObject*",
                   "s3:DeleteObject*",
                   "s3:GetBucketLocation"
               ],
               "Resource": [
                   "arn:aws:s3:::your-s3-bucket",
                   "arn:aws:s3:::your-s3-bucket/*"
               ]
           }
       ]
   }'
   ```

1. Create an IAM role, so that Amazon RDS can assume this IAM role on your behalf to access your Amazon S3 buckets\. For more information, see [Creating a role to delegate permissions to an IAM user](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-user.html) in the *IAM User Guide*\.

   The following example shows using the AWS CLI command to create a role named `rds-s3-export-role`\.

   ```
   aws iam create-role  --role-name rds-s3-export-role  --assume-role-policy-document '{
        "Version": "2012-10-17",
        "Statement": [
          {
            "Effect": "Allow",
            "Principal": {
               "Service": "export.rds.amazonaws.com"
             },
            "Action": "sts:AssumeRole"
          }
        ] 
      }'
   ```

1. Attach the IAM policy that you created to the IAM role that you created\.

   The following AWS CLI command attaches the policy created earlier to the role named `rds-s3-export-role`\. Replace `your-policy-arn` with the policy ARN that you noted in an earlier step\. 

   ```
   aws iam attach-role-policy  --policy-arn your-policy-arn  --role-name rds-s3-export-role
   ```

### Using a cross\-account Amazon S3 bucket<a name="USER_ExportSnapshot.Setup.XAcctBucket"></a>

You can use Amazon S3 buckets across AWS accounts\. To use a cross\-account bucket, add a bucket policy to allow access to the IAM role that you're using for the S3 exports\. For more information, see [Example 2: Bucket owner granting cross\-account bucket permissions](https://docs.aws.amazon.com/AmazonS3/latest/userguide/example-walkthroughs-managing-access-example2.html)\.To add a bucket policy
+ Attach a bucket policy to your bucket, as shown in the following example\.

  ```
  {
      "Version": "2012-10-17",
      "Statement": [
          {
              "Effect": "Allow",
              "Principal": {
                  "AWS": "arn:aws:iam::123456789012:role/Admin"
              },
              "Action": [
                  "s3:PutObject*",
                  "s3:ListBucket",
                  "s3:GetObject*",
                  "s3:DeleteObject*",
                  "s3:GetBucketLocation"
              ],
              "Resource": [
                  "arn:aws:s3:::mycrossaccountbucket",
                  "arn:aws:s3:::mycrossaccountbucket/*"
              ]
          }
      ]
  }
  ```

### Using a cross\-account AWS KMS key for encrypting Amazon S3 exports<a name="USER_ExportSnapshot.CMK"></a>

You can use a cross\-account AWS KMS key to encrypt Amazon S3 exports\. First, you add a key policy to the local account, then you add IAM policies in the external account\. For more information, see [Allowing users in other accounts to use a KMS key](https://docs.aws.amazon.com/kms/latest/developerguide/key-policy-modifying-external-accounts.html)\.

**To use a cross\-account KMS key**

1. Add a key policy to the local account\.

   The following example gives `ExampleRole` and `ExampleUser` in the external account 444455556666 permissions in the local account 123456789012\.

   ```
   {
       "Sid": "Allow an external account to use this KMS key",
       "Effect": "Allow",
       "Principal": {
           "AWS": [
               "arn:aws:iam::444455556666:role/ExampleRole",
               "arn:aws:iam::444455556666:user/ExampleUser"
           ]
       },
       "Action": [
           "kms:Encrypt",
           "kms:Decrypt",
           "kms:ReEncrypt*",
           "kms:GenerateDataKey*",
           "kms:CreateGrant",
           "kms:DescribeKey",
           "kms:RetireGrant"
       ],
       "Resource": "*"
   }
   ```

1. Add IAM policies to the external account\.

   The following example IAM policy allows the principal to use the KMS key in account 123456789012 for cryptographic operations\. To give this permission to `ExampleRole` and `ExampleUser` in account 444455556666, [attach the policy](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_managed-using.html#attach-managed-policy-console) to them in that account\.

   ```
   {
       "Sid": "Allow use of KMS key in account 123456789012",
       "Effect": "Allow",
       "Action": [
           "kms:Encrypt",
           "kms:Decrypt",
           "kms:ReEncrypt*",
           "kms:GenerateDataKey*",
           "kms:CreateGrant",
           "kms:DescribeKey",
           "kms:RetireGrant"
       ],
       "Resource": "arn:aws:kms:us-west-2:123456789012:key/1234abcd-12ab-34cd-56ef-1234567890ab"
   }
   ```

## Exporting a DB snapshot to an Amazon S3 bucket<a name="USER_ExportSnapshot.Exporting"></a>

You can have up to five concurrent DB snapshot export tasks in progress per AWS account\.

**Note**  
Exporting RDS snapshots can take a while depending on your database type and size\. The export task first restores and scales the entire database before extracting the data to Amazon S3\. The task's progress during this phase displays as **Starting**\. When the task switches to exporting data to S3, progress displays as **In progress**\.  
The time it takes for the export to complete depends on the data stored in the database\. For example, tables with well\-distributed numeric primary key or index columns export the fastest\. Tables that don't contain a column suitable for partitioning and tables with only one index on a string\-based column take longer\. This longer export time occurs because the export uses a slower single\-threaded process\. 

You can export a DB snapshot to Amazon S3 using the AWS Management Console, the AWS CLI, or the RDS API\.

If you use a Lambda function to export a snapshot, add the `kms:DescribeKey` action to the Lambda function policy\. For more information, see [AWS Lambda permissions](https://docs.aws.amazon.com/lambda/latest/dg/lambda-permissions.html)\.

### Console<a name="USER_ExportSnapshot.ExportConsole"></a>

The **Export to Amazon S3** console option appears only for snapshots that can be exported to Amazon S3\. A snapshot might not be available for export because of the following reasons:
+ The DB engine isn't supported for S3 export\.
+ The DB instance version isn't supported for S3 export\.
+ S3 export isn't supported in the AWS Region where the snapshot was created\.

**To export a DB snapshot**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Snapshots**\.

1. From the tabs, choose the type of snapshot that you want to export\.

1. In the list of snapshots, choose the snapshot that you want to export\.

1. For **Actions**, choose **Export to Amazon S3**\.

   The **Export to Amazon S3** window appears\.

1. For **Export identifier**, enter a name to identify the export task\. This value is also used for the name of the file created in the S3 bucket\.

1. Choose the data to be exported:
   + Choose **All** to export all data in the snapshot\.
   + Choose **Partial** to export specific parts of the snapshot\. To identify which parts of the snapshot to export, enter one or more databases, schemas, or tables for **Identifiers**, separated by spaces\.

     Use the following format:

     ```
     database[.schema][.table] database2[.schema2][.table2] ... databasen[.scheman][.tablen]
     ```

     For example:

     ```
     mydatabase mydatabase2.myschema1 mydatabase2.myschema2.mytable1 mydatabase2.myschema2.mytable2
     ```

1. For **S3 bucket**, choose the bucket to export to\.

   To assign the exported data to a folder path in the S3 bucket, enter the optional path for **S3 prefix**\.

1. For **IAM role**, either choose a role that grants you write access to your chosen S3 bucket, or create a new role\. 
   + If you created a role by following the steps in [Providing access to an Amazon S3 bucket using an IAM role](#USER_ExportSnapshot.SetupIAMRole), choose that role\.
   + If you didn't create a role that grants you write access to your chosen S3 bucket, then choose **Create a new role** to create the role automatically\. Next, enter a name for the role in **IAM role name**\.

1. For **AWS KMS key**, enter the ARN for the key to use for encrypting the exported data\.

1. Choose **Export to Amazon S3**\.

### AWS CLI<a name="USER_ExportSnapshot.ExportCLI"></a>

To export a DB snapshot to Amazon S3 using the AWS CLI, use the [start\-export\-task](https://docs.aws.amazon.com/cli/latest/reference/rds/start-export-task.html) command with the following required options:
+ `--export-task-identifier` 
+ `--source-arn` 
+ `--s3-bucket-name` 
+ `--iam-role-arn` 
+ `--kms-key-id` 

In the following examples, the snapshot export task is named *my\-snapshot\-export*, which exports a snapshot to an S3 bucket named *my\-export\-bucket*\.

**Example**  
For Linux, macOS, or Unix:  

```
1. aws rds start-export-task \
2.     --export-task-identifier my-snapshot-export \
3.     --source-arn arn:aws:rds:AWS_Region:123456789012:snapshot:snapshot-name \
4.     --s3-bucket-name my-export-bucket \
5.     --iam-role-arn iam-role \
6.     --kms-key-id my-key
```
For Windows:  

```
1. aws rds start-export-task ^
2.     --export-task-identifier my-snapshot-export ^
3.     --source-arn arn:aws:rds:AWS_Region:123456789012:snapshot:snapshot-name ^
4.     --s3-bucket-name my-export-bucket ^
5.     --iam-role-arn iam-role ^
6.     --kms-key-id my-key
```
Sample output follows\.  

```
{
    "Status": "STARTING", 
    "IamRoleArn": "iam-role", 
    "ExportTime": "2019-08-12T01:23:53.109Z", 
    "S3Bucket": "my-export-bucket", 
    "PercentProgress": 0, 
    "KmsKeyId": "my-key", 
    "ExportTaskIdentifier": "my-snapshot-export", 
    "TotalExtractedDataInGB": 0, 
    "TaskStartTime": "2019-11-13T19:46:00.173Z", 
    "SourceArn": "arn:aws:rds:AWS_Region:123456789012:snapshot:snapshot-name"
}
```
To provide a folder path in the S3 bucket for the snapshot export, include the `--s3-prefix` option in the [start\-export\-task](https://docs.aws.amazon.com/cli/latest/reference/rds/start-export-task.html) command\.

### RDS API<a name="USER_ExportSnapshot.ExportAPI"></a>

To export a DB snapshot to Amazon S3 using the Amazon RDS API, use the [StartExportTask](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_StartExportTask.html) operation with the following required parameters:
+ `ExportTaskIdentifier`
+ `SourceArn`
+ `S3BucketName`
+ `IamRoleArn`
+ `KmsKeyId`

## Monitoring snapshot exports<a name="USER_ExportSnapshot.Monitoring"></a>

You can monitor DB snapshot exports using the AWS Management Console, the AWS CLI, or the RDS API\.

### Console<a name="USER_ExportSnapshot.MonitorConsole"></a>

**To monitor DB snapshot exports**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Snapshots**\.

1. To view the list of snapshot exports, choose the **Exports in Amazon S3** tab\.

1. To view information about a specific snapshot export, choose the export task\.

### AWS CLI<a name="USER_ExportSnapshot.MonitorCLI"></a>

To monitor DB snapshot exports using the AWS CLI, use the [ describe\-export\-tasks](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-export-tasks.html) command\.

The following example shows how to display current information about all of your snapshot exports\.

**Example**  

```
 1. aws rds describe-export-tasks
 2. 
 3. {
 4.     "ExportTasks": [
 5.         {
 6.             "Status": "CANCELED",
 7.             "TaskEndTime": "2019-11-01T17:36:46.961Z",
 8.             "S3Prefix": "something",
 9.             "ExportTime": "2019-10-24T20:23:48.364Z",
10.             "S3Bucket": "examplebucket",
11.             "PercentProgress": 0,
12.             "KmsKeyId": "arn:aws:kms:AWS_Region:123456789012:key/K7MDENG/bPxRfiCYEXAMPLEKEY",
13.             "ExportTaskIdentifier": "anewtest",
14.             "IamRoleArn": "arn:aws:iam::123456789012:role/export-to-s3",
15.             "TotalExtractedDataInGB": 0,
16.             "TaskStartTime": "2019-10-25T19:10:58.885Z",
17.             "SourceArn": "arn:aws:rds:AWS_Region:123456789012:snapshot:parameter-groups-test"
18.         },
19. {
20.             "Status": "COMPLETE",
21.             "TaskEndTime": "2019-10-31T21:37:28.312Z",
22.             "WarningMessage": "{\"skippedTables\":[],\"skippedObjectives\":[],\"general\":[{\"reason\":\"FAILED_TO_EXTRACT_TABLES_LIST_FOR_DATABASE\"}]}",
23.             "S3Prefix": "",
24.             "ExportTime": "2019-10-31T06:44:53.452Z",
25.             "S3Bucket": "examplebucket1",
26.             "PercentProgress": 100,
27.             "KmsKeyId": "arn:aws:kms:AWS_Region:123456789012:key/2Zp9Utk/h3yCo8nvbEXAMPLEKEY",
28.             "ExportTaskIdentifier": "thursday-events-test", 
29.             "IamRoleArn": "arn:aws:iam::123456789012:role/export-to-s3",
30.             "TotalExtractedDataInGB": 263,
31.             "TaskStartTime": "2019-10-31T20:58:06.998Z",
32.             "SourceArn": "arn:aws:rds:AWS_Region:123456789012:snapshot:rds:example-1-2019-10-31-06-44"
33.         },
34.         {
35.             "Status": "FAILED",
36.             "TaskEndTime": "2019-10-31T02:12:36.409Z",
37.             "FailureCause": "The S3 bucket edgcuc-export isn't located in the current AWS Region. Please, review your S3 bucket name and retry the export.",
38.             "S3Prefix": "",
39.             "ExportTime": "2019-10-30T06:45:04.526Z",
40.             "S3Bucket": "examplebucket2",
41.             "PercentProgress": 0,
42.             "KmsKeyId": "arn:aws:kms:AWS_Region:123456789012:key/2Zp9Utk/h3yCo8nvbEXAMPLEKEY",
43.             "ExportTaskIdentifier": "wednesday-afternoon-test",
44.             "IamRoleArn": "arn:aws:iam::123456789012:role/export-to-s3",
45.             "TotalExtractedDataInGB": 0,
46.             "TaskStartTime": "2019-10-30T22:43:40.034Z",
47.             "SourceArn": "arn:aws:rds:AWS_Region:123456789012:snapshot:rds:example-1-2019-10-30-06-45"
48.         }
49.     ]
50. }
```
To display information about a specific snapshot export, include the `--export-task-identifier` option with the `describe-export-tasks` command\. To filter the output, include the `--Filters` option\. For more options, see the [ describe\-export\-tasks](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-export-tasks.html) command\.

### RDS API<a name="USER_ExportSnapshot.MonitorAPI"></a>

To display information about DB snapshot exports using the Amazon RDS API, use the [DescribeExportTasks](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeExportTasks.html) operation\.

To track completion of the export workflow or to initiate another workflow, you can subscribe to Amazon Simple Notification Service topics\. For more information on Amazon SNS, see [Working with Amazon RDS event notification](USER_Events.md)\.

## Canceling a snapshot export task<a name="USER_ExportSnapshot.Canceling"></a>

You can cancel a DB snapshot export task using the AWS Management Console, the AWS CLI, or the RDS API\.

**Note**  
Canceling a snapshot export task doesn't remove any data that was exported to Amazon S3\. For information about how to delete the data using the console, see [ How do I delete objects from an S3 bucket?](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/delete-objects.html) To delete the data using the CLI, use the [delete\-object](https://docs.aws.amazon.com/cli/latest/reference/s3api/delete-object.html) command\.

### Console<a name="USER_ExportSnapshot.CancelConsole"></a>

**To cancel a snapshot export task**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Snapshots**\.

1. Choose the **Exports in Amazon S3** tab\.

1. Choose the snapshot export task that you want to cancel\.

1. Choose **Cancel**\.

1. Choose **Cancel export task** on the confirmation page\.

 

### AWS CLI<a name="USER_ExportSnapshot.CancelCLI"></a>

To cancel a snapshot export task using the AWS CLI, use the [cancel\-export\-task](https://docs.aws.amazon.com/cli/latest/reference/rds/cancel-export-task.html) command\. The command requires the `--export-task-identifier` option\.

**Example**  

```
 1. aws rds cancel-export-task --export-task-identifier my_export
 2. {
 3.     "Status": "CANCELING", 
 4.     "S3Prefix": "", 
 5.     "ExportTime": "2019-08-12T01:23:53.109Z", 
 6.     "S3Bucket": "examplebucket", 
 7.     "PercentProgress": 0, 
 8.     "KmsKeyId": "arn:aws:kms:AWS_Region:123456789012:key/K7MDENG/bPxRfiCYEXAMPLEKEY", 
 9.     "ExportTaskIdentifier": "my_export", 
10.     "IamRoleArn": "arn:aws:iam::123456789012:role/export-to-s3", 
11.     "TotalExtractedDataInGB": 0, 
12.     "TaskStartTime": "2019-11-13T19:46:00.173Z", 
13.     "SourceArn": "arn:aws:rds:AWS_Region:123456789012:snapshot:export-example-1"
14. }
```

### RDS API<a name="USER_ExportSnapshot.CancelAPI"></a>

To cancel a snapshot export task using the Amazon RDS API, use the [CancelExportTask](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CancelExportTask.html) operation with the `ExportTaskIdentifier` parameter\.

## Failure messages for Amazon S3 export tasks<a name="USER_ExportSnapshot.failure-msg"></a>

The following table describes the messages that are returned when Amazon S3 export tasks fail\.


| Failure message | Description | 
| --- | --- | 
| An unknown internal error occurred\. |  The task has failed because of an unknown error, exception, or failure\.  | 
| An unknown internal error occurred writing the export task's metadata to the S3 bucket \[bucket name\]\. |  The task has failed because of an unknown error, exception, or failure\.  | 
| The RDS export failed to write the export task's metadata because it can't assume the IAM role \[role ARN\]\. |  The export task assumes your IAM role to validate whether it is allowed to write metadata to your S3 bucket\. If the task can't assume your IAM role, it fails\.  | 
| The RDS export failed to write the export task's metadata to the S3 bucket \[bucket name\] using the IAM role \[role ARN\] with the KMS key \[key ID\]\. Error code: \[error code\] |  One or more permissions are missing, so the export task can't access the S3 bucket\. This failure message is raised when receiving one of the following error codes: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_ExportSnapshot.html) These error codes indicate settings are misconfigured for the IAM role, S3 bucket, or KMS key\.  | 
| The IAM role \[role ARN\] isn't authorized to call \[S3 action\] on the S3 bucket \[bucket name\]\. Review your permissions and retry the export\. |  The IAM policy is misconfigured\. Permission for the specific S3 action on the S3 bucket is missing, which causes the export task to fail\.  | 
| KMS key check failed\. Check the credentials on your KMS key and try again\. | The KMS key credential check failed\. | 
| S3 credential check failed\. Check the permissions on your S3 bucket and IAM policy\. | The S3 credential check failed\. | 
| The S3 bucket \[bucket name\] isn't valid\. Either it isn't located in the current AWS Region or it doesn't exist\. Review your S3 bucket name and retry the export\. | The S3 bucket is invalid\. | 
| The S3 bucket \[bucket name\] isn't located in the current AWS Region\. Review your S3 bucket name and retry the export\. | The S3 bucket is in the wrong AWS Region\. | 

## Troubleshooting PostgreSQL permissions errors<a name="USER_ExportSnapshot.postgres-permissions"></a>

When exporting PostgreSQL databases to Amazon S3, you might see a `PERMISSIONS_DO_NOT_EXIST` error stating that certain tables were skipped\. This error usually occurs when the superuser, which you specified when creating the DB instance, doesn't have permissions to access those tables\.

To fix this error, run the following command:

```
GRANT ALL PRIVILEGES ON ALL TABLES IN SCHEMA schema_name TO superuser_name
```

For more information on superuser privileges, see [Master user account privileges](UsingWithRDS.MasterAccounts.md)\.

## File naming convention<a name="USER_ExportSnapshot.FileNames"></a>

Exported data for specific tables is stored in the format `base_prefix/files`, where the base prefix is the following:

```
export_identifier/database_name/schema_name.table_name/
```

For example:

```
export-1234567890123-459/rdststdb/rdststdb.DataInsert_7ADB5D19965123A2/
```

There are two conventions for how files are named\. The current convention is the following:

```
partition_index/part-00000-random_uuid.format-based_extension
```

For example:

```
1/part-00000-c5a881bb-58ff-4ee6-1111-b41ecff340a3-c000.gz.parquet
2/part-00000-d7a881cc-88cc-5ab7-2222-c41ecab340a4-c000.gz.parquet
3/part-00000-f5a991ab-59aa-7fa6-3333-d41eccd340a7-c000.gz.parquet
```

The older convention is the following:

```
part-partition_index-random_uuid.format-based_extension
```

For example:

```
part-00000-c5a881bb-58ff-4ee6-1111-b41ecff340a3-c000.gz.parquet
part-00001-d7a881cc-88cc-5ab7-2222-c41ecab340a4-c000.gz.parquet
part-00002-f5a991ab-59aa-7fa6-3333-d41eccd340a7-c000.gz.parquet
```

The file naming convention is subject to change\. Therefore, when reading target tables, we recommend that you read everything inside the base prefix for the table\.

## Data conversion when exporting to an Amazon S3 bucket<a name="USER_ExportSnapshot.data-types"></a>

When you export a DB snapshot to an Amazon S3 bucket, Amazon RDS converts data to, exports data in, and stores data in the Parquet format\. For more information about Parquet, see the [Apache Parquet](https://parquet.apache.org/docs/) website\.

Parquet stores all data as one of the following primitive types:
+ BOOLEAN
+ INT32
+ INT64
+ INT96
+ FLOAT
+ DOUBLE
+ BYTE\_ARRAY – A variable\-length byte array, also known as binary
+ FIXED\_LEN\_BYTE\_ARRAY – A fixed\-length byte array used when the values have a constant size

The Parquet data types are few to reduce the complexity of reading and writing the format\. Parquet provides logical types for extending primitive types\. A *logical type* is implemented as an annotation with the data in a `LogicalType` metadata field\. The logical type annotation explains how to interpret the primitive type\. 

When the `STRING` logical type annotates a `BYTE_ARRAY` type, it indicates that the byte array should be interpreted as a UTF\-8 encoded character string\. After an export task completes, Amazon RDS notifies you if any string conversion occurred\. The underlying data exported is always the same as the data from the source\. However, due to the encoding difference in UTF\-8, some characters might appear different from the source when read in tools such as Athena\.

For more information, see [Parquet logical type definitions](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md) in the Parquet documentation\.

**Topics**
+ [MySQL and MariaDB data type mapping to Parquet](#USER_ExportSnapshot.data-types.MySQL)
+ [PostgreSQL data type mapping to Parquet](#USER_ExportSnapshot.data-types.PostgreSQL)

### MySQL and MariaDB data type mapping to Parquet<a name="USER_ExportSnapshot.data-types.MySQL"></a>

The following table shows the mapping from MySQL and MariaDB data types to Parquet data types when data is converted and exported to Amazon S3\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_ExportSnapshot.html)

### PostgreSQL data type mapping to Parquet<a name="USER_ExportSnapshot.data-types.PostgreSQL"></a>

The following table shows the mapping from PostgreSQL data types to Parquet data types when data is converted and exported to Amazon S3\.


| PostgreSQL data type | Parquet primitive type | Logical type annotation | Mapping notes | 
| --- | --- | --- | --- | 
| Numeric data types | 
| BIGINT | INT64 |  |   | 
| BIGSERIAL | INT64 |  |   | 
| DECIMAL | BYTE\_ARRAY | STRING | A DECIMAL type is converted to a string in a BYTE\_ARRAY type and encoded as UTF8\.This conversion is to avoid complications due to data precision and data values that are not a number \(NaN\)\. | 
| DOUBLE PRECISION | DOUBLE |  |   | 
| INTEGER | INT32 |  |   | 
| MONEY | BYTE\_ARRAY | STRING |   | 
| REAL | FLOAT |  |   | 
| SERIAL | INT32 |  |   | 
| SMALLINT | INT32 | INT\_16 |   | 
| SMALLSERIAL | INT32 | INT\_16 |   | 
| String and related data types | 
| ARRAY | BYTE\_ARRAY | STRING |  An array is converted to a string and encoded as BINARY \(UTF8\)\. This conversion is to avoid complications due to data precision, data values that are not a number \(NaN\), and time data values\.  | 
| BIT | BYTE\_ARRAY | STRING |   | 
| BIT VARYING | BYTE\_ARRAY | STRING |   | 
| BYTEA | BINARY |  |   | 
| CHAR | BYTE\_ARRAY | STRING |   | 
| CHAR\(N\) | BYTE\_ARRAY | STRING |   | 
| ENUM | BYTE\_ARRAY | STRING |   | 
| NAME | BYTE\_ARRAY | STRING |   | 
| TEXT | BYTE\_ARRAY | STRING |   | 
| TEXT SEARCH | BYTE\_ARRAY | STRING |   | 
| VARCHAR\(N\) | BYTE\_ARRAY | STRING |   | 
| XML | BYTE\_ARRAY | STRING |   | 
| Date and time data types | 
| DATE | BYTE\_ARRAY | STRING |   | 
| INTERVAL | BYTE\_ARRAY | STRING |   | 
| TIME | BYTE\_ARRAY | STRING |  | 
| TIME WITH TIME ZONE | BYTE\_ARRAY | STRING |  | 
| TIMESTAMP | BYTE\_ARRAY | STRING |  | 
| TIMESTAMP WITH TIME ZONE | BYTE\_ARRAY | STRING |  | 
| Geometric data types | 
| BOX | BYTE\_ARRAY | STRING |   | 
| CIRCLE | BYTE\_ARRAY | STRING |   | 
| LINE | BYTE\_ARRAY | STRING |   | 
| LINESEGMENT | BYTE\_ARRAY | STRING |   | 
| PATH | BYTE\_ARRAY | STRING |   | 
| POINT | BYTE\_ARRAY | STRING |   | 
| POLYGON | BYTE\_ARRAY | STRING |   | 
| JSON data types | 
| JSON | BYTE\_ARRAY | STRING |   | 
| JSONB | BYTE\_ARRAY | STRING |   | 
| Other data types | 
| BOOLEAN | BOOLEAN |  |   | 
| CIDR | BYTE\_ARRAY | STRING |  Network data type | 
| COMPOSITE | BYTE\_ARRAY | STRING |   | 
| DOMAIN | BYTE\_ARRAY | STRING |   | 
| INET | BYTE\_ARRAY | STRING |  Network data type | 
| MACADDR | BYTE\_ARRAY | STRING |   | 
| OBJECT IDENTIFIER | N/A |  |  | 
| PG\_LSN | BYTE\_ARRAY | STRING |   | 
| RANGE | BYTE\_ARRAY | STRING |   | 
| UUID | BYTE\_ARRAY | STRING |   | 