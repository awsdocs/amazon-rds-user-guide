# Amazon RDS Database Log Files<a name="USER_LogAccess"></a>

You can view, download, and watch database logs using the Amazon RDS console, the AWS Command Line Interface \(AWS CLI\), or the Amazon RDS API\. Viewing, downloading, or watching transaction logs is not supported\. 

For engine\-specific documentation, see the following\.


****  

| Database Engine | Relevant Documentation | 
| --- | --- | 
|  MariaDB  |  You can access the error log, the slow query log, and the general log\. For more information, see [MariaDB Database Log Files](USER_LogAccess.Concepts.MariaDB.md)\.  | 
|  Microsoft SQL Server  |  You can access SQL Server error logs, agent logs, and trace files\. For more information, see [Microsoft SQL Server Database Log Files](USER_LogAccess.Concepts.SQLServer.md)\.  | 
|  MySQL  |  You can access the error log, the slow query log, and the general log\. For more information, see [MySQL Database Log Files](USER_LogAccess.Concepts.MySQL.md)\.  | 
|  Oracle  |  You can access Oracle alert logs, audit files, and trace files\. For more information, see [Oracle Database Log Files](USER_LogAccess.Concepts.Oracle.md)\.  | 
|  PostgreSQL  |  You can access query logs and error logs\. Error logs can contain auto\-vacuum and connection information, as well as rds\_admin actions\. For more information, see [PostgreSQL Database Log Files](USER_LogAccess.Concepts.PostgreSQL.md)\.  | 

## Viewing and Listing Database Log Files<a name="USER_LogAccess.Procedural.Viewing"></a>

You can view database log files for your DB engine by using the Amazon RDS console\. You can list what log files are available for download or monitoring by using the AWS CLI or Amazon RDS API\. 

**Note**  
 If you can't view the list of log files for an existing Oracle DB instance, reboot the instance to view the list\. 

### AWS Management Console<a name="USER_LogAccess.CON"></a>

**To view a database log file**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Instances**\.

1. Choose the DB instance that has the log file that you want to view, and then choose **Instance Actions**, **See Details**\. 

1. Scroll down to the **Logs** section\. 

1. In the **Logs** section, choose the log you wish to view and then choose **View**\.

### CLI<a name="USER_LogAccess.CLI"></a>

To list the available database log files for a DB instance, use the AWS CLI [http://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-log-files.html](http://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-log-files.html) command\.

The following example directs a list of log files for a DB instance named `my-db-instance` to a text file called log\_file\_list\.txt\.

**Example**  

```
1. aws rds describe-db-log-files --db-instance-identifier my-db-instance > log_file_list.txt
```

### API<a name="USER_LogAccess.API"></a>

To list the available database log files for a DB instance, call the Amazon RDS API [http://docs.aws.amazon.com/AmazonRDS/latest/APIReference//API_DescribeDBLogFiles.html](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference//API_DescribeDBLogFiles.html) action\.

## Downloading a Database Log File<a name="USER_LogAccess.Procedural.Downloading"></a>

You can use the Amazon RDS console or the AWS CLI to download a database log file\. 

You can download a complete log file by using the [DownloadCompleteDBLogFile](RESTReference.md#RESTReference.DownloadCompleteDBLogFile) REST API\. You can also download complete log files by using the `rds-download-db-logfile` CLI command\. For more information, see [ The `rds-download-db-logfile` Command ](RESTReference.md#RESTReference.DownloadCompleteDBLogFile.CLIversion)\. 

### AWS Management Console<a name="USER_LogAccess.Procedural.Downloading.CON"></a>

**To download a database log file**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Instances**\.

1. Choose the DB instance that has the log file that you want to view, and then choose **Instance actions**, **See details**\. 

1. Scroll down to the **Logs** section\. 

1. In the **Logs** section, choose the button next to the log you want to download, and then choose **Download**\.

1. Open the context \(right\-click\) menu for the link provided, and then choose **Save Link As**\. Type the location where you want the log file to be saved, and then choose **Save**\.  
![\[viewing log file\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/log_download2.png)

### CLI<a name="USER_LogAccess.Procedural.Downloading.CLI"></a>

To download a database log file, use the command [http://docs.aws.amazon.com/cli/latest/reference/rds/download-db-log-file-portion.html](http://docs.aws.amazon.com/cli/latest/reference/rds/download-db-log-file-portion.html)\.

The following example shows how to download the contents of a log file called log/ERROR\.4 and store it in a local file called errorlog\.txt\. 

**Example**  
For Linux, OS X, or Unix:  

```
1. aws rds download-db-log-file-portion \
2.     --db-instance-identifier myexampledb \
3.     --no-paginate \
4.     --log-file-name log/ERROR.4 > errorlog.txt
```
For Windows:  

```
1. aws rds download-db-log-file-portion ^
2.     --db-instance-identifier myexampledb ^
3.     --no-paginate ^
4.     --log-file-name log/ERROR.4 > errorlog.txt
```

## Watching a Database Log File<a name="USER_LogAccess.Procedural.Watching"></a>

You can monitor the contents of a log file by using the Amazon RDS console\.

### AWS Management Console<a name="USER_LogAccess.Procedural.Watching.CON"></a>

**To watch a database log file**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Instances**\.

1. Choose the DB instance that has the log file that you want to watch, and then choose **Instance Actions**, **See Details**\. 

1.  Choose the **Recent Events & Logs** tab\. 

1. In the **Logs** pane, choose the **Watch** button next to the log you want to watch\.

## Publishing Database Logs to Amazon CloudWatch Logs<a name="USER_LogAccess.Procedural.UploadtoCloudWatch"></a>

In addition to viewing and downloading DB instance logs, you can also publish logs to Amazon CloudWatch Logs for real\-time analysis\. With CloudWatch Logs, you can perform real\-time analysis of the log data, and you can use CloudWatch to create alarms and view metrics\. You can use CloudWatch Logs store your log data in highly durable storage, which you can manage with the CloudWatch Logs Agent\.

AWS retains log data published to CloudWatch Logs for an indefinite time period unless you specify a retention period\. For more information about setting a CloudWatch Log retention period, see [Change Log Data Retention in CloudWatch Logs](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/SettingLogRetention.html)\. 

 For more information about publishing database logs to CloudWatch Logs, see the following:

+ [Publishing MariaDB Logs to CloudWatch Logs](USER_LogAccess.Concepts.MariaDB.md#USER_LogAccess.MariaDB.PublishtoCloudWatchLogs)

+ [Publishing MySQL Logs to CloudWatch Logs](USER_LogAccess.Concepts.MySQL.md#USER_LogAccess.MySQLDB.PublishtoCloudWatchLogs)

+ [Publishing Audit Log Data From Amazon Aurora to Amazon CloudWatch Logs](AuroraMySQL.Integrating.CloudWatch.md)

## <a name="USER_LogAccess.related"></a>