# Working with Microsoft SQL Server Logs<a name="Appendix.SQLServer.CommonDBATasks.Logs"></a>

You can use the Amazon RDS console to view, watch, and download SQL Server Agent logs and Microsoft SQL Server error logs\.

## Watching Log Files<a name="Appendix.SQLServer.CommonDBATasks.Logs.Watch"></a>

If you view a log in the Amazon RDS console, you can see its contents as they exist at that moment\. Watching a log in the console opens it in a dynamic state so that you can see updates to it in near\-real time\.

Only the latest log is active for watching\. For example, suppose you have the following logs shown:

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/logs.png)

Only log/ERROR, as the most recent log, is being actively updated\. You can choose to watch others, but they are static and will not update\.

## Archiving Log Files<a name="Appendix.SQLServer.CommonDBATasks.Logs.Archive"></a>

The Amazon RDS console shows logs for the past week through the current day\. You can download and archive logs to keep them for reference past that time\. One way to archive logs is to load them into an Amazon S3 bucket\. For instructions on how to set up an Amazon S3 bucket and upload a file, see [Amazon S3 Basics](https://docs.aws.amazon.com/AmazonS3/latest/gsg/AmazonS3Basics.html) in the *Amazon Simple Storage Service Getting Started Guide* and click **Get Started**\. 

## Viewing Error and Agent Logs<a name="Appendix.SQLServer.CommonDBATasks.Logs.SP"></a>

To view Microsoft SQL server error and agent logs, use the Amazon RDS stored procedure `rds_read_error_log` with the following parameters: 
+ **`@index`** – the version of the log to retrieve\. The default value is 0, which retrieves the current error log\. Specify 1 to retrieve the previous log, specify 2 to retrieve the one before that, and so on\. 
+ **`@type`** – the type of log to retrieve\. Specify 1 to retrieve an error log\. Specify 2 to retrieve an agent log\. 

**Example**  
The following example requests the current error log\.   

```
EXEC rdsadmin.dbo.rds_read_error_log @index = 0, @type = 1;
```