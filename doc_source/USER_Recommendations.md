# Using Amazon RDS Recommendations<a name="USER_Recommendations"></a>

Amazon RDS provides automated recommendations for database resources, such as DB instances, read replicas, and DB parameter groups\. These recommendations provide best practice guidance by analyzing DB instance configuration, usage, and performance data\.

You can find examples of these recommendations in the following table\.


| Type | Description | Recommendation | Additional Information | 
| --- | --- | --- | --- | 
|  Engine version outdated  |  Your DB instance is not running the latest minor engine version\.  |  We recommend that you upgrade to the latest version because it contains the latest security fixes and other improvements\.  |  [Upgrading a DB Instance Engine Version](USER_UpgradeDBInstance.Upgrading.md)  | 
|  Pending maintenance available  |  You have pending maintenance available on your DB instance\.  |  We recommend that you perform the pending maintenance available on your DB instance\. Updates to the operating system most often occur for security issues and should be done as soon as possible\.  |  [Maintaining a DB Instance](USER_UpgradeDBInstance.Maintenance.md)  | 
|  Automated backups disabled  |  Your DB instance has automated backups disabled\.  |  We recommend that you enable automated backups on your DB instance\. Automated backups enable point\-in\-time recovery of your DB instance\. You receive backup storage up to the storage size of your DB instance at no additional charge\.  |  [Working With Backups](USER_WorkingWithAutomatedBackups.md)  | 
|  Magnetic volumes in use  |  Your DB instance is using magnetic storage\.  |  Magnetic storage is not recommended for most DB instances\. We recommend switching to General Purpose \(SSD\) storage or provisioned IOPS storage\.  |  [Amazon RDS DB Instance Storage](CHAP_Storage.md)  | 
|  EC2\-Classic platform in use  |  Your DB instance is using the legacy EC2\-Classic platform\.  |  We recommend moving your DB instance to the EC2\-VPC platform for better network access control\. Amazon VPC provides a virtual network that is logically isolated from other virtual networks in the AWS Cloud\.  |  [Determining Whether You Are Using the EC2\-VPC or EC2\-Classic Platform](USER_VPC.FindDefaultVPC.md)  | 
|  Enhanced Monitoring disabled  |  Your DB instance doesn't have Enhanced Monitoring enabled\.  |  We recommend enabling Enhanced Monitoring\. Enhanced Monitoring provides real\-time operating system metrics for monitoring and troubleshooting\.  |  [Enhanced Monitoring](USER_Monitoring.OS.md)  | 
|  Encryption disabled  |  Your DB instance doesn't have encryption enabled\.  |  We recommend enabling encryption\. You can encrypt your existing Amazon RDS DB instances by restoring from an encrypted snapshot\.  |  [Encrypting Amazon RDS Resources](Overview.Encryption.md)  | 
|  Previous generation DB instance class in use  |  Your DB instance is running on a previous\-generation DB instance class\.  |  Previous\-generation DB instance classes have been replaced by DB instance classes with better price, better performance, or both\. We recommend running your DB instance on a later generation DB instance class\.  |  [DB Instance Classes](Concepts.DBInstanceClass.md)  | 
|  Huge pages not used for an Oracle DB instance  |  The `use_large_pages` parameter is not set to `ONLY` in the DB parameter group used by your DB instance\.  |  For increased database scalability, we recommend setting `use_large_pages` to `ONLY` in the DB parameter group used by your DB instance\.  |  [Using Huge Pages with an Oracle DB Instance](CHAP_Oracle.md#Oracle.Concepts.HugePages)  | 
|  Nondefault custom memory parameters  |  Your DB parameter group sets memory parameters that diverge too much from the default values\.  |  Settings that diverge too much from the default values can cause poor performance and errors\. We recommend setting custom memory parameters to their default values in the DB parameter group used by the DB instance\.  |  [Working with DB Parameter Groups](USER_WorkingWithParamGroups.md)  | 
|  Change buffering enabled for a MySQL DB instance  |  Your DB parameter group has change buffering enabled\.  |  Change buffering allows a MySQL DB instance to defer some writes necessary to maintain secondary indexes\. This configuration can improve performance slightly, but it can create a large delay in crash recovery\. During crash recovery, the secondary index must be brought up to date\. So, the benefits of change buffering are outweighed by the potentially very long crash recovery events\. We recommend disabling change buffering\.  |  [ Best practices for configuring parameters for Amazon RDS for MySQL, part 1: Parameters related to performance](https://aws.amazon.com/blogs/database/best-practices-for-configuring-parameters-for-amazon-rds-for-mysql-part-1-parameters-related-to-performance/) on the AWS Database Blog  | 
|  Query cache enabled for a MySQL DB instance  |  Your DB parameter group has query cache parameter enabled\.  |  The query cache can cause the DB instance to appear to stall when changes require the cache to be purged\. Most workloads don't benefit from a query cache\. The query cache was removed from MySQL version 8\.0\. We recommend that you disable the query cache parameter\.  |  [ Best practices for configuring parameters for Amazon RDS for MySQL, part 1: Parameters related to performance](https://aws.amazon.com/blogs/database/best-practices-for-configuring-parameters-for-amazon-rds-for-mysql-part-1-parameters-related-to-performance/) on the AWS Database Blog  | 
|  Logging to table  |  Your DB parameter group sets logging output to `TABLE`\.  |  Setting logging output to `TABLE` uses more storage than setting this parameter to `FILE`\. To avoid reaching the storage limit, we recommend setting the logging output parameter to `FILE`\.  |  [MySQL Database Log Files](USER_LogAccess.Concepts.MySQL.md)  | 

Amazon RDS generates recommendations for a resource when the resource is created or modified\. Amazon RDS also periodically scans your resources and generates recommendations\.

## Responding to Amazon RDS Recommendations<a name="USER_Recommendations.Responding"></a>

You can find recommendations in the AWS Management Console\. You can perform the recommended action immediately, schedule it for the next maintenance window, or dismiss it\.

**To respond to Amazon RDS recommendations**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Recommendations**\.  
![\[Select Recommendations in the console\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/recommendations-select.png)

   The Recommendations page appears\.  
![\[Main Recommendations page in the console\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/recommendations-main.png)

1. On the **Recommendations** page, choose one of the following:
   + **Active** – Shows the current recommendations that you can apply, dismiss, or schedule\.
   + **Dismissed** – Shows the recommendations that have been dismissed\. When you choose **Dismissed**, you can apply these dismissed recommendations\.
   + **Scheduled** – Shows the recommendations that are scheduled but not yet applied\. These recommendations will be applied in the next scheduled maintenance window\.
   + **Applied** – Shows the recommendations that are currently applied\.

   From any list of recommendations, you can open a section to view the recommendations in that section\.  
![\[Take action on recommendations in the console\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/recommendations-active.png)

   To configure preferences for displaying recommendations in each section, choose the **Preferences** icon\.  
![\[Preferences icon for Recommendations in the console\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/recommendations-preferences.png)

   From the **Preferences** window that appears, you can set display options\. These options include the visible columns and the number of recommendations to display on the page\.

1. Manage your active recommendations:

   1. Choose **Active** and open one or more sections to view the recommendations in them\.

   1. Choose one or more recommendations and choose **Apply now** \(to apply them immediately\), **Schedule** \(to apply them in next maintenance window\), or **Dismiss**\.

      If the **Apply now** button appears for a recommendation but is unavailable \(grayed out\), the DB instance is not available\. You can apply recommendations immediately only if the DB instance status is **available**\. For example, you can't apply recommendations immediately to the DB instance if its status is **modifying**\. In this case, wait for the DB instance to be available and then apply the recommendation\.

      If the **Apply now** button doesn't appear for a recommendation, you can't apply the recommendation using the **Recommendations** page\. You can modify the DB instance to apply the recommendation manually\.

      For more information about modifying a DB instance, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md)\.
**Note**  
When you choose **Apply now**, a brief DB instance outage might result\.