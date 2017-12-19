# Amazon Aurora MySQL Database Engine Updates: 2015\-08\-24<a name="AuroraMySQL.Updates.20150824"></a>

**Version:** 1\.1

This update includes the following improvements:

+ Replication stability improvements when replicating with a MySQL database \(binlog replication\)\. For information on Amazon Aurora MySQL replication with MySQL, see [Replication with Amazon Aurora](Aurora.Replication.md)\.

+ A 1 gigabyte \(GB\) limit on the size of the relay logs accumulated for an Amazon Aurora MySQL DB cluster that is a replication slave\. This improves the file management for the Aurora DB clusters\.

+ Stability improvements in the areas of read ahead, recursive foreign\-key relationships, and Aurora replication\.

+ Integration of MySQL bug fixes\.

  + InnoDB databases with names beginning with a digit cause a full\-text search \(FTS\) parser error\. \(Bug \#17607956\)

  + InnoDB full\-text searches fail in databases whose names began with a digit\. \(Bug \#17161372\)

  + For InnoDB databases on Windows, the full\-text search \(FTS\) object ID is not in the expected hexadecimal format\. \(Bug \#16559254\)

  + A code regression introduced in MySQL 5\.6 negatively impacted DROP TABLE and ALTER TABLE performance\. This could cause a performance drop between MySQL Server 5\.5\.x and 5\.6\.x\. \(Bug \#16864741\)

+ Simplified logging to reduce the size of log files and the amount of storage that they require\.