# Creating a DB Snapshot<a name="USER_CreateSnapshot"></a>

Amazon RDS creates a storage volume snapshot of your DB instance, backing up the entire DB instance and not just individual databases\. Creating this DB snapshot on a Single\-AZ DB instance results in a brief I/O suspension that can last from a few seconds to a few minutes, depending on the size and class of your DB instance\. Multi\-AZ DB instances are not affected by this I/O suspension since the backup is taken on the standby\. 

When you create a DB snapshot, you need to identify which DB instance you are going to back up, and then give your DB snapshot a name so you can restore from it later\. The amount of time it takes to create a snapshot varies with the size your databases\. Since the snapshot includes the entire storage volume, the size of files, such as temporary files, also affects the amount of time it takes to create the snapshot\. 

You can create a DB snapshot using the AWS Management Console, the AWS CLI, or the RDS API\.

## Console<a name="USER_CreateSnapshot.CON"></a>

**To create a DB snapshot**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. In the list of DB instances, choose the DB instance for which you want to take a snapshot\.

1. For **Actions**, choose **Take snapshot**\.

   The **Take DB Snapshot** window appears\.

1. Type the name of the snapshot in the **Snapshot Name** box\.  
![\[Console db snapshot edit db\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/DBSnapshot.png)

1. Choose **Take Snapshot**\.

## AWS CLI<a name="USER_CreateSnapshot.CLI"></a>

When you create a DB snapshot using the AWS CLI, you need to identify which DB instance you are going to back up, and then give your DB snapshot a name so you can restore from it later\. You can do this by using the AWS CLI [https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-snapshot.html](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-snapshot.html) command with the following parameters:
+ `--db-instance-identifier`
+ `--db-snapshot-identifier`

In this example, you create a DB snapshot called *mydbsnapshot* for a DB instance called *mydbinstance*\.

**Example**  
For Linux, OS X, or Unix:  

```
1. aws rds create-db-snapshot \
2.     --db-instance-identifier mydbinstance \
3.     --db-snapshot-identifier mydbsnapshot
```
For Windows:  

```
1. aws rds create-db-snapshot ^
2.     --db-instance-identifier mydbinstance ^
3.     --db-snapshot-identifier mydbsnapshot
```

## RDS API<a name="USER_CreateSnapshot.API"></a>

When you create a DB snapshot using the Amazon RDS API, you need to identify which DB instance you are going to back up, and then give your DB snapshot a name so you can restore from it later\. You can do this by using the Amazon RDS API [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBSnapshot.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBSnapshot.html) command with the following parameters:
+ `DBInstanceIdentifier`
+ `DBSnapshotIdentifier`