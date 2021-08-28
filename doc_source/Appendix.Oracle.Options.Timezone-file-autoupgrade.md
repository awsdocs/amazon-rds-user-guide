# Oracle time zone file autoupgrade<a name="Appendix.Oracle.Options.Timezone-file-autoupgrade"></a>

With the `TIMEZONE_FILE_AUTOUPGRADE` option, you can upgrade the current time zone file to the latest version on your DB instance\.

**Topics**
+ [Purpose of time zone files](#Appendix.Oracle.Options.Timezone-file-autoupgrade.purpose)
+ [Considerations for updating your time zone file](#Appendix.Oracle.Options.Timezone-file-autoupgrade.considerations)
+ [Strategies for updating your time zone file](#Appendix.Oracle.Options.Timezone-file-autoupgrade.strategies)
+ [Preparing to update the time zone file](#Appendix.Oracle.Options.Timezone-file-autoupgrade.preparing)
+ [Adding the time zone file autoupgrade option](#Appendix.Oracle.Options.Timezone-file-autoupgrade.adding)
+ [Checking your data after the update of the time zone file](#Appendix.Oracle.Options.Timezone-file-autoupgrade.checking)

## Purpose of time zone files<a name="Appendix.Oracle.Options.Timezone-file-autoupgrade.purpose"></a>

In Oracle Database, the `TIMESTAMP WITH TIME ZONE` data type stores time stamp and time zone data\. This data type is useful for preserving local time zone information\.

Oracle Database stores transition rules and UTC offsets in *time zone files*\. The offset is the difference between local time and UTC\. When you create an Oracle database in an on\-premises environment, you choose the time zone file version\. Data with the `TIMESTAMP WITH TIME ZONE` data type uses the rules in the associated time zone file version\. The time zone files reside in `$ORACLE_HOME/oracore/zoneinfo/`\.

If a government changes the rules for Daylight Savings Time \(DST\), Oracle publishes new time zone files\. Oracle releases time zone files separately from PSUs and RUs\. The time zone file names use the format DSTv*version*, as in DSTv35\. When you add the `TIMEZONE_FILE_AUTOUPGRADE` option in Amazon RDS for Oracle, you can update your time zone files\. 

The `TIMEZONE_FILE_AUTOUPGRADE` option is useful when you move data between different environments\. If you try to import data from a source database with a higher time zone file version than the target database, you receive the `ORA-39405` error\. Previously, you had to work around the error by using either of the following techniques:
+ Create an RDS for Oracle instance with the desired time zone file, export data from your source database, and then import it into the new database\.
+ Use AWS DMS or logical replication to migrate your data\.

With the `TIMEZONE_FILE_AUTOUPGRADE` option, you can upgrade the time zone file on the source database without using the preceding cumbersome techniques\.

## Considerations for updating your time zone file<a name="Appendix.Oracle.Options.Timezone-file-autoupgrade.considerations"></a>

When you update your time zone file, data that uses `TIMESTAMP WITH TIME ZONE` might change\. Your primary consideration is downtime\.

**Warning**  
If you add the `TIMEZONE_FILE_AUTOUPGRADE`, your engine upgrade might have prolonged downtime\. Updating time zone data for a large database might take hours or even days\.

The length of the update depends on factors such as the following:
+ The amount of `TIMESTAMP WITH TIME ZONE` data in your database
+ The instance configuration
+ The DB instance class
+ The storage configuration
+ The database configuration
+ The database parameter settings

Additional downtime can occur when you do the following:
+ Add the option to the option group when the instance uses an outdated time zone file
+ Upgrade the Oracle database engine when the new engine version contains a new version of the time zone file

**Note**  
During the time zone file update, RDS for Oracle calls `PURGE DBA_RECYCLEBIN`\.

## Strategies for updating your time zone file<a name="Appendix.Oracle.Options.Timezone-file-autoupgrade.strategies"></a>

You can upgrade your engine and update your time zone file independently\. Thus, you must choose among different update strategies\. 

The examples in this section assume that your instance uses database version 19\.0\.0\.0\.ru\-2019\-07\.rur\-2019\-07\.r1 and time zone file DSTv33\. Your DB instance file system includes file DSTv34\. Also assume that release update 19\.0\.0\.0\.ru\-2021\-01\.rur\-2021\-01\.r1 includes DSTv35\. To update your time zone file, you can use the following strategies:
+ Update the time zone file used by your database without upgrading the DB engine version\. 

  Update the time zone file used by your instance from DSTv33 to DSTv34\. In your modify instance operation, do the following:
  + Add `TIMEZONE_FILE_AUTOUPGRADE` to the option group used by your instance\.
  + Don’t change your engine version\.
+ Upgrade your DB engine version and the time zone file in the same operation\.

  Upgrade your engine to version 19\.0\.0\.0\.ru\-2021\-01\.rur\-2021\-01\.r1 and update your time zone file to DSTv35 in the same operation\. In your modify instance operation, do the following:
  + Add `TIMEZONE_FILE_AUTOUPGRADE` to the option group used by your instance\.
  + Change your engine version\.
+ Upgrade your DB engine version without updating the time zone file\.

  Upgrade your database to version 19\.0\.0\.0\.ru\-2021\-01\.rur\-2021\-01\.r1 but retain time zone file DSTv33\. In this case, the possibilities are as follows:
  + Your instance isn't associated with an option group that includes `TIMEZONE_FILE_AUTOUPGRADE`\. Leave the option group as it is\.
  + Your instance is associated with an option group that includes `TIMEZONE_FILE_AUTOUPGRADE`\. Associate your instance with an option group that doesn't have `TIMEZONE_FILE_AUTOUPGRADE`\. Then modify the engine version\.

  You might choose this strategy for the following reasons:
  + Your data doesn't use the `TIMESTAMP WITH TIME ZONE` data type\.
  + Your data uses the `TIMESTAMP WITH TIME ZONE` data type, but your data is not affected by the time zone changes\.
  + You want to postpone updating the time zone file because you can't tolerate the extra downtime\.

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
   + This procedure CREATE\_ERROR\_TABLE creates a table to log errors\. For more information, see [CREATE\_ERROR\_TABLE Procedure](https://docs.oracle.com/en/database/oracle/oracle-database/19/arpls/DBMS_DST.html#GUID-6A7EA024-B02D-4486-B1D6-EF6ABF5DE507) in the Oracle Database documentation\.

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

The procedure for adding the time zone autoupgrade option to a DB instance is the following: 

1. Create a new option group, or copy or modify an existing option group\.

1. Add the option to the option group\.

1. Associate the option group with the DB instance\.

When you add the option, a brief outage occurs while your DB instance is automatically restarted\. 

### Console<a name="Appendix.Oracle.Options.Timezone-file-autoupgrade.console"></a>

**To add the time zone file autoupgrade option to a DB instance**

1. Determine the option group you want to use\. You can create a new option group or use an existing option group\. If you want to use an existing option group, skip to the next step\. Otherwise, create a custom DB option group with the following settings: 

   1. For **Engine** choose the oracle edition for your DB instance\. 

   1. For **Major engine version** choose the version of your DB instance\. 

   For more information, see [Creating an option group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.Create)\. 

1. Add the **TIMEZONE\_FILE\_AUTOUPGRADE** option to the option group\.
**Important**  
If you add the option to an existing option group that is already attached to one or more DB instances, a brief outage occurs while all the DB instances are automatically restarted\. 

1. Apply the option group to a new or existing DB instance: 
   + For a new DB instance, you apply the option group when you launch the instance\. For more information, see [Creating an Amazon RDS DB instance](USER_CreateDBInstance.md)\. 
   + For an existing DB instance, you apply the option group by modifying the instance and attaching the new option group\. When you add the time zone option to an existing DB instance, a brief outage occurs while your DB instance is automatically restarted\. For more information, see [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)\. 

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