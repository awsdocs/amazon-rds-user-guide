# Support for SQL Server Reporting Services in SQL Server<a name="Appendix.SQLServer.Options.SSRS"></a>

Microsoft SQL Server Reporting Services \(SSRS\) is a server\-based application used for report generation and distribution\. It's part of a suite of SQL Server services that also includes SQL Server Analysis Services \(SSAS\) and SQL Server Integration Services \(SSIS\)\. SSRS is a service built on top of SQL Server\. You can use it to collect data from various data sources and present it in a way that's easily understandable and ready for analysis\.

Amazon RDS for SQL Server supports running SSRS directly on RDS DB instances\. You can enable SSRS for existing or new DB instances\.

RDS supports SSRS for SQL Server Standard and Enterprise Editions on the following versions:
+ SQL Server 2017, version 14\.00\.3223\.3\.v1 and later
+ SQL Server 2016, version 13\.00\.5820\.21\.v1 and later

## Limitations and Recommendations<a name="SSRS.Limitations"></a>

The following limitations and recommendations apply to running SSRS on RDS for SQL Server:
+ Instances must use AWS Managed Microsoft AD for SSRS web portal and web server authentication\.
+ Importing and restoring report server databases from other instances of SSRS isn't supported\.

  Make sure to use the databases that are created when the `SSRS` option is added to the RDS DB instance\. For more information, see [Report Server Databases](#SSRS.DBs)\.
+ You can't configure SSRS to listen on the default SSL port \(443\)\. The allowed values are 1150–49511, except 1234, 1434, 3260, 3343, 3389, and 47001\.
+ Subscriptions through email or a Microsoft Windows file share aren't supported\.
+ Using Reporting Services Configuration Manager isn't supported\.
+ Creating and modifying roles isn't supported\.
+ Modifying report server properties isn't supported\.
+ System administrator and system user roles aren't granted\.
+ You can't edit system\-level role assignments through the web portal\.

## Enabling SSRS<a name="SSRS.Enabling"></a>

Use the following process to enable SSRS on your DB instance:

1. Create a new option group, or choose an existing option group\.

1. Add the `SSRS` option to the option group\.

1. Associate the option group with the DB instance\.

1. Allow inbound access to the virtual private cloud \(VPC\) security group for the SSRS listener port\.

### Creating an Option Group for SSRS<a name="SSRS.OptionGroup"></a>

To work with SSRS, create an option group that corresponds to the SQL Server engine and version of the DB instance that you plan to use\. To do this, use the AWS Management Console or the AWS CLI\. 

**Note**  
You can also use an existing option group if it's for the correct SQL Server engine and version\.

#### Console<a name="OptionGroup.SSRS.Console"></a>

The following procedure creates an option group for SQL Server Standard Edition 2017\.

**To create the option group**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Option groups**\.

1. Choose **Create group**\.

1. In the **Create option group** pane, do the following:

   1. For **Name**, enter a name for the option group that is unique within your AWS account, such as **ssrs\-se\-2017**\. The name can contain only letters, digits, and hyphens\.

   1. For **Description**, enter a brief description of the option group, such as **SSRS option group for SQL Server SE 2017**\. The description is used for display purposes\.

   1. For **Engine**, choose **sqlserver\-se**\.

   1. For **Major engine version**, choose **14\.00**\.

1. Choose **Create**\.

#### CLI<a name="OptionGroup.SSRS.CLI"></a>

The following procedure creates an option group for SQL Server Standard Edition 2017\.

**To create the option group**
+ Run one of the following commands\.

**Example**  
For Linux, macOS, or Unix:  

```
aws rds create-option-group \
    --option-group-name ssrs-se-2017 \
    --engine-name sqlserver-se \
    --major-engine-version 14.00 \
    --option-group-description "SSRS option group for SQL Server SE 2017"
```
For Windows:  

```
aws rds create-option-group ^
    --option-group-name ssrs-se-2017 ^
    --engine-name sqlserver-se ^
    --major-engine-version 14.00 ^
    --option-group-description "SSRS option group for SQL Server SE 2017"
```

### Adding the SSRS Option to Your Option Group<a name="Appendix.SQLServer.Options.SSAS.Add"></a>

Next, use the AWS Management Console or the AWS CLI to add the `SSRS` option to your option group\.

#### Console<a name="Options.SSAS.Add.Console"></a>

**To add the SSRS option**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Option groups**\.

1. Choose the option group that you just created\.

1. Choose **Add option**\.

1. Under **Option details**, choose **SSRS** for **Option name**\.

1. Under **Option settings**, do the following:

   1. Enter the port for the SSRS service to listen on\. The default is 8443\. For a list of allowed values, see [Limitations and Recommendations](#SSRS.Limitations)\.

   1. Enter a value for **Max memory**\.

      **Max memory** specifies the upper threshold above which no new memory allocation requests are granted to report server applications\. The number is a percentage of the total memory of the DB instance\. The allowed values are 10–80\.

1. For **Security groups**, choose the VPC security group to associate with the option\. Use the same security group that is associated with your DB instance\.

1. Under **Scheduling**, choose whether to add the option immediately or at the next maintenance window\.

1. Choose **Add option**\.

#### CLI<a name="Options.SSAS.Add.CLI"></a>

**To add the SSRS option**

1. Create a JSON file, for example `ssrs-option.json`, with the following parameters:
   + `OptionGroupName` – The name of option group that you created or chose previously \(`ssrs-se-2017` in the following example\)\.
   + `Port` – The port for the SSRS service to listen on\. The default is 8443\. For a list of allowed values, see [Limitations and Recommendations](#SSRS.Limitations)\.
   + `VpcSecurityGroupMemberships` – VPC security group memberships for your RDS DB instance\.
   + `MAX_MEMORY` – The upper threshold above which no new memory allocation requests are granted to report server applications\. The number is a percentage of the total memory of the DB instance\. The allowed values are 10–80\.

   ```
   {
   "OptionGroupName": "ssrs-se-2017",
   "OptionsToInclude": [
   	{
   	"OptionName": "SSRS",
   	"Port": 8443,
   	"VpcSecurityGroupMemberships": ["sg-0abcdef123"],
   	"OptionSettings": [{"Name": "MAX_MEMORY","Value": "60"}]
   	}],
   "ApplyImmediately": true
   }
   ```

1. Add the `SSRS` option to the option group\.  
**Example**  

   For Linux, macOS, or Unix:

   ```
   aws rds add-option-to-option-group \
       --cli-input-json file://ssrs-option.json \
       --apply-immediately
   ```

   For Windows:

   ```
   aws rds add-option-to-option-group ^
       --cli-input-json file://ssrs-option.json ^
       --apply-immediately
   ```

### Associating Your Option Group with Your DB Instance<a name="SSRS.Apply"></a>

Use the AWS Management Console or the AWS CLI to associate your option group with your DB instance\.

If you use an existing DB instance, it must already have an Active Directory domain and AWS Identity and Access Management \(IAM\) role associated with it\. If you create a new instance, specify an existing Active Directory domain and IAM role\. For more information, see [Using Windows Authentication with an Amazon RDS for SQL Server DB Instance](USER_SQLServerWinAuth.md)\.

#### Console<a name="Apply.SSRS.Console"></a>

You can associate your option group with a new or existing DB instance:
+ For a new DB instance, associate the option group when you launch the instance\. For more information, see [Creating an Amazon RDS DB Instance](USER_CreateDBInstance.md)\.
+ For an existing DB instance, modify the instance and associate the new option group\. For more information, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md)\.

#### CLI<a name="Apply.SSRS.CLI"></a>

You can associate your option group with a new or existing DB instance\.

**To create a DB instance that uses your option group**
+ Specify the same DB engine type and major version as you used when creating the option group\.  
**Example**  

  For Linux, macOS, or Unix:

  ```
  aws rds create-db-instance \
      --db-instance-identifier myssrsinstance \
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
      --option-group-name ssrs-se-2017
  ```

  For Windows:

  ```
  aws rds create-db-instance ^
      --db-instance-identifier myssrsinstance ^
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
      --option-group-name ssrs-se-2017
  ```

**To modify a DB instance to use your option group**
+ Run one of the following commands\.  
**Example**  

  For Linux, macOS, or Unix:

  ```
  aws rds modify-db-instance \
      --db-instance-identifier myssrsinstance \
      --option-group-name ssrs-se-2017 \
      --apply-immediately
  ```

  For Windows:

  ```
  aws rds modify-db-instance ^
      --db-instance-identifier myssrsinstance ^
      --option-group-name ssrs-se-2017 ^
      --apply-immediately
  ```

### Allowing Inbound Access to Your VPC Security Group<a name="SSRS.Inbound"></a>

To allow inbound access to the VPC security group associated with your DB instance, create an inbound rule for the specified SSRS listener port\. For more information about setting up security groups, see [Provide Access to Your DB Instance in Your VPC by Creating a Security Group](CHAP_SettingUp.md#CHAP_SettingUp.SecurityGroup)\.

## Report Server Databases<a name="SSRS.DBs"></a>

When your DB instance is associated with the SSRS option, two new databases are created on your DB instance: rdsadmin\_ReportServer and rdsadmin\_ReportServerTempDB\. These databases act as the ReportServer and ReportServerTempDB databases\. SSRS stores its data in the ReportServer database and caches its data in the ReportServerTempDB database\. RDS owns and manages these databases, so database operations on them such as ALTER and DROP aren't permitted\.

## Accessing the SSRS Web Portal<a name="SSRS.Access"></a>

Use the following process to access the SSRS web portal:

1. Enable Secure Sockets Layer \(SSL\)\.

1. Grant access to domain users\.

1. Access the web portal using a browser and the domain user credentials\.

### Enabling SSL on RDS<a name="SSRS.Access.SSL"></a>

SSRS uses the HTTPS SSL protocol for its connections\. To work with this protocol, import an SSL certificate into the Microsoft Windows operating system on your client computer\.

For more information on SSL certificates, see [Using SSL/TLS to Encrypt a Connection to a DB Instance](UsingWithRDS.SSL.md)\. For more information about using SSL with SQL Server, see [Using SSL with a Microsoft SQL Server DB Instance](SQLServer.Concepts.General.SSL.Using.md)\.

### Granting Access to Domain Users<a name="SSRS.Access.Grant"></a>

In a new SSRS activation, there are no role assignments in SSRS\. To give a domain user or user group access to the web portal, RDS provides a stored procedure\.

**To grant access to a domain user on the web portal**
+ Use the following stored procedure\.

  ```
  exec msdb.dbo.rds_msbi_task
  @task_type='SSRS_GRANT_PORTAL_PERMISSION',
  @ssrs_group_or_username=N'AD_domain\user';
  ```

The domain user or user group is granted the `RDS_SSRS_ROLE` system role\. This role has the following system\-level tasks granted to it:
+ Execute report definitions
+ Manage jobs
+ Manage shared schedules
+ View shared schedules

The item\-level role of `Content Manager` on the root folder is also granted\.

### Accessing the Web Portal<a name="SSRS.Access"></a>

After the `SSRS_GRANT_PORTAL_PERMISSION` task finishes successfully, you have access to the portal using a web browser\. The web portal URL has the following format\.

```
https://rds_endpoint:port/Reports
```

In this format, the following applies:
+ *`rds_endpoint`* – The endpoint for the RDS DB instance that you're using with SSRS\.

  You can find the endpoint on the **Connectivity & security** tab for your DB instance\. For more information, see [Connecting to a DB Instance Running the Microsoft SQL Server Database Engine](USER_ConnectToMicrosoftSQLServerInstance.md)\.
+ `port` – The listener port for SSRS that you set in the `SSRS` option\.

**To access the web portal**

1. Enter the web portal URL in your browser\.

   ```
   https://myssrsinstance.cg034itsfake.us-east-1.rds.amazonaws.com:8443/Reports
   ```

1. Log in with the credentials for a domain user that you granted access with the `SSRS_GRANT_PORTAL_PERMISSION` task\.

## Deploying Reports to SSRS<a name="SSRS.Deploy"></a>

After you have access to the web portal, you can deploy reports to it\. You can use the Upload tool in the web portal to upload reports, or deploy directly from [SQL Server Data Tools \(SSDT\)](https://docs.microsoft.com/en-us/sql/ssdt/download-sql-server-data-tools-ssdt)\. When deploying from SSDT, ensure the following:
+ The user who launched SSDT has access to the SSRS web portal\.
+ The `TargetServerURL` value in the SSRS project properties is set to the HTTPS endpoint of the RDS DB instance suffixed with `ReportServer`, for example:

  ```
  https://myssrsinstance.cg034itsfake.us-east-1.rds.amazonaws.com:8443/ReportServer
  ```

## Revoking System\-Level Permissions<a name="SSRS.Access.Revoke"></a>

The `RDS_SSRS_ROLE` system role doesn't have sufficient permissions to delete system\-level role assignments\. To remove a user or user group from `RDS_SSRS_ROLE`, use the same stored procedure that you used to grant the role but use the `SSRS_REVOKE_PORTAL_PERMISSION` task type\.

**To revoke access from a domain user for the web portal**
+ Use the following stored procedure\.

  ```
  exec msdb.dbo.rds_msbi_task
  @task_type='SSRS_REVOKE_PORTAL_PERMISSION',
  @ssrs_group_or_username=N'AD_domain\user';
  ```

Doing this deletes the user from the `RDS_SSRS_ROLE` system role\. It also deletes the user from the `Content Manager` item\-level role if the user has it\.

## Monitoring the Status of a Task<a name="SSRS.Monitor"></a>

To track the status of your granting or revoking task, call the `rds_fn_task_status` function\. It takes two parameters\. The first parameter should always be `NULL` because it doesn't apply to SSRS\. The second parameter accepts a task ID\. 

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
| `task_type` | For SSRS, tasks can have the following task types: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Appendix.SQLServer.Options.SSRS.html)  | 
| `database_name` | Not applicable to SSRS tasks\. | 
| `% complete` | The progress of the task as a percentage\. | 
| `duration (mins)` | The amount of time spent on the task, in minutes\. | 
| `lifecycle` |  The status of the task\. Possible statuses are the following: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Appendix.SQLServer.Options.SSRS.html)  | 
| `task_info` | Additional information about the task\. If an error occurs during processing, this column contains information about the error\.  | 
| `last_updated` | The date and time that the task status was last updated\.  | 
| `created_at` | The date and time that the task was created\. | 
| `S3_object_arn` |  Not applicable to SSRS tasks\.  | 
| `overwrite_S3_backup_file` | Not applicable to SSRS tasks\. | 
| `KMS_master_key_arn` |  Not applicable to SSRS tasks\.  | 
| `filepath` |  Not applicable to SSRS tasks\.  | 
| `overwrite_file` |  Not applicable to SSRS tasks\.  | 
| `task_metadata` | Metadata associated with the SSRS task\. | 

## Disabling SSRS<a name="SSRS.Disable"></a>

To disable SSRS, remove the `SSRS` option from its option group\. Removing the option doesn't delete the SSRS databases\. For more information, see [Deleting the SSRS Databases](#SSRS.Drop)\.

You can re\-enable SSRS by adding back the `SSRS` option\. If you have also deleted the SSRS databases, re\-enabling SSRS on the same DB instance creates new report server databases\.

### Console<a name="SSRS.Disable.Console"></a>

**To remove the SSRS option from its option group**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Option groups**\.

1. Choose the option group with the `SSRS` option \(`ssrs-se-2017` in the previous examples\)\.

1. Choose **Delete option**\.

1. Under **Deletion options**, choose **SSRS** for **Options to delete**\.

1. Under **Apply immediately**, choose **Yes** to delete the option immediately, or **No** to delete it at the next maintenance window\.

1. Choose **Delete**\.

### CLI<a name="SSRS.Disable.CLI"></a>

**To remove the SSRS option from its option group**
+ Run one of the following commands\.  
**Example**  

  For Linux, macOS, or Unix:

  ```
  aws rds remove-option-from-option-group \
      --option-group-name ssrs-se-2017 \
      --options SSRS \
      --apply-immediately
  ```

  For Windows:

  ```
  aws rds remove-option-from-option-group ^
      --option-group-name ssrs-se-2017 ^
      --options SSRS ^
      --apply-immediately
  ```

## Deleting the SSRS Databases<a name="SSRS.Drop"></a>

Removing the `SSRS` option doesn't delete the report server databases\. To delete them, use the following stored procedure\. 

To delete the report server databases, be sure to remove the `SSRS` option first\.

**To delete the SSRS databases**
+ Use the following stored procedure\.

  ```
  exec msdb.dbo.rds_drop_ssrs_databases
  ```