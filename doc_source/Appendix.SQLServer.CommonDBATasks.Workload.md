# Analyzing Your Database Workload on an Amazon RDS DB Instance with SQL Server Tuning Advisor<a name="Appendix.SQLServer.CommonDBATasks.Workload"></a>

The Database Engine Tuning Advisor is a client application provided by Microsoft that analyzes database workload and recommends an optimal set of indexes for your Microsoft SQL Server databases based on the kinds of queries you run\. Like SQL Server Management Studio, you run Tuning Advisor from a client computer that connects to your Amazon RDS DB instance that is running SQL Server\. The client computer can be a local computer that you run on premises within your own network or it can be an Amazon EC2 Windows instance that is running in the same region as your Amazon RDS DB instance\.

This section shows how to capture a workload for Tuning Advisor to analyze\. This is the preferred process for capturing a workload because Amazon RDS restricts host access to the SQL Server instance\. The full documentation on Tuning Advisor can be found on [MSDN](http://msdn.microsoft.com/en-us/library/ms173494%28v=sql.105%29.aspx)\.

To use Tuning Advisor, you must provide what is called a workload to the advisor\. A workload is a set of Transact\-SQL statements that run against a database or databases that you want to tune\. Database Engine Tuning Advisor uses trace files, trace tables, Transact\-SQL scripts, or XML files as workload input when tuning databases\. When working with Amazon RDS, a workload can be a file on a client computer or a database table on an Amazon RDS SQL Server DB accessible to your client computer\. The file or the table must contain queries against the databases you want to tune in a format suitable for replay\.

For Tuning Advisor to be most effective, a workload should be as realistic as possible\. You can generate a workload file or table by performing a trace against your DB instance\. While a trace is running, you can either simulate a load on your DB instance or run your applications with a normal load\.

There are two types of traces: client\-side and server\-side\. A client\-side trace is easier to set up and you can watch trace events being captured in real\-time in SQL Server Profiler\. A server\-side trace is more complex to set up and requires some Transact\-SQL scripting\. In addition, because the trace is written to a file on the Amazon RDS DB instance, storage space is consumed by the trace\. It is important to track of how much storage space a running server\-side trace uses because the DB instance could enter a storage\-full state and would no longer be available if it runs out of storage space\.

For a client\-side trace, when a sufficient amount of trace data has been captured in the SQL Server Profiler, you can then generate the workload file by saving the trace to either a file on your local computer or in a database table on a DB instance that is available to your client computer\. The main disadvantage of using a client\-side trace is that the trace may not capture all queries when under heavy loads\. This could weaken the effectiveness of the analysis performed by the Database Engine Tuning Advisor\. If you need to run a trace under heavy loads and you want to ensure that it captures every query during a trace session, you should use a server\-side trace\.

For a server\-side trace, you must get the trace files on the DB instance into a suitable workload file or you can save the trace to a table on the DB instance after the trace completes\. You can use the SQL Server Profiler to save the trace to a file on your local computer or have the Tuning Advisor read from the trace table on the DB instance\.

## Running a Client\-Side Trace on a SQL Server DB Instance<a name="Appendix.SQLServer.CommonDBATasks.TuningAdvisor.ClientSide"></a>

 **To run a client\-side trace on a SQL Server DB instance** 

1. Start SQL Server Profiler\. It is installed in the Performance Tools folder of your SQL Server instance folder\. You must load or define a trace definition template to start a client\-side trace\.

1. In the SQL Server Profiler File menu, choose **New Trace**\. In the **Connect to Server** dialog box, enter the DB instance endpoint, port, master user name, and password of the database you would like to run a trace on\.

1. In the **Trace Properties** dialog box, enter a trace name and choose a trace definition template\. A default template, TSQL\_Replay, ships with the application\. You can edit this template to define your trace\. Edit events and event information under the **Events Selection** tab of the **Trace Properties** dialog box\. For more information about trace definition templates and using the SQL Server Profiler to specify a client\-side trace see the documentation in [MSDN](http://msdn.microsoft.com/en-us/library/ms173494%28v=sql.105%29.aspx)\.

1. Start the client\-side trace and watch SQL queries in real\-time as they run against your DB instance\.

1. Select **Stop Trace** from the **File** menu when you have completed the trace\. Save the results as a file or as a trace table on you DB instance\.

## Running a Server\-Side Trace on a SQL Server DB Instance<a name="Appendix.SQLServer.CommonDBATasks.TuningAdvisor.ServerSide"></a>

Writing scripts to create a server\-side trace can be complex and is beyond the scope of this document\. This section contains sample scripts that you can use as examples\. As with a client\-side trace, the goal is to create a workload file or trace table that you can open using the Database Engine Tuning Advisor\.

The following is an abridged example script that starts a server\-side trace and captures details to a workload file\. The trace initially saves to the file RDSTrace\.trc in the D:\\RDSDBDATA\\Log directory and rolls\-over every 100 MB so subsequent trace files are named RDSTrace\_1\.trc, RDSTrace\_2\.trc, etc\.

```
DECLARE @file_name NVARCHAR(245) = 'D:\RDSDBDATA\Log\RDSTrace';
DECLARE @max_file_size BIGINT = 100;
DECLARE @on BIT = 1
DECLARE @rc INT
DECLARE @traceid INT

EXEC @rc = sp_trace_create @traceid OUTPUT, 2, @file_name, @max_file_size
IF (@rc = 0) BEGIN
   EXEC sp_trace_setevent @traceid, 10, 1, @on
   EXEC sp_trace_setevent @traceid, 10, 2, @on
   EXEC sp_trace_setevent @traceid, 10, 3, @on
 . . .
   EXEC sp_trace_setfilter @traceid, 10, 0, 7, N'SQL Profiler'
   EXEC sp_trace_setstatus @traceid, 1
   END
```

The following example is a script that stops a trace\. Note that a trace created by the previous script continues to run until you explicitly stop the trace or the process runs out of disk space\.

```
DECLARE @traceid INT
SELECT @traceid = traceid FROM ::fn_trace_getinfo(default) 
WHERE property = 5 AND value = 1 AND traceid <> 1 

IF @traceid IS NOT NULL BEGIN
   EXEC sp_trace_setstatus @traceid, 0
   EXEC sp_trace_setstatus @traceid, 2
END
```

You can save server\-side trace results to a database table and use the database table as the workload for the Tuning Advisor by using the fn\_trace\_gettable function\. The following commands load the results of all files named RDSTrace\.trc in the D:\\rdsdbdata\\Log directory, including all rollover files like RDSTrace\_1\.trc, into a table named RDSTrace in the current database\.

```
SELECT * INTO RDSTrace
FROM fn_trace_gettable('D:\rdsdbdata\Log\RDSTrace.trc', default);
```

To save a specific rollover file to a table, for example the RDSTrace\_1\.trc file, specify the name of the rollover file and substitute 1 instead of default as the last parameter to fn\_trace\_gettable\.

```
SELECT * INTO RDSTrace_1
FROM fn_trace_gettable('D:\rdsdbdata\Log\RDSTrace_1.trc', 1);
```

## Running Tuning Advisor with a Trace<a name="Appendix.SQLServer.CommonDBATasks.TuningAdvisor.Running"></a>

Once you create a trace, either as a local file or as a database table, you can then run Tuning Advisor against your DB instance\. Microsoft includes documentation on using the Database Engine Tuning Advisor in [MSDN](http://msdn.microsoft.com/en-us/library/ms173494%28v=sql.105%29.aspx)\. Using Tuning Advisor with Amazon RDS is the same process as when working with a standalone, remote SQL Server instance\. You can either use the Tuning Advisor UI on your client machine or use the dta\.exe utility from the command line\. In both cases, you must connect to the Amazon RDS DB instance using the endpoint for the DB instance and provide your master user name and master user password when using Tuning Advisor\. 

The following code example demonstrates using the dta\.exe command line utility against an Amazon RDS DB instance with an endpoint of **dta\.cnazcmklsdei\.us\-east\-1\.rds\.amazonaws\.com**\. The example includes the master user name **admin** and the master user password **test**, the example database to tune is named **RDSDTA** and the input workload is a trace file on the local machine named **C:\\RDSTrace\.trc**\. The example command line code also specifies a trace session named **RDSTrace1** and specifies output files to the local machine named **RDSTrace\.sql** for the SQL output script, **RDSTrace\.txt** for a result file, and **RDSTrace\.xml** for an XML file of the analysis\. There is also an error table specified on the RDSDTA database named **RDSTraceErrors**\.

```
dta -S dta.cnazcmklsdei.us-east-1.rds.amazonaws.com -U admin -P test -D RDSDTA -if C:\RDSTrace.trc -s RDSTrace1 -of C:\ RDSTrace.sql -or C:\ RDSTrace.txt -ox C:\ RDSTrace.xml -e RDSDTA.dbo.RDSTraceErrors 
```

Here is the same example command line code except the input workload is a table on the remote Amazon RDS instance named **RDSTrace** which is on the **RDSDTA** database\.

```
dta -S dta.cnazcmklsdei.us-east-1.rds.amazonaws.com -U admin -P test -D RDSDTA -it RDSDTA.dbo.RDSTrace -s RDSTrace1 -of C:\ RDSTrace.sql -or C:\ RDSTrace.txt -ox C:\ RDSTrace.xml -e RDSDTA.dbo.RDSTraceErrors
```

A full list of dta utility command\-line parameters can be found in [MSDN](http://msdn.microsoft.com/en-us/library/ms162812.aspx)\.