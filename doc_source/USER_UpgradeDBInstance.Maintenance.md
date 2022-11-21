# Maintaining a DB instance<a name="USER_UpgradeDBInstance.Maintenance"></a>

Periodically, Amazon RDS performs maintenance on Amazon RDS resources\. Maintenance most often involves updates to the DB instance's underlying hardware, underlying operating system \(OS\), or database engine version\. Updates to the operating system most often occur for security issues and should be done as soon as possible\. 

Some maintenance items require that Amazon RDS take your DB instance offline for a short time\. Maintenance items that require a resource to be offline include required operating system or database patching\. Required patching is automatically scheduled only for patches that are related to security and instance reliability\. Such patching occurs infrequently \(typically once every few months\) and seldom requires more than a fraction of your maintenance window\. 

Deferred DB instance modifications that you have chosen not to apply immediately are also applied during the maintenance window\. For example, you might choose to change the DB instance class or parameter group during the maintenance window\. Such modifications that you specify using the **pending reboot** setting don't show up in the **Pending maintenance** list\. For information about modifying a DB instance, see [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)\.

## Viewing pending maintenance<a name="USER_UpgradeDBInstance.Maintenance.Viewing"></a>

View whether a maintenance update is available for your DB instance by using the RDS console, the AWS CLI, or the RDS API\. If an update is available, it is indicated in the **Maintenance** column for the DB instance on the Amazon RDS console, as shown following\.

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

To take an action, choose the DB instance to show its details, then choose **Maintenance & backups**\. The pending maintenance items appear\.

