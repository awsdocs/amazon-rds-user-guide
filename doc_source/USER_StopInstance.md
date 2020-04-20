# Stopping an Amazon RDS DB Instance Temporarily<a name="USER_StopInstance"></a>

If you use a DB instance intermittently, for temporary testing, or for a daily development activity, you can stop your Amazon RDS DB instance temporarily to save money\. While your DB instance is stopped, you are charged for provisioned storage \(including Provisioned IOPS\) and backup storage \(including manual snapshots and automated backups within your specified retention window\), but not for DB instance hours\. For more information, see [Billing FAQs](http://aws.amazon.com/rds/faqs/#billing)\. 

**Note**  
In some cases, a large amount of time is required to stop a DB instance\. If you want to stop your DB instance and restart it immediately, you can reboot the DB instance\. For information about rebooting a DB instance, see [Rebooting a DB Instance](USER_RebootInstance.md)\.

You can stop and start DB instances that are running the following engines: 
+ MariaDB
+ Microsoft SQL Server
+ MySQL
+ Oracle
+ PostgreSQL

Stopping and starting a DB instance is supported for all DB instance classes, and in all AWS Regions\. 

You can stop and start a DB instance whether it is configured for a single Availability Zone or for Multi\-AZ, for database engines that support Multi\-AZ deployments\. You can't stop an Amazon RDS for SQL Server DB instance in a Multi\-AZ configuration\. 

**Note**  
For a Multi\-AZ deployment, a large amount of time might be required to stop a DB instance\. If you have at least one backup after a previous failover, then you can speed up the stop DB instance operation by performing a reboot with failover operation before stopping the DB instance\.

When you stop a DB instance, the DB instance performs a normal shutdown and stops running\. The status of the DB instance changes to `stopping` and then `stopped`\. Any storage volumes remain attached to the DB instance, and their data is kept\. Any data stored in the RAM of the DB instance is deleted\. 

Stopping a DB instance removes pending actions, except for pending actions for the DB instance's option group or DB parameter group\.

**Important**  
You can stop a DB instance for up to seven days\. If you don't manually start your DB instance after seven days, your DB instance is automatically started so that it doesn't fall behind any required maintenance updates\.

## Benefits<a name="USER_StopInstance.Benefits"></a>

Stopping and starting a DB instance is faster than creating a DB snapshot, and then restoring the snapshot\. 

When you stop a DB instance it retains its ID, Domain Name Server \(DNS\) endpoint, parameter group, security group, and option group\. When you start a DB instance, it has the same configuration as when you stopped it\. In addition, if you stop a DB instance, Amazon RDS retains the Amazon Simple Storage Service \(Amazon S3\) transaction logs so you can do a point\-in\-time restore if necessary\. 

## Limitations<a name="USER_StopInstance.Limitations"></a>

The following are some limitations to stopping and starting a DB instance: 
+ You can't stop a DB instance that has a read replica, or that is a read replica\.
+ You can't stop an Amazon RDS for SQL Server DB instance in a Multi\-AZ configuration\.
+ You can't modify a stopped DB instance\.
+ You can't delete an option group that is associated with a stopped DB instance\.
+ You can't delete a DB parameter group that is associated with a stopped DB instance\.

## Option and Parameter Group Considerations<a name="USER_StopInstance.OGPG"></a>

You can't remove persistent options \(including permanent options\) from an option group if there are DB instances associated with that option group\. This functionality is also true of any DB instance with a state of `stopping`, `stopped`, or `starting`\. 

You can change the option group or DB parameter group that is associated with a stopped DB instance, but the change does not occur until the next time you start the DB instance\. If you chose to apply changes immediately, the change occurs when you start the DB instance\. Otherwise the changes occurs during the next maintenance window after you start the DB instance\. 

## VPC Considerations<a name="USER_StopInstance.VPC"></a>

When you stop a DB instance it retains its DNS endpoint\. If you stop a DB instance that is not in an Amazon Virtual Private Cloud \(Amazon VPC\), Amazon RDS releases the IP addresses of the DB instance\. If you stop a DB instance that is in a VPC, the DB instance retains its IP addresses\. 

**Note**  
You should always connect to a DB instance using the DNS endpoint, not the IP address\.

## Console<a name="USER_StopInstance.CON"></a>

**To stop a DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**, and then choose the DB instance that you want to stop\. 

1. For **Actions**, choose **Stop**\. 

1. \(Optional\) In the **Stop DB Instance** window, choose **Yes** for **Create Snapshot?** and enter the snapshot name for **Snapshot name**\. Choose **Yes** if you want to create a snapshot of the DB instance before stopping it\. 

1. Choose **Yes, Stop Now** to stop the DB instance, or choose **Cancel** to cancel the operation\.

## AWS CLI<a name="USER_StopInstance.CLI"></a>

To stop a DB instance by using the AWS CLI, call the [stop\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/stop-db-instance.html) command with the following parameters: 
+ `--db-instance-identifier` – the name of the DB instance\. 

**Example**  

```
1. stop-db-instance --db-instance-identifier mydbinstance
```

## RDS API<a name="USER_StopInstance.API"></a>

To stop a DB instance by using the Amazon RDS API, call the [StopDBInstance](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_StopDBInstance.html) operation with the following parameter: 
+ `DBInstanceIdentifier` – the name of the DB instance\. 