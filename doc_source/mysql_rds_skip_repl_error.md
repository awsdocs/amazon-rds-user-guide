# mysql\.rds\_skip\_repl\_error<a name="mysql_rds_skip_repl_error"></a>

Skips and deletes a replication error on a MySQL DB read replica\.

## Syntax<a name="mysql_rds_skip_repl_error-syntax"></a>

 

```
CALL mysql.rds_skip_repl_error;
```

## Usage notes<a name="mysql_rds_skip_repl_error-usage-notes"></a>

The master user must run the `mysql.rds_skip_repl_error` procedure on a read replica\. For more information about this procedure, see [Calling the mysql\.rds\_skip\_repl\_error procedure](Appendix.MySQL.CommonDBATasks.md#Appendix.MySQL.CommonDBATasks.SkipError.procedure)\.

To determine if there are errors, run the MySQL `SHOW REPLICA STATUS\G` command\. If a replication error isn't critical, you can run `mysql.rds_skip_repl_error` to skip the error\. If there are multiple errors, `mysql.rds_skip_repl_error` deletes the first error, then warns that others are present\. You can then use `SHOW REPLICA STATUS\G` to determine the correct course of action for the next error\. For information about the values returned, see [the MySQL documentation](https://dev.mysql.com/doc/refman/8.0/en/show-replica-status.html)\.

**Note**  
Previous versions of MySQL used `SHOW SLAVE STATUS` instead of `SHOW REPLICA STATUS`\. If you are using a MySQL version before 8\.0\.23, then use `SHOW SLAVE STATUS`\. 

For more information about addressing replication errors with Amazon RDS, see [Troubleshooting a MySQL read replica problem](USER_MySQL.Replication.ReadReplicas.md#USER_ReadRepl.Troubleshooting)\.

### Replication stopped error<a name="w285aac41c95c45b7c11"></a>

When you call the `mysql.rds_skip_repl_error` procedure, you might receive an error message stating that the replica is down or disabled\.

This error message appears if you run the procedure on the primary instance instead of the read replica\. You must run this procedure on the read replica for the procedure to work\.

This error message might also appear if you run the procedure on the read replica, but replication can't be restarted successfully\. 

If you need to skip a large number of errors, the replication lag can increase beyond the default retention period for binary log \(binlog\) files\. In this case, you might encounter a fatal error due to binlog files being purged before they have been replayed on the read replica\. This purge causes replication to stop, and you can no longer call the `mysql.rds_skip_repl_error` command to skip replication errors\. 

You can mitigate this issue by increasing the number of hours that binlog files are retained on your source database instance\. After you have increased the binlog retention time, you can restart replication and call the `mysql.rds_skip_repl_error` command as needed\.

To set the binlog retention time, use the [mysql\.rds\_set\_configuration](mysql_rds_set_configuration.md) procedure and specify a configuration parameter of `'binlog retention hours'` along with the number of hours to retain binlog files on the DB cluster\. The following example sets the retention period for binlog files to 48 hours\.

```
CALL mysql.rds_set_configuration('binlog retention hours', 48);
```