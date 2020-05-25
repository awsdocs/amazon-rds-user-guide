# Oracle Enterprise Manager Database Express<a name="Appendix.Oracle.Options.OEM_DBControl"></a>

Amazon RDS supports Oracle Enterprise Manager \(OEM\) Database Express through the use of the OEM option\. Amazon RDS supports the following versions of OEM database: 
+ Oracle Enterprise Manager Database Express for Oracle 19c, Oracle 18c, and Oracle 12c
+ Oracle Enterprise Manager 11g Database Control for Oracle 11g

OEM Database Express and Database Control are similar tools that have a web\-based interface for Oracle database administration\. For more information about these tools, see [Accessing Enterprise Manager Database Express 18c](https://docs.oracle.com/en/cloud/paas/database-dbaas-cloud/csdbi/access-em-database-express-18c.html), [Accessing Enterprise Manager Database Express 12c](https://docs.oracle.com/en/cloud/paas/database-dbaas-cloud/csdbi/access-em-database-express-12c.html), and [Accessing Enterprise Manager 11g Database Control](https://docs.oracle.com/cloud/latest/dbcs_dbaas/CSDBI/GUID-0A67F8E8-E1A9-4D0E-8381-FEC4B9316841.htm#CSDBI3445) in the Oracle documentation\. 

The following are some limitations to using OEM Database: 
+ OEM Database is not supported on the db\.t3\.micro or db\.t3\.small DB instance classes\. 

  For more information about DB instance classes, see [DB Instance Class Support for Oracle](CHAP_Oracle.md#Oracle.Concepts.InstanceClasses)\. 
+ OEM 11g Database Control is not compatible with the following time zones: America/Argentina/Buenos\_Aires, America/Matamoros, America/Monterrey, America/Toronto, Asia/Ashgabat, Asia/Dhaka, Asia/Kathmandu, Asia/Kolkata, Asia/Ulaanbaatar, Atlantic/Cape\_Verde, Australia/Eucla, Pacific/Kiritimati\. 

  For more information about time zone support, see [Oracle Time Zone](Appendix.Oracle.Options.Timezone.md)\. 

## OEM Database Option Settings<a name="Appendix.Oracle.Options.OEM_DBControl.Options"></a>

Amazon RDS supports the following settings for the OEM option\. 


****  

| Option Setting | Valid Values | Description | 
| --- | --- | --- | 
| **Port** | An integer value |  The port on the DB instance that listens for OEM Database\. The default for OEM Database Express is 5500\. The default for OEM 11g Database Control is 1158\.  For OEM 11g Database Control, set the port to 1158 or to a value in the 5500 to 5519 range\.  | 
| **Security Groups** | — |  A security group that has access to **Port**\.   | 

## Adding the OEM Database Option<a name="Appendix.Oracle.Options.OEM_DBControl.Add"></a>

The general process for adding the OEM option to a DB instance is the following: 

1. Create a new option group, or copy or modify an existing option group\.

1. Add the option to the option group\.

1. Associate the option group with the DB instance\.

When you add the OEM option for an Oracle 11g DB instance, no outage occurs, so you don't need to restart your DB instance\. However, when you add the OEM option for an Oracle 12c, Oracle 18c, or Oracle 19c DB instance, a brief outage occurs while your DB instance is automatically restarted\. 

**To add the OEM option to a DB instance**

1. Determine the option group you want to use\. You can create a new option group or use an existing option group\. If you want to use an existing option group, skip to the next step\. Otherwise, create a custom DB option group with the following settings: 

   1. For **Engine** choose the oracle edition for your DB instance\. 

   1. For **Major engine version** choose the version of your DB instance\. 

   For more information, see [Creating an Option Group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.Create)\. 

1. Add the OEM option to the option group, and configure the option settings\. For more information about adding options, see [Adding an Option to an Option Group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.AddOption)\. For more information about each setting, see [OEM Database Option Settings](#Appendix.Oracle.Options.OEM_DBControl.Options)\. 
**Note**  
If you add the OEM option to an existing option group that is already attached to one or more Oracle 19c, Oracle 18c, or Oracle 12c DB instances, a brief outage occurs while all the DB instances are automatically restarted\. 

1. Apply the option group to a new or existing DB instance: 
   + For a new DB instance, you apply the option group when you launch the instance\. For more information, see [Creating an Amazon RDS DB Instance](USER_CreateDBInstance.md)\. 
   + For an existing DB instance, you apply the option group by modifying the instance and attaching the new option group\. When you add the OEM option for an Oracle 19c, Oracle 18c, or Oracle 12c DB instance, a brief outage occurs while your DB instance is automatically restarted\. For more information, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md)\. 

**Note**  
You can also use the AWS CLI to add the OEM option\. For examples, see [Adding an Option to an Option Group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.AddOption)\.

## Using OEM Database<a name="Appendix.Oracle.Options.OEM_DBControl.Using"></a>

After you enable the OEM option, you can begin using the OEM Database tool from your web browser\. 

You can access either OEM Database Control or OEM Database Express from your web browser\. For example, if the endpoint for your Amazon RDS DB instance is `mydb.f9rbfa893tft.us-east-1.rds.amazonaws.com`, and your OEM port is 1158, then the URL to access the OEM Database Control the following\. 

```
1. https://mydb.f9rbfa893tft.us-east-1.rds.amazonaws.com:1158/em
```

When you access either tool from your web browser, a login window appears that prompts you for a user name and password\. Type the master user name and master password for your DB instance\. You are now ready to manage your Oracle databases\. 

## Modifying OEM Database Settings<a name="Appendix.Oracle.Options.OEM_DBControl.ModifySettings"></a>

After you enable OEM Database, you can modify the Security Groups setting for the option\. 

You can't modify the OEM port number after you have associated the option group with a DB instance\. To change the OEM port number for a DB instance, do the following: 

1. Create a new option group\.

1. Add the OEM option with the new port number to the new option group\. 

1. Remove the existing option group from the DB instance\.

1. Add the new option group to the DB instance\.

For more information about how to modify option settings, see [Modifying an Option Setting](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.ModifyOption)\. For more information about each setting, see [OEM Database Option Settings](#Appendix.Oracle.Options.OEM_DBControl.Options)\. 

## Performing Database Tasks with OEM Database<a name="Appendix.Oracle.Options.OEM_DBControl.DBTasks"></a>

You can use Amazon RDS procedures to run certain OEM Database Express tasks\. By running these procedures, you can do the tasks listed following\.

**Note**  
OEM Database Express tasks run asynchronously\.

**Topics**
+ [Switching the Website Front End for OEM Database Express to Adobe Flash](#Appendix.Oracle.Options.OEM_DBControl.DBTasks.FrontEndToFlash)
+ [Switching the Website Front End for OEM Database Express to Oracle JET](#Appendix.Oracle.Options.OEM_DBControl.DBTasks.FrontEndToOracleJET)

### Switching the Website Front End for OEM Database Express to Adobe Flash<a name="Appendix.Oracle.Options.OEM_DBControl.DBTasks.FrontEndToFlash"></a>

**Note**  
This task is only available on Oracle DB instances running version 19c or later\.

Starting with Oracle 19c, Oracle has deprecated the former OEM Database Express user interface, which was based on Adobe Flash\. Instead, OEM Database Express now uses an interface built with Oracle JET\. If you experience difficulties with the new interface, you can switch back to the deprecated Flash\-based interface\. Difficulties you might experience with the new interface include being stuck on a `Loading` screen after logging in to OEM Database Express\. You might also miss certain features that were present in the Flash\-based version of OEM Database Express\.

To switch the OEM Database Express website front end to Adobe Flash, run the Amazon RDS procedure `rdsadmin.rdsadmin_oem_tasks.em_express_frontend_to_flash`\. This procedure is equivalent to the `execemx emx` SQL command\.

Security best practices discourage the use of Adobe Flash\. Although you can revert to the Flash\-based OEM Database Express, we recommend the use of the JET\-based OEM Database Express websites if possible\. If you revert to using Adobe Flash and want to switch back to using Oracle JET, use the `rdsadmin.rdsadmin_oem_tasks.em_express_frontend_to_jet` procedure\. After an Oracle database upgrade, a newer version of Oracle JET might resolve JET\-related issues in OEM Database Express\. For more information about switching to Oracle JET, see [Switching the Website Front End for OEM Database Express to Oracle JET](#Appendix.Oracle.Options.OEM_DBControl.DBTasks.FrontEndToOracleJET)\.

**Note**  
Running this task from the source DB instance for a read replica also causes the read replica to switch its OEM Database Express website front ends to Adobe Flash\.

The following procedure invocation creates a task to switch the OEM Database Express website to Adobe Flash and returns the ID of the task\.

```
SELECT rdsadmin.rdsadmin_oem_tasks.em_express_frontend_to_flash() as TASK_ID from DUAL;
```

You can view the result by displaying the task's output file\.

```
SELECT text FROM table(rdsadmin.rds_file_util.read_text_file('BDUMP','dbtask-task-id.log'));
```

Replace *`task-id`* with the task ID returned by the procedure\. For more information about the Amazon RDS procedure `rdsadmin.rds_file_util.read_text_file`, see [Reading Files in a DB Instance Directory](Appendix.Oracle.CommonDBATasks.Misc.md#Appendix.Oracle.CommonDBATasks.ReadingFiles) 

You can also view the contents of the task's output file in the AWS Management Console by searching the log entries in the **Logs & events** section for the `task-id`\.

### Switching the Website Front End for OEM Database Express to Oracle JET<a name="Appendix.Oracle.Options.OEM_DBControl.DBTasks.FrontEndToOracleJET"></a>

**Note**  
This task is only available on Oracle DB instances running version 19c or later\.

To switch the OEM Database Express website front end to Oracle JET, run the Amazon RDS procedure `rdsadmin.rdsadmin_oem_tasks.em_express_frontend_to_jet`\. This procedure is equivalent to the `execemx omx` SQL command\.

By default, the OEM Database Express websites for Oracle DB instances running 19c or later use Oracle JET\. If you used the `rdsadmin.rdsadmin_oem_tasks.em_express_frontend_to_flash` procedure to switch the OEM Database Express website front end to Adobe Flash, you can switch back to Oracle JET\. To do this, use the `rdsadmin.rdsadmin_oem_tasks.em_express_frontend_to_jet` procedure\. For more information about switching to Adobe Flash, see [Switching the Website Front End for OEM Database Express to Adobe Flash](#Appendix.Oracle.Options.OEM_DBControl.DBTasks.FrontEndToFlash)\.

**Note**  
Running this task from the source DB instance for a read replica also causes the read replica to switch its OEM Database Express website front ends to Oracle JET\.

The following procedure invocation creates a task to switch the OEM Database Express website to Oracle JET and returns the ID of the task\.

```
SELECT rdsadmin.rdsadmin_oem_tasks.em_express_frontend_to_jet() as TASK_ID from DUAL;
```

You can view the result by displaying the task's output file\.

```
SELECT text FROM table(rdsadmin.rds_file_util.read_text_file('BDUMP','dbtask-task-id.log'));
```

Replace *`task-id`* with the task ID returned by the procedure\. For more information about the Amazon RDS procedure `rdsadmin.rds_file_util.read_text_file`, see [Reading Files in a DB Instance Directory](Appendix.Oracle.CommonDBATasks.Misc.md#Appendix.Oracle.CommonDBATasks.ReadingFiles) 

You can also view the contents of the task's output file in the AWS Management Console by searching the log entries in the **Logs & events** section for the `task-id`\.

## Removing the OEM Database Option<a name="Appendix.Oracle.Options.OEM_DBControl.Remove"></a>

You can remove the OEM option from a DB instance\. When you remove the OEM option for an Oracle 19c, Oracle 18c, or Oracle 12c DB instance, a brief outage occurs while your DB instance is automatically restarted\. So, after you remove the OEM option, you don't need to restart your DB instance\. When you remove the OEM option for an Oracle 11g DB instance, there is not outage, and you don't need to restart your DB instance\. 

To remove the OEM option from a DB instance, do one of the following: 
+ Remove the OEM option from the option group it belongs to\. This change affects all DB instances that use the option group\. For more information, see [Removing an Option from an Option Group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.RemoveOption)\. 
+ Modify the DB instance and specify a different option group that doesn't include the OEM option\. This change affects a single DB instance\. You can specify the default \(empty\) option group, or a different custom option group\. For more information, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md)\. 