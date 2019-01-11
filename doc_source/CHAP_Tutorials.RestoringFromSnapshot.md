# Tutorial: Restore a DB Instance from a DB Snapshot<a name="CHAP_Tutorials.RestoringFromSnapshot"></a>

A common scenario when working with Amazon RDS is to have a DB instance that you work with occasionally but that you don't need full time\. For example, you might have a quarterly customer survey that uses an Amazon Elastic Compute Cloud \(Amazon EC2\) instance to host a customer survey website and a DB instance that is used to store the survey results\. One way to save money on such a scenario is to take a DB snapshot of the DB instance after the survey is completed, delete the DB instance, and then restore the DB instance when you need to conduct the survey again\. 

In the following illustration, you can see a possible scenario where an EC2 instance hosting a customer survey website is in the same Amazon Virtual Private Cloud \(Amazon VPC\) as a DB instance that retains the customer survey data\. Note that each instance has its own security group; the EC2 instance security group allows access from the internet while the DB instance security group allows access only to and from the EC2 instance\. When the survey is done, the EC2 instance can be stopped and the DB instance can be deleted after a final DB snapshot is created\. When you need to conduct another survey, you can restart the EC2 instance and restore the DB instance from the DB snapshot\. 

![\[Tutorial VPC use\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/tut-restoring1.png)

For information about how to set up the needed VPC security groups for this scenario that allows the EC2 instance to connect with the DB instance, see [A DB Instance in a VPC Accessed by an EC2 Instance in the Same VPC](USER_VPC.Scenarios.md#USER_VPC.Scenario1)\. 

 You must create a DB snapshot before you can restore a DB instance from one\. When you restore the DB instance, you provide the name of the DB snapshot to restore from, and then provide a name for the new DB instance that is created from the restore operation\. You cannot restore from a DB snapshot to an existing DB instance; a new DB instance is created when you restore\. 

## Prerequisites for Restoring a DB Instance from a DB Snapshot<a name="CHAP_Tutorials.RestoringFromSnapshot.Prerequisites"></a>

Some settings on the restored DB instance are reset when the instance is restored, so you must retain the original resources to be able to restore the DB instance to its previous settings\. For example, when you restore a DB instance from a DB snapshot, the default DB parameter and a default security group are associated with the restored instance\. That association means that the default security group does not allow access to the DB instance, and no custom parameter settings are available in the default parameter group\. You need to retain the DB parameter group and security group associated with the DB instance that was used to create the DB snapshot\. 

The following are required before you can restore a DB instance from a DB snapshot: 
+ You must have created a DB snapshot of a DB instance before you can restore a DB instance from that DB snapshot\. For more information about creating a DB snapshot, see [Creating a DB Snapshot](USER_CreateSnapshot.md)\. 
+ You must retain the parameter group and security group associated with the DB instance you created the DB snapshot from\. 
+  You need to determine the correct option group for the restored DB instance: 
  + The option group associated with the DB snapshot that you restore from is associated with the restored DB instance once it is created\. For example, if the DB snapshot you restore from uses Oracle Transparent Data Encryption \(TDE\), the restored DB instance uses the same option group, which had the TDE option\. 
  + You cannot use the option group associated with the original DB instance if you attempt to restore that instance into a different VPC or into a different platform\. This restriction occurs because when an option group is assigned to a DB instance, it is also linked to the platform that the DB instance is on, either VPC or EC2\-Classic \(non\-VPC\)\. If a DB instance is in a VPC, the option group associated with the instance is linked to that VPC\. 
  +  If you restore a DB instance into a different VPC or onto a different platform, you must either assign the default option group to the instance, assign an option group that is linked to that VPC or platform, or create a new option group and assign it to the DB instance\. Note that with persistent or permanent options, such as Oracle TDE, you must create a new option group that includes the persistent or permanent option when restoring a DB instance into a different VPC\. For more information about working with option groups, see [Working with Option Groups](USER_WorkingWithOptionGroups.md)\. 

## Restoring a DB Instance from a DB Snapshot<a name="CHAP_Tutorials.RestoringFromSnapshot.Steps"></a>

You can use the procedure following to restore from a snapshot in the AWS Management Console\. 

**To restore a DB instance from a DB snapshot**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Snapshots**\.

1. Choose the DB snapshot that you want to restore from\. 

1. For **Actions**, choose **Restore Snapshot**\.  
![\[Console restore snapshot db\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/tut-restoring2.png)

   The **Restore DB Instance** page appears\.

1. For **DB Instance Identifier** under **Settings**, enter the name that you want to use for the restored DB instance\. If you are restoring from a DB instance that you deleted after you made the DB snapshot, you can use the name of that DB instance\.

1. Choose **Restore DB Instance**\.

## Modifying a Restored DB Instance<a name="CHAP_Tutorials.RestoringFromSnapshot.Modifying"></a>

As soon as the restore operation is complete, you should associate the custom security group used by the instance you restored from with any applicable custom DB parameter group that you might have\. Only the default DB parameter and security groups are associated with the restored instance\. If you want to restore the functionality of the DB instance to that of the DB instance that the snapshot was created from, you must modify the DB instance to use the security group and parameter group used by the previous DB instance\. 

You must apply any changes explicitly using the RDS console's **Modify** command, the `ModifyDBInstance` API, or the `aws rds modify-db-instance` command line tool, once the DB instance is available\. We recommend that you retain parameter groups for any DB snapshots you have so that you can associate a restored instance with the correct parameter file\. 

You can modify other settings on the restored DB instance\. For example, you can use a different storage type than the source DB snapshot\. In this case the restoration process is slower because of the additional work required to migrate the data to the new storage type\. In the case of restoring to or from Magnetic \(Standard\) storage, the migration process is the slowest, because Magnetic storage does not have the IOPS capability of Provisioned IOPS or General Purpose \(SSD\) storage\. 

 The next steps assume that your DB instance is in a VPC\. If your DB instance is not in a VPC, use the AWS Management Console to locate the DB security group you need for the DB instance\. 

**To modify a restored DB instance to have the settings of the original DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose the name of the DB instance created when you restored from the DB snapshot to display its details\. Choose the **Connectivity** tab\. The security group assigned to the DB instance might not allow access\. If there are no inbound rules, no permissions exist that allow inbound access\.   
![\[Restored snapshot db\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/tut-restoring25.png)

1. Choose **Modify**\. 

1. In the **Network & Security** section, choose the security group that you want to use for your DB instance\. If you need to add rules to create a new security group to use with an EC2 instance, see [A DB Instance in a VPC Accessed by an EC2 Instance in the Same VPC](USER_VPC.Scenarios.md#USER_VPC.Scenario1) for more information\. 

   You can also remove a security group by choosing the **X** associated with it\.  
![\[Choose security group\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/tut-restoring3.png)

1. Choose **Continue**, and then choose **Apply immediately**\. 

1. Choose **Modify DB Instance**\. 

   After the instance status is available, choose the DB instance name to display its details\. Choose the **Connectivity** tab, and confirm that the new security group has been applied, making the DB instance authorized for access\.   
![\[Console restore snapshot DB\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/tut-restoring4.png)