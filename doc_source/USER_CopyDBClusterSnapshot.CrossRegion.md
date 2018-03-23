# Copying a DB Cluster Snapshot<a name="USER_CopyDBClusterSnapshot.CrossRegion"></a>

Use the procedures in this topic to copy a DB cluster snapshot\. If your source database engine is Aurora, then your snapshot is a DB cluster snapshot\. If your source database engine is MariaDB, Microsoft SQL Server, MySQL, Oracle, or PostgreSQL, then your snapshot is a DB snapshot\. For instructions on how to copy a DB snapshot, see [Copying a DB Snapshot](USER_CopyDBSnapshot.md)\. 

For each AWS account, you can copy up to five DB cluster snapshots at a time from one AWS Region to another\. Copying both encrypted and unencrypted DB cluster snapshots is supported\. If you copy a DB cluster snapshot to another AWS Region, you create a manual DB cluster snapshot that is retained in that AWS Region\. Copying a DB cluster snapshot out of the source AWS Region incurs Amazon RDS data transfer charges\. 

For more information about data transfer pricing, see [Amazon RDS Pricing](https://aws.amazon.com/rds/pricing/)\. 

After the DB cluster snapshot copy has been created in the new AWS Region, the DB cluster snapshot copy behaves the same as all other DB cluster snapshots in that AWS Region\. 

## AWS Management Console<a name="USER_CopyDBClusterSnapshot.CON"></a>

This procedure works for copying encrypted or unencrypted DB cluster snapshots, in the same AWS Region or across regions\.

To cancel a copy operation once it is in progress, delete the target DB cluster snapshot while that DB cluster snapshot is in **copying** status\.

**To copy a DB cluster snapshot**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Snapshots**\.

1. Select the check box for the DB snapshot you want to copy\.

1. Choose **Snapshot Actions**, and then choose **Copy Snapshot**\. The **Make Copy of DB Snapshot** page appears\.   
![\[Copy a DB snapshot\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/DBSnapshotCopy1.png)

1. \(Optional\) To copy the DB cluster snapshot to a different AWS Region, choose that AWS Region for **Destination Region**\.

1. Type the name of the DB cluster snapshot copy in **New DB Snapshot Identifier**\. 

1. To copy tags and values from the snapshot to the copy of the snapshot, choose **Copy Tags**\.

1. For **Enable Encryption**, choose one of the following options: 
   + Choose **No** if the DB cluster snapshot isn't encrypted and you don't want to encrypt the copy\. 
   + Choose **Yes** if the DB cluster snapshot isn't encrypted but you want to encrypt the copy\. In this case, for **Master Key**, specify the KMS key identifier to use to encrypt the DB cluster snapshot copy\. 
   + Choose **Yes** if the DB cluster snapshot is encrypted\. In this case, you must encrypt the copy, so **Yes** is already selected\. For **Master Key**, specify the KMS key identifier to use to encrypt the DB cluster snapshot copy\. 

1. Choose **Copy Snapshot**\. 

## Copying an Unencrypted DB Cluster Snapshot by Using the AWS CLI or Amazon RDS API<a name="USER_CopyDBClusterSnapshot.Unencrypted.CrossRegion"></a>

Use the procedures in the following sections to copy an unencrypted DB cluster snapshot by using the AWS CLI or Amazon RDS API\.

To cancel a copy operation once it is in progress, delete the target DB cluster snapshot identified by `--target-db-cluster-snapshot-identifier` or `TargetDBClusterSnapshotIdentifier` while that DB cluster snapshot is in **copying** status\.

### CLI<a name="USER_CopyDBClusterSnapshot.Unencrypted.CrossRegion.CLI"></a>

To copy a DB cluster snapshot, use the AWS CLI [http://docs.aws.amazon.com/cli/latest/reference/rds/copy-db-cluster-snapshot.html](http://docs.aws.amazon.com/cli/latest/reference/rds/copy-db-cluster-snapshot.html) command\. If you are copying the snapshot to another AWS Region, run the command in the AWS Region to which the snapshot will be copied\. 

The following options are used to copy an unencrypted DB cluster snapshot:
+ `--source-db-cluster-snapshot-identifier` – The identifier for the DB cluster snapshot to be copied\. If you are copying the snapshot to another AWS Region, this identifier must be in the ARN format for the source AWS Region\.
+ `--target-db-cluster-snapshot-identifier` – The identifier for the new copy of the DB cluster snapshot\.

The following code creates a copy of DB cluster snapshot `arn:aws:rds:us-east-1:123456789012:cluster-snapshot:aurora-cluster1-snapshot-20130805` named `myclustersnapshotcopy` in the AWS Region in which the command is run\. When the copy is made, all tags on the original snapshot are copied to the snapshot copy\.

**Example**  
For Linux, OS X, or Unix:  

```
1. aws rds copy-db-cluster-snapshot \
2.   --source-db-cluster-snapshot-identifier arn:aws:rds:us-east-1:123456789012:cluster-snapshot:aurora-cluster1-snapshot-20130805 \
3.   --target-db-cluster-snapshot-identifier myclustersnapshotcopy \
4.   --copy-tags
```
For Windows:  

```
1. aws rds copy-db-cluster-snapshot ^
2.   --source-db-cluster-snapshot-identifier arn:aws:rds:us-east-1:123456789012:cluster-snapshot:aurora-cluster1-snapshot-20130805 ^
3.   --target-db-cluster-snapshot-identifier myclustersnapshotcopy ^
4.   --copy-tags
```

### API<a name="USER_CopyDBClusterSnapshot.Unencrypted.CrossRegion.API"></a>

To copy a DB cluster snapshot, use the Amazon RDS API [http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CopyDBClusterSnapshot.html](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CopyDBClusterSnapshot.html) action\. If you are copying the snapshot to another AWS Region, perform the action in the AWS Region to which the snapshot will be copied\. 

The following parameters are used to copy an unencrypted DB cluster snapshot:
+ `SourceDBClusterSnapshotIdentifier` – The identifier for the DB cluster snapshot to be copied\. If you are copying the snapshot to another AWS Region, this identifier must be in the ARN format for the source AWS Region\.
+ `TargetDBClusterSnapshotIdentifier` – The identifier for the new copy of the DB cluster snapshot\.

The following code creates a copy of a snapshot `arn:aws:rds:us-east-1:123456789012:cluster-snapshot:aurora-cluster1-snapshot-20130805` named `myclustersnapshotcopy` in the us\-west\-1 region\. When the copy is made, all tags on the original snapshot are copied to the snapshot copy\.

**Example**  

```
 1. https://rds.us-west-1.amazonaws.com/
 2.    ?Action=CopyDBClusterSnapshot
 3.    &CopyTags=true
 4.    &SignatureMethod=HmacSHA256
 5.    &SignatureVersion=4
 6.    &SourceDBSnapshotIdentifier=arn%3Aaws%3Ards%3Aus-east-1%3A123456789012%3Acluster-snapshot%3Aaurora-cluster1-snapshot-20130805
 7.    &TargetDBSnapshotIdentifier=myclustersnapshotcopy
 8.    &Version=2013-09-09
 9.    &X-Amz-Algorithm=AWS4-HMAC-SHA256
10.    &X-Amz-Credential=AKIADQKE4SARGYLE/20140429/us-west-1/rds/aws4_request
11.    &X-Amz-Date=20140429T175351Z
12.    &X-Amz-SignedHeaders=content-type;host;user-agent;x-amz-content-sha256;x-amz-date
13.    &X-Amz-Signature=9164337efa99caf850e874a1cb7ef62f3cea29d0b448b9e0e7c53b288ddffed2
```

## Copying an Encrypted DB Cluster Snapshot by Using the AWS CLI or Amazon RDS API<a name="USER_CopyDBClusterSnapshot.Encrypted.CrossRegion"></a>

Use the procedures in the following sections to copy an encrypted DB cluster snapshot by using the AWS CLI or Amazon RDS API\.

To cancel a copy operation once it is in progress, delete the target DB cluster snapshot identified by `--target-db-cluster-snapshot-identifier` or `TargetDBClusterSnapshotIdentifier` while that DB cluster snapshot is in **copying** status\.

### CLI<a name="USER_CopyDBClusterSnapshot.Encrypted.CrossRegion.CLI"></a>

To copy a DB cluster snapshot, use the AWS CLI [http://docs.aws.amazon.com/cli/latest/reference/rds/copy-db-cluster-snapshot.html](http://docs.aws.amazon.com/cli/latest/reference/rds/copy-db-cluster-snapshot.html) command\. If you are copying the snapshot to another AWS Region, run the command in the AWS Region to which the snapshot will be copied\. 

The following options are used to copy an encrypted DB cluster snapshot:
+ `--source-region` – If you are copying the snapshot to another AWS Region, specify the AWS Region that the encrypted DB cluster snapshot will be copied from\. 

  If you are copying the snapshot to another AWS Region and you don't specify `source-region`, you must specify the `pre-signed-url` option instead\. The `pre-signed-url` value must be a URL that contains a Signature Version 4 signed request for the `CopyDBClusterSnapshot` action to be called in the source AWS Region where the DB cluster snapshot is copied from\. To learn more about the `pre-signed-url`, see [http://docs.aws.amazon.com/cli/latest/reference/rds/copy-db-cluster-snapshot.html](http://docs.aws.amazon.com/cli/latest/reference/rds/copy-db-cluster-snapshot.html)\. 
+ `--source-db-cluster-snapshot-identifier` – The identifier for the encrypted DB cluster snapshot to be copied\. If you are copying the snapshot to another AWS Region, this identifier must be in the ARN format for the source AWS Region\. If that is the case, the AWS Region specified in `source-db-cluster-snapshot-identifier` must match the AWS Region specified for `--source-region`\. 
+ `--target-db-cluster-snapshot-identifier` – The identifier for the new copy of the encrypted DB cluster snapshot\.
+ `--kms-key-id` – The KMS key identifier for the key to use to encrypt the copy of the DB cluster snapshot\.

  You can optionally use this option if the DB cluster snapshot is encrypted, you are copying the snapshot in the same AWS Region, and you want to specify a new KMS encryption key to use to encrypt the copy\. Otherwise, the copy of the DB cluster snapshot is encrypted with the same KMS key as the source DB cluster snapshot\. 

  You must use this option if the DB cluster snapshot is encrypted and you are copying the snapshot to another AWS Region\. In that case, you must specify a KMS key for the destination AWS Region\.

The following code example copies the encrypted DB cluster snapshot from the us\-west\-2 region to the us\-east\-1 region\. The command is called in the us\-east\-1 region\. 

**Example**  
For Linux, OS X, or Unix:  

```
1. aws rds copy-db-cluster-snapshot \
2.   --source-db-cluster-snapshot-identifier arn:aws:rds:us-west-2:123456789012:cluster-snapshot:aurora-cluster1-snapshot-20161115 \
3.   --target-db-cluster-snapshot-identifier myclustersnapshotcopy \
4.   --source-region us-west-2 \	
5.   --kms-key-id my-us-east-1-key
```
For Windows:  

```
1. aws rds copy-db-cluster-snapshot ^
2.   --source-db-cluster-snapshot-identifier arn:aws:rds:us-west-2:123456789012:cluster-snapshot:aurora-cluster1-snapshot-20161115 ^
3.   --target-db-cluster-snapshot-identifier myclustersnapshotcopy ^
4.   --source-region us-west-2 ^	
5.   --kms-key-id my-us-east-1-key
```

### API<a name="USER_CopyDBClusterSnapshot.Encrypted.CrossRegion.API"></a>

To copy a DB cluster snapshot, use the Amazon RDS API [http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CopyDBClusterSnapshot.html](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CopyDBClusterSnapshot.html) action\. If you are copying the snapshot to another AWS Region, perform the action in the AWS Region to which the snapshot will be copied\.

The following parameters are used to copy an encrypted DB cluster snapshot:
+ `SourceDBClusterSnapshotIdentifier` – The identifier for the encrypted DB cluster snapshot to be copied\. If you are copying the snapshot to another AWS Region, this identifier must be in the ARN format for the source AWS Region\. 
+ `TargetDBClusterSnapshotIdentifier` – The identifier for the new copy of the encrypted DB cluster snapshot\.
+ `KmsKeyId` – The KMS key identifier for the key to use to encrypt the copy of the DB cluster snapshot\.

  You can optionally use this parameter if the DB cluster snapshot is encrypted, you are copying the snapshot in the same AWS Region, and you want to specify a new KMS encryption key to use to encrypt the copy\. Otherwise, the copy of the DB cluster snapshot is encrypted with the same KMS key as the source DB cluster snapshot\. 

  You must use this parameter if the DB cluster snapshot is encrypted and you are copying the snapshot to another AWS Region\. In that case, you must specify a KMS key for the destination AWS Region\.
+ `PreSignedUrl` – If you are copying the snapshot to another AWS Region, you must specify the `PreSignedUrl` parameter\. The `PreSignedUrl` value must be a URL that contains a Signature Version 4 signed request for the `CopyDBClusterSnapshot` action to be called in the source AWS Region where the DB cluster snapshot is copied from\. To learn more about using a presigned URL, see [http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CopyDBClusterSnapshot.html](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CopyDBClusterSnapshot.html)\. 

  To automatically rather than manually generate a presigned URL, use the AWS CLI [http://docs.aws.amazon.com/cli/latest/reference/rds/copy-db-cluster-snapshot.html](http://docs.aws.amazon.com/cli/latest/reference/rds/copy-db-cluster-snapshot.html) command with the `--source-region` option instead\.

The following code example copies the encrypted DB cluster snapshot from the us\-west\-2 region to the us\-east\-1 region\. The action is called in the us\-east\-1 region\. 

**Example**  

```
 1. https://rds.us-east-1.amazonaws.com/
 2.     ?Action=CopyDBClusterSnapshot
 3.     &KmsKeyId=my-us-east-1-key
 4.     &PreSignedUrl=https%253A%252F%252Frds.us-west-2.amazonaws.com%252F
 5.          %253FAction%253DCopyDBClusterSnapshot
 6.          %2526DestinationRegion%253Dus-east-1
 7.          %2526KmsKeyId%253Dmy-us-east-1-key
 8.          %2526SourceDBClusterSnapshotIdentifier%253Darn%25253Aaws%25253Ards%25253Aus-west-2%25253A123456789012%25253Acluster-snapshot%25253Aaurora-cluster1-snapshot-20161115
 9.          %2526SignatureMethod%253DHmacSHA256
10.          %2526SignatureVersion%253D4
11.          %2526Version%253D2014-10-31
12.          %2526X-Amz-Algorithm%253DAWS4-HMAC-SHA256
13.          %2526X-Amz-Credential%253DAKIADQKE4SARGYLE%252F20161117%252Fus-west-2%252Frds%252Faws4_request
14.          %2526X-Amz-Date%253D20161117T215409Z
15.          %2526X-Amz-Expires%253D3600
16.          %2526X-Amz-SignedHeaders%253Dcontent-type%253Bhost%253Buser-agent%253Bx-amz-content-sha256%253Bx-amz-date
17.          %2526X-Amz-Signature%253D255a0f17b4e717d3b67fad163c3ec26573b882c03a65523522cf890a67fca613
18.     &SignatureMethod=HmacSHA256
19.     &SignatureVersion=4
20.     &SourceDBClusterSnapshotIdentifier=arn%3Aaws%3Ards%3Aus-west-2%3A123456789012%3Acluster-snapshot%3Aaurora-cluster1-snapshot-20161115
21.     &TargetDBClusterSnapshotIdentifier=myclustersnapshotcopy
22.     &Version=2014-10-31
23.     &X-Amz-Algorithm=AWS4-HMAC-SHA256
24.     &X-Amz-Credential=AKIADQKE4SARGYLE/20161117/us-east-1/rds/aws4_request
25.     &X-Amz-Date=20161117T221704Z
26.     &X-Amz-SignedHeaders=content-type;host;user-agent;x-amz-content-sha256;x-amz-date
27.     &X-Amz-Signature=da4f2da66739d2e722c85fcfd225dc27bba7e2b8dbea8d8612434378e52adccf
```

## Copying a DB Cluster Snapshot Across Accounts<a name="USER_CopyDBClusterSnapshot.CrossAccount"></a>

You can enable other AWS accounts to copy DB cluster snapshots that you specify by using the Amazon RDS API `ModifyDBClusterSnapshotAttribute` and `CopyDBClusterSnapshot` actions\. You can only copy DB cluster snapshots across accounts in the same AWS Region\. The cross\-account copying process works as follows, where Account A is making the snapshot available to copy, and Account B is copying it\.

1. Using Account A, call `ModifyDBClusterSnapshotAttribute`, specifying **restore** for the `AttributeName` parameter, and the ID for Account B for the `ValuesToAdd` parameter\.

1. \(If the snapshot is encrypted\) Using Account A, update the key policy for the KMS key, first adding the ARN of Account B as a `Principal`, and then allow the `kms:CreateGrant` action\.

1. \(If the snapshot is encrypted\) Using Account B, choose or create an IAM user and attach an IAM policy to that user that allows it to copy an encrypted DB snapshot using your KMS key\.

1. Using Account B, call `CopyDBClusterSnapshot` and use the `SourceDBClusterSnapshotIdentifier` parameter to specify the ARN of the DB cluster snapshot to be copied, which must include the ID for Account A\.

To list all of the AWS accounts permitted to restore a DB snapshot, use the [DescribeDBSnapshotAttributes](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBSnapshotAttributes.html) or [DescribeDBClusterSnapshotAttributes](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBClusterSnapshotAttributes.html) API action\.

To remove sharing permission for an AWS account, use the `ModifyDBSnapshotAttribute` or `ModifyDBClusterSnapshotAttribute` action with `AttributeName` set to `restore` and the ID of the account to remove in the `ValuesToRemove` parameter\.

### Copying an Unencrypted DB Cluster Snapshot to Another Account<a name="USER_CopyDBClusterSnapshot.Unencrypted.CrossAccount"></a>

Use the following procedure to copy an unencrypted DB cluster snapshot to another account in the same AWS Region\.

1. In the source account for the DB cluster snapshot, call `ModifyDBClusterSnapshotAttribute`, specifying **restore** for the `AttributeName` parameter, and the ID for the target account for the `ValuesToAdd` parameter\.

   Running the following example using the account `987654321` permits two AWS account identifiers, `123451234512` and `123456789012`, to restore the DB snapshot named `manual-snapshot1`\.

   ```
   https://rds.us-west-2.amazonaws.com/
   	?Action=ModifyDBClusterSnapshotAttribute
   	&AttributeName=restore
   	&DBClusterSnapshotIdentifier=manual-snapshot1
   	&SignatureMethod=HmacSHA256&SignatureVersion=4
   	&ValuesToAdd.member.1=123451234512
   	&ValuesToAdd.member.2=123456789012
   	&Version=2014-10-31
   	&X-Amz-Algorithm=AWS4-HMAC-SHA256
   	&X-Amz-Credential=AKIADQKE4SARGYLE/20150922/us-west-2/rds/aws4_request
   	&X-Amz-Date=20150922T220515Z
   	&X-Amz-SignedHeaders=content-type;host;user-agent;x-amz-content-sha256;x-amz-date
   	&X-Amz-Signature=ef38f1ce3dab4e1dbf113d8d2a265c67d17ece1999ffd36be85714ed36dddbb3
   ```

1. In the target account, call `CopyDBClusterSnapshot` and use the `SourceDBClusterSnapshotIdentifier` parameter to specify the ARN of the DB cluster snapshot to be copied, which must include the ID for the source account\.

   Running the following example using the account `123451234512` copies the DB cluster snapshot `aurora-cluster1-snapshot-20130805` from account `987654321` and creates a DB cluster snapshot named `dbclustersnapshot1`\.

   ```
    1. https://rds.us-west-2.amazonaws.com/
    2.    ?Action=CopyDBClusterSnapshot
    3.    &CopyTags=true
    4.    &SignatureMethod=HmacSHA256
    5.    &SignatureVersion=4
    6.    &SourceDBClusterSnapshotIdentifier=arn:aws:rds:us-west-2:987654321:cluster-snapshot:aurora-cluster1-snapshot-20130805
    7.    &TargetDBClusterSnapshotIdentifier=dbclustersnapshot1
    8.    &Version=2013-09-09
    9.    &X-Amz-Algorithm=AWS4-HMAC-SHA256
   10.    &X-Amz-Credential=AKIADQKE4SARGYLE/20150922/us-west-2/rds/aws4_request
   11.    &X-Amz-Date=20140429T175351Z
   12.    &X-Amz-SignedHeaders=content-type;host;user-agent;x-amz-content-sha256;x-amz-date
   13.    &X-Amz-Signature=9164337efa99caf850e874a1cb7ef62f3cea29d0b448b9e0e7c53b288ddffed2
   ```

### Copying an Encrypted DB Cluster Snapshot to Another Account<a name="USER_CopyDBClusterSnapshot.Encrypted.CrossAccount"></a>

Use the following procedure to copy an encrypted DB cluster snapshot to another account in the same AWS Region\.

1. In the source account for the DB cluster snapshot, call `ModifyDBClusterSnapshotAttribute`, specifying **restore** for the `AttributeName` parameter, and the ID for the target account for the `ValuesToAdd` parameter\.

   Running the following example using the account `987654321` permits two AWS account identifiers, `123451234512` and `123456789012`, to restore the DB snapshot named `manual-snapshot1`\.

   ```
   https://rds.us-west-2.amazonaws.com/
   	?Action=ModifyDBClusterSnapshotAttribute
   	&AttributeName=restore
   	&DBClusterSnapshotIdentifier=manual-snapshot1
   	&SignatureMethod=HmacSHA256&SignatureVersion=4
   	&ValuesToAdd.member.1=123451234512
   	&ValuesToAdd.member.2=123456789012
   	&Version=2014-10-31
   	&X-Amz-Algorithm=AWS4-HMAC-SHA256
   	&X-Amz-Credential=AKIADQKE4SARGYLE/20150922/us-west-2/rds/aws4_request
   	&X-Amz-Date=20150922T220515Z
   	&X-Amz-SignedHeaders=content-type;host;user-agent;x-amz-content-sha256;x-amz-date
   	&X-Amz-Signature=ef38f1ce3dab4e1dbf113d8d2a265c67d17ece1999ffd36be85714ed36dddbb3
   ```

1. In the source account for the DB cluster snapshot, update the key policy for the KMS key, first adding the ARN of the target account as a `Principa`l, and then allow the `kms:CreateGrant` action\. For more information, see [Allowing Access to an AWS KMS Encryption Key](USER_ShareSnapshot.md#USER_ShareSnapshot.Encrypted.KeyPolicy)\.

1. In the target account, choose or create an IAM user and attach an IAM policy to that user that allows it to copy an encrypted DB snapshot using your KMS key\. For more information, see [Creating an IAM Policy to Enable Copying of the Encrypted Snapshot](USER_ShareSnapshot.md#USER_ShareSnapshot.Encrypted.KeyPolicy.IAM)\.

1. In the target account, call `CopyDBClusterSnapshot` and use the `SourceDBClusterSnapshotIdentifier` parameter to specify the ARN of the DB cluster snapshot to be copied, which must include the ID for the source account\.

   Running the following example using the account `123451234512` copies the DB cluster snapshot `aurora-cluster1-snapshot-20130805` from account `987654321` and creates a DB cluster snapshot named `dbclustersnapshot1`\.

   ```
    1. https://rds.us-west-2.amazonaws.com/
    2.    ?Action=CopyDBClusterSnapshot
    3.    &CopyTags=true
    4.    &SignatureMethod=HmacSHA256
    5.    &SignatureVersion=4
    6.    &SourceDBClusterSnapshotIdentifier=arn:aws:rds:us-west-2:987654321:cluster-snapshot:aurora-cluster1-snapshot-20130805
    7.    &TargetDBClusterSnapshotIdentifier=dbclustersnapshot1
    8.    &Version=2013-09-09
    9.    &X-Amz-Algorithm=AWS4-HMAC-SHA256
   10.    &X-Amz-Credential=AKIADQKE4SARGYLE/20150922/us-west-2/rds/aws4_request
   11.    &X-Amz-Date=20140429T175351Z
   12.    &X-Amz-SignedHeaders=content-type;host;user-agent;x-amz-content-sha256;x-amz-date
   13.    &X-Amz-Signature=9164337efa99caf850e874a1cb7ef62f3cea29d0b448b9e0e7c53b288ddffed2
   ```