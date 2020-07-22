# Renaming a DB Instance<a name="USER_RenameInstance"></a>

 You can rename a DB instance by using the AWS Management Console, the AWS CLI `modify-db-instance` command, or the Amazon RDS API `ModifyDBInstance` action\. Renaming a DB instance can have far\-reaching effects; the following is a list of things you should know before you rename a DB instance\. 
+  When you rename a DB instance, the endpoint for the DB instance changes, because the URL includes the name you assigned to the DB instance\. You should always redirect traffic from the old URL to the new one\.
+  When you rename a DB instance, the old DNS name that was used by the DB instance is immediately deleted, although it could remain cached for a few minutes\. The new DNS name for the renamed DB instance becomes effective in about 10 minutes\. The renamed DB instance is not available until the new name becomes effective\. 
+  You cannot use an existing DB instance name when renaming an instance\. 
+  All read replicas associated with a DB instance remain associated with that instance after it is renamed\. For example, suppose you have a DB instance that serves your production database and the instance has several associated read replicas\. If you rename the DB instance and then replace it in the production environment with a DB snapshot, the DB instance that you renamed will still have the read replicas associated with it\. 
+  Metrics and events associated with the name of a DB instance are maintained if you reuse a DB instance name\. For example, if you promote a read replica and rename it to be the name of the previous primary DB instance, the events and metrics associated with the primary DB instance are associated with the renamed instance\. 
+  DB instance tags remain with the DB instance, regardless of renaming\. 
+  DB snapshots are retained for a renamed DB instance\. 

## Renaming to Replace an Existing DB Instance<a name="USER_RenameInstance.RR"></a>

The most common reasons for renaming a DB instance are that you are promoting a read replica or you are restoring data from a DB snapshot or PITR\. By renaming the database, you can replace the DB instance without having to change any application code that references the DB instance\. In these cases, you would do the following: 

1. Stop all traffic going to the primary DB instance\. This can involve redirecting traffic from accessing the databases on the DB instance or some other way you want to use to prevent traffic from accessing your databases on the DB instance\. 

1. Rename the primary DB instance to a name that indicates it is no longer the primary DB instance as described later in this topic\. 

1. Create a new primary DB instance by restoring from a DB snapshot or by promoting a read replica, and then give the new instance the name of the previous primary DB instance\. 

1. Associate any read replicas with the new primary DB instance\. 

If you delete the old primary DB instance, you are responsible for deleting any unwanted DB snapshots of the old primary DB instance\. 

For information about promoting a read replica, see [Promoting a Read Replica to Be a Standalone DB Instance](USER_ReadRepl.md#USER_ReadRepl.Promote)\. 

## Console<a name="USER_RenameInstance.CON"></a>

**To rename a DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose the DB instance that you want to rename\.

1. Choose **Modify**\.

1. In **Settings**, enter a new name for **DB instance identifier**\.

1. Choose **Continue**\.

1. To apply the changes immediately, choose **Apply immediately**\. Choosing this option can cause an outage in some cases\. For more information, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md)\. 

1. On the confirmation page, review your changes\. If they are correct, choose **Modify DB Instance** to save your changes\. 

   Alternatively, choose **Back** to edit your changes, or choose **Cancel** to cancel your changes\. 

## AWS CLI<a name="USER_RenameInstance.CLI"></a>

To rename a DB instance, use the AWS CLI command [https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html)\. Provide the current `--db-instance-identifier` value and `--new-db-instance-identifier` parameter with the new name of the DB instance\. 

**Example**  
For Linux, macOS, or Unix:  

```
1. aws rds modify-db-instance \
2.     --db-instance-identifier DBInstanceIdentifier \
3.     --new-db-instance-identifier NewDBInstanceIdentifier
```
For Windows:  

```
1. aws rds modify-db-instance ^
2.     --db-instance-identifier DBInstanceIdentifier ^
3.     --new-db-instance-identifier NewDBInstanceIdentifier
```

## RDS API<a name="USER_RenameInstance.API"></a>

To rename a DB instance, call Amazon RDS API function [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html) with the following parameters:
+ `DBInstanceIdentifier` = existing name for the instance
+ `NewDBInstanceIdentifier` = new name for the instance

```
 1. https://rds.amazonaws.com/
 2. 	?Action=ModifyDBInstance
 3. 	&DBInstanceIdentifier=mydbinstance
 4. 	&NewDBInstanceIdentifier=mynewdbinstanceidentifier
 5. 	&Version=2012-01-15						
 6. 	&SignatureVersion=2
 7. 	&SignatureMethod=HmacSHA256
 8. 	&Timestamp=2012-01-20T22%3A06%3A23.624Z
 9. 	&AWSAccessKeyId=<AWS Access Key ID>
10. 	&Signature=<Signature>
```