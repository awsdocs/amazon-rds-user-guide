# Maintaining a DB instance<a name="USER_UpgradeDBInstance.Maintenance"></a>

Periodically, Amazon RDS performs maintenance on Amazon RDS resources\. Maintenance most often involves updates to the DB instance's underlying hardware, underlying operating system \(OS\), or database engine version\. Updates to the operating system most often occur for security issues and should be done as soon as possible\. 

Some maintenance items require that Amazon RDS take your DB instance offline for a short time\. Maintenance items that require a resource to be offline include required operating system or database patching\. Required patching is automatically scheduled only for patches that are related to security and instance reliability\. Such patching occurs infrequently \(typically once every few months\) and seldom requires more than a fraction of your maintenance window\. 

Deferred DB instance modifications that you have chosen not to apply immediately are also applied during the maintenance window\. For example, you might choose to change the DB instance class or parameter group during the maintenance window\. Such modifications that you specify using the **pending reboot** setting don't show up in the **Pending maintenance** list\. For information about modifying a DB instance, see [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)\.

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

The maintenance window determines when pending operations start, but doesn't limit the total run time of these operations\. Maintenance operations aren't guaranteed to finish before the maintenance window ends, and can continue beyond the specified end time\. For more information, see [The Amazon RDS maintenance window](#Concepts.DBMaintenance)\. 

## Applying updates for a DB instance<a name="USER_UpgradeDBInstance.OSUpgrades"></a>

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

## Maintenance for Multi\-AZ deployments<a name="USER_UpgradeDBInstance.Maintenance.Multi-AZ"></a>

Running a DB instance as a Multi\-AZ deployment can further reduce the impact of a maintenance event, because Amazon RDS applies operating system updates by following these steps: 

1. Perform maintenance on the standby\.

1. Promote the standby to primary\.

1. Perform maintenance on the old primary, which becomes the new standby\.

When you modify the database engine for your DB instance in a Multi\-AZ deployment, then Amazon RDS upgrades both the primary and secondary DB instances at the same time\. In this case, the database engine for the entire Multi\-AZ deployment is shut down during the upgrade\. 

For more information on Multi\-AZ deployments, see [Multi\-AZ deployments for high availability](Concepts.MultiAZ.md)\.

## The Amazon RDS maintenance window<a name="Concepts.DBMaintenance"></a>

Every DB instance has a weekly maintenance window during which any system changes are applied\. You can think of the maintenance window as an opportunity to control when modifications and software patching occur, in the event either are requested or required\. If a maintenance event is scheduled for a given week, it is initiated during the 30\-minute maintenance window you identify\. Most maintenance events also complete during the 30\-minute maintenance window, although larger maintenance events may take more than 30 minutes to complete\. 

The 30\-minute maintenance window is selected at random from an 8\-hour block of time per region\. If you don't specify a preferred maintenance window when you create the DB instance, then Amazon RDS assigns a 30\-minute maintenance window on a randomly selected day of the week\. 

RDS will consume some of the resources on your DB instance while maintenance is being applied\. You might observe a minimal effect on performance\. For a DB instance, on rare occasions, a Multi\-AZ failover might be required for a maintenance update to complete\. 

Following, you can find the time blocks for each region from which default maintenance windows are assigned\. 


****  
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_UpgradeDBInstance.Maintenance.html)

## Adjusting the preferred DB instance maintenance window<a name="AdjustingTheMaintenanceWindow"></a>

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

To adjust the preferred maintenance window, use the Amazon RDS API [ `ModifyDBInstance`](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html) operation with the following parameters:
+ `DBInstanceIdentifier`
+ `PreferredMaintenanceWindow`

## Working with mandatory operating system updates<a name="Mandatory_OS_Updates"></a>

RDS for MySQL and RDS for PostgreSQL DB instances occasionally require mandatory operating system updates\. Amazon RDS upgrades the operating system to a newer version to improve database performance and customers’ overall security posture\. Typically, the updates take about 10 minutes\. The updates don't affect how the DB instances function\.

Whether a mandatory operating system update is required for a DB instance depends on its DB engine version and DB instance class\. In the following sections, you can find descriptions of the affected DB engine versions and DB instance classes for RDS for MySQL and RDS for PostgreSQL\.

