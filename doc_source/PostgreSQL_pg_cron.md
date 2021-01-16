# Scheduling maintenance with the PostgreSQL pg\_cron extension<a name="PostgreSQL_pg_cron"></a>

The PostgreSQL `pg_cron` extension provides a scheduler for maintenance commands within a PostgreSQL database\. See also [ What is pg\_cron?](https://github.com/citusdata/pg_cron) in the `pg_cron` documentation\. 

**Note**  
The `pg_cron` extension is supported on Amazon RDS PostgreSQL engine versions 12\.5 and higher\.

**Topics**
+ [Enabling the pg\_cron extension](#PostgreSQL_pg_cron.enable)
+ [Granting permissions to pg\_cron](#PostgreSQL_pg_cron.permissions)
+ [Cron job to manually vacuum a table](#PostgreSQL_pg_cron.vacuum)
+ [Cron job to purge the pg\_cron history](#PostgreSQL_pg_cron.job_run_details)
+ [Disabling logging of pg\_cron history](#PostgreSQL_pg_cron.log_run)
+ [Scheduling a cron job for a database other than postgres](#PostgreSQL_pg_cron.otherDB)
+ [The pg\_cron reference](#PostgreSQL_pg_cron.reference)

## Enabling the pg\_cron extension<a name="PostgreSQL_pg_cron.enable"></a>

Enable the `pg_cron` extension as follows:

1. Modify the parameter group associated with your DB instance and add `pg_cron` to the `shared_preload_libraries` parameter value\. This change requires a DB instance restart for it to take effect\. For more information, see [Modifying parameters in a DB parameter group](USER_WorkingWithParamGroups.md#USER_WorkingWithParamGroups.Modifying)\. 

1. After the DB instance has restarted, perform the following command using an account that has the `rds_superuser` permissions:

   ```
   CREATE EXTENSION pg_cron;
   ```

1. The `pg_cron` scheduler is set in the default PostgreSQL database named `postgres`\. The `pg_cron` objects are created in this `postgres` database and all scheduling actions run in this database\. 

   To schedule jobs to run in other databases within your PostgreSQL DB instance, see the example in [Scheduling a cron job for a database other than postgres](#PostgreSQL_pg_cron.otherDB)\.

## Granting permissions to pg\_cron<a name="PostgreSQL_pg_cron.permissions"></a>

As the rds\_superuser, you can create the `pg_cron` extension and then grant permissions to other users\. For other users to be able to schedule jobs, grant them permissions to objects in the cron schema\.

**Important**  
We recommend that you grant permissions to the cron schema sparingly\. 

To grant others permission to the cron schema, do the following:

```
postgres=> GRANT USAGE ON SCHEMA cron TO other-user;
```

This permission provides the *other\-user* with access to the cron schema to schedule and unschedule cron jobs\. However, for the cron jobs to execute successfully, the user also needs permission to access the objects in the cron jobs\. If the user does not have permission, the job will fail and errors such as the following will appear in the postgresql\.log\. In this example, the user does not have permission to access the `pgbench_accounts` table\.

```
2020-12-08 16:41:00 UTC::@:[30647]:ERROR: permission denied for table pgbench_accounts
2020-12-08 16:41:00 UTC::@:[30647]:STATEMENT: update pgbench_accounts set abalance = abalance + 1
2020-12-08 16:41:00 UTC::@:[27071]:LOG: background worker "pg_cron" (PID 30647) exited with exit code 1
```

There will also be messages in the `cron.job_run_details` table such as the following:

```
postgres=> select jobid, username, status, return_message, start_time from cron.job_run_details where status = 'failed';
 jobid |  username  | status |                   return_message                    |          start_time
-------+------------+--------+-----------------------------------------------------+-------------------------------
   143 | unprivuser | failed | ERROR: permission denied for table pgbench_accounts | 2020-12-08 16:41:00.036268+00
   143 | unprivuser | failed | ERROR: permission denied for table pgbench_accounts | 2020-12-08 16:40:00.050844+00
   143 | unprivuser | failed | ERROR: permission denied for table pgbench_accounts | 2020-12-08 16:42:00.175644+00
   143 | unprivuser | failed | ERROR: permission denied for table pgbench_accounts | 2020-12-08 16:43:00.069174+00
   143 | unprivuser | failed | ERROR: permission denied for table pgbench_accounts | 2020-12-08 16:44:00.059466+00
(5 rows)
```

See also [The pg\_cron tables](#PostgreSQL_pg_cron.tables)\.

## Cron job to manually vacuum a table<a name="PostgreSQL_pg_cron.vacuum"></a>

Autovacuum handles vacuum maintenance for most cases\. See [Working with PostgreSQL autovacuum on Amazon RDS](Appendix.PostgreSQL.CommonDBATasks.Autovacuum.md)\. However, you might want to manually vacuum a specific table at a time of your choosing\. Following is an example of using the `cron.schedule` function to set up a job to use `VACUUM FREEZE` on a specific table every day at 22:00 \(GMT\)\.

```
SELECT cron.schedule('manual vacuum', '0 22 * * *', 'VACUUM FREEZE pgbench_accounts');
 schedule
----------
1
(1 row)
```

After this runs, you can check the history in the `cron.job_run_details` table as follows:

```
postgres=> select * from cron.job_run_details;
 jobid | runid | job_pid | database | username | command | status | return_message | start_time | end_time
-------+-------+---------+----------+----------+----------------------------------------+-----------+----------------+-------------------------------+-------------------------------
 1 | 1 | 3395 | postgres | adminuser| vacuum freeze pgbench_accounts | succeeded | VACUUM | 2020-12-04 21:10:00.050386+00 | 2020-12-04 21:10:00.072028+00
(1 row)
```

Following is an example of viewing the history in the `cron.job_run_details` table to investigate the cause of a job that failed\.

```
postgres=> select * from cron.job_run_details where status = 'failed';
 jobid | runid | job_pid | database | username | command | status | return_message | start_time | end_time
-------+-------+---------+----------+----------+---------------------------------------+--------+--------------------------------------------------+-------------------------------+-------------------------------
 5 | 4 | 30339 | postgres | adminuser| vacuum freeze pgbench_account | failed | ERROR: relation "pgbench_account" does not exist | 2020-12-04 21:48:00.015145+00 | 2020-12-04 21:48:00.029567+00
(1 row)
```

See also [The pg\_cron tables](#PostgreSQL_pg_cron.tables)\.

## Cron job to purge the pg\_cron history<a name="PostgreSQL_pg_cron.job_run_details"></a>

The `cron.job_run_details` table contains a history of cron jobs that can become very large over time\. We recommend that you schedule a job that purges this table\. For example, keeping a week's worth of entries may be sufficient for troubleshooting purposes\. The following example uses the [cron\.schedule](#PostgreSQL_pg_cron.schedule) function to schedule a job that runs every day at midnight to purge the `cron.job_run_details` table\. The job keeps only the last seven days\. Use your rds\_superuser account to schedule the job such as the following:

```
SELECT cron.schedule('0 0 * * *', $$DELETE 
    FROM cron.job_run_details 
    WHERE end_time < now() – interval '7 days'$$);
```

See also [The pg\_cron tables](#PostgreSQL_pg_cron.tables)\.

## Disabling logging of pg\_cron history<a name="PostgreSQL_pg_cron.log_run"></a>

To completely disable writing anything to the `cron.job_run_details` table, modify the parameter group associated with the DB instance and set the `cron.log_run` parameter to off\. The `pg_cron` extension will no longer write to the table and will only produce errors in the postgresql\.log file\. See also Modifying parameters in a DB parameter group\. Use the following command to check the value of the `cron.log_run` parameter\.

```
postgres=> SHOW cron.log_run;
```

See also [The pg\_cron parameters](#PostgreSQL_pg_cron.parameters)\.

## Scheduling a cron job for a database other than postgres<a name="PostgreSQL_pg_cron.otherDB"></a>

The metadata for `pg_cron` is all held in the PostgreSQL default database named `postgres`\. Since background workers are used for running the maintenance cron jobs, you can schedule a job in any of your databases within the RDS DB instance\. See the following example:

1. In the cron database, schedule the job as you normally would using the [cron\.schedule](#PostgreSQL_pg_cron.schedule) function:

   ```
   postgres=> SELECT cron.schedule('database1 manual vacuum', '29 03 * * *', 'vacuum freeze test_table');
   ```

1. As an rds\_superuser, update the database column for the job you just created so that it runs in another database within your RDS DB instance\.

   ```
   postgres=> UPDATE cron.job SET database = 'database1' WHERE jobid = 106;
   ```

1.  Verify by querying the `cron.job` table\.

   ```
   postgres=> select * from cron.job;
    jobid | schedule | command | nodename | nodeport | database | username | active | jobname
   -------+-------------+----------------------------------------+-----------+----------+-----------+-----------+--------+-------------------------
    106 | 29 03 * * * | vacuum freeze test_table | localhost | 8192 | database1 | adminuser | t | database1 manual vacuum
    1 | 59 23 * * * | vacuum freeze pgbench_accounts | localhost | 8192 | postgres | adminuser | t | manual vacuum
   (2 rows)
   ```

**Note**  
When you add a cron job that you intend to have execute on a different database, it is possible for the job to attempt to run in the default database \(`postgres`\) before you update the correct database column\. If the user name has permissions, it would successfully execute in the default database\.

## The pg\_cron reference<a name="PostgreSQL_pg_cron.reference"></a>

The following parameters, functions, and tables are available with the `pg_cron` extension\. For more information, see [What is pg\_cron?](https://github.com/citusdata/pg_cron) in the `pg_cron` documentation\.

**Topics**
+ [The pg\_cron parameters](#PostgreSQL_pg_cron.parameters)
+ [cron\.schedule function](#PostgreSQL_pg_cron.schedule)
+ [cron\.unschedule function](#PostgreSQL_pg_cron.unschedule)
+ [The pg\_cron tables](#PostgreSQL_pg_cron.tables)

### The pg\_cron parameters<a name="PostgreSQL_pg_cron.parameters"></a>

Following is the list of parameters to control the `pg_cron` extension behavior\. 


| Parameter | Description | 
| --- | --- | 
| `cron.database_name` |  The database in which `pg_cron` metadata is kept\.  | 
| cron\.host |  The hostname to connect to PostgresSQL\. You can't modify this value\.  | 
| cron\.log\_run |  Log into the `job_run_details` table all jobs that run \. Values are `on` or `off`\.  See also [The pg\_cron tables](#PostgreSQL_pg_cron.tables)\.  | 
| cron\.log\_statement |  Log all cron statements prior to execution\. Values are `on` or `off`\.  | 
| cron\.max\_running\_jobs |  The maximum number of jobs that can run concurrently\.  | 
| cron\.use\_background\_workers |  Use background workers instead of client sessions\. You can't modify this value\.  | 

You can use the following SQL command to display these parameters and their values:

```
postgres=> SELECT name, setting, short_desc FROM pg_settings WHERE name LIKE 'cron.%' ORDER BY name;
```

### cron\.schedule function<a name="PostgreSQL_pg_cron.schedule"></a>

This function schedules a cron job\. The job is initially scheduled in the default `postgres` database\. The function returns a `bigint` value representing the job identifier\. To schedule jobs to run in other databases within your PostgreSQL DB instance, see the example in [Scheduling a cron job for a database other than postgres](#PostgreSQL_pg_cron.otherDB)\.

The function has two syntax formats\.

**Syntax**  

```
cron.schedule (job_name,
    schedule,
    command
);

cron.schedule (schedule,
    command
);
```

**Parameters**      
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/PostgreSQL_pg_cron.html)

**Examples**  

```
postgres=> SELECT cron.schedule ('test','0 10 * * *', 'VACUUM pgbench_history');
 schedule
----------
      145
(1 row)

postgres=> SELECT cron.schedule ('0 15 * * *', 'VACUUM pgbench_accounts');
 schedule
----------
      146
(1 row)
```

### cron\.unschedule function<a name="PostgreSQL_pg_cron.unschedule"></a>

This function deletes a cron job\. You can either pass in the `job_name` or the `job_id`\. A policy makes sure you are the owner in order to unschedule the job\. The function returns a Boolean indicating success or failure\.

The function has the following syntax formats:

**Syntax**  

```
cron.unschedule (job_id);

cron.unschedule (job_name);
```

**Parameters**      
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/PostgreSQL_pg_cron.html)

**Examples**  

```
postgres=> select cron.unschedule(108);
 unschedule
------------
 t
(1 row)

postgres=> select cron.unschedule('test');
 unschedule
------------
 t
(1 row)
```

### The pg\_cron tables<a name="PostgreSQL_pg_cron.tables"></a>

The following tables are created and used to schedule the cron jobs and record how the execution completed\. 


| Table Name | Description | 
| --- | --- | 
| cron\.job |  Contains the metadata about each scheduled job\. Most interactions with this table should be done via the `cron.schedule` and `cron.unschedule` functions\.  We do not recommend giving update/insert privileges directly to this table\. Doing so would allow the user to update the `username` column to run as rds\-superuser\.   | 
| cron\.job\_run\_details |  Contains historic information about past scheduled job executions\. This is useful to investigate the status, return messages, and start and end time from the job execution\.  This table should be purged on a regular basis so that it doesn’t grow indefinitely\. For an example, see [Cron job to purge the pg\_cron history](#PostgreSQL_pg_cron.job_run_details)\.   | 