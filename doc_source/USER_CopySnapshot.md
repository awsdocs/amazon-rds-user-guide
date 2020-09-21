# Copying a Snapshot<a name="USER_CopySnapshot"></a>

With Amazon RDS, you can copy automated or manual DB snapshots\. After you copy a snapshot, the copy is a manual snapshot\. 

You can copy a snapshot within the same AWS Region, you can copy a snapshot across AWS Regions, and you can copy shared snapshots\. 

## Limitations<a name="USER_CopySnapshot.Limitations"></a>

The following are some limitations when you copy snapshots: 
+ You can't copy a snapshot to or from the following AWS Regions: China \(Beijing\) or China \(Ningxia\)\. 
+ You can copy a snapshot between AWS GovCloud \(US\-East\) and AWS GovCloud \(US\-West\), but you can't copy a snapshot between these AWS GovCloud \(US\) Regions and other AWS Regions\.
+ If you delete a source snapshot before the target snapshot becomes available, the snapshot copy may fail\. Verify that the target snapshot has a status of `AVAILABLE` before you delete a source snapshot\. 
+ You can have up to five snapshot copy requests in progress to a single destination Region per account\.
+ Depending on the Regions involved and the amount of data to be copied, a cross\-Region snapshot copy can take hours to complete\. If there is a large number of cross\-Region snapshot copy requests from a given source AWS Region, Amazon RDS might put new cross\-Region copy requests from that source AWS Region into a queue until some in\-progress copies complete\. No progress information is displayed about copy requests while they are in the queue\. Progress information is displayed when the copy starts\. 

## Snapshot Retention<a name="USER_CopySnapshot.Retention"></a>

Amazon RDS deletes automated snapshots at the end of their retention period, when you disable automated snapshots for a DB instance, or when you delete a DB instance\. If you want to keep an automated snapshot for a longer period, copy it to create a manual snapshot, which is retained until you delete it\. Amazon RDS storage costs might apply to manual snapshots if they exceed your default storage space\. 

