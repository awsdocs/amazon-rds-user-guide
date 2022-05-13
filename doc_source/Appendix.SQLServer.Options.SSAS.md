# Support for SQL Server Analysis Services in Amazon RDS for SQL Server<a name="Appendix.SQLServer.Options.SSAS"></a>

Microsoft SQL Server Analysis Services \(SSAS\) is part of the Microsoft Business Intelligence \(MSBI\) suite\. SSAS is an online analytical processing \(OLAP\) and data mining tool that is installed within SQL Server\. You use SSAS to analyze data to help make business decisions\. SSAS differs from the SQL Server relational database because SSAS is optimized for queries and calculations common in a business intelligence environment\.

 You can turn on SSAS for existing or new DB instances\. It's installed on the same DB instance as your database engine\. For more information on SSAS, see the Microsoft [Analysis services documentation](https://docs.microsoft.com/en-us/analysis-services)\.

Amazon RDS supports SSAS for SQL Server Standard and Enterprise Editions on the following versions:
+ Tabular mode:
  + SQL Server 2019, version 15\.00\.4043\.16\.v1 and higher
  + SQL Server 2017, version 14\.00\.3223\.3\.v1 and higher
  + SQL Server 2016, version 13\.00\.5426\.0\.v1 and higher
+ Multidimensional mode:
  + SQL Server 2017, version 14\.00\.3381\.3\.v1 and higher
  + SQL Server 2016, version 13\.00\.5882\.1\.v1 and higher

