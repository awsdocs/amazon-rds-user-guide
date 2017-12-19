# Stopping an Amazon RDS DB Instance Temporarily<a name="USER_StopInstance"></a>

If you use a DB instance intermittently, for temporary testing, or for a daily development activity, you can stop your Amazon RDS DB instance temporarily to save money\. While your DB instance is stopped, you are charged for provisioned storage \(including Provisioned IOPS\) and backup storage \(including manual snapshots and automated backups within your specified retention window\), but not for DB instance hours\. For more information, see [Billing FAQs](http://aws.amazon.com/rds/faqs/#billing)\. 

You can stop and start DB instances that are running the following engines: MariaDB, Microsoft SQL Server, MySQL, Oracle, and PostgreSQL\. Stopping and starting a DB instance is supported for all DB instance classes, and in all AWS Regions\. 

When you stop a DB instance, the DB instance performs a normal shutdown and stops running\. The status of the DB instance changes to `stopping` and then `stopped`\. Any storage volumes remain attached to the DB instance, and their data is kept\. Any data stored in the RAM of the DB instance is deleted\. Amazon RDS automatically backs up a stopped DB instance\. 

You can stop a DB instance for up to seven days\. If you do not manually start your DB instance after seven days, your DB instance is automatically started\. 

## Benefits<a name="USER_StopInstance.Benefits"></a>

Stopping and starting a DB instance is faster than creating a DB snapshot, and then restoring the snapshot\. 

When you stop a DB instance it retains its ID, Domain Name Server \(DNS\) endpoint, parameter group, security group, and option group\. When you start a DB instance, it has the same configuration as when you stopped it\. In addition, if you stop a DB instance, Amazon RDS retains the Amazon Simple Storage Service \(Amazon S3\) transaction logs so you can do a point\-in\-time restore if necessary\. 

## Limitations<a name="USER_StopInstance.Limitations"></a>

The following are some limitations to stopping and starting a DB instance: 

+ You can't stop a DB instance that has a Read Replica, or that is a Read Replica\.

+ You can't stop a DB instance that is in a Multi\-AZ deployment\.

+ You can't stop a DB instance that uses Microsoft SQL Server Mirroring\.

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

## AWS Management Console<a name="USER_StopInstance.CON"></a>

**To stop a DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **DB Instances**, and then select the DB instance that you want to modify\. 

1. Choose **Instance Actions**, and then choose **Stop**\. 

1. Choose **Continue**\. 

## CLI<a name="USER_StopInstance.CLI"></a>

To stop a DB instance by using the AWS CLI, call the [stop\-db\-instance](http://docs.aws.amazon.com/cli/latest/reference/rds/stop-db-instance.html) command with the following parameters: 

+ `--db-instance-identifier` – the name of the db instance\. 

**Example**  

```
1. stop-db-instance --db-instance-identifier mydbinstance
```

## API<a name="USER_StopInstance.API"></a>

To stop a DB instance by using the Amazon RDS API, call the [StopDBInstance](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_StopDBInstance.html) action with the following parameters: 

+ `DBInstanceIdentifier` – the name of the db instance\. 

**Example**  

```
 1. https://rds.amazonaws.com/
 2.     ?Action=StopDBInstance
 3.     &DBInstanceIdentifier=mydbinstance
 4.     &SignatureMethod=HmacSHA256
 5.     &SignatureVersion=4
 6.     &Version=2014-10-31
 7.     &X-Amz-Algorithm=AWS4-HMAC-SHA256
 8.     &X-Amz-Credential=AKIADQKE4SARGYLE/20131016/us-west-1/rds/aws4_request
 9.     &X-Amz-Date=20131016T233051Z
10.     &X-Amz-SignedHeaders=content-type;host;user-agent;x-amz-content-sha256;x-amz-date
11.     &X-Amz-Signature=087a8eb41cb1ab5f99e81575f23e73757ffc6a1e42d7d2b30b9cc0be988cff97
```

## Related Topics<a name="USER_StopInstance.Related"></a>

+ [Starting an Amazon RDS DB Instance That Was Previously Stopped](USER_StartInstance.md)

+ [Deleting a DB Instance](USER_DeleteInstance.md)

+ [Rebooting a DB Instance](USER_RebootInstance.md)