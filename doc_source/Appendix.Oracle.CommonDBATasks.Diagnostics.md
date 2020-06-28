# Common DBA Diagnostic Tasks for Oracle DB Instances<a name="Appendix.Oracle.CommonDBATasks.Diagnostics"></a>

Oracle Database includes a fault diagnosability infrastructure that you can use to investigate database problems\. In Oracle terminology, a *problem* is a critical error such as a code bug or data corruption\. An *incident* is the occurrence of a problem\. If the same error occurs three times, then the infrastructure shows three incidents of this problem\. For more information, see [Diagnosing and Resolving Problems](https://docs.oracle.com/en/database/oracle/oracle-database/19/admin/diagnosing-and-resolving-problems.html#GUID-8DEB1BE0-8FB9-4FB2-A19A-17CF6F5791C3) in the Oracle Database documentation\.

The Automatic Diagnostic Repository Command Interpreter \(ADRCI\) utility is an Oracle command\-line tool that you use to manage diagnostic data\. For example, you can use this tool to investigate problems and package diagnostic data\. An *incident package* includes diagnostic data for an incident or all incidents that reference a specific problem\. You can upload an incident package, which is implemented as a \.zip file, to Oracle Support\.

To deliver a managed service experience, Amazon RDS doesn't provide shell access to ADRCI\. To perform diagnostic tasks for your Oracle instance, instead use the Amazon RDS package `rdsadmin.rdsadmin_adrci_util`\.

By using the functions in `rdsadmin_adrci_util`, you can list and package problems and incidents, and also show trace files\. All functions return a task ID\. This ID forms part of the name of log file that contains the ADRCI output, as in `dbtask-task_id.log`\. The log file resides in the BDUMP directory\.

## Common Parameters for Diagnostic Procedures<a name="Appendix.Oracle.CommonDBATasks.CommonDiagParameters"></a>

To perform diagnostic tasks, use functions in the Amazon RDS package `rdsadmin.rdsadmin_adrci_util`\. The package has the following common parameters\.


****  

| Parameter Name | Data Type | Valid Values | Default | Required | Description | 
| --- | --- | --- | --- | --- | --- | 
| `incident_id` | number |  A valid incident ID or null  | Null | No |  If the value is null, the function shows all incidents\. If the value isn't null and represents a valid incident ID, the function shows the specified incident\.   | 
| `problem_id` | number | A valid problem ID or null | Null | No |  If the value is null, the function shows all problems\. If the value isn't null and represents a valid problem ID, the function shows the specified problem\.  | 
|  `last`  |  number  |  A valid integer greater than 0 or null  |  Null  |  No  |  If the value is null, then the function displays at most 50 items\. If the value isn't null, the function displays the specified number\.  | 

## Listing Incidents<a name="Appendix.Oracle.CommonDBATasks.Incidents"></a>

To list diagnostic incidents for Oracle, use the Amazon RDS function `rdsadmin.rdsadmin_adrci_util.list_adrci_incidents`\. You can list incidents in either basic or detailed mode\. By default, the function lists the 50 most recent incidents\.

This function uses the following common parameters:
+  `incident_id`
+  `problem_id`

If you specify both of the preceding parameters, `incident_id` overrides `problem_id`\. For more information, see [Common Parameters for Diagnostic Procedures](#Appendix.Oracle.CommonDBATasks.CommonDiagParameters)\.

This function uses the following additional parameter\.


****  

| Parameter Name | Data Type | Valid Values | Default | Required | Description | 
| --- | --- | --- | --- | --- | --- | 
|  `detail`  |  boolean  | TRUE or FALSE |  `FALSE`  |  No  |  If `TRUE`, the function lists incidents in detail mode\. If `FALSE`, the function lists incidents in basic mode\.  | 

To list all incidents, call the `rdsadmin.rdsadmin_adrci_util.list_adrci_incidents` function without any arguments\. You can store the output in a SQL client variable\.

```
SQL> var task_id varchar2(80);
SQL> exec :task_id := rdsadmin.rdsadmin_adrci_util.list_adrci_incidents;

PL/SQL procedure successfully completed.
```

To get the task ID, specify the variable in a query of the `dual` table\.

```
SQL> select :task_id from dual;

:TASK_ID
------------------
1590786706158-3126
```

To read the log file, call the Amazon RDS procedure `rdsadmin.rds_file_util.read_text_file`\. Supply the task ID as part of the file name\. The following output shows three incidents: 53523, 53522, and 53521\.

```
SQL> select * from table(rdsadmin.rds_file_util.read_text_file('BDUMP', 'dbtask-'||:task_id||'.log'));

TEXT
-------------------------------------------------------------------------------------------------------------------------
2020-05-29 21:11:46.193 UTC [INFO ] Listing ADRCI incidents.
2020-05-29 21:11:46.256 UTC [INFO ]
ADR Home = /rdsdbdata/log/diag/rdbms/orcl_a/ORCL:
*************************************************************************
INCIDENT_ID PROBLEM_KEY                                                 CREATE_TIME
----------- ----------------------------------------------------------- ----------------------------------------
53523       ORA 700 [EVENT_CREATED_INCIDENT] [942] [SIMULATED_ERROR_003 2020-05-29 20:15:20.928000 +00:00
53522       ORA 700 [EVENT_CREATED_INCIDENT] [942] [SIMULATED_ERROR_002 2020-05-29 20:15:15.247000 +00:00
53521       ORA 700 [EVENT_CREATED_INCIDENT] [942] [SIMULATED_ERROR_001 2020-05-29 20:15:06.047000 +00:00
3 rows fetched


2020-05-29 21:11:46.256 UTC [INFO ] The ADRCI incidents were successfully listed.
2020-05-29 21:11:46.256 UTC [INFO ] The task finished successfully.

14 rows selected.
```

To list a particular incident, specify its ID using the `incident_id` parameter\. In the following example, you query the log file for incident 53523 only\.

```
SQL> exec :task_id := rdsadmin.rdsadmin_adrci_util.list_adrci_incidents(incident_id=>53523);

PL/SQL procedure successfully completed.

SQL> select * from table(rdsadmin.rds_file_util.read_text_file('BDUMP', 'dbtask-'||:task_id||'.log'));

TEXT
------------------------------------------------------------------------------------------------------------------
2020-05-29 21:15:25.358 UTC [INFO ] Listing ADRCI incidents.
2020-05-29 21:15:25.426 UTC [INFO ]
ADR Home = /rdsdbdata/log/diag/rdbms/orcl_a/ORCL:
*************************************************************************
INCIDENT_ID          PROBLEM_KEY                                                 CREATE_TIME
-------------------- ----------------------------------------------------------- ---------------------------------
53523                ORA 700 [EVENT_CREATED_INCIDENT] [942] [SIMULATED_ERROR_003 2020-05-29 20:15:20.928000 +00:00
1 rows fetched


2020-05-29 21:15:25.427 UTC [INFO ] The ADRCI incidents were successfully listed.
2020-05-29 21:15:25.427 UTC [INFO ] The task finished successfully.

12 rows selected.
```

## Listing Problems<a name="Appendix.Oracle.CommonDBATasks.Problems"></a>

To list diagnostic problems for Oracle, use the Amazon RDS function `rdsadmin.rdsadmin_adrci_util.list_adrci_problems`\.

By default, the function lists the 50 most recent problems\. 

This function uses the common parameter `problem_id`\. For more information, see [Common Parameters for Diagnostic Procedures](#Appendix.Oracle.CommonDBATasks.CommonDiagParameters)\.

To get the task ID for all problems, call the `rdsadmin.rdsadmin_adrci_util.list_adrci_problems` function without any arguments, and store the output in a SQL client variable\.

```
SQL> exec :task_id := rdsadmin.rdsadmin_adrci_util.list_adrci_problems;

PL/SQL procedure successfully completed.
```

To read the log file, call the `rdsadmin.rds_file_util.read_text_file` function, supplying the task ID as part of the file name\. In the following output, the log file shows three problems: 1, 2, and 3\.

```
SQL> select * from table(rdsadmin.rds_file_util.read_text_file('BDUMP', 'dbtask-'||:task_id||'.log'));

TEXT
----------------------------------------------------------------------------------------------------------------------
2020-05-29 21:18:50.764 UTC [INFO ] Listing ADRCI problems.
2020-05-29 21:18:50.829 UTC [INFO ]
ADR Home = /rdsdbdata/log/diag/rdbms/orcl_a/ORCL:
*************************************************************************
PROBLEM_ID   PROBLEM_KEY                                                 LAST_INCIDENT        LASTINC_TIME
---------- ----------------------------------------------------------- ------------- ---------------------------------
2          ORA 700 [EVENT_CREATED_INCIDENT] [942] [SIMULATED_ERROR_003 53523         2020-05-29 20:15:20.928000 +00:00
3          ORA 700 [EVENT_CREATED_INCIDENT] [942] [SIMULATED_ERROR_002 53522         2020-05-29 20:15:15.247000 +00:00
1          ORA 700 [EVENT_CREATED_INCIDENT] [942] [SIMULATED_ERROR_001 53521         2020-05-29 20:15:06.047000 +00:00
3 rows fetched


2020-05-29 21:18:50.829 UTC [INFO ] The ADRCI problems were successfully listed.
2020-05-29 21:18:50.829 UTC [INFO ] The task finished successfully.

14 rows selected.
```

In the following example, you list problem 3 only\.

```
SQL> exec :task_id := rdsadmin.rdsadmin_adrci_util.list_adrci_problems(problem_id=>3);

PL/SQL procedure successfully completed.
```

To read the log file for problem 3, call `rdsadmin.rds_file_util.read_text_file`\. Supply the task ID as part of the file name\.

```
SQL> select * from table(rdsadmin.rds_file_util.read_text_file('BDUMP', 'dbtask-'||:task_id||'.log'));

TEXT
-------------------------------------------------------------------------
2020-05-29 21:19:42.533 UTC [INFO ] Listing ADRCI problems.
2020-05-29 21:19:42.599 UTC [INFO ]
ADR Home = /rdsdbdata/log/diag/rdbms/orcl_a/ORCL:
*************************************************************************
PROBLEM_ID PROBLEM_KEY                                                 LAST_INCIDENT LASTINC_TIME
---------- ----------------------------------------------------------- ------------- ---------------------------------
3          ORA 700 [EVENT_CREATED_INCIDENT] [942] [SIMULATED_ERROR_002 53522         2020-05-29 20:15:15.247000 +00:00
1 rows fetched


2020-05-29 21:19:42.599 UTC [INFO ] The ADRCI problems were successfully listed.
2020-05-29 21:19:42.599 UTC [INFO ] The task finished successfully.

12 rows selected.
```

## Creating Incident Packages<a name="Appendix.Oracle.CommonDBATasks.IncPackages"></a>

You can create incident packages using the Amazon RDS function `rdsadmin.rdsadmin_adrci_util.create_adrci_package`\. The output is a \.zip file that you can supply to Oracle Support\.

This function uses the following common parameters:
+ `problem_id`
+ `incident_id`

Make sure to specify one of the preceding parameters\. If you specify both parameters, `incident_id` overrides `problem_id`\. For more information, see [Common Parameters for Diagnostic Procedures](#Appendix.Oracle.CommonDBATasks.CommonDiagParameters)\.

To create a package for a specific incident, call the Amazon RDS function `rdsadmin.rdsadmin_adrci_util.create_adrci_package` with the `incident_id` parameter\. The following example creates a package for incident 53523\.

```
SQL> exec :task_id := rdsadmin.rdsadmin_adrci_util.create_adrci_package(incident_id=>53523);

PL/SQL procedure successfully completed.
```

To read the log file, call the `rdsadmin.rds_file_util.read_text_file`\. You can supply the task ID as part of the file name\. The output shows that you generated incident package `ORA700EVE_20200529212043_COM_1.zip`\.

```
SSQL> select * from table(rdsadmin.rds_file_util.read_text_file('BDUMP', 'dbtask-'||:task_id||'.log'));

TEXT
--------------------------------------------------------------------------------------------------------------------------------------
2020-05-29 21:20:43.031 UTC [INFO ] The ADRCI package is being created.
2020-05-29 21:20:47.641 UTC [INFO ] Generated package 1 in file /rdsdbdata/log/trace/ORA700EVE_20200529212043_COM_1.zip, mode complete
2020-05-29 21:20:47.642 UTC [INFO ] The ADRCI package was successfully created.
2020-05-29 21:20:47.642 UTC [INFO ] The task finished successfully.
```

To package diagnostic data for a particular problem, specify its ID using the `problem_id` parameter\. In the following example, you package data for problem 3 only\.

```
SQL> exec :task_id := rdsadmin.rdsadmin_adrci_util.create_adrci_package(problem_id=>3);

PL/SQL procedure successfully completed.
```

To read the task output, call `rdsadmin.rds_file_util.read_text_file`, supplying the task ID as part of the file name\. The output shows that you generated incident package `ORA700EVE_20200529212111_COM_1.zip`\.

```
SQL> select * from table(rdsadmin.rds_file_util.read_text_file('BDUMP', 'dbtask-'||:task_id||'.log'));

TEXT
------------------------------------------------------------------------------------------------------------------------------------------------------------
2020-05-29 21:21:11.050 UTC [INFO ] The ADRCI package is being created.
2020-05-29 21:21:15.646 UTC [INFO ] Generated package 2 in file /rdsdbdata/log/trace/ORA700EVE_20200529212111_COM_1.zip, mode complete
2020-05-29 21:21:15.646 UTC [INFO ] The ADRCI package was successfully created.
2020-05-29 21:21:15.646 UTC [INFO ] The task finished successfully.
```

## Showing Trace Files<a name="Appendix.Oracle.CommonDBATasks.ShowTrace"></a>

You can show trace files using the Amazon RDS function `rdsadmin.rdsadmin_adrci_util.show_adrci_tracefile`\.

This function uses the following parameter\.


****  

| Parameter Name | Data Type | Valid Values | Default | Required | Description | 
| --- | --- | --- | --- | --- | --- | 
|  `filename`  |  varchar2  | A valid trace file name |  Null  |  No  |  If the value is null, the function shows all trace files\. If it isn't null, the function shows the specified file\.  | 

To show the trace file, call the Amazon RDS function `rdsadmin.rdsadmin_adrci_util.show_adrci_tracefile` with the `incident_id` parameter\.

```
SQL> exec :task_id := rdsadmin.rdsadmin_adrci_util.show_adrci_tracefile;

PL/SQL procedure successfully completed.
```

To list the trace file names, call the Amazon RDS procedure `rdsadmin.rds_file_util.read_text_file`, supplying the task ID as part of the file name\.

```
SQL> select * from table(rdsadmin.rds_file_util.read_text_file('BDUMP', 'dbtask-'||:task_id||'.log')) where text like '%/alert_%';

TEXT
---------------------------------------------------------------
     diag/rdbms/orcl_a/ORCL/trace/alert_ORCL.log.2020-05-28
     diag/rdbms/orcl_a/ORCL/trace/alert_ORCL.log.2020-05-27
     diag/rdbms/orcl_a/ORCL/trace/alert_ORCL.log.2020-05-26
     diag/rdbms/orcl_a/ORCL/trace/alert_ORCL.log.2020-05-25
     diag/rdbms/orcl_a/ORCL/trace/alert_ORCL.log.2020-05-24
     diag/rdbms/orcl_a/ORCL/trace/alert_ORCL.log.2020-05-23
     diag/rdbms/orcl_a/ORCL/trace/alert_ORCL.log.2020-05-22
     diag/rdbms/orcl_a/ORCL/trace/alert_ORCL.log.2020-05-21
     diag/rdbms/orcl_a/ORCL/trace/alert_ORCL.log

9 rows selected.
```

In the following example, you generate output for alert\_ORCL\.log\.

```
SQL> exec :task_id := rdsadmin.rdsadmin_adrci_util.show_adrci_tracefile('diag/rdbms/orcl_a/ORCL/trace/alert_ORCL.log');

PL/SQL procedure successfully completed.
```

To read the log file, call `rdsadmin.rds_file_util.read_text_file`\. Supply the task ID as part of the file name\. The output shows the first 10 lines of alert\_ORCL\.log\.

```
SQL> select * from table(rdsadmin.rds_file_util.read_text_file('BDUMP', 'dbtask-'||:task_id||'.log')) where rownum <= 10;

TEXT
-----------------------------------------------------------------------------------------
2020-05-29 21:24:02.083 UTC [INFO ] The trace files are being displayed.
2020-05-29 21:24:02.128 UTC [INFO ] Thu May 28 23:59:10 2020
Thread 1 advanced to log sequence 2048 (LGWR switch)
  Current log# 3 seq# 2048 mem# 0: /rdsdbdata/db/ORCL_A/onlinelog/o1_mf_3_hbl2p8xs_.log
Thu May 28 23:59:10 2020
Archived Log entry 2037 added for thread 1 sequence 2047 ID 0x5d62ce43 dest 1:
Fri May 29 00:04:10 2020
Thread 1 advanced to log sequence 2049 (LGWR switch)
  Current log# 4 seq# 2049 mem# 0: /rdsdbdata/db/ORCL_A/onlinelog/o1_mf_4_hbl2qgmh_.log
Fri May 29 00:04:10 2020

10 rows selected.
```