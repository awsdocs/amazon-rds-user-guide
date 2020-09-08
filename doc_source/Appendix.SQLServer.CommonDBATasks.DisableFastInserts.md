# Disabling Fast Inserts During Bulk Loading<a name="Appendix.SQLServer.CommonDBATasks.DisableFastInserts"></a>

Starting with SQL Server 2016, fast inserts are enabled by default\. Fast inserts leverage the minimal logging that occurs while the database is in the simple or bulk logged recovery model to optimize insert performance\. With fast inserts, each bulk load batch acquires new extents, bypassing the allocation lookup for existing extents with available free space to optimize insert performance\.

However, with fast inserts bulk loads with small batch sizes can lead to increased unused space consumed by objects\. If increasing batch size isn't feasible, enabling trace flag 692 can help reduce unused reserved space, but at the expense of performance\. Enabling this trace flag disables fast inserts while bulk loading data into heap or clustered indexes\.

You enable trace flag 692 as a startup parameter using DB parameter groups\. For more information, see [Working with DB Parameter Groups](USER_WorkingWithParamGroups.md)\.

Trace flag 692 is supported for Amazon RDS on SQL Server 2016 and later\. For more information on trace flags, see [DBCC TRACEON \- Trace Flags](https://docs.microsoft.com/en-us/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql) in the Microsoft documentation\.