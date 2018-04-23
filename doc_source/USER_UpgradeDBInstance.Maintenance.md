# Maintaining an Amazon RDS DB Instance<a name="USER_UpgradeDBInstance.Maintenance"></a>

Periodically, Amazon RDS performs maintenance on Amazon RDS resources\. Maintenance most often involves updates to the DB instance's or DB cluster's underlying operating system \(OS\) or database engine version\. Updates to the operating system most often occur for security issues and should be done as soon as possible\. 

Maintenance items require that Amazon RDS take your DB instance or DB cluster offline for a short time\. Maintenance that requires a resource to be offline include scale compute operations, which generally take only a few minutes from start to finish, and required operating system or database patching\. Required patching is automatically scheduled only for patches that are related to security and instance reliability\. Such patching occurs infrequently \(typically once every few months\) and seldom requires more than a fraction of your maintenance window\. 

DB instances are not automatically backed up when an OS update is applied, so you should back up your DB instances before you apply an update\. 

You can view whether a maintenance update is available for your DB instance or DB cluster by using the RDS console, the AWS CLI, or the Amazon RDS API\. If an update is available, it is indicated by the word **Available** or **Required** in the **Maintenance** column for the DB instance or DB cluster on the Amazon RDS console, as shown following: 

![\[Offline patch available\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/offlinepatchavailable.png)

If an update is available, you can take one of the actions\. 
+ Defer the maintenance items\.
+ Apply the maintenance items immediately\.
+ Schedule the maintenance items to start during your next maintenance window\.
+ Take no action\.

**Note**  
Certain OS updates are marked as **Required**\. If you defer a required update, you receive a notice from Amazon RDS indicating when the update will be performed on your DB instance or DB cluster\. Other updates are **Available**\. You can defer these updates indefinitely\.

The maintenance window determines when pending operations start, but does not limit the total execution time of these operations\. Maintenance operations are not guaranteed to finish before the maintenance window ends, and can continue beyond the specified end time\. For more information, see [The Amazon RDS Maintenance Window](#Concepts.DBMaintenance)\. 

## Applying Updates for a DB Instance or DB Cluster<a name="USER_UpgradeDBInstance.OSUpgrades"></a>

With Amazon RDS, you can choose when to apply maintenance operations\. You can decide when Amazon RDS applies updates by using the RDS console, AWS Command Line Interface \(AWS CLI\), or RDS API\. 

Use the procedures in this topic to immediately upgrade or schedule an upgrade for your DB instance\. For more information, see [Maintaining an Amazon RDS DB Instance](#USER_UpgradeDBInstance.Maintenance)\. 

### AWS Management Console<a name="USER_UpgradeDBInstance.OSUpgrades.Console"></a>

**To manage an update for a DB instance or DB cluster**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Instances** to manage updates for a DB instance, or **Clusters** to manage updates for an Aurora DB cluster\. 

1. Select the check box for the DB instance or DB cluster that has a required update\. 

1. Choose **Instance actions** for a DB instance, or **Actions** for a DB cluster, and then choose one of the following:
   + **Upgrade now**
   + **Upgrade at next window**
**Note**  
If you choose **Upgrade at next window** and later want to delay the update, you can select **Defer upgrade**\.

### CLI<a name="USER_UpgradeDBInstance.OSUpgrades.CLI"></a>

To apply a pending update to a DB instance or DB cluster, use the [apply\-pending\-maintenance\-action](http://docs.aws.amazon.com/cli/latest/reference/rds/apply-pending-maintenance-action.html) AWS CLI command\.

**Example**  
For Linux, OS X, or Unix:  

```
1. aws rds apply-pending-maintenance-action \
2.     --resource-identifier arn:aws:rds:us-west-2:001234567890:db:mysql-db \
3.     --apply-action system-update \
4.     --opt-in-type immediate
```
For Windows:  

```
1. aws rds apply-pending-maintenance-action ^
2.     --resource-identifier arn:aws:rds:us-west-2:001234567890:db:mysql-db ^
3.     --apply-action system-update ^
4.     --opt-in-type immediate
```

To return a list of resources that have at least one pending update, use the [describe\-pending\-maintenance\-actions](http://docs.aws.amazon.com/cli/latest/reference/rds/describe-pending-maintenance-actions.html) AWS CLI command\.

**Example**  
For Linux, OS X, or Unix:  

```
1. aws rds describe-pending-maintenance-actions \
2.     --resource-identifier arn:aws:rds:us-west-2:001234567890:db:mysql-db
```
For Windows:  

```
1. aws rds describe-pending-maintenance-actions ^
2.     --resource-identifier arn:aws:rds:us-west-2:001234567890:db:mysql-db
```

You can also return a list of resources for a DB instance or DB cluster by specifying the `--filters` parameter of the `describe-pending-maintenance-actions` AWS CLI command\. The format for the `--filters` command is `Name=filter-name,Value=resource-id,...`\.

The following are the accepted values for the `Name` parameter of a filter:
+ `db-instance-id` – Accepts a list of DB instance identifiers or Amazon Resource Names \(ARNs\)\. The returned list only includes pending maintenance actions for the DB instances identified by these identifiers or ARNs\.
+ `db-cluster-id` – Accepts a list of DB cluster identifiers or ARNs\. The returned list only includes pending maintenance actions for the DB clusters identified by these identifiers or ARNs\.

For example, the following example returns the pending maintenance actions for the `sample-cluster1` and `sample-cluster2` DB clusters\.

**Example**  
For Linux, OS X, or Unix:  

```
1. aws rds describe-pending-maintenance-actions \
2. 	--filters Name=db-cluster-id,Values=sample-cluster1,sample-cluster2
```
For Windows:  

```
1. aws rds describe-pending-maintenance-actions ^
2. 	--filters Name=db-cluster-id,Values=sample-cluster1,sample-cluster2
```

### API<a name="USER_UpgradeDBInstance.OSUpgrades.API"></a>

To apply an update to a DB instance or DB cluster, call the Amazon RDS API [http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ApplyPendingMaintenanceAction.html](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ApplyPendingMaintenanceAction.html) action\.

To return a list of resources that have at least one pending update, call the Amazon RDS API [http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribePendingMaintenanceActions.html](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribePendingMaintenanceActions.html) action\.

## Maintenance for Multi\-AZ Deployments<a name="USER_UpgradeDBInstance.Maintenance.Multi-AZ"></a>

Running a DB instance as a Multi\-AZ deployment can further reduce the impact of a maintenance event, because Amazon RDS will apply operating system updates by following these steps: 

1. Perform maintenance on the standby\.

1. Promote the standby to primary\.

1. Perform maintenance on the old primary, which becomes the new standby\.

When you modify the database engine for your DB instance in a Multi\-AZ deployment, then Amazon RDS upgrades both the primary and secondary DB instances at the same time\. In this case, the database engine for the entire Multi\-AZ deployment is shut down during the upgrade\. 

For more information on Multi\-AZ deployments, see [High Availability \(Multi\-AZ\)](Concepts.MultiAZ.md)\.

An Amazon Aurora DB cluster spans multiple Availability Zones \(AZs\) by default and maintenance is performed on all instances in an Aurora DB cluster during the cluster maintenance window\.

## The Amazon RDS Maintenance Window<a name="Concepts.DBMaintenance"></a>

Every DB instance and DB cluster has a weekly maintenance window during which any system changes are applied\. You can think of the maintenance window as an opportunity to control when modifications and software patching occur, in the event either are requested or required\. If a maintenance event is scheduled for a given week, it is initiated during the 30\-minute maintenance window you identify\. Most maintenance events also complete during the 30\-minute maintenance window, although larger maintenance events may take more than 30 minutes to complete\. 

The 30\-minute maintenance window is selected at random from an 8\-hour block of time per region\. If you don't specify a preferred maintenance window when you create the DB instance or DB cluster, then Amazon RDS assigns a 30\-minute maintenance window on a randomly selected day of the week\. 

RDS will consume some of the resources on your DB instance or DB cluster while maintenance is being applied\. You might observe a minimal effect on performance\. For a DB instance, on rare occasions, a Multi\-AZ failover might be required for a maintenance update to complete\. 

Following, you can find the time blocks for each region from which default maintenance windows are assigned\. 


****  
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_UpgradeDBInstance.Maintenance.html)

## Adjusting the Preferred DB Instance Maintenance Window<a name="AdjustingTheMaintenanceWindow"></a>

The maintenance window should fall at the time of lowest usage and thus might need modification from time to time\. Your DB instance will only be unavailable during this time if the system changes, such as a scale storage operation or a change in DB instance class, are being applied and require an outage, and only for the minimum amount of time required to make the necessary changes\. 

**Note**  
For upgrades to the database engine, Amazon Aurora manages the preferred maintenance window for a DB cluster and not individual instances\. For information on adjusting the maintenance window for Aurora, see [Adjusting the Preferred DB Cluster Maintenance Window](#AdjustingTheMaintenanceWindow.Aurora)\.

In the following example, you adjust the preferred maintenance window for a DB instance\. 

For the purpose of this example, we assume that the DB instance named *mydbinstance* exists and has a preferred maintenance window of "Sun:05:00\-Sun:06:00" UTC\. 

### AWS Management Console<a name="AdjustingTheMaintenanceWindow.CON"></a>

**To adjust the preferred maintenance window**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Instances**, and then select the DB instance that you want to modify\. 

1. Choose **Instance actions**, and then choose **Modify**\. The **Modify DB Instance** page appears\.

1. In the **Maintenance** section, update the maintenance window\.
**Note**  
The maintenance window and the backup window for the DB instance cannot overlap\. If you enter a value for the maintenance window that overlaps the backup window, an error message appears\. 

1. Choose **Continue**\.

   On the confirmation page, review your changes\.

1. To apply the changes to the maintenance window immediately, select **Apply immediately**\. 

1.  Choose **Modify DB Instance** to save your changes\. 

   Alternatively, choose **Back** to edit your changes, or choose **Cancel** to cancel your changes\. 

### CLI<a name="AdjustingTheMaintenanceWindow.CLI"></a>

To adjust the preferred maintenance window, use the AWS CLI [http://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html](http://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) command with the following parameters:
+ `--db-instance-identifier`
+ `--preferred-maintenance-window`

**Example**  
The following code example sets the maintenance window to Tuesdays from 4:00\-4:30AM UTC\.  
For Linux, OS X, or Unix:  

```
aws rds modify-db-instance \
--db-instance-identifier mydbinstance \
--preferred-maintenance-window Tue:04:00-Tue:04:30
```
For Windows:  

```
aws rds modify-db-instance ^
--db-instance-identifier mydbinstance ^
--preferred-maintenance-window Tue:04:00-Tue:04:30
```

### API<a name="AdjustingTheMaintenanceWindow.API"></a>

To adjust the preferred maintenance window, use the Amazon RDS API [http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html) action with the following parameters:
+ `DBInstanceIdentifier = mydbinstance`
+ `PreferredMaintenanceWindow = Tue:04:00-Tue:04:30`

**Example**  
The following code example sets the maintenance window to Tuesdays from 4:00\-4:30AM UTC\.  

```
 1. https://rds.us-west-2.amazonaws.com/
 2. ?Action=ModifyDBInstance
 3. &DBInstanceIdentifier=mydbinstance
 4. &PreferredMaintenanceWindow=Tue:04:00-Tue:04:30
 5. &SignatureMethod=HmacSHA256
 6. &SignatureVersion=4
 7. &Version=2014-09-01
 8. &X-Amz-Algorithm=AWS4-HMAC-SHA256
 9. &X-Amz-Credential=AKIADQKE4SARGYLE/20140425/us-east-1/rds/aws4_request
10. &X-Amz-Date=20140425T192732Z
11. &X-Amz-SignedHeaders=content-type;host;user-agent;x-amz-content-sha256;x-amz-date
12. &X-Amz-Signature=1dc9dd716f4855e9bdf188c70f1cf9f6251b070b68b81103b59ec70c3e7854b3
```

## Adjusting the Preferred DB Cluster Maintenance Window<a name="AdjustingTheMaintenanceWindow.Aurora"></a>

The Aurora DB cluster maintenance window should fall at the time of lowest usage and thus might need modification from time to time\. Your DB cluster is unavailable during this time only if the updates that are being applied require an outage\. The outage is for the minimum amount of time required to make the necessary updates\. 

### AWS Management Console<a name="AdjustingTheMaintenanceWindow.Aurora.CON"></a>

**To adjust the preferred DB cluster maintenance window**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. Choose **Clusters** on the left of the console\.

1. Choose the DB cluster for which you want to change the maintenance window\.

1. From **Actions**, choose **Modify cluster**\.

1. In the **Maintenance** section, update the maintenance window\.

1. Choose **Continue**\.

   On the confirmation page, review your changes\.

1. To apply the changes to the maintenance window immediately, select **Apply immediately**\. 

1.  Choose **Modify cluster** to save your changes\. 

   Alternatively, choose **Back** to edit your changes, or choose **Cancel** to cancel your changes\. 

### CLI<a name="AdjustingTheMaintenanceWindow.Aurora.CLI"></a>

To adjust the preferred DB cluster maintenance window, use the AWS CLI [http://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html](http://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html) command with the following parameters:
+ `--db-cluster-identifier`
+ `--preferred-maintenance-window`

**Example**  
The following code example sets the maintenance window to Tuesdays from 4:00\-4:30AM UTC\.  
For Linux, OS X, or Unix:  

```
aws rds modify-db-cluster \
--db-cluster-identifier my-cluster \
--preferred-maintenance-window Tue:04:00-Tue:04:30
```
For Windows:  

```
aws rds modify-db-cluster ^
--db-cluster-identifier my-cluster ^
--preferred-maintenance-window Tue:04:00-Tue:04:30
```

### API<a name="AdjustingTheMaintenanceWindow.Aurora.API"></a>

To adjust the preferred DB cluster maintenance window, use the Amazon RDS API [http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html) action with the following parameters:
+ `DBClusterIdentifier = my-cluster`
+ `PreferredMaintenanceWindow = Tue:04:00-Tue:04:30`

**Example**  
The following code example sets the maintenance window to Tuesdays from 4:00\-4:30AM UTC\.  

```
 1. https://rds.us-west-2.amazonaws.com/
 2. ?Action=ModifyDBCluster
 3. &DBClusterIdentifier=my-cluster
 4. &PreferredMaintenanceWindow=Tue:04:00-Tue:04:30
 5. &SignatureMethod=HmacSHA256
 6. &SignatureVersion=4
 7. &Version=2014-10-31
 8. &X-Amz-Algorithm=AWS4-HMAC-SHA256
 9. &X-Amz-Credential=AKIADQKE4SARGYLE/20140725/us-east-1/rds/aws4_request
10. &X-Amz-Date=20161017T161457Z
11. &X-Amz-SignedHeaders=content-type;host;user-agent;x-amz-content-sha256;x-amz-date
12. &X-Amz-Signature=d6d1c65c2e94f5800ab411a3f7336625820b103713b6c063430900514e21d784
```

## Related Topics<a name="USER_UpgradeDBInstance.Maintenance.Related"></a>
+ [Upgrading a DB Instance Engine Version](USER_UpgradeDBInstance.Upgrading.md)