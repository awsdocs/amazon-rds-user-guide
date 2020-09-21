# Support for SQL Server Analysis Services in SQL Server<a name="Appendix.SQLServer.Options.SSAS"></a>

Microsoft SQL Server Analysis Services \(SSAS\) is part of the Microsoft Business Intelligence \(MSBI\) suite\. SSAS is an online analytical processing \(OLAP\) and data mining tool that is installed within SQL Server\. You use SSAS to analyze data to help make business decisions\. SSAS differs from the SQL Server relational database because SSAS is optimized for queries and calculations common in a business intelligence environment\. For more information on SSAS, see the Microsoft [Analysis Services documentation](https://docs.microsoft.com/en-us/analysis-services)\.

Amazon RDS for SQL Server supports running SQL Server Analysis Services \(SSAS\) in Tabular mode\. You can enable SSAS on existing or new DB instances\. It's installed on the same DB instance as your database engine\.

RDS supports SSAS for SQL Server Standard and Enterprise Editions on the following versions:
+ SQL Server 2017, version 14\.00\.3223\.3\.v1 and later
+ SQL Server 2016, version 13\.00\.5426\.0\.v1 and later

## Limitations<a name="SSAS.Limitations"></a>

The following limitations apply to running SSAS on RDS for SQL Server:
+ Tabular is the only supported mode for SSAS\.
+ Multi\-AZ instances aren't supported\.
+ Instances must use AWS Directory Service for Microsoft Active Directory for SSAS authentication\.
+ Users aren't given SSAS server administrator access, but they can be granted database\-level administrator access\.
+ The only supported port for accessing SSAS is 2383\.
+ You can't deploy projects directly\. We provide an RDS stored procedure to do this\. For more information, see [Deploying SSAS Projects on Amazon RDS](#SSAS.Deploy)\. 
+ Processing during deployment isn't supported\.
+ Using \.xmla files for deployment isn't supported\.
+ SSAS project input files and database backup output files can only be in the `D:\S3` folder on the DB instance\.

## Enabling SSAS<a name="SSAS.Enabling"></a>

Use the following process to enable SSAS for your DB instance:

1. Create a new option group, or choose an existing option group\.

1. Add the `SSAS` option to the option group\.

1. Associate the option group with the DB instance\.

1. Allow inbound access to the VPC security group for the SSAS listener port\.

1. Enable Amazon S3 integration\.

### Creating the Option Group for SSAS<a name="SSAS.OptionGroup"></a>

Use the AWS Management Console or the AWS CLI to create an option group that corresponds to the SQL Server engine and version of the DB instance that you plan to use\.

**Note**  
You can also use an existing option group if it's for the correct SQL Server engine and version\.

#### Console<a name="SSAS.OptionGroup.Console"></a>

The following console procedure creates an option group for SQL Server Standard Edition 2017\.

**To create the option group**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Option groups**\.

1. Choose **Create group**\.

1. In the **Create option group** pane, do the following:

   1. For **Name**, enter a name for the option group that is unique within your AWS account, such as **ssas\-se\-2017**\. The name can contain only letters, digits, and hyphens\.

   1. For **Description**, enter a brief description of the option group, such as **SSAS option group for SQL Server SE 2017**\. The description is used for display purposes\. 

   1. For **Engine**, choose **sqlserver\-se**\.

   1. For **Major engine version**, choose **14\.00**\.

1. Choose **Create**\.

#### CLI<a name="SSAS.OptionGroup.CLI"></a>

The following CLI example creates an option group for SQL Server Standard Edition 2017\.

**To create the option group**
+ Use one of the following commands\.  
**Example**  

  For Linux, macOS, or Unix:

  ```
  aws rds create-option-group \
      --option-group-name ssas-se-2017 \
      --engine-name sqlserver-se \
      --major-engine-version 14.00 \
      --option-group-description "SSAS option group for SQL Server SE 2017"
  ```

  For Windows:

  ```
  aws rds create-option-group ^
      --option-group-name ssas-se-2017 ^
      --engine-name sqlserver-se ^
      --major-engine-version 14.00 ^
      --option-group-description "SSAS option group for SQL Server SE 2017"
  ```

### Adding the SSAS Option to the Option Group<a name="SSAS.Add"></a>

Next, use the AWS Management Console or the AWS CLI to add the `SSAS` option to the option group\.

#### Console<a name="SSAS.Add.Console"></a>

**To add the SSAS option**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Option groups**\.

1. Choose the option group that you just created\.

1. Choose **Add option**\.

1. Under **Option details**, choose **SSAS** for **Option name**\.

1. Under **Option settings**, enter a value from 10–80 for **Max memory**\.

   **Max memory** specifies the upper threshold above which SSAS begins releasing memory more aggressively to make room for requests that are running, and also new high\-priority requests\. The number is a percentage of the total memory of the DB instance\. The allowed values are 10–80, and the default is 45\.
**Note**  
The port for accessing SSAS, 2383, is prepopulated\.

1. For **Security groups**, choose the VPC security group to associate with the option\.

1. Under **Scheduling**, choose whether to add the option immediately or at the next maintenance window\.

1. Choose **Add option**\.

#### CLI<a name="SSAS.Add.CLI"></a>

**To add the SSAS option**

1. Create a JSON file, for example `ssas-option.json`, with the following parameters:
   + `OptionGroupName` – The name of option group that you created or chose previously \(`ssas-se-2017` in the following example\)\.
   + `Port` – The port that you use to access SSAS\. The only supported port is 2383\.
   + `VpcSecurityGroupMemberships` – VPC security group memberships for your RDS DB instance\.
   + `MAX_MEMORY` – The upper threshold above which SSAS should begin releasing memory more aggressively to make room for requests that are running, and also new high\-priority requests\. The number is a percentage of the total memory of the DB instance\. The allowed values are 10–80, and the default is 45\.

   ```
   {
   "OptionGroupName": "ssas-se-2017",
   "OptionsToInclude": [
   	{
   	"OptionName": "SSAS",
   	"Port": 2383,
   	"VpcSecurityGroupMemberships" : ["sg-0abcdef123"],
   	"OptionSettings": [{"Name" : "MAX_MEMORY","Value" : "60"}]
   	}],
   "ApplyImmediately": true
   }
   ```

1. Add the `SSAS` option to the option group\.  
**Example**  

   For Linux, macOS, or Unix:

   ```
   aws rds add-option-to-option-group \
       --cli-input-json file://ssas-option.json \
       --apply-immediately
   ```

   For Windows:

   ```
   aws rds add-option-to-option-group ^
       --cli-input-json file://ssas-option.json ^
       --apply-immediately
   ```

### Associating the Option Group with Your DB Instance<a name="SSAS.Apply"></a>

You can use the AWS Management Console or the AWS CLI to associate the option group with your DB instance\.

#### Console<a name="SSAS.Apply.Console"></a>

Associate your option group with a new or existing DB instance:
+ For a new DB instance, associate the option group with the DB instance when you launch the instance\. For more information, see [Creating an Amazon RDS DB Instance](USER_CreateDBInstance.md)\.
+ For an existing DB instance, modify the instance and associate the new option group with it\. For more information, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md)\.
**Note**  
If you use an existing instance, it must already have an Active Directory domain and IAM role associated with it\. If you create a new instance, specify an existing Active Directory domain and IAM role\. For more information, see [Using Windows Authentication with an Amazon RDS for SQL Server DB Instance](USER_SQLServerWinAuth.md)\.

