# Importing data from Amazon S3 into an RDS for PostgreSQL DB instance<a name="USER_PostgreSQL.S3Import"></a>

You can import data that's been stored using Amazon Simple Storage Service into a table on an RDS for PostgreSQL DB instance\. To do this, you first install the RDS for PostgreSQL `aws_s3` extension\. This extension provides the functions that you use to import data from an Amazon S3 bucket\. A *bucket* is an Amazon S3 container for objects and files\. The data can be in a comma\-separate value \(CSV\) file, a text file, or a compressed \(gzip\) file\. Following, you can learn how to install the extension and how to import data from Amazon S3 into a table\. 

Your database must be running PostgreSQL version 10\.7 or higher to import from Amazon S3 into RDS for PostgreSQL\. 

If you don't have data stored on Amazon S3, you need to first create a bucket and store the data\. For more information, see the following topics in the *Amazon Simple Storage Service User Guide*\. 
+ [Create a bucket](https://docs.aws.amazon.com/AmazonS3/latest/gsg/CreatingABucket.html)
+ [Add an object to a bucket](https://docs.aws.amazon.com/AmazonS3/latest/gsg/PuttingAnObjectInABucket.html) 

**Note**  
Importing data from Amazon S3 isn't supported for Aurora Serverless v1\. It is supported for Aurora Serverless v2\.

**Topics**
+ [Installing the aws\_s3 extension](#USER_PostgreSQL.S3Import.InstallExtension)
+ [Overview of importing data from Amazon S3 data](#USER_PostgreSQL.S3Import.Overview)
+ [Setting up access to an Amazon S3 bucket](#USER_PostgreSQL.S3Import.AccessPermission)
+ [Importing data from Amazon S3 to your RDS for PostgreSQL DB instance](#USER_PostgreSQL.S3Import.FileFormats)
+ [Function reference](#USER_PostgreSQL.S3Import.Reference)

## Installing the aws\_s3 extension<a name="USER_PostgreSQL.S3Import.InstallExtension"></a>

Before you can use Amazon S3 with your RDS for PostgreSQL DB instance, you need to install the `aws_s3` extension\. This extension provides functions for importing data from an Amazon S3\. It also provides functions for exporting data from an RDS for PostgreSQL DB instance to an Amazon S3 bucket\. For more information, see [Exporting data from an RDS for PostgreSQL DB instance to Amazon S3](postgresql-s3-export.md)\. The `aws_s3` extension depends on some of the helper functions in the `aws_commons` extension, which is installed automatically when needed\. 

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

## Overview of importing data from Amazon S3 data<a name="USER_PostgreSQL.S3Import.Overview"></a>

**To import S3 data into Amazon RDS**

First, gather the details that you need to supply to the function\. These include the name of the table on your RDS for PostgreSQL DB instance, and the bucket name, file path, file type, and AWS Region where the Amazon S3 data is stored\. For more information, see [View an object](https://docs.aws.amazon.com/AmazonS3/latest/gsg/OpeningAnObject.html) in the *Amazon Simple Storage Service User Guide*\.

1. Get the name of the table into which the `aws_s3.table_import_from_s3` function is to import the data\. As an example, the following command creates a table `t1` that can be used in later steps\. 

   ```
   postgres=> CREATE TABLE t1 
       (col1 varchar(80), 
       col2 varchar(80), 
       col3 varchar(80));
   ```

1. Get the details about the Amazon S3 bucket and the data to import\. To do this, open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/), and choose **Buckets**\. Find the bucket containing your data in the list\. Choose the bucket, open its Object overview page, and then choose Properties\.

   Make a note of the bucket name, path, the AWS Region, and file type\. You need the Amazon Resource Name \(ARN\) later, to set up access to Amazon S3 through an IAM role\. For more more information, see [Setting up access to an Amazon S3 bucket](#USER_PostgreSQL.S3Import.AccessPermission)\. The image following shows an example\.   
![\[Image of a file object in an Amazon S3 bucket.\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/aws_s3_import-export_s3_bucket-info.png)

1. You can verify the path to the data on the Amazon S3 bucket by using the AWS CLI command `aws s3 cp`\. If the information is correct, this command downloads a copy of the Amazon S3 file\. 

   ```
   aws s3 cp s3://sample_s3_bucket/sample_file_path ./ 
   ```

1. Set up permissions on your  RDS for PostgreSQL DB instance to allow access to the file on the Amazon S3 bucket\. To do so, you use either an AWS Identity and Access Management \(IAM\) role or security credentials\. For more information, see [Setting up access to an Amazon S3 bucket](#USER_PostgreSQL.S3Import.AccessPermission)\.

1. Supply the path and other Amazon S3 object details gathered \(see step 2\) to the `create_s3_uri` function to construct an Amazon S3 URI object\. To learn more about this function, see [aws\_commons\.create\_s3\_uri](#USER_PostgreSQL.S3Import.create_s3_uri)\. The following is an example of constructing this object during a psql session\.

   ```
   postgres=> SELECT aws_commons.create_s3_uri(
      'docs-lab-store-for-rpg',
      'versions_and_jdks_listing.csv',
      'us-west-1'
   ) AS s3_uri \gset
   ```

   In the next step, you pass this object \(`aws_commons._s3_uri_1`\) to the `aws_s3.table_import_from_s3` function to import the data to the table\. 

1. Invoke the `aws_s3.table_import_from_s3` function to import the data from Amazon S3 into your table\. For reference information, see [aws\_s3\.table\_import\_from\_s3](#aws_s3.table_import_from_s3)\. For examples, see [Importing data from Amazon S3 to your RDS for PostgreSQL DB instance](#USER_PostgreSQL.S3Import.FileFormats)\. 

## Setting up access to an Amazon S3 bucket<a name="USER_PostgreSQL.S3Import.AccessPermission"></a>

To import data from an Amazon S3 file, give the RDS for PostgreSQL DB instance permission to access the Amazon S3 bucket containing the file\. You provide access to an Amazon S3 bucket in one of two ways, as described in the following topics\.

**Topics**
+ [Using an IAM role to access an Amazon S3 bucket](#USER_PostgreSQL.S3Import.ARNRole)
+ [Using security credentials to access an Amazon S3 bucket](#USER_PostgreSQL.S3Import.Credentials)
+ [Troubleshooting access to Amazon S3](#USER_PostgreSQL.S3Import.troubleshooting)

### Using an IAM role to access an Amazon S3 bucket<a name="USER_PostgreSQL.S3Import.ARNRole"></a>

Before you load data from an Amazon S3 file, give your RDS for PostgreSQL DB instance permission to access the Amazon S3 bucket the file is in\. This way, you don't have to manage additional credential information or provide it in the [aws\_s3\.table\_import\_from\_s3](#aws_s3.table_import_from_s3) function call\.

To do this, create an IAM policy that provides access to the Amazon S3 bucket\. Create an IAM role and attach the policy to the role\. Then assign the IAM role to your DB instance\. 

**Note**  
You can't associate an IAM role with an Aurora Serverless v1 DB cluster, so the following steps don't apply\.

**To give an RDS for PostgreSQL DB instance access to Amazon S3 through an IAM role**

1. Create an IAM policy\. 

   This policy provides the bucket and object permissions that allow your RDS for PostgreSQL DB instance to access Amazon S3\. 

   Include in the policy the following required actions to allow the transfer of files from an Amazon S3 bucket to Amazon RDS: 
   + `s3:GetObject` 
   + `s3:ListBucket` 

   Include in the policy the following resources to identify the Amazon S3 bucket and objects in the bucket\. This shows the Amazon Resource Name \(ARN\) format for accessing Amazon S3\.
   + arn:aws:s3:::*your\-s3\-bucket*
   + arn:aws:s3:::*your\-s3\-bucket*/\*

   For more information on creating an IAM policy for RDS for PostgreSQL, see [Creating and using an IAM policy for IAM database access](UsingWithRDS.IAMDBAuth.IAMPolicy.md)\. See also [Tutorial: Create and attach your first customer managed policy](https://docs.aws.amazon.com/IAM/latest/UserGuide/tutorial_managed-policies.html) in the *IAM User Guide*\.

   The following AWS CLI command creates an IAM policy named `rds-s3-import-policy` with these options\. It grants access to a bucket named `your-s3-bucket`\. 
**Note**  
Make a note of the Amazon Resource Name \(ARN\) of the policy returned by this command\. You need the ARN in a subsequent step when you attach the policy to an IAM role\.  
**Example**  

   For Linux, macOS, or Unix:

   ```
   aws iam create-policy \
      --policy-name rds-s3-import-policy \
      --policy-document '{
        "Version": "2012-10-17",
        "Statement": [
          {
            "Sid": "s3import",
            "Action": [
              "s3:GetObject",
              "s3:ListBucket"
            ],
            "Effect": "Allow",
            "Resource": [
              "arn:aws:s3:::your-s3-bucket", 
              "arn:aws:s3:::your-s3-bucket/*"
            ] 
          }
        ] 
      }'
   ```

   For Windows:

   ```
   aws iam create-policy ^
      --policy-name rds-s3-import-policy ^
      --policy-document '{
        "Version": "2012-10-17",
        "Statement": [
          {
            "Sid": "s3import",
            "Action": [
              "s3:GetObject",
              "s3:ListBucket"
            ], 
            "Effect": "Allow",
            "Resource": [
              "arn:aws:s3:::your-s3-bucket", 
              "arn:aws:s3:::your-s3-bucket/*"
            ] 
          }
        ] 
      }'
   ```

1. Create an IAM role\. 

   You do this so Amazon RDS can assume this IAM role to access your Amazon S3 buckets\. For more information, see [Creating a role to delegate permissions to an IAM user](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-user.html) in the *IAM User Guide*\.

   We recommend using the `[aws:SourceArn](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html#condition-keys-sourcearn)` and `[aws:SourceAccount](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html#condition-keys-sourceaccount)` global condition context keys in resource\-based policies to limit the service's permissions to a specific resource\. This is the most effective way to protect against the [confused deputy problem](https://docs.aws.amazon.com/IAM/latest/UserGuide/confused-deputy.html)\. 

   If you use both global condition context keys and the `aws:SourceArn` value contains the account ID, the `aws:SourceAccount` value and the account in the `aws:SourceArn` value must use the same account ID when used in the same policy statement\.
   + Use `aws:SourceArn` if you want cross\-service access for a single resource\. 
   + Use `aws:SourceAccount` if you want to allow any resource in that account to be associated with the cross\-service use\.

   In the policy, be sure to use the `aws:SourceArn` global condition context key with the full ARN of the resource\. The following example shows how to do so using the AWS CLI command to create a role named `rds-s3-import-role`\.   
**Example**  

   For Linux, macOS, or Unix:

   ```
   aws iam create-role \
      --role-name rds-s3-import-role \
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
   aws iam create-role ^
      --role-name rds-s3-import-role ^
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

   The following AWS CLI command attaches the policy created in the previous step to the role named `rds-s3-import-role` Replace `your-policy-arn` with the policy ARN that you noted in an earlier step\.   
**Example**  

   For Linux, macOS, or Unix:

   ```
   aws iam attach-role-policy \
      --policy-arn your-policy-arn \
      --role-name rds-s3-import-role
   ```

   For Windows:

   ```
   aws iam attach-role-policy ^
      --policy-arn your-policy-arn ^
      --role-name rds-s3-import-role
   ```

1. Add the IAM role to the DB instance\. 

   You do so by using the AWS Management Console or AWS CLI, as described following\. 

#### Console<a name="collapsible-section-1"></a>

**To add an IAM role for a PostgreSQL DB instance using the console**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. Choose the PostgreSQL DB instance name to display its details\.

1. On the **Connectivity & security** tab, in the **Manage IAM roles **section, choose the role to add under **Add IAM roles to this instance **\. 

1. Under **Feature**, choose **s3Import**\.

1. Choose **Add role**\.

#### AWS CLI<a name="collapsible-section-2"></a>

**To add an IAM role for a PostgreSQL DB instance using the CLI**
+ Use the following command to add the role to the PostgreSQL DB instance named `my-db-instance`\. Replace *`your-role-arn`* with the role ARN that you noted in a previous step\. Use `s3Import` for the value of the `--feature-name` option\.   
**Example**  

  For Linux, macOS, or Unix:

  ```
  aws rds add-role-to-db-instance \
     --db-instance-identifier my-db-instance \
     --feature-name s3Import \
     --role-arn your-role-arn   \
     --region your-region
  ```

  For Windows:

  ```
  aws rds add-role-to-db-instance ^
     --db-instance-identifier my-db-instance ^
     --feature-name s3Import ^
     --role-arn your-role-arn ^
     --region your-region
  ```

#### RDS API<a name="collapsible-section-3"></a>

To add an IAM role for a PostgreSQL DB instance using the Amazon RDS API, call the [ AddRoleToDBInstance](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_AddRoleToDBInstance.html) operation\. 

### Using security credentials to access an Amazon S3 bucket<a name="USER_PostgreSQL.S3Import.Credentials"></a>

If you prefer, you can use security credentials to provide access to an Amazon S3 bucket instead of providing access with an IAM role\. You do so by specifying the `credentials` parameter in the [aws\_s3\.table\_import\_from\_s3](#aws_s3.table_import_from_s3) function call\. 

The `credentials` parameter is a structure of type `aws_commons._aws_credentials_1`, which contains AWS credentials\. Use the [aws\_commons\.create\_aws\_credentials](#USER_PostgreSQL.S3Import.create_aws_credentials) function to set the access key and secret key in an `aws_commons._aws_credentials_1` structure, as shown following\. 

```
postgres=> SELECT aws_commons.create_aws_credentials(
   'sample_access_key', 'sample_secret_key', '')
AS creds \gset
```

After creating the `aws_commons._aws_credentials_1 `structure, use the [aws\_s3\.table\_import\_from\_s3](#aws_s3.table_import_from_s3) function with the `credentials` parameter to import the data, as shown following\.

```
postgres=> SELECT aws_s3.table_import_from_s3(
   't', '', '(format csv)',
   :'s3_uri', 
   :'creds'
);
```

Or you can include the [aws\_commons\.create\_aws\_credentials](#USER_PostgreSQL.S3Import.create_aws_credentials) function call inline within the `aws_s3.table_import_from_s3` function call\.

```
postgres=> SELECT aws_s3.table_import_from_s3(
   't', '', '(format csv)',
   :'s3_uri', 
   aws_commons.create_aws_credentials('sample_access_key', 'sample_secret_key', '')
);
```

### Troubleshooting access to Amazon S3<a name="USER_PostgreSQL.S3Import.troubleshooting"></a>

If you encounter connection problems when attempting to import data from Amazon S3, see the following for recommendations:
+ [Troubleshooting Amazon RDS identity and access](security_iam_troubleshoot.md)
+ [Troubleshooting Amazon S3](https://docs.aws.amazon.com/AmazonS3/latest/dev/troubleshooting.html) in the *Amazon Simple Storage Service User Guide*
+ [Troubleshooting Amazon S3 and IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/troubleshoot_iam-s3.html) in the *IAM User Guide*

## Importing data from Amazon S3 to your RDS for PostgreSQL DB instance<a name="USER_PostgreSQL.S3Import.FileFormats"></a>

You import data from your Amazon S3 bucket by using the `table_import_from_s3` function of the aws\_s3 extension\. For reference information, see [aws\_s3\.table\_import\_from\_s3](#aws_s3.table_import_from_s3)\. 

**Note**  
The following examples use the IAM role method to allow access to the Amazon S3 bucket\. Thus, the `aws_s3.table_import_from_s3` function calls don't include credential parameters\.

The following shows a typical example\.

```
postgres=> SELECT aws_s3.table_import_from_s3(
   't1',
   '', 
   '(format csv)',
   :'s3_uri'
);
```

The parameters are the following:
+ `t1` – The name for the table in the PostgreSQL DB instance to copy the data into\. 
+ `''` – An optional list of columns in the database table\. You can use this parameter to indicate which columns of the S3 data go in which table columns\. If no columns are specified, all the columns are copied to the table\. For an example of using a column list, see [Importing an Amazon S3 file that uses a custom delimiter](#USER_PostgreSQL.S3Import.FileFormats.CustomDelimiter)\.
+ `(format csv)` – PostgreSQL COPY arguments\. The copy process uses the arguments and format of the [PostgreSQL COPY](https://www.postgresql.org/docs/current/sql-copy.html) command to import the data\. Choices for format include comma\-separated value \(CSV\) as shown in this example, text, and binary\. The default is text\. 
+  `s3_uri` – A structure that contains the information identifying the Amazon S3 file\. For an example of using the [aws\_commons\.create\_s3\_uri](#USER_PostgreSQL.S3Import.create_s3_uri) function to create an `s3_uri` structure, see [Overview of importing data from Amazon S3 data](#USER_PostgreSQL.S3Import.Overview)\.

For more information about this function, see [aws\_s3\.table\_import\_from\_s3](#aws_s3.table_import_from_s3)\.

The `aws_s3.table_import_from_s3` function returns text\. To specify other kinds of files for import from an Amazon S3 bucket, see one of the following examples\. 

**Topics**
+ [Importing an Amazon S3 file that uses a custom delimiter](#USER_PostgreSQL.S3Import.FileFormats.CustomDelimiter)
+ [Importing an Amazon S3 compressed \(gzip\) file](#USER_PostgreSQL.S3Import.FileFormats.gzip)
+ [Importing an encoded Amazon S3 file](#USER_PostgreSQL.S3Import.FileFormats.Encoded)

### Importing an Amazon S3 file that uses a custom delimiter<a name="USER_PostgreSQL.S3Import.FileFormats.CustomDelimiter"></a>

The following example shows how to import a file that uses a custom delimiter\. It also shows how to control where to put the data in the database table using the `column_list` parameter of the [aws\_s3\.table\_import\_from\_s3](#aws_s3.table_import_from_s3) function\. 

For this example, assume that the following information is organized into pipe\-delimited columns in the Amazon S3 file\.

```
1|foo1|bar1|elephant1
2|foo2|bar2|elephant2
3|foo3|bar3|elephant3
4|foo4|bar4|elephant4
...
```

**To import a file that uses a custom delimiter**

1. Create a table in the database for the imported data\.

   ```
   postgres=> CREATE TABLE test (a text, b text, c text, d text, e text);
   ```

1. Use the following form of the [aws\_s3\.table\_import\_from\_s3](#aws_s3.table_import_from_s3) function to import data from the Amazon S3 file\. 

   You can include the [aws\_commons\.create\_s3\_uri](#USER_PostgreSQL.S3Import.create_s3_uri) function call inline within the `aws_s3.table_import_from_s3` function call to specify the file\. 

   ```
   postgres=> SELECT aws_s3.table_import_from_s3(
      'test',
      'a,b,d,e',
      'DELIMITER ''|''', 
      aws_commons.create_s3_uri('sampleBucket', 'pipeDelimitedSampleFile', 'us-east-2')
   );
   ```

The data is now in the table in the following columns\.

```
postgres=> SELECT * FROM test;
a | b | c | d | e 
---+------+---+---+------+-----------
1 | foo1 | | bar1 | elephant1
2 | foo2 | | bar2 | elephant2
3 | foo3 | | bar3 | elephant3
4 | foo4 | | bar4 | elephant4
```

### Importing an Amazon S3 compressed \(gzip\) file<a name="USER_PostgreSQL.S3Import.FileFormats.gzip"></a>

The following example shows how to import a file from Amazon S3 that is compressed with gzip\. The file that you import needs to have the following Amazon S3 metadata:
+ Key: `Content-Encoding`
+ Value: `gzip`

If you upload the file using the AWS Management Console, the metadata is typically applied by the system\. For information about uploading files to Amazon S3 using the AWS Management Console, the AWS CLI, or the API, see [Uploading objects](https://docs.aws.amazon.com/AmazonS3/latest/userguide/upload-objects.html) in the *Amazon Simple Storage Service User Guide*\. 

For more information about Amazon S3 metadata and details about system\-provided metadata, see [Editing object metadata in the Amazon S3 console](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/add-object-metadata.html) in the *Amazon Simple Storage Service User Guide*\.

Import the gzip file into your RDS for PostgreSQL DB instance as shown following\.

```
postgres=> CREATE TABLE test_gzip(id int, a text, b text, c text, d text);
postgres=> SELECT aws_s3.table_import_from_s3(
 'test_gzip', '', '(format csv)',
 'myS3Bucket', 'test-data.gz', 'us-east-2'
);
```

### Importing an encoded Amazon S3 file<a name="USER_PostgreSQL.S3Import.FileFormats.Encoded"></a>

The following example shows how to import a file from Amazon S3 that has Windows\-1252 encoding\.

```
postgres=> SELECT aws_s3.table_import_from_s3(
 'test_table', '', 'encoding ''WIN1252''',
 aws_commons.create_s3_uri('sampleBucket', 'SampleFile', 'us-east-2')
);
```

## Function reference<a name="USER_PostgreSQL.S3Import.Reference"></a>

**Topics**
+ [aws\_s3\.table\_import\_from\_s3](#aws_s3.table_import_from_s3)
+ [aws\_commons\.create\_s3\_uri](#USER_PostgreSQL.S3Import.create_s3_uri)
+ [aws\_commons\.create\_aws\_credentials](#USER_PostgreSQL.S3Import.create_aws_credentials)

### aws\_s3\.table\_import\_from\_s3<a name="aws_s3.table_import_from_s3"></a>

Imports Amazon S3 data into an Amazon RDS table\. The `aws_s3` extension provides the `aws_s3.table_import_from_s3` function\. The return value is text\.

#### Syntax<a name="aws_s3.table_import_from_s3-syntax"></a>

The required parameters are `table_name`, `column_list` and `options`\. These identify the database table and specify how the data is copied into the table\. 

You can also use the following parameters: 
+ The `s3_info` parameter specifies the Amazon S3 file to import\. When you use this parameter, access to Amazon S3 is provided by an IAM role for the PostgreSQL DB instance\.

  ```
  aws_s3.table_import_from_s3 (
     table_name text, 
     column_list text, 
     options text, 
     s3_info aws_commons._s3_uri_1
  )
  ```
+ The `credentials` parameter specifies the credentials to access Amazon S3\. When you use this parameter, you don't use an IAM role\.

  ```
  aws_s3.table_import_from_s3 (
     table_name text, 
     column_list text, 
     options text, 
     s3_info aws_commons._s3_uri_1,
     credentials aws_commons._aws_credentials_1
  )
  ```

#### Parameters<a name="aws_s3.table_import_from_s3-parameters"></a>

 *table\_name*   
A required text string containing the name of the PostgreSQL database table to import the data into\. 

 *column\_list*   
A required text string containing an optional list of the PostgreSQL database table columns in which to copy the data\. If the string is empty, all columns of the table are used\. For an example, see [Importing an Amazon S3 file that uses a custom delimiter](#USER_PostgreSQL.S3Import.FileFormats.CustomDelimiter)\.

 *options*   
A required text string containing arguments for the PostgreSQL `COPY` command\. These arguments specify how the data is to be copied into the PostgreSQL table\. For more details, see the [PostgreSQL COPY documentation](https://www.postgresql.org/docs/current/sql-copy.html)\.

 *s3\_info*   
An `aws_commons._s3_uri_1` composite type containing the following information about the S3 object:  
+ `bucket` – The name of the Amazon S3 bucket containing the file\.
+ `file_path` – The Amazon S3 file name including the path of the file\.
+ `region` – The AWS Region that the file is in\. For a listing of AWS Region names and associated values, see [Regions, Availability Zones, and Local Zones](Concepts.RegionsAndAvailabilityZones.md)\.

 *credentials*   
An `aws_commons._aws_credentials_1` composite type containing the following credentials to use for the import operation:  
+ Access key
+ Secret key
+ Session token
For information about creating an `aws_commons._aws_credentials_1` composite structure, see [aws\_commons\.create\_aws\_credentials](#USER_PostgreSQL.S3Import.create_aws_credentials)\.

#### Alternate syntax<a name="aws_s3.table_import_from_s3-alternative-syntax"></a>

To help with testing, you can use an expanded set of parameters instead of the `s3_info` and `credentials` parameters\. Following are additional syntax variations for the `aws_s3.table_import_from_s3` function: 
+ Instead of using the `s3_info` parameter to identify an Amazon S3 file, use the combination of the `bucket`, `file_path`, and `region` parameters\. With this form of the function, access to Amazon S3 is provided by an IAM role on the PostgreSQL DB instance\.

  ```
  aws_s3.table_import_from_s3 (
     table_name text, 
     column_list text, 
     options text, 
     bucket text, 
     file_path text, 
     region text 
  )
  ```
+ Instead of using the `credentials` parameter to specify Amazon S3 access, use the combination of the `access_key`, `session_key`, and `session_token` parameters\.

  ```
  aws_s3.table_import_from_s3 (
     table_name text, 
     column_list text, 
     options text, 
     bucket text, 
     file_path text, 
     region text, 
     access_key text, 
     secret_key text, 
     session_token text 
  )
  ```

#### Alternate parameters<a name="aws_s3.table_import_from_s3-alternative-parameters"></a>

*bucket*  
A text string containing the name of the Amazon S3 bucket that contains the file\. 

*file\_path*  
A text string containing the Amazon S3 file name including the path of the file\. 

*region*  
A text string identifying the AWS Region location of the file\. For a listing of AWS Region names and associated values, see [Regions, Availability Zones, and Local Zones](Concepts.RegionsAndAvailabilityZones.md)\.

*access\_key*  
A text string containing the access key to use for the import operation\. The default is NULL\.

*secret\_key*  
A text string containing the secret key to use for the import operation\. The default is NULL\.

*session\_token*  
\(Optional\) A text string containing the session key to use for the import operation\. The default is NULL\.

### aws\_commons\.create\_s3\_uri<a name="USER_PostgreSQL.S3Import.create_s3_uri"></a>

Creates an `aws_commons._s3_uri_1` structure to hold Amazon S3 file information\. Use the results of the `aws_commons.create_s3_uri` function in the `s3_info` parameter of the [aws\_s3\.table\_import\_from\_s3](#aws_s3.table_import_from_s3) function\. 

#### Syntax<a name="USER_PostgreSQL.S3Import.create_s3_uri-syntax"></a>

```
aws_commons.create_s3_uri(
   bucket text,
   file_path text,
   region text
)
```

#### Parameters<a name="USER_PostgreSQL.S3Import.create_s3_uri-parameters"></a>

*bucket*  
A required text string containing the Amazon S3 bucket name for the file\.

*file\_path*  
A required text string containing the Amazon S3 file name including the path of the file\.

*region*  
A required text string containing the AWS Region that the file is in\. For a listing of AWS Region names and associated values, see [Regions, Availability Zones, and Local Zones](Concepts.RegionsAndAvailabilityZones.md)\.

### aws\_commons\.create\_aws\_credentials<a name="USER_PostgreSQL.S3Import.create_aws_credentials"></a>

Sets an access key and secret key in an `aws_commons._aws_credentials_1` structure\. Use the results of the `aws_commons.create_aws_credentials` function in the `credentials` parameter of the [aws\_s3\.table\_import\_from\_s3](#aws_s3.table_import_from_s3) function\. 

#### Syntax<a name="USER_PostgreSQL.S3Import.create_aws_credentials-syntax"></a>

```
aws_commons.create_aws_credentials(
   access_key text,
   secret_key text,
   session_token text
)
```

#### Parameters<a name="USER_PostgreSQL.S3Import.create_aws_credentials-parameters"></a>

*access\_key*  
A required text string containing the access key to use for importing an Amazon S3 file\. The default is NULL\.

*secret\_key*  
A required text string containing the secret key to use for importing an Amazon S3 file\. The default is NULL\.

*session\_token*  
An optional text string containing the session token to use for importing an Amazon S3 file\. The default is NULL\. If you provide an optional `session_token`, you can use temporary credentials\.