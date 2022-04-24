# Working with MariaDB replication in Amazon RDS<a name="USER_MariaDB.Replication"></a>

You usually use read replicas to configure replication between Amazon RDS DB instances\. For general information about read replicas, see [Working with read replicas](USER_ReadRepl.md)\. For specific information about working with read replicas on Amazon RDS for MariaDB, see [Working with MariaDB read replicas](USER_MariaDB.Replication.ReadReplicas.md)\. 

You can also configure replication based on binary log coordinates for a MariaDB DB instance\. For MariaDB instances, you can also configure replication based on global transaction IDs \(GTIDs\), which provides better crash safety\. For more information, see [Configuring GTID\-based replication with an external source instance](MariaDB.Procedural.Replication.GTID.md)\. 

The following are other replication options available with RDS for MariaDB:
+ You can set up replication between an RDS for MariaDB DB instance and a MySQL or MariaDB instance that is external to Amazon RDS\. For information about configuring replication with an external source, see [Configuring binary log file position replication with an external source instance](MySQL.Procedural.Importing.External.ReplMariaDB.md)\.
+ You can configure replication to import databases from a MySQL or MariaDB instance that is external to Amazon RDS, or to export databases to such instances\. For more information, see [Importing data to an Amazon RDS MariaDB or MySQL DB instance with reduced downtime](MySQL.Procedural.Importing.NonRDSReplMariaDB.md) and [Exporting data from a MySQL DB instance by using replication](MySQL.Procedural.Exporting.NonRDSRepl.md)\.

For any of these replication options, you can use either row\-based replication, statement\-based, or mixed replication\. Row\-based replication only replicates the changed rows that result from a SQL statement\. Statement\-based replication replicates the entire SQL statement\. Mixed replication uses statement\-based replication when possible, but switches to row\-based replication when SQL statements that are unsafe for statement\-based replication are run\. In most cases, mixed replication is recommended\. The binary log format of the DB instance determines whether replication is row\-based, statement\-based, or mixed\. For information about setting the binary log format, see [Binary logging format](USER_LogAccess.Concepts.MariaDB.md#USER_LogAccess.MariaDB.BinaryFormat)\.

**Topics**
+ [Working with MariaDB read replicas](USER_MariaDB.Replication.ReadReplicas.md)
+ [Configuring GTID\-based replication with an external source instance](MariaDB.Procedural.Replication.GTID.md)
+ [Configuring binary log file position replication with an external source instance](MySQL.Procedural.Importing.External.ReplMariaDB.md)