# Copying a DB Snapshot<a name="USER_CopyDBSnapshot"></a>

Use the procedures in this topic to copy a DB snapshot\. For an overview of copying a snapshot, see [Copying a DB Snapshot or DB Cluster Snapshot](USER_CopySnapshot.md) 

If your source database engine is MariaDB, Microsoft SQL Server, MySQL, Oracle, or PostgreSQL, then your snapshot is a DB snapshot\. If your source database engine is Aurora, then your snapshot is a DB cluster snapshot\. For instructions on how to copy a DB cluster snapshot, see [Copying a DB Cluster Snapshot](USER_CopyDBClusterSnapshot.CrossRegion.md)\. 

For each AWS account, you can copy up to five DB snapshots at a time from one AWS Region to another\. If you copy a DB snapshot to another AWS Region, you create a manual DB snapshot that is retained in that AWS Region\. Copying a DB snapshot out of the source AWS Region incurs Amazon RDS data transfer charges\. 

For more information about data transfer pricing, see [Amazon RDS Pricing](https://aws.amazon.com/rds/pricing/)\. 

After the DB snapshot copy has been created in the new AWS Region, the DB snapshot copy behaves the same as all other DB snapshots in that AWS Region\. 

## AWS Management Console<a name="USER_CopySnapshot.CON"></a>

This procedure copies an encrypted or unencrypted DB snapshot, in the same AWS Region or across regions, by using the AWS Management Console\. 

**To copy a DB snapshot**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Snapshots**\.

1. Select the DB snapshot that you want to copy\.

1. Choose **Snapshot Actions**, and then choose **Copy Snapshot**\. The **Make Copy of DB Snapshot** page appears\.   
![\[Copy a DB snapshot\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/DBSnapshotCopy1.png)

1. \(Optional\) To copy the DB snapshot to a different AWS Region, for **Destination Region**, choose the new AWS Region\. 
**Note**  
The destination AWS Region must have the same database engine version available as the source AWS Region\. 

1. For **New DB Snapshot Identifier**, type the name of the DB snapshot copy\. 

1. \(Optional\) For **Target Option Group**, choose a new option group\. 

   Specify this option if you are copying a snapshot from one AWS Region to another, and your DB instance uses a non\-default option group\. If your source DB instance uses Transparent Data Encryption for Oracle or Microsoft SQL Server, you must specify this option when copying across regions\. For more information, see [Option Group Considerations](USER_CopySnapshot.md#USER_CopySnapshot.Options)\. 

1. \(Optional\) Select **Copy Tags** to copy tags and values from the snapshot to the copy of the snapshot\. 

1. \(Optional\) For **Enable Encryption**, choose one of the following options: 
   + Choose **No** if the DB snapshot isn't encrypted and you don't want to encrypt the copy\. 
   + Choose **Yes** if the DB snapshot isn't encrypted but you want to encrypt the copy\. In this case, for **Master Key**, specify the KMS key identifier to use to encrypt the DB snapshot copy\. 
   + Choose **Yes** if the DB snapshot is encrypted\. In this case, you must encrypt the copy, so **Yes** is already selected\. For **Master Key**, specify the KMS key identifier to use to encrypt the DB snapshot copy\. 

1. Choose **Copy Snapshot**\.

## CLI<a name="USER_CopySnapshot.CLI"></a>

You can copy a DB snapshot by using the AWS CLI command [copy\-db\-snapshot](http://docs.aws.amazon.com/cli/latest/reference/rds/copy-db-snapshot.html)\. If you are copying the snapshot to a new AWS Region, run the command in the new AWS Region\. 

The following options are used to copy a DB snapshot\. Not all options are required for all scenarios\. Use the descriptions and the examples that follow to determine which options to use\. 
+ `--source-db-snapshot-identifier` – The identifier for the source DB snapshot\. 
  + If the source snapshot is in the same AWS Region as the copy, specify a valid DB snapshot identifier\. For example, `rds:mysql-instance1-snapshot-20130805`\. 
  + If the source snapshot is in a different AWS Region than the copy, specify a valid DB snapshot ARN\. For example, `arn:aws:rds:us-west-2:123456789012:snapshot:mysql-instance1-snapshot-20130805`\. 
  + If you are copying from a shared manual DB snapshot, this parameter must be the Amazon Resource Name \(ARN\) of the shared DB snapshot\. 
  + If you are copying an encrypted snapshot this parameter must be in the ARN format for the source AWS Region, and must match the `SourceDBSnapshotIdentifier` in the `PreSignedUrl` parameter\. 
+ `--target-db-snapshot-identifier` – The identifier for the new copy of the encrypted DB snapshot\. 
+ `--copy-tags` – Include the copy tags option to copy tags and values from the snapshot to the copy of the snapshot\. 
+ `--option-group-name` – The option group to associate with the copy of the snapshot\. 

  Specify this option if you are copying a snapshot from one AWS Region to another, and your DB instance uses a non\-default option group\. If your source DB instance uses Transparent Data Encryption for Oracle or Microsoft SQL Server, you must specify this option when copying across regions\. For more information, see [Option Group Considerations](USER_CopySnapshot.md#USER_CopySnapshot.Options)\. 
+ `--kms-key-id` – The AWS KMS key ID for an encrypted DB snapshot\. The KMS key ID is the Amazon Resource Name \(ARN\), KMS key identifier, or the KMS key alias for the KMS encryption key\. 
  + If you copy an encrypted DB snapshot from your AWS account, you can specify a value for this parameter to encrypt the copy with a new KMS encryption key\. If you don't specify a value for this parameter, then the copy of the DB snapshot is encrypted with the same KMS key as the source DB snapshot\. 
  + If you copy an encrypted DB snapshot that is shared from another AWS account, then you must specify a value for this parameter\. 
  + If you specify this parameter when you copy an unencrypted snapshot, the copy is encrypted\. 
  + If you copy an encrypted snapshot to a different AWS Region, then you must specify a KMS key for the destination AWS Region\. KMS encryption keys are specific to the AWS Region that they are created in, and you cannot use encryption keys from one AWS Region in another AWS Region\. 
+ `--source-region` – The ID of the AWS Region of the source DB snapshot\. If you copy an encrypted snapshot to a different AWS Region, then you must specify this option\. 

**Example From Unencrypted, To Same Region**  
The following code creates a copy of a snapshot, with the new name `mydbsnapshotcopy`, in the same AWS Region as the source snapshot\. When the copy is made, all tags on the original snapshot are copied to the snapshot copy\.   
For Linux, OS X, or Unix:  

```
1. aws rds copy-db-snapshot \
2.     --source-db-snapshot-identifier mysql-instance1-snapshot-20130805 \
3.     --target-db-snapshot-identifier mydbsnapshotcopy \
4.     --copy-tags
```
For Windows:  

```
1. aws rds copy-db-snapshot ^
2.     --source-db-snapshot-identifier mysql-instance1-snapshot-20130805 ^
3.     --target-db-snapshot-identifier mydbsnapshotcopy ^
4.     --copy-tags
```

**Example From Unencrypted, Across Regions**  
The following code creates a copy of a snapshot, with the new name `mydbsnapshotcopy`, in the AWS Region in which the command is run\.   
For Linux, OS X, or Unix:  

```
1. aws rds copy-db-snapshot \
2.     --source-db-snapshot-identifier arn:aws:rds:us-east-1:123456789012:snapshot:mysql-instance1-snapshot-20130805 \
3.     --target-db-snapshot-identifier mydbsnapshotcopy
```
For Windows:  

```
1. aws rds copy-db-snapshot ^
2.     --source-db-snapshot-identifier arn:aws:rds:us-east-1:123456789012:snapshot:mysql-instance1-snapshot-20130805 ^
3.     --target-db-snapshot-identifier mydbsnapshotcopy
```

**Example From Encrypted, Across Regions**  
The following code example copies an encrypted DB snapshot from the us\-west\-2 region in the us\-east\-1 region\. Run the command in the us\-east\-1 region\.   
For Linux, OS X, or Unix:  

```
1. aws rds copy-db-snapshot \
2. 	--source-db-snapshot-identifier arn:aws:rds:us-west-2:123456789012:snapshot:mysql-instance1-snapshot-20161115 \
3. 	--target-db-snapshot-identifier mydbsnapshotcopy \
4. 	--source-region us-west-2 \	
5. 	--kms-key-id my-us-east-1-key \ 
6.     --option-group-name	custom-option-group-name
```
For Windows:  

```
1. aws rds copy-db-snapshot ^
2. 	--source-db-snapshot-identifier arn:aws:rds:us-west-2:123456789012:snapshot:mysql-instance1-snapshot-20161115 ^
3. 	--target-db-snapshot-identifier mydbsnapshotcopy ^
4. 	--source-region us-west-2 ^	
5. 	--kms-key-id my-us-east-1-key ^
6.     --option-group-name	custom-option-group-name
```

## API<a name="USER_CopySnapshot.API"></a>

You can copy a DB snapshot by using the Amazon RDS API action [CopyDBSnapshot](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CopyDBSnapshot.html)\. If you are copying the snapshot to a new AWS Region, perform the action in the new AWS Region\. 

The following parameters are used to copy a DB snapshot\. Not all parameters are required for all scenarios\. Use the descriptions and the examples that follow to determine which parameters to use\. 
+ `SourceDBSnapshotIdentifier` – The identifier for the source DB snapshot\. 
  + If the source snapshot is in the same AWS Region as the copy, specify a valid DB snapshot identifier\. For example, `rds:mysql-instance1-snapshot-20130805`\. 
  + If the source snapshot is in a different AWS Region than the copy, specify a valid DB snapshot ARN\. For example, `arn:aws:rds:us-west-2:123456789012:snapshot:mysql-instance1-snapshot-20130805`\. 
  + If you are copying from a shared manual DB snapshot, this parameter must be the Amazon Resource Name \(ARN\) of the shared DB snapshot\. 
  + If you are copying an encrypted snapshot this parameter must be in the ARN format for the source AWS Region, and must match the `SourceDBSnapshotIdentifier` in the `PreSignedUrl` parameter\. 
+ `TargetDBSnapshotIdentifier` – The identifier for the new copy of the encrypted DB snapshot\. 
+ `CopyTags` – Set this parameter to `true` to copy tags and values from the snapshot to the copy of the snapshot\. The default is `false`\. 
+ `OptionGroupName` – The option group to associate with the copy of the snapshot\. 

  Specify this parametr if you are copying a snapshot from one AWS Region to another, and your DB instance uses a non\-default option group\. If your source DB instance uses Transparent Data Encryption for Oracle or Microsoft SQL Server, you must specify this parameter when copying across regions\. For more information, see [Option Group Considerations](USER_CopySnapshot.md#USER_CopySnapshot.Options)\. 
+ `KmsKeyId` – The AWS KMS key ID for an encrypted DB snapshot\. The KMS key ID is the Amazon Resource Name \(ARN\), KMS key identifier, or the KMS key alias for the KMS encryption key\. 
  + If you copy an encrypted DB snapshot from your AWS account, you can specify a value for this parameter to encrypt the copy with a new KMS encryption key\. If you don't specify a value for this parameter, then the copy of the DB snapshot is encrypted with the same KMS key as the source DB snapshot\. 
  + If you copy an encrypted DB snapshot that is shared from another AWS account, then you must specify a value for this parameter\. 
  + If you specify this parameter when you copy an unencrypted snapshot, the copy is encrypted\. 
  + If you copy an encrypted snapshot to a different AWS Region, then you must specify a KMS key for the destination AWS Region\. KMS encryption keys are specific to the AWS Region that they are created in, and you cannot use encryption keys from one AWS Region in another AWS Region\. 
+ `PreSignedUrl` – The URL that contains a Signature Version 4 signed request for the `CopyDBSnapshot` API action in the source AWS Region that contains the source DB snapshot to copy\. 

  You must specify this parameter when you copy an encrypted DB snapshot from another AWS Region by using the Amazon RDS API\. You can specify the source region option instead of this parameter when you copy an encrypted DB snapshot from another AWS Region by using the AWS CLI\. 

  The presigned URL must be a valid request for the `CopyDBSnapshot` API action that can be executed in the source AWS Region that contains the encrypted DB snapshot to be copied\. The presigned URL request must contain the following parameter values: 
  + `DestinationRegion` \- The AWS Region that the encrypted DB snapshot will be copied to\. This AWS Region is the same one where the `CopyDBSnapshot` action is called that contains this presigned URL\. 

    For example, if you copy an encrypted DB snapshot from the us\-west\-2 region to the us\-east\-1 region, then you call the `CopyDBSnapshot` action in the us\-east\-1 region and provide a presigned URL that contains a call to the `CopyDBSnapshot` action in the us\-west\-2 region\. For this example, the `DestinationRegion` in the presigned URL must be set to the us\-east\-1 region\. 
  + `KmsKeyId` \- The KMS key identifier for the key to use to encrypt the copy of the DB snapshot in the destination AWS Region\. This is the same identifier for both the `CopyDBSnapshot` action that is called in the destination AWS Region, and the action contained in the presigned URL\. 
  + `SourceDBSnapshotIdentifier` \- The DB snapshot identifier for the encrypted snapshot to be copied\. This identifier must be in the Amazon Resource Name \(ARN\) format for the source AWS Region\. For example, if you are copying an encrypted DB snapshot from the us\-west\-2 region, then your `SourceDBSnapshotIdentifier` looks like the following example: `arn:aws:rds:us-west-2:123456789012:snapshot:mysql-instance1-snapshot-20161115`\. 

  For more information on Signature Version 4 signed requests, see the following:
  + [Authenticating Requests: Using Query Parameters \(AWS Signature Version 4\)](http://docs.aws.amazon.com/AmazonS3/latest/API/sigv4-query-string-auth.html) in the Amazon Simple Storage Service API Reference
  + [Signature Version 4 Signing Process](http://docs.aws.amazon.com/general/latest/gr/signature-version-4.html) in the AWS General Reference

**Example From Unencrypted, To Same Region**  
The following code creates a copy of a snapshot, with the new name `mydbsnapshotcopy`, in the same AWS Region as the source snapshot\. When the copy is made, all tags on the original snapshot are copied to the snapshot copy\.   

```
 1. https://rds.us-west-1.amazonaws.com/
 2. 	?Action=CopyDBSnapshot
 3. 	&CopyTags=true
 4. 	&SignatureMethod=HmacSHA256
 5. 	&SignatureVersion=4
 6. 	&SourceDBSnapshotIdentifier=mysql-instance1-snapshot-20130805
 7. 	&TargetDBSnapshotIdentifier=mydbsnapshotcopy
 8. 	&Version=2013-09-09
 9. 	&X-Amz-Algorithm=AWS4-HMAC-SHA256
10. 	&X-Amz-Credential=AKIADQKE4SARGYLE/20140429/us-west-1/rds/aws4_request
11. 	&X-Amz-Date=20140429T175351Z
12. 	&X-Amz-SignedHeaders=content-type;host;user-agent;x-amz-content-sha256;x-amz-date
13. 	&X-Amz-Signature=9164337efa99caf850e874a1cb7ef62f3cea29d0b448b9e0e7c53b288ddffed2
```

**Example From Unencrypted, Across Regions**  
The following code creates a copy of a snapshot, with the new name `mydbsnapshotcopy`, in the us\-west\-1 region\.   

```
 1. https://rds.us-west-1.amazonaws.com/
 2. 	?Action=CopyDBSnapshot
 3. 	&SignatureMethod=HmacSHA256
 4. 	&SignatureVersion=4
 5. 	&SourceDBSnapshotIdentifier=arn%3Aaws%3Ards%3Aus-east-1%3A123456789012%3Asnapshot%3Amysql-instance1-snapshot-20130805
 6. 	&TargetDBSnapshotIdentifier=mydbsnapshotcopy
 7. 	&Version=2013-09-09
 8. 	&X-Amz-Algorithm=AWS4-HMAC-SHA256
 9. 	&X-Amz-Credential=AKIADQKE4SARGYLE/20140429/us-west-1/rds/aws4_request
10. 	&X-Amz-Date=20140429T175351Z
11. 	&X-Amz-SignedHeaders=content-type;host;user-agent;x-amz-content-sha256;x-amz-date
12. 	&X-Amz-Signature=9164337efa99caf850e874a1cb7ef62f3cea29d0b448b9e0e7c53b288ddffed2
```

**Example From Encrypted, Across Regions**  
The following code creates a copy of a snapshot, with the new name `mydbsnapshotcopy`, in the us\-east\-1 region\.   

```
 1. https://rds.us-east-1.amazonaws.com/
 2.     ?Action=CopyDBSnapshot
 3.     &KmsKeyId=my-us-east-1-key
 4.     &OptionGroupName=custom-option-group-name
 5.     &PreSignedUrl=https%253A%252F%252Frds.us-west-2.amazonaws.com%252F
 6.          %253FAction%253DCopyDBSnapshot
 7.          %2526DestinationRegion%253Dus-east-1
 8.          %2526KmsKeyId%253Dmy-us-east-1-key
 9.          %2526SourceDBSnapshotIdentifier%253Darn%25253Aaws%25253Ards%25253Aus-west-2%25253A123456789012%25253Asnapshot%25253Amysql-instance1-snapshot-20161115
10.          %2526SignatureMethod%253DHmacSHA256
11.          %2526SignatureVersion%253D4
12.          %2526Version%253D2014-10-31
13.          %2526X-Amz-Algorithm%253DAWS4-HMAC-SHA256
14.          %2526X-Amz-Credential%253DAKIADQKE4SARGYLE%252F20161117%252Fus-west-2%252Frds%252Faws4_request
15.          %2526X-Amz-Date%253D20161117T215409Z
16.          %2526X-Amz-Expires%253D3600
17.          %2526X-Amz-SignedHeaders%253Dcontent-type%253Bhost%253Buser-agent%253Bx-amz-content-sha256%253Bx-amz-date
18.          %2526X-Amz-Signature%253D255a0f17b4e717d3b67fad163c3ec26573b882c03a65523522cf890a67fca613
19.     &SignatureMethod=HmacSHA256
20.     &SignatureVersion=4
21.     &SourceDBSnapshotIdentifier=arn%3Aaws%3Ards%3Aus-west-2%3A123456789012%3Asnapshot%3Amysql-instance1-snapshot-20161115
22.     &TargetDBSnapshotIdentifier=mydbsnapshotcopy
23.     &Version=2014-10-31
24.     &X-Amz-Algorithm=AWS4-HMAC-SHA256
25.     &X-Amz-Credential=AKIADQKE4SARGYLE/20161117/us-east-1/rds/aws4_request
26.     &X-Amz-Date=20161117T221704Z
27.     &X-Amz-SignedHeaders=content-type;host;user-agent;x-amz-content-sha256;x-amz-date
28.     &X-Amz-Signature=da4f2da66739d2e722c85fcfd225dc27bba7e2b8dbea8d8612434378e52adccf
```