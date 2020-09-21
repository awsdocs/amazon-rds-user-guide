# Support for SQL Server Integration Services in SQL Server<a name="Appendix.SQLServer.Options.SSIS"></a>

Microsoft SQL Server Integration Services \(SSIS\) is a component that you can use to perform a broad range of data migration tasks\. SSIS is a platform for data integration and workflow applications\. It features a data warehousing tool used for data extraction, transformation, and loading \(ETL\)\. You can also use this tool to automate maintenance of SQL Server databases and updates to multidimensional cube data\.

SSIS projects are organized into packages saved as XML\-based \.dtsx files\. Packages can contain control flows and data flows\. You use data flows to represent ETL operations\. After deployment, packages are stored in SQL Server in the SSISDB database\. SSISDB is an online transaction processing \(OLTP\) database in the full recovery mode\.

Amazon RDS for SQL Server supports running SSIS directly on an RDS DB instance\. You can enable SSIS on an existing or new DB instance\. SSIS is installed on the same DB instance as your database engine\.

RDS supports SSIS for SQL Server Standard and Enterprise Editions on the following versions:
+ SQL Server 2017, version 14\.00\.3223\.3\.v1 and later
+ SQL Server 2016, version 13\.00\.5426\.0\.v1 and later

## Limitations and Recommendations<a name="SSIS.Limitations"></a>

