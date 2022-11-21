# Performing common scheduling tasks for Oracle DB instances<a name="Appendix.Oracle.CommonDBATasks.Scheduler"></a>

Some scheduler jobs owned by `SYS` can interfere with normal database operations\. Oracle Support recommends you disable these jobs or modify the schedule\. To perform tasks for Oracle Scheduler jobs owned by `SYS`, use the Amazon RDS package `rdsadmin.rdsadmin_dbms_scheduler`\.

The `rdsadmin.rdsadmin_dbms_scheduler` procedures are supported for the following Amazon RDS for Oracle DB engine versions:
+ Oracle Database 21c \(21\.0\.0\)
+ Oracle Database 19c
+ Oracle Database 12c Release 2 \(12\.2\) on 12\.2\.0\.2\.ru\-2019\-07\.rur\-2019\-07\.r1 or higher 12\.2 versions
+ Oracle Database 12c Release 1 \(12\.1\) on 12\.1\.0\.2\.v17 or higher 12\.1 versions

## Common parameters for Oracle Scheduler procedures<a name="Appendix.Oracle.CommonDBATasks.Scheduler.CommonParameters"></a>

To perform tasks with Oracle Scheduler, use procedures in the Amazon RDS package `rdsadmin.rdsadmin_dbms_scheduler`\. Several parameters are common to the procedures in the package\. The package has the following common parameters\.


****  

| Parameter name | Data type | Valid values | Default | Required | Description | 
| --- | --- | --- | --- | --- | --- | 
|  `name`  |  varchar2  |  `'SYS.BSLN_MAINTAIN_STATS_JOB'`,`'SYS.CLEANUP_ONLINE_IND_BUILD'`   |  —  |  Yes  |  The name of the job to modify\.  Currently, you can only modify `SYS.CLEANUP_ONLINE_IND_BUILD` and `SYS.BSLN_MAINTAIN_STATS_JOB` jobs\.   | 
|  `attribute`  |  varchar2  |  `'REPEAT_INTERVAL'`,`'SCHEDULE_NAME'`  |  –  |  Yes  |  Attribute to modify\. To modify the repeat interval for the job, specify `'REPEAT_INTERVAL'`\. To modify the schedule name for the job, specify `'SCHEDULE_NAME'`\.  | 
|  `value`  |  varchar2  |  A valid schedule interval or schedule name, depending on attribute used\.  |  –  |  Yes  |  The new value of the attribute\.  | 

## Modifying DBMS\_SCHEDULER jobs<a name="Appendix.Oracle.CommonDBATasks.ModifyScheduler"></a>

