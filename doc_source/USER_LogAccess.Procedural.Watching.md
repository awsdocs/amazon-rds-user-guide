# Watching a database log file<a name="USER_LogAccess.Procedural.Watching"></a>

Watching a database log file is equivalent to tailing the file on a UNIX or Linux system\. You can watch a log file by using the AWS Management Console\. RDS refreshes the tail of the log every 5 seconds\.

## <a name="USER_LogAccess.Procedural.Watching.CON"></a>

**To watch a database log file**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose the name of the DB instance that has the log file that you want to view\.

1. Choose the **Logs & events** tab\.  
![\[Choose the Logs & events tab\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/Monitoring_logsEvents.png)

1. In the **Logs** section, choose a log file, and then choose **Watch**\.  
![\[Choose a log\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/Monitoring_LogsEvents_watch.png)

   RDS shows the tail of the log, as in the following MySQL example\.  
![\[Tail of a log file\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/Monitoring_LogsEvents_watch_content.png)