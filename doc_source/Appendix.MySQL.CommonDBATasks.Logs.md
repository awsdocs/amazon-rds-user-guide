# Managing table\-based MySQL logs<a name="Appendix.MySQL.CommonDBATasks.Logs"></a>

You can direct the general and slow query logs to tables on the DB instance by creating a DB parameter group and setting the `log_output` server parameter to `TABLE`\. General queries are then logged to the `mysql.general_log` table, and slow queries are logged to the `mysql.slow_log` table\. You can query the tables to access the log information\. Enabling this logging increases the amount of data written to the database, which can degrade performance\.

Both the general log and the slow query logs are disabled by default\. In order to enable logging to tables, you must also set the `general_log` and `slow_query_log` server parameters to `1`\.

Log tables keep growing until the respective logging activities are turned off by resetting the appropriate parameter to `0`\. A large amount of data often accumulates over time, which can use up a considerable percentage of your allocated storage space\. Amazon RDS doesn't allow you to truncate the log tables, but you can move their contents\. Rotating a table saves its contents to a backup table and then creates a new empty log table\. You can manually rotate the log tables with the following command line procedures, where the command prompt is indicated by `PROMPT>`: 

```
PROMPT> CALL mysql.rds_rotate_slow_log;
PROMPT> CALL mysql.rds_rotate_general_log;
```

To completely remove the old data and reclaim the disk space, call the appropriate procedure twice in succession\. 