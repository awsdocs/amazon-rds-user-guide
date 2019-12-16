# Common DBA Oracle Scheduler Tasks for Oracle DB Instances<a name="Appendix.Oracle.CommonDBATasks.Scheduler"></a>

Some SYS\-owned scheduler jobs can interfere with normal database operations, and Oracle Support recommends they be disabled or the job schedule be modified\. You can use the Amazon RDS package `rdsadmin.rdsadmin_dbms_scheduler` to perform tasks for SYS\-owned Oracle Scheduler jobs\.

These procedures are supported for the following Amazon RDS for Oracle DB engine versions:
+ 11\.2\.0\.4\.v21 or higher 11\.2 versions
+ 12\.1\.0\.2\.v17 or higher 12\.1 versions
+ 12\.2\.0\.2\.ru\-2019\-07\.rur\-2019\-07\.r1 or higher 12\.2 versions

**Topics**
+ [Common Parameters for Oracle Scheduler Procedures](#Appendix.Oracle.CommonDBATasks.Scheduler.CommonParameters)
+ [Modifying DBMS\_SCHEDULER Jobs](#Appendix.Oracle.CommonDBATasks.ModifyScheduler)
+ [Disabling SYS\-Owned Oracle Scheduler Jobs](#Appendix.Oracle.CommonDBATasks.Scheduler.Disabling)
+ [Enabling SYS\-Owned Oracle Scheduler Jobs](#Appendix.Oracle.CommonDBATasks.Scheduler.Enabling)
+ [Modifying the Repeat Interval for Jobs of CALENDAR Type](#Appendix.Oracle.CommonDBATasks.Scheduler.Modifying_Calendar)
+ [Modifying the Repeat Interval for Jobs of NAMED Type](#Appendix.Oracle.CommonDBATasks.Scheduler.Modifying_Named)

## Common Parameters for Oracle Scheduler Procedures<a name="Appendix.Oracle.CommonDBATasks.Scheduler.CommonParameters"></a>

To perform tasks with Oracle Scheduler, use procedures in the Amazon RDS package `rdsadmin.rdsadmin_dbms_scheduler`\. Several parameters are common to the procedures in the package\. The package has the following common parameters\.


****  

| Parameter Name | Data Type | Valid Values | Default | Required | Description | 
| --- | --- | --- | --- | --- | --- | 
| `name` | varchar2 | `'SYS.BSLN_MAINTAIN_STATS_JOB'`,`'SYS.CLEANUP_ONLINE_IND_BUILD'`  | — | Yes |  The name of the job to modify\.  Currently, you can only modify `SYS.CLEANUP_ONLINE_IND_BUILD` and `SYS.BSLN_MAINTAIN_STATS_JOB` jobs\.   | 
| `attribute` | varchar2 | `'REPEAT_INTERVAL'`,`'SCHEDULE_NAME'` | – | Yes |  Attribute to modify\. To modify the repeat interval for the job, specify `'REPEAT_INTERVAL'`\. To modify the schedule name for the job, specify `'SCHEDULE_NAME'`\.  | 
| `value` | varchar2 | A valid schedule interval or schedule name, depending on attribute used\. | – | Yes |  The new value of the attribute\.  | 

## Modifying DBMS\_SCHEDULER Jobs<a name="Appendix.Oracle.CommonDBATasks.ModifyScheduler"></a>

You can still use the Oracle procedure `dbms_scheduler.set_attribute` to modify certain components of the scheduler\. For more information, see [DBMS\_SCHEDULER](https://docs.oracle.com/database/121/ARPLS/d_sched.htm#ARPLS72235) and [SET\_ATTRIBUTE Procedure](https://docs.oracle.com/database/121/ARPLS/d_sched.htm#ARPLS72399) in the Oracle documentation\. 

When working with Amazon RDS DB instances, prepend the schema name `SYS` to the object name\. The following example sets the resource plan attribute for the Monday window object\. 

```
begin
    dbms_scheduler.set_attribute(
        name      => 'SYS.MONDAY_WINDOW',
        attribute => 'RESOURCE_PLAN',
        value     => 'resource_plan_1');
end;
/
```

**Note**  
Some SYS\-owned Oracle Scheduler jobs can interfere with normal database operations\. Oracle Support recommends that they be disabled or that the job schedule be modified\. Amazon RDS for Oracle doesn't provide the required privileges to modify SYS\-owned Oracle Scheduler jobs using the `DBMS_SCHEDULER` package\. Instead, you can use the procedures in the Amazon RDS package `rdsadmin.rdsadmin_dbms_scheduler` to perform tasks for SYS\-owned Oracle Scheduler jobs\. For information about using these procedures, see [Common DBA Oracle Scheduler Tasks for Oracle DB Instances](#Appendix.Oracle.CommonDBATasks.Scheduler)\.

## Disabling SYS\-Owned Oracle Scheduler Jobs<a name="Appendix.Oracle.CommonDBATasks.Scheduler.Disabling"></a>

To disable a SYS\-owned Oracle Scheduler job, use the `rdsadmin.rdsadmin_dbms_scheduler.disable` procedure\. 

This procedure uses the `name` common parameter for Oracle Scheduler tasks\. For more information, see [Common Parameters for Oracle Scheduler Procedures](#Appendix.Oracle.CommonDBATasks.Scheduler.CommonParameters)\.

The following example disables the `SYS.CLEANUP_ONLINE_IND_BUILD` Oracle Scheduler job\.

```
BEGIN
   rdsadmin.rdsadmin_dbms_scheduler.disable('SYS.CLEANUP_ONLINE_IND_BUILD');
END;
/
```

## Enabling SYS\-Owned Oracle Scheduler Jobs<a name="Appendix.Oracle.CommonDBATasks.Scheduler.Enabling"></a>

To enable a SYS\-owned Oracle Scheduler job, use the `rdsadmin.rdsadmin_dbms_scheduler.disable` procedure\.

This procedure uses the `name` common parameter for Oracle Scheduler tasks\. For more information, see [Common Parameters for Oracle Scheduler Procedures](#Appendix.Oracle.CommonDBATasks.Scheduler.CommonParameters)\.

The following example enables the `SYS.CLEANUP_ONLINE_IND_BUILD` Oracle Scheduler job\.

```
BEGIN
   rdsadmin.rdsadmin_dbms_scheduler.enable('SYS.CLEANUP_ONLINE_IND_BUILD');
END;
/
```

## Modifying the Repeat Interval for Jobs of CALENDAR Type<a name="Appendix.Oracle.CommonDBATasks.Scheduler.Modifying_Calendar"></a>

To modify the repeat interval to modify a SYS\-owned Oracle Scheduler job of `CALENDAR` type, use the `rdsadmin.rdsadmin_dbms_scheduler.disable` procedure\.

This procedure uses the following common parameters for Oracle Scheduler tasks:
+ `name`
+ `attribute`
+ `value`

For more information, see [Common Parameters for Oracle Scheduler Procedures](#Appendix.Oracle.CommonDBATasks.Scheduler.CommonParameters)\.

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

## Modifying the Repeat Interval for Jobs of NAMED Type<a name="Appendix.Oracle.CommonDBATasks.Scheduler.Modifying_Named"></a>

Some Oracle Scheduler jobs use a schedule name instead of an interval\. For these type of jobs, you must create a new named schedule in the master user schema\. Use the standard Oracle `sys.dbms_scheduler.create_schedule` procedure to do this\. Also, use the `rdsadmin.rdsadmin_dbms_scheduler.set_attribute procedure` to assign the new named schedule to the job\. 

This procedure uses the following common parameter for Oracle Scheduler tasks:
+ `name`
+ `attribute`
+ `value`

For more information, see [Common Parameters for Oracle Scheduler Procedures](#Appendix.Oracle.CommonDBATasks.Scheduler.CommonParameters)\.

The following example modifies the repeat interval of the `SYS.BSLN_MAINTAIN_STATS_JOB` Oracle Scheduler job\.

```
BEGIN
     dbms_scheduler.create_schedule (
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