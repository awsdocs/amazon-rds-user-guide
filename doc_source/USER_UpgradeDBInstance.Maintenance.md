# Maintaining a DB Instance<a name="USER_UpgradeDBInstance.Maintenance"></a>

Periodically, Amazon RDS performs maintenance on Amazon RDS resources\. Maintenance most often involves updates to the DB instance's underlying hardware, underlying operating system \(OS\), or database engine version\. Updates to the operating system most often occur for security issues and should be done as soon as possible\. 

Some maintenance items require that Amazon RDS take your DB instance offline for a short time\. Maintenance items that require a resource to be offline include required operating system or database patching\. Required patching is automatically scheduled only for patches that are related to security and instance reliability\. Such patching occurs infrequently \(typically once every few months\) and seldom requires more than a fraction of your maintenance window\. 

Deferred DB instance modifications that you have chosen not to apply immediately are applied during the maintenance window\. For example, you may choose to change the DB instance class or parameter group during the maintenance window\. For information about modifying a DB instance, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md)\.

You can view whether a maintenance update is available for your DB instance by using the RDS console, the AWS CLI, or the Amazon RDS API\. If an update is available, it is indicated in the **Maintenance** column for the DB instance on the Amazon RDS console, as shown following\. 

![\[Offline patch available\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/offlinepatchavailable.png)

If no maintenance update is available for a DB instance, the column value is **none** for it\.

If a maintenance update is available for a DB instance, the following column values are possible:
+ **required** – The maintenance action will be applied to the resource and can't be deferred indefinitely\.
+ **available** – The maintenance action is available, but it will not be applied to the resource automatically\. You can apply it manually\.
+ **next window** – The maintenance action will be applied to the resource during the next maintenance window\.
+ **In progress** – The maintenance action is in the process of being applied to the resource\.

If an update is available, you can take one of the actions: 
+ If the maintenance value is **next window**, defer the maintenance items by choosing **Defer upgrade** from **Actions**\. You can't defer a maintenance action if it has already started\.
+ Apply the maintenance items immediately\.
+ Schedule the maintenance items to start during your next maintenance window\.
+ Take no action\.

**Note**  
Certain OS updates are marked as **required**\. If you defer a required update, you get a notice from Amazon RDS indicating when the update will be performed\. Other updates are marked as **available**, and these you can defer indefinitely\.

To take an action, choose the DB instance to show its details, then choose **Maintenance & backups**\. The pending maintenance items appear\.

![\[Pending maintenance items\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/offlinepatchavailabledetails.png)

The maintenance window determines when pending operations start, but doesn't limit the total run time of these operations\. Maintenance operations aren't guaranteed to finish before the maintenance window ends, and can continue beyond the specified end time\. For more information, see [The Amazon RDS Maintenance Window](#Concepts.DBMaintenance)\. 

## Applying Updates for a DB Instance<a name="USER_UpgradeDBInstance.OSUpgrades"></a>

With Amazon RDS, you can choose when to apply maintenance operations\. You can decide when Amazon RDS applies updates by using the RDS console, AWS Command Line Interface \(AWS CLI\), or RDS API\. 

### Console<a name="USER_UpgradeDBInstance.OSUpgrades.Console"></a>

**To manage an update for a DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose the DB instance that has a required update\. 

1. For **Actions**, choose one of the following:
   + **Upgrade now**
   + **Upgrade at next window**
**Note**  
If you choose **Upgrade at next window** and later want to delay the update, you can choose **Defer upgrade**\. You can't defer a maintenance action if it has already started\.  
To cancel a maintenance action, modify the DB instance and disable **Auto minor version upgrade**\.

### AWS CLI<a name="USER_UpgradeDBInstance.OSUpgrades.CLI"></a>

To apply a pending update to a DB instance, use the [apply\-pending\-maintenance\-action](https://docs.aws.amazon.com/cli/latest/reference/rds/apply-pending-maintenance-action.html) AWS CLI command\.

**Example**  
For Linux, macOS, or Unix:  

```
aws rds apply-pending-maintenance-action \
    --resource-identifier arn:aws:rds:us-west-2:001234567890:db:mysql-db \
    --apply-action system-update \
    --opt-in-type immediate
```
For Windows:  

```
aws rds apply-pending-maintenance-action ^
    --resource-identifier arn:aws:rds:us-west-2:001234567890:db:mysql-db ^
    --apply-action system-update ^
    --opt-in-type immediate
```

**Note**  
To defer a maintenance action, specify `undo-opt-in` for `--opt-in-type`\. You can't specify `undo-opt-in` for `--opt-in-type` if the maintenance action has already started\.  
To cancel a maintenance action, run the [modify\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) AWS CLI command and specify `--no-auto-minor-version-upgrade`\.

To return a list of resources that have at least one pending update, use the [describe\-pending\-maintenance\-actions](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-pending-maintenance-actions.html) AWS CLI command\.

**Example**  
For Linux, macOS, or Unix:  

```
aws rds describe-pending-maintenance-actions \
    --resource-identifier arn:aws:rds:us-west-2:001234567890:db:mysql-db
```
For Windows:  

```
aws rds describe-pending-maintenance-actions ^
    --resource-identifier arn:aws:rds:us-west-2:001234567890:db:mysql-db
```

You can also return a list of resources for a DB instance by specifying the `--filters` parameter of the `describe-pending-maintenance-actions` AWS CLI command\. The format for the `--filters` command is `Name=filter-name,Value=resource-id,...`\.

The following are the accepted values for the `Name` parameter of a filter:
+ `db-instance-id` – Accepts a list of DB instance identifiers or Amazon Resource Names \(ARNs\)\. The returned list only includes pending maintenance actions for the DB instances identified by these identifiers or ARNs\.
+ `db-cluster-id` – Accepts a list of DB cluster identifiers or ARNs for Amazon Aurora\. The returned list only includes pending maintenance actions for the DB clusters identified by these identifiers or ARNs\.

For example, the following example returns the pending maintenance actions for the `sample-instance1` and `sample-instance2` DB instances\.

**Example**  
For Linux, macOS, or Unix:  

```
aws rds describe-pending-maintenance-actions \
	--filters Name=db-instance-id,Values=sample-instance1,sample-instance2
```
For Windows:  

```
aws rds describe-pending-maintenance-actions ^
	--filters Name=db-instance-id,Values=sample-instance1,sample-instance2
```

### RDS API<a name="USER_UpgradeDBInstance.OSUpgrades.API"></a>

To apply an update to a DB instance, call the Amazon RDS API [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ApplyPendingMaintenanceAction.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ApplyPendingMaintenanceAction.html) operation\.

To return a list of resources that have at least one pending update, call the Amazon RDS API [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribePendingMaintenanceActions.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribePendingMaintenanceActions.html) operation\.

## Maintenance for Multi\-AZ Deployments<a name="USER_UpgradeDBInstance.Maintenance.Multi-AZ"></a>

Running a DB instance as a Multi\-AZ deployment can further reduce the impact of a maintenance event, because Amazon RDS applies operating system updates by following these steps: 

1. Perform maintenance on the standby\.

1. Promote the standby to primary\.

1. Perform maintenance on the old primary, which becomes the new standby\.

When you modify the database engine for your DB instance in a Multi\-AZ deployment, then Amazon RDS upgrades both the primary and secondary DB instances at the same time\. In this case, the database engine for the entire Multi\-AZ deployment is shut down during the upgrade\. 

For more information on Multi\-AZ deployments, see [High Availability \(Multi\-AZ\) for Amazon RDS](Concepts.MultiAZ.md)\.

## The Amazon RDS Maintenance Window<a name="Concepts.DBMaintenance"></a>

Every DB instance has a weekly maintenance window during which any system changes are applied\. You can think of the maintenance window as an opportunity to control when modifications and software patching occur, in the event either are requested or required\. If a maintenance event is scheduled for a given week, it is initiated during the 30\-minute maintenance window you identify\. Most maintenance events also complete during the 30\-minute maintenance window, although larger maintenance events may take more than 30 minutes to complete\. 

The 30\-minute maintenance window is selected at random from an 8\-hour block of time per region\. If you don't specify a preferred maintenance window when you create the DB instance, then Amazon RDS assigns a 30\-minute maintenance window on a randomly selected day of the week\. 

RDS will consume some of the resources on your DB instance while maintenance is being applied\. You might observe a minimal effect on performance\. For a DB instance, on rare occasions, a Multi\-AZ failover might be required for a maintenance update to complete\. 

Following, you can find the time blocks for each region from which default maintenance windows are assigned\. 


****  
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_UpgradeDBInstance.Maintenance.html)

## Adjusting the Preferred DB Instance Maintenance Window<a name="AdjustingTheMaintenanceWindow"></a>

The maintenance window should fall at the time of lowest usage and thus might need modification from time to time\. Your DB instance will only be unavailable during this time if the system changes, such as a change in DB instance class, are being applied and require an outage, and only for the minimum amount of time required to make the necessary changes\. 

In the following example, you adjust the preferred maintenance window for a DB instance\. 

For the purpose of this example, we assume that the DB instance named *mydbinstance* exists and has a preferred maintenance window of "Sun:05:00\-Sun:06:00" UTC\. 

### Console<a name="AdjustingTheMaintenanceWindow.CON"></a>

**To adjust the preferred maintenance window**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**, and then select the DB instance that you want to modify\. 

1. Choose **Modify**\. The **Modify DB Instance** page appears\.

1. In the **Maintenance** section, update the maintenance window\.
**Note**  
The maintenance window and the backup window for the DB instance cannot overlap\. If you enter a value for the maintenance window that overlaps the backup window, an error message appears\. 

1. Choose **Continue**\.

   On the confirmation page, review your changes\.

1. To apply the changes to the maintenance window immediately, select **Apply immediately**\. 

1.  Choose **Modify DB Instance** to save your changes\. 

   Alternatively, choose **Back** to edit your changes, or choose **Cancel** to cancel your changes\. 

### AWS CLI<a name="AdjustingTheMaintenanceWindow.CLI"></a>

To adjust the preferred maintenance window, use the AWS CLI [https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) command with the following parameters:
+ `--db-instance-identifier`
+ `--preferred-maintenance-window`

**Example**  
The following code example sets the maintenance window to Tuesdays from 4:00\-4:30AM UTC\.  
For Linux, macOS, or Unix:  

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

### RDS API<a name="AdjustingTheMaintenanceWindow.API"></a>

To adjust the preferred maintenance window, use the Amazon RDS API [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html) operation with the following parameters:
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