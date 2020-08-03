# Oracle SQLT<a name="Oracle.Options.SQLT"></a>

Amazon RDS supports Oracle SQLTXPLAIN \(SQLT\) through the use of the SQLT option\. 

The Oracle `EXPLAIN PLAN` statement can determine the execution plan of a SQL statement\. It can verify whether the Oracle optimizer chooses a certain execution plan, such as a nested loops join\. It also helps you understand the optimizer's decisions, such as why it chose a nested loops join over a hash join\. So `EXPLAIN PLAN` helps you understand the statement's performance\.

SQLT is an Oracle utility that produces a report\. The report includes object statistics, object metadata, optimizer\-related initialization parameters, and other information that a database administrator can use to tune a SQL statement for optimal performance\. SQLT produces an HTML report with hyperlinks to all of the sections in the report\.

Unlike Automatic Workload Repository or Statspack reports, SQLT works on individual SQL statements\. SQLT is a collection of SQL, PL/SQL, and SQL\*Plus files that collect, store, and display performance data\. 

Following are the supported Oracle versions for each SQLT version\.


****  

| SQLT Version | Oracle 19c | Oracle 18c | Oracle 12c version 12\.2 | Oracle 12c version 12\.1 | Oracle 11g | 
| --- | --- | --- | --- | --- | --- | 
|  12\.2\.180725  |  Supported  |  Supported  |  Supported  |  Supported  |  Supported  | 
|  12\.2\.180331  |  Not supported  |  Supported  |  Supported  |  Supported  |  Supported  | 
|  12\.1\.160429  |  Not supported  |  Not supported  |  Supported  |  Supported  |  Supported  | 

