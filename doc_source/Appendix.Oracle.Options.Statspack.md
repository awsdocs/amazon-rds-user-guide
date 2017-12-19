# Oracle Statspack<a name="Appendix.Oracle.Options.Statspack"></a>

The Oracle Statspack option installs and enables the Oracle Statspack performance statistics feature\. Oracle Statspack is a collection of SQL, PL/SQL, and SQL\*Plus scripts that collect, store, and display performance data\. For information about using Oracle Statspack, see [Oracle Statspack](http://docs.oracle.com/cd/E13160_01/wli/docs10gr3/dbtuning/statsApdx.html) in the Oracle documentation\. 

**Note**  
Oracle Statspack is no longer supported by Oracle and has been replaced by the more advanced Automatic Workload Repository \(AWR\)\. AWR is available only for Oracle Enterprise Edition customers who have purchased the Diagnostics Pack\. Oracle Statspack can be used with any Oracle DB engine on Amazon RDS\. 

The following steps show you how to work with Oracle Statspack on Amazon RDS:

1. If you have an existing DB instance that has the PERFSTAT account already created and you want to use Oracle Statspack with it, you must drop the PERFSTAT account before adding the Statspack option to the option group associated with your DB instance\. If you attempt to add the Statspack option to an option group associated with a DB instance that already has the PERFSTAT account created, you get an error and the RDS event RDS\-Event\-0058 is generated\.

   If you have already installed Statspack, and the PERFSTAT account is associated with Statspack, then skip this step, and do not drop the PERFSTAT user\.

   You can drop the PERFSTAT account by running the following command:

   ```
   DROP USER perfstat CASCADE;
   ```

1. Add the Statspack option to an option group and then associate that option group with your DB instance\. Amazon RDS installs the Statspack scripts on the DB instance and then sets up the PERFSTAT user account, the account you use to run the Statspack scripts\. If you have installed Statspack, skip this step\. 

1. After Amazon RDS has installed Statspack on your DB instance, you must log in to the DB instance using your master user name and master password\. You must then reset the PERFSTAT password from the randomly generated value Amazon RDS created when Statspack was installed\. After you have reset the PERFSTAT password, you can log in using the PERFSTAT user account and run the Statspack scripts\. 

   Use the following command to reset the password:

   ```
   ALTER USER perfstat IDENTIFIED BY <new_password> ACCOUNT UNLOCK;
   ```

1. After you have logged on using the PERFSTAT account, you can either manually create a Statspack snapshot or create a job that will take a Statspack snapshot after a given time interval\. For example, the following job creates a Statspack snapshot every hour:  

   ```
   variable jn number;
   execute dbms_job.submit(:jn, 'statspack.snap;',sysdate,'trunc(SYSDATE+1/24,''HH24'')');
   commit;
   ```

1. Once you have created at least two Statspack snapshots, you can view them using the following query:  

   ```
   select snap_id, snap_time from stats$snapshot order by 1;
   ```

1. To create a Statspack report, you choose two snapshots to analyze and run the following Amazon RDS command:

   ```
   exec RDSADMIN.RDS_RUN_SPREPORT(<begin snap>,<end snap>);
   ```

   For example, the following Amazon RDS command would create a report based on the interval between Statspack snapshots 1 and 7:

   ```
   exec RDSADMIN.RDS_RUN_SPREPORT(1,7);
   ```

The file name of the Statspack report that is generated includes the number of the two Statspack snapshots used\. For example, a report file created using Statspack snapshots 1 and 7 would be named ORCL\_spreport\_1\_7\.lst\. You can download the Statspack report by selecting the report in the Log section of the RDS console and clicking **Download** or you can use the trace file procedures explained in [Working with Oracle Trace Files](USER_LogAccess.Concepts.Oracle.md#USER_LogAccess.Concepts.Oracle.WorkingWithTracefiles)\. 

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/statspack1.png)

If an error occurs when producing the report, an error file is created using the same naming conventions but with an extension of \.err\. For example, if an error occurred while creating a report using Statspack snapshots 1 and 7, the report file would be named ORCL\_spreport\_1\_7\.err\. You can download the error report by selecting the report in the Log section of the RDS console and clicking **Download** or use the trace file procedures explained in [Working with Oracle Trace Files](USER_LogAccess.Concepts.Oracle.md#USER_LogAccess.Concepts.Oracle.WorkingWithTracefiles)\.

Oracle Statspack does some basic checking before running the report, so you could also see error messages displayed at the command prompt\. For example, if you attempt to generate a report based on an invalid range, such as the beginning Statspack snapshot value is larger than the ending Statspack snapshot value, the error message is displayed at the command prompt and no error file is created\.

```
exec RDSADMIN.RDS_RUN_SPREPORT(2,1);
*
ERROR at line 1:
ORA-20000: Invalid snapshot IDs. Find valid ones in perfstat.stats$snapshot.
```

If you use an invalid number for one of the Statspack snapshots, the error message will also be displayed at the command prompt\. For example, if you have 20 Statspack snapshots but request that a report be run using Statspack snapshots 1 and 50, the command prompt will display an error\.

```
exec RDSADMIN.RDS_RUN_SPREPORT(1,50);
*
ERROR at line 1:
ORA-20000: Could not find both snapshot IDs
```

For more information about how to use Oracle Statspack, including information on adjusting the amount of data captured by adjusting the snapshot level, go to the Oracle [Statspack documentation page](http://docs.oracle.com/cd/B10500_01/server.920/a96533/statspac.htm)\.

To remove Oracle Statspack files, use the following command: 

```
execute statspack.purge(<begin snap>, <end snap>); 
```

## Related Topics<a name="Appendix.Oracle.Options.Statspack.Related"></a>

+ [Working with Option Groups](USER_WorkingWithOptionGroups.md)

+ [Options for Oracle DB Instances](Appendix.Oracle.Options.md)