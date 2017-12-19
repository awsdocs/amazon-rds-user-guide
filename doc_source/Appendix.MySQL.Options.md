# Options for MySQL DB Instances<a name="Appendix.MySQL.Options"></a>

This appendix describes options, or additional features, that are available for Amazon RDS instances running the MySQL DB engine\. To enable these options, you can add them to a custom option group, and then associate the option group with your DB instance\. For more information about working with option groups, see [Working with Option Groups](USER_WorkingWithOptionGroups.md)\. 

Amazon RDS supports the following options for MySQL: 


****  

| Option | Option ID | Engine Versions | 
| --- | --- | --- | 
|  [MariaDB Audit Plugin Support](Appendix.MySQL.Options.AuditPlugin.md)  |  `MARIADB_AUDIT_PLUGIN`  |  MySQL 5\.6\.29 and later MySQL 5\.7\.16 and later  | 
|  [MySQL `MEMCACHED` Support](Appendix.MySQL.Options.memcached.md)  |  `MEMCACHED`  |  MySQL 5\.6 and later  | 