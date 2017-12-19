# Amazon RDS Database Log Files<a name="USER_LogAccess"></a>

You can view, download, and watch database logs using the Amazon RDS console, the AWS Command Line Interface \(AWS CLI\), or the Amazon RDS API\. Viewing, downloading, or watching transaction logs is not supported\. 

For engine\-specific documentation, see the following: 


****  

| Database Engine | Relevant Documentation | 
| --- | --- | 
|  MariaDB  |  You can access the error log, the slow query log, and the general log\. For more information, see [MariaDB Database Log Files](USER_LogAccess.Concepts.MariaDB.md)\.  | 
|  Microsoft SQL Server  |  You can access SQL Server error logs, agent logs, and trace files\. For more information, see [Microsoft SQL Server Database Log Files](USER_LogAccess.Concepts.SQLServer.md)\.  | 
|  MySQL  |  You can access the error log, the slow query log, and the general log\. For more information, see [MySQL Database Log Files](USER_LogAccess.Concepts.MySQL.md)\.  | 
|  Oracle  |  You can access Oracle alert logs, audit files, and trace files\. For more information, see [Oracle Database Log Files](USER_LogAccess.Concepts.Oracle.md)\.  | 
|  PostgreSQL  |  You can access query logs and error logs\. Error logs can contain auto\-vacuum and connection information, as well as rds\_admin actions\. For more information, see [PostgreSQL Database Log Files](USER_LogAccess.Concepts.PostgreSQL.md)\.  | 

## Viewing and Listing Database Log Files<a name="USER_LogAccess.Procedural.Viewing"></a>

You can view database log files for your DB engine by using the Amazon RDS console\. You can list what log files are available for download or monitoring by using the Amazon RDS CLI or APIs\. 

**Note**  
 If you cannot view the list of log files for an existing Oracle DB instance, reboot the instance to view the list\. 

### AWS Management Console<a name="USER_LogAccess.CON"></a>

**To view a database log file**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Instances**\.

1. Choose the DB instance that has the log file you want to view, and then choose **Instance Actions | See Details**\. 

1.  Choose the **Recent Events & Logs** tab\. 

1. In the **Logs** pane, choose the **View** button next to the log you want to view\.

### CLI<a name="USER_LogAccess.CLI"></a>

To list the available database log files for a DB instance use the AWS CLI [http://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-log-files.html](http://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-log-files.html) command\.

The following example directs a list of log files for a DB instance named `my-db-instance` to a text file called log\_file\_list\.txt\.

**Example**  

```
1. aws rds describe-db-log-files --db-instance-identifier my-db-instance > log_file_list.txt
```

### API<a name="USER_LogAccess.API"></a>

To list the available database log files for a DB instance call the Amazon RDS API [http://docs.aws.amazon.com/AmazonRDS/latest/APIReference//API_DescribeDBLogFiles.html](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference//API_DescribeDBLogFiles.html) action\.

## Downloading a Database Log File<a name="USER_LogAccess.Procedural.Downloading"></a>

You can use the Amazon RDS console or the AWS CLI to download a database log file\. 

You can download a complete log file by using the [DownloadCompleteDBLogFile](RESTReference.md#RESTReference.DownloadCompleteDBLogFile) REST API\. You can also download complete log files by using the `rds-download-db-logfile` RDS CLI command\. For more information, see [ The `rds-download-db-logfile` Command ](RESTReference.md#RESTReference.DownloadCompleteDBLogFile.CLIversion)\. 

### AWS Management Console<a name="USER_LogAccess.Procedural.Downloading.CON"></a>

**To download a database log file**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Instances**\.

1. Choose the DB instance that has the log file you want to view, and then choose **Instance Actions | See Details**\. 

1. Choose the **Recent Events & Logs** tab\. 

1. In the **Logs** pane, choose the **Download** button next to the log you want to download\.

1. Right\-click the link provided, and then choose **Save Link As\.\.\.** from the dropdown menu\. Type the location where you want the log file to be saved, then choose **Save**\.  
![\[viewing log file\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/log_download2.png)

### CLI<a name="USER_LogAccess.Procedural.Downloading.CLI"></a>

To download a database log file use the command [http://docs.aws.amazon.com/cli/latest/reference/rds/download-db-log-file-portion.html](http://docs.aws.amazon.com/cli/latest/reference/rds/download-db-log-file-portion.html)\.

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

1. Choose the DB instance that has the log file you want to watch, and then choose **Instance Actions | See Details**\. 

1.  Choose the **Recent Events & Logs** tab\. 

1. In the **Logs** pane, choose the **Watch** button next to the log you want to watch\.

## Related Topics<a name="USER_LogAccess.related"></a>

+ [Monitoring Amazon RDS](CHAP_Monitoring.md)

+ [Using Amazon RDS Event Notification](USER_Events.md)