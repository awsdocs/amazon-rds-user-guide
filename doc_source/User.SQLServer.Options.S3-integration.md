# Integrating an Amazon RDS for SQL Server DB Instance with Amazon S3<a name="User.SQLServer.Options.S3-integration"></a>

You can transfer files between a DB instance running Amazon RDS for SQL Server and an Amazon S3 bucket\. By doing this, you can use Amazon S3 with SQL Server features such as BULK INSERT\. For example, you can download \.csv, \.xml, \.txt, and other files from Amazon S3 to the DB instance host and import the data from `D:\S3\` into the database\. All files are stored in `D:\S3\` on the DB instance\.

The following limitations apply:
+ Files in the `D:\S3` folder are deleted on the standby replica after a failover on Multi\-AZ instances\. For more information, see [Multi\-AZ Limitations for S3 Integration](#S3-MAZ)\.
+ The DB instance and the S3 bucket must be in the same AWS Region\.
+ If you run more than one S3 integration task at a time, the tasks run sequentially, not in parallel\.
**Note**  
S3 integration tasks share the same queue as native backup and restore tasks\. At maximum, you can have only two tasks in progress at any time in this queue\. Therefore, two running native backup and restore tasks will block any S3 integration tasks\.
+ You must re\-enable the S3 integration feature on restored instances\. S3 integration isn't propagated from the source instance to the restored instance\. Files in D:\\S3 are deleted on a restored instance\.
+ Downloading to the DB instance is limited to 100 files\. In other words, there can't be more than 100 files in `D:\S3\`\.
+ Only files without file extensions or with the following file extensions are supported for download: \.bcp, \.csv, \.dat, \.fmt, \.info, \.lst, \.tbl, \.txt, and \.xml\.
**Note**  
Files with the \.ispac file extension are supported for download when SQL Server Integration Services is enabled\. For more information on enabling SSIS, see [SQL Server Integration Services](Appendix.SQLServer.Options.SSIS.md)\.  
Files with the following file extensions are supported for download when SQL Server Analysis Services is enabled: \.abf, \.asdatabase, \.configsettings, \.deploymentoptions, \.deploymenttargets, and \.xmla\. For more information on enabling SSAS, see [SQL Server Analysis Services](Appendix.SQLServer.Options.SSAS.md)\.
+ The S3 bucket must have the same owner as the related AWS Identity and Access Management \(IAM\) role\. Also, the bucket can't be open to the public\.
+ File size for uploads is limited to 50 GB per file\.

**Topics**
+ [Prerequisites for Integrating RDS SQL Server with S3](#Appendix.SQLServer.Options.S3-integration.preparing)
+ [Enabling RDS SQL Server Integration with S3](#Appendix.SQLServer.Options.S3-integration.enabling)
+ [Transferring Files Between RDS SQL Server and an S3 Bucket](#Appendix.SQLServer.Options.S3-integration.using)
+ [Multi\-AZ Limitations for S3 Integration](#S3-MAZ)
+ [Disabling RDS SQL Server Integration with S3](#Appendix.SQLServer.Options.S3-integration.disabling)

For more information on working with files in Amazon S3, see [Getting Started with Amazon Simple Storage Service](https://docs.aws.amazon.com/AmazonS3/latest/gsg)\.

## Prerequisites for Integrating RDS SQL Server with S3<a name="Appendix.SQLServer.Options.S3-integration.preparing"></a>

Before you begin, find or create the S3 bucket that you want to use\. Also, add permissions so that the RDS DB instance can access the S3 bucket\. To configure this access, you create both an IAM policy and an IAM role\.

### Console<a name="Appendix.SQLServer.Options.S3-integration.preparing.console"></a>

**To create an IAM policy for access to Amazon S3**

1. In the [IAM Management Console](https://console.aws.amazon.com/iam/home?#home), choose **Policies** in the navigation pane\.

1. Create a new policy, and use the **Visual editor** tab for the following steps\.

1. For **Service**, enter **S3** and then choose the **S3** service\.

1. For **Actions**, choose the following to grant the access that your DB instance requires:
   + `ListAllMyBuckets` – required
   + `ListBucket` – required
   + `GetBucketACL` – required
   + `GetBucketLocation` – required
   + `GetObject` – required for downloading files from S3 to `D:\S3\`
   + `PutObject` – required for uploading files from `D:\S3\` to S3
   + `ListMultipartUploadParts` – required for uploading files from `D:\S3\` to S3
   + `AbortMultipartUpload` – required for uploading files from `D:\S3\` to S3

1. For **Resources**, the options that display depend on which actions you choose in the previous step\. You might see options for **bucket**, **object**, or both\. For each of these, add the appropriate Amazon Resource Name \(ARN\)\.

   For **bucket**, add the ARN for the bucket that you want to use\. For example, if your bucket is named `example-bucket`, set the ARN to `arn:aws:s3:::example-bucket`\.

   For **object**, enter the ARN for the bucket and then choose one of the following:
   + To grant access to all files in the specified bucket, choose **Any** for both **Bucket name** and **Object name**\.
   + To grant access to specific files or folders in the bucket, provide ARNs for the specific buckets and objects that you want SQL Server to access\. 

1. Follow the instructions in the console until you finish creating the policy\.

   The preceding is an abbreviated guide to setting up a policy\. For more detailed instructions on creating IAM policies, see [Creating IAM Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_create.html) in the *IAM User Guide\.*

**To create an IAM role that uses the IAM policy from the previous procedure**

1. In the [IAM Management Console](https://console.aws.amazon.com/iam/home?#home), choose **Roles** in the navigation pane\.

1. Create a new IAM role, and choose the following options as they appear in the console:
   + **AWS service**
   + **RDS**
   + **RDS – Add Role to Database**

   Then choose **Next:Permissions** at the bottom\.

1. For **Attach permissions policies**, enter the name of the IAM policy that you previously created\. Then choose the policy from the list\.

1. Follow the instructions in the console until you finish creating the role\.

   The preceding is an abbreviated guide to setting up a role\. If you want more detailed instructions on creating roles, see [IAM Roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles.html) in the *IAM User Guide\.*

### AWS CLI<a name="Appendix.SQLServer.Options.S3-integration.preparing.cli"></a>

**To grant Amazon RDS access to an Amazon S3 bucket**

1. Create an IAM policy that grants Amazon RDS access to an S3 bucket\.

   Include the appropriate actions to grant the access your DB instance requires:
   + `ListAllMyBuckets` – required
   + `ListBucket` – required
   + `GetBucketACL` – required
   + `GetBucketLocation` – required
   + `GetObject` – required for downloading files from S3 to `D:\S3\`
   + `PutObject` – required for uploading files from `D:\S3\` to S3
   + `ListMultipartUploadParts` – required for uploading files from `D:\S3\` to S3
   + `AbortMultipartUpload` – required for uploading files from `D:\S3\` to S3

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
   	                "Effect": "Allow",
   	                "Action": "s3:ListAllMyBuckets",
   	                "Resource": "*"
   	            },
   	            {
   	                "Effect": "Allow",
   	                "Action": [
   	                    "s3:ListBucket",
   	                    "s3:GetBucketACL",
   	                    "s3:GetBucketLocation"
   	                ],
   	                "Resource": "arn:aws:s3:::bucket_name"
   	            },
   	            {
   	                "Effect": "Allow",
   	                "Action": [
   	                    "s3:GetObject",
   	                    "s3:PutObject",
   	                    "s3:ListMultipartUploadParts",
   	                    "s3:AbortMultipartUpload"
   	                ],
   	                "Resource": "arn:aws:s3:::bucket_name/key_prefix/*"
   	            }
   	        ]
   	    }'
   ```

   For Windows:

   Make sure to change the line endings to the ones supported by your interface \(`^` instead of `\`\)\. Also, in Windows, you must escape all double quotes with a `\`\. To avoid the need to escape the quotes in the JSON, you can save it to a file instead and pass that in as a parameter\. 

   First, create the `policy.json` file with the following permission policy:

   ```
   {
       "Version": "2012-10-17",
       "Statement": [
           {
               "Effect": "Allow",
               "Action": "s3:ListAllMyBuckets",
               "Resource": "*"
           },
           {
               "Effect": "Allow",
               "Action": [
                   "s3:ListBucket",
                   "s3:GetBucketACL",
                   "s3:GetBucketLocation"
               ],
               "Resource": "arn:aws:s3:::bucket_name"
           },
           {
               "Effect": "Allow",
               "Action": [
                   "s3:GetObject",
                   "s3:PutObject",
                   "s3:ListMultipartUploadParts",
                   "s3:AbortMultipartUpload"
               ],
               "Resource": "arn:aws:s3:::bucket_name/key_prefix/*"
           }
       ]
   }
   ```

   Then use the following command to create the policy:

   ```
   aws iam create-policy ^
        --policy-name rds-s3-integration-policy ^
        --policy-document file://policy_file_path
   ```

1. After the policy is created, note the Amazon Resource Name \(ARN\) of the policy\. You need the ARN for a later step\.

1. Create an IAM role that Amazon RDS can assume on your behalf to access your S3 buckets\.

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

   Make sure to change the line endings to the ones supported by your interface \(`^` instead of `\`\)\. Also, in Windows, you must escape all double quotes with a `\`\. To avoid the need to escape the quotes in the JSON, you can save it to a file instead and pass that in as a parameter\. 

   First, create the `assume_role_policy.json` file with the following policy:

   ```
   {
       "Version": "2012-10-17",
       "Statement": [
           {
               "Effect": "Allow",
               "Principal": {
                   "Service": [
                       "rds.amazonaws.com"
                   ]
               },
               "Action": "sts:AssumeRole"
           }
       ]
   }
   ```

   Then use the following command to create the IAM role:

   ```
   aws iam create-role ^
        --role-name rds-s3-integration-role ^
        --assume-role-policy-document file://assume_role_policy_file_path
   ```

   For more information, see [Creating a Role to Delegate Permissions to an IAM User](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-user.html) in the *IAM User Guide*\.

1. After the IAM role is created, note the ARN of the role\. You need the ARN for a later step\.

1. Attach the IAM policy that you created to the IAM role that you created\.

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

## Enabling RDS SQL Server Integration with S3<a name="Appendix.SQLServer.Options.S3-integration.enabling"></a>

In the following section, you can find how to enable Amazon S3 integration with Amazon RDS for SQL Server\. To work with S3 integration, your DB instance must be associated with the IAM role that you previously created before you use the `S3_INTEGRATION` feature\-name parameter\.

**Note**  
To add an IAM role to a DB instance, the status of the DB instance must be **available**\.

### Console<a name="Appendix.SQLServer.Options.S3-integration.enabling.console"></a>

**To associate your IAM role with your DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. Choose the RDS SQL Server DB instance name to display its details\.

1. On the **Connectivity & security** tab, in the **Manage IAM roles** section, choose the IAM role to add for **Add IAM roles to this instance**\.

1. For **Feature**, choose **S3\_INTEGRATION**\.  
![\[Add the S3_INTEGRATION role\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/ora-s3-integration-role.png)

1. Choose **Add role**\.

### AWS CLI<a name="Appendix.SQLServer.Options.S3-integration.enabling.cli"></a>

**To add the IAM role to the RDS SQL Server DB instance**
+ The following AWS CLI command adds your IAM role to an RDS SQL Server DB instance named `mydbinstance`\.  
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

## Transferring Files Between RDS SQL Server and an S3 Bucket<a name="Appendix.SQLServer.Options.S3-integration.using"></a>

You can use Amazon RDS stored procedures to download and upload files between S3 and your RDS DB instance\. You can also use Amazon RDS stored procedures to list and delete files on the RDS instance\.

The files that you download from and upload to Amazon S3 are stored in the `D:\S3` folder\. This is the only folder that you can use to access your files\. You can organize your files into subfolders, which are created for you when you include the destination folder during download\.

Some of the stored procedures require that you provide an Amazon Resource Name \(ARN\) to your Amazon S3 bucket and file\. The format for your ARN is `arn:aws:s3:::bucket_name/file_name`\. Amazon S3 doesn't require an account number or AWS Region in ARNs\.

S3 integration tasks run sequentially and share the same queue as native backup and restore tasks\. At maximum, you can have only two tasks in progress at any time in this queue\. It can take up to five minutes for the task to begin processing\.

### Downloading Files from an Amazon S3 Bucket to a SQL Server DB Instance<a name="Appendix.SQLServer.Options.S3-integration.using.download"></a>

To download files from an S3 bucket to an RDS SQL Server DB instance, use the Amazon RDS stored procedure `msdb.dbo.rds_download_from_s3` with the following parameters\.


| Parameter Name | Data Type | Default | Required | Description | 
| --- | --- | --- | --- | --- | 
|  `@s3_arn_of_file`  |  NVARCHAR  |  –  |  Required  |  The S3 ARN of the file to download, for example: `arn:aws:s3:::bucket_name/mydata.csv`  | 
|  `@rds_file_path`  |  NVARCHAR  |  –  |  Optional  |  The file path for the RDS instance\. If not specified, the file path is `D:\S3\<filename in s3>`\. RDS supports absolute paths and relative paths\. If you want to create a subfolder, include it in the file path\.  | 
|  `@overwrite_file`  |  INT  |  0  |  Optional  | Overwrite the existing file:  0 = Don't overwrite 1 = Overwrite | 

You can download files without a file extension and files with the following file extensions: \.bcp, \.csv, \.dat, \.fmt, \.info, \.lst, \.tbl, \.txt, and \.xml\.

**Note**  
Files with the \.ispac file extension are supported for download when SQL Server Integration Services is enabled\. For more information on enabling SSIS, see [SQL Server Integration Services](Appendix.SQLServer.Options.SSIS.md)\.  
Files with the following file extensions are supported for download when SQL Server Analysis Services is enabled: \.abf, \.asdatabase, \.configsettings, \.deploymentoptions, \.deploymenttargets, and \.xmla\. For more information on enabling SSAS, see [SQL Server Analysis Services](Appendix.SQLServer.Options.SSAS.md)\.

The following example shows the stored procedure to download files from S3\. 

```
exec msdb.dbo.rds_download_from_s3
	    @s3_arn_of_file='arn:aws:s3:::bucket_name/bulk_data.csv',
	    @rds_file_path='D:\S3\seed_data\data.csv',
	    @overwrite_file=1;
```

The example `rds_download_from_s3` operation creates a folder named `seed_data` in `D:\S3\`, if the folder doesn't exist yet\. Then the example downloads the source file `bulk_data.csv` from S3 to a new file named `data.csv` on the DB instance\. If the file previously existed, it's overwritten because the `@overwrite_file` parameter is set to `1`\.

### Uploading Files from a SQL Server DB Instance to an Amazon S3 Bucket<a name="Appendix.SQLServer.Options.S3-integration.using.upload"></a>

To upload files from an RDS SQL Server DB instance to an S3 bucket, use the Amazon RDS stored procedure `msdb.dbo.rds_upload_to_s3` with the following parameters\.


| Parameter Name | Data Type | Default | Required | Description | 
| --- | --- | --- | --- | --- | 
|  `@s3_arn_of_file`  |  NVARCHAR  |  –  |  Required  |  The S3 ARN of the file to be created in S3, for example: `arn:aws:s3:::bucket_name/mydata.csv`  | 
|  `@rds_file_path`  |  NVARCHAR  |  –  |  Required  | The file path of the file to upload to S3\. Absolute and relative paths are supported\. | 
|  `@overwrite_file`  |  INT  |  –  |  Optional  |  Overwrite the existing file:  0 = Don't overwrite 1 = Overwrite  | 

The following example uploads the file named `data.csv` from the specified location in `D:\S3\seed_data\` to a file `new_data.csv` in the S3 bucket specified by the ARN\.

```
exec msdb.dbo.rds_upload_to_s3 
		@rds_file_path='D:\S3\seed_data\data.csv',
		@s3_arn_of_file='arn:aws:s3:::bucket_name/new_data.csv',
		@overwrite_file=1;
```

If the file previously existed in S3, it's overwritten because the @overwrite\_file parameter is set to `1`\.

### Listing Files on the RDS DB Instance<a name="Appendix.SQLServer.Options.S3-integration.using.listing-files"></a>

To list the files available on the DB instance, use both a stored procedure and a function\. First, run the following stored procedure to gather file details from the files in `D:\S3\`\. 

```
exec msdb.dbo.rds_gather_file_details;
```

The stored procedure returns the ID of the task\. Like other tasks, this stored procedure runs asynchronously\. As soon as the status of the task is `SUCCESS`, you can use the task ID in the `rds_fn_list_file_details` function to list the existing files and directories in D:\\S3\\, as shown following\.

```
SELECT * FROM msdb.dbo.rds_fn_list_file_details(TASK_ID);
```

The `rds_fn_list_file_details` function returns a table with the following columns\.


| Output Parameter | Description | 
| --- | --- | 
| filepath | Absolute path of the file \(for example, D:\\S3\\mydata\.csv\) | 
| size\_in\_bytes | File size \(in bytes\) | 
| last\_modified\_utc | Last modification date and time in UTC format | 
| is\_directory | Option that indicates whether the item is a directory \(true/false\) | 

### Deleting Files on the RDS Instance<a name="Appendix.SQLServer.Options.S3-integration.using.deleting-files"></a>

To delete the files available on the DB instance, use the Amazon RDS stored procedure `msdb.dbo.rds_delete_from_filesystem` with the following parameters\. 


| Parameter Name | Data Type | Default | Required | Description | 
| --- | --- | --- | --- | --- | 
|  `@rds_file_path`  |  NVARCHAR  |  –  |  Required  | The file path of the file to delete\. Absolute and relative paths are supported\.  | 
|  `@force_delete`  |  INT  | 0 |  Optional  |  To delete a directory, this flag must be included and set to `1`\. `1` = delete a directory This parameter is ignored if you are deleting a file\.  | 

To delete a directory, the `@rds_file_path` must end with a backslash \(`\`\) and `@force_delete` must be set to `1`\.

The following example deletes the file `D:\S3\delete_me.txt`\.

```
exec msdb.dbo.rds_delete_from_filesystem
    @rds_file_path='D:\S3\delete_me.txt';
```

The following example deletes the directory `D:\S3\example_folder\`\.

```
exec msdb.dbo.rds_delete_from_filesystem
    @rds_file_path='D:\S3\example_folder\',
    @force_delete=1;
```

### Monitoring the Status of a File Transfer Task<a name="Appendix.SQLServer.Options.S3-integration.using.monitortasks"></a>

To track the status of your S3 integration task, call the `rds_fn_task_status` function\. It takes two parameters\. The first parameter should always be `NULL` because it doesn't apply to S3 integration\. The second parameter accepts a task ID\. Set the second parameter to `0` to get results for all tasks\. 

To see a list of all tasks, set the first parameter to `NULL` and the second parameter to `0`, as shown in the following example\.

```
SELECT * FROM msdb.dbo.rds_fn_task_status(NULL,0);
```

To get a specific task, set the first parameter to `NULL` and the second parameter to the task ID, as shown in the following example\.

```
SELECT * FROM msdb.dbo.rds_fn_task_status(NULL,42);
```

The `rds_fn_task_status` function returns the following information\.


| Output Parameter | Description | 
| --- | --- | 
| `task_id` | The ID of the task\. | 
| `task_type` | For S3 integration, tasks can have the following task types: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/User.SQLServer.Options.S3-integration.html)  | 
| `database_name` | Not applicable to S3 integration tasks\. | 
| `% complete` | The progress of the task as a percentage\. | 
| `duration(mins)` | The amount of time spent on the task, in minutes\. | 
| `lifecycle` |  The status of the task\. Possible statuses are the following: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/User.SQLServer.Options.S3-integration.html)  | 
| `task_info` | Additional information about the task\. If an error occurs during processing, this column contains information about the error\.  | 
| `last_updated` | The date and time that the task status was last updated\.  | 
| `created_at` | The date and time that the task was created\. | 
| `S3_object_arn` | The ARN of the S3 object downloaded from or uploaded to\. | 
| `overwrite_S3_backup_file` | Not applicable to S3 integration tasks\. | 
| `KMS_master_key_arn` | Not applicable to S3 integration tasks\. | 
| `filepath` | The file path on the RDS DB instance\. | 
| `overwrite_file` | An option that indicates if an existing file is overwritten\. | 
| `task_metadata` | Not applicable to S3 integration tasks\. | 

### Canceling a Task<a name="Appendix.SQLServer.Options.S3-integration.canceltasks"></a>

To cancel S3 integration tasks, use the `msdb.dbo.rds_cancel_task` stored procedure with the `task_id` parameter\. Delete and list tasks that are in progress can't be cancelled\. The following example shows a request to cancel a task\. 

```
exec msdb.dbo.rds_cancel_task @task_id = 1234;
```

To get an overview of all tasks and their task IDs, use the `rds_fn_task_status` function as described in [Monitoring the Status of a File Transfer Task](#Appendix.SQLServer.Options.S3-integration.using.monitortasks)\.

## Multi\-AZ Limitations for S3 Integration<a name="S3-MAZ"></a>

On Multi\-AZ instances, files in the `D:\S3` folder are deleted on the standby replica after a failover\. A failover can be planned, for example, during DB instance modifications such as changing the instance class or upgrading the engine version\. Or a failover can be unplanned, during an outage of the primary\.

**Note**  
We don't recommend using the `D:\S3` folder for file storage\. The best practice is to upload created files to Amazon S3 to make them durable, and download files when you need to import data\.

To determine the last failover time, you can use the `msdb.dbo.rds_failover_time` stored procedure\. For more information, see [Determining the Last Failover Time](Appendix.SQLServer.CommonDBATasks.LastFailover.md)\.

**Example of No Recent Failover**  
This example shows the output when there is no recent failover in the error logs\. No failover has happened since 2020\-04\-29 23:59:00\.01\.  
Therefore, all files downloaded after that time that haven't been deleted using the `rds_delete_from_filesystem` stored procedure are still accessible on the current host\. Files downloaded before that time might also be available\.  


| errorlog\_available\_from | recent\_failover\_time | 
| --- | --- | 
|  2020\-04\-29 23:59:00\.0100000  |  null  | 

**Example of Recent Failover**  
This example shows the output when there is a failover in the error logs\. The most recent failover was at 2020\-05\-05 18:57:51\.89\.  
All files downloaded after that time that haven't been deleted using the `rds_delete_from_filesystem` stored procedure are still accessible on the current host\.  


| errorlog\_available\_from | recent\_failover\_time | 
| --- | --- | 
|  2020\-04\-29 23:59:00\.0100000  |  2020\-05\-05 18:57:51\.8900000  | 

## Disabling RDS SQL Server Integration with S3<a name="Appendix.SQLServer.Options.S3-integration.disabling"></a>

Following, you can find how to disable Amazon S3 integration with Amazon RDS for SQL Server\. Files in `D:\S3\` aren't deleted when disabling S3 integration\.

**Note**  
To remove an IAM role from a DB instance, the status of the DB instance must be `available`\.

### Console<a name="Appendix.SQLServer.Options.S3-integration.disabling.console"></a>

**To disassociate your IAM role from your DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. Choose the RDS SQL Server DB instance name to display its details\.

1. On the **Connectivity & security** tab, in the **Manage IAM roles** section, choose the IAM role to remove\.

1. Choose **Delete**\.

### AWS CLI<a name="Appendix.SQLServer.Options.S3-integration.disabling.cli"></a>

**To remove the IAM role from the RDS SQL Server DB instance**
+ The following AWS CLI command removes the IAM role from a RDS SQL Server DB instance named `mydbinstance`\.  
**Example**  

  For Linux, macOS, or Unix:

  ```
  aws rds remove-role-from-db-instance \
  	   --db-instance-identifier mydbinstance \
  	   --feature-name S3_INTEGRATION \
  	   --role-arn your-role-arn
  ```

  For Windows:

  ```
  aws rds remove-role-from-db-instance ^
  	   --db-instance-identifier mydbinstance ^
  	   --feature-name S3_INTEGRATION ^
  	   --role-arn your-role-arn
  ```

  Replace `your-role-arn` with the appropriate IAM role ARN for the `--feature-name` option\.