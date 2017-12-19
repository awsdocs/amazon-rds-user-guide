# Rebooting a DB Instance<a name="USER_RebootInstance"></a>

You might need to reboot your DB instance, usually for maintenance reasons\. For example, if you make certain modifications, or if you change the DB parameter group associated with the DB instance, you must reboot the instance for the changes to take effect\. 

Rebooting a DB instance restarts the database engine service\. Rebooting a DB instance results in a momentary outage, during which the DB instance status is set to *rebooting*\. If the Amazon RDS instance is configured for Multi\-AZ, the reboot can be conducted with a failover\. An Amazon RDS event is created when the reboot is completed\. 

If your DB instance is a Multi\-AZ deployment, you can force a failover from one availability zone to another when you reboot\. When you force a failover of your DB instance, Amazon RDS automatically switches to a standby replica in another Availability Zone, and updates the DNS record for the DB instance to point to the standby DB instance\. As a result, you need to clean up and re\-establish any existing connections to your DB instance\. Rebooting with failover is beneficial when you want to simulate a failure of a DB instance for testing, or restore operations to the original AZ after a failover occurs\. For more information, see [High Availability \(Multi\-AZ\)](Concepts.MultiAZ.md)\. 

When you reboot the primary instance of an Amazon Aurora DB cluster, RDS also automatically reboots all of the Aurora Replicas in that DB cluster\. When you reboot the primary instance of an Aurora DB cluster, no failover occurs\. When you reboot an Aurora Replica, no failover occurs\. To failover an Aurora DB cluster, call the AWS CLI command [http://docs.aws.amazon.com/cli/latest/reference/rds/failover-db-cluster.html](http://docs.aws.amazon.com/cli/latest/reference/rds/failover-db-cluster.html), or the API action [http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_FailoverDBCluster.html](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_FailoverDBCluster.html)\. 

You can't reboot your DB instance if it is not in the "Available" state\. Your database can be unavailable for several reasons, such as an in\-progress backup, a previously requested modification, or a maintenance\-window action\. 

The time required to reboot your DB instance depends on the crash recovery process of your specific database engine\. To improve the reboot time, we recommend that you reduce database activities as much as possible during the reboot process Reduce database activity reduces rollback activity for in\-transit transactions\. 

## AWS Management Console<a name="USER_RebootInstance.Console"></a>

**To reboot a DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Instances**, and then select the DB instance that you want to reboot\. 

1. Choose **Instance Actions** and then choose **Reboot**\. 

   The **Reboot DB Instance** page appears\.

1. \(Optional\) Select **Reboot with failover?** to force a failover from one AZ to another\. 

1. Choose **Reboot** to reboot your DB instance\. 

   Alternatively, choose **Cancel**\. 

## CLI<a name="USER_RebootInstance.CLI"></a>

To reboot a DB instance by using the AWS CLI, call the [http://docs.aws.amazon.com/cli/latest/reference/rds/reboot-db-instance.html](http://docs.aws.amazon.com/cli/latest/reference/rds/reboot-db-instance.html) command\. 

**Example Simple Reboot**  
For Linux, OS X, or Unix:  

```
1. aws rds reboot-db-instance \
2.     --db-instance-identifier mydbinstance
```
For Windows:  

```
1. aws rds reboot-db-instance ^
2.     --db-instance-identifier mydbinstance
```

**Example Reboot with Failover**  
To force a failover from one AZ to the other, use the `--force-failover` parameter\.   
For Linux, OS X, or Unix:  

```
1. aws rds reboot-db-instance \
2.     --db-instance-identifier mydbinstance \
3.     --force-failover
```
For Windows:  

```
1. aws rds reboot-db-instance ^
2.     --db-instance-identifier mydbinstance ^
3.     --force-failover
```

## API<a name="USER_RebootInstance.API"></a>

To reboot a DB instance by using the Amazon RDS API, call the [http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RebootDBInstance.html](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RebootDBInstance.html) action\. 

**Example Simple Reboot**  

```
1. https://rds.amazonaws.com/
2. 	?Action=RebootDBInstance
3.     &DBInstanceIdentifier=mydbinstance
4.     &Version=2014-10-31						
5.     &X-Amz-Algorithm=AWS4-HMAC-SHA256
6.     &X-Amz-Credential=AKIADQKE4SARGYLE/20131016/us-west-1/rds/aws4_request
7.     &X-Amz-Date=20131016T233051Z
8.     &X-Amz-SignedHeaders=content-type;host;user-agent;x-amz-content-sha256;x-amz-date
9.     &X-Amz-Signature=087a8eb41cb1ab5f99e81575f23e73757ffc6a1e42d7d2b30b9cc0be988cff97
```

**Example Reboot with Failover**  
To force a failover from one AZ to the other, set the `ForceFailover` parameter to `true`\.   

```
 1. https://rds.amazonaws.com/
 2.     ?Action=RebootDBInstance
 3.     &DBInstanceIdentifier=mydbinstance
 4.     &ForceFailover=true
 5.     &Version=2014-10-31						
 6.     &X-Amz-Algorithm=AWS4-HMAC-SHA256
 7.     &X-Amz-Credential=AKIADQKE4SARGYLE/20131016/us-west-1/rds/aws4_request
 8.     &X-Amz-Date=20131016T233051Z
 9.     &X-Amz-SignedHeaders=content-type;host;user-agent;x-amz-content-sha256;x-amz-date
10.     &X-Amz-Signature=087a8eb41cb1ab5f99e81575f23e73757ffc6a1e42d7d2b30b9cc0be988cff97
```