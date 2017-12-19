# Restoring License\-Terminated DB Instances<a name="Appendix.SQLServer.CommonDBATasks.RestoreLTI"></a>

Microsoft has requested that some Amazon RDS customers who did not report their Microsoft License Mobility information terminate their DB instance\. Amazon RDS takes snapshots of these DB instances, and you can restore from the snapshot to a new DB instance that has the License Included model\. 

You can restore from a snapshot of Standard Edition to either Standard Edition or Enterprise Edition\. 

You can restore from a snapshot of Enterprise Edition to either Standard Edition or Enterprise Edition\. 

**To restore from a SQL Server snapshot after Amazon RDS has created a final snapshot of your instance:**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Snapshots**\.

1. Choose the snapshot of your SQL Server DB instance\. Amazon RDS created a final snapshot of your DB instance; the name of the terminated instance snapshot is in the format: '<name of instance>\-final\-snapshot'\. For example, if your DB instance name was **mytest\.cdxgahslksma\.us\-east\-1\.rds\.com**, the final snapshot would be called** mytest\-final\-snapshot** and would be located in the same region as the original DB instance\. 

1. Choose **Restore Snapshot**\.

   The **Restore DB Instance** window appears\.

1. For **License Model** choose **license\-included**\. 

1. Choose the SQL Server DB engine you want to use\. 

1. In the **DB Instance Identifier** text box type the name for the restored DB instance\. 

1. Choose **Restore DB Instance**\.

For more information about restoring from a snapshot, see [Restoring from a DB Snapshot](USER_RestoreFromSnapshot.md)\. 