To download SQLT and access instructions for using it:
+ Log in to your My Oracle Support account, and open the following documents:
+ To download SQLT: [Document 215187\.1](https://support.oracle.com/epmos/faces/DocumentDisplay?id=215187.1)
+ For SQLT usage instructions: [Document 1614107\.1](https://support.oracle.com/epmos/faces/DocumentDisplay?id=1614107.1)
+ For frequently asked questions about SQLT: [Document 1454160\.1](https://support.oracle.com/epmos/faces/DocumentDisplay?id=1454160.1)
+ For information about reading SQLT output: [Document 1456176\.1](https://support.oracle.com/epmos/main/downloadattachmentprocessor?parent=DOCUMENT&sourceId=1456176.1&attachid=1456176.1:58&clickstream=yes)
+ For interpreting the Main report: [Document 1922234\.1](https://support.oracle.com/epmos/faces/DocumentDisplay?parent=DOCUMENT&sourceId=215187.1&id=1922234.1)

 You can use SQLT with any edition of the following Oracle Database versions: 
+ Oracle 19c, 19\.0\.0\.0
+ Oracle 18c, 18\.0\.0\.0
+ Oracle 12c, 12\.2\.0\.1
+ Oracle 12c, 12\.1\.0\.2
+ Oracle 11g, 11\.2\.0\.4

Amazon RDS does not support the following SQLT methods: 
+ `XPLORE` 
+ `XHUME` 

## Prerequisites for SQLT<a name="Oracle.Options.SQLT.PreReqs"></a>

The following are prerequisites for using SQLT:
+ You must remove users and roles that are required by SQLT, if they exist\.

  The SQLT option creates the following users and roles on a DB instance: 
  + `SQLTXPLAIN` user
  + `SQLTXADMIN` user
  + `SQLT_USER_ROLE` role

  If your DB instance has any of these users or roles, log in to the DB instance using a SQL client, and drop them using the following statements:

  ```
  DROP USER SQLTXPLAIN CASCADE;
  DROP USER SQLTXADMIN CASCADE;   
  DROP ROLE SQLT_USER_ROLE CASCADE;
  ```
+ You must remove tablespaces that are required by SQLT, if they exist\.

  The SQLT option creates the following tablespaces on a DB instance: 
  + `RDS_SQLT_TS`
  + `RDS_TEMP_SQLT_TS`

  If your DB instance has these tablespaces, log in to the DB instance using a SQL client, and drop them\.

## SQLT Option Settings<a name="Oracle.Options.SQLT.Options"></a>

 SQLT can work with licensed features that are provided by the Oracle Tuning Pack and the Oracle Diagnostics Pack\. The Oracle Tuning Pack includes the SQL Tuning Advisor, and the Oracle Diagnostics Pack includes the Automatic Workload Repository\. The SQLT settings enable or disable access to these features from SQLT\. 

Amazon RDS supports the following settings for the SQLT option\. 


****  

| Option Setting | Valid Values | Default Value | Description | 
| --- | --- | --- | --- | 
|  `LICENSE_PACK`  |  `T`, `D`, `N`  |  `N`   |  The Oracle Management Packs that you want to access with SQLT\. Enter one of the following values: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Oracle.Options.SQLT.html)  Amazon RDS does not provide licenses for these Oracle Management Packs\. If you indicate that you want to use a pack that is not included in your DB instance, you can use SQLT with the DB instance\. However, SQLT can't access the pack, and the SQLT report doesn't include the data for the pack\. For example, if you specify `T`, but the DB instance doesn't include the Oracle Tuning Pack, SQLT works on the DB instance, but the report it generates doesn't contain data related to the Oracle Tuning Pack\.   | 
|  `VERSION`  |  `2016-04-29.v1`, `2018-03-31.v1`, `2018-07-25.v1`  |  `2016-04-29.v1`   |  The version of SQLT that you want to install\.  For Oracle version 19\.0\.0\.0, the only supported version is `2018-07-25.v1`\. This version is also the default for Oracle version 19\.0\.0\.0\.   | 

## Adding the SQLT Option<a name="Oracle.Options.SQLT.Add"></a>

The following is the general process for adding the SQLT option to a DB instance: 

1. Create a new option group, or copy or modify an existing option group\.

1. Add the SQLT option to the option group\.

1. Associate the option group with the DB instance\.

After you add the SQLT option, as soon as the option group is active, SQLT is active\. 

**To add the SQLT option to a DB instance**

1. Determine the option group that you want to use\. You can create a new option group or use an existing option group\. If you want to use an existing option group, skip to the next step\. Otherwise, create a custom DB option group with the following settings: 

   1. For **Engine**, choose the Oracle edition that you want to use\. The SQLT option is supported on all editions\. 

   1. For **Major engine version**, choose the version of your DB instance\. 

   For more information, see [Creating an Option Group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.Create)\. 

1. Add the **SQLT** option to the option group\. For more information about adding options, see [Adding an Option to an Option Group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.AddOption)\.  

1. Apply the option group to a new or existing DB instance: 
   + For a new DB instance, you apply the option group when you launch the instance\. For more information, see [Creating an Amazon RDS DB Instance](USER_CreateDBInstance.md)\. 
   + For an existing DB instance, you apply the option group by modifying the instance and attaching the new option group\. For more information, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md)\. 

1. \(Optional\) Verify the SQLT installation on each DB instance with the SQLT option\. 

   1. Use a SQL client to connect to the DB instance as the master user\.

      For information about connecting to an Oracle DB instance using a SQL client, see [Connecting to a DB Instance Running the Oracle Database Engine](USER_ConnectToOracleInstance.md)\.

   1. Run the following query:

      ```
      SELECT sqltxplain.sqlt$a.get_param('tool_version') sqlt_version FROM DUAL;                        
      ```

      The query returns the current version of the SQLT option on Amazon RDS\. `12.1.160429` is an example of a version of SQLT that is available on Amazon RDS\.

1. Change the passwords of the users that are created by the SQLT option\.

   1. Use a SQL client to connect to the DB instance as the master user\.

   1. Run the following SQL statement to change the password for the `SQLTXADMIN` user:

      ```
      ALTER USER SQLTXADMIN IDENTIFIED BY new_password ACCOUNT UNLOCK;                         
      ```

   1. Run the following SQL statement to change the password for the `SQLTXPLAIN` user:

      ```
      ALTER USER SQLTXPLAIN IDENTIFIED BY new_password ACCOUNT UNLOCK;                         
      ```

**Note**  
Upgrading SQLT requires uninstalling an older version of SQLT and then installing the new version\. So, all SQLT metadata can be lost when you upgrade SQLT\. A major version upgrade of a database also uninstalls and re\-installs SQLT\. An example of a major version upgrade is an upgrade from Oracle 11g to Oracle 12c\.

## Using SQLT<a name="Oracle.Options.SQLT.Using"></a>

SQLT works with the Oracle SQL\*Plus utility\. 

**To use SQLT**

1.  Download the SQLT \.zip file from [Document 215187\.1](https://support.oracle.com/epmos/faces/DocumentDisplay?id=215187.1) on the My Oracle Support site\. 
**Note**  
You can't download SQLT 12\.1\.160429 from the My Oracle Support site\. Oracle has deprecated this older version\.

1.  Unzip the SQLT \.zip file\. 

1.  From a command prompt, change to the `sqlt/run` directory on your file system\. 

1.  From the command prompt, open SQL\*Plus, and connect to the DB instance as the master user\. 

   For information about connecting to a DB instance using SQL\*Plus, see [Connecting to a DB Instance Running the Oracle Database Engine](USER_ConnectToOracleInstance.md)\.

1.  Get the SQL ID of a SQL statement: 

   ```
   SELECT SQL_ID FROM V$SQL WHERE SQL_TEXT='sql_statement';                               
   ```

   Your output is similar to the following: 

   ```
   SQL_ID
   -------------
   chvsmttqjzjkn
   ```

1. Analyze a SQL statement with SQLT: 

   ```
   START sqltxtract.sql sql_id sqltxplain_user_password                    
   ```

   For example, for the SQL ID `chvsmttqjzjkn`, enter the following:

   ```
   START sqltxtract.sql chvsmttqjzjkn sqltxplain_user_password                    
   ```

   SQLT generates the HTML report and related resources as a \.zip file in the directory from which the SQLT command was run\.

1.  \(Optional\) To enable application users to diagnose SQL statements with SQLT, grant `SQLT_USER_ROLE` to each application user with the following statement: 

   ```
   GRANT SQLT_USER_ROLE TO application_user_name;                
   ```
**Note**  
Oracle does not recommend running SQLT with the `SYS` user or with users that have the `DBA` role\. It is a best practice to run SQLT diagnostics using the application user's account, by granting `SQLT_USER_ROLE` to the application user\.

## Upgrading the SQLT Option<a name="Oracle.Options.SQLT.Upgrading"></a>

With Amazon RDS for Oracle, you can upgrade the SQLT option from your existing version to a higher version\. To upgrade the SQLT option, complete steps 1–3 in [Using SQLT](#Oracle.Options.SQLT.Using) for the new version of SQLT\. Also, if you granted privileges for the previous version of SQLT in step 7 of that section, grant the privileges again for the new SQLT version\. 

Upgrading the SQLT option results in the loss of the older SQLT version's metadata\. The older SQLT version's schema and related objects are dropped, and the newer version of SQLT is installed\. For more information about the changes in the latest SQLT version, see [Document 1614201\.1](https://support.oracle.com/epmos/faces/DocumentDisplay?parent=DOCUMENT&sourceId=215187.1&id=1614201.1) on the My Oracle Support site\.

**Note**  
Version downgrades are not supported\.

## Modifying SQLT Settings<a name="Oracle.Options.SQLT.ModifySettings"></a>

After you enable SQLT, you can modify the `LICENSE_PACK` and `VERSION` settings for the option\.

For more information about how to modify option settings, see [Modifying an Option Setting](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.ModifyOption)\. For more information about each setting, see [SQLT Option Settings](#Oracle.Options.SQLT.Options)\. 

## Removing the SQLT Option<a name="Oracle.Options.SQLT.Remove"></a>

You can remove SQLT from a DB instance\. 

To remove SQLT from a DB instance, do one of the following: 
+ To remove SQLT from multiple DB instances, remove the SQLT option from the option group to which the DB instances belong\. This change affects all DB instances that use the option group\. For more information, see [Removing an Option from an Option Group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.RemoveOption)\. 
+ To remove SQLT from a single DB instance, modify the DB instance and specify a different option group that doesn't include the SQLT option\. You can specify the default \(empty\) option group or a different custom option group\. For more information, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md)\. 

## Related Topics<a name="Oracle.Options.SQLT.Related"></a>
+ [Working with Option Groups](USER_WorkingWithOptionGroups.md)
+ [Options for Oracle DB Instances](Appendix.Oracle.Options.md)