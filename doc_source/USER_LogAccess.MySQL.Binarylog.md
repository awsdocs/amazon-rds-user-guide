# Accessing MySQL binary logs<a name="USER_LogAccess.MySQL.Binarylog"></a>

You can use the mysqlbinlog utility to download or stream binary logs from RDS for MySQL DB instances\. The binary log is downloaded to your local computer, where you can perform actions such as replaying the log using the mysql utility\. For more information about using the mysqlbinlog utility, go to [Using mysqlbinlog to back up binary log files](https://dev.mysql.com/doc/refman/8.0/en/mysqlbinlog-backup.html)\.

 To run the mysqlbinlog utility against an Amazon RDS instance, use the following options: 
+  Specify the `--read-from-remote-server` option\. 
+  `--host`: Specify the DNS name from the endpoint of the instance\. 
+  `--port`: Specify the port used by the instance\. 
+  `--user`: Specify a MySQL user that has been granted the replication slave permission\. 
+  `--password`: Specify the password for the user, or omit a password value so that the utility prompts you for a password\. 
+ To have the file downloaded in binary format, specify the `--raw` option\. 
+  `--result-file`: Specify the local file to receive the raw output\. 
+ Specify the names of one or more binary log files\. To get a list of the available logs, use the SQL command SHOW BINARY LOGS\.
+ To stream the binary log files, specify the `--stop-never` option\. 

For more information about mysqlbinlog options, go to [mysqlbinlog \- utility for processing binary log files](https://dev.mysql.com/doc/refman/8.0/en/mysqlbinlog.html)\. 

For example, see the following\. 

For Linux, macOS, or Unix:

```
mysqlbinlog \
    --read-from-remote-server \
    --host=MySQL56Instance1.cg034hpkmmjt.region.rds.amazonaws.com \
    --port=3306  \
    --user ReplUser \
    --password \
    --raw \
    --result-file=/tmp/ \
    binlog.00098
```

For Windows:

```
mysqlbinlog ^
    --read-from-remote-server ^
    --host=MySQL56Instance1.cg034hpkmmjt.region.rds.amazonaws.com ^
    --port=3306  ^
    --user ReplUser ^
    --password ^
    --raw ^
    --result-file=/tmp/ ^
    binlog.00098
```

Amazon RDS normally purges a binary log as soon as possible, but the binary log must still be available on the instance to be accessed by mysqlbinlog\. To specify the number of hours for RDS to retain binary logs, use the `mysql.rds_set_configuration` stored procedure and specify a period with enough time for you to download the logs\. After you set the retention period, monitor storage usage for the DB instance to ensure that the retained binary logs don't take up too much storage\.

The following example sets the retention period to 1 day\.

```
call mysql.rds_set_configuration('binlog retention hours', 24);
```

To display the current setting, use the `mysql.rds_show_configuration` stored procedure\.

```
call mysql.rds_show_configuration;
```