#### CLI<a name="SSAS.Apply.CLI"></a>

You can associate your option group with a new or existing DB instance\.

**Note**  
If you use an existing instance, it must already have an Active Directory domain and IAM role associated with it\. If you create a new instance, specify an existing Active Directory domain and IAM role\. For more information, see [Using Windows Authentication with an Amazon RDS for SQL Server DB Instance](USER_SQLServerWinAuth.md)\.

**To create a DB instance that uses the option group**
+ Specify the same DB engine type and major version that you used when creating the option group\.  
**Example**  

  For Linux, macOS, or Unix:

  ```
  aws rds create-db-instance \
      --db-instance-identifier myssasinstance \
      --db-instance-class db.m5.2xlarge \
      --engine sqlserver-se \
      --engine-version 14.00.3223.3.v1 \
      --allocated-storage 100 \
      --master-user-password secret123 \
      --master-username admin \
      --storage-type gp2 \
      --license-model li \
      --domain-iam-role-name my-directory-iam-role \
      --domain my-domain-id \
      --option-group-name ssas-se-2017
  ```

  For Windows:

  ```
  aws rds create-db-instance ^
      --db-instance-identifier myssasinstance ^
      --db-instance-class db.m5.2xlarge ^
      --engine sqlserver-se ^
      --engine-version 14.00.3223.3.v1 ^
      --allocated-storage 100 ^
      --master-user-password secret123 ^
      --master-username admin ^
      --storage-type gp2 ^
      --license-model li ^
      --domain-iam-role-name my-directory-iam-role ^
      --domain my-domain-id ^
      --option-group-name ssas-se-2017
  ```

