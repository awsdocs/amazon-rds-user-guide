# Amazon RDS Database Log Files<a name="USER_LogAccess"></a>

You can view, download, and watch database logs using the AWS Management Console, the AWS Command Line Interface \(AWS CLI\), or the Amazon RDS API\. Viewing, downloading, or watching transaction logs isn't supported\. 

For engine\-specific information, see the following:
+ [MariaDB Database Log Files](USER_LogAccess.Concepts.MariaDB.md)
+ [Microsoft SQL Server Database Log Files](USER_LogAccess.Concepts.SQLServer.md)
+ [MySQL Database Log Files](USER_LogAccess.Concepts.MySQL.md)
+ [Oracle Database Log Files](USER_LogAccess.Concepts.Oracle.md)
+ [PostgreSQL Database Log Files](USER_LogAccess.Concepts.PostgreSQL.md)

## Viewing and Listing Database Log Files<a name="USER_LogAccess.Procedural.Viewing"></a>

You can view database log files for your DB engine by using the AWS Management Console\. You can list what log files are available for download or monitoring by using the AWS CLI or Amazon RDS API\. 

**Note**  
 If you can't view the list of log files for an existing Oracle DB instance, reboot the instance to view the list\. 

### Console<a name="USER_LogAccess.CON"></a>

**To view a database log file**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose the name of the DB instance that has the log file that you want to view\.

1. Choose the **Logs & events** tab\.

1. Scroll down to the **Logs** section\. 

1. In the **Logs** section, choose the log that you want to view, and then choose **View**\.

### AWS CLI<a name="USER_LogAccess.CLI"></a>

To list the available database log files for a DB instance, use the AWS CLI [https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-log-files.html](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-log-files.html) command\.

The following example returns a list of log files for a DB instance named `my-db-instance`\.

**Example**  

```
1. aws rds describe-db-log-files --db-instance-identifier my-db-instance
```

### RDS API<a name="USER_LogAccess.API"></a>

To list the available database log files for a DB instance, use the Amazon RDS API [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBLogFiles.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBLogFiles.html) action\.

## Downloading a Database Log File<a name="USER_LogAccess.Procedural.Downloading"></a>

You can use the AWS Management Console, AWS CLI or API to download a database log file\. 

### Console<a name="USER_LogAccess.Procedural.Downloading.CON"></a>

**To download a database log file**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose the name of the DB instance that has the log file that you want to view\.

1. Choose the **Logs & events** tab\.

1. Scroll down to the **Logs** section\. 

1. In the **Logs** section, choose the button next to the log that you want to download, and then choose **Download**\.

