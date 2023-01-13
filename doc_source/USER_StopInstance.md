# Stopping an Amazon RDS DB instance temporarily<a name="USER_StopInstance"></a>

Suppose that you use a DB instance intermittently, for temporary testing, or for a daily development activity\. If so, you can stop your Amazon RDS DB instance temporarily to save money\. While your DB instance is stopped, you are charged for provisioned storage \(including Provisioned IOPS\)\. You're also charged for backup storage, including manual snapshots and automated backups within your specified retention window\. However, you're not charged for DB instance hours\. For more information, see [Billing FAQs](http://aws.amazon.com/rds/faqs/#billing)\. 

**Note**  
In some cases, a large amount of time is required to stop a DB instance\. If you want to stop your DB instance and restart it immediately, you can reboot the DB instance\. For information about rebooting a DB instance, see [Rebooting a DB instance](USER_RebootInstance.md)\.

You can stop and start DB instances that are running the following engines: 
+ MariaDB
+ Microsoft SQL Server
+ MySQL
+ Oracle
+ PostgreSQL

Stopping and starting a DB instance is supported for all DB instance classes, and in all AWS Regions\. 

You can stop and start a DB instance whether it is configured for a single Availability Zone\. Or you can do so or for Multi\-AZ, for database engines that support Multi\-AZ deployments\. You can't stop an Amazon RDS for SQL Server DB instance in a Multi\-AZ configuration\. 

**Note**  
For a Multi\-AZ deployment, a large amount of time might be required to stop a DB instance\. If you have at least one backup after a previous failover, then you can speed up the stop DB instance operation\. To do so, perform a reboot with failover operation before stopping the DB instance\.

When you stop a DB instance, the DB instance performs a normal shutdown and stops running\. The status of the DB instance changes to `stopping` and then `stopped`\.  Occasionally, an RDS for PostgreSQL DB instance doesn't shut down cleanly\. If this happens, you see that the instance goes through a recovery process when you restart it later\. This is expected behavior of the database engine, intended to protect database integrity\. Some memory\-based statistics and counters don't retain history and are re\-initialized after restart, to capture the operational workload moving forward\.

At the end of the normal shutdown process, any storage volumes remain attached to the DB instance, and their data is kept\. Any data stored in the RAM of the DB instance is deleted\. 

Stopping a DB instance removes pending actions, except for pending actions for the DB instance's option group or DB parameter group\.

Automated backups aren't created while a DB instance is stopped\. Backups can be retained longer than the backup retention period if a DB instance has been stopped\. RDS doesn't include time spent in the `stopped` state when the backup retention window is calculated\.

**Important**  
You can stop a DB instance for up to seven days\. If you don't manually start your DB instance after seven days, your DB instance is automatically started\. This way, it doesn't fall behind any required maintenance updates\.

## Benefits<a name="USER_StopInstance.Benefits"></a>

Stopping and starting a DB instance is faster than creating a DB snapshot, and then restoring the snapshot\. 

When you stop a DB instance it retains its ID, Domain Name Server \(DNS\) endpoint, parameter group, security group, and option group\. When you start a DB instance, it has the same configuration as when you stopped it\. In addition, if you stop a DB instance, Amazon RDS keeps the Amazon S3 transaction logs\. This means that you can do a point\-in\-time restore if necessary\. 

## Limitations<a name="USER_StopInstance.Limitations"></a>

The following are some limitations to stopping and starting a DB instance: 
+ You can't stop a DB instance that has a read replica, or that is a read replica\.
+ You can't stop an Amazon RDS for SQL Server DB instance in a Multi\-AZ configuration\.
+ You can't modify a stopped DB instance\.
+ You can't delete an option group that is associated with a stopped DB instance\.
+ You can't delete a DB parameter group that is associated with a stopped DB instance\.
+ In a Multi\-AZ configuration, the primary and secondary Availability Zones might be switched after you start the DB instance\.

## Option and parameter group considerations<a name="USER_StopInstance.OGPG"></a>

You can't remove persistent options \(including permanent options\) from an option group if there are DB instances associated with that option group\. This functionality is also true of any DB instance with a state of `stopping`, `stopped`, or `starting`\. 

You can change the option group or DB parameter group that is associated with a stopped DB instance\. However, the change doesn't occur until the next time you start the DB instance\. If you chose to apply changes immediately, the change occurs when you start the DB instance\. Otherwise the change occurs during the next maintenance window after you start the DB instance\. 

## Public IP address<a name="USER_StopInstance.PublicIPAddress"></a>

When you stop a DB instance, it retains its DNS endpoint\. If you stop a DB instance that has a public IP address, Amazon RDS releases its public IP address\. When the DB instance is restarted, it has a different public IP address\. 

**Note**  
You should always connect to a DB instance using the DNS endpoint, not the IP address\.

## Stopping a DB instance temporarily<a name="USER_StopInstance.Stopping"></a>

You can stop a DB using the AWS Management Console, the AWS CLI, or the RDS API\.

### Console<a name="USER_StopInstance.CON"></a>

**To stop a DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**, and then choose the DB instance that you want to stop\. 

1. For **Actions**, choose **Stop temporarily**\. 

1. In the **Stop DB instance temporarily** window, select the acknowledgement that the DB instance will restart automatically after 7 days\.

1. \(Optional\) Select **Save the DB instance in a snapshot** and enter the snapshot name for **Snapshot name**\. Choose this option if you want to create a snapshot of the DB instance before stopping it\.

1. Choose **Stop temporarily** to stop the DB instance, or choose **Cancel** to cancel the operation\.

### AWS CLI<a name="USER_StopInstance.CLI"></a>

To stop a DB instance by using the AWS CLI, call the [stop\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/stop-db-instance.html) command with the following option: 
+ `--db-instance-identifier` – the name of the DB instance\. 

**Example**  

```
1. aws rds stop-db-instance --db-instance-identifier mydbinstance
```

### RDS API<a name="USER_StopInstance.API"></a>

To stop a DB instance by using the Amazon RDS API, call the [StopDBInstance](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_StopDBInstance.html) operation with the following parameter: 
+ `DBInstanceIdentifier` – the name of the DB instance\. 