**To modify a DB instance to associate the option group**
+ Use one of the following commands\.  
**Example**  

  For Linux, macOS, or Unix:

  ```
  aws rds modify-db-instance \
      --db-instance-identifier myssasinstance \
      --option-group-name ssas-se-2017 \
      --apply-immediately
  ```

  For Windows:

  ```
  aws rds modify-db-instance ^
      --db-instance-identifier myssasinstance ^
      --option-group-name ssas-se-2017 ^
      --apply-immediately
  ```

### Allowing Inbound Access to Your VPC Security Group<a name="SSAS.InboundRule"></a>

Create an inbound rule for the specified SSAS listener port in the VPC security group associated with your DB instance\. For more information about setting up security groups, see [Provide Access to Your DB Instance in Your VPC by Creating a Security Group](CHAP_SettingUp.md#CHAP_SettingUp.SecurityGroup)\.

### Enabling S3 Integration<a name="SSAS.EnableS3"></a>

To download model configuration files to your host for deployment, use S3 integration\. For more information, see [Integrating an Amazon RDS for SQL Server DB Instance with Amazon S3](User.SQLServer.Options.S3-integration.md)\.

## Deploying SSAS Projects on Amazon RDS<a name="SSAS.Deploy"></a>

On RDS, you can't deploy SSAS projects directly by using SQL Server Management Studio \(SSMS\)\. To deploy projects, use an RDS stored procedure\.

**Note**  
Using \.xmla files for deployment isn't supported\.

Before you deploy projects, make sure of the following:
+ S3 integration is enabled\. For more information, see [Integrating an Amazon RDS for SQL Server DB Instance with Amazon S3](User.SQLServer.Options.S3-integration.md)\.
+ The `Processing Option` configuration setting is set to `Do Not Process`\. This setting means that no processing happens after deployment\.
+ You have both the `myssasproject.asdatabase` and `myssasproject.deploymentoptions` files\. They're automatically generated when you build the SSAS project\.

**To deploy an SSAS project on RDS**

1. Download the `.asdatabase` \(SSAS model\) file from your S3 bucket to your DB instance, as shown in the following example\. For more information on the download parameters, see [Downloading Files from an Amazon S3 Bucket to a SQL Server DB Instance](User.SQLServer.Options.S3-integration.md#Appendix.SQLServer.Options.S3-integration.using.download)\.

   ```
   exec msdb.dbo.rds_download_from_s3 
   @s3_arn_of_file='arn:aws:s3:::bucket_name/myssasproject.asdatabase', 
   [@rds_file_path='D:\S3\myssasproject.asdatabase'],
   [@overwrite_file=1];
   ```

1. Download the `.deploymentoptions` file from your S3 bucket to your DB instance\.

   ```
   exec msdb.dbo.rds_download_from_s3
   @s3_arn_of_file='arn:aws:s3:::bucket_name/myssasproject.deploymentoptions', 
   [@rds_file_path='D:\S3\myssasproject.deploymentoptions'],
   [@overwrite_file=1];
   ```

1. Deploy the project\.

   ```
   exec msdb.dbo.rds_msbi_task
   @task_type='SSAS_DEPLOY_PROJECT',
   @file_path='D:\S3\myssasproject.asdatabase';
   ```

## Monitoring the Status of a Deployment Task<a name="SSAS.Monitor"></a>

To track the status of your deployment \(or download\) task, call the `rds_fn_task_status` function\. It takes two parameters\. The first parameter should always be `NULL` because it doesn't apply to SSAS\. The second parameter accepts a task ID\. 

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
| `task_type` | For SSAS, tasks can have the following task types: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Appendix.SQLServer.Options.SSAS.html)  | 
| `database_name` | Not applicable to SSAS tasks\. | 
| `% complete` | The progress of the task as a percentage\. | 
| `duration (mins)` | The amount of time spent on the task, in minutes\. | 
| `lifecycle` |  The status of the task\. Possible statuses are the following: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Appendix.SQLServer.Options.SSAS.html)  | 
| `task_info` | Additional information about the task\. If an error occurs during processing, this column contains information about the error\.  | 
| `last_updated` | The date and time that the task status was last updated\.  | 
| `created_at` | The date and time that the task was created\. | 
| `S3_object_arn` |  Not applicable to SSAS tasks\.  | 
| `overwrite_S3_backup_file` | Not applicable to SSAS tasks\. | 
| `KMS_master_key_arn` |  Not applicable to SSAS tasks\.  | 
| `filepath` |  Not applicable to SSAS tasks\.  | 
| `overwrite_file` |  Not applicable to SSAS tasks\.  | 
| `task_metadata` | Metadata associated with the SSAS task\. | 

