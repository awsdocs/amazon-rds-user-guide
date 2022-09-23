# Configuring MySQL binary logging<a name="USER_LogAccess.MySQL.BinaryFormat"></a>

The *binary log* is a set of log files that contain information about data modifications made to an MySQL server instance\. The binary log contains information such as the following:
+ Events that describe database changes such as table creation or row modifications
+ Information about the duration of each statement that updated data
+ Events for statements that could have updated data but didn't

The binary log records statements that are sent during replication\. It is also required for some recovery operations\. For more information, see [The Binary Log](https://dev.mysql.com/doc/refman/8.0/en/binary-log.html) and [Binary Log Overview](https://dev.mysql.com/doc/internals/en/binary-log-overview.html) in the MySQL documentation\.

The automated backups feature determines whether binary logging is turned on or off for MySQL\. You have the following options:

Turn binary logging on  
Set the backup retention period to a positive nonzero value\.

Turn binary logging off  
Set the backup retention period to zero\.

For more information, see [Enabling automated backups](USER_WorkingWithAutomatedBackups.md#USER_WorkingWithAutomatedBackups.Enabling)\.

MySQL on Amazon RDS supports the *row\-based*, *statement\-based*, and *mixed* binary logging formats\. We recommend mixed unless you need a specific binlog format\. For details on the different MySQL binary log formats, see [Binary logging formats](https://dev.mysql.com/doc/refman/8.0/en/binary-log-formats.html) in the MySQL documentation\.

If you plan to use replication, the binary logging format is important because it determines the record of data changes that is recorded in the source and sent to the replication targets\. For information about the advantages and disadvantages of different binary logging formats for replication, see [Advantages and disadvantages of statement\-based and row\-based replication](https://dev.mysql.com/doc/refman/8.0/en/replication-sbr-rbr.html) in the MySQL documentation\.

**Important**  
Setting the binary logging format to row\-based can result in very large binary log files\. Large binary log files reduce the amount of storage available for a DB instance and can increase the amount of time to perform a restore operation of a DB instance\.  
Statement\-based replication can cause inconsistencies between the source DB instance and a read replica\. For more information, see [ Determination of safe and unsafe statements in binary logging](https://dev.mysql.com/doc/refman/8.0/en/replication-rbr-safe-unsafe.html) in the MySQL documentation\.

**To set the MySQL binary logging format**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Parameter groups**\.

1. Choose the parameter group used by the DB instance you want to modify\.

   You can't modify a default parameter group\. If the DB instance is using a default parameter group, create a new parameter group and associate it with the DB instance\.

   For more information on parameter groups, see [Working with parameter groups](USER_WorkingWithParamGroups.md)\.

1. From **Parameter group actions**, choose **Edit**\.

1. Set the `binlog_format` parameter to the binary logging format of your choice \(**ROW**, **STATEMENT**, or **MIXED**\)\. You can also use the value `OFF` to turn off binary logging\.

1. Choose **Save changes** to save the updates to the DB parameter group\.

 After you perform these steps, you must reboot the DB instance for your changes to apply\. For more information, see [Rebooting a DB instance](USER_RebootInstance.md)\. 

**Important**  
Changing a DB parameter group affects all DB instances that use that parameter group\. If you want to specify different binary logging formats for different MySQL DB instances in an AWS Region, the DB instances must use different DB parameter groups\. These parameter groups identify different logging formats\. Assign the appropriate DB parameter group to the each DB instance\.