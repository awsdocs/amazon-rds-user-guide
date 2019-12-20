# SQL Server Performance Considerations<a name="Appendix.SQLServer.Options.TDE.Perf"></a>

The performance of a SQL Server DB instance can be impacted by using transparent data encryption\. 

Performance for unencrypted databases can also be degraded if the databases are on a DB instance that has at least one encrypted database\. As a result, we recommend that you keep encrypted and unencrypted databases on separate DB instances\.

Because of the nature of encryption, the database size and the size of the transaction log is larger than for an unencrypted database\. You could run over your allocation of free backup space\. The nature of TDE causes an unavoidable performance hit\. If you need high performance and TDE, measure the impact and make sure that it meets your needs\. There is less of an impact on performance if you use Provisioned IOPS and at least an M3\.Large DB instance class\. 