# Creating a DB Snapshot<a name="USER_CreateSnapshot"></a>

Amazon RDS creates a storage volume snapshot of your DB instance, backing up the entire DB instance and not just individual databases\. Creating this DB snapshot on a Single\-AZ DB instance results in a brief I/O suspension that can last from a few seconds to a few minutes, depending on the size and class of your DB instance\. Multi\-AZ DB instances are not affected by this I/O suspension since the backup is taken on the standby\. 

 When you create a DB snapshot, you need to identify which DB instance you are going to back up, and then give your DB snapshot a name so you can restore from it later\. If you have IAM database authentication enabled, then this setting is inherited from the source DB instance\. 

The amount of time it takes to create a snapshot varies with the size your databases\. Since the snapshot includes the entire storage volume, the size of files, such as temporary files, also affects the amount of time it takes to create the snapshot\. 

## AWS Management Console<a name="USER_CreateSnapshot.CON"></a>

**To create a DB snapshot**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, click **DB Instances**\.

1. Click **Instance Actions**, and then click **Take DB Snapshot**\.

   The **Take DB Snapshot** window appears\.

1.  Type the name of the snapshot in the **Snapshot Name** text box\.   
![\[Console db snapshot edit db\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/DBSnapshot.png)

1.  Click **Yes, Take Snapshot**\. 

## CLI<a name="USER_CreateSnapshot.CLI"></a>

When you create a DB snapshot using the AWS CLI, you need to identify which DB instance you are going to back up, and then give your DB snapshot a name so you can restore from it later\. You can do this by using the AWS CLI [http://docs.aws.amazon.com/cli/latest/reference/rds/create-db-snapshot.html](http://docs.aws.amazon.com/cli/latest/reference/rds/create-db-snapshot.html) command with the following parameters:

+ `--db-instance-identifier`

+ `--db-snapshot-identifier`

In this example, you create a DB snapshot called *mydbsnapshot* for a DB instance called *mydbinstance*\.

**Example**  
For Linux, OS X, or Unix:  

```
1. aws rds create-db-snapshot /
2.     --db-instance-identifier mydbinstance /
3.     --db-snapshot-identifier mydbsnapshot
```
For Windows:  

```
1. aws rds create-db-snapshot ^
2.     --db-instance-identifier mydbinstance ^
3.     --db-snapshot-identifier mydbsnapshot
```
The output from this command should look similar to the following:  

```
1. DBSNAPSHOT  mydbsnapshot  mydbinstance  2009-10-21T01:54:49.521Z  MySQL     50
2. creating  sa  5.6.27 general-public-license
```

## API<a name="USER_CreateSnapshot.API"></a>

When you create a DB snapshot using the Amazon RDS API, you need to identify which DB instance you are going to back up, and then give your DB snapshot a name so you can restore from it later\. You can do this by using the Amazon RDS API [http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBSnapshot.html](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBSnapshot.html) command with the following parameters:

+ DBInstanceIdentifier

+ DBSnapshotIdentifier

In this example, you create a DB snapshot called *mydbsnapshot* for a DB instance called *mydbinstance*\.

**Example**  

```
https://rds.us-east-1.amazonaws.com/
    ?Action=CreateDBSnapshot
    &DBInstanceIdentifier=mydbinstance
    &DBSnapshotIdentifier=mydbsnapshot
    &SignatureMethod=HmacSHA256
    &SignatureVersion=4
    &Version=2013-09-09
    &X-Amz-Algorithm=AWS4-HMAC-SHA256
    &X-Amz-Credential=AKIADQKE4SARGYLE/20140423/us-east-1/rds/aws4_request
    &X-Amz-Date=20140423T161105Z
    &X-Amz-SignedHeaders=content-type;host;user-agent;x-amz-content-sha256;x-amz-date
    &X-Amz-Signature=e9649af6edcfbab4016f04d72e1b7fc16d8734c37477afcf25b3def625484ed2
```

## Related Topics<a name="USER_CreateSnapshot.related"></a>

+ [Restoring from a DB Snapshot](USER_RestoreFromSnapshot.md)

+ [Copying a DB Snapshot or DB Cluster Snapshot](USER_CopySnapshot.md)

+ [Sharing a DB Snapshot or DB Cluster Snapshot](USER_ShareSnapshot.md)