**Contents**
+ [Limitations](#SSAS.Limitations)
+ [Turning on SSAS](#SSAS.Enabling)
  + [Creating an option group for SSAS](#SSAS.OptionGroup)
  + [Adding the SSAS option to the option group](#SSAS.Add)
  + [Associating the option group with your DB instance](#SSAS.Apply)
  + [Allowing inbound access to your VPC security group](#SSAS.InboundRule)
  + [Enabling Amazon S3 integration](#SSAS.EnableS3)
+ [Deploying SSAS projects on Amazon RDS](#SSAS.Deploy)
+ [Monitoring the status of a deployment task](#SSAS.Monitor)
+ [Using SSAS on Amazon RDS](#SSAS.Use)
  + [Setting up a Windows\-authenticated user for SSAS](#SSAS.Use.Auth)
  + [Adding a domain user as a database administrator](#SSAS.Admin)
  + [Creating an SSAS proxy](#SSAS.Use.Proxy)
  + [Scheduling SSAS database processing using SQL Server Agent](#SSAS.Use.Schedule)
  + [Revoking SSAS access from the proxy](#SSAS.Use.Revoke)
+ [Backing up an SSAS database](#SSAS.Backup)
+ [Restoring an SSAS database](#SSAS.Restore)
  + [Restoring a DB instance to a specified time](#SSAS.PITR)
+ [Changing the SSAS mode](#SSAS.ChangeMode)
+ [Turning off SSAS](#SSAS.Disable)
+ [Troubleshooting SSAS issues](#SSAS.Trouble)

## Limitations<a name="SSAS.Limitations"></a>

The following limitations apply to using SSAS on RDS for SQL Server:
+ RDS for SQL Server supports running SSAS in Tabular or Multidimensional mode\. For more information, see [Comparing tabular and multidimensional solutions](https://docs.microsoft.com/en-us/analysis-services/comparing-tabular-and-multidimensional-solutions-ssas) in the Microsoft documentation\.
+ You can only use one SSAS mode at a time\. Before changing modes, make sure to delete all of the SSAS databases\.

  For more information, see [Changing the SSAS mode](#SSAS.ChangeMode)\.
+ Multidimensional mode isn't supported on SQL Server 2019\.
+ Multi\-AZ instances aren't supported\.
+ Instances must use AWS Directory Service for Microsoft Active Directory for SSAS authentication\.
+ Users aren't given SSAS server administrator access, but they can be granted database\-level administrator access\.
+ The only supported port for accessing SSAS is 2383\.
+ You can't deploy projects directly\. We provide an RDS stored procedure to do this\. For more information, see [Deploying SSAS projects on Amazon RDS](#SSAS.Deploy)\.
+ Processing during deployment isn't supported\.
+ Using \.xmla files for deployment isn't supported\.
+ SSAS project input files and database backup output files can only be in the `D:\S3` folder on the DB instance\.

## Turning on SSAS<a name="SSAS.Enabling"></a>

Use the following process to turn on SSAS for your DB instance:

1. Create a new option group, or choose an existing option group\.

1. Add the `SSAS` option to the option group\.

1. Associate the option group with the DB instance\.

1. Allow inbound access to the virtual private cloud \(VPC\) security group for the SSAS listener port\.

1. Turn on Amazon S3 integration\.

### Creating an option group for SSAS<a name="SSAS.OptionGroup"></a>

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

### Adding the SSAS option to the option group<a name="SSAS.Add"></a>

Next, use the AWS Management Console or the AWS CLI to add the `SSAS` option to the option group\.

#### Console<a name="SSAS.Add.Console"></a>

**To add the SSAS option**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Option groups**\.

1. Choose the option group that you just created\.

1. Choose **Add option**\.

1. Under **Option details**, choose **SSAS** for **Option name**\.

1. Under **Option settings**, do the following:

   1. For **Max memory**, enter a value in the range 10–80\.

      **Max memory** specifies the upper threshold above which SSAS begins releasing memory more aggressively to make room for requests that are running, and also new high\-priority requests\. The number is a percentage of the total memory of the DB instance\. The allowed values are 10–80, and the default is 45\.

   1. For **Mode**, choose the SSAS server mode, **Tabular** or **Multidimensional**\.

      If you don't see the **Mode** option setting, it means that Multidimensional mode isn't supported in your AWS Region\. For more information, see [Limitations](#SSAS.Limitations)\.

      **Tabular** is the default\.

   1. For **Security groups**, choose the VPC security group to associate with the option\.
**Note**  
The port for accessing SSAS, 2383, is prepopulated\.

1. Under **Scheduling**, choose whether to add the option immediately or at the next maintenance window\.

1. Choose **Add option**\.

#### CLI<a name="SSAS.Add.CLI"></a>

**To add the SSAS option**

1. Create a JSON file, for example `ssas-option.json`, with the following parameters:
   + `OptionGroupName` – The name of option group that you created or chose previously \(`ssas-se-2017` in the following example\)\.
   + `Port` – The port that you use to access SSAS\. The only supported port is 2383\.
   + `VpcSecurityGroupMemberships` – Memberships for VPC security groups for your RDS DB instance\.
   + `MAX_MEMORY` – The upper threshold above which SSAS should begin releasing memory more aggressively to make room for requests that are running, and also new high\-priority requests\. The number is a percentage of the total memory of the DB instance\. The allowed values are 10–80, and the default is 45\.
   + `MODE` – The SSAS server mode, either `Tabular` or `Multidimensional`\. `Tabular` is the default\.

     If you receive an error that the `MODE` option setting isn't valid, it means that Multidimensional mode isn't supported in your AWS Region\. For more information, see [Limitations](#SSAS.Limitations)\.

   The following is an example of a JSON file with SSAS option settings\.

   ```
   {
   "OptionGroupName": "ssas-se-2017",
   "OptionsToInclude": [
   	{
   	"OptionName": "SSAS",
   	"Port": 2383,
   	"VpcSecurityGroupMemberships": ["sg-0abcdef123"],
   	"OptionSettings": [{"Name":"MAX_MEMORY","Value":"60"},{"Name":"MODE","Value":"Multidimensional"}]
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

### Associating the option group with your DB instance<a name="SSAS.Apply"></a>

You can use the console or the CLI to associate the option group with your DB instance\.

#### Console<a name="SSAS.Apply.Console"></a>

Associate your option group with a new or existing DB instance:
+ For a new DB instance, associate the option group with the DB instance when you launch the instance\. For more information, see [Creating an Amazon RDS DB instance](USER_CreateDBInstance.md)\.
+ For an existing DB instance, modify the instance and associate the new option group with it\. For more information, see [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)\.
**Note**  
If you use an existing instance, it must already have an Active Directory domain and AWS Identity and Access Management \(IAM\) role associated with it\. If you create a new instance, specify an existing Active Directory domain and IAM role\. For more information, see [Using Windows Authentication with an Amazon RDS for SQL Server DB instance](USER_SQLServerWinAuth.md)\.

#### CLI<a name="SSAS.Apply.CLI"></a>

You can associate your option group with a new or existing DB instance\.

**Note**  
If you use an existing instance, it must already have an Active Directory domain and IAM role associated with it\. If you create a new instance, specify an existing Active Directory domain and IAM role\. For more information, see [Using Windows Authentication with an Amazon RDS for SQL Server DB instance](USER_SQLServerWinAuth.md)\.

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

### Allowing inbound access to your VPC security group<a name="SSAS.InboundRule"></a>

Create an inbound rule for the specified SSAS listener port in the VPC security group associated with your DB instance\. For more information about setting up security groups, see [Provide access to your DB instance in your VPC by creating a security group](CHAP_SettingUp.md#CHAP_SettingUp.SecurityGroup)\.

### Enabling Amazon S3 integration<a name="SSAS.EnableS3"></a>

To download model configuration files to your host for deployment, use Amazon S3 integration\. For more information, see [Integrating an Amazon RDS for SQL Server DB instance with Amazon S3](User.SQLServer.Options.S3-integration.md)\. 

## Deploying SSAS projects on Amazon RDS<a name="SSAS.Deploy"></a>

On RDS, you can't deploy SSAS projects directly by using SQL Server Management Studio \(SSMS\)\. To deploy projects, use an RDS stored procedure\.

**Note**  
Using \.xmla files for deployment isn't supported\.

Before you deploy projects, make sure of the following:
+ Amazon S3 integration is turned on\. For more information, see [Integrating an Amazon RDS for SQL Server DB instance with Amazon S3](User.SQLServer.Options.S3-integration.md)\.
+ The `Processing Option` configuration setting is set to `Do Not Process`\. This setting means that no processing happens after deployment\.
+ You have both the `myssasproject.asdatabase` and `myssasproject.deploymentoptions` files\. They're automatically generated when you build the SSAS project\.

**To deploy an SSAS project on RDS**

1. Download the `.asdatabase` \(SSAS model\) file from your S3 bucket to your DB instance, as shown in the following example\. For more information on the download parameters, see [Downloading files from an Amazon S3 bucket to a SQL Server DB instance](User.SQLServer.Options.S3-integration.md#Appendix.SQLServer.Options.S3-integration.using.download)\.

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

## Monitoring the status of a deployment task<a name="SSAS.Monitor"></a>

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


| Output parameter | Description | 
| --- | --- | 
| `task_id` | The ID of the task\. | 
| `task_type` | For SSAS, tasks can have the following task types: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Appendix.SQLServer.Options.SSAS.html)  | 
| `database_name` | Not applicable to SSAS tasks\. | 
| `% complete` | The progress of the task as a percentage\. | 
| `duration (mins)` | The amount of time spent on the task, in minutes\. | 
| `lifecycle` |  The status of the task\. Possible statuses are the following: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Appendix.SQLServer.Options.SSAS.html)  | 
| `task_info` | Additional information about the task\. If an error occurs during processing, this column contains information about the error\. For more information, see [Troubleshooting SSAS issues](#SSAS.Trouble)\. | 
| `last_updated` | The date and time that the task status was last updated\. | 
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

1. Locate the connection string, and update the user name and password to give access to the source SQL database\. Doing this is required for processing SSAS objects\.

   1. For Tabular mode, do the following:

      1. Expand the **Connections** tab\.

      1. Open the context \(right\-click\) menu for the connection object, and then choose **Properties**\.

      1. Update the user name and password in the connection string\.

   1. For Multidimensional mode, do the following:

      1. Expand the **Data Sources** tab\.

      1. Open the context \(right\-click\) menu for the data source object, and then choose **Properties**\.

      1. Update the user name and password in the connection string\.

1. Open the context \(right\-click\) menu for the SSAS database that you created and choose **Process Database**\.

   Depending on the size of the input data, the processing operation might take several minutes to complete\.

**Topics**
+ [Setting up a Windows\-authenticated user for SSAS](#SSAS.Use.Auth)
+ [Adding a domain user as a database administrator](#SSAS.Admin)
+ [Creating an SSAS proxy](#SSAS.Use.Proxy)
+ [Scheduling SSAS database processing using SQL Server Agent](#SSAS.Use.Schedule)
+ [Revoking SSAS access from the proxy](#SSAS.Use.Revoke)

### Setting up a Windows\-authenticated user for SSAS<a name="SSAS.Use.Auth"></a>

The main administrator user \(sometimes called the master user\) can use the following code example to set up a Windows\-authenticated login and grant the required procedure permissions\. Doing this grants permissions to the domain user to run SSAS customer tasks, use S3 file transfer procedures, create credentials, and work with the SQL Server Agent proxy\. For more information, see [Credentials \(database engine\)](https://docs.microsoft.com/en-us/sql/relational-databases/security/authentication-access/credentials-database-engine?view=sql-server-ver15) and [Create a SQL Server Agent proxy](https://docs.microsoft.com/en-us/sql/ssms/agent/create-a-sql-server-agent-proxy?view=sql-server-ver15) in the Microsoft documentation\.

You can grant some or all of the following permissions as needed to Windows\-authenticated users\.

**Example**  

```
-- Create a server-level domain user login, if it doesn't already exist
USE [master]
GO
CREATE LOGIN [mydomain\user_name] FROM WINDOWS
GO

-- Create domain user, if it doesn't already exist
USE [msdb]
GO
CREATE USER [mydomain\user_name] FOR LOGIN [mydomain\user_name]
GO

-- Grant necessary privileges to the domain user
USE [master]
GO
GRANT ALTER ANY CREDENTIAL TO [mydomain\user_name]
GO

USE [msdb]
GO
GRANT EXEC ON msdb.dbo.rds_msbi_task TO [mydomain\user_name] with grant option
GRANT SELECT ON msdb.dbo.rds_fn_task_status TO [mydomain\user_name] with grant option
GRANT EXEC ON msdb.dbo.rds_task_status TO [mydomain\user_name] with grant option
GRANT EXEC ON msdb.dbo.rds_cancel_task TO [mydomain\user_name] with grant option
GRANT EXEC ON msdb.dbo.rds_download_from_s3 TO [mydomain\user_name] with grant option
GRANT EXEC ON msdb.dbo.rds_upload_to_s3 TO [mydomain\user_name] with grant option
GRANT EXEC ON msdb.dbo.rds_delete_from_filesystem TO [mydomain\user_name] with grant option
GRANT EXEC ON msdb.dbo.rds_gather_file_details TO [mydomain\user_name] with grant option
GRANT EXEC ON msdb.dbo.sp_add_proxy TO [mydomain\user_name] with grant option
GRANT EXEC ON msdb.dbo.sp_update_proxy TO [mydomain\user_name] with grant option
GRANT EXEC ON msdb.dbo.sp_grant_login_to_proxy TO [mydomain\user_name] with grant option
GRANT EXEC ON msdb.dbo.sp_revoke_login_from_proxy TO [mydomain\user_name] with grant option
GRANT EXEC ON msdb.dbo.sp_delete_proxy TO [mydomain\user_name] with grant option
GRANT EXEC ON msdb.dbo.sp_enum_login_for_proxy to [mydomain\user_name] with grant option
GRANT EXEC ON msdb.dbo.sp_enum_proxy_for_subsystem TO [mydomain\user_name] with grant option
GRANT EXEC ON msdb.dbo.rds_sqlagent_proxy TO [mydomain\user_name] with grant option
ALTER ROLE [SQLAgentUserRole] ADD MEMBER [mydomain\user_name]
GO
```

### Adding a domain user as a database administrator<a name="SSAS.Admin"></a>

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

### Creating an SSAS proxy<a name="SSAS.Use.Proxy"></a>

To be able to schedule SSAS database processing using SQL Server Agent, create an SSAS credential and an SSAS proxy\. Run these procedures as a Windows\-authenticated user\.

**To create the SSAS credential**
+ Create the credential for the proxy\. To do this, you can use SSMS or the following SQL statement\.

  ```
  USE [master]
  GO
  CREATE CREDENTIAL [SSAS_Credential] WITH IDENTITY = N'mydomain\user_name', SECRET = N'mysecret'
  GO
  ```
**Note**  
`IDENTITY` must be a domain\-authenticated login\. Replace `mysecret` with the password for the domain\-authenticated login\.

**To create the SSAS proxy**

1. Use the following SQL statement to create the proxy\.

   ```
   USE [msdb]
   GO
   EXEC msdb.dbo.sp_add_proxy @proxy_name=N'SSAS_Proxy',@credential_name=N'SSAS_Credential',@description=N''
   GO
   ```

1. Use the following SQL statement to grant access to the proxy to other users\.

   ```
   USE [msdb]
   GO
   EXEC msdb.dbo.sp_grant_login_to_proxy @proxy_name=N'SSAS_Proxy',@login_name=N'mydomain\user_name'
   GO
   ```

1. Use the following SQL statement to give the SSAS subsystem access to the proxy\.

   ```
   USE [msdb]
   GO
   EXEC msdb.dbo.rds_sqlagent_proxy @task_type='GRANT_SUBSYSTEM_ACCESS',@proxy_name='SSAS_Proxy',@proxy_subsystem='SSAS'
   GO
   ```

**To view the proxy and grants on the proxy**

1. Use the following SQL statement to view the grantees of the proxy\.

   ```
   USE [msdb]
   GO
   EXEC sp_help_proxy
   GO
   ```

1. Use the following SQL statement to view the subsystem grants\.

   ```
   USE [msdb]
   GO
   EXEC msdb.dbo.sp_enum_proxy_for_subsystem
   GO
   ```

### Scheduling SSAS database processing using SQL Server Agent<a name="SSAS.Use.Schedule"></a>

After you create the credential and proxy and grant SSAS access to the proxy, you can create a SQL Server Agent job to schedule SSAS database processing\.

**To schedule SSAS database processing**
+ Use SSMS or T\-SQL for creating the SQL Server Agent job\. The following example uses T\-SQL\. You can further configure its job schedule through SSMS or T\-SQL\.
  + The `@command` parameter outlines the XML for Analysis \(XMLA\) command to be run by the SQL Server Agent job\. This example configures SSAS Multidimensional database processing\.
  + The `@server` parameter outlines the target SSAS server name of the SQL Server Agent job\.

    To call the SSAS service within the same RDS DB instance where the SQL Server Agent job resides, use `localhost:2383`\.

    To call the SSAS service from outside the RDS DB instance, use the RDS endpoint\. You can also use the Kerberos Active Directory \(AD\) endpoint \(`your-DB-instance-name.your-AD-domain-name`\) if the RDS DB instances are joined by the same domain\. For external DB instances, make sure to properly configure the VPC security group associated with the RDS DB instance for a secure connection\.

  You can further edit the query to support various XMLA operations\. Make edits either by directly modifying the T\-SQL query or by using the SSMS UI following SQL Server Agent job creation\.

  ```
  USE [msdb]
  GO
  DECLARE @jobId BINARY(16)
  EXEC msdb.dbo.sp_add_job @job_name=N'SSAS_Job', 
      @enabled=1, 
      @notify_level_eventlog=0, 
      @notify_level_email=0, 
      @notify_level_netsend=0, 
      @notify_level_page=0, 
      @delete_level=0, 
      @category_name=N'[Uncategorized (Local)]', 
      @job_id = @jobId OUTPUT
  GO
  EXEC msdb.dbo.sp_add_jobserver 
      @job_name=N'SSAS_Job', 
      @server_name = N'(local)'
  GO
  EXEC msdb.dbo.sp_add_jobstep @job_name=N'SSAS_Job', @step_name=N'Process_SSAS_Object', 
      @step_id=1, 
      @cmdexec_success_code=0, 
      @on_success_action=1, 
      @on_success_step_id=0, 
      @on_fail_action=2, 
      @on_fail_step_id=0, 
      @retry_attempts=0, 
      @retry_interval=0, 
      @os_run_priority=0, @subsystem=N'ANALYSISCOMMAND', 
      @command=N'<Batch xmlns="http://schemas.microsoft.com/analysisservices/2003/engine">
          <Parallel>
              <Process xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
                  xmlns:ddl2="http://schemas.microsoft.com/analysisservices/2003/engine/2" xmlns:ddl2_2="http://schemas.microsoft.com/analysisservices/2003/engine/2/2" 
                  xmlns:ddl100_100="http://schemas.microsoft.com/analysisservices/2008/engine/100/100" xmlns:ddl200="http://schemas.microsoft.com/analysisservices/2010/engine/200" 
                  xmlns:ddl200_200="http://schemas.microsoft.com/analysisservices/2010/engine/200/200" xmlns:ddl300="http://schemas.microsoft.com/analysisservices/2011/engine/300" 
                  xmlns:ddl300_300="http://schemas.microsoft.com/analysisservices/2011/engine/300/300" xmlns:ddl400="http://schemas.microsoft.com/analysisservices/2012/engine/400" 
                  xmlns:ddl400_400="http://schemas.microsoft.com/analysisservices/2012/engine/400/400" xmlns:ddl500="http://schemas.microsoft.com/analysisservices/2013/engine/500" 
                  xmlns:ddl500_500="http://schemas.microsoft.com/analysisservices/2013/engine/500/500">
                  <Object>
                      <DatabaseID>Your_SSAS_Database_ID</DatabaseID>
                  </Object>
                  <Type>ProcessFull</Type>
                  <WriteBackTableCreation>UseExisting</WriteBackTableCreation>
              </Process>
          </Parallel>
      </Batch>', 
      @server=N'localhost:2383', 
      @database_name=N'master', 
      @flags=0, 
      @proxy_name=N'SSAS_Proxy'
  GO
  ```

### Revoking SSAS access from the proxy<a name="SSAS.Use.Revoke"></a>

You can revoke access to the SSAS subsystem and delete the SSAS proxy using the following stored procedures\.

**To revoke access and delete the proxy**

1. Revoke subsystem access\.

   ```
   USE [msdb]
   GO
   EXEC msdb.dbo.rds_sqlagent_proxy @task_type='REVOKE_SUBSYSTEM_ACCESS',@proxy_name='SSAS_Proxy',@proxy_subsystem='SSAS'
   GO
   ```

1. Revoke the grants on the proxy\.

   ```
   USE [msdb]
   GO
   EXEC msdb.dbo.sp_revoke_login_from_proxy @proxy_name=N'SSAS_Proxy',@name=N'mydomain\user_name'
   GO
   ```

1. Delete the proxy\.

   ```
   USE [msdb]
   GO
   EXEC dbo.sp_delete_proxy @proxy_name = N'SSAS_Proxy'
   GO
   ```

## Backing up an SSAS database<a name="SSAS.Backup"></a>

You can create SSAS database backup files only in the `D:\S3` folder on the DB instance\. To move the backup files to your S3 bucket, use Amazon S3\.

You can back up an SSAS database as follows:
+ A domain user with the `admin` role for a particular database can use SSMS to back up the database to the `D:\S3` folder\.

  For more information, see [Adding a domain user as a database administrator](#SSAS.Admin)\.
+ You can use the following stored procedure\. This stored procedure doesn't support encryption\.

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

## Restoring an SSAS database<a name="SSAS.Restore"></a>

Use the following stored procedure to restore an SSAS database from a backup\. 

You can't restore a database if there is an existing SSAS database with the same name\. The stored procedure for restoring doesn't support encrypted backup files\.

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

### Restoring a DB instance to a specified time<a name="SSAS.PITR"></a>

Point\-in\-time recovery \(PITR\) doesn't apply to SSAS databases\. If you do PITR, only the SSAS data in the last snapshot before the requested time is available on the restored instance\.

**To have up\-to\-date SSAS databases on a restored DB instance**

1. Back up your SSAS databases to the `D:\S3` folder on the source instance\.

1. Transfer the backup files to the S3 bucket\.

1. Transfer the backup files from the S3 bucket to the `D:\S3` folder on the restored instance\.

1. Run the stored procedure to restore the SSAS databases onto the restored instance\.

   You can also reprocess the SSAS project to restore the databases\.

## Changing the SSAS mode<a name="SSAS.ChangeMode"></a>

You can change the mode in which SSAS runs, either Tabular or Multidimensional\. To change the mode, use the AWS Management Console or the AWS CLI to modify the options settings in the SSAS option\.

**Important**  
You can only use one SSAS mode at a time\. Make sure to delete all of the SSAS databases before changing the mode, or you receive an error\.

### Console<a name="SSAS.ChangeMode.CON"></a>

The following Amazon RDS console procedure changes the SSAS mode to Tabular and sets the `MAX_MEMORY` parameter to 70 percent\.

**To modify the SSAS option**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Option groups**\.

1. Choose the option group with the `SSAS` option that you want to modify \(`ssas-se-2017` in the previous examples\)\.

1. Choose **Modify option**\.

1. Change the option settings:

   1. For **Max memory**, enter **70**\.

   1. For **Mode**, choose **Tabular**\.

1. Choose **Modify option**\.

### AWS CLI<a name="SSAS.ChangeMode.CLI"></a>

The following AWS CLI example changes the SSAS mode to Tabular and sets the `MAX_MEMORY` parameter to 70 percent\.

For the CLI command to work, make sure to include all of the required parameters, even if you're not modifying them\.

**To modify the SSAS option**
+ Use one of the following commands\.  
**Example**  

  For Linux, macOS, or Unix:

  ```
  aws rds add-option-to-option-group \
      --option-group-name ssas-se-2017 \
      --options "OptionName=SSAS,VpcSecurityGroupMemberships=sg-12345e67,OptionSettings=[{Name=MAX_MEMORY,Value=70},{Name=MODE,Value=Tabular}]" \
      --apply-immediately
  ```

  For Windows:

  ```
  aws rds add-option-to-option-group ^
      --option-group-name ssas-se-2017 ^
      --options OptionName=SSAS,VpcSecurityGroupMemberships=sg-12345e67,OptionSettings=[{Name=MAX_MEMORY,Value=70},{Name=MODE,Value=Tabular}] ^
      --apply-immediately
  ```

## Turning off SSAS<a name="SSAS.Disable"></a>

To turn off SSAS, remove the `SSAS` option from its option group\.

**Important**  
Before you remove the `SSAS` option, delete your SSAS databases\.  
We highly recommend that you back up your SSAS databases before deleting them and removing the `SSAS` option\.

### Console<a name="SSAS.Disable.Console"></a>

**To remove the SSAS option from its option group**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Option groups**\.

1. Choose the option group with the `SSAS` option that you want to remove \(`ssas-se-2017` in the previous examples\)\.

1. Choose **Delete option**\.

1. Under **Deletion options**, choose **SSAS** for **Options to delete**\.

1. Under **Apply immediately**, choose **Yes** to delete the option immediately, or **No** to delete it at the next maintenance window\.

1. Choose **Delete**\.

### AWS CLI<a name="SSAS.Disable.CLI"></a>

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

## Troubleshooting SSAS issues<a name="SSAS.Trouble"></a>

You might encounter the following issues when using SSAS\.


| Issue | Type | Troubleshooting suggestions | 
| --- | --- | --- | 
| Unable to configure the SSAS option\. The requested SSAS mode is new\_mode, but the current DB instance has number current\_mode databases\. Delete the existing databases before switching to new\_mode mode\. To regain access to current\_mode mode for database deletion, either update the current DB option group, or attach a new option group with %s as the MODE option setting value for the SSAS option\. | RDS event | You can't change the SSAS mode if you still have SSAS databases that use the current mode\. Delete the SSAS databases, then try again\. | 
| Unable to remove the SSAS option because there are number existing mode databases\. The SSAS option can't be removed until all SSAS databases are deleted\. Add the SSAS option again, delete all SSAS databases, and try again\. | RDS event | You can't turn off SSAS if you still have SSAS databases\. Delete the SSAS databases, then try again\. | 
| The SSAS option isn't enabled or is in the process of being enabled\. Try again later\. | RDS stored procedure | You can't run SSAS stored procedures when the option is turned off, or when it's being turned on\. | 
| The SSAS option is configured incorrectly\. Make sure that the option group membership status is "in\-sync", and review the RDS event logs for relevant SSAS configuration error messages\. Following these investigations, try again\. If errors continue to occur, contact AWS Support\. | RDS stored procedure |  You can't run SSAS stored procedures when your option group membership isn't in the `in-sync` status\. This puts the SSAS option in an incorrect configuration state\. If your option group membership status changes to `failed` due to SSAS option modification, there are two possible reasons:   [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Appendix.SQLServer.Options.SSAS.html) Reconfigure the SSAS option, because RDS allows only one SSAS mode at a time, and doesn't support SSAS option removal with SSAS databases present\. Check the RDS event logs for configuration errors for your SSAS instance, and resolve the issues accordingly\.  | 
| Deployment failed\. The change can only be deployed on a server running in deployment\_file\_mode mode\. The current server mode is current\_mode\. | RDS stored procedure |  You can't deploy a Tabular database to a Multidimensional server, or a Multidimensional database to a Tabular server\. Make sure that you're using files with the correct mode, and verify that the `MODE` option setting is set to the appropriate value\.  | 
| The restore failed\. The backup file can only be restored on a server running in restore\_file\_mode mode\. The current server mode is current\_mode\. | RDS stored procedure |  You can't restore a Tabular database to a Multidimensional server, or a Multidimensional database to a Tabular server\. Make sure that you're using files with the correct mode, and verify that the `MODE` option setting is set to the appropriate value\.  | 
| The restore failed\. The backup file and the RDS DB instance versions are incompatible\. | RDS stored procedure |  You can't restore an SSAS database with a version incompatible to the SQL Server instance version\. For more information, see [Compatibility levels for tabular models](https://docs.microsoft.com/en-us/analysis-services/tabular-models/compatibility-level-for-tabular-models-in-analysis-services) and [Compatibility level of a multidimensional database](https://docs.microsoft.com/en-us/analysis-services/multidimensional-models/compatibility-level-of-a-multidimensional-database-analysis-services) in the Microsoft documentation\.  | 
| The restore failed\. The backup file specified in the restore operation is damaged or is not an SSAS backup file\. Make sure that @rds\_file\_path is correctly formatted\. | RDS stored procedure |  You can't restore an SSAS database with a damaged file\. Make sure that the file isn't damaged or corrupted\. This error can also be raised when `@rds_file_path` isn't correctly formatted \(for example, it has double backslashes as in `D:\S3\\incorrect_format.abf`\)\.  | 
| The restore failed\. The restored database name can't contain any reserved words or invalid characters: \. , ; ' ` : / \\\\ \* \| ? \\" & % $ \! \+ = \( \) \[ \] \{ \} < >, or be longer than 100 characters\. | RDS stored procedure |  The restored database name can't contain any reserved words or characters that aren't valid, or be longer than 100 characters\. For SSAS object naming conventions, see [Object naming rules](https://docs.microsoft.com/en-us/analysis-services/multidimensional-models/olap-physical/object-naming-rules-analysis-services) in the Microsoft documentation\.  | 
| An invalid role name was provided\. The role name can't contain any reserved strings\. | RDS stored procedure |  The role name can't contain any reserved strings\. For SSAS object naming conventions, see [Object naming rules](https://docs.microsoft.com/en-us/analysis-services/multidimensional-models/olap-physical/object-naming-rules-analysis-services) in the Microsoft documentation\.  | 
| An invalid role name was provided\. The role name can't contain any of the following reserved characters: \. , ; ' ` : / \\\\ \* \| ? \\" & % $ \! \+ = \( \) \[ \] \{ \} < > | RDS stored procedure |  The role name can't contain any reserved characters\. For SSAS object naming conventions, see [Object naming rules](https://docs.microsoft.com/en-us/analysis-services/multidimensional-models/olap-physical/object-naming-rules-analysis-services) in the Microsoft documentation\.  | 