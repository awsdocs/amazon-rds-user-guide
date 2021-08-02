# Accessing MySQL error logs<a name="USER_LogAccess.MySQL.Errorlog"></a>

MySQL writes to the error log (`mysql-error.log`) only on startup, shutdown, and when it encounters errors\. A DB instance can go hours or days without new entries being written to the error log\. If you see no recent entries, it's because the server did not encounter an error that would result in a log entry\.

MySQL writes `mysql-error.log` to disk every 5 minutes\. RDS appends the contents of the log to `mysql-error-running.log`\. 

RDS rotates the `mysql-error-running.log` file every hour\. Each log file has the hour it was generated \(in UTC\) appended to its name\. The log files also have a timestamp that helps you determine when the log entries were written\. The generated logs are kept for last two weeks (Aurora) or one day (RDS)\.
