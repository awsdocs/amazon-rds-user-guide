# Accessing MySQL error logs<a name="USER_LogAccess.MySQL.Errorlog"></a>

MySQL writes errors in the `mysql-error.log` file\. Each log file has the hour it was generated \(in UTC\) appended to its name\. The log files also have a timestamp that helps you determine when the log entries were written\.

MySQL writes to the error log only on startup, shutdown, and when it encounters errors\. A DB instance can go hours or days without new entries being written to the error log\. If you see no recent entries, it's because the server did not encounter an error that would result in a log entry\.

MySQL writes `mysql-error.log` to disk every 5 minutes\. MySQL appends the contents of the log to `mysql-error-running.log`\. 

MySQL rotates the `mysql-error-running.log` file every hour\. RDS for MySQL retains the logs generated during the last two weeks\.

**Note**  
The log retention period is different between Amazon RDS and Aurora\.