## Using SSAS on Amazon RDS<a name="SSAS.Use"></a>

After deploying the SSAS project, you can directly process the OLAP database on SSMS\.

**To use SSAS on RDS**

1. In SSMS, connect to SSAS using the user name and password for the Active Directory domain\.

1. Expand **Databases**\. The newly deployed SSAS database appears\.

1. Expand **Connections**, open the context \(right\-click\) menu for the connection object, and then choose **Properties**\.

1. In the connection string, update the user name and password to those for the source SQL database\. Doing this is required for processing tables\.

1. Open the context \(right\-click\) menu for the SSAS database that you created and choose **Process Database**\.

   Depending on the size of the input data, the processing operation might take several minutes to complete\.

### Adding a Domain User as a Database Administrator<a name="SSAS.admin"></a>

You can add a domain user as an SSAS database administrator in the following ways:
+ A database administrator can use SSMS to create a role with `admin` privileges, then add users to that role\.
+ You can use the following stored procedure\.

  ```
  exec msdb.dbo.rds_msbi_task
  @task_type='SSAS_ADD_DB_ADMIN_MEMBER',
  @database_name='myssasdb',
  @ssas_role_name='exampleRole',
  @ssas_role_member='domain_name\domain_user_name';
  ```

  The following parameters are required:
  + `@task_type` – The type of the MSBI task, in this case `SSAS_ADD_DB_ADMIN_MEMBER`\.
  + `@database_name` – The name of the SSAS database to which you're granting administrator privileges\.
  + `@ssas_role_name` – The SSAS database administrator role name\. If the role doesn't already exist, it's created\.
  + `@ssas_role_member` – The SSAS database user that you're adding to the administrator role\.

## Backing Up an SSAS Database<a name="SSAS.Backup"></a>

You can create SSAS database backup files only in the `D:\S3` folder on the DB instance\. To move the backup files to your S3 bucket, use Amazon S3\.

