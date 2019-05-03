# Importing Amazon S3 Data into an RDS PostgreSQL DB Instance<a name="USER_PostgreSQL.S3Import"></a>

You can import data from the Amazon Simple Storage Service \(Amazon S3\) into a table of an RDS PostgreSQL DB instance\. Amazon RDS provides the `aws_s3` PostgreSQL exension for the import operations\. 

For more information on storing data with Amazon S3, see [Create a Bucket](https://docs.aws.amazon.com/AmazonS3/latest/gsg/CreatingABucket.html) in the *Amazon Simple Storage Service Getting Started Guide*\. For instructions on how to upload a file to an Amazon S3 bucket, see [Add an Object to a Bucket](https://docs.aws.amazon.com/AmazonS3/latest/gsg/PuttingAnObjectInABucket.html) in the *Amazon Simple Storage Service Getting Started Guide*\.

**Note**  
Your database must be running PostgreSQL version 11\.1 or later to import from Amazon S3 to Amazon RDS for PostgreSQL\.

**Topics**
+ [Overview of Importing Amazon S3 Data](#USER_PostgreSQL.S3Import.Overview)
+ [Using an ARN Role to Access an Amazon S3 File](#USER_PostgreSQL.S3Import.ARNRole)
+ [Using Security Credentials to Access an Amazon S3 File](#USER_PostgreSQL.S3Import.Credentials)
+ [Handling Various Amazon S3 File Formats](#USER_PostgreSQL.S3Import.FileFormats)
+ [Function Reference](#USER_PostgreSQL.S3Import.Reference)

## Overview of Importing Amazon S3 Data<a name="USER_PostgreSQL.S3Import.Overview"></a>

You use the `aws_s3.table_import_from_s3` function to import data stored in an Amazon S3 bucket to a PostgreSQL database table\. Before you can use this function, do the following preparation tasks\.

1. Install the `aws_s3` extension\.

   Invoke psql and use the following command to install the required Amazon RDS PostgreSQL extensions\. These include the `aws_s3` and `aws_commons` extensions\.

   ```
   psql=> CREATE EXTENSION aws_s3 CASCADE;
   NOTICE: installing required extension "aws_commons"
   ```

   The `aws_s3` extension provides the `table_import_from_s3` function you use to import Amazon S3 data\. The rest of these steps describe your options for supplying the required information for the [aws\_s3\.table\_import\_from\_s3](#USER_PostgreSQL.S3Import.table_import_from_s3) function\.

1. Provide permission to access the Amazon S3 file\.

   Before data can be loaded from an Amazon S3 file, you must first give your RDS PostgreSQL DB instance permission to access Amazon S3\. You can provide permission by creating an ARN role that has access to the Amazon S3 bucket\. You then assign the role to the RDS PostgreSQL DB instance\. 

   For details on setting up an ARN role to provide access permission, see [Using an ARN Role to Access an Amazon S3 File](#USER_PostgreSQL.S3Import.ARNRole)\.

1. Specify the Amazon S3 file\.

   Identify an Amazon S3 file using the following three items:
   + Bucket name \- A bucket is a container for Amazon S3 objects or files\.
   + File path \- The file path locates the file in the Amazon S3 bucket\.
   + Region \- The region is the geographical location of the Amazon S3 bucket\.

   To get this information, see [View an Object](https://docs.aws.amazon.com/AmazonS3/latest/gsg/OpeningAnObject.html) in the *Amazon Simple Storage Service Getting Started Guide*\. 

   Provide this file information in the `s3_info` parameter of the [aws\_s3\.table\_import\_from\_s3](#USER_PostgreSQL.S3Import.table_import_from_s3) function call\. The `s3_info` parameter is a structure of type `aws_commons._s3_uri_1`\. Use the [aws\_commons\.create\_s3\_uri](#USER_PostgreSQL.S3Import.create_s3_uri) function to create an `aws_commons._s3_uri_1` structure to hold the file information\. For example\.

   ```
   psql=> SELECT aws_commons.create_s3_uri(
      'sample_s3_bucket',
      'sample.csv',
      'us-east-1'
   ) AS s3_uri \gset
   ```

1. Identify your PostgreSQL database table in which to put the data\. For example, the following is a sample `t1` database table\.

   ```
   psql=> CREATE TABLE t1 (bid bigint PRIMARY KEY, name varchar(80));
   ```

After you've completed the preparation tasks, use the [aws\_s3\.table\_import\_from\_s3](#USER_PostgreSQL.S3Import.table_import_from_s3) function to import Amazon S3 data\. The following shows a typical PostgreSQL example using psql\.

```
psql=> SELECT aws_s3.table_import_from_s3(
   't1',
   '', 
   '(format csv)',
   :'s3_uri'
);
```

The parameters include the following\.
+ Table name \(`t1`\) – The table in the PostgreSQL DB instance in which to copy the data\. 
+ Column list \(`''`\) – An optional list of columns in the database table\. You can use this parameter to indicate which columns of the S3 data go in which of the table's columns\. If no columns are specified, all the columns are copied to the table\. For an example of using a column list, see [Importing a File that uses a Custom Delimiter](#USER_PostgreSQL.S3Import.FileFormats.CustomDelimiter)\.
+ PostgreSQL COPY arguments \(`(format csv)`\) – The copy process uses the arguments and format of the [PostgreSQL COPY](https://www.postgresql.org/docs/current/sql-copy.html) command\. In this example, the `COPY` command uses the comma separated values \(CSV\) file format to copy the data\. For more examples, see [Handling Various Amazon S3 File Formats](#USER_PostgreSQL.S3Import.FileFormats)\.
+ S3 file identification \(`s3_uri`\) – A structure that contains the information identifying the Amazon S3 file\. 

## Using an ARN Role to Access an Amazon S3 File<a name="USER_PostgreSQL.S3Import.ARNRole"></a>

Before data can be loaded from an Amazon S3 file, you must first give your RDS PostgreSQL DB instance permission to access Amazon S3\. In this method of accessing an Amazon S3 file, you create an ARN role that has access to the Amazon S3 bucket and then assign the role to the RDS PostgreSQL DB instance\. With this method, no additional credential information is required in the `aws_s3.table_import_from_s3` function call\. 

**To give RDS PostgreSQL ARN role access to Amazon S3**

1. Create an AWS Identity and Access Management \(IAM\) policy to provide the bucket and object permissions that allow your RDS PostgreSQL DB instance to access Amazon S3\. Include the following required actions in the policy to allow the transfer of files from an Amazon S3 bucket to Amazon RDS\. 
   + GetObject 
   + ListBucket 

   The following AWS CLI command creates an IAM policy named `rds-s3-integration-policy` with these options\. It grants access to a bucket named `your-s3-bucket-arn`\. 
**Note**  
After you create the policy, note the Amazon Resource Name \(ARN\) of the policy\. You need the ARN for a subsequent step\. 

------
#### [ For Linux, OS X, or Unix ]

   ```
   aws iam create-policy \
      --policy-name rds-s3-integration-policy \
      --policy-document '{
        "Version": "2012-10-17",
        "Statement": [
          {
            "Sid": "s3integration",
            "Action": [
              "s3:GetObject",
              "s3:ListBucket",
              "s3:PutObject"
            ],
            "Effect": "Allow",
            "Resource": [
              "arn:aws:s3:::your-s3-bucket-arn", 
              "arn:aws:s3:::your-s3-bucket-arn/*"
            ] 
          }
        ] 
      }'
   ```

------
#### [ For Windows ]

   ```
   aws iam create-policy ^
      --policy-name rds-s3-integration-policy ^
      --policy-document '{
        "Version": "2012-10-17",
        "Statement": [
          {
            "Sid": "s3integration",
            "Action": [
              "s3:GetObject",
              "s3:ListBucket",
              "s3:PutObject"
            ], 
            "Effect": "Allow",
            "Resource": [
              "arn:aws:s3:::your-s3-bucket-arn", 
              "arn:aws:s3:::your-s3-bucket-arn/*"
            ] 
          }
        ] 
      }'
   ```

------

1. Create an IAM role that Amazon RDS can assume on your behalf to access your Amazon S3 buckets\. For more information, see [Creating a Role to Delegate Permissions to an IAM User](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-user.html) in the *IAM User Guide*\.

   The following example shows using the AWS CLI command to create a role named `rds-s3-integration-role`\. 

------
#### [ For Linux, OS X, or Unix ]

   ```
   aws iam create-role \
      --role-name rds-s3-integration-role \
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

------
#### [ For Windows ]

   ```
   aws iam create-role ^
      --role-name rds-s3-integration-role ^
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

------

1. Attach the policy you created to the role you created\.

   The following AWS CLI command attaches the policy created earlier to the role named `rds-s3-integration-role`\. Replace `your-policy-arn` with the policy ARN that you noted in a previous step\. 

------
#### [ For Linux, OS X, or Unix ]

   ```
   aws iam attach-role-policy \
      --policy-arn your-policy-arn \
      --role-name rds-s3-integration-role
   ```

------
#### [ For Windows: ]

   ```
   aws iam attach-role-policy ^
      --policy-arn your-policy-arn ^
      --role-name rds-s3-integration-role
   ```

------

1. Add the role to the PostgreSQL DB instance using the AWS Management Console or AWS CLI\.

### Console<a name="collapsible-section-1"></a>

Do the following to add the role to the PostgreSQL DB instance using the AWS Management Console\.

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console.aws.amazon.com/rds/](https://console.aws.amazon.com/rds/)\. 

1. Choose the PostgreSQL DB instance name to display its details\.

1. On the **Connectivity & security** tab, in the **Manage IAM roles **section, choose the role to add under **Add IAM roles to this instance**\. 

1. Under Feature, choose **s3Import**\.

1. Choose **Add role**\.

### CLI<a name="collapsible-section-2"></a>

Use the following command to add the role to the PostgreSQL DB instance named `mydbinstance`\. Replace `your-role-arn` with the role ARN that you noted in a previous step\. Use `s3Import` for the value of the `--feature-name` option\. 

------
#### [ For Linux, OS X, or Unix ]

```
aws rds add-role-to-db-instance \
   --db-instance-identifier mydbinstance \
   --feature-name s3Import \
   --role-arn your-role-arn   \
   --region us-west-2
```

------
#### [ For Windows: ]

```
aws rds add-role-to-db-instance ^
   --db-instance-identifier mydbinstance ^
   --feature-name s3Import ^
   --role-arn your-role-arn ^
   --region us-west-2
```

------

## Using Security Credentials to Access an Amazon S3 File<a name="USER_PostgreSQL.S3Import.Credentials"></a>

Instead of using an ARN role to access an Amazon S3 file, you can provide the access key and secret key in the `credentials` parameter of the [aws\_s3\.table\_import\_from\_s3](#USER_PostgreSQL.S3Import.table_import_from_s3) function call\. 

The `credentials` parameter is a structure of type `aws_commons._aws_credentials_1`, which contains AWS credentials\. Use the [aws\_commons\.create\_aws\_credentials](#USER_PostgreSQL.S3Import.create_aws_credentials) function to set the access key and secret key in an `aws_commons._aws_credentials_1` structure\. For example\. 

```
psql=> SELECT aws_commons.create_aws_credentials(
   '<sample_access_key>', '<sample_secret_key>', '')
AS creds \gset
```

After creating the `aws_commons._aws_credentials_1 `structure, you can then use the [aws\_s3\.table\_import\_from\_s3](#USER_PostgreSQL.S3Import.table_import_from_s3) function to import the data\. For example\.

```
psql=> SELECT aws_s3.table_import_from_s3(
   't', '', '(format csv)',
   :'s3_uri', 
   :'creds'
);
```

As an alternative, you can include the [aws\_commons\.create\_aws\_credentials](#USER_PostgreSQL.S3Import.create_aws_credentials) function call inline within the `aws_s3.table_import_from_s3` function call\.

```
psql=> SELECT aws_s3.table_import_from_s3(
   't', '', '(format csv)',
   :'s3_uri', 
   aws_commons.create_aws_credentials('<sample_access_key>', '<sample_secret_key>', '')
);
```

## Handling Various Amazon S3 File Formats<a name="USER_PostgreSQL.S3Import.FileFormats"></a>

The following examples show how to specify different kinds of files when importing\.

**Topics**
+ [Importing a File that uses a Custom Delimiter](#USER_PostgreSQL.S3Import.FileFormats.CustomDelimiter)
+ [Importing a Compressed \(GZIP\) File](#USER_PostgreSQL.S3Import.FileFormats.gzip)
+ [Importing an Encoded File](#USER_PostgreSQL.S3Import.FileFormats.Encoded)

**Note**  
The following examples use the ARN role method for providing access to the Amazon S3 file so there are no credential parameters in the `aws_s3.table_import_from_s3` function calls\.

### Importing a File that uses a Custom Delimiter<a name="USER_PostgreSQL.S3Import.FileFormats.CustomDelimiter"></a>

The following example also shows how to control where to put the data in the database table using the `column_list` parameter of the [aws\_s3\.table\_import\_from\_s3](#USER_PostgreSQL.S3Import.table_import_from_s3) function\. 

For this example, assume the following information is organized into pipe\-delimited columns in the Amazon S3 file\.

```
1|foo1|bar1|elephant1
2|foo2|bar2|elephant2
3|foo3|bar3|elephant3
4|foo4|bar4|elephant4
...
```

1. Create a table in the database for the imported data\.

   ```
   psql=> CREATE TABLE test (a text, b text, c text, d text, e text);
   CREATE TABLE
   ```

1. Use the following form of the [aws\_s3\.table\_import\_from\_s3](#USER_PostgreSQL.S3Import.table_import_from_s3) function to import data from the Amazon S3 file\. 

   Note, you can include the [aws\_commons\.create\_s3\_uri](#USER_PostgreSQL.S3Import.create_s3_uri) function call inline within the `aws_s3.table_import_from_s3` function call to specify the file\. 

   ```
   psql=> SELECT aws_s3.table_import_from_s3(
      'test',
      'a,b,d,e',
      'DELIMITER ''|''', 
      aws_commons.create_s3_uri('sampleBucket', 'pipeDelimitedSampleFile', 'us-east-2')
   );
   ```

1. The data is now in the table in the following columns\.

   ```
   psql=> SELECT * FROM test;
   a | b | c | d | e 
   ---+------+---+---+------+-----------
   1 | foo1 | | bar1 | elephant1
   2 | foo2 | | bar2 | elephant2
   3 | foo3 | | bar3 | elephant3
   4 | foo4 | | bar4 | elephant4
   ```

### Importing a Compressed \(GZIP\) File<a name="USER_PostgreSQL.S3Import.FileFormats.gzip"></a>

The following example shows how to import a file from Amazon S3 that is compressed with gzip\. 

Ensure that the file contains the following Amazon S3 metadata\.
+ Key: Content\-Encoding
+ Value: gzip

For more about Amazon S3 metadata, see [How Do I Add Metadata to an S3 Object?](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/add-object-metadata.html) in the *Amazon Simple Storage Service Console User Guide*\.

Import the gzip file into your RDS PostgreSQL DB instance\.

```
psql=> CREATE TABLE test_gzip(id int, a text, b text, c text, d text);
CREATE TABLE
psql=> SELECT aws_s3.table_import_from_s3(
 'test_gzip', '', '(format csv)',
 'myS3Bucket', 'test-data.gz', 'us-east-2'
);
```

### Importing an Encoded File<a name="USER_PostgreSQL.S3Import.FileFormats.Encoded"></a>

The following example shows how to import a file from Amazon S3 that has Windows\-1252 encoding\.

```
psql=> SELECT aws_s3.table_import_from_s3(
 'test_table', '', 'encoding ''WIN1252''',
 aws_commons.create_s3_uri('sampleBucket', 'SampleFile', 'us-east-2')
);
```

## Function Reference<a name="USER_PostgreSQL.S3Import.Reference"></a>

**Topics**
+ [The aws\_s3\.table\_import\_from\_s3 Function](#USER_PostgreSQL.S3Import.table_import_from_s3)
+ [The aws\_commons\.create\_s3\_uri Function](#USER_PostgreSQL.S3Import.create_s3_uri)
+ [The aws\_commons\.create\_aws\_credentials Function](#USER_PostgreSQL.S3Import.create_aws_credentials)

### The aws\_s3\.table\_import\_from\_s3 Function<a name="USER_PostgreSQL.S3Import.table_import_from_s3"></a>

The `aws_s3` extension provides the `table_import_from_s3` function you use to import Amazon S3 data\. 

The first three required parameters include `table_name`, `column_list` and `options`\. These identify the database table and specify how the data will be copied into the table\. Additional parameters are shown in the following syntax variations for the function\. 
+ The `s3_info` parameter specifies the Amazon S3 file\. With this form of the function, access to Amazon S3 is provided by an ARN role on the PostgreSQL DB instance\.

  ```
  aws_s3.table_import_from_s3 (
     table_name text, 
     column_list text, 
     options text, 
     s3_info aws_commons._s3_uri_1
  )
  ```
+ The `credentials` parameter specifies the credentials to access Amazon S3\.

  ```
  aws_s3.table_import_from_s3 (
     table_name text, 
     column_list text, 
     options text, 
     s3_info aws_commons._s3_uri_1,
     credentials aws_commons._aws_credentials_1
  )
  ```

The `aws_s3.table_import_from_s3` function parameters are described in the following table\.


****  

| Parameter | Description | 
| --- | --- | 
| table\_name | A required text string containing the name of the PostgreSQL database table to import the data into\. | 
| column\_list |  A required text string containing an optional list of the PostgreSQL database table columns in which to copy the data\. If the string is empty, all columns of the table will be used\. For an example, see [Importing a File that uses a Custom Delimiter](#USER_PostgreSQL.S3Import.FileFormats.CustomDelimiter)\.  | 
| options |  A required text string containing arguments for the PostgreSQL `COPY` command\. These arguments specify how the data is to be copied into the PostgreSQL table\. For more details see the [PostgreSQL COPY documentation](https://www.postgresql.org/docs/current/sql-copy.html)\.  | 
| s3\_info |  An `aws_commons._s3_uri_1` composite type containing the following information about the s3 object\. [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_PostgreSQL.S3Import.html) To create an `aws_commons._s3_uri_1` composite structure, see [The aws\_commons\.create\_s3\_uri Function](#USER_PostgreSQL.S3Import.create_s3_uri)\.  | 
| credentials |  An `aws_commons._aws_credentials_1` composite type containing the following credentials to use for the import operation\. [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_PostgreSQL.S3Import.html) To create an `aws_commons._aws_credentials_1` composite structure, see [The aws\_commons\.create\_aws\_credentials Function](#USER_PostgreSQL.S3Import.create_aws_credentials)\.  | 

**Alternate Parameters**  
To help with testing, an expanded set of parameters can be used instead of the `s3_info` and `credentials` parameters\. Following are additional syntax variations for the `aws_s3.table_import_from_s3` function\. 
+ Instead of using the `s3_info` parameter to identify an Amazon S3 file, use the combination of the `bucket`, `file_path`, and `region` parameters\. With this form of the function, access to Amazon S3 is provided by an ARN role on the PostgreSQL DB instance\.

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
+ instead of using the `credentials` parameter to specify Amazon S3 access, use the combination of the `access_key`, `session_key`, and `session_token` parameters\.

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

The alternate parameters for the `aws_s3.table_import_from_s3` function are described in the following table\.


****  

| Parameter | Description | 
| --- | --- | 
| bucket |  A text string containing the name of the Amazon S3 bucket that contains the file\.   | 
| file\_path |  A text string containing the Amazon S3 path of the file\.   | 
| region | A text string containing the Amazon region the file is in\.  | 
| access\_key | A text string containing the access key to use for the import operation\. The default is NULL\.  | 
| secret\_key | A text string containing the secret key to use for the import operation\. The default is NULL\.  | 
| session\_token | An optional text string containing the session key to use for the import operation\. The default is NULL\.  | 

### The aws\_commons\.create\_s3\_uri Function<a name="USER_PostgreSQL.S3Import.create_s3_uri"></a>

Use the `aws_commons.create_s3_uri` function to create an `aws_commons._s3_uri_1` structure to hold Amazon S3 file information\. You then use the results of this function in the `s3_info` parameter of the [aws\_s3\.table\_import\_from\_s3](#USER_PostgreSQL.S3Import.table_import_from_s3) function\. The function syntax is as follows\.

```
aws_commons.create_s3_uri(
   bucket text,
   file_path text,
   region text
)
```

The `aws_commons.create_s3_uri` function parameters are described in the following table\.


****  

| Parameter | Description | 
| --- | --- | 
| bucket |  A required text string containing the Amazon S3 bucket name for the file\.  | 
| file\_path |  A required text string containing the Amazon S3 path of the file\.  | 
| region |  A required text string containing the region the file is in\.  | 

### The aws\_commons\.create\_aws\_credentials Function<a name="USER_PostgreSQL.S3Import.create_aws_credentials"></a>

Use the `aws_commons.create_aws_credentials` function to set the access key and secret key in an `aws_commons._aws_credentials_1` structure\. You then use the results of this function in the `credentials` parameter of the [aws\_s3\.table\_import\_from\_s3](#USER_PostgreSQL.S3Import.table_import_from_s3) function\. The function syntax is as follows\.

```
aws_commons.create_aws_credentials(
   access_key text,
   secret_key text,
   session_token text
)
```

The `aws_commons.create_aws_credentials` function parameters are described in the following table\.


****  

| Parameter | Description | 
| --- | --- | 
| access\_key |  A required text string containing the access key to use for importing an Amazon S3 file\. The default is NULL\.  | 
| secret\_key | A required text string containing the secret key to use for importing an Amazon S3 file\. The default is NULL\. | 
| session\_token | An optional text string containing the session token to use for importing an Amazon S3 file\. The default is NULL\. Note, if you provide an optional session\_token, you can use temporary credentials\. | 