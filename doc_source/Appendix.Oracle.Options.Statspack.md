# Oracle Statspack<a name="Appendix.Oracle.Options.Statspack"></a>

The Oracle Statspack option installs and enables the Oracle Statspack performance statistics feature\. Oracle Statspack is a collection of SQL, PL/SQL, and SQL\*Plus scripts that collect, store, and display performance data\. For information about using Oracle Statspack, see [Oracle Statspack](http://docs.oracle.com/cd/E13160_01/wli/docs10gr3/dbtuning/statsApdx.html) in the Oracle documentation\.

**Note**  
Oracle Statspack is no longer supported by Oracle and has been replaced by the more advanced Automatic Workload Repository \(AWR\)\. AWR is available only for Oracle Enterprise Edition customers who have purchased the Diagnostics Pack\. You can use Oracle Statspack with any Oracle DB engine on Amazon RDS\. You can't run Oracle Statspack on Amazon RDS read replicas\. 

## Setting Up Oracle Statspack<a name="Appendix.Oracle.Options.Statspack.setting-up"></a>

To run Statspack scripts, you must add the Statspack option\.

**To set up Oracle Statspack**

1. In a SQL client, log in to the Oracle DB with an administrative account\.

1. Do either of the following actions, depending on whether Statspack is installed:
   + If Statspack is installed, and the `PERFSTAT` account is associated with Statspack, skip to Step 4\.
   + If Statspack is not installed, and the `PERFSTAT` account exists, drop the account as follows:

     ```
     DROP USER PERFSTAT CASCADE;
     ```

     Otherwise, attempting to add the Statspack option generates an error and `RDS-Event-0058`\.

1. Add the Statspack option to an option group\. See [Adding an Option to an Option Group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.AddOption)\.

   Amazon RDS automatically installs the Statspack scripts on the DB instance and then sets up the `PERFSTAT` account\.

1. Reset the password using the following SQL statement, replacing *pwd* with your new password:

   ```
   ALTER USER PERFSTAT IDENTIFIED BY pwd ACCOUNT UNLOCK;
   ```

   You can log in using the `PERFSTAT` user account and run the Statspack scripts\.

1. Do either of the following actions, depending on your DB engine version:
   + If you are using Oracle 18c or lower, skip this step\.
   + If you are using Oracle 19c or higher, grant the `CREATE JOB` privilege to the `PERFSTAT` account using the following statement:

     ```
     GRANT CREATE JOB TO PERFSTAT;
     ```

1. Ensure that idle wait events in the `PERFSTAT.STATS$IDLE_EVENT` table are populated\.

   Because of Oracle Bug 28523746, the idle wait events in `PERFSTAT.STATS$IDLE_EVENT` may not be populated\. To ensure all idle events are available, run the following statement:

   ```
   INSERT INTO PERFSTAT.STATS$IDLE_EVENT (EVENT)
   SELECT NAME FROM V$EVENT_NAME WHERE WAIT_CLASS='Idle'
   MINUS
   SELECT EVENT FROM PERFSTAT.STATS$IDLE_EVENT;
   COMMIT;
   ```

## Generating Statspack Reports<a name="Appendix.Oracle.Options.Statspack.generating-reports"></a>

A Statspack report compares two snapshots\.

**To generate Statspack reports**

1. In a SQL client, log in to the Oracle DB with the `PERFSTAT` account\.

1. Create a snapshot using either of the following techniques:
   + Create a Statspack snapshot manually\.
   + Create a job that takes a Statspack snapshot after a given time interval\. For example, the following job creates a Statspack snapshot every hour:

     ```
     VARIABLE jn NUMBER;
     exec dbms_job.submit(:jn, 'statspack.snap;',SYSDATE,'TRUNC(SYSDATE+1/24,''HH24'')');
     COMMIT;
     ```

1. View the snapshots using the following query:

   ```
   SELECT SNAP_ID, SNAP_TIME FROM STATS$SNAPSHOT ORDER BY 1;
   ```

1. Run the Amazon RDS procedure `rdsadmin.rds_run_spreport`, replacing *begin\_snap* and *end\_snap* with the snapshot IDs:

   ```
   exec rdsadmin.rds_run_spreport(begin_snap,end_snap);
   ```

   For example, the following command creates a report based on the interval between Statspack snapshots 1 and 2:

   ```
   exec rdsadmin.rds_run_spreport(1,2);
   ```

   The file name of the Statspack report includes the number of the two snapshots\. For example, a report file created using Statspack snapshots 1 and 2 would be named `ORCL_spreport_1_2.lst`\.

1. Monitor the output for errors\.

   Oracle Statspack performs checks before running the report\. Therefore, you could also see error messages in the command output\. For example, you might try to generate a report based on an invalid range, where the beginning Statspack snapshot value is larger than the ending value\. In this case, the output shows the error message, but the DB engine does not generate an error file\.

   ```
   exec rdsadmin.rds_run_spreport(2,1);
   *
   ERROR at line 1:
   ORA-20000: Invalid snapshot IDs. Find valid ones in perfstat.stats$snapshot.
   ```

   If you use an invalid number a Statspack snapshot, the output shows an error\. For example, if you try to generate a report for snapshots 1 and 50, but snapshot 50 doesn't exist, the output shows an error\.

   ```
   exec rdsadmin.rds_run_spreport(1,50);
   *
   ERROR at line 1:
   ORA-20000: Could not find both snapshot IDs
   ```

1. \(Optional\) 

   To retrieve the report, call the trace file procedures, as explained in [Working with Oracle Trace Files](USER_LogAccess.Concepts.Oracle.md#USER_LogAccess.Concepts.Oracle.WorkingWithTracefiles)\. 

   Alternatively, download the Statspack report from the RDS console\. Go to the **Log** section of the DB instance details and choose **Download**:  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/statspack1.png)

   If an error occurs while generating a report, the DB engine uses the same naming conventions as for a report but with an extension of \.err\. For example, if an error occurred while creating a report using Statspack snapshots 1 and 7, the report file would be named `ORCL_spreport_1_7.err`\. You can download the error report using the same techniques as for a standard Snapshot report\.

## Removing Statspack Files<a name="Appendix.Oracle.Options.Statspack.removing-files"></a>

To remove Oracle Statspack files, use the following command:

```
exec statspack.purge(begin snap, end snap); 
```