You can back up an SSAS database as follows:
+ A domain user with the `admin` role for a particular database can use SSMS to back up the database to the `D:\S3` folder\.

  For more information, see [Adding a Domain User as a Database Administrator](#SSAS.admin)\.
+ You can use the following stored procedure\.

  ```
  exec msdb.dbo.rds_msbi_task
  @task_type='SSAS_BACKUP_DB',
  @database_name='myssasdb',
  @file_path='D:\S3\ssas_db_backup.abf',
  [@ssas_apply_compression=1],
  [@ssas_overwrite_file=1];
  ```

  The following parameters are required:
  + `@task_type` – The type of the MSBI task, in this case `SSAS_BACKUP_DB`\.
  + `@database_name` – The name of the SSAS database that you're backing up\.
  + `@file_path` – The path for the SSAS backup file\. The `.abf` extension is required\.

  The following parameters are optional:
  + `@ssas_apply_compression` – Whether to apply SSAS backup compression\. Valid values are 1 \(Yes\) and 0 \(No\)\.
  + `@ssas_overwrite_file` – Whether to overwrite the SSAS backup file\. Valid values are 1 \(Yes\) and 0 \(No\)\.
**Note**  
The stored procedure for backup doesn't support encryption\.

## Restoring an SSAS Database<a name="SSAS.Restore"></a>

Use the following stored procedure to restore an SSAS database from a backup\.

```
exec msdb.dbo.rds_msbi_task
@task_type='SSAS_RESTORE_DB',
@database_name='mynewssasdb',
@file_path='D:\S3\ssas_db_backup.abf';
```

The following parameters are required:
+ `@task_type` – The type of the MSBI task, in this case `SSAS_RESTORE_DB`\.
+ `@database_name` – The name of the new SSAS database that you're restoring to\.
+ `@file_path` – The path to the SSAS backup file\.

**Note**  
You can't restore a database if there is an existing SSAS database with the same name\. The stored procedure for restoring doesn't support encrypted backup files\.

### Restoring a DB Instance to a Specified Time<a name="SSAS.PITR"></a>

Point\-in\-time recovery \(PITR\) doesn't apply to SSAS databases\. If you do PITR, only the SSAS data in the last snapshot before the requested time is available on the restored instance\.

**To have up\-to\-date SSAS databases on a restored DB instance**

1. Back up your SSAS databases to the `D:\S3` folder on the source instance\.

1. Transfer the backup files to the S3 bucket\.

1. Transfer the backup files from the S3 bucket to the `D:\S3` folder on the restored instance\.

1. Run the stored procedure to restore the SSAS databases onto the restored instance\.

**Note**  
You can also reprocess the SSAS project to restore the databases\.

## Disabling SSAS<a name="SSAS.Disable"></a>

To disable SSAS, remove the `SSAS` option from its option group\. Before you remove the `SSAS` option, delete your SSAS databases\.

**Important**  
We highly recommend that you back up your SSAS databases before deleting them and removing the `SSAS` option\.

### Console<a name="SSAS.Disable.Console"></a>

**To remove the SSAS option from its option group**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Option groups**\.

1. Choose the option group with the `SSAS` option \(`ssas-se-2017` in the previous examples\)\.

1. Choose **Delete option**\.

1. Under **Deletion options**, choose **SSAS** for **Options to delete**\.

1. Under **Apply immediately**, choose **Yes** to delete the option immediately, or **No** to delete it at the next maintenance window\.

1. Choose **Delete**\.

### CLI<a name="SSAS.Disable.CLI"></a>

**To remove the SSAS option from its option group**
+ Use one of the following commands\.  
**Example**  

  For Linux, macOS, or Unix:

  ```
  aws rds remove-option-from-option-group \
      --option-group-name ssas-se-2017 \
      --options SSAS \
      --apply-immediately
  ```

  For Windows:

  ```
  aws rds remove-option-from-option-group ^
      --option-group-name ssas-se-2017 ^
      --options SSAS ^
      --apply-immediately
  ```