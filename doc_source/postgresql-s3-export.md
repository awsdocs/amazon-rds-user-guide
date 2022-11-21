# Exporting data from an RDS for PostgreSQL DB instance to Amazon S3<a name="postgresql-s3-export"></a>

You can query data from an RDS for PostgreSQL DB instance and export it directly into files stored in an Amazon S3 bucket\. To do this, you first install the RDS for PostgreSQL `aws_s3` extension\. This extension provides you with the functions that you use to export the results of queries to Amazon S3\. Following, you can find out how to install the extension and how to export data to Amazon S3\. 

You can export from a provisioned or an Aurora Serverless v2 DB instance\. These steps aren't supported for Aurora Serverless v1\. 

**Note**  
Cross\-account export to Amazon S3 isn't supported\. 

All currently available versions of RDS for PostgreSQL support exporting data to Amazon Simple Storage Service\. For detailed version information, see [Amazon RDS for PostgreSQL updates](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-versions.html) in the *Amazon RDS for PostgreSQL Release Notes*\.

If you don't have a bucket set up for your export, see the following topics the *Amazon Simple Storage Service User Guide*\. 
+ [Setting up Amazon S3](https://docs.aws.amazon.com/AmazonS3/latest/userguide/setting-up-s3.html)
+ [Create a bucket](https://docs.aws.amazon.com/AmazonS3/latest/gsg/CreatingABucket.html)

The upload to Amazon S3 uses server\-side encryption by default\. If you are using encryption, the Amazon S3 bucket must be encrypted with an AWS managed key\. Currently, you can't export data to a bucket that's encrypted with a customer managed key\.

**Note**  
You can save DB snapshot data to Amazon S3 using the AWS Management Console, AWS CLI, or Amazon RDS API\. For more information, see [Exporting DB snapshot data to Amazon S3](USER_ExportSnapshot.md)\.

**Topics**
+ [Installing the aws\_s3 extension](#USER_PostgreSQL.S3Export.InstallExtension)
+ [Overview of exporting data to Amazon S3](#postgresql-s3-export-overview)
+ [Specifying the Amazon S3 file path to export to](#postgresql-s3-export-file)
+ [Setting up access to an Amazon S3 bucket](#postgresql-s3-export-access-bucket)
+ [Exporting query data using the aws\_s3\.query\_export\_to\_s3 function](#postgresql-s3-export-examples)
+ [Troubleshooting access to Amazon S3](#postgresql-s3-export-troubleshoot)
+ [Function reference](#postgresql-s3-export-functions)

## Installing the aws\_s3 extension<a name="USER_PostgreSQL.S3Export.InstallExtension"></a>

Before you can use Amazon Simple Storage Service with your RDS for PostgreSQL DB instance, you need to install the `aws_s3` extension\. This extension provides functions for exporting data from an RDS for PostgreSQL DB instance to an Amazon S3 bucket\. It also provides functions for importing data from an Amazon S3\. For more information, see [Importing data from Amazon S3 into an RDS for PostgreSQL DB instance](USER_PostgreSQL.S3Import.md)\. The `aws_s3` extension depends on some of the helper functions in the `aws_commons` extension, which is installed automatically when needed\. 

**To install the `aws_s3` extension**

1. Use psql \(or pgAdmin\) to connect to the RDS for PostgreSQL DB instance as a user that has `rds_superuser` privileges\. If you kept the default name during the setup process, you connect as `postgres`\.

   ```
   psql --host=111122223333.aws-region.rds.amazonaws.com --port=5432 --username=postgres --password
   ```

1. To install the extension, run the following command\. 

   ```
   postgres=> CREATE EXTENSION aws_s3 CASCADE;
   NOTICE: installing required extension "aws_commons"
   CREATE EXTENSION
   ```

1. To verify that the extension is installed, you can use the psql `\dx` metacommand\.

   ```
   postgres=> \dx
          List of installed extensions
       Name     | Version |   Schema   |                 Description
   -------------+---------+------------+---------------------------------------------
    aws_commons | 1.2     | public     | Common data types across AWS services
    aws_s3      | 1.1     | public     | AWS S3 extension for importing data from S3
    plpgsql     | 1.0     | pg_catalog | PL/pgSQL procedural language
   (3 rows)
   ```

The functions for importing data from Amazon S3 and exporting data to Amazon S3 are now available to use\.

### Verify that your RDS for PostgreSQL version supports exports to Amazon S3<a name="postgresql-s3-supported"></a>

You can verify that your RDS for PostgreSQL version supports export to Amazon S3 by using the `describe-db-engine-versions` command\. The following example verifies support for version 10\.14\.

```
aws rds describe-db-engine-versions --region us-east-1 ^
--engine postgres --engine-version 10.14 | grep s3Export
```

If the output includes the string `"s3Export"`, then the engine supports Amazon S3 exports\. Otherwise, the engine doesn't support them\.

## Overview of exporting data to Amazon S3<a name="postgresql-s3-export-overview"></a>

To export data stored in an RDS for PostgreSQL database to an Amazon S3 bucket, use the following procedure\.

**To export RDS for PostgreSQL data to S3**

1. Identify an Amazon S3 file path to use for exporting data\. For details about this process, see [Specifying the Amazon S3 file path to export to](#postgresql-s3-export-file)\.

1. Provide permission to access the Amazon S3 bucket\.

   To export data to an Amazon S3 file, give the RDS for PostgreSQL DB instance permission to access the Amazon S3 bucket that the export will use for storage\. Doing this includes the following steps:

   1. Create an IAM policy that provides access to an Amazon S3 bucket that you want to export to\.

   1. Create an IAM role\.

   1. Attach the policy you created to the role you created\.

   1. Add this IAM role to your DB instance\.

   For details about this process, see [Setting up access to an Amazon S3 bucket](#postgresql-s3-export-access-bucket)\.

1. Identify a database query to get the data\. Export the query data by calling the `aws_s3.query_export_to_s3` function\. 

   After you complete the preceding preparation tasks, use the [aws\_s3\.query\_export\_to\_s3](#aws_s3.export_query_to_s3) function to export query results to Amazon S3\. For details about this process, see [Exporting query data using the aws\_s3\.query\_export\_to\_s3 function](#postgresql-s3-export-examples)\.

## Specifying the Amazon S3 file path to export to<a name="postgresql-s3-export-file"></a>

Specify the following information to identify the location in Amazon S3 where you want to export data to:
+ Bucket name – A *bucket* is a container for Amazon S3 objects or files\.

  For more information on storing data with Amazon S3, see [Create a bucket](https://docs.aws.amazon.com/AmazonS3/latest/gsg/CreatingABucket.html) and [View an object](https://docs.aws.amazon.com/AmazonS3/latest/gsg/OpeningAnObject.html) in the *Amazon Simple Storage Service User Guide*\. 
+ File path – The file path identifies where the export is stored in the Amazon S3 bucket\. The file path consists of the following:
  + An optional path prefix that identifies a virtual folder path\.
  + A file prefix that identifies one or more files to be stored\. Larger exports are stored in multiple files, each with a maximum size of approximately 6 GB\. The additional file names have the same file prefix but with `_partXX` appended\. The `XX` represents 2, then 3, and so on\.

  For example, a file path with an `exports` folder and a `query-1-export` file prefix is `/exports/query-1-export`\.
+ AWS Region \(optional\) – The AWS Region where the Amazon S3 bucket is located\. If you don't specify an AWS Region value, then Amazon RDS saves your files into Amazon S3 in the same AWS Region as the exporting DB instance\.
**Note**  
Currently, the AWS Region must be the same as the region of the exporting DB instance\.

  For a listing of AWS Region names and associated values, see [Regions, Availability Zones, and Local Zones](Concepts.RegionsAndAvailabilityZones.md)\.

To hold the Amazon S3 file information about where the export is to be stored, you can use the [aws\_commons\.create\_s3\_uri](#aws_commons.create_s3_uri) function to create an `aws_commons._s3_uri_1` composite structure as follows\.

```
psql=> SELECT aws_commons.create_s3_uri(
   'sample-bucket',
   'sample-filepath',
   'us-west-2'
) AS s3_uri_1 \gset
```

You later provide this `s3_uri_1` value as a parameter in the call to the [aws\_s3\.query\_export\_to\_s3](#aws_s3.export_query_to_s3) function\. For examples, see [Exporting query data using the aws\_s3\.query\_export\_to\_s3 function](#postgresql-s3-export-examples)\.

## Setting up access to an Amazon S3 bucket<a name="postgresql-s3-export-access-bucket"></a>

To export data to Amazon S3, give your PostgreSQL DB instance permission to access the Amazon S3 bucket that the files are to go in\. 

To do this, use the following procedure\.

**To give a PostgreSQL DB instance access to Amazon S3 through an IAM role**

1. Create an IAM policy\. 

   This policy provides the bucket and object permissions that allow your PostgreSQL DB instance to access Amazon S3\. 

   As part of creating this policy, take the following steps:

   1. Include in the policy the following required actions to allow the transfer of files from your PostgreSQL DB instance to an Amazon S3 bucket: 
      + `s3:PutObject`
      + `s3:AbortMultipartUpload`

   1. Include the Amazon Resource Name \(ARN\) that identifies the Amazon S3 bucket and objects in the bucket\. The ARN format for accessing Amazon S3 is: `arn:aws:s3:::your-s3-bucket/*`

   For more information on creating an IAM policy for Amazon RDS for PostgreSQL, see [Creating and using an IAM policy for IAM database access](UsingWithRDS.IAMDBAuth.IAMPolicy.md)\. See also [Tutorial: Create and attach your first customer managed policy](https://docs.aws.amazon.com/IAM/latest/UserGuide/tutorial_managed-policies.html) in the *IAM User Guide*\.

   The following AWS CLI command creates an IAM policy named `rds-s3-export-policy` with these options\. It grants access to a bucket named `your-s3-bucket`\. 
**Warning**  
We recommend that you set up your database within a private VPC that has endpoint policies configured for accessing specific buckets\. For more information, see [ Using endpoint policies for Amazon S3](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-endpoints-s3.html#vpc-endpoints-policies-s3) in the Amazon VPC User Guide\.  
We strongly recommend that you do not create a policy with all\-resource access\. This access can pose a threat for data security\. If you create a policy that gives `S3:PutObject` access to all resources using `"Resource":"*"`, then a user with export privileges can export data to all buckets in your account\. In addition, the user can export data to *any publicly writable bucket within your AWS Region*\. 

   After you create the policy, note the Amazon Resource Name \(ARN\) of the policy\. You need the ARN for a subsequent step when you attach the policy to an IAM role\. 

   ```
   aws iam create-policy  --policy-name rds-s3-export-policy  --policy-document '{
        "Version": "2012-10-17",
        "Statement": [
          {
            "Sid": "s3export",
            "Action": [
              "s3:PutObject",
              "s3:AbortMultipartUpload"
            ],
            "Effect": "Allow",
            "Resource": [
              "arn:aws:s3:::your-s3-bucket/*"
            ] 
          }
        ] 
      }'
   ```

1. Create an IAM role\. 

   You do this so Amazon RDS can assume this IAM role on your behalf to access your Amazon S3 buckets\. For more information, see [Creating a role to delegate permissions to an IAM user](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-user.html) in the *IAM User Guide*\.

   We recommend using the `[aws:SourceArn](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html#condition-keys-sourcearn)` and `[aws:SourceAccount](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html#condition-keys-sourceaccount)` global condition context keys in resource\-based policies to limit the service's permissions to a specific resource\. This is the most effective way to protect against the [confused deputy problem](https://docs.aws.amazon.com/IAM/latest/UserGuide/confused-deputy.html)\. 

   If you use both global condition context keys and the `aws:SourceArn` value contains the account ID, the `aws:SourceAccount` value and the account in the `aws:SourceArn` value must use the same account ID when used in the same policy statement\.
   + Use `aws:SourceArn` if you want cross\-service access for a single resource\. 
   + Use `aws:SourceAccount` if you want to allow any resource in that account to be associated with the cross\-service use\.

    In the policy, be sure to use the `aws:SourceArn` global condition context key with the full ARN of the resource\. The following example shows how to do so using the AWS CLI command to create a role named `rds-s3-export-role`\.   
**Example**  

   For Linux, macOS, or Unix:

   ```
   aws iam create-role  \
       --role-name rds-s3-export-role  \
       --assume-role-policy-document '{
        "Version": "2012-10-17",
        "Statement": [
          {
            "Effect": "Allow",
            "Principal": {
               "Service": "rds.amazonaws.com"
             },
            "Action": "sts:AssumeRole",
            "Condition": {
                "StringEquals": {
                   "aws:SourceAccount": "111122223333",
                   "aws:SourceArn": "arn:aws:rds:us-east-1:111122223333:db:dbname"
                   }
                }
          }
        ] 
      }'
   ```

   For Windows:

   ```
   aws iam create-role  ^
       --role-name rds-s3-export-role  ^
       --assume-role-policy-document '{
        "Version": "2012-10-17",
        "Statement": [
          {
            "Effect": "Allow",
            "Principal": {
               "Service": "rds.amazonaws.com"
             },
            "Action": "sts:AssumeRole",
            "Condition": {
                "StringEquals": {
                   "aws:SourceAccount": "111122223333",
                   "aws:SourceArn": "arn:aws:rds:us-east-1:111122223333:db:dbname"
                   }
                }
          }
        ] 
      }'
   ```

1. Attach the IAM policy that you created to the IAM role that you created\.

   The following AWS CLI command attaches the policy created earlier to the role named `rds-s3-export-role.` Replace `your-policy-arn` with the policy ARN that you noted in an earlier step\. 

   ```
   aws iam attach-role-policy  --policy-arn your-policy-arn  --role-name rds-s3-export-role  
   ```

1. Add the IAM role to the DB instance\. You do so by using the AWS Management Console or AWS CLI, as described following\.

### Console<a name="collapsible-section-1"></a>

**To add an IAM role for a PostgreSQL DB instance using the console**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. Choose the PostgreSQL DB instance name to display its details\.

1. On the **Connectivity & security** tab, in the **Manage IAM roles **section, choose the role to add under **Add IAM roles to this instance**\. 

1. Under **Feature**, choose **s3Export**\.

1. Choose **Add role**\.

### AWS CLI<a name="collapsible-section-2"></a>

**To add an IAM role for a PostgreSQL DB instance using the CLI**
+ Use the following command to add the role to the PostgreSQL DB instance named `my-db-instance`\. Replace *`your-role-arn`* with the role ARN that you noted in a previous step\. Use `s3Export` for the value of the `--feature-name` option\.   
**Example**  

  For Linux, macOS, or Unix:

  ```
  aws rds add-role-to-db-instance \
     --db-instance-identifier my-db-instance \
     --feature-name s3Export \
     --role-arn your-role-arn   \
     --region your-region
  ```

  For Windows:

  ```
  aws rds add-role-to-db-instance ^
     --db-instance-identifier my-db-instance ^
     --feature-name s3Export ^
     --role-arn your-role-arn ^
     --region your-region
  ```

## Exporting query data using the aws\_s3\.query\_export\_to\_s3 function<a name="postgresql-s3-export-examples"></a>

Export your PostgreSQL data to Amazon S3 by calling the [aws\_s3\.query\_export\_to\_s3](#aws_s3.export_query_to_s3) function\. 

**Topics**
+ [Prerequisites](#postgresql-s3-export-examples-prerequisites)
+ [Calling aws\_s3\.query\_export\_to\_s3](#postgresql-s3-export-examples-basic)
+ [Exporting to a CSV file that uses a custom delimiter](#postgresql-s3-export-examples-custom-delimiter)
+ [Exporting to a binary file with encoding](#postgresql-s3-export-examples-encoded)

### Prerequisites<a name="postgresql-s3-export-examples-prerequisites"></a>

Before you use the `aws_s3.query_export_to_s3` function, be sure to complete the following prerequisites:
+ Install the required PostgreSQL extensions as described in [Overview of exporting data to Amazon S3](#postgresql-s3-export-overview)\.
+ Determine where to export your data to Amazon S3 as described in [Specifying the Amazon S3 file path to export to](#postgresql-s3-export-file)\.
+ Make sure that the DB instance has export access to Amazon S3 as described in [Setting up access to an Amazon S3 bucket](#postgresql-s3-export-access-bucket)\.

The examples following use a database table called `sample_table`\. These examples export the data into a bucket called `sample-bucket`\. The example table and data are created with the following SQL statements in psql\.

```
psql=> CREATE TABLE sample_table (bid bigint PRIMARY KEY, name varchar(80));
psql=> INSERT INTO sample_table (bid,name) VALUES (1, 'Monday'), (2,'Tuesday'), (3, 'Wednesday');
```

### Calling aws\_s3\.query\_export\_to\_s3<a name="postgresql-s3-export-examples-basic"></a>

The following shows the basic ways of calling the [aws\_s3\.query\_export\_to\_s3](#aws_s3.export_query_to_s3) function\. 

These examples use the variable `s3_uri_1` to identify a structure that contains the information identifying the Amazon S3 file\. Use the [aws\_commons\.create\_s3\_uri](#aws_commons.create_s3_uri) function to create the structure\.

```
psql=> SELECT aws_commons.create_s3_uri(
   'sample-bucket',
   'sample-filepath',
   'us-west-2'
) AS s3_uri_1 \gset
```

Although the parameters vary for the following two `aws_s3.query_export_to_s3` function calls, the results are the same for these examples\. All rows of the `sample_table` table are exported into a bucket called `sample-bucket`\. 

```
psql=> SELECT * FROM aws_s3.query_export_to_s3('SELECT * FROM sample_table', :'s3_uri_1');

psql=> SELECT * FROM aws_s3.query_export_to_s3('SELECT * FROM sample_table', :'s3_uri_1', options :='format text');
```

The parameters are described as follows:
+ `'SELECT * FROM sample_table'` – The first parameter is a required text string containing an SQL query\. The PostgreSQL engine runs this query\. The results of the query are copied to the S3 bucket identified in other parameters\.
+ `:'s3_uri_1'` – This parameter is a structure that identifies the Amazon S3 file\. This example uses a variable to identify the previously created structure\. You can instead create the structure by including the `aws_commons.create_s3_uri` function call inline within the `aws_s3.query_export_to_s3` function call as follows\.

  ```
  SELECT * from aws_s3.query_export_to_s3('select * from sample_table', 
     aws_commons.create_s3_uri('sample-bucket', 'sample-filepath', 'us-west-2') 
  );
  ```
+ `options :='format text'` – The `options` parameter is an optional text string containing PostgreSQL `COPY` arguments\. The copy process uses the arguments and format of the [PostgreSQL COPY](https://www.postgresql.org/docs/current/sql-copy.html) command\. 

If the file specified doesn't exist in the Amazon S3 bucket, it's created\. If the file already exists, it's overwritten\. The syntax for accessing the exported data in Amazon S3 is the following\.

```
s3-region://bucket-name[/path-prefix]/file-prefix
```

Larger exports are stored in multiple files, each with a maximum size of approximately 6 GB\. The additional file names have the same file prefix but with `_partXX` appended\. The `XX` represents 2, then 3, and so on\. For example, suppose that you specify the path where you store data files as the following\.

```
s3-us-west-2://my-bucket/my-prefix
```

If the export has to create three data files, the Amazon S3 bucket contains the following data files\.

```
s3-us-west-2://my-bucket/my-prefix
s3-us-west-2://my-bucket/my-prefix_part2
s3-us-west-2://my-bucket/my-prefix_part3
```

For the full reference for this function and additional ways to call it, see [aws\_s3\.query\_export\_to\_s3](#aws_s3.export_query_to_s3)\. For more about accessing files in Amazon S3, see [View an object](https://docs.aws.amazon.com/AmazonS3/latest/gsg/OpeningAnObject.html) in the *Amazon Simple Storage Service User Guide*\. 

### Exporting to a CSV file that uses a custom delimiter<a name="postgresql-s3-export-examples-custom-delimiter"></a>

The following example shows how to call the [aws\_s3\.query\_export\_to\_s3](#aws_s3.export_query_to_s3) function to export data to a file that uses a custom delimiter\. The example uses arguments of the [PostgreSQL COPY](https://www.postgresql.org/docs/current/sql-copy.html) command to specify the comma\-separated value \(CSV\) format and a colon \(:\) delimiter\.

```
SELECT * from aws_s3.query_export_to_s3('select * from basic_test', :'s3_uri_1', options :='format csv, delimiter $$:$$');
```

### Exporting to a binary file with encoding<a name="postgresql-s3-export-examples-encoded"></a>

The following example shows how to call the [aws\_s3\.query\_export\_to\_s3](#aws_s3.export_query_to_s3) function to export data to a binary file that has Windows\-1253 encoding\.

```
SELECT * from aws_s3.query_export_to_s3('select * from basic_test', :'s3_uri_1', options :='format binary, encoding WIN1253');
```

## Troubleshooting access to Amazon S3<a name="postgresql-s3-export-troubleshoot"></a>

If you encounter connection problems when attempting to export data to Amazon S3, first confirm that the outbound access rules for the VPC security group associated with your DB instance permit network connectivity\. Specifically, the security group must have a rule that allows the DB instance to send TCP traffic to port 443 and to any IPv4 addresses \(0\.0\.0\.0/0\)\. For more information, see [Provide access to your DB instance in your VPC by creating a security group](CHAP_SettingUp.md#CHAP_SettingUp.SecurityGroup)\.

See also the following for recommendations:
+ [Troubleshooting Amazon RDS identity and access](security_iam_troubleshoot.md)
+ [Troubleshooting Amazon S3](https://docs.aws.amazon.com/AmazonS3/latest/dev/troubleshooting.html) in the *Amazon Simple Storage Service User Guide*
+ [Troubleshooting Amazon S3 and IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/troubleshoot_iam-s3.html) in the *IAM User Guide*

## Function reference<a name="postgresql-s3-export-functions"></a>

**Topics**
+ [aws\_s3\.query\_export\_to\_s3](#aws_s3.export_query_to_s3)
+ [aws\_commons\.create\_s3\_uri](#aws_commons.create_s3_uri)

### aws\_s3\.query\_export\_to\_s3<a name="aws_s3.export_query_to_s3"></a>

Exports a PostgreSQL query result to an Amazon S3 bucket\. The `aws_s3` extension provides the `aws_s3.query_export_to_s3` function\. 

The two required parameters are `query` and `s3_info`\. These define the query to be exported and identify the Amazon S3 bucket to export to\. An optional parameter called `options` provides for defining various export parameters\. For examples of using the `aws_s3.query_export_to_s3` function, see [Exporting query data using the aws\_s3\.query\_export\_to\_s3 function](#postgresql-s3-export-examples)\.

**Syntax**

```
aws_s3.query_export_to_s3(
    query text,    
    s3_info aws_commons._s3_uri_1,    
    options text
)
```Input parameters

*query*  
A required text string containing an SQL query that the PostgreSQL engine runs\. The results of this query are copied to an S3 bucket identified in the `s3_info` parameter\.

*s3\_info*  
An `aws_commons._s3_uri_1` composite type containing the following information about the S3 object:  
+ `bucket` – The name of the Amazon S3 bucket to contain the file\.
+ `file_path` – The Amazon S3 file name and path\.
+ `region` – The AWS Region that the bucket is in\. For a listing of AWS Region names and associated values, see [Regions, Availability Zones, and Local Zones](Concepts.RegionsAndAvailabilityZones.md)\. 

  Currently, this value must be the same AWS Region as that of the exporting DB instance\. The default is the AWS Region of the exporting DB instance\. 
To create an `aws_commons._s3_uri_1` composite structure, see the [aws\_commons\.create\_s3\_uri](#aws_commons.create_s3_uri) function\.

*options*  
An optional text string containing arguments for the PostgreSQL `COPY` command\. These arguments specify how the data is to be copied when exported\. For more details, see the [PostgreSQL COPY documentation](https://www.postgresql.org/docs/current/sql-copy.html)\.

#### Alternate input parameters<a name="aws_s3.export_query_to_s3-alternate-parameters"></a>

To help with testing, you can use an expanded set of parameters instead of the `s3_info` parameter\. Following are additional syntax variations for the `aws_s3.query_export_to_s3` function\. 

Instead of using the `s3_info` parameter to identify an Amazon S3 file, use the combination of the `bucket`, `file_path`, and `region` parameters\.

```
aws_s3.query_export_to_s3(
    query text,    
    bucket text,    
    file_path text,    
    region text,    
    options text
)
```

*query*  
A required text string containing an SQL query that the PostgreSQL engine runs\. The results of this query are copied to an S3 bucket identified in the `s3_info` parameter\.

*bucket*  
A required text string containing the name of the Amazon S3 bucket that contains the file\.

*file\_path*  
A required text string containing the Amazon S3 file name including the path of the file\.

*region*  
An optional text string containing the AWS Region that the bucket is in\. For a listing of AWS Region names and associated values, see [Regions, Availability Zones, and Local Zones](Concepts.RegionsAndAvailabilityZones.md)\.  
Currently, this value must be the same AWS Region as that of the exporting DB instance\. The default is the AWS Region of the exporting DB instance\. 

*options*  
An optional text string containing arguments for the PostgreSQL `COPY` command\. These arguments specify how the data is to be copied when exported\. For more details, see the [PostgreSQL COPY documentation](https://www.postgresql.org/docs/current/sql-copy.html)\.

#### Output parameters<a name="aws_s3.export_query_to_s3-output-parameters"></a>

```
aws_s3.query_export_to_s3(
    OUT rows_uploaded bigint,
    OUT files_uploaded bigint,
    OUT bytes_uploaded bigint
)
```

*rows\_uploaded*  
The number of table rows that were successfully uploaded to Amazon S3 for the given query\.

*files\_uploaded*  
The number of files uploaded to Amazon S3\. Files are created in sizes of approximately 6 GB\. Each additional file created has `_partXX` appended to the name\. The `XX` represents 2, then 3, and so on as needed\.

*bytes\_uploaded*  
The total number of bytes uploaded to Amazon S3\.

#### Examples<a name="aws_s3.export_query_to_s3-examples"></a>

```
psql=> SELECT * from aws_s3.query_export_to_s3('select * from sample_table', 'sample-bucket', 'sample-filepath');
psql=> SELECT * from aws_s3.query_export_to_s3('select * from sample_table', 'sample-bucket', 'sample-filepath','us-west-2');
psql=> SELECT * from aws_s3.query_export_to_s3('select * from sample_table', 'sample-bucket', 'sample-filepath','us-west-2','format text');
```

### aws\_commons\.create\_s3\_uri<a name="aws_commons.create_s3_uri"></a>

Creates an `aws_commons._s3_uri_1` structure to hold Amazon S3 file information\. You use the results of the `aws_commons.create_s3_uri` function in the `s3_info` parameter of the [aws\_s3\.query\_export\_to\_s3](#aws_s3.export_query_to_s3) function\. For an example of using the `aws_commons.create_s3_uri` function, see [Specifying the Amazon S3 file path to export to](#postgresql-s3-export-file)\.

**Syntax**

```
aws_commons.create_s3_uri(
   bucket text,
   file_path text,
   region text
)
```Input parameters

*bucket*  
A required text string containing the Amazon S3 bucket name for the file\.

*file\_path*  
A required text string containing the Amazon S3 file name including the path of the file\.

*region*  
A required text string containing the AWS Region that the file is in\. For a listing of AWS Region names and associated values, see [Regions, Availability Zones, and Local Zones](Concepts.RegionsAndAvailabilityZones.md)\.