To modify certain components of Oracle Scheduler, use the Oracle procedure `dbms_scheduler.set_attribute`\. For more information, see [DBMS\_SCHEDULER](https://docs.oracle.com/database/121/ARPLS/d_sched.htm#ARPLS72235) and [SET\_ATTRIBUTE procedure](https://docs.oracle.com/database/121/ARPLS/d_sched.htm#ARPLS72399) in the Oracle documentation\. 

When working with Amazon RDS DB instances, prepend the schema name `SYS` to the object name\. The following example sets the resource plan attribute for the Monday window object\.

```
BEGIN
    DBMS_SCHEDULER.SET_ATTRIBUTE(
        name      => 'SYS.MONDAY_WINDOW',
        attribute => 'RESOURCE_PLAN',
        value     => 'resource_plan_1');
END;
/
```

## Modifying AutoTask maintenance windows<a name="Appendix.Oracle.CommonDBATasks.Scheduler.maintenance-windows"></a>

Amazon RDS for Oracle instances are created with default settings for maintenance windows\. Automated maintenance tasks such as optimizer statistics collection run during these windows\. By default, the maintenance windows turn on Oracle Database Resource Manager\.

To modify the window, use the `DBMS_SCHEDULER` package\. You might need to modify the maintenance window settings for the following reasons:
+ You want maintenance jobs to run at a different time, with different settings, or not at all\. For example, might want to modify the window duration, or change the repeat time and interval\.
+ You want to avoid the performance impact of enabling Resource Manager during maintenance\. For example, if the default maintenance plan is specified, and if the maintenance window opens while the database is under load, you might see wait events such as `resmgr:cpu quantum`\. This wait event is related to Database Resource Manager\. You have the following options:
  + Ensure that maintenance windows are active during off\-peak times for your DB instance\.
  + Disable the default maintenance plan by setting the `resource_plan` attribute to an empty string\.
  + Set the `resource_manager_plan` parameter to `FORCE:` in your parameter group\. If your instance uses Enterprise Edition, this setting prevents Database Resource Manager plans from activating\.

**To modify your maintenance window settings**

1. Connect to your database using an Oracle SQL client\.

1. Query the current configuration for a scheduler window\. 

   The following example queries the configuration for `MONDAY_WINDOW`\.

   ```
   SELECT ENABLED, RESOURCE_PLAN, DURATION, REPEAT_INTERVAL
   FROM   DBA_SCHEDULER_WINDOWS 
   WHERE  WINDOW_NAME='MONDAY_WINDOW';
   ```

   The following output shows that the window is using the default values\.

   ```
   ENABLED         RESOURCE_PLAN                  DURATION         REPEAT_INTERVAL
   --------------- ------------------------------ ---------------- ------------------------------
   TRUE            DEFAULT_MAINTENANCE_PLAN       +000 04:00:00    freq=daily;byday=MON;byhour=22
                                                                   ;byminute=0; bysecond=0
   ```

1. Modify the window using the `DBMS_SCHEDULER` package\.

   The following example sets the resource plan to null so that the Resource Manager won't run during the maintenance window\.

   ```
   BEGIN
     -- disable the window to make changes
     DBMS_SCHEDULER.DISABLE(name=>'"SYS"."MONDAY_WINDOW"',force=>TRUE);
   
     -- specify the empty string to use no plan
     DBMS_SCHEDULER.SET_ATTRIBUTE(name=>'"SYS"."MONDAY_WINDOW"', attribute=>'RESOURCE_PLAN', value=>'');
   
     -- re-enable the window
     DBMS_SCHEDULER.ENABLE(name=>'"SYS"."MONDAY_WINDOW"');
   END;
   /
   ```

   The following example sets the maximum duration of the window to 2 hours\.

   ```
   BEGIN
     DBMS_SCHEDULER.DISABLE(name=>'"SYS"."MONDAY_WINDOW"',force=>TRUE);
     DBMS_SCHEDULER.SET_ATTRIBUTE(name=>'"SYS"."MONDAY_WINDOW"', attribute=>'DURATION', value=>'0 2:00:00');
     DBMS_SCHEDULER.ENABLE(name=>'"SYS"."MONDAY_WINDOW"');
   END;
   /
   ```

   The following example sets the repeat interval to every Monday at 10 AM\.

   ```
   BEGIN
     DBMS_SCHEDULER.DISABLE(name=>'"SYS"."MONDAY_WINDOW"',force=>TRUE);
     DBMS_SCHEDULER.SET_ATTRIBUTE(name=>'"SYS"."MONDAY_WINDOW"', attribute=>'REPEAT_INTERVAL', value=>'freq=daily;byday=MON;byhour=10;byminute=0;bysecond=0');
     DBMS_SCHEDULER.ENABLE(name=>'"SYS"."MONDAY_WINDOW"');
   END;
   /
   ```

## Setting the time zone for Oracle Scheduler jobs<a name="Appendix.Oracle.CommonDBATasks.Scheduler.TimeZone"></a>

To modify the time zone for Oracle Scheduler, you can use the Oracle procedure `dbms_scheduler.set_scheduler_attribute`\. For more information about the `dbms_scheduler` package, see [DBMS\_SCHEDULER](https://docs.oracle.com/en/database/oracle/oracle-database/19/arpls/DBMS_SCHEDULER.html) and [SET\_SCHEDULER\_ATTRIBUTE](https://docs.oracle.com/en/database/oracle/oracle-database/19/arpls/DBMS_SCHEDULER.html#GUID-2AB97BF7-7154-4E6C-933F-B2659B18A907) in the Oracle documentation\.

**To modify the current time zone setting**

1. Connect to the database using a client such as SQL Developer\. For more information, see [Connecting to your DB instance using Oracle SQL developer](USER_ConnectToOracleInstance.SQLDeveloper.md)\.

1. Set the default time zone as following, substituting your time zone for `time_zone_name`\.

   ```
   BEGIN
     DBMS_SCHEDULER.SET_SCHEDULER_ATTRIBUTE(
       attribute => 'default_timezone',
       value => 'time_zone_name'
     );
   END;
   /
   ```

In the following example, you change the time zone to Asia/Shanghai\. 

Start by querying the current time zone, as shown following\.

```
SELECT VALUE FROM DBA_SCHEDULER_GLOBAL_ATTRIBUTE WHERE ATTRIBUTE_NAME='DEFAULT_TIMEZONE';
```

The output shows that the current time zone is ETC/UTC\.

```
VALUE
-------
Etc/UTC
```

Then you set the time zone to Asia/Shanghai\.

```
BEGIN
  DBMS_SCHEDULER.SET_SCHEDULER_ATTRIBUTE(
    attribute => 'default_timezone',
    value => 'Asia/Shanghai'
  );
END;
/
```

For more information about changing the system time zone, see [Oracle time zone](Appendix.Oracle.Options.Timezone.md)\.

## Turning off Oracle Scheduler jobs owned by SYS<a name="Appendix.Oracle.CommonDBATasks.Scheduler.Disabling"></a>

To disable an Oracle Scheduler job owned by the SYS user, use the `rdsadmin.rdsadmin_dbms_scheduler.disable` procedure\. 

This procedure uses the `name` common parameter for Oracle Scheduler tasks\. For more information, see [Common parameters for Oracle Scheduler procedures](#Appendix.Oracle.CommonDBATasks.Scheduler.CommonParameters)\.

The following example disables the `SYS.CLEANUP_ONLINE_IND_BUILD` Oracle Scheduler job\.

```
BEGIN
   rdsadmin.rdsadmin_dbms_scheduler.disable('SYS.CLEANUP_ONLINE_IND_BUILD');
END;
/
```

## Turning on Oracle Scheduler jobs owned by SYS<a name="Appendix.Oracle.CommonDBATasks.Scheduler.Enabling"></a>

To turn on an Oracle Scheduler job owned by SYS, use the `rdsadmin.rdsadmin_dbms_scheduler.enable` procedure\.

This procedure uses the `name` common parameter for Oracle Scheduler tasks\. For more information, see [Common parameters for Oracle Scheduler procedures](#Appendix.Oracle.CommonDBATasks.Scheduler.CommonParameters)\.

The following example enables the `SYS.CLEANUP_ONLINE_IND_BUILD` Oracle Scheduler job\.

```
BEGIN
   rdsadmin.rdsadmin_dbms_scheduler.enable('SYS.CLEANUP_ONLINE_IND_BUILD');
END;
/
```

## Modifying the Oracle Scheduler repeat interval for jobs of CALENDAR type<a name="Appendix.Oracle.CommonDBATasks.Scheduler.Modifying_Calendar"></a>

To modify the repeat interval to modify a SYS\-owned Oracle Scheduler job of `CALENDAR` type, use the `rdsadmin.rdsadmin_dbms_scheduler.disable` procedure\.

This procedure uses the following common parameters for Oracle Scheduler tasks:
+ `name`
+ `attribute`
+ `value`

For more information, see [Common parameters for Oracle Scheduler procedures](#Appendix.Oracle.CommonDBATasks.Scheduler.CommonParameters)\.

The following example modifies the repeat interval of the `SYS.CLEANUP_ONLINE_IND_BUILD` Oracle Scheduler job\.

```
BEGIN
     rdsadmin.rdsadmin_dbms_scheduler.set_attribute(
          name      => 'SYS.CLEANUP_ONLINE_IND_BUILD', 
          attribute => 'repeat_interval', 
          value     => 'freq=daily;byday=FRI,SAT;byhour=20;byminute=0;bysecond=0');
END;
/
```

## Modifying the Oracle Scheduler repeat interval for jobs of NAMED type<a name="Appendix.Oracle.CommonDBATasks.Scheduler.Modifying_Named"></a>

Some Oracle Scheduler jobs use a schedule name instead of an interval\. For this type of jobs, you must create a new named schedule in the master user schema\. Use the standard Oracle `sys.dbms_scheduler.create_schedule` procedure to do this\. Also, use the `rdsadmin.rdsadmin_dbms_scheduler.set_attribute procedure` to assign the new named schedule to the job\. 

This procedure uses the following common parameter for Oracle Scheduler tasks:
+ `name`
+ `attribute`
+ `value`

For more information, see [Common parameters for Oracle Scheduler procedures](#Appendix.Oracle.CommonDBATasks.Scheduler.CommonParameters)\.

The following example modifies the repeat interval of the `SYS.BSLN_MAINTAIN_STATS_JOB` Oracle Scheduler job\.

```
BEGIN
     DBMS_SCHEDULER.CREATE_SCHEDULE (
          schedule_name   => 'rds_master_user.new_schedule',
          start_date      => SYSTIMESTAMP,
          repeat_interval => 'freq=daily;byday=MON,TUE,WED,THU,FRI;byhour=0;byminute=0;bysecond=0',
          end_date        => NULL,
          comments        => 'Repeats daily forever');
END;
/
 
BEGIN
     rdsadmin.rdsadmin_dbms_scheduler.set_attribute (
          name      => 'SYS.BSLN_MAINTAIN_STATS_JOB', 
          attribute => 'schedule_name',
          value     => 'rds_master_user.new_schedule');
END;
/
```

## Turning off autocommit for Oracle Scheduler job creation<a name="Appendix.Oracle.CommonDBATasks.Scheduler.autocommit"></a>

When `DBMS_SCHEDULER.CREATE_JOB` creates Oracle Scheduler jobs, it creates the jobs immediately and commits the changes\. You might need to incorporate the creation of Oracle Scheduler jobs in the user transaction to do the following:
+ Roll back the Oracle Schedule job when the user transaction is rolled back\.
+ Create the Oracle Scheduler job when the main user transaction is committed\.

You can use the procedure `rdsadmin.rdsadmin_dbms_scheduler.set_no_commit_flag` to turn on this behavior\. This procedure takes no parameters\. You can use this procedure in the following RDS for Oracle releases:
+ 21\.0\.0\.0\.ru\-2022\-07\.rur\-2022\-07\.r1 and higher
+ 19\.0\.0\.0\.ru\-2022\-07\.rur\-2022\-07\.r1 and higher

The following example turns off autocommit for Oracle Scheduler, creates an Oracle Scheduler job, and then rolls back the transaction\. Because autocommit is turned off, the database also rolls back the creation of the Oracle Scheduler job\.

```
BEGIN
  rdsadmin.rdsadmin_dbms_scheduler.set_no_commit_flag;
  DBMS_SCHEDULER.CREATE_JOB(job_name   => 'EMPTY_JOB', 
                            job_type   => 'PLSQL_BLOCK', 
                            job_action => 'begin null; end;',
                            auto_drop  => false);
  ROLLBACK;
END;
/

PL/SQL procedure successfully completed.

SELECT * FROM DBA_SCHEDULER_JOBS WHERE JOB_NAME='EMPTY_JOB';

no rows selected
```