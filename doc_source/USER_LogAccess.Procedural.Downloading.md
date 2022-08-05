# Downloading a database log file<a name="USER_LogAccess.Procedural.Downloading"></a>

You can use the AWS Management Console, AWS CLI, or API to download a database log file\. 

## Console<a name="USER_LogAccess.Procedural.Downloading.CON"></a>

**To download a database log file**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose the name of the DB instance that has the log file that you want to view\.

1. Choose the **Logs & events** tab\.

1. Scroll down to the **Logs** section\. 

1. In the **Logs** section, choose the button next to the log that you want to download, and then choose **Download**\.

1. Open the context \(right\-click\) menu for the link provided, and then choose **Save Link As**\. Enter the location where you want the log file to be saved, and then choose **Save**\.  
![\[viewing log file\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/log_download2.png)

## AWS CLI<a name="USER_LogAccess.Procedural.Downloading.CLI"></a>

To download a database log file, use the AWS CLI command [https://docs.aws.amazon.com/cli/latest/reference/rds/download-db-log-file-portion.html](https://docs.aws.amazon.com/cli/latest/reference/rds/download-db-log-file-portion.html)\. By default, this command downloads only the latest portion of a log file\. However, you can download an entire file by specifying the parameter `--starting-token 0`\.

The following example shows how to download the entire contents of a log file called *log/ERROR\.4* and store it in a local file called *errorlog\.txt*\.

**Example**  
For Linux, macOS, or Unix:  

```
1. aws rds download-db-log-file-portion \
2.     --db-instance-identifier myexampledb \
3.     --starting-token 0 --output text \
4.     --log-file-name log/ERROR.4 > errorlog.txt
```
For Windows:  

```
1. aws rds download-db-log-file-portion ^
2.     --db-instance-identifier myexampledb ^
3.     --starting-token 0 --output text ^
4.     --log-file-name log/ERROR.4 > errorlog.txt
```

## RDS API<a name="USER_LogAccess.Procedural.Downloading.API"></a>

To download a database log file, use the Amazon RDS API [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DownloadDBLogFilePortion.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DownloadDBLogFilePortion.html) action\.