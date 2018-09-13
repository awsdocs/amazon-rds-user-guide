# Working with MariaDB Replication<a name="USER_MariaDB.Replication"></a>

You usually use Read Replicas to configure replication between Amazon RDS DB instances\. For general information about Read Replicas, see [Working with Read Replicas of MariaDB, MySQL, and PostgreSQL DB Instances](USER_ReadRepl.md)\. For specific information about working with Read Replicas on Amazon RDS MariaDB, see [Working with MariaDB Read Replicas](USER_MariaDB.Replication.ReadReplicas.md)\. 

You can also configure replication based on binary log coordinates for a MariaDB DB instance\. For MariaDB instances, you can also configure replication based on global transaction IDs \(GTIDs\), which provides better crash safety\. For more information, see [Configuring GTID\-Based Replication into an Amazon RDS MariaDB DB instance](MariaDB.Procedural.Replication.GTID.md)\. 

**Note**  
The other replication options are available with Amazon RDS MariaDB:  
You can set up replication between an Amazon RDS MariaDB DB instance and a MySQL or MariaDB instance that is external to Amazon RDS\. For information about configuring replication with an external source, see [Replication with a MySQL or MariaDB Instance Running External to Amazon RDS](MySQL.Procedural.Importing.External.Repl.md)\.
You can configure replication to import databases from a MySQL or MariaDB instance that is external to Amazon RDS, or to export databases to such instances\. For more information, see [Importing Data to an Amazon RDS MySQL or MariaDB DB Instance with Reduced Downtime](MySQL.Procedural.Importing.NonRDSRepl.md) and [Exporting Data from a MySQL DB Instance by Using Replication](MySQL.Procedural.Exporting.NonRDSRepl.md)\.

**Topics**
+ [Working with MariaDB Read Replicas](USER_MariaDB.Replication.ReadReplicas.md)
+ [Configuring GTID\-Based Replication into an Amazon RDS MariaDB DB instance](MariaDB.Procedural.Replication.GTID.md)