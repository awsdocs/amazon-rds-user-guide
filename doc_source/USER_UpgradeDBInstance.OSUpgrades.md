# Updating the Operating System for a DB Instance or DB Cluster<a name="USER_UpgradeDBInstance.OSUpgrades"></a>

With Amazon RDS, you can choose when to update the underlying operating system\. You can decide when Amazon RDS applies OS updates by using the RDS console, AWS Command Line Interface \(AWS CLI\), or RDS API\. 

Use the procedures in this topic to immediately upgrade or schedule an upgrade for your DB instance\. For more information, see [Amazon RDS Maintenance](USER_UpgradeDBInstance.Maintenance.md)\. 

## AWS Management Console<a name="USER_UpgradeDBInstance.OSUpgrades.Console"></a>

**To manage an OS update for a DB instance or DB cluster**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Instances** to manage updates for a DB instance, or **Clusters** to manage updates for an Aurora DB cluster\. 

1. Select the check box for the DB instance or DB cluster that has a required operating system update\. 

1. Choose **Instance Actions** for a DB instance, or **Cluster Actions** for a DB cluster, and then choose one of the following:

   + **Upgrade Now**

   + **Upgrade at Next Window**
**Note**  
If you choose **Upgrade at Next Window** and later want to delay the OS update, you can select **Defer Upgrade**\.

## CLI<a name="USER_UpgradeDBInstance.OSUpgrades.CLI"></a>

To apply a pending OS update to a DB instance or DB cluster, use the [apply\-pending\-maintenance\-action](http://docs.aws.amazon.com/cli/latest/reference/rds/apply-pending-maintenance-action.html) AWS CLI command\.

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

To return a list of resources that have at least one pending OS update, use the [describe\-pending\-maintenance\-actions](http://docs.aws.amazon.com/cli/latest/reference/rds/describe-pending-maintenance-actions.html) AWS CLI command\.

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

## API<a name="USER_UpgradeDBInstance.OSUpgrades.API"></a>

To apply an OS update to a DB instance or DB cluster, call the Amazon RDS API [http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ApplyPendingMaintenanceAction.html](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ApplyPendingMaintenanceAction.html) action\.

**Example**  

```
 1. https://rds.us-west-2.amazonaws.com/
 2.    ?Action=ApplyPendingMaintenanceAction
 3.    &ResourceIdentifier=arn:aws:rds:us-east-1:123456781234:db:my-instance
 4.    &ApplyAction=system-update
 5.    &OptInType=immediate
 6.    &SignatureMethod=HmacSHA256
 7.    &SignatureVersion=4
 8.    &Version=2014-10-31
 9.    &X-Amz-Algorithm=AWS4-HMAC-SHA256
10.    &X-Amz-Credential=AKIADQKE4SARGYLE/20141216/us-west-2/rds/aws4_request
11.    &X-Amz-Date=20140421T194732Z
12.    &X-Amz-SignedHeaders=content-type;host;user-agent;x-amz-content-sha256;x-amz-date
13.    &X-Amz-Signature=6e25c542bf96fe24b28c12976ec92d2f856ab1d2a158e21c35441a736e4fde2b
```

To return a list of resources that have at least one pending OS update, call the Amazon RDS API [DescribePendingMaintenanceActions](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribePendingMaintenanceActions.html) action\.

**Example**  

```
 1. https://rds.us-west-2.amazonaws.com/
 2.    ?Action=DescribePendingMaintenanceActions
 3.    &SignatureMethod=HmacSHA256
 4.    &SignatureVersion=4
 5.    &Version=2014-10-31
 6.    &X-Amz-Algorithm=AWS4-HMAC-SHA256
 7.    &X-Amz-Credential=AKIADQKE4SARGYLE/20141216/us-west-2/rds/aws4_request
 8.    &X-Amz-Date=20140421T194732Z
 9.    &X-Amz-SignedHeaders=content-type;host;user-agent;x-amz-content-sha256;x-amz-date
10.    &X-Amz-Signature=6e25c542bf96fe24b28c12976ec92d2f856ab1d2a158e21c35441a736e4fde2b
```

## Related Topics<a name="USER_UpgradeDBInstance.OSUpgrades.Related"></a>

+ [Amazon RDS Maintenance](USER_UpgradeDBInstance.Maintenance.md)

+ [Upgrading a DB Instance Engine Version](USER_UpgradeDBInstance.Upgrading.md)