The following limitations and recommendations apply to running SSIS on RDS for SQL Server:
+ The DB instance must use AWS Managed Microsoft AD for SSIS authentication\.
+ The DB instance must have an associated parameter group with the `clr enabled` parameter set to 1\. For more information, see [Modifying the Parameter for SSIS](#SSIS.ModifyParam)\.
**Note**  
If you enable the `clr enabled` parameter on SQL Server 2017, you can't use the common language runtime \(CLR\) on your DB instance\.
+ The following control flow tasks are supported:
  + Analysis Services Execute DDL Task
  + Analysis Services Processing Task
  + Bulk Insert Task
  + Check Database Integrity Task
  + Data Flow Task
  + Data Mining Query Task
  + Data Profiling Task
  + Execute Package Task
  + Execute SQL Server Agent Job Task
  + Execute SQL Task
  + Execute T\-SQL Statement Task
  + Notify Operator Task
  + Rebuild Index Task
  + Reorganize Index Task
  + Shrink Database Task
  + Transfer Database Task
  + Transfer Jobs Task
  + Transfer Logins Task
  + Transfer SQL Server Objects Task
  + Update Statistics Task
+ Only project deployment is supported\.
+ Running SSIS packages by using SQL Server Agent is supported\.
+ Only SQL Server–based logging is supported\.
+ Use only the `D:\S3` folder for working with files\. Files placed in any other directory are deleted\. Be aware of a few other file location details:
  + Place SSIS project input and output files in the `D:\S3` folder\.
  + For the Data Flow Task, change the location for `BLOBTempStoragePath` and `BufferTempStoragePath` to a file inside the `D:\S3` folder\.
  + Ensure that all parameters, variables, and expressions used for file connections point to the `D:\S3` folder\.
  + On Multi\-AZ instances, files created by SSIS in the `D:\S3` folder are deleted after a failover\. For more information, see [Multi\-AZ Limitations for S3 Integration](User.SQLServer.Options.S3-integration.md#S3-MAZ)\.
  + Upload the files created by SSIS in the `D:\S3` folder to your Amazon S3 bucket to make them durable\.
+ Import Column and Export Column transformations and the Script component on the Data Flow Task aren't supported\.
+ You can't enable dump on running SSIS packages, and you can't add data taps on SSIS packages\.
+ The SSIS Scale Out feature isn't supported\.
+ You can't deploy projects directly\. We provide RDS stored procedures to do this\. For more information, see [Deploying an SSIS Project](#SSIS.Deploy)\.
+ Build SSIS project \(\.ispac\) files with the `DoNotSavePasswords` protection mode for deploying on RDS\.
+ SSIS isn't supported on Always On instances with read replicas\.
+ You can't back up the SSISDB database that is associated with the `SSIS` option\.
+ Importing and restoring the SSISDB database from other instances of SSIS isn't supported\.

## Enabling SSIS<a name="SSIS.Enabling"></a>

You enable SSIS by adding the SSIS option to your DB instance\. Use the following process:

1. Create a new option group, or choose an existing option group\.

1. Add the `SSIS` option to the option group\.

1. Create a new parameter group, or choose an existing parameter group\.

1. Modify the parameter group to set the `clr enabled` parameter to 1\.

1. Associate the option group and parameter group with the DB instance\.

1. Enable Amazon S3 integration\.

**Note**  
If a database with the name SSISDB or a reserved SSIS login already exists on the DB instance, you can't enable SSIS on the instance\.

### Creating the Option Group for SSIS<a name="SSIS.OptionGroup"></a>

To work with SSIS, create an option group or modify an option group that corresponds to the SQL Server edition and version of the DB instance that you plan to use\. To do this, use the AWS Management Console or the AWS CLI\.

#### Console<a name="SSIS.OptionGroup.Console"></a>

The following procedure creates an option group for SQL Server Standard Edition 2016\.

**To create the option group**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Option groups**\.

1. Choose **Create group**\.

1. In the **Create option group** window, do the following:

   1. For **Name**, enter a name for the option group that is unique within your AWS account, such as **ssis\-se\-2016**\. The name can contain only letters, digits, and hyphens\.

   1. For **Description**, enter a brief description of the option group, such as **SSIS option group for SQL Server SE 2016**\. The description is used for display purposes\. 

   1. For **Engine**, choose **sqlserver\-se**\.

   1. For **Major engine version**, choose **13\.00**\.

1. Choose **Create**\.

#### CLI<a name="SSIS.OptionGroup.CLI"></a>

The following procedure creates an option group for SQL Server Standard Edition 2016\.

**To create the option group**
+ Run one of the following commands\.  
**Example**  

  For Linux, macOS, or Unix:

  ```
  aws rds create-option-group \
      --option-group-name ssis-se-2016 \
      --engine-name sqlserver-se \
      --major-engine-version 13.00 \
      --option-group-description "SSIS option group for SQL Server SE 2016"
  ```

  For Windows:

  ```
  aws rds create-option-group ^
      --option-group-name ssis-se-2016 ^
      --engine-name sqlserver-se ^
      --major-engine-version 13.00 ^
      --option-group-description "SSIS option group for SQL Server SE 2016"
  ```

### Adding the SSIS Option to the Option Group<a name="SSIS.Add"></a>

Next, use the AWS Management Console or the AWS CLI to add the `SSIS` option to your option group\.

#### Console<a name="SSIS.Add.Console"></a>

**To add the SSIS option**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Option groups**\.

1. Choose the option group that you just created, **ssis\-se\-2016** in this example\.

1. Choose **Add option**\.

1. Under **Option details**, choose **SSIS** for **Option name**\.

1. Under **Scheduling**, choose whether to add the option immediately or at the next maintenance window\.

1. Choose **Add option**\.

#### CLI<a name="SSIS.Add.CLI"></a>

**To add the SSIS option**
+ Add the `SSIS` option to the option group\.  
**Example**  

  For Linux, macOS, or Unix:

  ```
  aws rds add-option-to-option-group \
      --option-group-name ssis-se-2016 \
      --options OptionName=SSIS \
      --apply-immediately
  ```

  For Windows:

  ```
  aws rds add-option-to-option-group ^
      --option-group-name ssis-se-2016 ^
      --options OptionName=SSIS ^
      --apply-immediately
  ```

### Creating the Parameter Group for SSIS<a name="SSIS.CreateParamGroup"></a>

Create or modify a parameter group for the `clr enabled` parameter that corresponds to the SQL Server edition and version of the DB instance that you plan to use for SSIS\.

#### Console<a name="SSIS.CreateParamGroup.Console"></a>

The following procedure creates a parameter group for SQL Server Standard Edition 2016\.

**To create the parameter group**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Parameter groups**\.

1. Choose **Create parameter group**\.

1. In the **Create parameter group** pane, do the following:

   1. For **Parameter group family**, choose **sqlserver\-se\-13\.0**\.

   1. For **Group name**, enter an identifier for the parameter group, such as **ssis\-sqlserver\-se\-13**\.

   1. For **Description**, enter **clr enabled parameter group**\.

1. Choose **Create**\.

#### CLI<a name="SSIS.CreateParamGroup.CLI"></a>

The following procedure creates a parameter group for SQL Server Standard Edition 2016\.

**To create the parameter group**
+ Run one of the following commands\.  
**Example**  

  For Linux, macOS, or Unix:

  ```
  aws rds create-db-parameter-group \
      --db-parameter-group-name ssis-sqlserver-se-13 \
      --db-parameter-group-family "sqlserver-se-13.0" \
      --description "clr enabled parameter group"
  ```

  For Windows:

  ```
  aws rds create-db-parameter-group ^
      --db-parameter-group-name ssis-sqlserver-se-13 ^
      --db-parameter-group-family "sqlserver-se-13.0" ^
      --description "clr enabled parameter group"
  ```

### Modifying the Parameter for SSIS<a name="SSIS.ModifyParam"></a>

Modify the `clr enabled` parameter in the parameter group that corresponds to the SQL Server edition and version of your DB instance\. For SSIS, set the `clr enabled` parameter to 1\.

#### Console<a name="SSIS.ModifyParam.Console"></a>

The following procedure modifies the parameter group that you created for SQL Server Standard Edition 2016\.

**To modify the parameter group**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Parameter groups**\.

1. Choose the parameter group, such as **ssis\-sqlserver\-se\-13**\.

1. Under **Parameters**, filter the parameter list for **clr**\.

1. Choose **clr enabled**\.

1. Choose **Edit parameters**\.

1. From **Values**, choose **1**\.

1. Choose **Save changes**\.

#### CLI<a name="SSIS.ModifyParam.CLI"></a>

The following procedure modifies the parameter group that you created for SQL Server Standard Edition 2016\.

**To modify the parameter group**
+ Run one of the following commands\.  
**Example**  

  For Linux, macOS, or Unix:

  ```
  aws rds modify-db-parameter-group \
      --db-parameter-group-name ssis-sqlserver-se-13 \
      --parameters "ParameterName='clr enabled',ParameterValue=1,ApplyMethod=immediate"
  ```

  For Windows:

  ```
  aws rds modify-db-parameter-group ^
      --db-parameter-group-name ssis-sqlserver-se-13 ^
      --parameters "ParameterName='clr enabled',ParameterValue=1,ApplyMethod=immediate"
  ```

### Associating the Option Group and Parameter Group with Your DB Instance<a name="SSIS.Apply"></a>

To associate the SSIS option group and parameter group with your DB instance, use the AWS Management Console or the AWS CLI 

**Note**  
If you use an existing instance, it must already have an Active Directory domain and AWS Identity and Access Management \(IAM\) role associated with it\. If you create a new instance, specify an existing Active Directory domain and IAM role\. For more information, see [Using Windows Authentication with an Amazon RDS for SQL Server DB Instance](USER_SQLServerWinAuth.md)\.

#### Console<a name="SSIS.Apply.Console"></a>

To finish enabling SSIS, associate your SSIS option group and parameter group with a new or existing DB instance:
+ For a new DB instance, associate them when you launch the instance\. For more information, see [Creating an Amazon RDS DB Instance](USER_CreateDBInstance.md)\.
+ For an existing DB instance, associate them by modifying the instance\. For more information, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md)\.

#### CLI<a name="SSIS.Apply.CLI"></a>

You can associate the SSIS option group and parameter group with a new or existing DB instance\.

**To create an instance with the SSIS option group and parameter group**
+ Specify the same DB engine type and major version as you used when creating the option group\.  
**Example**  

  For Linux, macOS, or Unix:

  ```
  aws rds create-db-instance \
      --db-instance-identifier myssisinstance \
      --db-instance-class db.m5.2xlarge \
      --engine sqlserver-se \
      --engine-version 13.00.5426.0.v1 \
      --allocated-storage 100 \
      --master-user-password secret123 \
      --master-username admin \
      --storage-type gp2 \
      --license-model li \
      --domain-iam-role-name my-directory-iam-role \
      --domain my-domain-id \
      --option-group-name ssis-se-2016 \
      --db-parameter-group-name ssis-sqlserver-se-13
  ```

  For Windows:

  ```
  aws rds create-db-instance ^
      --db-instance-identifier myssisinstance ^
      --db-instance-class db.m5.2xlarge ^
      --engine sqlserver-se ^
      --engine-version 13.00.5426.0.v1 ^
      --allocated-storage 100 ^
      --master-user-password secret123 ^
      --master-username admin ^
      --storage-type gp2 ^
      --license-model li ^
      --domain-iam-role-name my-directory-iam-role ^
      --domain my-domain-id ^
      --option-group-name ssis-se-2016 ^
      --db-parameter-group-name ssis-sqlserver-se-13
  ```

**To modify an instance and associate the SSIS option group and parameter group**
+ Run one of the following commands\.  
**Example**  

  For Linux, macOS, or Unix:

  ```
  aws rds modify-db-instance \
      --db-instance-identifier myssisinstance \
      --option-group-name ssis-se-2016 \
      --db-parameter-group-name ssis-sqlserver-se-13 \
      --apply-immediately
  ```

  For Windows:

  ```
  aws rds modify-db-instance ^
      --db-instance-identifier myssisinstance ^
      --option-group-name ssis-se-2016 ^
      --db-parameter-group-name ssis-sqlserver-se-13 ^
      --apply-immediately
  ```

### Enabling S3 Integration<a name="SSIS.EnableS3"></a>

To download SSIS project \(\.ispac\) files to your host for deployment, use S3 file integration\. For more information, see [Integrating an Amazon RDS for SQL Server DB Instance with Amazon S3](User.SQLServer.Options.S3-integration.md)\.

## Administrative Permissions on SSISDB<a name="SSIS.Permissions"></a>

When the instance is created or modified with the SSIS option, the result is an SSISDB database with the ssis\_admin and ssis\_logreader roles granted to the master user\. The master user has the following privileges in SSISDB:
+ alter on ssis\_admin role
+ alter on ssis\_logreader role
+ alter any user

Because the master user is a SQL\-authenticated user, you can't use the master user for executing SSIS packages\. The master user can use these privileges to create new SSISDB users and add them to the ssis\_admin and ssis\_logreader roles\. Doing this is useful for giving access to your domain users for using SSIS\.

### Setting Up a Windows\-Authenticated User for SSIS<a name="SSIS.Use.Auth"></a>

The master user can use the following code example to set up a Windows\-authenticated login in SSISDB and grant the required procedure permissions\. Doing this grants permissions to the domain user to deploy and run SSIS packages, use S3 file transfer procedures, create credentials, and work with the SQL Server Agent proxy\. For more information, see [Credentials \(Database Engine\)](https://docs.microsoft.com/en-us/sql/relational-databases/security/authentication-access/credentials-database-engine?view=sql-server-ver15) and [Create a SQL Server Agent Proxy](https://docs.microsoft.com/en-us/sql/ssms/agent/create-a-sql-server-agent-proxy?view=sql-server-ver15) in the Microsoft documentation\.

**Note**  
You can grant some or all of the following permissions as needed to Windows\-authenticated users\.

**Example**  

```
USE [SSISDB]
GO
CREATE USER [mydomain\user_name] FOR LOGIN [mydomain\user_name]
ALTER ROLE [ssis_admin] ADD MEMBER [mydomain\user_name]
ALTER ROLE [ssis_logreader] ADD MEMBER [mydomain\user_name]
GO

USE [msdb]
GO
CREATE USER [mydomain\user_name] FOR LOGIN [mydomain\user_name]
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
GRANT EXEC ON msdb.dbo.sp_enum_proxy_for_subsystem TO [mydomain\user_name]  with grant option
GRANT EXEC ON msdb.dbo.rds_sqlagent_proxy TO [mydomain\user_name] WITH GRANT OPTION
ALTER ROLE [SQLAgentUserRole] ADD MEMBER [mydomain\user_name]
GO

USE [master]
GO
GRANT ALTER ANY CREDENTIAL TO [mydomain\user_name]
GO
```

## Deploying an SSIS Project<a name="SSIS.Deploy"></a>

On RDS, you can't deploy SSIS projects directly by using SQL Server Management Studio \(SSMS\) or SSIS procedures\. To download project files from Amazon S3 and then deploy them, use RDS stored procedures\.

To run the stored procedures, log in as any user that you granted permissions for running the stored procedures\. For more information, see [Setting Up a Windows\-Authenticated User for SSIS](#SSIS.Use.Auth)\.

**To deploy the SSIS project**

1. Download the project \(\.ispac\) file\.

   ```
   exec msdb.dbo.rds_download_from_s3
   @s3_arn_of_file='arn:aws:s3:::bucket_name/ssisproject.ispac',
   [@rds_file_path='D:\S3\ssisproject.ispac'],
   [@overwrite_file=1];
   ```

1. Submit the deployment task, making sure of the following:
   + The folder is present in the SSIS catalog\.
   + The project name matches the project name that you used while developing the SSIS project\.

   ```
   exec msdb.dbo.rds_msbi_task
   @task_type='SSIS_DEPLOY_PROJECT',
   @folder_name='DEMO',
   @project_name='ssisproject',
   @file_path='D:\S3\ssisproject.ispac;
   ```

## Monitoring the Status of a Deployment Task<a name="SSIS.Monitor"></a>

To track the status of your deployment task, call the `rds_fn_task_status` function\. It takes two parameters\. The first parameter should always be `NULL` because it doesn't apply to SSIS\. The second parameter accepts a task ID\. 

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
| `task_type` | `SSIS_DEPLOY_PROJECT` | 
| `database_name` | Not applicable to SSIS tasks\. | 
| `% complete` | The progress of the task as a percentage\. | 
| `duration (mins)` | The amount of time spent on the task, in minutes\. | 
| `lifecycle` |  The status of the task\. Possible statuses are the following: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Appendix.SQLServer.Options.SSIS.html)  | 
| `task_info` | Additional information about the task\. If an error occurs during processing, this column contains information about the error\. | 
| `last_updated` | The date and time that the task status was last updated\. | 
| `created_at` | The date and time that the task was created\. | 
| `S3_object_arn` |  Not applicable to SSIS tasks\.  | 
| `overwrite_S3_backup_file` | Not applicable to SSIS tasks\. | 
| `KMS_master_key_arn` |  Not applicable to SSIS tasks\.  | 
| `filepath` |  Not applicable to SSIS tasks\.  | 
| `overwrite_file` |  Not applicable to SSIS tasks\.  | 
| `task_metadata` | Metadata associated with the SSIS task\. | 

## Using SSIS<a name="SSIS.Use"></a>

After deploying the SSIS project into the SSIS catalog, you can run packages directly from SSMS or schedule them by using SQL Server Agent\. You must use a Windows\-authenticated login for executing SSIS packages\. For more information, see [Setting Up a Windows\-Authenticated User for SSIS](#SSIS.Use.Auth)\.

### Setting Database Connection Managers for SSIS Projects<a name="SSIS.Use.ConnMgrs"></a>

When you use a connection manager, you can use these types of authentication:
+ For local database connections, you can use SQL authentication or Windows authentication\. For Windows authentication, use `DB_instance_name.fully_qualified_domain_name` as the server name of the connection string\.

  An example is `myssisinstance.corp-ad.example.com`, where `myssisinstance` is the DB instance name and `corp-ad.example.com` is the fully qualified domain name\.
+ For remote connections, always use SQL authentication\.

### Creating an SSIS Proxy<a name="SSIS.Use.Proxy"></a>

To be able to schedule SSIS packages using SQL Server Agent, create an SSIS credential and an SSIS proxy\. Run these procedures as a Windows\-authenticated user\.

**To create the SSIS credential**
+ Create the credential for the proxy\. To do this, you can use SSMS or the following SQL statement\.

  ```
  USE [master]
  GO
  CREATE CREDENTIAL [SSIS_Credential] WITH IDENTITY = N'mydomain\user_name', SECRET = N'mysecret'
  GO
  ```
**Note**  
`IDENTITY` must be a domain\-authenticated login\. Replace `mysecret` with the password for the domain\-authenticated login\.  
Whenever the SSISDB primary host is changed, alter the SSIS proxy credentials to allow the new host to access them\.

**To create the SSIS proxy**

1. Use the following SQL statement to create the proxy\.

   ```
   USE [msdb]
   GO
   EXEC msdb.dbo.sp_add_proxy @proxy_name=N'SSIS_Proxy',@credential_name=N'SSIS_Credential',@description=N''
   GO
   ```

1. Use the following SQL statement to grant access to the proxy to other users\.

   ```
   USE [msdb]
   GO
   EXEC msdb.dbo.sp_grant_login_to_proxy @proxy_name=N'SSIS_Proxy',@login_name=N'mydomain\user_name'
   GO
   ```

1. Use the following SQL statement to give the SSIS subsystem access to the proxy\.

   ```
   USE [msdb]
   GO
   EXEC msdb.dbo.rds_sqlagent_proxy @task_type='GRANT_SUBSYSTEM_ACCESS',@proxy_name='SSIS_Proxy',@proxy_subsystem='SSIS'
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

### Scheduling an SSIS Package Using SQL Server Agent<a name="SSIS.Use.Schedule"></a>

After you create the credential and proxy and grant SSIS access to the proxy, you can create a SQL Server Agent job to schedule the SSIS package\.

**To schedule the SSIS package**
+ You can use SSMS or T\-SQL for creating the SQL Server Agent job\. The following example uses T\-SQL\.

  ```
  USE [msdb]
  GO
  DECLARE @jobId BINARY(16)
  EXEC msdb.dbo.sp_add_job @job_name=N'MYSSISJob',
  @enabled=1,
  @notify_level_eventlog=0,
  @notify_level_email=2,
  @notify_level_page=2,
  @delete_level=0,
  @category_name=N'[Uncategorized (Local)]',
  @job_id = @jobId OUTPUT
  GO
  EXEC msdb.dbo.sp_add_jobserver @job_name=N'MYSSISJob',@server_name=N'(local)'
  GO
  EXEC msdb.dbo.sp_add_jobstep @job_name=N'MYSSISJob',@step_name=N'ExecuteSSISPackage',
  @step_id=1,
  @cmdexec_success_code=0,
  @on_success_action=1,
  @on_fail_action=2,
  @retry_attempts=0,
  @retry_interval=0,
  @os_run_priority=0,
  @subsystem=N'SSIS',
  @command=N'/ISSERVER "\"\SSISDB\MySSISFolder\MySSISProject\MySSISPackage.dtsx\"" /SERVER "\"my-rds-ssis-instance.corp-ad.company.com/\"" 
  /Par "\"$ServerOption::LOGGING_LEVEL(Int16)\"";1 /Par "\"$ServerOption::SYNCHRONIZED(Boolean)\"";True /CALLERINFO SQLAGENT /REPORTING E',
  @database_name=N'master',
  @flags=0,
  @proxy_name=N'SSIS_Proxy'
  GO
  ```

### Revoking SSIS Access from the Proxy<a name="SSIS.Use.Revoke"></a>

You can revoke access to the SSIS subsystem and delete the SSIS proxy using the following stored procedures\.

**To revoke access and delete the proxy**

1. Revoke subsystem access\.

   ```
   USE [msdb]
   GO
   EXEC msdb.dbo.rds_sqlagent_proxy @task_type='REVOKE_SUBSYSTEM_ACCESS',@proxy_name='SSIS_Proxy',@proxy_subsystem='SSIS'
   GO
   ```

1. Revoke the grants on the proxy\.

   ```
   USE [msdb]
   GO
   EXEC msdb.dbo.sp_revoke_login_from_proxy @proxy_name=N'SSIS_Proxy',@name=N'mydomain\user_name'
   GO
   ```

1. Delete the proxy\.

   ```
   USE [msdb]
   GO
   EXEC dbo.sp_delete_proxy @proxy_name = N'SSIS_Proxy'
   GO
   ```

## Disabling SSIS<a name="SSIS.Disable"></a>

To disable SSIS, remove the `SSIS` option from its option group\.

**Important**  
Removing the option doesn't delete the SSISDB database, so you can safely remove the option without losing the SSIS projects\.  
You can re\-enable the `SSIS` option after removal to reuse the SSIS projects that were previously deployed to the SSIS catalog\.

### Console<a name="SSIS.Disable.Console"></a>

The following procedure removes the `SSIS` option\.

**To remove the SSIS option from its option group**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Option groups**\.

1. Choose the option group with the `SSIS` option \(`ssis-se-2016` in the previous examples\)\.

1. Choose **Delete option**\.

1. Under **Deletion options**, choose **SSIS** for **Options to delete**\.

1. Under **Apply immediately**, choose **Yes** to delete the option immediately, or **No** to delete it at the next maintenance window\.

1. Choose **Delete**\.

### CLI<a name="SSIS.Disable.CLI"></a>

The following procedure removes the `SSIS` option\.

**To remove the SSIS option from its option group**
+ Run one of the following commands\.  
**Example**  

  For Linux, macOS, or Unix:

  ```
  aws rds remove-option-from-option-group \
      --option-group-name ssis-se-2016 \
      --options SSIS \
      --apply-immediately
  ```

  For Windows:

  ```
  aws rds remove-option-from-option-group ^
      --option-group-name ssis-se-2016 ^
      --options SSIS ^
      --apply-immediately
  ```

## Dropping the SSISDB Database<a name="SSIS.Drop"></a>

After removing the SSIS option, the SSISDB database isn't deleted\. To drop the SSISDB database, use the `rds_drop_ssis_database` stored procedure after removing the SSIS option\.

**To drop the SSIS database**
+ Use the following stored procedure\.

  ```
  USE [msdb]
  GO
  EXEC dbo.rds_drop_ssis_database
  GO
  ```

After dropping the SSISDB database, if you re\-enable the SSIS option you get a fresh SSISDB catalog\.