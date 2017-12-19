# Microsoft SQL Server Database Log Files<a name="USER_LogAccess.Concepts.SQLServer"></a>

You can access Microsoft SQL Server error logs, agent logs, trace files, and dump files by using the Amazon RDS console or APIs\. For more information about viewing, downloading, and watching file\-based database logs, see [Amazon RDS Database Log Files](USER_LogAccess.md)\. 

## Retention Schedule<a name="USER_LogAccess.Concepts.SQLServer.Retention"></a>

Log files are rotated each day and whenever your DB instance is restarted\. The following is the retention schedule for Microsoft SQL Server logs on Amazon RDS\. 


****  

| Log Type | Retention Schedule | 
| --- | --- | 
|  Error logs  |  A maximum of 30 error logs are retained\. Amazon RDS may delete error logs older than 7 days\.    | 
|  Agent logs  |  A maximum of 10 agent logs are retained\. Amazon RDS may delete agent logs older than 7 days\.    | 
|  Trace files  |  Trace files are retained according to the trace file retention period of your DB instance\. The default trace file retention period is 7 days\. To modify the trace file retention period for your DB instance, see [Setting the Retention Period for Trace and Dump Files](Appendix.SQLServer.CommonDBATasks.TraceFiles.md#Appendix.SQLServer.CommonDBATasks.TraceFiles.PurgeTraceFiles)\.   | 
|  Dump files  |  Dump files are retained according to the dump file retention period of your DB instance\. The default dump file retention period is 7 days\. To modify the dump file retention period for your DB instance, see [Setting the Retention Period for Trace and Dump Files](Appendix.SQLServer.CommonDBATasks.TraceFiles.md#Appendix.SQLServer.CommonDBATasks.TraceFiles.PurgeTraceFiles)\.   | 

## Viewing the SQL Server Error Log by Using the rds\_read\_error\_log Procedure<a name="USER_LogAccess.Concepts.SQLServer.Proc"></a>

You can use the Amazon RDS stored procedure `rds_read_error_log` to view error logs and agent logs\. For more information, see [Using the rds\_read\_error\_log Procedure](Appendix.SQLServer.CommonDBATasks.Logs.md#Appendix.SQLServer.CommonDBATasks.Logs.SP)\. 

## Related Topics<a name="USER_LogAccess.Concepts.SQLServer.Related"></a>

+ [Using SQL Server Agent](Appendix.SQLServer.CommonDBATasks.Agent.md)

+ [Working with Microsoft SQL Server Logs](Appendix.SQLServer.CommonDBATasks.Logs.md)

+ [Working with Trace and Dump Files](Appendix.SQLServer.CommonDBATasks.TraceFiles.md)