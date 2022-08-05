# Viewing and listing database log files<a name="USER_LogAccess.Procedural.Viewing"></a>

You can view database log files for your Amazon RDS DB engine by using the AWS Management Console\. You can list what log files are available for download or monitoring by using the AWS CLI or Amazon RDS API\. 

**Note**  
If you can't view the list of log files for an existing RDS for Oracle DB instance, reboot the instance to view the list\. 

## Console<a name="USER_LogAccess.CON"></a>

**To view a database log file**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose the name of the DB instance that has the log file that you want to view\.

1. Choose the **Logs & events** tab\.

1. Scroll down to the **Logs** section\.

1. \(Optional\) Enter a search term to filter your results\.

1. Choose the log that you want to view, and then choose **View**\.

## AWS CLI<a name="USER_LogAccess.CLI"></a>

To list the available database log files for a DB instance, use the AWS CLI [https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-log-files.html](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-log-files.html) command\.

The following example returns a list of log files for a DB instance named `my-db-instance`\.

**Example**  

```
1. aws rds describe-db-log-files --db-instance-identifier my-db-instance
```

## RDS API<a name="USER_LogAccess.API"></a>

To list the available database log files for a DB instance, use the Amazon RDS API [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBLogFiles.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBLogFiles.html) action\.