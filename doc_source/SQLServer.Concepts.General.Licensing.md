# Licensing Microsoft SQL Server on Amazon RDS<a name="SQLServer.Concepts.General.Licensing"></a>

When you set up an Amazon RDS DB instance for Microsoft SQL Server, the software license is included\. 

This means that you don't need to purchase SQL Server licenses separately\. AWS holds the license for the SQL Server database software\. Amazon RDS pricing includes the software license, underlying hardware resources, and Amazon RDS management capabilities\. 

Amazon RDS supports the following Microsoft SQL Server editions: 
+ Enterprise
+ Standard
+ Web
+ Express

**Note**  
Licensing for SQL Server Web Edition supports only public and internet\-accessible webpages, websites, web applications, and web services\. This level of support is required for compliance with Microsoft's usage rights\. For more information, see [AWS Service Terms](http://aws.amazon.com/serviceterms)\. 

Amazon RDS supports Multi\-AZ deployments for DB instances running Microsoft SQL Server by using SQL Server Database Mirroring \(DBM\) or Always On Availability Groups \(AGs\)\. There are no additional licensing requirements for Multi\-AZ deployments\. For more information, see [Multi\-AZ Deployments for Microsoft SQL Server](USER_SQLServerMultiAZ.md)\. 

## Restoring License\-Terminated DB Instances<a name="SQLServer.Concepts.General.Licensing.Restoring"></a>

Amazon RDS takes snapshots of license\-terminated DB instances\. If your instance is terminated for licensing issues, you can restore it from the snapshot to a new DB instance\. New DB instances have a license included\.

For more information, see [Restoring License\-Terminated DB Instances](Appendix.SQLServer.CommonDBATasks.RestoreLTI.md)\. 

## Development and Test<a name="SQLServer.Concepts.General.Licensing.Development"></a>

Because of licensing requirements, we can't offer SQL Server Developer edition on Amazon RDS\. You can use Express edition for many development, testing, and other nonproduction needs\. However, if you need the full feature capabilities of an enterprise\-level installation of SQL Server for development, you can download and install SQL Server Developer edition \(and other MSDN products\) on Amazon EC2\. Dedicated infrastructure is not required for Developer edition\. By using your own host, you also gain access to other programmability features that are not accessible on Amazon RDS\. For more information on the difference between SQL Server editions, see [Editions and supported features of SQL Server 2017](https://docs.microsoft.com/en-us/sql/sql-server/editions-and-components-of-sql-server-2017) in the Microsoft documentation\.