# Options for MySQL DB instances<a name="Appendix.MySQL.Options"></a>

Following, you can find a description of options, or additional features, that are available for Amazon RDS instances running the MySQL DB engine\. To enable these options, you can add them to a custom option group, and then associate the option group with your DB instance\. For more information about working with option groups, see [Working with option groups](USER_WorkingWithOptionGroups.md)\. 

Amazon RDS supports the following options for MySQL: 


****  

| Option | Option ID | Engine versions | 
| --- | --- | --- | 
|  [MariaDB Audit Plugin support](Appendix.MySQL.Options.AuditPlugin.md)  |  `MARIADB_AUDIT_PLUGIN`  |  MySQL 8\.0\.25 and higher 8\.0 versions All MySQL 5\.7 versions  | 
|  [MySQL memcached support](Appendix.MySQL.Options.memcached.md)  |  `MEMCACHED`  |  All MySQL 5\.7 and 8\.0 versions  | 