1. Open the context \(right\-click\) menu for the link provided, and then choose **Save Link As**\. Enter the location where you want the log file to be saved, and then choose **Save**\.  
![\[viewing log file\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/log_download2.png)

### AWS CLI<a name="USER_LogAccess.Procedural.Downloading.CLI"></a>

To download a database log file, use the AWS CLI command [https://docs.aws.amazon.com/cli/latest/reference/rds/download-db-log-file-portion.html](https://docs.aws.amazon.com/cli/latest/reference/rds/download-db-log-file-portion.html)\. By default, this command downloads only the latest portion of a log file\. However, you can download an entire file by specifying the parameter `--starting-token 0`\.

The following example shows how to download the entire contents of a log file called *log/ERROR\.4* and store it in a local file called *errorlog\.txt*\.

**Example**  
For Linux, macOS, or Unix:  

```
1. aws rds download-db-log-file-portion \
2. 						--db-instance-identifier myexampledb \
3. 						--starting-token 0 --output text \
4. 						--log-file-name log/ERROR.4 > errorlog.txt
```
For Windows:  

```
1. aws rds download-db-log-file-portion ^
2. 						--db-instance-identifier myexampledb ^
3. 						--starting-token 0 --output text ^
4. 						--log-file-name log/ERROR.4 > errorlog.txt
```

### RDS API<a name="USER_LogAccess.Procedural.Downloading.API"></a>

To download a database log file, use the Amazon RDS API [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DownloadDBLogFilePortion.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DownloadDBLogFilePortion.html) action\.

## Watching a Database Log File<a name="USER_LogAccess.Procedural.Watching"></a>

You can monitor the contents of a log file by using the AWS Management Console\.

### Console<a name="USER_LogAccess.Procedural.Watching.CON"></a>

**To watch a database log file**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose the name of the DB instance that has the log file that you want to view\.

1. Choose the **Logs & events** tab\.

1. In the **Logs** section, choose a log file, and then choose **Watch**\.

## Publishing Database Logs to Amazon CloudWatch Logs<a name="USER_LogAccess.Procedural.UploadtoCloudWatch"></a>

In addition to viewing and downloading DB instance logs, you can publish logs to Amazon CloudWatch Logs\. With CloudWatch Logs, you can perform real\-time analysis of the log data, store the data in highly durable storage, and manage the data with the CloudWatch Logs Agent\. AWS retains log data published to CloudWatch Logs for an indefinite time period unless you specify a retention period\. For more information, see [Change Log Data Retention in CloudWatch Logs](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/Working-with-log-groups-and-streams.html#SettingLogRetention)\. 

 For engine\-specific information, see the following:
+ [Publishing MariaDB Logs to Amazon CloudWatch Logs](USER_LogAccess.Concepts.MariaDB.md#USER_LogAccess.MariaDB.PublishtoCloudWatchLogs)
+ [Publishing MySQL Logs to CloudWatch Logs](USER_LogAccess.Concepts.MySQL.md#USER_LogAccess.MySQLDB.PublishtoCloudWatchLogs)
+ [Publishing Oracle Logs to Amazon CloudWatch Logs](USER_LogAccess.Concepts.Oracle.md#USER_LogAccess.Oracle.PublishtoCloudWatchLogs)
+ [Publishing PostgreSQL Logs to CloudWatch Logs](USER_LogAccess.Concepts.PostgreSQL.md#USER_LogAccess.PostgreSQL.PublishtoCloudWatchLogs)
+ [Publishing SQL Server Logs to Amazon CloudWatch Logs](USER_LogAccess.Concepts.SQLServer.md#USER_LogAccess.SQLServer.PublishtoCloudWatchLogs)

## Reading Log File Contents Using REST<a name="DownloadCompleteDBLogFile"></a>

Amazon RDS provides a REST endpoint that allows access to DB instance log files\. This is useful if you need to write an application to stream Amazon RDS log file contents\.

The syntax is:

```
GET /v13/downloadCompleteLogFile/DBInstanceIdentifier/LogFileName HTTP/1.1
Content-type: application/json
host: rds.region.amazonaws.com
```

The following parameters are required:
+ `DBInstanceIdentifier`—the name of the DB instance that contains the log file you want to download\.
+ `LogFileName`—the name of the log file to be downloaded\.

The response contains the contents of the requested log file, as a stream\.

The following example downloads the log file named *log/ERROR\.6* for the DB instance named *sample\-sql* in the *us\-west\-2* region\.

```
GET /v13/downloadCompleteLogFile/sample-sql/log/ERROR.6 HTTP/1.1
host: rds.us-west-2.amazonaws.com
X-Amz-Security-Token: AQoDYXdzEIH//////////wEa0AIXLhngC5zp9CyB1R6abwKrXHVR5efnAVN3XvR7IwqKYalFSn6UyJuEFTft9nObglx4QJ+GXV9cpACkETq=
X-Amz-Date: 20140903T233749Z
X-Amz-Algorithm: AWS4-HMAC-SHA256
X-Amz-Credential: AKIADQKE4SARGYLE/20140903/us-west-2/rds/aws4_request
X-Amz-SignedHeaders: host
X-Amz-Content-SHA256: e3b0c44298fc1c229afbf4c8996fb92427ae41e4649b934de495991b7852b855
X-Amz-Expires: 86400
X-Amz-Signature: 353a4f14b3f250142d9afc34f9f9948154d46ce7d4ec091d0cdabbcf8b40c558
```

If you specify a nonexistent DB instance, the response consists of the following error:
+ `DBInstanceNotFound`—`DBInstanceIdentifier` does not refer to an existing DB instance\. \(HTTP status code: 404\)