**Topics**
+ [Mandatory operating system updates for RDS for MySQL](#Aurora.Maintenance.Mandatory_OS_Updates.MySQL)
+ [Mandatory operating system updates for RDS for PostgreSQL](#Aurora.Maintenance.Mandatory_OS_Updates.PostgreSQL)
+ [Recommended actions for mandatory operating system updates](#Aurora.Maintenance.Mandatory_OS_Updates.Recommended_Actions)

### Mandatory operating system updates for RDS for MySQL<a name="Aurora.Maintenance.Mandatory_OS_Updates.MySQL"></a>

We plan to use the following schedule for operating system updates for RDS for MySQL\. For each date in the table, the start time is 00:00 Universal Coordinated Time \(UTC\)\.


| Action or recommendation | Date range | 
| --- | --- | 
|  We recommend that you update the operating system for your RDS for MySQL DB instances\. To update the operating system, follow the instructions in [Applying updates for a DB instance](#USER_UpgradeDBInstance.OSUpgrades)\.  |  Now–January 31, 2022  | 
|  Amazon RDS starts automatic upgrades of the operating system for your RDS for MySQL DB instances to the latest version in a maintenance window\.  |  January 31, 2022–March 30, 2022  | 
|  Amazon RDS starts automatic upgrades of the operating system for your RDS for MySQL DB instances to the latest version regardless of whether they are in a maintenance window\.  |  After March 30, 2022  | 

For this operating system update to be required, a DB instance must be running the versions with "Yes" in the following table for each DB instance class\.


**Mandatory operating system updates by version and DB instance class for RDS for MySQL**  

| RDS for MySQL version | db\.t2 | db\.r4 | db\.m4 | db\.t3 | db\.r5 | db\.m5 | 
| --- | --- | --- | --- | --- | --- | --- | 
|  8\.0\.23 and higher 8\.0 versions  |  Yes  |  Yes  |  Yes  |  Yes  |  Yes  |  Yes  | 
|  8\.0\.21 and lower 8\.0 versions  |  No  |  No  |  No  |  No  |  No  |  No  | 
|  5\.7\.33 and higher 5\.7 versions  |  Yes  |  Yes  |  Yes  |  Yes  |  Yes  |  Yes  | 
|  5\.7\.31 and lower 5\.7 versions  |  No  |  No  |  No  |  No  |  No  |  No  | 
|  5\.6\.51 and higher 5\.6 versions  |  Yes  |  Yes  |  Yes  |  Yes  |  Yes  |  Yes  | 
|  5\.6\.49 and lower 5\.6 versions  |  No  |  No  |  No  |  No  |  No  |  No  | 

### Mandatory operating system updates for RDS for PostgreSQL<a name="Aurora.Maintenance.Mandatory_OS_Updates.PostgreSQL"></a>

We plan to use the following schedule for operating system updates for RDS for PostgreSQL\. For each date in the table, the start time is 00:00 Universal Coordinated Time \(UTC\)\.


| Action or recommendation | Date range | 
| --- | --- | 
|  We recommend that you update the operating system for your RDS for PostgreSQL DB instances\. To update the operating system, follow the instructions in [Applying updates for a DB instance](#USER_UpgradeDBInstance.OSUpgrades)\.  |  Now–January 10, 2022  | 
|  Amazon RDS starts automatic upgrades of the operating system for your RDS for PostgreSQL DB instances to the latest version in a maintenance window\.  |  January 10, 2022–March 30, 2022  | 
|  Amazon RDS starts automatic upgrades of the operating system for your RDS for PostgreSQL DB instances to the latest version regardless of whether they are in a maintenance window\.  |  After March 30, 2022  | 

For this operating system update to be required, a DB instance must be running the versions with "Yes" in the following table for each DB instance class\.


**Mandatory operating system updates by version and DB instance class for RDS for PostgreSQL**  

| RDS for PostgreSQL version | db\.t2 | db\.r4 | db\.m4 | db\.t3 | db\.r5 | db\.m5 | 
| --- | --- | --- | --- | --- | --- | --- | 
|  All 13 versions  |  No  |  No  |  No  |  Yes  |  Yes  |  Yes  | 
|  12\.7 and higher 12 versions  |  Yes  |  Yes  |  Yes  |  Yes  |  Yes  |  Yes  | 
|  12\.6  |  No  |  No  |  No  |  Yes  |  Yes  |  Yes  | 
|  12\.5 and lower 12 versions  |  No  |  No  |  No  |  No  |  No  |  No  | 
|  11\.12 and higher 11 versions  |  Yes  |  Yes  |  Yes  |  Yes  |  Yes  |  Yes  | 
|  11\.11  |  No  |  No  |  No  |  Yes  |  Yes  |  Yes  | 
|  11\.10 and lower 11 versions  |  No  |  No  |  No  |  No  |  No  |  No  | 
|  10\.17 and higher 10 versions  |  Yes  |  Yes  |  Yes  |  Yes  |  Yes  |  Yes  | 
|  10\.16  |  No  |  No  |  No  |  Yes  |  Yes  |  Yes  | 
|  10\.15 and lower 10 versions  |  No  |  No  |  No  |  No  |  No  |  No  | 
|  9\.6\.22 and higher 9\.6 versions  |  Yes  |  Yes  |  Yes  |  Yes  |  Yes  |  Yes  | 
|  9\.6\.21  |  No  |  No  |  No  |  Yes  |  Yes  |  Yes  | 
|  9\.6\.20 and lower 9\.6 versions  |  No  |  No  |  No  |  No  |  No  |  No  | 

### Recommended actions for mandatory operating system updates<a name="Aurora.Maintenance.Mandatory_OS_Updates.Recommended_Actions"></a>

Whether a mandatory operating system update is required for a DB instance depends on its DB engine version and DB instance class\. You can find the affected DB engine versions and DB instance classes in [Mandatory operating system updates by DB engine version and DB instance class for RDS for MySQL](#mandatory-os-updates-version-db-instance-class-mysql) and [Mandatory operating system updates by DB engine version and DB instance class for RDS for PostgreSQL](#mandatory-os-updates-version-db-instance-class-postgresql)\.

If an operating system update is required for your DB instance based on its DB engine version and DB instance class, the required update appears in the AWS Management Console\. In this case, update the operating system by following the instructions in [Applying updates for a DB instance](#USER_UpgradeDBInstance.OSUpgrades)\.

Although we recommend the actions described in this section, Amazon RDS won't upgrade or modify the DB instance class of your DB instances automatically\. Amazon RDS won't upgrade the DB engine version of a DB instance unless the **auto minor version upgrade** option is turned on\. For more information about the **auto minor version upgrade** option, see [Automatically upgrading the minor engine version](USER_UpgradeDBInstance.Upgrading.md#USER_UpgradeDBInstance.Upgrading.AutoMinorVersionUpgrades)\.

If you are unable to identify any mandatory operating system updates in the AWS Management Console, we recommend the following actions:
+ Your DB instance doesn't use a DB engine version that requires this operating system update\. Your DB instance uses an affected DB instance class\.

  Take the following recommended actions:

  1. Upgrade your DB engine version to a version that requires this operating system update\. For more information, see [Upgrading a DB instance engine version](USER_UpgradeDBInstance.Upgrading.md)\.

  1. Update the operating system by following the instructions in [Applying updates for a DB instance](#USER_UpgradeDBInstance.OSUpgrades)\.
+ Your DB instance uses a DB engine version that requires this operating system update\. Your DB instance uses an older DB instance class in a DB instance class family that is affected by this update\.

  We recommend that you modify your DB instance to use a DB instance class that requires this operating system update\. For example, if your DB instance uses a db\.r3 DB instance class, we recommend that you modify your DB instance to use a db\.r4 or db\.r5 DB instance class\. Similarly, if your DB instance uses a db\.m3 DB instance class, we recommend that you modify your DB instance to use a db\.m4 or db\.m5 DB instance class\. In this case, you don't need to update the operating system because the DB instance class modification applies the operating system update\. You can also modify your DB instance to use a different DB instance class family, such as db\.m6g or db\.r6g\.

  If your DB instance already uses a DB instance class that isn't in a DB instance class family that is affected by this operating system update, then you don't need to modify its DB instance class\. For example, if your DB instance uses a db\.m6g, db\.x2g, or db\.z1d DB instance class, then you don't need to modify its DB instance class\.

  For more information about DB instance classes, see [DB instance classes](Concepts.DBInstanceClass.md)\. For more information about modifying a DB instance, see [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)\.
+ Your DB instance doesn't use a DB engine version that requires this operating system update\. Your DB instance uses an older DB instance class in a DB instance class family that is affected by this operating system update\.

  Take the following recommended actions:

  1. Upgrade your DB engine version to a version that requires this operating system update\. For more information, see [Upgrading a DB instance engine version](USER_UpgradeDBInstance.Upgrading.md)\.

  1. Modify your DB instance to use a newer DB instance class to use newer DB instance in the same DB instance class family in a different DB instance class family\.

     For example, if your DB instance uses a db\.r3 DB instance class, we recommend that you modify your DB instance to use a db\.r4 or db\.r5 DB instance class\. Similarly, if your DB instance uses a db\.m3 DB instance class, we recommend that you modify your DB instance to use a db\.m4 or db\.m5 DB instance class\. In this case, you don't need to update the operating system because the DB instance class modification applies the operating system update\. You can also modify your DB instance to use a different DB instance class family, such as db\.m6g or db\.r6g\.

     For more information about DB instance classes, see [DB instance classes](Concepts.DBInstanceClass.md)\. For more information about modifying a DB instance, see [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)\.