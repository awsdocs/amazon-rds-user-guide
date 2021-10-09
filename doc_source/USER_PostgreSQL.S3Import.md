# Importing Amazon S3 data into an RDS for PostgreSQL DB instance<a name="USER_PostgreSQL.S3Import"></a>

You can import data from Amazon S3 into a table belonging to an RDS for PostgreSQL DB instance\. To do this, you use the `aws_s3` PostgreSQL extension that Amazon RDS provides\. 

**Note**  
To import from Amazon S3 into RDS for PostgreSQL, your database must be running PostgreSQL version 10\.7 or later\. 

For more information on storing data with Amazon S3, see [Create a bucket](https://docs.aws.amazon.com/AmazonS3/latest/gsg/CreatingABucket.html) in the *Amazon Simple Storage Service User Guide*\. For instructions on how to upload a file to an Amazon S3 bucket, see [Add an object to a bucket](https://docs.aws.amazon.com/AmazonS3/latest/gsg/PuttingAnObjectInABucket.html) in the *Amazon Simple Storage Service User Guide*\.

**Topics**
+ [Overview of importing Amazon S3 data](#USER_PostgreSQL.S3Import.Overview)
+ [Setting up access to an Amazon S3 bucket](#USER_PostgreSQL.S3Import.AccessPermission)
+ [Using the aws\_s3\.table\_import\_from\_s3 function to import Amazon S3 data](#USER_PostgreSQL.S3Import.FileFormats)
+ [Function reference](#USER_PostgreSQL.S3Import.Reference)

## Overview of importing Amazon S3 data<a name="USER_PostgreSQL.S3Import.Overview"></a>

To import data stored in an Amazon S3 bucket to a PostgreSQL database table, follow these steps\. 

**To import S3 data into Amazon RDS**

1. Install the required PostgreSQL extensions\. These include the `aws_s3` and `aws_commons` extensions\. To do so, start psql and use the following command\. 

   ```
   psql=> CREATE EXTENSION aws_s3 CASCADE;
   NOTICE: installing required extension "aws_commons"
   ```

   The `aws_s3` extension provides the [aws\_s3\.table\_import\_from\_s3](#aws_s3.table_import_from_s3) function that you use to import Amazon S3 data\. The `aws_commons` extension provides additional helper functions\.

1. Identify the database table and Amazon S3 file to use\.

   The [aws\_s3\.table\_import\_from\_s3](#aws_s3.table_import_from_s3) function requires the name of the PostgreSQL database table that you want to import data into\. The function also requires that you identify the Amazon S3 file to import\. To provide this information, take the following steps\.

   1. Identify the PostgreSQL database table to put the data in\. For example, the following is a sample `t1` database table used in the examples for this topic\. 

      ```
      psql=> CREATE TABLE t1 (col1 varchar(80), col2 varchar(80), col3 varchar(80));
      ```

   1. Get the following information to identify the Amazon S3 file that you want to import:
      + Bucket name – A *bucket* is a container for Amazon S3 objects or files\.
      + File path – The file path locates the file in the Amazon S3 bucket\.
      + AWS Region – The AWS Region is the location of the Amazon S3 bucket\. For example, if the S3 bucket is in the US East \(N\. Virginia\) Region, use `us-east-1`\. For a listing of AWS Region names and associated values, see [ Regions, Availability Zones, and Local Zones ](Concepts.RegionsAndAvailabilityZones.md)\.

      To find how to get this information, see [View an object](https://docs.aws.amazon.com/AmazonS3/latest/gsg/OpeningAnObject.html) in the *Amazon Simple Storage Service User Guide*\. You can confirm the information by using the AWS CLI command `aws s3 cp`\. If the information is correct, this command downloads a copy of the Amazon S3 file\. 

      ```
      aws s3 cp s3://sample_s3_bucket/sample_file_path ./ 
      ```

   1. Use the [aws\_commons\.create\_s3\_uri](#USER_PostgreSQL.S3Import.create_s3_uri) function to create an `aws_commons._s3_uri_1` structure to hold the Amazon S3 file information\. You provide this `aws_commons._s3_uri_1` structure as a parameter in the call to the [aws\_s3\.table\_import\_from\_s3](#aws_s3.table_import_from_s3) function\.

      For a psql example, see the following\.

      ```
      psql=> SELECT aws_commons.create_s3_uri(
         'sample_s3_bucket',
         'sample.csv',
         'us-east-1'
      ) AS s3_uri \gset
      ```

1. Provide permission to access the Amazon S3 file\.

   To import data from an Amazon S3 file, give the RDS for PostgreSQL DB instance permission to access the Amazon S3 bucket the file is in\. To do this, you use either an AWS Identity and Access Management \(IAM\) role or security credentials\. For more information, see [Setting up access to an Amazon S3 bucket](#USER_PostgreSQL.S3Import.AccessPermission)\.

1. Import the Amazon S3 data by calling the `aws_s3.table_import_from_s3` function\.

   After you complete the previous preparation tasks, use the [aws\_s3\.table\_import\_from\_s3](#aws_s3.table_import_from_s3) function to import the Amazon S3 data\. For more information, see [Using the aws\_s3\.table\_import\_from\_s3 function to import Amazon S3 data](#USER_PostgreSQL.S3Import.FileFormats)\.

## Setting up access to an Amazon S3 bucket<a name="USER_PostgreSQL.S3Import.AccessPermission"></a>

To import data from an Amazon S3 file, give the RDS for PostgreSQL DB instance permission to access the Amazon S3 bucket the file is in\. You provide access to an Amazon S3 bucket in one of two ways, as described in the following topics\.

**Topics**
+ [Using an IAM role to access an Amazon S3 bucket](#USER_PostgreSQL.S3Import.ARNRole)
+ [Using security credentials to access an Amazon S3 bucket](#USER_PostgreSQL.S3Import.Credentials)
+ [Troubleshooting access to Amazon S3](#USER_PostgreSQL.S3Import.troubleshooting)

### Using an IAM role to access an Amazon S3 bucket<a name="USER_PostgreSQL.S3Import.ARNRole"></a>

Before you load data from an Amazon S3 file, give your RDS for PostgreSQL DB instance permission to access the Amazon S3 bucket the file is in\. This way, you don't have to manage additional credential information or provide it in the [aws\_s3\.table\_import\_from\_s3](#aws_s3.table_import_from_s3) function call\.

To do this, create an IAM policy that provides access to the Amazon S3 bucket\. Create an IAM role and attach the policy to the role\. Then assign the IAM role to your DB instance\. 

**To give an RDS for PostgreSQL DB instance access to Amazon S3 through an IAM role**

1. Create an IAM policy\. 

   This policy provides the bucket and object permissions that allow your RDS for PostgreSQL DB instance to access Amazon S3\. 

   Include in the policy the following required actions to allow the transfer of files from an Amazon S3 bucket to Amazon RDS: 
   + `s3:GetObject` 
   + `s3:ListBucket` 

   Include in the policy the following resources to identify the Amazon S3 bucket and objects in the bucket\. This shows the Amazon Resource Name \(ARN\) format for accessing Amazon S3\.
   + arn:aws:s3:::*your\-s3\-bucket*
   + arn:aws:s3:::*your\-s3\-bucket*/\*

   For more information on creating an IAM policy for Amazon RDS for PostgreSQL, see [Creating and using an IAM policy for IAM database access](UsingWithRDS.IAMDBAuth.IAMPolicy.md)\. See also [Tutorial: Create and attach your first customer managed policy](https://docs.aws.amazon.com/IAM/latest/UserGuide/tutorial_managed-policies.html) in the *IAM User Guide*\.

   The following AWS CLI command creates an IAM policy named `rds-s3-import-policy` with these options\. It grants access to a bucket named `your-s3-bucket`\. 
**Note**  
After you create the policy, note the Amazon Resource Name \(ARN\) of the policy\. You need the ARN for a subsequent step when you attach the policy to an IAM role\.   
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

   You do this so Amazon RDS can assume this IAM role on your behalf to access your Amazon S3 buckets\. For more information, see [Creating a role to delegate permissions to an IAM user](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-user.html) in the *IAM User Guide*\.

   The following example shows using the AWS CLI command to create a role named `rds-s3-import-role`\.   
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
            "Action": "sts:AssumeRole"
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
            "Action": "sts:AssumeRole"
          }
        ] 
      }'
   ```

1. Attach the IAM policy that you created to the IAM role that you created\.

   The following AWS CLI command attaches the policy created earlier to the role named `rds-s3-import-role` Replace `your-policy-arn` with the policy ARN that you noted in an earlier step\.   
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
**Note**  
Also, be sure the database you use doesn't have any restrictions noted in [Importing Amazon S3 data into an RDS for PostgreSQL DB instance](#USER_PostgreSQL.S3Import)\. 

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

If you prefer, you can use security credentials to provide access to an Amazon S3 bucket instead of providing access with an IAM role\. To do this, use the `credentials` parameter in the [aws\_s3\.table\_import\_from\_s3](#aws_s3.table_import_from_s3) function call\. 

The `credentials` parameter is a structure of type `aws_commons._aws_credentials_1`, which contains AWS credentials\. Use the [aws\_commons\.create\_aws\_credentials](#USER_PostgreSQL.S3Import.create_aws_credentials) function to set the access key and secret key in an `aws_commons._aws_credentials_1` structure, as shown following\. 

```
psql=> SELECT aws_commons.create_aws_credentials(
   'sample_access_key', 'sample_secret_key', '')
AS creds \gset
```

After creating the `aws_commons._aws_credentials_1 `structure, use the [aws\_s3\.table\_import\_from\_s3](#aws_s3.table_import_from_s3) function with the `credentials` parameter to import the data, as shown following\.

```
psql=> SELECT aws_s3.table_import_from_s3(
   't', '', '(format csv)',
   :'s3_uri', 
   :'creds'
);
```

Or you can include the [aws\_commons\.create\_aws\_credentials](#USER_PostgreSQL.S3Import.create_aws_credentials) function call inline within the `aws_s3.table_import_from_s3` function call\.

```
psql=> SELECT aws_s3.table_import_from_s3(
   't', '', '(format csv)',
   :'s3_uri', 
   aws_commons.create_aws_credentials('sample_access_key', 'sample_secret_key', '')
);
```

### Troubleshooting access to Amazon S3<a name="USER_PostgreSQL.S3Import.troubleshooting"></a>

If you encounter connection problems when attempting to import Amazon S3 file data, see the following for recommendations:
+ [Troubleshooting Amazon RDS identity and access](security_iam_troubleshoot.md)
+ [Troubleshooting Amazon S3](https://docs.aws.amazon.com/AmazonS3/latest/dev/troubleshooting.html) in the *Amazon Simple Storage Service User Guide*
+ [Troubleshooting Amazon S3 and IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/troubleshoot_iam-s3.html) in the *IAM User Guide*

## Using the aws\_s3\.table\_import\_from\_s3 function to import Amazon S3 data<a name="USER_PostgreSQL.S3Import.FileFormats"></a>

Import your Amazon S3 data by calling the [aws\_s3\.table\_import\_from\_s3](#aws_s3.table_import_from_s3) function\. 

**Note**  
The following examples use the IAM role method for providing access to the Amazon S3 bucket\. Thus, the `aws_s3.table_import_from_s3` function calls don't include credential parameters\.

The following shows a typical PostgreSQL example using psql\.

```
psql=> SELECT aws_s3.table_import_from_s3(
   't1',
   '', 
   '(format csv)',
   :'s3_uri'
);
```

The parameters are the following:
+ `t1` – The name for the table in the PostgreSQL DB instance to copy the data into\. 
+ `''` – An optional list of columns in the database table\. You can use this parameter to indicate which columns of the S3 data go in which table columns\. If no columns are specified, all the columns are copied to the table\. For an example of using a column list, see [Importing an Amazon S3 file that uses a custom delimiter](#USER_PostgreSQL.S3Import.FileFormats.CustomDelimiter)\.
+ `(format csv)` – PostgreSQL COPY arguments\. The copy process uses the arguments and format of the [PostgreSQL COPY](https://www.postgresql.org/docs/current/sql-copy.html) command\. In the preceding example, the `COPY` command uses the comma\-separated value \(CSV\) file format to copy the data\. 
+  `s3_uri` – A structure that contains the information identifying the Amazon S3 file\. For an example of using the [aws\_commons\.create\_s3\_uri](#USER_PostgreSQL.S3Import.create_s3_uri) function to create an `s3_uri` structure, see [Overview of importing Amazon S3 data](#USER_PostgreSQL.S3Import.Overview)\.

The return value is text\. For the full reference of this function, see [aws\_s3\.table\_import\_from\_s3](#aws_s3.table_import_from_s3)\.

The following examples show how to specify different kinds of files when importing Amazon S3 data\.

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
   psql=> CREATE TABLE test (a text, b text, c text, d text, e text);
   ```

1. Use the following form of the [aws\_s3\.table\_import\_from\_s3](#aws_s3.table_import_from_s3) function to import data from the Amazon S3 file\. 

   You can include the [aws\_commons\.create\_s3\_uri](#USER_PostgreSQL.S3Import.create_s3_uri) function call inline within the `aws_s3.table_import_from_s3` function call to specify the file\. 

   ```
   psql=> SELECT aws_s3.table_import_from_s3(
      'test',
      'a,b,d,e',
      'DELIMITER ''|''', 
      aws_commons.create_s3_uri('sampleBucket', 'pipeDelimitedSampleFile', 'us-east-2')
   );
   ```

The data is now in the table in the following columns\.

```
psql=> SELECT * FROM test;
a | b | c | d | e 
---+------+---+---+------+-----------
1 | foo1 | | bar1 | elephant1
2 | foo2 | | bar2 | elephant2
3 | foo3 | | bar3 | elephant3
4 | foo4 | | bar4 | elephant4
```

### Importing an Amazon S3 compressed \(gzip\) file<a name="USER_PostgreSQL.S3Import.FileFormats.gzip"></a>

The following example shows how to import a file from Amazon S3 that is compressed with gzip\. 

Ensure that the file contains the following Amazon S3 metadata:
+ Key: `Content-Encoding`
+ Value: `gzip`

For more about adding these values to Amazon S3 metadata, see [How do I add metadata to an S3 object?](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/add-object-metadata.html) in the *Amazon Simple Storage Service User Guide*\.

Import the gzip file into your RDS for PostgreSQL DB instance as shown following\.

```
psql=> CREATE TABLE test_gzip(id int, a text, b text, c text, d text);
psql=> SELECT aws_s3.table_import_from_s3(
 'test_gzip', '', '(format csv)',
 'myS3Bucket', 'test-data.gz', 'us-east-2'
);
```

### Importing an encoded Amazon S3 file<a name="USER_PostgreSQL.S3Import.FileFormats.Encoded"></a>

The following example shows how to import a file from Amazon S3 that has Windows\-1252 encoding\.

```
psql=> SELECT aws_s3.table_import_from_s3(
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
+ `region` – The AWS Region that the file is in\. For a listing of AWS Region names and associated values, see [ Regions, Availability Zones, and Local Zones ](Concepts.RegionsAndAvailabilityZones.md)\.

 *credentials*   
An `aws_commons._aws_credentials_1` composite type containing the following credentials to use for the import operation:  
+ Access key
+ Secret key
+ Session token
For information about creating an `aws_commons._aws_credentials_1` composite structure, see [aws\_commons\.create\_aws\_credentials](#USER_PostgreSQL.S3Import.create_aws_credentials)\.

#### Alternate syntax<a name="aws_s3.table_import_from_s3-alternative-syntax"></a>

To he lp with testing, you can use an expanded set of parameters instead of the `s3_info` and `credentials` parameters\. Following are additional syntax variations for the `aws_s3.table_import_from_s3` function: 
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
A text string containing the AWS Region that the file is in\. For a listing of AWS Region names and associated values, see [ Regions, Availability Zones, and Local Zones ](Concepts.RegionsAndAvailabilityZones.md)\.

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
A required text string containing the AWS Region that the file is in\. For a listing of AWS Region names and associated values, see [ Regions, Availability Zones, and Local Zones ](Concepts.RegionsAndAvailabilityZones.md)\.

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