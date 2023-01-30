# Oracle time zone file autoupgrade<a name="Appendix.Oracle.Options.Timezone-file-autoupgrade"></a>

With the `TIMEZONE_FILE_AUTOUPGRADE` option, you can upgrade the current time zone file to the latest version on your DB instance\.

**Topics**
+ [Overview of Oracle time zone files](#Appendix.Oracle.Options.Timezone-file-autoupgrade.tz-overview)
+ [Downtime during the time zone file update](#Appendix.Oracle.Options.Timezone-file-autoupgrade.considerations)
+ [Strategies for updating your time zone file](#Appendix.Oracle.Options.Timezone-file-autoupgrade.strategies)
+ [Preparing to update the time zone file](#Appendix.Oracle.Options.Timezone-file-autoupgrade.preparing)
+ [Adding the time zone file autoupgrade option](#Appendix.Oracle.Options.Timezone-file-autoupgrade.adding)
+ [Checking your data after the update of the time zone file](#Appendix.Oracle.Options.Timezone-file-autoupgrade.checking)

## Overview of Oracle time zone files<a name="Appendix.Oracle.Options.Timezone-file-autoupgrade.tz-overview"></a>

An Oracle Database *time zone file* stores the following information:
+ Offset from Coordinated Universal Time \(UTC\)
+ Transition times for Daylight Saving Time \(DST\)
+ Abbreviations for standard time and DST

Oracle Database supplies multiple versions of time zone files\. When you create an Oracle database in an on\-premises environment, you choose the time zone file version\. For more information , see [Choosing a Time Zone File](https://docs.oracle.com/en/database/oracle/oracle-database/19/nlspg/datetime-data-types-and-time-zone-support.html#GUID-805AB986-DE12-4FEA-AF56-5AABCD2132DF) in the *Oracle Database Globalization Support Guide*\.

If the rules change for DST, Oracle publishes new time zone files\. Oracle releases these new time zone files independently of the schedule for quarterly RUs and RURs\. The time zone files reside on the database host in the directory `$ORACLE_HOME/oracore/zoneinfo/`\. The time zone file names use the format DSTv*version*, as in DSTv35\.

### How the time zone file affects data transfer<a name="Appendix.Oracle.Options.Timezone-file-autoupgrade.data-transfer"></a>

In Oracle Database, the `TIMESTAMP WITH TIME ZONE` data type stores time stamp and time zone data\. Data with the `TIMESTAMP WITH TIME ZONE` data type uses the rules in the associated time zone file version\. Thus, the existing data is affected when you update the time zone file\.

Problems can occur when you transfer data between databases that use different versions of the time zone file\. For example, if you try to import data from a source database with a higher time zone file version than the target database, you receive the `ORA-39405` error\. Previously, you had to work around this error by using either of the following techniques:
+ Create an RDS for Oracle DB instance with the desired time zone file, export data from your source database, and then import it into the new database\.
+ Use AWS DMS or logical replication to migrate your data\.

### Automatic updates using the TIMEZONE\_FILE\_AUTOUPGRADE option<a name="Appendix.Oracle.Options.Timezone-file-autoupgrade.option-overview"></a>

When you add the `TIMEZONE_FILE_AUTOUPGRADE` option in RDS for Oracle, RDS updates your time zone files automatically\. By ensuring that your databases use the same time zone file version, you avoid time\-consuming manual techniques when you move data between different environments\.

When you add the `TIMESTAMP WITH TIME ZONE` option to your option group, you can choose whether to add the option immediately or during the maintenance window\. After your DB instance uses the new option, RDS checks whether it can install a newer DSTv*version* file\. For example, if your current version might be DSTv34, RDS might determine that DSTv35 is now available\. In this case, RDS immediately updates to the new version and updates all existing time zone files\.

To find the available DST versions, look at the patches in [Release notes for Amazon Relational Database Service \(Amazon RDS\) for Oracle](https://docs.aws.amazon.com/AmazonRDS/latest/OracleReleaseNotes/Welcome.html)\. For example, [version 19\.0\.0\.0\.ru\-2022\-10\.rur\-2022\-10\.r1](https://docs.aws.amazon.com/AmazonRDS/latest/OracleReleaseNotes/oracle-version-19-0.html#oracle-version-RU-RUR.19.0.0.0.ru-2022-10.rur-2022-10.r1) lists patch 34533061: RDBMS \- DSTV39 UPDATE \- TZDATA2022C\.

## Downtime during the time zone file update<a name="Appendix.Oracle.Options.Timezone-file-autoupgrade.considerations"></a>

When RDS updates your time zone file, existing data that uses `TIMESTAMP WITH TIME ZONE` might change\. In this case, your primary consideration is downtime\.

**Warning**  
If you add the `TIMEZONE_FILE_AUTOUPGRADE` option, your engine upgrade might have prolonged downtime\. Updating time zone data for a large database might take hours or even days\.

The length of the time zone file update depends on factors such as the following:
+ The amount of `TIMESTAMP WITH TIME ZONE` data in your database
+ The DB instance configuration
+ The DB instance class
+ The storage configuration
+ The database configuration
+ The database parameter settings

Additional downtime can occur when you do the following:
+ Add the option to the option group when the DB instance uses an outdated time zone file
+ Upgrade the Oracle database engine when the new engine version contains a new version of the time zone file

**Note**  
During the time zone file update, RDS for Oracle calls `PURGE DBA_RECYCLEBIN`\.

## Strategies for updating your time zone file<a name="Appendix.Oracle.Options.Timezone-file-autoupgrade.strategies"></a>

You can upgrade your engine and update your time zone file independently\. Thus, you must choose among different update strategies, depending on whether you want to upgrade your database and time zone file at the same time\. 

The examples in this section assume that your DB instance uses database version 19\.0\.0\.0\.ru\-2019\-07\.rur\-2019\-07\.r1 and time zone file DSTv33\. Your DB instance file system includes file DSTv34\. Also assume that release update 19\.0\.0\.0\.ru\-2022\-10\.rur\-2022\-10\.r1 includes DSTv35\. To update your time zone file, you can use the following strategies\.

**Topics**
+ [Update the time zone file without upgrading the engine](#Appendix.Oracle.Options.Timezone-file-autoupgrade.strategies.no-upgrade)
+ [Upgrade the time zone file and DB engine version](#Appendix.Oracle.Options.Timezone-file-autoupgrade.strategies.upgrade)
+ [Upgrade your DB engine version without updating the time zone file](#Appendix.Oracle.Options.Timezone-file-autoupgrade.strategies.upgrade-only)

### Update the time zone file without upgrading the engine<a name="Appendix.Oracle.Options.Timezone-file-autoupgrade.strategies.no-upgrade"></a>

In this scenario, your database is using DSTv33, but DSTv34 is available\. You want to update the time zone file used by your DB instance from DSTv33 to DSTv34, but you don't want to upgrade your engine version\. In your modify DB instance operation, do the following:
+ Add `TIMEZONE_FILE_AUTOUPGRADE` to the option group used by your DB instance\. Specify whether to add the option immediately or defer it to the maintenance window\.
+ Don’t change your engine version\.

After the option is applied, RDS checks for a new DST version, sees that DSTv35 is available, and immediately starts the update\.

### Upgrade the time zone file and DB engine version<a name="Appendix.Oracle.Options.Timezone-file-autoupgrade.strategies.upgrade"></a>

In this scenario, your database is using DSTv33, but DSTv34 is available\. You want to upgrade your DB engine to version 19\.0\.0\.0\.ru\-2022\-10\.rur\-2022\-10\.r1 and update your time zone file to DSTv35 in the same operation\. In your modify DB instance operation, do the following:
+ Add `TIMEZONE_FILE_AUTOUPGRADE` to the option group used by your DB instance\. Specify whether to add the option immediately or defer it to the maintenance window\.
+ Change your engine version\.

After the option is applied, RDS checks for a new DST version, sees that DSTv35 is available, and immediately starts the update\. At the same time, RDS upgrades your DB engine to version 19\.0\.0\.0\.ru\-2022\-10\.rur\-2022\-10\.r1\.

### Upgrade your DB engine version without updating the time zone file<a name="Appendix.Oracle.Options.Timezone-file-autoupgrade.strategies.upgrade-only"></a>

In this scenario, your database is using DSTv33, but DSTv34 is available\. You want to upgrade your DB engine to version 19\.0\.0\.0\.ru\-2022\-10\.rur\-2022\-10\.r1 but retain time zone file DSTv33\. You might choose this strategy for the following reasons:
+ Your data doesn't use the `TIMESTAMP WITH TIME ZONE` data type\.
+ Your data uses the `TIMESTAMP WITH TIME ZONE` data type, but your data is not affected by the time zone changes\.
+ You want to postpone updating the time zone file because you can't tolerate the extra downtime\.

Your strategy depends on which of the following possibilities are true:
+ Your DB instance isn't associated with an option group that includes `TIMEZONE_FILE_AUTOUPGRADE`\. Leave your option group as it is\.
+ Your DB instance is associated with an option group that includes `TIMEZONE_FILE_AUTOUPGRADE`\. Associate your DB instance with an option group that doesn't include `TIMEZONE_FILE_AUTOUPGRADE`, and then modify the DB engine version\. 

## Preparing to update the time zone file<a name="Appendix.Oracle.Options.Timezone-file-autoupgrade.preparing"></a>

A time zone file upgrade has two separate phases: prepare and upgrade\. While not required, we strongly recommend that you perform the prepare step\. In this step, you find out which data will be affected by running the PL/SQL procedure `DBMS_DST.FIND_AFFECTED_TABLES`\. For more information about the prepare window, see [Upgrading the Time Zone File and Timestamp with Time Zone Data](https://docs.oracle.com/en/database/oracle/oracle-database/19/nlspg/datetime-data-types-and-time-zone-support.html#GUID-B0ACDB2E-4B49-4EB4-B4CC-9260DAE1567A) in the Oracle Database documentation\.

**To prepare to update the time zone file**

1. Connect to your Oracle database using a SQL client\.

1. Determine the current timezone file version used\.

   ```
   SELECT * FROM V$TIMEZONE_FILE;
   ```

1. Determine the latest timezone file version available on your DB instance\. This step is only applicable if you use Oracle Database 12c Release 2 \(12\.2\) or higher\.

   ```
   SELECT DBMS_DST.GET_LATEST_TIMEZONE_VERSION FROM DUAL;
   ```

1. Determine the total size of tables that have columns of type `TIMESTAMP WITH LOCAL TIME ZONE` or `TIMESTAMP WITH TIME ZONE`\.

   ```
   SELECT SUM(BYTES)/1024/1024/1024 "Total_size_w_TSTZ_columns_GB"
   FROM   DBA_SEGMENTS
   WHERE  SEGMENT_TYPE LIKE 'TABLE%'
   AND    (OWNER, SEGMENT_NAME) IN
            (SELECT OWNER, TABLE_NAME
             FROM   DBA_TAB_COLUMNS
             WHERE  DATA_TYPE LIKE 'TIMESTAMP%TIME ZONE');
   ```

1. Determine the names and sizes of segments that have columns of type `TIMESTAMP WITH LOCAL TIME ZONE` or `TIMESTAMP WITH TIME ZONE`\.

   ```
   SELECT OWNER, SEGMENT_NAME, SUM(BYTES)/1024/1024/1024 "SEGMENT_SIZE_W_TSTZ_COLUMNS_GB"
   FROM   DBA_SEGMENTS
   WHERE  SEGMENT_TYPE LIKE 'TABLE%'
   AND    (OWNER, SEGMENT_NAME) IN
            (SELECT OWNER, TABLE_NAME
             FROM   DBA_TAB_COLUMNS
             WHERE  DATA_TYPE LIKE 'TIMESTAMP%TIME ZONE')
   GROUP BY OWNER, SEGMENT_NAME;
   ```

1. Run the prepare step\. 
   + The procedure `DBMS_DST.CREATE_AFFECTED_TABLE` creates a table to store any affected data\. You pass the name of this table to the `DBMS_DST.FIND_AFFECTED_TABLES` procedure\. For more information, see [CREATE\_AFFECTED\_TABLE Procedure](https://docs.oracle.com/en/database/oracle/oracle-database/19/arpls/DBMS_DST.html#GUID-C53BAABA-914A-404C-9CD5-823257BE0B00) in the Oracle Database documentation\.
   + This procedure `CREATE_ERROR_TABLE` creates a table to log errors\. For more information, see [CREATE\_ERROR\_TABLE Procedure](https://docs.oracle.com/en/database/oracle/oracle-database/19/arpls/DBMS_DST.html#GUID-6A7EA024-B02D-4486-B1D6-EF6ABF5DE507) in the Oracle Database documentation\.

   The following example creates the affected data and error tables, and finds all affected tables\.

   ```
   EXEC DBMS_DST.CREATE_ERROR_TABLE('my_error_table')
   EXEC DBMS_DST.CREATE_AFFECTED_TABLE('my_affected_table')
   
   EXEC DBMS_DST.BEGIN_PREPARE(new_version);
   EXEC DBMS_DST.FIND_AFFECTED_TABLES('my_affected_table', TRUE, 'my_error_table');
   EXEC DBMS_DST.END_PREPARE;
   
   SELECT * FROM my_affected_table;
   SELECT * FROM my_error_table;
   ```

1. Query the affected and error tables\.

   ```
   SELECT * FROM my_affected_table;
   SELECT * FROM my_error_table;
   ```

## Adding the time zone file autoupgrade option<a name="Appendix.Oracle.Options.Timezone-file-autoupgrade.adding"></a>

When you add the option to an option group, the option group is in one of the following states:
+ An existing option group is currently attached to at least one DB instance\. When you add the option, all DB instances that use this option group automatically restart\. This causes a brief outage\.
+ An existing option group is not attached to any DB instance\. You plan to add the option and then associate the existing option group with existing DB instances or with a new DB instance\.
+ You create a new option group and add the option\. You plan to associate the new option group with existing DB instances or with a new DB instance\.

### Console<a name="Appendix.Oracle.Options.Timezone-file-autoupgrade.console"></a>

**To add the time zone file autoupgrade option to a DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Option groups**\.

1. Determine the option group you want to use\. You can create a new option group or use an existing option group\. If you want to use an existing option group, skip to the next step\. Otherwise, create a custom DB option group with the following settings: 

   1. For **Engine** choose the Oracle Database edition for your DB instance\. 

   1. For **Major engine version** choose the version of your DB instance\. 

   For more information, see [Creating an option group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.Create)\. 

1. Choose the option group that you want to modify, and then choose **Add option**\.

1. In the **Add option** window, do the following: 

   1. Choose **TIMEZONE\_FILE\_AUTOUPGRADE**\.

   1. To enable the option on all associated DB instances as soon as you add it, for **Apply Immediately**, choose **Yes**\. If you choose **No** \(the default\), the option is enabled for each associated DB instance during its next maintenance window\.

1. When the settings are as you want them, choose **Add option**\.

### AWS CLI<a name="Appendix.Oracle.Options.Timezone-file-autoupgrade.CLI"></a>

The following example uses the AWS CLI [add\-option\-to\-option\-group](https://docs.aws.amazon.com/cli/latest/reference/rds/add-option-to-option-group.html) command to add the `TIMEZONE_FILE_AUTOUPGRADE` option to an option group called `myoptiongroup`\.

For Linux, macOS, or Unix:

```
aws rds add-option-to-option-group \
    --option-group-name "myoptiongroup" \
    --options "OptionName=TIMEZONE_FILE_AUTOUPGRADE" \
    --apply-immediately
```

For Windows:

```
aws rds add-option-to-option-group ^
    --option-group-name "myoptiongroup" ^
    --options "OptionName=TIMEZONE_FILE_AUTOUPGRADE" ^
    --apply-immediately
```

## Checking your data after the update of the time zone file<a name="Appendix.Oracle.Options.Timezone-file-autoupgrade.checking"></a>

We recommend that you check your data after you update the time zone file\. During the prepare step, RDS for Oracle automatically creates the following tables:
+ `rdsadmin.rds_dst_affected_tables` – Lists the tables that contain data affected by the update
+ `rdsadmin.rds_dst_error_table` – Lists the errors generated during the update

These tables are independent of any tables that you create in the prepare window\. To see the results of the update, query the tables as follows\.

```
SELECT * FROM rdsadmin.rds_dst_affected_tables;
SELECT * FROM rdsadmin.rds_dst_error_table;
```

For more information about the schema for the affected data and error tables, see [FIND\_AFFECTED\_TABLES Procedure](https://docs.oracle.com/en/database/oracle/oracle-database/19/arpls/DBMS_DST.html#GUID-1F977505-671C-4D5B-8570-86956F136199) in the Oracle documentation\.