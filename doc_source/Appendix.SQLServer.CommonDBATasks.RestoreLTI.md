# Restoring License\-Terminated DB Instances<a name="Appendix.SQLServer.CommonDBATasks.RestoreLTI"></a>

Microsoft has requested that some Amazon RDS customers who did not report their Microsoft License Mobility information terminate their DB instance\. Amazon RDS takes snapshots of these DB instances, and you can restore from the snapshot to a new DB instance that has the License Included model\. 

You can restore from a snapshot of Standard Edition to either Standard Edition or Enterprise Edition\. 

You can restore from a snapshot of Enterprise Edition to either Standard Edition or Enterprise Edition\. 

**To restore from a SQL Server snapshot after Amazon RDS has created a final snapshot of your instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Snapshots**\.

1. Choose the snapshot of your SQL Server DB instance\. Amazon RDS creates a final snapshot of your DB instance\. The name of the terminated instance snapshot is in the format `instance_name-final-snapshot`\. For example, if your DB instance name is **mytest\.cdxgahslksma\.us\-east\-1\.rds\.com**, the final snapshot is called** mytest\-final\-snapshot** and is located in the same AWS Region as the original DB instance\. 

1. For **Actions**, choose **Restore Snapshot**\.

   The **Restore DB Instance** window appears\.

1. For **License Model**, choose **license\-included**\. 

1. Choose the SQL Server DB engine that you want to use\. 

1. For **DB Instance Identifier**, enter the name for the restored DB instance\. 

1. Choose **Restore DB Instance**\.

For more information about restoring from a snapshot, see [Restoring from a DB Snapshot](USER_RestoreFromSnapshot.md)\. 