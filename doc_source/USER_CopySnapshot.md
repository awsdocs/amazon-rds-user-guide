# Copying a DB Snapshot or DB Cluster Snapshot<a name="USER_CopySnapshot"></a>

With Amazon Relational Database Service \(Amazon RDS\), you can copy DB snapshots and DB cluster snapshots\. You can copy automated or manual snapshots\. After you copy a snapshot, the copy is a manual snapshot\. 

You can copy a snapshot within the same AWS Region, you can copy a snapshot across AWS Regions, and you can copy a snapshot across AWS accounts\. 

You can't copy a DB cluster snapshot across regions and accounts in a single step\. Perform one step for each of these copy actions\. As an alternative to copying, you can also share manual snapshots with other AWS accounts\. For more information, see [Sharing a DB Snapshot or DB Cluster Snapshot](USER_ShareSnapshot.md)\. 

## Limitations<a name="USER_CopySnapshot.Limitations"></a>

The following are some limitations when you copy snapshots: 

+ You can't copy a snapshot to or from the following regions: AWS GovCloud \(US\), China \(Beijing\)\. 

+ If you delete a source snapshot before the target snapshot becomes available, the snapshot copy may fail\. Verify that the target snapshot has a status of `AVAILABLE` before you delete a source snapshot\. 

+ You can have up to five snapshot copy requests in progress to a single destination per account\.

+ You can't copy a DB snapshot across regions if it was created from an Oracle DB instance that is using AWS CloudHSM Classic to store TDE Keys\. 

+ Depending on the regions involved and the amount of data to be copied, a cross\-region snapshot copy can take hours to complete\. If there is a large number of cross\-region snapshot copy requests from a given source AWS Region, Amazon RDS might put new cross\-region copy requests from that source AWS Region into a queue until some in\-progress copies complete\. No progress information is displayed about copy requests while they are in the queue\. Progress information is displayed when the copy starts\. 

## Snapshot Retention<a name="USER_CopySnapshot.Retention"></a>

Amazon RDS deletes automated snapshots at the end of their retention period, when you disable automated snapshots for a DB instance or DB cluster, or when you delete a DB instance or DB cluster\. If you want to keep an automated snapshot for a longer period, copy it to create a manual snapshot, which is retained until you delete it\. Amazon RDS storage costs might apply to manual snapshots if they exceed your default storage space\. 

For more information about backup storage costs, see [Amazon RDS Pricing](https://aws.amazon.com/rds/pricing/)\. 

## Copying Shared Snapshots<a name="USER_CopySnapshot.Shared"></a>

You can copy snapshots shared to you by other AWS accounts\. If you are copying an encrypted snapshot that has been shared from another AWS account, you must have access to the KMS encryption key that was used to encrypt the snapshot\. You can copy shared unencrypted DB snapshots across regions, but you can only copy shared encrypted DB snapshots in the same AWS Region\. You can only copy shared DB cluster snapshots, encrypted or not, in the same AWS Region\. For more information, see [Sharing an Encrypted Snapshot](USER_ShareSnapshot.md#USER_ShareSnapshot.Encrypted)\. 

## Handling Encryption<a name="USER_CopySnapshot.Encryption"></a>

You can copy a snapshot that has been encrypted using an AWS KMS encryption key\. If you copy an encrypted snapshot, the copy of the snapshot must also be encrypted\. If you copy an encrypted snapshot within the same AWS Region, you can encrypt the copy with the same KMS encryption key as the original snapshot, or you can specify a different KMS encryption key\. If you copy an encrypted snapshot across regions, you can't use the same KMS encryption key for the copy as used for the source snapshot, because KMS keys are region\-specific\. Instead, you must specify a KMS key valid in the destination AWS Region\. 

You can also encrypt a copy of an unencrypted snapshot\. This way, you can quickly add encryption to a previously unencrypted DB instance or DB cluster\. That is, you can create a snapshot of your DB instance or DB cluster when you are ready to encrypt it, and then create a copy of that snapshot and specify a KMS encryption key to encrypt that snapshot copy\. You can then restore an encrypted DB instance or DB cluster from the encrypted snapshot\. For Amazon Aurora DB cluster snapshots, you also have the option to leave the DB cluster snapshot unencrypted and instead specify a KMS encryption key when restoring\. The restored DB cluster is encrypted using the specified key\. 

## Option Group Considerations<a name="USER_CopySnapshot.Options"></a>

Option groups are specific to the AWS Region that they are created in, and you can't use an option group from one AWS Region in another AWS Region\. 

When you copy a snapshot across regions, you can specify a new option group for the snapshot\. We recommend that you prepare the new option group before you copy the snapshot\. In the destination AWS Region, create an option group with the same settings as the original DB instance or DB cluster\. If one already exists in the new AWS Region, you can use that one\. 

If you copy a snapshot and you don't specify a new option group for the snapshot, when you restore it the DB instance or DB cluster gets the default option group\. To give the new DB instance or DB cluster the same options as the original, you must do the following: 

1. In the destination AWS Region, create an option group with the same settings as the original DB instance or DB cluster\. If one already exists in the new AWS Region, you can use that one\. 

1. After you restore the snapshot in the destination AWS Region, modify the new DB instance or DB cluster and add the new or existing option group from the previous step\. 

## Parameter Group Considerations<a name="USER_CopySnapshot.Parameters"></a>

When you copy a snapshot across regions, the copy doesn't include the parameter group used by the original DB instance or DB cluster\. When you restore a snapshot to create a new DB instance or DB cluster, that DB instance or DB cluster gets the default parameter group for the AWS Region it is created in\. To give the new DB instance or DB cluster the same parameters as the original, you must do the following: 

1. In the destination AWS Region, create a DB parameter group or DB cluster parameter group with the same settings as the original DB instance or DB cluster\. If one already exists in the new AWS Region, you can use that one\. 

1. After you restore the snapshot in the destination AWS Region, modify the new DB instance or DB cluster and add the new or existing parameter group from the previous step\. 

## Copying a DB Snapshot<a name="USER_CopyDBSnapshot.CopyDBSnapshot"></a>

If your source database engine is MariaDB, Microsoft SQL Server, MySQL, Oracle, or PostgreSQL, then your snapshot is a DB snapshot\. For instructions on how to copy a DB snapshot, see [Copying a DB Snapshot](USER_CopyDBSnapshot.md)\. 

## Copying a DB Cluster Snapshot<a name="USER_CopyDBSnapshot.CopyDBClusterSnapshot"></a>

If your source database engine is Aurora, then your snapshot is a DB cluster snapshot\. For instructions on how to copy a db cluster snapshot, see [Copying a DB Cluster Snapshot](USER_CopyDBClusterSnapshot.CrossRegion.md)\. 

## Related Topics<a name="USER_CopySnapshot.Related"></a>

+ [Creating a DB Snapshot](USER_CreateSnapshot.md)

+ [Restoring from a DB Snapshot](USER_RestoreFromSnapshot.md)

+ [Backing Up and Restoring Amazon RDS DB Instances](CHAP_CommonTasks.BackupRestore.md)