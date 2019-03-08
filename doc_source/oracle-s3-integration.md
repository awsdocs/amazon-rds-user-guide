# Amazon S3 Integration<a name="oracle-s3-integration"></a>

You can transfer files between an Amazon RDS for Oracle DB instance and an Amazon S3 bucket\. You can use Amazon S3 integration with Oracle features such as Data Pump\. For example, you can download Data Pump files from Amazon S3 to the DB instance host\.

**Note**  
The DB instance and the Amazon S3 bucket must be in the same AWS Region\.

**Topics**
+ [Prerequisites for Amazon RDS Oracle Integration with Amazon S3](#oracle-s3-integration.preparing)
+ [Adding the Amazon S3 Integration Option](#oracle-s3-integration.preparing.option-group)
+ [Transferring Files Between Amazon RDS for Oracle and an Amazon S3 Bucket](#oracle-s3-integration.using)
+ [Removing the Amazon S3 Integration Option](#oracle-s3-integration.removing)

## Prerequisites for Amazon RDS Oracle Integration with Amazon S3<a name="oracle-s3-integration.preparing"></a>

To work with Amazon RDS for Oracle integration with Amazon S3, the Amazon RDS must have access to an Amazon S3 bucket\. 

**To grant Amazon RDS access to an Amazon S3 bucket**

1. Create an AWS Identity and Access Management \(IAM\) policy that grants Amazon RDS access to an Amazon S3 bucket\.

   Include the appropriate actions in the policy based on the type of access required:
   + `GetObject` – Required to transfer files from an Amazon S3 bucket to Amazon RDS\.
   + `ListBucket` – Required to transfer files from an Amazon S3 bucket to Amazon RDS\.
   + `PutObject` – Required to transfer files from Amazon RDS to an Amazon S3 bucket\.

   The following AWS CLI command creates an IAM policy named `rds-s3-integration-policy` with these options\. It grants access to a bucket named `your-s3-bucket-arn`\.  
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

1. After the policy is created, note the Amazon Resource Name \(ARN\) of the policy\. You need the ARN for a subsequent step\.

1. Create a role that Amazon RDS can assume on your behalf to access your Amazon S3 buckets\.

   The following AWS CLI command creates the `rds-s3-integration-role` for this purpose\.  
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

   For more information, see [Creating a Role to Delegate Permissions to an IAM User](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-user.html) in the *IAM User Guide*\.

1. After the role is created, note the ARN of the role\. You need the ARN for a subsequent step\.

1. Attach the policy you created to the role you created\.

   The following AWS CLI command attaches the policy to the role named `rds-s3-integration-role`\.  
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

   Replace `your-policy-arn` with the policy ARN that you noted in a previous step\.

1. Add the role to the Oracle DB instance\.

   The following AWS CLI command adds the role to an Oracle DB instance named `mydbinstance`\.  
**Example**  

   For Linux, OS X, or Unix:

   ```
   aws rds add-role-to-db-instance \
      --db-instance-identifier mydbinstance \
      --feature-name S3_INTEGRATION \
      --role-arn your-role-arn
   ```

   For Windows:

   ```
   aws rds add-role-to-db-instance ^
      --db-instance-identifier mydbinstance ^
      --feature-name S3_INTEGRATION ^
      --role-arn your-role-arn
   ```

   Replace `your-role-arn` with the role ARN that you noted in a previous step\. `S3_INTEGRATION` must be specified for the `--feature-name` option\.

   You can also add the role to the Oracle DB instance using the AWS Management Console:

   1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

   1. Choose the Oracle DB instance name to display its details\.

   1. On the **Connectivity & security** tab, in the **Manage IAM roles** section, choose the role to add under **Add IAM roles to this instance**\.

   1. Under **Feature**, choose **S3\_INTEGRATION**\.  
![\[Add S3_INTEGRATION role\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/ora-s3-integration-role.png)

   1. Choose **Add role**\.

## Adding the Amazon S3 Integration Option<a name="oracle-s3-integration.preparing.option-group"></a>

To use Amazon RDS for Oracle Integration with Amazon S3, your Amazon RDS Oracle DB instance must be associated with an option group that includes the `S3_INTEGRATION` option\.

### Console<a name="oracle-s3-integration.preparing.option-group.console"></a>

**To configure an option group for Amazon S3 integration**

1. Create a new option group or identify an existing option group to which you can add the `S3_INTEGRATION` option\.

   For information about creating an option group, see [Creating an Option Group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.Create)\.

1. Add the `S3_INTEGRATION` option to the option group\.

   For information about adding an option to an option group, see [Adding an Option to an Option Group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.AddOption)\.

1. Create a new Oracle DB instance and associate the option group with it, or modify an Oracle DB instance to associate the option group with it\.

   For information about creating an Oracle DB instance, see [Creating a DB Instance Running the Oracle Database Engine](USER_CreateOracleInstance.md)\.

   For information about modifying an Oracle DB instance, see [Modifying a DB Instance Running the Oracle Database Engine](USER_ModifyInstance.Oracle.md)\.

### AWS CLI<a name="oracle-s3-integration.preparing.option-group.cli"></a>

**To configure an option group for Amazon S3 integration**

1. Create a new option group or identify an existing option group to which you can add the `S3_INTEGRATION` option\.

   For information about creating an option group, see [Creating an Option Group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.Create)\.

1. Add the `S3_INTEGRATION` option to the option group\.

   For example, the following AWS CLI command adds the `S3_INTEGRATION` option to an option group named **myoptiongroup**\.  
**Example**  

   For Linux, OS X, or Unix:

   ```
   aws rds add-option-to-option-group \
      --option-group-name myoptiongroup \
      --options OptionName=S3_INTEGRATION,OptionVersion=1.0
   ```

   For Windows:

   ```
   aws rds add-option-to-option-group ^
      --option-group-name myoptiongroup ^
      --options OptionName=S3_INTEGRATION,OptionVersion=1.0
   ```

1. Create a new Oracle DB instance and associate the option group with it, or modify an Oracle DB instance to associate the option group with it\.

   For information about creating an Oracle DB instance, see [Creating a DB Instance Running the Oracle Database Engine](USER_CreateOracleInstance.md)\.

   For information about modifying an Oracle DB instance, see [Modifying a DB Instance Running the Oracle Database Engine](USER_ModifyInstance.Oracle.md)\.

## Transferring Files Between Amazon RDS for Oracle and an Amazon S3 Bucket<a name="oracle-s3-integration.using"></a>

You can use Amazon RDS procedures to upload files from an Oracle DB instance to an Amazon S3 bucket\. You can also use Amazon RDS procedures to download files from an Amazon S3 bucket to an Oracle DB instance\. 

**Topics**
+ [Uploading Files from an Oracle DB Instance to an Amazon S3 Bucket](#oracle-s3-integration.using.upload)
+ [Downloading Files from an Amazon S3 Bucket to an Oracle DB Instance](#oracle-s3-integration.using.download)
+ [Monitoring the Status of a File Transfer](#oracle-s3-integration.using.task-status)

### Uploading Files from an Oracle DB Instance to an Amazon S3 Bucket<a name="oracle-s3-integration.using.upload"></a>

To upload files from an Oracle DB instance to an Amazon S3 bucket, use the Amazon RDS procedure `rdsadmin.rdsadmin_s3_tasks.upload_to_s3`\. The `rdsadmin.rdsadmin_s3_tasks.upload_to_s3` procedure has the following parameters\.


****  

| Parameter Name | Data Type | Default | Required | Description | 
| --- | --- | --- | --- | --- | 
|  `p_bucket_name`  |  VARCHAR2  |  –  |  required  |  The name of the Amazon S3 bucket to upload files to\.   | 
|  `p_prefix`  |  VARCHAR2  |  –  |  required  |  A file name prefix that file names must match to be uploaded\. An empty prefix uploads all files in the specified directory\.   | 
|  `p_s3_prefix`  |  VARCHAR2  |  –  |  required  |  An Amazon S3 file name prefix that files are uploaded to\. An empty prefix uploads all files to the top level in the specified Amazon S3 bucket and doesn't add a prefix to the file names\.  For example, if the prefix is `folder_1/oradb`, files are uploaded to `folder_1`\. In this case, the `oradb` prefix is added to each file\.   | 
|  `p_directory_name`  |  VARCHAR2  |  –  |  required  |  The name of the Oracle directory object to upload files from\. The directory can be any user\-created directory object or the Data Pump directory, such as `DATA_PUMP_DIR`\.   | 

The following example uploads all of the files in the `DATA_PUMP_DIR` directory to the Amazon S3 bucket named `mys3bucket`\.

```
SELECT rdsadmin.rdsadmin_s3_tasks.upload_to_s3(
      p_bucket_name    =>  'mys3bucket', 
      p_prefix         =>  '', 
      p_s3_prefix      =>  '', 
      p_directory_name =>  'DATA_PUMP_DIR') 
   AS TASK_ID FROM DUAL;
```

The following example uploads all of the files with the prefix `db` in the `DATA_PUMP_DIR` directory to the Amazon S3 bucket named `mys3bucket`\.

```
SELECT rdsadmin.rdsadmin_s3_tasks.upload_to_s3(
      p_bucket_name    =>  'mys3bucket', 
      p_prefix         =>  'db', 
      p_s3_prefix      =>  '', 
      p_directory_name =>  'DATA_PUMP_DIR') 
   AS TASK_ID FROM DUAL;
```

The following example uploads all of the files in the `DATA_PUMP_DIR` directory to the Amazon S3 bucket named `mys3bucket`\. The files are uploaded to a `dbfiles` folder\.

```
SELECT rdsadmin.rdsadmin_s3_tasks.upload_to_s3(
      p_bucket_name    =>  'mys3bucket', 
      p_prefix         =>  '', 
      p_s3_prefix      =>  'dbfiles/', 
      p_directory_name =>  'DATA_PUMP_DIR') 
   AS TASK_ID FROM DUAL;
```

The following example uploads all of the files in the `DATA_PUMP_DIR` directory to the Amazon S3 bucket named `mys3bucket`\. The files are uploaded to a `dbfiles` folder and `ora` is added to the beginning of each file name\.

```
SELECT rdsadmin.rdsadmin_s3_tasks.upload_to_s3(
      p_bucket_name    =>  'mys3bucket', 
      p_prefix         =>  '', 
      p_s3_prefix      =>  'dbfiles/ora', 
      p_directory_name =>  'DATA_PUMP_DIR') 
   AS TASK_ID FROM DUAL;
```

In each example, the `SELECT` statement returns the ID of the task in a `VARCHAR2` data type\.

### Downloading Files from an Amazon S3 Bucket to an Oracle DB Instance<a name="oracle-s3-integration.using.download"></a>

To download files from an Amazon S3 bucket to an Oracle DB instance, use the Amazon RDS procedure `rdsadmin.rdsadmin_s3_tasks.download_from_s3`\. The `rdsadmin.rdsadmin_s3_tasks.download_from_s3` procedure has the following parameters\.


****  

| Parameter Name | Data Type | Default | Required | Description | 
| --- | --- | --- | --- | --- | 
|  `p_bucket_name`  |  VARCHAR2  |  –  |  required  |  The name of the Amazon S3 bucket to download files from\.   | 
|  `p_s3_prefix`  |  VARCHAR2  |  ''  |  optional  |  A file name prefix that file names must match to be downloaded\. An empty prefix downloads all files in the specified Amazon S3 bucket\.  The procedure downloads Amazon S3 objects only from the first level folder that matches the prefix\. Nested directory structures matching the specified prefix are not downloaded\. For example, suppose that an Amazon S3 bucket has the folder structure `folder_1/folder_2/folder_3`\. Suppose also that you specify the `'folder_1/folder_2'` prefix\. In this case, only the files in `folder_2` are downloaded, not the files in `folder_1` or `folder_3`\.   | 
|  `p_directory_name`  |  VARCHAR2  |  –  |  required  |  The name of the Oracle directory object to download files to\. The directory can be any user\-created directory object or the Data Pump directory, such as `DATA_PUMP_DIR`\.   | 

The following example downloads all of the files in the Amazon S3 bucket named `mys3bucket` to the `DATA_PUMP_DIR` directory\.

```
SELECT rdsadmin.rdsadmin_s3_tasks.download_from_s3(
      p_bucket_name    =>  'mys3bucket',       
      p_directory_name =>  'DATA_PUMP_DIR') 
   AS TASK_ID FROM DUAL;
```

The following example downloads all of the files with the prefix `db` in the Amazon S3 bucket named `mys3bucket` to the `DATA_PUMP_DIR` directory\.

```
SELECT rdsadmin.rdsadmin_s3_tasks.download_from_s3(
      p_bucket_name    =>  'mys3bucket', 
      p_s3_prefix      =>  'db', 
      p_directory_name =>  'DATA_PUMP_DIR') 
   AS TASK_ID FROM DUAL;
```

In each example, the `SELECT` statement returns the ID of the task in a `VARCHAR2` data type\.

The following example downloads all of the files in the folder `myfolder/` in the Amazon S3 bucket named `mys3bucket` to the `DATA_PUMP_DIR` directory\. Use the prefix parameter setting to specify the Amazon S3 folder\.

```
SELECT rdsadmin.rdsadmin_s3_tasks.download_from_s3(
      p_bucket_name    =>  'mys3bucket', 
      p_s3_prefix      =>  'myfolder/', 
      p_directory_name =>  'DATA_PUMP_DIR') 
   AS TASK_ID FROM DUAL;
```

In each example, the `SELECT` statement returns the ID of the task in a `VARCHAR2` data type\.

**Note**  
You can use the `UTL_FILE.FREMOVE` Oracle procedure to remove files from a directory\. For more information, see [FREMOVE Procedure](https://docs.oracle.com/database/121/ARPLS/u_file.htm#ARPLS70924) in the Oracle documentation\.

### Monitoring the Status of a File Transfer<a name="oracle-s3-integration.using.task-status"></a>

File transfer tasks publish Amazon RDS events when they start and when they complete\. For information about viewing events, see [Viewing Amazon RDS Events](USER_ListEvents.md)\.

You can view the status of an ongoing task in a bdump file\. The bdump files are located in the `/rdsdbdata/log/trace` directory\. Each bdump file name is in the following format\.

```
dbtask-task-id.log            
```

Replace `task-id` with the ID of the task that you want to monitor\.

You can use the `rdsadmin.rds_file_util.read_text_file` stored procedure to view the contents of bdump files\. For example, the following query returns the contents of the `dbtask-1546988886389-2444.log` bdump file\.

```
SELECT text FROM table(rdsadmin.rds_file_util.read_text_file('BDUMP','dbtask-1546988886389-2444.log'));            
```

## Removing the Amazon S3 Integration Option<a name="oracle-s3-integration.removing"></a>

You can remove Amazon S3 integration option from a DB instance\. 

To remove the Amazon S3 integration option from a DB instance, do one of the following: 
+ To remove the Amazon S3 integration option from multiple DB instances, remove the `S3_INTEGRATION` option from the option group to which the DB instances belong\. This change affects all DB instances that use the option group\. For more information, see [Removing an Option from an Option Group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.RemoveOption)\. 
+ To remove the Amazon S3 integration option from a single DB instance, modify the DB instance and specify a different option group that doesn't include the `S3_INTEGRATION` option\. You can specify the default \(empty\) option group or a different custom option group\. For more information, see [Modifying a DB Instance Running the Oracle Database Engine](USER_ModifyInstance.Oracle.md)\. 