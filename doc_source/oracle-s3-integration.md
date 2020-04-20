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

To work with Amazon RDS for Oracle integration with Amazon S3, the Amazon RDS DB instance must have access to an Amazon S3 bucket\. For this, you create an AWS Identity and Access Management \(IAM\) policy and an IAM role\. The Amazon VPC used by your DB instance doesn't need to provide access to the Amazon S3 endpoints\.

**Note**  
To add a role to a DB instance, the status of the DB instance must be `available`\.

### Console<a name="oracle-s3-integration.preparing.console"></a>

**To create an IAM policy to allow Amazon RDS access to an Amazon S3 bucket**

1. Open the [IAM Management Console](https://console.aws.amazon.com/iam/home?#home)\.

1. In the navigation pane, choose **Policies**\.

1. Choose **Create policy**\.

1. On the **Visual editor** tab, choose **Choose a service**, and then choose **S3**\.

1. For **Actions**, choose **Expand all**, and then choose the bucket permissions and object permissions needed for the IAM policy\.

   Include the appropriate actions in the policy based on the type of access required:
   + `GetObject` – Required to transfer files from an Amazon S3 bucket to Amazon RDS\.
   + `ListBucket` – Required to transfer files from an Amazon S3 bucket to Amazon RDS\.
   + `PutObject` – Required to transfer files from Amazon RDS to an Amazon S3 bucket\.

   *Object permissions* are permissions for object operations in Amazon S3, and need to be granted for objects in a bucket, not the bucket itself\. For more information about permissions for object operations in Amazon S3, see [Permissions for Object Operations](https://docs.aws.amazon.com/AmazonS3/latest/dev/using-with-s3-actions.html#using-with-s3-actions-related-to-objects)\.

1. Choose **Resources**, and choose **Add ARN** for **bucket**\.

1. In the **Add ARN\(s\)** dialog box, provide the details about your resource, and choose **Add**\.

   Specify the Amazon S3 bucket to allow access to\. For instance, if you want to allow Amazon RDS to access the Amazon S3 bucket named `example-bucket`, then set the Amazon Resource Name \(ARN\) value to `arn:aws:s3:::example-bucket`\.

1. If the **object** resource is listed, choose **Add ARN** for **object**\.

1. In the **Add ARN\(s\)** dialog box, provide the details about your resource\.

   For the Amazon S3 bucket, specify the Amazon S3 bucket to allow access to\. For the object, you can choose **Any** to grant permissions to any object in the bucket\.
**Note**  
You can set **Amazon Resource Name \(ARN\)** to a more specific ARN value to allow Amazon RDS to access only specific files or folders in an Amazon S3 bucket\. For more information about how to define an access policy for Amazon S3, see [Managing Access Permissions to Your Amazon S3 Resources](https://docs.aws.amazon.com/AmazonS3/latest/dev/s3-access-control.html)\.

1. \(Optional\) Choose **Add additional permissions** to add another Amazon S3 bucket to the policy, and repeat the previous steps for the bucket\.
**Note**  
You can repeat this to add corresponding bucket permission statements to your policy for each Amazon S3 bucket that you want Amazon RDS to access\. Optionally, you can also grant access to all buckets and objects in Amazon S3\.

1. Choose **Review policy**\.

1. For **Name**, enter a name for your IAM policy, for example `rds-s3-integration-policy`\. You use this name when you create an IAM role to associate with your DB instance\. You can also add an optional **Description** value\.

1. Choose **Create policy**\.

**To create an IAM role to allow Amazon RDS access to an Amazon S3 bucket**

1. In the navigation pane, choose **Roles**\.

1. Choose **Create role**\.

1. For **AWS service**, choose **RDS**\.

1. For **Select your use case**, choose **RDS – Add Role to Database**\.

1. Choose **Next: Permissions**\.

1. For **Search** under **Attach permissions policies**, enter the name of the IAM policy you created, and choose the policy when it appears in the list\.

1. Choose **Next: Tags** and then **Next: Review**\.

1. Set **Role name** to a name for your IAM role, for example `rds-s3-integration-role`\. You can also add an optional **Role description** value\.

1. Choose **Create Role**\.

**To associate your IAM role with your DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. Choose the Oracle DB instance name to display its details\.

1. On the **Connectivity & security** tab, in the **Manage IAM roles** section, choose the role to add under **Add IAM roles to this instance**\.

1. For **Feature**, choose **S3\_INTEGRATION**\.  
![\[Add S3_INTEGRATION role\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/ora-s3-integration-role.png)

1. Choose **Add role**\.

### AWS CLI<a name="oracle-s3-integration.preparing.cli"></a>

**To grant Amazon RDS access to an Amazon S3 bucket**

1. Create an AWS Identity and Access Management \(IAM\) policy that grants Amazon RDS access to an Amazon S3 bucket\.

   Include the appropriate actions in the policy based on the type of access required:
   + `GetObject` – Required to transfer files from an Amazon S3 bucket to Amazon RDS\.
   + `ListBucket` – Required to transfer files from an Amazon S3 bucket to Amazon RDS\.
   + `PutObject` – Required to transfer files from Amazon RDS to an Amazon S3 bucket\.

   The following AWS CLI command creates an IAM policy named `rds-s3-integration-policy` with these options\. It grants access to a bucket named `your-s3-bucket-arn`\.  
**Example**  

   For Linux, macOS, or Unix:

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

1. Create an IAM role that Amazon RDS can assume on your behalf to access your Amazon S3 buckets\.

   The following AWS CLI command creates the `rds-s3-integration-role` for this purpose\.  
**Example**  

   For Linux, macOS, or Unix:

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

   For Linux, macOS, or Unix:

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

   For Linux, macOS, or Unix:

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

## Adding the Amazon S3 Integration Option<a name="oracle-s3-integration.preparing.option-group"></a>

To use Amazon RDS for Oracle Integration with Amazon S3, your Amazon RDS Oracle DB instance must be associated with an option group that includes the `S3_INTEGRATION` option\.

### Console<a name="oracle-s3-integration.preparing.option-group.console"></a>

**To configure an option group for Amazon S3 integration**

1. Create a new option group or identify an existing option group to which you can add the `S3_INTEGRATION` option\.

   For information about creating an option group, see [Creating an Option Group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.Create)\.

1. Add the `S3_INTEGRATION` option to the option group\.

   For information about adding an option to an option group, see [Adding an Option to an Option Group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.AddOption)\.

1. Create a new Oracle DB instance and associate the option group with it, or modify an Oracle DB instance to associate the option group with it\.

   For information about creating a DB instance, see [Creating an Amazon RDS DB Instance](USER_CreateDBInstance.md)\.

   For information about modifying an Oracle DB instance, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md)\.

### AWS CLI<a name="oracle-s3-integration.preparing.option-group.cli"></a>

**To configure an option group for Amazon S3 integration**

1. Create a new option group or identify an existing option group to which you can add the `S3_INTEGRATION` option\.

   For information about creating an option group, see [Creating an Option Group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.Create)\.

1. Add the `S3_INTEGRATION` option to the option group\.

   For example, the following AWS CLI command adds the `S3_INTEGRATION` option to an option group named **myoptiongroup**\.  
**Example**  

   For Linux, macOS, or Unix:

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

   For information about creating a DB instance, see [Creating an Amazon RDS DB Instance](USER_CreateDBInstance.md)\.

   For information about modifying an Oracle DB instance, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md)\.

## Transferring Files Between Amazon RDS for Oracle and an Amazon S3 Bucket<a name="oracle-s3-integration.using"></a>

You can use Amazon RDS procedures to upload files from an Oracle DB instance to an Amazon S3 bucket\. You can also use Amazon RDS procedures to download files from an Amazon S3 bucket to an Oracle DB instance\. 

**Note**  
These procedures upload or download the files in a single directory\. You can't include subdirectories in these operations\.

**Topics**
+ [Uploading Files from an Oracle DB Instance to an Amazon S3 Bucket](#oracle-s3-integration.using.upload)
+ [Downloading Files from an Amazon S3 Bucket to an Oracle DB Instance](#oracle-s3-integration.using.download)
+ [Monitoring the Status of a File Transfer](#oracle-s3-integration.using.task-status)

### Uploading Files from an Oracle DB Instance to an Amazon S3 Bucket<a name="oracle-s3-integration.using.upload"></a>

To upload files from an Oracle DB instance to an Amazon S3 bucket, use the Amazon RDS procedure `rdsadmin.rdsadmin_s3_tasks.upload_to_s3`\. For example, you can upload Oracle Recovery Manager \(RMAN\) backup files\. For more information about performing RMAN backups, see [Common DBA Recovery Manager \(RMAN\) Tasks for Oracle DB Instances](Appendix.Oracle.CommonDBATasks.RMAN.md)\.

The `rdsadmin.rdsadmin_s3_tasks.upload_to_s3` procedure has the following parameters\.


****  

| Parameter Name | Data Type | Default | Required | Description | 
| --- | --- | --- | --- | --- | 
|  `p_bucket_name`  |  VARCHAR2  |  –  |  required  |  The name of the Amazon S3 bucket to upload files to\.   | 
|  `p_directory_name`  |  VARCHAR2  |  –  |  required  |  The name of the Oracle directory object to upload files from\. The directory can be any user\-created directory object or the Data Pump directory, such as `DATA_PUMP_DIR`\.   You can only upload files from the specified directory\. You can't upload files in subdirectories in the specified directory\.   | 
|  `p_s3_prefix`  |  VARCHAR2  |  –  |  required  |  An Amazon S3 file name prefix that files are uploaded to\. An empty prefix uploads all files to the top level in the specified Amazon S3 bucket and doesn't add a prefix to the file names\.  For example, if the prefix is `folder_1/oradb`, files are uploaded to `folder_1`\. In this case, the `oradb` prefix is added to each file\.   | 
|  `p_prefix`  |  VARCHAR2  |  –  |  required  |  A file name prefix that file names must match to be uploaded\. An empty prefix uploads all files in the specified directory\.   | 

The return value for the `rdsadmin.rdsadmin_s3_tasks.upload_to_s3` procedure is a task ID\.

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

You can view the result by displaying the task's output file\.

```
SELECT text FROM table(rdsadmin.rds_file_util.read_text_file('BDUMP','dbtask-task-id.log'));                
```

Replace *`task-id`* with the task ID returned by the procedure\.

**Note**  
Tasks are executed asynchronously\.

### Downloading Files from an Amazon S3 Bucket to an Oracle DB Instance<a name="oracle-s3-integration.using.download"></a>

To download files from an Amazon S3 bucket to an Oracle DB instance, use the Amazon RDS procedure `rdsadmin.rdsadmin_s3_tasks.download_from_s3`\. The `rdsadmin.rdsadmin_s3_tasks.download_from_s3` procedure has the following parameters\.


****  

| Parameter Name | Data Type | Default | Required | Description | 
| --- | --- | --- | --- | --- | 
|  `p_bucket_name`  |  VARCHAR2  |  –  |  required  |  The name of the Amazon S3 bucket to download files from\.   | 
|  `p_directory_name`  |  VARCHAR2  |  –  |  required  |  The name of the Oracle directory object to download files to\. The directory can be any user\-created directory object or the Data Pump directory, such as `DATA_PUMP_DIR`\.   | 
|  `p_s3_prefix`  |  VARCHAR2  |  ''  |  optional  |  A file name prefix that file names must match to be downloaded\. An empty prefix downloads all of the top level files in the specified Amazon S3 bucket, but not the files in folders in the bucket\.  The procedure downloads Amazon S3 objects only from the first level folder that matches the prefix\. Nested directory structures matching the specified prefix are not downloaded\. For example, suppose that an Amazon S3 bucket has the folder structure `folder_1/folder_2/folder_3`\. Suppose also that you specify the `'folder_1/folder_2/'` prefix\. In this case, only the files in `folder_2` are downloaded, not the files in `folder_1` or `folder_3`\. If, instead, you specify the `'folder_1/folder_2'` prefix, all files in `folder_1` that match the `'folder_2'` prefix are downloaded, and no files in `folder_2` are downloaded\.  | 

The return value for the `rdsadmin.rdsadmin_s3_tasks.download_from_s3` procedure is a task ID\.

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

The following example downloads all of the files in the folder `myfolder/` in the Amazon S3 bucket named `mys3bucket` to the `DATA_PUMP_DIR` directory\. Use the prefix parameter setting to specify the Amazon S3 folder\.

```
SELECT rdsadmin.rdsadmin_s3_tasks.download_from_s3(
      p_bucket_name    =>  'mys3bucket', 
      p_s3_prefix      =>  'myfolder/', 
      p_directory_name =>  'DATA_PUMP_DIR') 
   AS TASK_ID FROM DUAL;
```

In each example, the `SELECT` statement returns the ID of the task in a `VARCHAR2` data type\.

You can view the result by displaying the task's output file\.

```
SELECT text FROM table(rdsadmin.rds_file_util.read_text_file('BDUMP','dbtask-task-id.log'));                
```

Replace *`task-id`* with the task ID returned by the procedure\.

**Note**  
Tasks are executed asynchronously\.  
You can use the `UTL_FILE.FREMOVE` Oracle procedure to remove files from a directory\. For more information, see [FREMOVE Procedure](https://docs.oracle.com/database/121/ARPLS/u_file.htm#ARPLS70924) in the Oracle documentation\.

### Monitoring the Status of a File Transfer<a name="oracle-s3-integration.using.task-status"></a>

File transfer tasks publish Amazon RDS events when they start and when they complete\. For information about viewing events, see [Viewing Amazon RDS Events](USER_ListEvents.md)\.

You can view the status of an ongoing task in a bdump file\. The bdump files are located in the `/rdsdbdata/log/trace` directory\. Each bdump file name is in the following format\.

```
dbtask-task-id.log            
```

Replace `task-id` with the ID of the task that you want to monitor\.

**Note**  
Tasks are executed asynchronously\.

You can use the `rdsadmin.rds_file_util.read_text_file` stored procedure to view the contents of bdump files\. For example, the following query returns the contents of the `dbtask-1546988886389-2444.log` bdump file\.

```
SELECT text FROM table(rdsadmin.rds_file_util.read_text_file('BDUMP','dbtask-1546988886389-2444.log'));            
```

## Removing the Amazon S3 Integration Option<a name="oracle-s3-integration.removing"></a>

You can remove Amazon S3 integration option from a DB instance\. 

To remove the Amazon S3 integration option from a DB instance, do one of the following: 
+ To remove the Amazon S3 integration option from multiple DB instances, remove the `S3_INTEGRATION` option from the option group to which the DB instances belong\. This change affects all DB instances that use the option group\. For more information, see [Removing an Option from an Option Group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.RemoveOption)\. 
+ To remove the Amazon S3 integration option from a single DB instance, modify the DB instance and specify a different option group that doesn't include the `S3_INTEGRATION` option\. You can specify the default \(empty\) option group or a different custom option group\. For more information, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md)\. 