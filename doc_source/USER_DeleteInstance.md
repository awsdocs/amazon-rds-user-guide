# Deleting a DB Instance<a name="USER_DeleteInstance"></a>

You can delete a DB instance in any state and at any time\. To delete a DB instance, you must specify the name of the instance, and specify whether to take a final DB snapshot taken of the instance\. 

If the DB instance you want to delete has a Read Replica, you should either promote the Read Replica or delete it\. For more information, see [Promoting a Read Replica to Be a Standalone DB Instance](USER_ReadRepl.md#USER_ReadRepl.Promote)\. 

## Final Snapshot<a name="USER_DeleteInstance.Snapshot"></a>

When you delete a DB instance, you can choose whether to create a final snapshot of the DB instance\. If you want to be able to restore the DB instance at a later time, you should create a final snapshot\. 


****  

|  | With Final Snapshot | Without Final Snapshot | 
| --- | --- | --- | 
|  How to Choose  |  You should create a final DB snapshot if you want to be able to restore your deleted DB instance at a later time\.   |  You can skip creating a final DB snapshot if you want to delete a DB instance quickly\.   You will not be able to restore the DB instance later\. If you have an earlier manual snapshot of the DB instance, you can restore the DB instance to the point\-in\-time of the earlier manual snapshot\.    | 
|  Automated Backups  |  All automated backups are deleted and can't be recovered\.  |  All automated backups are deleted and can't be recovered\.  | 
|  Manual Snapshots  |  Earlier manual snapshots are not deleted\.   |  Earlier manual snapshots are not deleted\.   | 

You can't create a final snapshot of your DB instance if it has one of the following statuses: `creating`, `failed`, `incompatible-restore`, or `incompatible-network`\. For more information about DB instance statuses, see [DB Instance Status](Overview.DBInstance.Status.md)\. 

## Delete a DB Instance<a name="USER_DeleteInstance.Deleting"></a>

You can delete a DB instance using the AWS Management Console, the AWS CLI, or the RDS API\.

### AWS Management Console<a name="USER_DeleteInstance.CON"></a>

**To delete a DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Instances**, and then select the DB instance that you want to delete\. 

1. Choose **Instance actions**, and then choose **Delete**\. 

1. For **Create final Snapshot?**, choose **Yes** or **No**\. 

1. If you chose yes in the previous step, for **Final snapshot name** type the name of your final DB snapshot\. 

1. Type **delete me** in the box\.

1. Choose **Delete**\. 

### CLI<a name="USER_DeleteInstance.CLI"></a>

To delete a DB instance by using the AWS CLI, call the [delete\-db\-instance](http://docs.aws.amazon.com/cli/latest/reference/rds/delete-db-instance.html) command with the following options: 
+ `--db-instance-identifier`
+ `--final-db-snapshot-identifier` or `--skip-final-snapshot`

**Example With a Final Snapshot**  
For Linux, OS X, or Unix:  

```
1. aws rds delete-db-instance \
2.     --db-instance-identifier mydbinstance  \
3.     --final-db-snapshot-identifier mydbinstancefinalsnapshot
```
For Windows:  

```
1. aws rds delete-db-instance ^
2.     --db-instance-identifier mydbinstance  ^
3.     --final-db-snapshot-identifier mydbinstancefinalsnapshot
```

**Example Without a Final Snapshot**  
For Linux, OS X, or Unix:  

```
1. aws rds delete-db-instance \
2.     --db-instance-identifier mydbinstance \
3.     --skip-final-snapshot
```
For Windows:  

```
1. aws rds delete-db-instance ^
2.     --db-instance-identifier mydbinstance ^
3.     --skip-final-snapshot
```

### API<a name="USER_DeleteInstance.API"></a>

To delete a DB instance by using the Amazon RDS API, call the [http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DeleteDBInstance.html](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DeleteDBInstance.html) action with the following parameters: 
+ `DBInstanceIdentifier`
+ `FinalDBSnapshotIdentifier` or `SkipFinalSnapshot`

**Example With a Final Snapshot**  

```
 1. https://rds.amazonaws.com/ 
 2.     ?Action=DeleteDBInstance
 3.     &DBInstanceIdentifier=mydbinstance
 4.     &FinalDBSnapshotIdentifier=mydbinstancefinalsnapshot
 5.     &SignatureMethod=HmacSHA256
 6.     &SignatureVersion=4
 7.     &Version=2014-10-31
 8.     &X-Amz-Algorithm=AWS4-HMAC-SHA256
 9.     &X-Amz-Credential=AKIADQKE4SARGYLE/20140305/us-west-1/rds/aws4_request
10.     &X-Amz-Date=20140305T185838Z
11.     &X-Amz-SignedHeaders=content-type;host;user-agent;x-amz-content-sha256;x-amz-date
12.     &X-Amz-Signature=b441901545441d3c7a48f63b5b1522c5b2b37c137500c93c45e209d4b3a064a3
```

**Example Without a Final Snapshot**  

```
 1. https://rds.amazonaws.com/
 2.     ?Action=DeleteDBInstance
 3.     &DBInstanceIdentifier=mydbinstance
 4.     &SkipFinalSnapshot=true
 5.     &SignatureMethod=HmacSHA256
 6.     &SignatureVersion=4
 7.     &Version=2014-10-31
 8.     &X-Amz-Algorithm=AWS4-HMAC-SHA256
 9.     &X-Amz-Credential=AKIADQKE4SARGYLE/20140305/us-west-1/rds/aws4_request
10.     &X-Amz-Date=20140305T185838Z
11.     &X-Amz-SignedHeaders=content-type;host;user-agent;x-amz-content-sha256;x-amz-date
12.     &X-Amz-Signature=b441901545441d3c7a48f63b5b1522c5b2b37c137500c93c45e209d4b3a064a3
```