![\[Pending maintenance items\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/offlinepatchavailabledetails.png)

The maintenance window determines when pending operations start, but doesn't limit the total run time of these operations\. Maintenance operations aren't guaranteed to finish before the maintenance window ends, and can continue beyond the specified end time\. For more information, see [The Amazon RDS maintenance window](#Concepts.DBMaintenance)\. 

You can also view whether a maintenance update is available for your DB instance by running the [https://docs.aws.amazon.com/cli/latest/reference/rds/describe-pending-maintenance-actions.html](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-pending-maintenance-actions.html) AWS CLI command\.

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

Running a DB instance as a Multi\-AZ deployment can further reduce the impact of a maintenance event\. This result is because Amazon RDS applies operating system updates by following these steps: 

1. Perform maintenance on the standby\.

1. Promote the standby to primary\.

1. Perform maintenance on the old primary, which becomes the new standby\.

When you modify the database engine for your DB instance in a Multi\-AZ deployment, Amazon RDS upgrades both primary and secondary DB instances at once\. In this case, the database engine for the entire Multi\-AZ deployment is shut down during the upgrade\. 

For more information on Multi\-AZ deployments, see [Multi\-AZ deployments for high availability](Concepts.MultiAZ.md)\.

## The Amazon RDS maintenance window<a name="Concepts.DBMaintenance"></a>

Every DB instance has a weekly maintenance window during which any system changes are applied\. Think of the maintenance window as an opportunity to control when modifications and software patching occur\. If a maintenance event is scheduled for a given week, it's initiated during the 30\-minute maintenance window you identify\. Most maintenance events also complete during the 30\-minute maintenance window, although larger maintenance events may take more than 30 minutes to complete\. 

The 30\-minute maintenance window is selected at random from an 8\-hour block of time per region\. If you don't specify a maintenance window when you create the DB instance, RDS assigns a 30\-minute maintenance window on a randomly selected day of the week\. 

RDS consumes some of the resources on your DB instance while maintenance is being applied\. You might observe a minimal effect on performance\. For a DB instance, on rare occasions, a Multi\-AZ failover might be required for a maintenance update to complete\. 

Following, you can find the time blocks for each region from which default maintenance windows are assigned\. 


****  
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_UpgradeDBInstance.Maintenance.html)

## Adjusting the preferred DB instance maintenance window<a name="AdjustingTheMaintenanceWindow"></a>

The maintenance window should fall at the time of lowest usage and thus might need modification from time to time\. Your DB instance is unavailable during this time only if the system changes, such as a change in DB instance class, are being applied and require an outage\. Your DB instance is unavailable only for the minimum amount of time required to make the necessary changes\. 

In the following example, you adjust the preferred maintenance window for a DB instance\. 

For this example, we assume that a DB instance named *mydbinstance* exists and has a preferred maintenance window of "Sun:05:00\-Sun:06:00" UTC\. 

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
+ `DBInstanceIdentifier`
+ `PreferredMaintenanceWindow`

## Working with operating system updates<a name="OS_Updates"></a>

RDS for MariaDB, RDS for MySQL, and RDS for PostgreSQL DB instances occasionally require operating system updates\. Amazon RDS upgrades the operating system to a newer version to improve database performance and customers’ overall security posture\. Typically, the updates take about 10 minutes\. Operating system updates don't change the DB engine version or DB instance class of a DB instance\.

Operating system updates can be either optional or mandatory\.
+ An **optional update** doesn’t have an apply date and can be applied at any time\. While these updates are optional, we recommend that you apply them periodically to keep your RDS fleet up to date\. RDS *does not* apply these updates automatically\. To be notified when a new optional update becomes available, you can subscribe to [RDS\-EVENT\-0230](USER_Events.Messages.md#RDS-EVENT-0230) in the security patching event category\. For information about subscribing to RDS events, see [Subscribing to Amazon RDS event notification](USER_Events.Subscribing.md)\.
+ A **mandatory update** is required and has an apply date\. Plan to schedule your update before this date\. After the specified apply date, Amazon RDS automatically upgrades the operating system for your DB instance to the latest version\. The update is performed in a subsequent maintenance window for the DB instance\.

**Note**  
Staying current on all optional and mandatory updates might be required to meet various compliance obligations\. We recommend that you apply all updates made available by RDS routinely during your maintenance windows\.

You can use the AWS Management Console or the AWS CLI to determine whether an update is optional or mandatory\.

### Console<a name="OS_Updates.CheckMaintenanceStatus.CON"></a>

**To determine whether an update is optional or mandatory using the AWS Management Console**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**, and then select the DB instance\.

1. Choose **Maintenance & backups**\.

1. In the **Pending maintenance** section, find the operating system update, and check the **Status** value\.

In the AWS Management Console, an optional update has its maintenance **Status** set to **available** and doesn't have an **Apply date**, as shown in the following image\.

![\[Optional operating system update\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/os-update-optional.png)

A mandatory update has its maintenance **Status** set to **required** and has an **Apply date**, as shown in the following image\.

![\[Required operating system update\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/os-update-required.png)

### AWS CLI<a name="OS_Updates.CheckMaintenanceStatus.CLI"></a>

To determine whether an update is optional or mandatory using the AWS CLI, call the [describe\-pending\-maintenance\-actions](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-pending-maintenance-actions.html) command\.

```
aws rds describe-pending-maintenance-actions
```

A mandatory operating system update includes an `AutoAppliedAfterDate` value and a `CurrentApplyDate` value\. An optional operating system update doesn't include these values\.

The following output shows a mandatory operating system update\.

```
{
  "ResourceIdentifier": "arn:aws:rds:us-east-1:123456789012:db:mydb1",
  "PendingMaintenanceActionDetails": [
    {
      "Action": "system-update",
      "AutoAppliedAfterDate": "2022-08-31T00:00:00+00:00",
      "CurrentApplyDate": "2022-08-31T00:00:00+00:00",
      "Description": "New Operating System update is available"
    }
  ]
}
```

The following output shows an optional operating system update\.

```
{
  "ResourceIdentifier": "arn:aws:rds:us-east-1:123456789012:db:mydb2",
  "PendingMaintenanceActionDetails": [
    {
      "Action": "system-update",
      "Description": "New Operating System update is available"
    }
  ]
}
```

### Availability of operating system updates<a name="OS_Updates.Availability"></a>

Operating system updates are specific to DB engine version and DB instance class\. Therefore, DB instances receive or require updates at different times\. When an operating system update is available for your DB instance based on its engine version and instance class, the update appears in the console\. It can also be viewed by running AWS CLI [describe\-pending\-maintenance\-actions](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-pending-maintenance-actions.html) command or by calling the RDS [DescribePendingMaintenanceActions](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribePendingMaintenanceActions.html) API operation\. If an update is available for your instance, you can update your operating system by following the instructions in [Applying updates for a DB instance](#USER_UpgradeDBInstance.OSUpgrades)\.

### Mandatory operating system updates schedule<a name="Mandatory_OS_Updates.Schedule"></a>

We plan to use the following schedule for mandatory operating system updates\. For each date in the table, the start time is 00:00 Universal Coordinated Time \(UTC\)\.


| DB engine | Apply date | 
| --- | --- | 
|  RDS for MySQL  |  August 31, 2022\*  | 
|  RDS for MariaDB  |  August 31, 2022  | 
|  RDS for PostgreSQL  |  August 31, 2022  | 

\* For RDS for MySQL, the date applies to the Asia Pacific \(Jakarta\) Region only\. Mandatory operating system updates are complete for other AWS Regions\.

 After the apply date, Amazon RDS will automatically upgrade the operating system for your DB instances to the latest version in a subsequent maintenance window\. To avoid an automatic upgrade, we recommend that you schedule your update before the apply date\. 