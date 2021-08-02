# Using Database Mail on Amazon RDS for SQL Server<a name="SQLServer.DBMail"></a>

You can use Database Mail to send email messages to users from your Amazon RDS on SQL Server database instance\. The messages can contain files and query results\. Database Mail includes the following components:
+ **Configuration and security objects** – These objects create profiles and accounts, and are stored in the `msdb` database\.
+ **Messaging objects** – These objects include the [sp\_send\_dbmail](https://docs.microsoft.com/en-us/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) stored procedure used to send messages, and data structures that hold information about messages\. They're stored in the `msdb` database\.
+ **Logging and auditing objects** – Database Mail writes logging information to the `msdb` database and the Microsoft Windows application event log\.
+ **Database Mail executable** – `DatabaseMail.exe` reads from a queue in the `msdb` database and sends email messages\.

RDS supports Database Mail for all SQL Server versions on the Web, Standard, and Enterprise Editions\.

## Limitations<a name="SQLServer.DBMail.Limitations"></a>

The following limitations apply to using Database Mail on your SQL Server DB instance:
+ Database Mail isn't supported for SQL Server Express Edition\.
+ Modifying Database Mail configuration parameters isn't supported\. To see the preset \(default\) values, use the [sysmail\_help\_configure\_sp](https://docs.microsoft.com/en-us/sql/relational-databases/system-stored-procedures/sysmail-help-configure-sp-transact-sql) stored procedure\.
+ File attachments aren't fully supported\. For more information, see [Working with file attachments](#SQLServer.DBMail.Files)\.
+ The maximum file attachment size is 1 MB\.
+ Database Mail requires additional configuration on Multi\-AZ DB instances\. For more information, see [Considerations for Multi\-AZ deployments](#SQLServer.DBMail.MAZ)\.
+ Configuring SQL Server Agent to send email messages to predefined operators isn't supported\.

## Enabling Database Mail<a name="SQLServer.DBMail.Enable"></a>

Use the following process to enable Database Mail for your DB instance:

1. Create a new parameter group\.

1. Modify the parameter group to set the `database mail xps` parameter to 1\.

1. Associate the parameter group with the DB instance\.

### Creating the parameter group for Database Mail<a name="DBMail.CreateParamGroup"></a>

Create a parameter group for the `database mail xps` parameter that corresponds to the SQL Server edition and version of your DB instance\.

**Note**  
You can also modify an existing parameter group\. Follow the procedure in [Modifying the parameter that enables Database Mail](#DBMail.ModifyParamGroup)\.

#### Console<a name="DBMail.CreateParamGroup.Console"></a>

The following example creates a parameter group for SQL Server Standard Edition 2016\.

**To create the parameter group**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Parameter groups**\.

1. Choose **Create parameter group**\.

1. In the **Create parameter group** pane, do the following:

   1. For **Parameter group family**, choose **sqlserver\-se\-13\.0**\.

   1. For **Group name**, enter an identifier for the parameter group, such as **dbmail\-sqlserver\-se\-13**\.

   1. For **Description**, enter **Database Mail XPs**\.

1. Choose **Create**\.

#### CLI<a name="DBMail.CreateParamGroup.CLI"></a>

The following example creates a parameter group for SQL Server Standard Edition 2016\.

**To create the parameter group**
+ Use one of the following commands\.  
**Example**  

  For Linux, macOS, or Unix:

  ```
  aws rds create-db-parameter-group \
      --db-parameter-group-name dbmail-sqlserver-se-13 \
      --db-parameter-group-family "sqlserver-se-13.0" \
      --description "Database Mail XPs"
  ```

  For Windows:

  ```
  aws rds create-db-parameter-group ^
      --db-parameter-group-name dbmail-sqlserver-se-13 ^
      --db-parameter-group-family "sqlserver-se-13.0" ^
      --description "Database Mail XPs"
  ```

### Modifying the parameter that enables Database Mail<a name="DBMail.ModifyParamGroup"></a>

Modify the `database mail xps` parameter in the parameter group that corresponds to the SQL Server edition and version of your DB instance\.

To enable Database Mail, set the `database mail xps` parameter to 1\.

#### Console<a name="DBMail.ModifyParamGroup.Console"></a>

The following example modifies the parameter group that you created for SQL Server Standard Edition 2016\.

**To modify the parameter group**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Parameter groups**\.

1. Choose the parameter group, such as **dbmail\-sqlserver\-se\-13**\.

1. Under **Parameters**, filter the parameter list for **mail**\.

1. Choose **database mail xps**\.

1. Choose **Edit parameters**\.

1. Enter **1**\.

1. Choose **Save changes**\.

#### CLI<a name="DBMail.ModifyParamGroup.CLI"></a>

The following example modifies the parameter group that you created for SQL Server Standard Edition 2016\.

**To modify the parameter group**
+ Use one of the following commands\.  
**Example**  

  For Linux, macOS, or Unix:

  ```
  aws rds modify-db-parameter-group \
      --db-parameter-group-name dbmail-sqlserver-se-13 \
      --parameters "ParameterName='database mail xps',ParameterValue=1,ApplyMethod=immediate"
  ```

  For Windows:

  ```
  aws rds modify-db-parameter-group ^
      --db-parameter-group-name dbmail-sqlserver-se-13 ^
      --parameters "ParameterName='database mail xps',ParameterValue=1,ApplyMethod=immediate"
  ```

### Associating the parameter group with the DB instance<a name="DBMail.AssocParamGroup"></a>

You can use the AWS Management Console or the AWS CLI to associate the Database Mail parameter group with the DB instance\.

#### Console<a name="DBMail.AssocParamGroup.Console"></a>

You can associate the Database Mail parameter group with a new or existing DB instance\.
+ For a new DB instance, associate it when you launch the instance\. For more information, see [Creating an Amazon RDS DB instance](USER_CreateDBInstance.md)\.
+ For an existing DB instance, associate it by modifying the instance\. For more information, see [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)\.

#### CLI<a name="DBMail.AssocParamGroup.CLI"></a>

You can associate the Database Mail parameter group with a new or existing DB instance\.

**To create a DB instance with the Database Mail parameter group**
+ Specify the same DB engine type and major version as you used when creating the parameter group\.  
**Example**  

  For Linux, macOS, or Unix:

  ```
  aws rds create-db-instance \
      --db-instance-identifier mydbinstance \
      --db-instance-class db.m5.2xlarge \
      --engine sqlserver-se \
      --engine-version 13.00.5426.0.v1 \
      --allocated-storage 100 \
      --master-user-password secret123 \
      --master-username admin \
      --storage-type gp2 \
      --license-model li
      --db-parameter-group-name dbmail-sqlserver-se-13
  ```

  For Windows:

  ```
  aws rds create-db-instance ^
      --db-instance-identifier mydbinstance ^
      --db-instance-class db.m5.2xlarge ^
      --engine sqlserver-se ^
      --engine-version 13.00.5426.0.v1 ^
      --allocated-storage 100 ^
      --master-user-password secret123 ^
      --master-username admin ^
      --storage-type gp2 ^
      --license-model li ^
      --db-parameter-group-name dbmail-sqlserver-se-13
  ```

**To modify a DB instance and associate the Database Mail parameter group**
+ Use one of the following commands\.  
**Example**  

  For Linux, macOS, or Unix:

  ```
  aws rds modify-db-instance \
      --db-instance-identifier mydbinstance \
      --db-parameter-group-name dbmail-sqlserver-se-13 \
      --apply-immediately
  ```

  For Windows:

  ```
  aws rds modify-db-instance ^
      --db-instance-identifier mydbinstance ^
      --db-parameter-group-name dbmail-sqlserver-se-13 ^
      --apply-immediately
  ```

## Configuring Database Mail<a name="SQLServer.DBMail.Configure"></a>

You perform the following tasks to configure Database Mail:

1. Create the Database Mail profile\.

1. Create the Database Mail account\.

1. Add the Database Mail account to the Database Mail profile\.

1. Add users to the Database Mail profile\.

**Note**  
To configure Database Mail, make sure that you have `execute` permission on the stored procedures in the `msdb` database\.

### Creating the Database Mail profile<a name="SQLServer.DBMail.Configure.Profile"></a>

To create the Database Mail profile, you use the [sysmail\_add\_profile\_sp](https://docs.microsoft.com/en-us/sql/relational-databases/system-stored-procedures/sysmail-add-profile-sp-transact-sql) stored procedure\. The following example creates a profile named `Notifications`\.

**To create the profile**
+ Use the following SQL statement\.

  ```
  USE msdb
  GO
  
  EXECUTE msdb.dbo.sysmail_add_profile_sp  
      @profile_name         = 'Notifications',  
      @description          = 'Profile used for sending outgoing notifications using Amazon SES.';
  GO
  ```

### Creating the Database Mail account<a name="SQLServer.DBMail.Configure.Account"></a>

To create the Database Mail account, you use the [sysmail\_add\_account\_sp](https://docs.microsoft.com/en-us/sql/relational-databases/system-stored-procedures/sysmail-add-account-sp-transact-sql) stored procedure\. The following example creates an account named `SES` that uses Amazon Simple Email Service\.

Using Amazon SES requires the following parameters:
+ `@email_address` – An Amazon SES verified identity\. For more information, see [Verified identities in Amazon SES](https://docs.aws.amazon.com/ses/latest/dg/verify-addresses-and-domains.html)\.
+ `@mailserver_name` – An Amazon SES SMTP endpoint\. For more information, see [Connecting to an Amazon SES SMTP endpoint](https://docs.aws.amazon.com/ses/latest/dg/smtp-connect.html)\.
+ `@username` – An Amazon SES SMTP user name\. For more information, see [Obtaining Amazon SES SMTP credentials](https://docs.aws.amazon.com/ses/latest/dg/smtp-credentials.html)\.

  Don't use an AWS Identity and Access Management user name\.
+ `@password` – An Amazon SES SMTP password\. For more information, see [Obtaining Amazon SES SMTP credentials](https://docs.aws.amazon.com/ses/latest/dg/smtp-credentials.html)\.

**To create the account**
+ Use the following SQL statement\.

  ```
  USE msdb
  GO
  
  EXECUTE msdb.dbo.sysmail_add_account_sp
      @account_name        = 'SES',
      @description         = 'Mail account for sending outgoing notifications.',
      @email_address       = 'nobody@example.com',
      @display_name        = 'Automated Mailer',
      @mailserver_name     = 'email-smtp.us-west-2.amazonaws.com',
      @port                = 587,
      @enable_ssl          = 1,
      @username            = 'Smtp_Username',
      @password            = 'Smtp_Password';
  GO
  ```

### Adding the Database Mail account to the Database Mail profile<a name="SQLServer.DBMail.Configure.AddAccount"></a>

To add the Database Mail account to the Database Mail profile, you use the [sysmail\_add\_profileaccount\_sp](https://docs.microsoft.com/en-us/sql/relational-databases/system-stored-procedures/sysmail-add-profileaccount-sp-transact-sql) stored procedure\. The following example adds the `SES` account to the `Notifications` profile\.

**To add the account to the profile**
+ Use the following SQL statement\.

  ```
  USE msdb
  GO
  
  EXECUTE msdb.dbo.sysmail_add_profileaccount_sp
      @profile_name        = 'Notifications',
      @account_name        = 'SES',
      @sequence_number     = 1;
  GO
  ```

### Adding users to the Database Mail profile<a name="SQLServer.DBMail.Configure.AddUser"></a>

To grant permission for an `msdb` database principal to use a Database Mail profile, you use the [sysmail\_add\_principalprofile\_sp](https://docs.microsoft.com/en-us/sql/relational-databases/system-stored-procedures/sysmail-add-principalprofile-sp-transact-sql) stored procedure\. A *principal* is an entity that can request SQL Server resources\. The database principal must map to a SQL Server authentication user, a Windows Authentication user, or a Windows Authentication group\.

The following example grants public access to the `Notifications` profile\.

**To add a user to the profile**
+ Use the following SQL statement\.

  ```
  USE msdb
  GO
  
  EXECUTE msdb.dbo.sysmail_add_principalprofile_sp  
      @profile_name       = 'Notifications',  
      @principal_name     = 'public',  
      @is_default         = 1;
  GO
  ```

## Amazon RDS stored procedures and functions for Database Mail<a name="SQLServer.DBMail.StoredProc"></a>

Microsoft provides [stored procedures](https://docs.microsoft.com/en-us/sql/relational-databases/system-stored-procedures/database-mail-stored-procedures-transact-sql) for using Database Mail, such as creating, listing, updating, and deleting accounts and profiles\. In addition, RDS provides the stored procedures and functions for Database Mail shown in the following table\.


| Procedure/Function | Description | 
| --- | --- | 
| rds\_fn\_sysmail\_allitems | Shows sent messages, including those submitted by other users\. | 
| rds\_fn\_sysmail\_event\_log | Shows events, including those for messages submitted by other users\. | 
| rds\_fn\_sysmail\_mailattachments | Shows attachments, including those to messages submitted by other users\. | 
| rds\_sysmail\_control | Starts and stops the mail queue \(DatabaseMail\.exe process\)\. | 
| rds\_sysmail\_delete\_mailitems\_sp | Deletes email messages sent by all users from the Database Mail internal tables\. | 

## Sending email messages using Database Mail<a name="SQLServer.DBMail.Send"></a>

You use the [sp\_send\_dbmail](https://docs.microsoft.com/en-us/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) stored procedure to send email messages using Database Mail\.

### Usage<a name="SQLServer.DBMail.Send.Usage"></a>

```
EXEC msdb.dbo.sp_send_dbmail
@profile_name = 'profile_name',
@recipients = 'recipient1@example.com[; recipient2; ... recipientn]',
@subject = 'subject',
@body = 'message_body',
[@body_format = 'HTML'],
[@file_attachments = 'file_path1; file_path2; ... file_pathn'],
[@query = 'SQL_query'],
[@attach_query_result_as_file = 0|1]';
```

The following parameters are required:
+ `@profile_name` – The name of the Database Mail profile from which to send the message\.
+ `@recipients` – The semicolon\-delimited list of email addresses to which to send the message\.
+ `@subject` – The subject of the message\.
+ `@body` – The body of the message\. You can also use a declared variable as the body\.

The following parameters are optional:
+ `@body_format` – This parameter is used with a declared variable to send email in HTML format\.
+ `@file_attachments` – The semicolon\-delimited list of message attachments\. File paths must be absolute paths\.
+ `@query` – A SQL query to run\. The query results can be attached as a file or included in the body of the message\.
+ `@attach_query_result_as_file` – Whether to attach the query result as a file\. Set to 0 for no, 1 for yes\. The default is 0\.

### Examples<a name="SQLServer.DBMail.Send.Examples"></a>

The following examples demonstrate how to send email messages\.

**Example of sending a message to a single recipient**  

```
USE msdb
GO

EXEC msdb.dbo.sp_send_dbmail
     @profile_name       = 'Notifications',
     @recipients         = 'nobody@example.com',
     @subject            = 'Automated DBMail message - 1',
     @body               = 'Database Mail configuration was successful.';
GO
```

**Example of sending a message to multiple recipients**  

```
USE msdb
GO

EXEC msdb.dbo.sp_send_dbmail
     @profile_name       = 'Notifications',
     @recipients         = 'recipient1@example.com;recipient2@example.com',
     @subject            = 'Automated DBMail message - 2',
     @body               = 'This is a message.';
GO
```

**Example of sending a SQL query result as a file attachment**  

```
USE msdb
GO

EXEC msdb.dbo.sp_send_dbmail
     @profile_name       = 'Notifications',
     @recipients         = 'nobody@example.com',
     @subject            = 'Test SQL query',
     @body               = 'This is a SQL query test.',
     @query              = 'SELECT * FROM abc.dbo.test',
     @attach_query_result_as_file = 1;
GO
```

**Example of sending a message in HTML format**  

```
USE msdb
GO

DECLARE @HTML_Body as NVARCHAR(500) = 'Hi, <h4> Heading </h4> </br> See the report. <b> Regards </b>';

EXEC msdb.dbo.sp_send_dbmail
     @profile_name       = 'Notifications',
     @recipients         = 'nobody@example.com',
     @subject            = 'Test HTML message',
     @body               = @HTML_Body,
     @body_format        = 'HTML';
GO
```

**Example of sending a message using a trigger when a specific event occurs in the database**  

```
USE AdventureWorks2017
GO
IF OBJECT_ID ('Production.iProductNotification', 'TR') IS NOT NULL
DROP TRIGGER Purchasing.iProductNotification
GO

CREATE TRIGGER iProductNotification ON Production.Product
   FOR INSERT
   AS
   DECLARE @ProductInformation nvarchar(255);
   SELECT
   @ProductInformation = 'A new product, ' + Name + ', is now available for $' + CAST(StandardCost AS nvarchar(20)) + '!'
   FROM INSERTED i;

EXEC msdb.dbo.sp_send_dbmail
     @profile_name       = 'Notifications',
     @recipients         = 'nobody@example.com',
     @subject            = 'New product information',
     @body               = @ProductInformation;
GO
```

## Viewing messages, logs, and attachments<a name="SQLServer.DBMail.View"></a>

You use RDS stored procedures to view messages, event logs, and attachments\.

**To view all email messages**
+ Use the following SQL query\.

  ```
  SELECT * FROM msdb.dbo.rds_fn_sysmail_allitems(); --WHERE sent_status='sent' or 'failed' or 'unsent'
  ```

**To view all email event logs**
+ Use the following SQL query\.

  ```
  SELECT * FROM msdb.dbo.rds_fn_sysmail_event_log();
  ```

**To view all email attachments**
+ Use the following SQL query\.

  ```
  SELECT * FROM msdb.dbo.rds_fn_sysmail_mailattachments();
  ```

## Deleting messages<a name="SQLServer.DBMail.Delete"></a>

You use the `rds_sysmail_delete_mailitems_sp` stored procedure to delete messages\.

**Note**  
RDS automatically deletes mail table items when DBMail history data reaches 1 GB in size, with a retention period of at least 24 hours\.  
If you want to keep mail items for a longer period, you can archive them\. For more information, see [Create a SQL Server Agent job to archive Database Mail messages and event logs](https://docs.microsoft.com/en-us/sql/relational-databases/database-mail/create-a-sql-server-agent-job-to-archive-database-mail-messages-and-event-logs) in the Microsoft documentation\.

**To delete all email messages**
+ Use the following SQL statement\.

  ```
  DECLARE @GETDATE datetime
  SET @GETDATE = GETDATE();
  EXECUTE msdb.dbo.rds_sysmail_delete_mailitems_sp @sent_before = @GETDATE;
  GO
  ```

**To delete all email messages with a particular status**
+ Use the following SQL statement to delete all failed messages\.

  ```
  DECLARE @GETDATE datetime
  SET @GETDATE = GETDATE();
  EXECUTE msdb.dbo.rds_sysmail_delete_mailitems_sp @sent_status = 'failed';
  GO
  ```

## Starting the mail queue<a name="SQLServer.DBMail.Start"></a>

You use the `rds_sysmail_control` stored procedure to start the Database Mail process\.

**Note**  
Enabling Database Mail automatically starts the mail queue\.

**To start the mail queue**
+ Use the following SQL statement\.

  ```
  EXECUTE msdb.dbo.rds_sysmail_control start;
  GO
  ```

## Stopping the mail queue<a name="SQLServer.DBMail.Stop"></a>

You use the `rds_sysmail_control` stored procedure to stop the Database Mail process\.

**To stop the mail queue**
+ Use the following SQL statement\.

  ```
  EXECUTE msdb.dbo.rds_sysmail_control stop;
  GO
  ```

## Working with file attachments<a name="SQLServer.DBMail.Files"></a>

The following file attachment extensions aren't supported in Database Mail messages from RDS on SQL Server: \.ade, \.adp, \.apk, \.appx, \.appxbundle, \.bat, \.bak, \.cab, \.chm, \.cmd, \.com, \.cpl, \.dll, \.dmg, \.exe, \.hta, \.inf1, \.ins, \.isp, \.iso, \.jar, \.job, \.js, \.jse, \.ldf, \.lib, \.lnk, \.mde, \.mdf, \.msc, \.msi, \.msix, \.msixbundle, \.msp, \.mst, \.nsh, \.pif, \.ps, \.ps1, \.psc1, \.reg, \.rgs, \.scr, \.sct, \.shb, \.shs, \.svg, \.sys, \.u3p, \.vb, \.vbe, \.vbs, \.vbscript, \.vxd, \.ws, \.wsc, \.wsf, and \.wsh\.

Database Mail uses the Microsoft Windows security context of the current user to control access to files\. Users who log in with SQL Server Authentication can't attach files using the `@file_attachments` parameter with the `sp_send_dbmail` stored procedure\. Windows doesn't allow SQL Server to provide credentials from a remote computer to another remote computer\. Therefore, Database Mail can't attach files from a network share when the command is run from a computer other than the computer running SQL Server\.

However, you can use SQL Server Agent jobs to attach files\. For more information on SQL Server Agent, see [Using SQL Server Agent](Appendix.SQLServer.CommonDBATasks.Agent.md) and [SQL Server Agent](https://docs.microsoft.com/en-us/sql/ssms/agent/sql-server-agent) in the Microsoft documentation\.

## Considerations for Multi\-AZ deployments<a name="SQLServer.DBMail.MAZ"></a>

When you configure Database Mail on a Multi\-AZ DB instance, the configuration isn't automatically propagated to the secondary\. We recommend converting the Multi\-AZ instance to a Single\-AZ instance, configuring Database Mail, and then converting the DB instance back to Multi\-AZ\. Then both the primary and secondary nodes have the Database Mail configuration\.

If you create a read replica from your Multi\-AZ instance that has Database Mail configured, the replica inherits the configuration, but without the password to the SMTP server\. Update the Database Mail account with the password\.