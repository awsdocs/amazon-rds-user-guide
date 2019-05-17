# Importing Data into PostgreSQL on Amazon RDS<a name="PostgreSQL.Procedural.Importing"></a>

Suppose that you have an existing PostgreSQL deployment that you want to move to Amazon RDS\. The complexity of your task depends on the size of your database and the types of database objects that you're transferring\. For example, consider a database that contains datasets on the order of gigabytes, along with stored procedures and triggers\. Such a database is going to be more complicated than a simple database with only a few megabytes of test data and no triggers or stored procedures\. 

We recommend that you use native PostgreSQL database migration tools under the following conditions:
+ You have a homogeneous migration, where you are migrating from a database with the same database engine as the target database\.
+ You are migrating an entire database\.
+ The native tools allow you to migrate your system with minimal downtime\.

In most other cases, performing a database migration using AWS Database Migration Service \(AWS DMS\) is the best approach\. AWS DMS can migrate databases without downtime and, for many database engines, continue ongoing replication until you are ready to switch over to the target database\. You can migrate to either the same database engine or a different database engine using AWS DMS\. If you are migrating to a different database engine than your source database, you can use the AWS Schema Conversion Tool \(AWS SCT\)\. You use AWS SCT to migrate schema objects that are not migrated by AWS DMS\. For more information about AWS DMS, see [ What Is AWS Database Migration Service?](https://docs.aws.amazon.com/dms/latest/userguide/Welcome.html)

Modify your DB parameter group to include the following settings *for your import only*\. You should test the parameter settings to find the most efficient settings for your DB instance size\. You also need to revert back to production values for these parameters after your import completes\.

Modify your DB instance settings to the following:
+ Disable DB instance backups \(set backup\_retention to 0\)\.
+ Disable Multi\-AZ\.

Modify your DB parameter group to include the following settings\. You should only use these settings when importing data\. You should test the parameter settings to find the most efficient settings for your DB instance size\. You also need to revert back to production values for these parameters after your import completes\.


| Parameter | Recommended Value When Importing | Description | 
| --- | --- | --- | 
|  `maintenance_work_mem`  |  524288, 1048576, 2097152 or 4194304 \(in KB\)\. These settings are comparable to 512 MB, 1 GB, 2 GB, and 4 GB\.  |  The value for this setting depends on the size of your host\. This parameter is used during CREATE INDEX statements and each parallel command can use this much memory\. Calculate the best value so that you don't set this value so high that you run out of memory\.  | 
|  `checkpoint_segments`  |   256  |  The value for this setting consumes more disk space, but gives you less contention on your WAL logs\. For PostgreSQL versions 9\.5\.x and 9\.6\.x, this value would be `max_wal_size`\.  | 
|  `checkpoint_timeout`  |   1800  |  The value for this setting allows for less frequent WAL rotation\.  | 
|  `synchronous_commit`  |  Off  |  Disable this setting to speed up writes\. Turning this parameter off can increase the risk of data loss in the event of a server crash \(do not turn off FSYNC\)  | 
|  `wal_buffers`  |   8192  |  This is value is in 8 KB units\. This again helps your WAL generation speed  | 
|  `autovacuum`  |  Off  |  Disable the PostgreSQL auto vacuum parameter while you are loading data so that it doesn’t use resources  | 

Use the `pg_dump -Fc` \(compressed\) or `pg_restore -j` \(parallel\) commands with these settings\.

**Note**  
The PostgreSQL command `pg_dumpall` requires super\_user permissions that are not granted when you create a DB instance, so it cannot be used for importing data\.

**Topics**
+ [Importing a PostgreSQL Database from an Amazon EC2 Instance](#PostgreSQL.Procedural.Importing.EC2)
+ [Using the \\copy Command to Import Data to a Table on a PostgreSQL DB Instance](#PostgreSQL.Procedural.Importing.Copy)
+ [Importing Amazon S3 Data into an RDS for PostgreSQL DB Instance](#USER_PostgreSQL.S3Import)

## Importing a PostgreSQL Database from an Amazon EC2 Instance<a name="PostgreSQL.Procedural.Importing.EC2"></a>

If you have data in a PostgreSQL server on an Amazon EC2 instance and want to move it to a PostgreSQL DB instance, you can use the following process\. The following list shows the steps to take\. Each step is discussed in more detail in the following sections\.

1. Create a file using pg\_dump that contains the data to be loaded

1. Create the target DB instance

1. Use *psql* to create the database on the DB instance and load the data

1. Create a DB snapshot of the DB instance

### Step 1: Create a File Using pg\_dump That Contains the Data to Load<a name="PostgreSQL.Procedural.Importing.EC2.Step1"></a>

The `pg_dump` utility uses the COPY command to create a schema and data dump of a PostgreSQL database\. The dump script generated by `pg_dump` loads data into a database with the same name and recreates the tables, indexes, and foreign keys\. You can use the `pg_restore` command and the `-d` parameter to restore the data to a database with a different name\.

Before you create the data dump, you should query the tables to be dumped to get a row count so you can confirm the count on the target DB instance\.

 The following command creates a dump file called mydb2dump\.sql for a database called mydb2\.

```
prompt>pg_dump dbname=mydb2 -f mydb2dump.sql 
```

### Step 2: Create the Target DB Instance<a name="PostgreSQL.Procedural.Importing.EC2.Step2"></a>

Create the target PostgreSQL DB instance using either the Amazon RDS console, AWS CLI, or API\. Create the instance with the backup retention setting set to 0 and disable Multi\-AZ\. Doing so allows faster data import\. You must create a database on the instance before you can dump the data\. The database can have the same name as the database that is contained the dumped data\. Alternatively, you can create a database with a different name\. In this case, you use the `pg_restore` command and the `-d` parameter to restore the data into the newly named database\.

For example, the following commands can be used to dump, restore, and rename a database\.

```
pg_dump -Fc -v -h [endpoint of instance] -U [master username] [database] > [database].dump
createdb [new database name]
pg_restore -v -h [endpoint of instance] -U [master username] -d [new database name] [database].dump
```

### Step 3: Use psql to Create the Database on the DB Instance and Load Data<a name="PostgreSQL.Procedural.Importing.EC2.Step3"></a>

You can use the same connection you used to execute the pg\_dump command to connect to the target DB instance and recreate the database\. Using *psql*, you can use the master user name and master password to create the database on the DB instance

The following example uses *psql* and a dump file named mydb2dump\.sql to create a database called mydb2 on a PostgreSQL DB instance called mypginstance:

For Linux, OS X, or Unix:

```
psql \
   -f mydb2dump.sql \
   --host mypginstance.c6c8mntzhgv0.us-west-2.rds.amazonaws.com \
   --port 8199 \
   --username myawsuser \
   --password password \
   --dbname mydb2
```

For Windows:

```
psql ^
   -f mydb2dump.sql ^
   --host mypginstance.c6c8mntzhgv0.us-west-2.rds.amazonaws.com ^
   --port 8199 ^
   --username myawsuser ^
   --password password ^
   --dbname mydb2
```

### Step 4: Create a DB Snapshot of the DB Instance<a name="PostgreSQL.Procedural.Importing.EC2.Step4"></a>

Once you have verified that the data was loaded into your DB instance, we recommend that you create a DB snapshot of the target PostgreSQL DB instance\. DB snapshots are complete backups of your DB instance that can be used to restore your DB instance to a known state\. A DB snapshot taken immediately after the load protects you from having to load the data again in case of a mishap\. You can also use such a snapshot to seed new DB instances\. For information about creating a DB snapshot, see [Creating a DB Snapshot](USER_CreateSnapshot.md)\.

## Using the \\copy Command to Import Data to a Table on a PostgreSQL DB Instance<a name="PostgreSQL.Procedural.Importing.Copy"></a>

You can run the `\copy` command from the *psql* prompt to import data into a table on a PostgreSQL DB instance\. The table must already exist on the DB instance\. For more information on the \\copy command, see the [PostgreSQL documentation](http://www.postgresql.org/docs/current/static/app-psql.html)\.

**Note**  
The `\copy` command doesn't provide confirmation of actions, such as a count of rows inserted\. PostgreSQL does provide error messages if the copy command fails due to an error\.

Create a \.csv file from the data in the source table, log on to the target database on the PostgreSQL instance using *psql*, and then run the following command\. This example uses *source\-table* as the source table name, *source\-table\.csv* as the \.csv file, and *target\-db* as the target database:

```
target-db=> \copy source-table from 'source-table.csv' with DELIMITER ','; 
```

You can also run the following command from your client computer command prompt\. This example uses *source\-table* as the source table name, *source\-table\.csv* as the \.csv file, and *target\-db* as the target database:

For Linux, OS X, or Unix:

```
$psql target-db \
    -U <admin user> \
    -p <port> \
    -h <DB instance name> \
    -c "\copy source-table from 'source-table.csv' with DELIMITER ','"
```

For Windows:

```
$psql target-db ^
    -U <admin user> ^
    -p <port> ^
    -h <DB instance name> ^
    -c "\copy source-table from 'source-table.csv' with DELIMITER ','"
```

## Importing Amazon S3 Data into an RDS for PostgreSQL DB Instance<a name="USER_PostgreSQL.S3Import"></a>

You can import data from Amazon S3 into a table belonging to an Amazon RDS for PostgreSQL DB instance\. To do this, you can use the `aws_s3` PostgreSQL extension that Amazon RDS provides\. 

For more information on storing data with Amazon S3, see [Create a Bucket](https://docs.aws.amazon.com/AmazonS3/latest/gsg/CreatingABucket.html) in the *Amazon Simple Storage Service Getting Started Guide*\. For instructions on how to upload a file to an Amazon S3 bucket, see [Add an Object to a Bucket](https://docs.aws.amazon.com/AmazonS3/latest/gsg/PuttingAnObjectInABucket.html) in the *Amazon Simple Storage Service Getting Started Guide*\.

**Note**  
Your database must be running PostgreSQL version 11\.1 or later to import from Amazon S3 to Amazon RDS for PostgreSQL\.

**Topics**
+ [Collecting and Importing Amazon S3 Data](#USER_PostgreSQL.S3Import.Overview)
+ [Using an IAM Role to Access an Amazon S3 File](#USER_PostgreSQL.S3Import.ARNRole)
+ [Using Security Credentials to Access an Amazon S3 File](#USER_PostgreSQL.S3Import.Credentials)
+ [Handling Amazon S3 File Formats](#USER_PostgreSQL.S3Import.FileFormats)
+ [Function Reference](#USER_PostgreSQL.S3Import.Reference)

### Collecting and Importing Amazon S3 Data<a name="USER_PostgreSQL.S3Import.Overview"></a>

To import data stored in an Amazon S3 bucket to a PostgreSQL database table, collect the following information and then use the `aws_s3.table_import_from_s3` function, as described\. 

**To import S3 data into RDS PostgreSQL**

1. Start psql and use the following command to install the required Amazon RDS PostgreSQL extensions\. These include the `aws_s3` and `aws_commons` extensions\.

   ```
   psql=> CREATE EXTENSION aws_s3 CASCADE;
   NOTICE: installing required extension "aws_commons"
   ```

   The `aws_s3` extension provides the [aws\_s3\.table\_import\_from\_s3](#USER_PostgreSQL.S3Import.table_import_from_s3) function that you use to import Amazon S3 data\. 

1. Provide permission to access the Amazon S3 file that you want to import\. To do so, create an AWS Identity and Access Management \(IAM\) role that has access to the Amazon S3 bucket the file is in\. You then assign the role to the RDS PostgreSQL DB instance\. 

   For details on setting up an IAM role to provide access permission, see [Using an IAM Role to Access an Amazon S3 File](#USER_PostgreSQL.S3Import.ARNRole)\.

1. Collect the required information for the `table_import_from_s3` function by taking the following steps:

   1. Get the following information for the Amazon S3 file that you want to import:
      + Bucket name – A bucket is a container for Amazon S3 objects or files\.
      + File path – The file path locates the file in the Amazon S3 bucket\.
      + AWS Region – The AWS Region is the location of the Amazon S3 bucket\.

      To find how to get this information, see [View an Object](https://docs.aws.amazon.com/AmazonS3/latest/gsg/OpeningAnObject.html) in the *Amazon Simple Storage Service Getting Started Guide*\. 

   1. Use the [aws\_commons\.create\_s3\_uri](#USER_PostgreSQL.S3Import.create_s3_uri) function to create an `aws_commons._s3_uri_1` structure to hold this file information, as shown in the following example\.

      ```
      psql=> SELECT aws_commons.create_s3_uri(
         'sample_s3_bucket',
         'sample.csv',
         'us-east-1'
      ) AS s3_uri \gset
      ```

      You later provide this `aws_commons._s3_uri_1` structure in the `s3_info` parameter for the call to the [aws\_s3\.table\_import\_from\_s3](#USER_PostgreSQL.S3Import.table_import_from_s3) function\.

   1. Identify the PostgreSQL database table in which to put the data\. For example, the following is a sample `t1` database table\.

      ```
      psql=> CREATE TABLE t1 (bid bigint PRIMARY KEY, name varchar(80));
      ```

1. After you complete the preparation tasks, use the [aws\_s3\.table\_import\_from\_s3](#USER_PostgreSQL.S3Import.table_import_from_s3) function to import Amazon S3 data\. 

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
+ `t1` – The name for the table in the PostgreSQL DB instance into which to copy the data\. 
+ `''` – An optional list of columns in the database table\. You can use this parameter to indicate which columns of the S3 data go in which table columns\. If no columns are specified, all the columns are copied to the table\. For an example of using a column list, see [Importing a File That Uses a Custom Delimiter](#USER_PostgreSQL.S3Import.FileFormats.CustomDelimiter)\.
+ `(format csv)` – PostgreSQL COPY arguments\. The copy process uses the arguments and format of the [PostgreSQL COPY](https://www.postgresql.org/docs/current/sql-copy.html) command\. In the preceding example, the `COPY` command uses the comma\-separated value \(CSV\) file format to copy the data\. For more examples, see [Handling Amazon S3 File Formats](#USER_PostgreSQL.S3Import.FileFormats)\.
+  `s3_uri` – A structure that contains the information identifying the Amazon S3 file\. 

For more information on this function, see [aws\_s3\.table\_import\_from\_s3](#USER_PostgreSQL.S3Import.table_import_from_s3)\.

### Using an IAM Role to Access an Amazon S3 File<a name="USER_PostgreSQL.S3Import.ARNRole"></a>

Before you load data from an Amazon S3 file, give your RDS PostgreSQL DB instance permission to access Amazon S3\. To do this, create an IAM role that has access to the Amazon S3 bucket and then assign the role to your DB instance\. This way, you don't have to provide or manage additional credential information\. 

**To give an RDS PostgreSQL DB instance access to Amazon S3 through an IAM role**

1. Create an IAM policy to provide the bucket and object permissions that allow your RDS PostgreSQL DB instance to access Amazon S3\. 

   Include the following required actions in the policy to allow the transfer of files from an Amazon S3 bucket to Amazon RDS: 
   + `GetObject` 
   + `ListBucket` 

   For more information on creating an IAM policy for RDS PostgreSQL, see [Creating and Using an IAM Policy for IAM Database Access](UsingWithRDS.IAMDBAuth.IAMPolicy.md)\.

   The following AWS CLI command creates an IAM policy named `rds-s3-integration-policy` with these options\. It grants access to a bucket named `your-s3-bucket-arn`\. 
**Note**  
After you create the policy, note the Amazon Resource Name \(ARN\) of the policy\. You need the ARN for a subsequent step in the import process\.   
**Example**  

   For Linux, OS X, or Unix:

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

   For Windows:

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

1. Create an IAM role that Amazon RDS can assume on your behalf to access your Amazon S3 buckets\. For more information, see [Creating a Role to Delegate Permissions to an IAM User](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-user.html) in the *IAM User Guide*\.

   The following example shows using the AWS CLI command to create a role named `rds-s3-integration-role`\.   
**Example**  

   For Linux, OS X, or Unix:

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

   For Windows:

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

1. Attach the IAM policy that you created to the IAM role that you created\.

   The following AWS CLI command attaches the policy created earlier to the role named `rds-s3-integration-role`\. Replace `your-policy-arn` with the policy ARN that you noted in a previous step\.   
**Example**  

   For Linux, OS X, or Unix:

   ```
   aws iam attach-role-policy \
      --policy-arn your-policy-arn \
      --role-name rds-s3-integration-role
   ```

   For Windows:

   ```
   aws iam attach-role-policy ^
      --policy-arn your-policy-arn ^
      --role-name rds-s3-integration-role
   ```

1. Add the IAM role to the PostgreSQL DB instance by using the AWS Management Console or AWS CLI, as described following\.

#### Console<a name="collapsible-section-1"></a>

**To add an IAM role for a PostgreSQL DB instance using the console**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console.aws.amazon.com/rds/](https://console.aws.amazon.com/rds/)\. 

1. Choose the PostgreSQL DB instance name to display its details\.

1. On the **Connectivity & security** tab, in the **Manage IAM roles **section, choose the role to add under **Add IAM roles to this instance**\. 

1. Under Feature, choose **s3Import**\.

1. Choose **Add role**\.

#### AWS CLI<a name="collapsible-section-2"></a>

**To add an IAM role for a PostgreSQL DB instance using the CLI**
+ Use the following command to add the role to the PostgreSQL DB instance named `mydbinstance`\. Replace *`your-role-arn`* with the role ARN that you noted in a previous step\. Use `s3Import` for the value of the `--feature-name` option\.   
**Example**  

  For Linux, OS X, or Unix:

  ```
  aws rds add-role-to-db-instance \
     --db-instance-identifier mydbinstance \
     --feature-name s3Import \
     --role-arn your-role-arn   \
     --region us-west-2
  ```

  For Windows:

  ```
  aws rds add-role-to-db-instance ^
     --db-instance-identifier mydbinstance ^
     --feature-name s3Import ^
     --role-arn your-role-arn ^
     --region us-west-2
  ```

### Using Security Credentials to Access an Amazon S3 File<a name="USER_PostgreSQL.S3Import.Credentials"></a>

Instead of using an IAM role to access an Amazon S3 file, you can provide the access key and secret key in the `credentials` parameter of the [aws\_s3\.table\_import\_from\_s3](#USER_PostgreSQL.S3Import.table_import_from_s3) function call\. 

The `credentials` parameter is a structure of type `aws_commons._aws_credentials_1`, which contains AWS credentials\. Use the [aws\_commons\.create\_aws\_credentials](#USER_PostgreSQL.S3Import.create_aws_credentials) function to set the access key and secret key in an `aws_commons._aws_credentials_1` structure, as shown following\. 

```
psql=> SELECT aws_commons.create_aws_credentials(
   '<sample_access_key>', '<sample_secret_key>', '')
AS creds \gset
```

After creating the `aws_commons._aws_credentials_1 `structure, use the [aws\_s3\.table\_import\_from\_s3](#USER_PostgreSQL.S3Import.table_import_from_s3) function to import the data, as shown following\.

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
   aws_commons.create_aws_credentials('<sample_access_key>', '<sample_secret_key>', '')
);
```

### Handling Amazon S3 File Formats<a name="USER_PostgreSQL.S3Import.FileFormats"></a>

The following examples show how to specify different kinds of files when importing\.

**Topics**
+ [Importing a File That Uses a Custom Delimiter](#USER_PostgreSQL.S3Import.FileFormats.CustomDelimiter)
+ [Importing a Compressed \(gzip\) File](#USER_PostgreSQL.S3Import.FileFormats.gzip)
+ [Importing an Encoded File](#USER_PostgreSQL.S3Import.FileFormats.Encoded)

**Note**  
The following examples use the IAM role method for providing access to the Amazon S3 file\. Thus, there are no credential parameters in the `aws_s3.table_import_from_s3` function calls\.

#### Importing a File That Uses a Custom Delimiter<a name="USER_PostgreSQL.S3Import.FileFormats.CustomDelimiter"></a>

The following example shows how to import a file that uses a customer delimiter\. It also shows how to control where to put the data in the database table using the `column_list` parameter of the [aws\_s3\.table\_import\_from\_s3](#USER_PostgreSQL.S3Import.table_import_from_s3) function\. 

For this example, assume the following information is organized into pipe\-delimited columns in the Amazon S3 file\.

```
1|foo1|bar1|elephant1
2|foo2|bar2|elephant2
3|foo3|bar3|elephant3
4|foo4|bar4|elephant4
...
```

**To import a file that uses a customer delimiter**

1. Create a table in the database for the imported data\.

   ```
   psql=> CREATE TABLE test (a text, b text, c text, d text, e text);
   CREATE TABLE
   ```

1. Use the following form of the [aws\_s3\.table\_import\_from\_s3](#USER_PostgreSQL.S3Import.table_import_from_s3) function to import data from the Amazon S3 file\. 

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

#### Importing a Compressed \(gzip\) File<a name="USER_PostgreSQL.S3Import.FileFormats.gzip"></a>

The following example shows how to import a file from Amazon S3 that is compressed with gzip\. 

Ensure that the file contains the following Amazon S3 metadata:
+ Key: `Content-Encoding`
+ Value: `gzip`

For more about adding these values to Amazon S3 metadata, see [How Do I Add Metadata to an S3 Object?](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/add-object-metadata.html) in the *Amazon Simple Storage Service Console User Guide*\.

Import the gzip file into your RDS PostgreSQL DB instance as shown following\.

```
psql=> CREATE TABLE test_gzip(id int, a text, b text, c text, d text);
CREATE TABLE
psql=> SELECT aws_s3.table_import_from_s3(
 'test_gzip', '', '(format csv)',
 'myS3Bucket', 'test-data.gz', 'us-east-2'
);
```

#### Importing an Encoded File<a name="USER_PostgreSQL.S3Import.FileFormats.Encoded"></a>

The following example shows how to import a file from Amazon S3 that has Windows\-1252 encoding\.

```
psql=> SELECT aws_s3.table_import_from_s3(
 'test_table', '', 'encoding ''WIN1252''',
 aws_commons.create_s3_uri('sampleBucket', 'SampleFile', 'us-east-2')
);
```

### Function Reference<a name="USER_PostgreSQL.S3Import.Reference"></a>

**Topics**
+ [aws\_s3\.table\_import\_from\_s3](#USER_PostgreSQL.S3Import.table_import_from_s3)
+ [aws\_commons\.create\_s3\_uri](#USER_PostgreSQL.S3Import.create_s3_uri)
+ [aws\_commons\.create\_aws\_credentials](#USER_PostgreSQL.S3Import.create_aws_credentials)

#### aws\_s3\.table\_import\_from\_s3<a name="USER_PostgreSQL.S3Import.table_import_from_s3"></a>

Imports Amazon S3 data into an RDS PostgreSQL table\. The `aws_s3` extension provides the `aws_s3.table_import_from_s3` function\. 

The three required parameters are `table_name`, `column_list` and `options`\. These identify the database table and specify how the data is copied into the table\. 

You can also use these parameters: 
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

The `aws_s3.table_import_from_s3` parameters are described in the following table\.


****  

| Parameter | Description | 
| --- | --- | 
| table\_name | A required text string containing the name of the PostgreSQL database table to import the data into\. | 
| column\_list |  A required text string containing an optional list of the PostgreSQL database table columns in which to copy the data\. If the string is empty, all columns of the table are used\. For an example, see [Importing a File That Uses a Custom Delimiter](#USER_PostgreSQL.S3Import.FileFormats.CustomDelimiter)\.  | 
| options |  A required text string containing arguments for the PostgreSQL `COPY` command\. These arguments specify how the data is to be copied into the PostgreSQL table\. For more details, see the [PostgreSQL COPY documentation](https://www.postgresql.org/docs/current/sql-copy.html)\.  | 
| s3\_info |  An `aws_commons._s3_uri_1` composite type containing the following information about the S3 object: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/PostgreSQL.Procedural.Importing.html) To create an `aws_commons._s3_uri_1` composite structure, see [aws\_commons\.create\_s3\_uri](#USER_PostgreSQL.S3Import.create_s3_uri)\.  | 
| credentials |  An `aws_commons._aws_credentials_1` composite type containing the following credentials to use for the import operation: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/PostgreSQL.Procedural.Importing.html) To create an `aws_commons._aws_credentials_1` composite structure, see [aws\_commons\.create\_aws\_credentials](#USER_PostgreSQL.S3Import.create_aws_credentials)\.  | 

**Alternate Parameters**  
To help with testing, you can use an expanded set of parameters instead of the `s3_info` and `credentials` parameters\. Following are additional syntax variations for the `aws_s3.table_import_from_s3` function\. 
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

Find descriptions for these alternate parameters in the following table\.


****  

| Parameter | Description | 
| --- | --- | 
| bucket |  A text string containing the name of the Amazon S3 bucket that contains the file\.   | 
| file\_path |  A text string containing the Amazon S3 path of the file\.   | 
| region | A text string containing the AWS Region that the file is in\.  | 
| access\_key | A text string containing the access key to use for the import operation\. The default is NULL\.  | 
| secret\_key | A text string containing the secret key to use for the import operation\. The default is NULL\.  | 
| session\_token | \(Optional\) A text string containing the session key to use for the import operation\. The default is NULL\.  | 

#### aws\_commons\.create\_s3\_uri<a name="USER_PostgreSQL.S3Import.create_s3_uri"></a>

Creates an `aws_commons._s3_uri_1` structure to hold Amazon S3 file information\. You use the results of the `aws_commons.create_s3_uri` function in the `s3_info` parameter of the [aws\_s3\.table\_import\_from\_s3](#USER_PostgreSQL.S3Import.table_import_from_s3) function\. The function syntax is as follows\.

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
| region |  A required text string containing the AWS Region the file is in\.  | 

#### aws\_commons\.create\_aws\_credentials<a name="USER_PostgreSQL.S3Import.create_aws_credentials"></a>

Sets an access key and secret key in an `aws_commons._aws_credentials_1` structure\. Use the results of the `aws_commons.create_aws_credentials` function in the `credentials` parameter of the [aws\_s3\.table\_import\_from\_s3](#USER_PostgreSQL.S3Import.table_import_from_s3) function\. The function syntax is as follows\.

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