For more information about backup storage costs, see [Amazon RDS Pricing](https://aws.amazon.com/rds/pricing/)\. 

## Copying Shared Snapshots<a name="USER_CopySnapshot.Shared"></a>

You can copy snapshots shared to you by other AWS accounts\. If you are copying an encrypted snapshot that has been shared from another AWS account, you must have access to the AWS KMS customer master key \(CMK\) that was used to encrypt the snapshot\. 

You can copy a shared DB snapshot across Regions, provided that the snapshot is unencrypted\. However, if the shared DB snapshot is encrypted, you can only copy it in the same AWS Region\.

**Note**  
Copying shared incremental snapshots in the same AWS Region is supported when they're unencrypted, or encrypted using the same AWS KMS key as the initial full snapshot\. If you use a different KMS key to encrypt subsequent snapshots when copying them, those shared snapshots are full snapshots\.

## Handling Encryption<a name="USER_CopySnapshot.Encryption"></a>

You can copy a snapshot that has been encrypted using an AWS KMS customer master key \(CMK\)\. If you copy an encrypted snapshot, the copy of the snapshot must also be encrypted\. If you copy an encrypted snapshot within the same AWS Region, you can encrypt the copy with the same AWS KMS CMK as the original snapshot, or you can specify a different AWS KMS CMK\. If you copy an encrypted snapshot across Regions, you can't use the same AWS KMS CMK for the copy as used for the source snapshot, because AWS KMS CMKs are Region\-specific\. Instead, you must specify a AWS KMS CMK valid in the destination AWS Region\.

The source snapshot remains encrypted throughout the copy process\. For more information, see [Limitations of Amazon RDS Encrypted DB Instances](Overview.Encryption.md#Overview.Encryption.Limitations)\.

You can also encrypt a copy of an unencrypted snapshot\. This way, you can quickly add encryption to a previously unencrypted DB instance\. That is, you can create a snapshot of your DB instance when you are ready to encrypt it, and then create a copy of that snapshot and specify a AWS KMS CMK to encrypt that snapshot copy\. You can then restore an encrypted DB instance from the encrypted snapshot\.

## Copying Snapshots Across AWS Regions<a name="USER_CopySnapshot.AcrossRegions"></a>

When you copy a snapshot to an AWS Region that is different from the source snapshot's AWS Region, the first copy is a full snapshot copy, even if you copy an incremental snapshot\. A full snapshot copy contains all of the data and metadata required to restore the DB instance\. After the first snapshot copy, you can copy incremental snapshots of the same DB instance to the same destination Region within the same AWS account\.

An incremental snapshot contains only the data that has changed after the most recent snapshot of the same DB instance\. Incremental snapshot copying is faster and results in lower storage costs than full snapshot copying\. Incremental snapshot copying across AWS Regions is supported for both unencrypted and encrypted snapshots\.

**Note**  
For shared snapshots, copying incremental snapshots across AWS Regions is only supported when they’re unencrypted\.

Depending on the AWS Regions involved and the amount of data to be copied, a cross\-Region snapshot copy can take hours to complete\. In some cases, there might be a large number of cross\-Region snapshot copy requests from a given source AWS Region\. In these cases, Amazon RDS might put new cross\-Region copy requests from that source AWS Region into a queue until some in\-progress copies complete\. No progress information is displayed about copy requests while they are in the queue\. Progress information is displayed when the copy starts\. 

Cross\-Region snapshot copy isn't supported in the following opt\-in AWS Regions:
+ Africa \(Cape Town\)
+ Asia Pacific \(Hong Kong\)
+ Europe \(Milan\)
+ Middle East \(Bahrain\)

**Note**  
When you copy a source snapshot that is a snapshot copy, the copy isn't incremental because the snapshot copy doesn't include the required metadata for incremental copies\.

## Option Group Considerations<a name="USER_CopySnapshot.Options"></a>

Option groups are specific to the AWS Region that they are created in, and you can't use an option group from one AWS Region in another AWS Region\. 

When you copy a snapshot across Regions, you can specify a new option group for the snapshot\. We recommend that you prepare the new option group before you copy the snapshot\. In the destination AWS Region, create an option group with the same settings as the original DB instance \. If one already exists in the new AWS Region, you can use that one\. 

If you copy a snapshot and you don't specify a new option group for the snapshot, when you restore it the DB instance gets the default option group\. To give the new DB instance the same options as the original, you must do the following: 

1. In the destination AWS Region, create an option group with the same settings as the original DB instance \. If one already exists in the new AWS Region, you can use that one\. 

1. After you restore the snapshot in the destination AWS Region, modify the new DB instance and add the new or existing option group from the previous step\. 

## Parameter Group Considerations<a name="USER_CopySnapshot.Parameters"></a>

When you copy a snapshot across Regions, the copy doesn't include the parameter group used by the original DB instance \. When you restore a snapshot to create a new DB instance , that DB instance gets the default parameter group for the AWS Region it is created in\. To give the new DB instance the same parameters as the original, you must do the following: 

1. In the destination AWS Region, create a DB parameter group with the same settings as the original DB instance \. If one already exists in the new AWS Region, you can use that one\. 

1. After you restore the snapshot in the destination AWS Region, modify the new DB instance and add the new or existing parameter group from the previous step\. 

## Copying a DB Snapshot<a name="USER_CopyDBSnapshot"></a>

Use the procedures in this topic to copy a DB snapshot\. For an overview of copying a snapshot, see [Copying a Snapshot](#USER_CopySnapshot) 

For each AWS account, you can copy up to five DB snapshots at a time from one AWS Region to another\. If you copy a DB snapshot to another AWS Region, you create a manual DB snapshot that is retained in that AWS Region\. Copying a DB snapshot out of the source AWS Region incurs Amazon RDS data transfer charges\. 

For more information about data transfer pricing, see [Amazon RDS Pricing](https://aws.amazon.com/rds/pricing/)\. 

After the DB snapshot copy has been created in the new AWS Region, the DB snapshot copy behaves the same as all other DB snapshots in that AWS Region\. 

You can copy a DB snapshot using the AWS Management Console, the AWS CLI, or the RDS API\.

### Console<a name="USER_CopySnapshot.CON"></a>

This procedure copies an encrypted or unencrypted DB snapshot, in the same AWS Region or across Regions, by using the AWS Management Console\. 

**To copy a DB snapshot**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Snapshots**\.

1. Select the DB snapshot that you want to copy\.

1. For **Actions**, choose **Copy Snapshot**\. The **Make Copy of DB Snapshot** page appears\.   
![\[Copy a DB snapshot\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/DBSnapshotCopy1.png)

1. \(Optional\) To copy the DB snapshot to a different AWS Region, for **Destination Region**, choose the new AWS Region\. 
**Note**  
The destination AWS Region must have the same database engine version available as the source AWS Region\. 

1. For **New DB Snapshot Identifier**, type the name of the DB snapshot copy\. 

1. \(Optional\) For **Target Option Group**, choose a new option group\. 

   Specify this option if you are copying a snapshot from one AWS Region to another, and your DB instance uses a non\-default option group\. 

   If your source DB instance uses Transparent Data Encryption for Oracle or Microsoft SQL Server, you must specify this option when copying across Regions\. For more information, see [Option Group Considerations](#USER_CopySnapshot.Options)\.

1. \(Optional\) Select **Copy Tags** to copy tags and values from the snapshot to the copy of the snapshot\. 

1. \(Optional\) For **Enable Encryption**, choose one of the following options: 
   + Choose **Disable encryption** if the DB snapshot isn't encrypted and you don't want to encrypt the copy\. 
   + Choose **Enable encryption** if the DB snapshot isn't encrypted but you want to encrypt the copy\. In this case, for **Master Key**, specify the AWS KMS key identifier to use to encrypt the DB snapshot copy\. 
   + Choose **Enable encryption** if the DB snapshot is encrypted\. In this case, you must encrypt the copy, so **Yes** is already selected\. For **Master Key**, specify the AWS KMS key identifier to use to encrypt the DB snapshot copy\. 

1. Choose **Copy Snapshot**\.

### AWS CLI<a name="USER_CopySnapshot.CLI"></a>

You can copy a DB snapshot by using the AWS CLI command [copy\-db\-snapshot](https://docs.aws.amazon.com/cli/latest/reference/rds/copy-db-snapshot.html)\. If you are copying the snapshot to a new AWS Region, run the command in the new AWS Region\. 

The following options are used to copy a DB snapshot\. Not all options are required for all scenarios\. Use the descriptions and the examples that follow to determine which options to use\. 
+ `--source-db-snapshot-identifier` – The identifier for the source DB snapshot\. 
  + If the source snapshot is in the same AWS Region as the copy, specify a valid DB snapshot identifier\. For example, `rds:mysql-instance1-snapshot-20130805`\. 
  + If the source snapshot is in a different AWS Region than the copy, specify a valid DB snapshot ARN\. For example, `arn:aws:rds:us-west-2:123456789012:snapshot:mysql-instance1-snapshot-20130805`\. 
  + If you are copying from a shared manual DB snapshot, this parameter must be the Amazon Resource Name \(ARN\) of the shared DB snapshot\. 
  + If you are copying an encrypted snapshot this parameter must be in the ARN format for the source AWS Region, and must match the `SourceDBSnapshotIdentifier` in the `PreSignedUrl` parameter\. 
+ `--target-db-snapshot-identifier` – The identifier for the new copy of the encrypted DB snapshot\. 
+ `--copy-tags` – Include the copy tags option to copy tags and values from the snapshot to the copy of the snapshot\. 
+ `--option-group-name` – The option group to associate with the copy of the snapshot\. 

  Specify this option if you are copying a snapshot from one AWS Region to another, and your DB instance uses a non\-default option group\. 

  If your source DB instance uses Transparent Data Encryption for Oracle or Microsoft SQL Server, you must specify this option when copying across Regions\. For more information, see [Option Group Considerations](#USER_CopySnapshot.Options)\.
+ `--kms-key-id` – The AWS KMS key identifier for an encrypted DB snapshot\. The AWS KMS key identifier is the Amazon Resource Name \(ARN\), key identifier, or key alias for the AWS KMS CMK\. 
  + If you copy an encrypted DB snapshot from your AWS account, you can specify a value for this parameter to encrypt the copy with a new AWS KMS CMK\. If you don't specify a value for this parameter, then the copy of the DB snapshot is encrypted with the same AWS KMS CMK as the source DB snapshot\. 
  + If you copy an encrypted DB snapshot that is shared from another AWS account, then you must specify a value for this parameter\. 
  + If you specify this parameter when you copy an unencrypted snapshot, the copy is encrypted\. 
  + If you copy an encrypted snapshot to a different AWS Region, then you must specify a AWS KMS CMK for the destination AWS Region\. AWS KMS CMKs are specific to the AWS Region that they are created in, and you cannot use encryption keys from one AWS Region in another AWS Region\. 
+ `--source-region` – The ID of the AWS Region of the source DB snapshot\. If you copy an encrypted snapshot to a different AWS Region, then you must specify this option\. 

**Example From Unencrypted, To Same Region**  
The following code creates a copy of a snapshot, with the new name `mydbsnapshotcopy`, in the same AWS Region as the source snapshot\. When the copy is made, all tags on the original snapshot are copied to the snapshot copy\.   
For Linux, macOS, or Unix:  

```
aws rds copy-db-snapshot \
    --source-db-snapshot-identifier mysql-instance1-snapshot-20130805 \
    --target-db-snapshot-identifier mydbsnapshotcopy \
    --copy-tags
```
For Windows:  

```
aws rds copy-db-snapshot ^
    --source-db-snapshot-identifier mysql-instance1-snapshot-20130805 ^
    --target-db-snapshot-identifier mydbsnapshotcopy ^
    --copy-tags
```

**Example From Unencrypted, Across Regions**  
The following code creates a copy of a snapshot, with the new name `mydbsnapshotcopy`, in the AWS Region in which the command is run\.   
For Linux, macOS, or Unix:  

```
aws rds copy-db-snapshot \
    --source-db-snapshot-identifier arn:aws:rds:us-east-1:123456789012:snapshot:mysql-instance1-snapshot-20130805 \
    --target-db-snapshot-identifier mydbsnapshotcopy
```
For Windows:  

```
aws rds copy-db-snapshot ^
    --source-db-snapshot-identifier arn:aws:rds:us-east-1:123456789012:snapshot:mysql-instance1-snapshot-20130805 ^
    --target-db-snapshot-identifier mydbsnapshotcopy
```

**Example From Encrypted, Across Regions**  
The following code example copies an encrypted DB snapshot from the us\-west\-2 Region in the us\-east\-1 Region\. Run the command in the us\-east\-1 Region\.   
For Linux, macOS, or Unix:  

```
aws rds copy-db-snapshot \
	--source-db-snapshot-identifier arn:aws:rds:us-west-2:123456789012:snapshot:mysql-instance1-snapshot-20161115 \
	--target-db-snapshot-identifier mydbsnapshotcopy \
	--source-region us-west-2 \	
	--kms-key-id my-us-east-1-key \ 
    --option-group-name	custom-option-group-name
```
For Windows:  

```
aws rds copy-db-snapshot ^
	--source-db-snapshot-identifier arn:aws:rds:us-west-2:123456789012:snapshot:mysql-instance1-snapshot-20161115 ^
	--target-db-snapshot-identifier mydbsnapshotcopy ^
	--source-region us-west-2 ^	
	--kms-key-id my-us-east-1-key ^
    --option-group-name	custom-option-group-name
```

### RDS API<a name="USER_CopySnapshot.API"></a>

You can copy a DB snapshot by using the Amazon RDS API operation [CopyDBSnapshot](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CopyDBSnapshot.html)\. If you are copying the snapshot to a new AWS Region, perform the action in the new AWS Region\. 

The following parameters are used to copy a DB snapshot\. Not all parameters are required for all scenarios\. Use the descriptions and the examples that follow to determine which parameters to use\. 
+ `SourceDBSnapshotIdentifier` – The identifier for the source DB snapshot\. 
  + If the source snapshot is in the same AWS Region as the copy, specify a valid DB snapshot identifier\. For example, `rds:mysql-instance1-snapshot-20130805`\. 
  + If the source snapshot is in a different AWS Region than the copy, specify a valid DB snapshot ARN\. For example, `arn:aws:rds:us-west-2:123456789012:snapshot:mysql-instance1-snapshot-20130805`\. 
  + If you are copying from a shared manual DB snapshot, this parameter must be the Amazon Resource Name \(ARN\) of the shared DB snapshot\. 
  + If you are copying an encrypted snapshot this parameter must be in the ARN format for the source AWS Region, and must match the `SourceDBSnapshotIdentifier` in the `PreSignedUrl` parameter\. 
+ `TargetDBSnapshotIdentifier` – The identifier for the new copy of the encrypted DB snapshot\. 
+ `CopyTags` – Set this parameter to `true` to copy tags and values from the snapshot to the copy of the snapshot\. The default is `false`\. 
+ `OptionGroupName` – The option group to associate with the copy of the snapshot\. 

  Specify this parameter if you are copying a snapshot from one AWS Region to another, and your DB instance uses a non\-default option group\. 

  If your source DB instance uses Transparent Data Encryption for Oracle or Microsoft SQL Server, you must specify this parameter when copying across Regions\. For more information, see [Option Group Considerations](#USER_CopySnapshot.Options)\.
+ `KmsKeyId` – The AWS KMS key identifier for an encrypted DB snapshot\. The AWS KMS key identifier is the Amazon Resource Name \(ARN\), key identifier, or key alias for the AWS KMS CMK\. 
  + If you copy an encrypted DB snapshot from your AWS account, you can specify a value for this parameter to encrypt the copy with a new AWS KMS CMK\. If you don't specify a value for this parameter, then the copy of the DB snapshot is encrypted with the same AWS KMS CMK as the source DB snapshot\. 
  + If you copy an encrypted DB snapshot that is shared from another AWS account, then you must specify a value for this parameter\. 
  + If you specify this parameter when you copy an unencrypted snapshot, the copy is encrypted\. 
  + If you copy an encrypted snapshot to a different AWS Region, then you must specify a AWS KMS CMK for the destination AWS Region\. AWS KMS CMKs are specific to the AWS Region that they are created in, and you cannot use encryption keys from one AWS Region in another AWS Region\. 
+ `PreSignedUrl` – The URL that contains a Signature Version 4 signed request for the `CopyDBSnapshot` API operation in the source AWS Region that contains the source DB snapshot to copy\. 

  You must specify this parameter when you copy an encrypted DB snapshot from another AWS Region by using the Amazon RDS API\. You can specify the source Region option instead of this parameter when you copy an encrypted DB snapshot from another AWS Region by using the AWS CLI\. 

  The presigned URL must be a valid request for the `CopyDBSnapshot` API operation that can be run in the source AWS Region containing the encrypted DB snapshot to be copied\. The presigned URL request must contain the following parameter values: 
  + `DestinationRegion` \- The AWS Region that the encrypted DB snapshot will be copied to\. This AWS Region is the same one where the `CopyDBSnapshot` action is called that contains this presigned URL\. 

    For example, if you copy an encrypted DB snapshot from the us\-west\-2 Region to the us\-east\-1 Region, then you call the `CopyDBSnapshot` action in the us\-east\-1 Region and provide a presigned URL that contains a call to the `CopyDBSnapshot` action in the us\-west\-2 Region\. For this example, the `DestinationRegion` in the presigned URL must be set to the us\-east\-1 Region\. 
  + `KmsKeyId` \- The AWS KMS key identifier for the key to use to encrypt the copy of the DB snapshot in the destination AWS Region\. This is the same identifier for both the `CopyDBSnapshot` action that is called in the destination AWS Region, and the action contained in the presigned URL\. 
  + `SourceDBSnapshotIdentifier` \- The DB snapshot identifier for the encrypted snapshot to be copied\. This identifier must be in the Amazon Resource Name \(ARN\) format for the source AWS Region\. For example, if you are copying an encrypted DB snapshot from the us\-west\-2 Region, then your `SourceDBSnapshotIdentifier` looks like the following example: `arn:aws:rds:us-west-2:123456789012:snapshot:mysql-instance1-snapshot-20161115`\. 

  For more information on Signature Version 4 signed requests, see the following:
  + [Authenticating Requests: Using Query Parameters \(AWS Signature Version 4\)](https://docs.aws.amazon.com/AmazonS3/latest/API/sigv4-query-string-auth.html) in the Amazon Simple Storage Service API Reference
  + [Signature Version 4 Signing Process](https://docs.aws.amazon.com/general/latest/gr/signature-version-4.html) in the AWS General Reference

**Example From Unencrypted, To Same Region**  
The following code creates a copy of a snapshot, with the new name `mydbsnapshotcopy`, in the same AWS Region as the source snapshot\. When the copy is made, all tags on the original snapshot are copied to the snapshot copy\.   

```
https://rds.us-west-1.amazonaws.com/
	?Action=CopyDBSnapshot
	&CopyTags=true
	&SignatureMethod=HmacSHA256
	&SignatureVersion=4
	&SourceDBSnapshotIdentifier=mysql-instance1-snapshot-20130805
	&TargetDBSnapshotIdentifier=mydbsnapshotcopy
	&Version=2013-09-09
	&X-Amz-Algorithm=AWS4-HMAC-SHA256
	&X-Amz-Credential=AKIADQKE4SARGYLE/20140429/us-west-1/rds/aws4_request
	&X-Amz-Date=20140429T175351Z
	&X-Amz-SignedHeaders=content-type;host;user-agent;x-amz-content-sha256;x-amz-date
	&X-Amz-Signature=9164337efa99caf850e874a1cb7ef62f3cea29d0b448b9e0e7c53b288ddffed2
```

**Example From Unencrypted, Across Regions**  
The following code creates a copy of a snapshot, with the new name `mydbsnapshotcopy`, in the us\-west\-1 Region\.   

```
https://rds.us-west-1.amazonaws.com/
	?Action=CopyDBSnapshot
	&SignatureMethod=HmacSHA256
	&SignatureVersion=4
	&SourceDBSnapshotIdentifier=arn%3Aaws%3Ards%3Aus-east-1%3A123456789012%3Asnapshot%3Amysql-instance1-snapshot-20130805
	&TargetDBSnapshotIdentifier=mydbsnapshotcopy
	&Version=2013-09-09
	&X-Amz-Algorithm=AWS4-HMAC-SHA256
	&X-Amz-Credential=AKIADQKE4SARGYLE/20140429/us-west-1/rds/aws4_request
	&X-Amz-Date=20140429T175351Z
	&X-Amz-SignedHeaders=content-type;host;user-agent;x-amz-content-sha256;x-amz-date
	&X-Amz-Signature=9164337efa99caf850e874a1cb7ef62f3cea29d0b448b9e0e7c53b288ddffed2
```

**Example From Encrypted, Across Regions**  
The following code creates a copy of a snapshot, with the new name `mydbsnapshotcopy`, in the us\-east\-1 Region\.   

```
https://rds.us-east-1.amazonaws.com/
    ?Action=CopyDBSnapshot
    &KmsKeyId=my-us-east-1-key
    &OptionGroupName=custom-option-group-name
    &PreSignedUrl=https%253A%252F%252Frds.us-west-2.amazonaws.com%252F
         %253FAction%253DCopyDBSnapshot
         %2526DestinationRegion%253Dus-east-1
         %2526KmsKeyId%253Dmy-us-east-1-key
         %2526SourceDBSnapshotIdentifier%253Darn%25253Aaws%25253Ards%25253Aus-west-2%25253A123456789012%25253Asnapshot%25253Amysql-instance1-snapshot-20161115
         %2526SignatureMethod%253DHmacSHA256
         %2526SignatureVersion%253D4
         %2526Version%253D2014-10-31
         %2526X-Amz-Algorithm%253DAWS4-HMAC-SHA256
         %2526X-Amz-Credential%253DAKIADQKE4SARGYLE%252F20161117%252Fus-west-2%252Frds%252Faws4_request
         %2526X-Amz-Date%253D20161117T215409Z
         %2526X-Amz-Expires%253D3600
         %2526X-Amz-SignedHeaders%253Dcontent-type%253Bhost%253Buser-agent%253Bx-amz-content-sha256%253Bx-amz-date
         %2526X-Amz-Signature%253D255a0f17b4e717d3b67fad163c3ec26573b882c03a65523522cf890a67fca613
    &SignatureMethod=HmacSHA256
    &SignatureVersion=4
    &SourceDBSnapshotIdentifier=arn%3Aaws%3Ards%3Aus-west-2%3A123456789012%3Asnapshot%3Amysql-instance1-snapshot-20161115
    &TargetDBSnapshotIdentifier=mydbsnapshotcopy
    &Version=2014-10-31
    &X-Amz-Algorithm=AWS4-HMAC-SHA256
    &X-Amz-Credential=AKIADQKE4SARGYLE/20161117/us-east-1/rds/aws4_request
    &X-Amz-Date=20161117T221704Z
    &X-Amz-SignedHeaders=content-type;host;user-agent;x-amz-content-sha256;x-amz-date
    &X-Amz-Signature=da4f2da66739d2e722c85fcfd225dc27bba7e2b8dbea8d8612434378e52adccf
```