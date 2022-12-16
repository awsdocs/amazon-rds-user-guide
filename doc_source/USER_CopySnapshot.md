# Copying a DB snapshot<a name="USER_CopySnapshot"></a>

With Amazon RDS, you can copy automated backups or manual DB snapshots\. After you copy a snapshot, the copy is a manual snapshot\. You can make multiple copies of an automated backup or manual snapshot, but each copy must have a unique identifier\.

You can copy a snapshot within the same AWS Region, you can copy a snapshot across AWS Regions, and you can copy shared snapshots\.

## Limitations<a name="USER_CopySnapshot.Limitations"></a>

The following are some limitations when you copy snapshots: 
+ You can't copy a snapshot to or from the China \(Beijing\) Region or the China \(Ningxia\) Region\.
+  You can copy a snapshot between AWS GovCloud \(US\-East\) and AWS GovCloud \(US\-West\)\. However, you can't copy a snapshot between these GovCloud \(US\) Regions and Regions that aren't GovCloud \(US\) Regions\.
+ If you delete a source snapshot before the target snapshot becomes available, the snapshot copy might fail\. Verify that the target snapshot has a status of `AVAILABLE` before you delete a source snapshot\.
+ You can have up to 20 snapshot copy requests in progress to a single destination Region per account\.
+ When you request multiple snapshot copies for the same source DB instance, they're queued internally\. The copies requested later won't start until the previous snapshot copies are completed\. For more information, see [ Why is my EC2 AMI or EBS snapshot creation slow?](https://aws.amazon.com/premiumsupport/knowledge-center/ebs-snapshot-ec2-ami-creation-slow/) in the AWS Knowledge Center\.
+ Depending on the AWS Regions involved and the amount of data to be copied, a cross\-Region snapshot copy can take hours to complete\. In some cases, there might be a large number of cross\-Region snapshot copy requests from a given source Region\. In such cases, Amazon RDS might put new cross\-Region copy requests from that source Region into a queue until some in\-progress copies complete\. No progress information is displayed about copy requests while they are in the queue\. Progress information is displayed when the copy starts\.

## Snapshot retention<a name="USER_CopySnapshot.Retention"></a>

Amazon RDS deletes automated backups in several situations:
+ At the end of their retention period\.
+ When you disable automated backups for a DB instance\.
+ When you delete a DB instance\.

If you want to keep an automated backup for a longer period, copy it to create a manual snapshot, which is retained until you delete it\. Amazon RDS storage costs might apply to manual snapshots if they exceed your default storage space\.

For more information about backup storage costs, see [Amazon RDS pricing](https://aws.amazon.com/rds/pricing/)\. 

## Copying shared snapshots<a name="USER_CopySnapshot.Shared"></a>

You can copy snapshots shared to you by other AWS accounts\. In some cases, you might copy an encrypted snapshot that has been shared from another AWS account\. In these cases, you must have access to the AWS KMS key that was used to encrypt the snapshot\. 

You can copy a shared DB snapshot across AWS Regions if the snapshot is unencrypted\. However, if the shared DB snapshot is encrypted, you can only copy it in the same Region\.

**Note**  
Copying shared incremental snapshots in the same AWS Region is supported when they're unencrypted, or encrypted using the same KMS key as the initial full snapshot\. If you use a different KMS key to encrypt subsequent snapshots when copying them, those shared snapshots are full snapshots\. For more information, see [Incremental snapshot copying](#USER_CopySnapshot.Incremental)\.

## Handling encryption<a name="USER_CopySnapshot.Encryption"></a>

You can copy a snapshot that has been encrypted using a KMS key\. If you copy an encrypted snapshot, the copy of the snapshot must also be encrypted\. If you copy an encrypted snapshot within the same AWS Region, you can encrypt the copy with the same KMS key as the original snapshot\. Or you can specify a different KMS key\.

If you copy an encrypted snapshot across Regions, you must specify a KMS key valid in the destination AWS Region\. It can be a Region\-specific KMS key, or a multi\-Region key\. For more information on multi\-Region KMS keys, see [Using multi\-Region keys in AWS KMS](https://docs.aws.amazon.com/kms/latest/developerguide/multi-region-keys-overview.html)\.

The source snapshot remains encrypted throughout the copy process\. For more information, see [Limitations of Amazon RDS encrypted DB instances](Overview.Encryption.md#Overview.Encryption.Limitations)\.

You can also encrypt a copy of an unencrypted snapshot\. This way, you can quickly add encryption to a previously unencrypted DB instance\. To do this, you create a snapshot of your DB instance when you are ready to encrypt it\. You then create a copy of that snapshot and specify a KMS key to encrypt that snapshot copy\. You can then restore an encrypted DB instance from the encrypted snapshot\.

## Incremental snapshot copying<a name="USER_CopySnapshot.Incremental"></a>

An *incremental* snapshot contains only the data that has changed after the most recent snapshot of the same DB instance\. Incremental snapshot copying is faster and results in lower storage costs than full snapshot copying\.

**Note**  
When you copy a source snapshot that is a snapshot copy itself, the new copy isn't incremental\. This is because the source snapshot copy doesn't include the required metadata for incremental copies\.

Whether a snapshot copy is incremental is determined by the most recently completed snapshot copy\. If the most recent snapshot copy was deleted, the next copy is a full copy, not an incremental copy\.

If a copy is still pending when you start another copy, the second copy doesn't start until the first copy finishes\.

When you copy a snapshot across AWS accounts, the copy is an incremental copy if the following conditions are met:
+ A different snapshot of the same source DB instance was previously copied to the destination account\.
+ The most recent snapshot copy still exists in the destination account\.
+ All copies of the snapshot in the destination account are either unencrypted, or were encrypted using the same KMS key\.

The following examples illustrate the difference between full and incremental snapshots\. They apply to both shared and unshared snapshots\.


| Snapshot | Encryption key | Full or incremental | 
| --- | --- | --- | 
| S1 | K1 | Full | 
| S2 | K1 | Incremental of S1 | 
| S3 | K1 | Incremental of S2 | 
| S4 | K1 | Incremental of S3 | 
| Copy of S1 \(S1C\) | K2 | Full | 
| Copy of S2 \(S2C\) | K3 | Full | 
| Copy of S3 \(S3C\) | K3 | Incremental of S2C | 
| Copy of S4 \(S4C\) | K3 | Incremental of S3C | 
| Copy 2 of S4 \(S4C2\) | K4 | Full | 

**Note**  
In these examples, snapshots S2, S3, and S4 are incremental only if the previous snapshot still exists\.  
The same applies to copies\. Snapshot copies S3C and S4C are incremental only if the previous copy still exists\.

For information on copying incremental snapshots across AWS Regions, see [Full and incremental copies](#USER_CopySnapshot.AcrossRegions.Full)\.

## Cross\-Region snapshot copying<a name="USER_CopySnapshot.AcrossRegions"></a>

You can copy DB snapshots across AWS Regions\. However, there are certain constraints and considerations for cross\-Region snapshot copying\.

### Requesting a cross\-Region DB snapshot copy<a name="USER_CopySnapshot.AcrossRegions.Policy"></a>

To communicate with the source Region to request a cross\-Region DB snapshot copy, the requester \(IAM role or IAM user\) must have access to the source DB snapshot and the source Region\. 

Certain conditions in the requester's IAM policy can cause the request to fail\. The following examples assume that you're copying the DB snapshot from US East \(Ohio\) to US East \(N\. Virginia\)\. These examples show conditions in the requester's IAM policy that cause the request to fail:
+ The requester's policy has a condition for `aws:RequestedRegion`\.

  ```
  ...
  "Effect": "Allow",
  "Action": "rds:CopyDBSnapshot",
  "Resource": "*",
  "Condition": {
      "StringEquals": {
          "aws:RequestedRegion": "us-east-1"
      }
  }
  ```

  The request fails because the policy doesn't allow access to the source Region\. For a successful request, specify both the source and destination Regions\.

  ```
  ...
  "Effect": "Allow",
  "Action": "rds:CopyDBSnapshot",
  "Resource": "*",
  "Condition": {
      "StringEquals": {
          "aws:RequestedRegion": [
              "us-east-1",
              "us-east-2"
          ]
      }
  }
  ```
+ The requester's policy doesn't allow access to the source DB snapshot\.

  ```
  ...
  "Effect": "Allow",
  "Action": "rds:CopyDBSnapshot",
  "Resource": "arn:aws:rds:us-east-1:123456789012:snapshot:target-snapshot"
  ...
  ```

  For a successful request, specify both the source and target snapshots\.

  ```
  ...
  "Effect": "Allow",
  "Action": "rds:CopyDBSnapshot",
  "Resource": [
      "arn:aws:rds:us-east-1:123456789012:snapshot:target-snapshot",
      "arn:aws:rds:us-east-2:123456789012:snapshot:source-snapshot"
  ]
  ...
  ```
+ The requester's policy denies `aws:ViaAWSService`\.

  ```
  ...
  "Effect": "Allow",
  "Action": "rds:CopyDBSnapshot",
  "Resource": "*",
  "Condition": {
      "Bool": {"aws:ViaAWSService": "false"}
  }
  ```

  Communication with the source Region is made by RDS on the requester's behalf\. For a successful request, don't deny calls made by AWS services\.
+ The requester's policy has a condition for `aws:SourceVpc` or `aws:SourceVpce`\.

  These requests might fail because when RDS makes the call to the remote Region, it isn't from the specified VPC or VPC endpoint\.

If you need to use one of the previous conditions that would cause a request to fail, you can include a second statement with `aws:CalledVia` in your policy to make the request succeed\. For example, you can use `aws:CalledVia` with `aws:SourceVpce` as shown here:

```
...
"Effect": "Allow",
"Action": "rds:CopyDBSnapshot",
"Resource": "*",
"Condition": {
    "Condition" : { 
        "ForAnyValue:StringEquals" : {
          "aws:SourceVpce": "vpce-1a2b3c4d"
        }
     }
},
{
    "Effect": "Allow",
    "Action": [
        "rds:CopyDBSnapshot"
    ],
    "Resource": "*",
    "Condition": {
        "ForAnyValue:StringEquals": {
            "aws:CalledVia": [
                "rds.amazonaws.com"
            ]
        }
    }
}
```

For more information, see [Policies and permissions in IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies.html) in the *IAM User Guide*\.

### Authorizing the snapshot copy<a name="USER_CopySnapshot.AcrossRegions.Auth"></a>

After a cross\-Region DB snapshot copy request returns `success`, RDS starts the copy in the background\. An authorization for RDS to access the source snapshot is created\. This authorization links the source DB snapshot to the target DB snapshot, and allows RDS to copy only to the specified target snapshot\.

 The authorization is verified by RDS using the `rds:CrossRegionCommunication` permission in the service\-linked IAM role\. If the copy is authorized, RDS communicates with the source Region and completes the copy\.

 RDS doesn't have access to DB snapshots that weren't authorized previously by a `CopyDBSnapshot` request\. The authorization is revoked when copying completes\.

 RDS uses the service\-linked role to verify the authorization in the source Region\. If you delete the service\-linked role during the copy process, the copy fails\.

For more information, see [Using service\-linked roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/using-service-linked-roles.html) in the *IAM User Guide*\.

### Using AWS Security Token Service credentials<a name="USER_CopySnapshot.AcrossRegions.assumeRole"></a>

Session tokens from the global AWS Security Token Service \(AWS STS\) endpoint are valid only in AWS Regions that are enabled by default \(commercial Regions\)\. If you use credentials from the `assumeRole` API operation in AWS STS, use the regional endpoint if the source Region is an opt\-in Region\. Otherwise, the request fails\. This happens because your credentials must be valid in both Regions, which is true for opt\-in Regions only when the regional AWS STS endpoint is used\.

To use the global endpoint, make sure that it's enabled for both Regions in the operations\. Set the global endpoint to `Valid in all AWS Regions` in the AWS STS account settings\.

 The same rule applies to credentials in the presigned URL parameter\.

For more information, see [Managing AWS STS in an AWS Region](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp_enable-regions.html) in the *IAM User Guide*\.

### Latency and multiple copy requests<a name="USER_CopySnapshot.AcrossRegions.Latency"></a>

Depending on the AWS Regions involved and the amount of data to be copied, a cross\-Region snapshot copy can take hours to complete\.

In some cases, there might be a large number of cross\-Region snapshot copy requests from a given source AWS Region\. In such cases, Amazon RDS might put new cross\-Region copy requests from that source AWS Region into a queue until some in\-progress copies complete\. No progress information is displayed about copy requests while they are in the queue\. Progress information is displayed when the copying starts\.

### Full and incremental copies<a name="USER_CopySnapshot.AcrossRegions.Full"></a>

When you copy a snapshot to a different AWS Region from the source snapshot, the first copy is a full snapshot copy, even if you copy an incremental snapshot\. A full snapshot copy contains all of the data and metadata required to restore the DB instance\. After the first snapshot copy, you can copy incremental snapshots of the same DB instance to the same destination Region within the same AWS account\. For more information on incremental snapshots, see [Incremental snapshot copying](#USER_CopySnapshot.Incremental)\.

Incremental snapshot copying across AWS Regions is supported for both unencrypted and encrypted snapshots\.

When you copy a snapshot across AWS Regions, the copy is an incremental copy if the following conditions are met:
+ The snapshot was previously copied to the destination Region\.
+ The most recent snapshot copy still exists in the destination Region\.
+ All copies of the snapshot in the destination Region are either unencrypted, or were encrypted using the same KMS key\.

## Option group considerations<a name="USER_CopySnapshot.Options"></a>

DB option groups are specific to the AWS Region that they are created in, and you can't use an option group from one AWS Region in another AWS Region\.

For Oracle databases, you can use the AWS CLI or RDS API to copy the custom DB option group from a snapshot that has been shared with your AWS account\. You can only copy option groups within the same AWS Region\. The option group isn't copied if it has already been copied to the destination account and no changes have been made to it since being copied\. If the source option group has been copied before, but has changed since being copied, RDS copies the new version to the destination account\. Default option groups aren't copied\.

When you copy a snapshot across Regions, you can specify a new option group for the snapshot\. We recommend that you prepare the new option group before you copy the snapshot\. In the destination AWS Region, create an option group with the same settings as the original DB instance\. If one already exists in the new AWS Region, you can use that one\.

In some cases, you might copy a snapshot and not specify a new option group for the snapshot\. In these cases, when you restore the snapshot the DB instance gets the default option group\. To give the new DB instance the same options as the original, do the following:

1. In the destination AWS Region, create an option group with the same settings as the original DB instance\. If one already exists in the new AWS Region, you can use that one\.

1. After you restore the snapshot in the destination AWS Region, modify the new DB instance and add the new or existing option group from the previous step\.

## Parameter group considerations<a name="USER_CopySnapshot.Parameters"></a>

When you copy a snapshot across Regions, the copy doesn't include the parameter group used by the original DB instance\. When you restore a snapshot to create a new DB instance, that DB instance gets the default parameter group for the AWS Region it is created in\. To give the new DB instance the same parameters as the original, do the following:

1. In the destination AWS Region, create a DB parameter group with the same settings as the original DB instance\. If one already exists in the new AWS Region, you can use that one\. 

1. After you restore the snapshot in the destination AWS Region, modify the new DB instance and add the new or existing parameter group from the previous step\. 

## Copying a DB snapshot<a name="USER_CopyDBSnapshot"></a>

Use the procedures in this topic to copy a DB snapshot\. For an overview of copying a snapshot, see [Copying a DB snapshot](#USER_CopySnapshot) 

For each AWS account, you can copy up to 20 DB snapshots at a time from one AWS Region to another\. If you copy a DB snapshot to another AWS Region, you create a manual DB snapshot that is retained in that AWS Region\. Copying a DB snapshot out of the source AWS Region incurs Amazon RDS data transfer charges\. 

For more information about data transfer pricing, see [Amazon RDS pricing](https://aws.amazon.com/rds/pricing/)\. 

After the DB snapshot copy has been created in the new AWS Region, the DB snapshot copy behaves the same as all other DB snapshots in that AWS Region\. 

You can copy a DB snapshot using the AWS Management Console, the AWS CLI, or the RDS API\.

### Console<a name="USER_CopySnapshot.CON"></a>

The following procedure copies an encrypted or unencrypted DB snapshot, in the same AWS Region or across Regions, by using the AWS Management Console\. 

**To copy a DB snapshot**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Snapshots**\.

1. Select the DB snapshot that you want to copy\.

1. For **Actions**, choose **Copy snapshot**\.

   The **Copy snapshot** page appears\.  
![\[Copy a DB snapshot\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/DBSnapshotCopy1.png)

1. For **Target option group \(optional\)**, choose a new option group if you want\.

   Specify this option if you are copying a snapshot from one AWS Region to another, and your DB instance uses a nondefault option group\.

   If your source DB instance uses Transparent Data Encryption for Oracle or Microsoft SQL Server, you must specify this option when copying across Regions\. For more information, see [Option group considerations](#USER_CopySnapshot.Options)\.

1. \(Optional\) To copy the DB snapshot to a different AWS Region, for **Destination Region**, choose the new AWS Region\.
**Note**  
The destination AWS Region must have the same database engine version available as the source AWS Region\.

1. For **New DB snapshot identifier**, type the name of the DB snapshot copy\.

   You can make multiple copies of an automated backup or manual snapshot, but each copy must have a unique identifier\.

1. \(Optional\) Select **Copy Tags** to copy tags and values from the snapshot to the copy of the snapshot\.

1. \(Optional\) For **Encryption**, do the following:

   1. Choose **Enable Encryption** if the DB snapshot isn't encrypted but you want to encrypt the copy\.
**Note**  
If the DB snapshot is encrypted, you must encrypt the copy, so the check box is already selected\.

   1. For **AWS KMS key**, specify the KMS key identifier to use to encrypt the DB snapshot copy\.

1. Choose **Copy snapshot**\.

### AWS CLI<a name="USER_CopySnapshot.CLI"></a>

You can copy a DB snapshot by using the AWS CLI command [copy\-db\-snapshot](https://docs.aws.amazon.com/cli/latest/reference/rds/copy-db-snapshot.html)\. If you are copying the snapshot to a new AWS Region, run the command in the new AWS Region\.

The following options are used to copy a DB snapshot\. Not all options are required for all scenarios\. Use the descriptions and the examples that follow to determine which options to use\.
+ `--source-db-snapshot-identifier` – The identifier for the source DB snapshot\.
  + If the source snapshot is in the same AWS Region as the copy, specify a valid DB snapshot identifier\. For example, `rds:mysql-instance1-snapshot-20130805`\.
  + If the source snapshot is in the same AWS Region as the copy, and has been shared with your AWS account, specify a valid DB snapshot ARN\. For example, `arn:aws:rds:us-west-2:123456789012:snapshot:mysql-instance1-snapshot-20130805`\.
  + If the source snapshot is in a different AWS Region than the copy, specify a valid DB snapshot ARN\. For example, `arn:aws:rds:us-west-2:123456789012:snapshot:mysql-instance1-snapshot-20130805`\.
  + If you are copying from a shared manual DB snapshot, this parameter must be the Amazon Resource Name \(ARN\) of the shared DB snapshot\.
  + If you are copying an encrypted snapshot this parameter must be in the ARN format for the source AWS Region, and must match the `SourceDBSnapshotIdentifier` in the `PreSignedUrl` parameter\.
+ `--target-db-snapshot-identifier` – The identifier for the new copy of the encrypted DB snapshot\. 
+ `--copy-option-group` – Copy the option group from a snapshot that has been shared with your AWS account\.
+ `--copy-tags` – Include the copy tags option to copy tags and values from the snapshot to the copy of the snapshot\.
+ `--option-group-name` – The option group to associate with the copy of the snapshot\.

  Specify this option if you are copying a snapshot from one AWS Region to another, and your DB instance uses a non\-default option group\.

  If your source DB instance uses Transparent Data Encryption for Oracle or Microsoft SQL Server, you must specify this option when copying across Regions\. For more information, see [Option group considerations](#USER_CopySnapshot.Options)\.
+ `--kms-key-id` – The KMS key identifier for an encrypted DB snapshot\. The KMS key identifier is the Amazon Resource Name \(ARN\), key identifier, or key alias for the KMS key\. 
  + If you copy an encrypted DB snapshot from your AWS account, you can specify a value for this parameter to encrypt the copy with a new KMS key\. If you don't specify a value for this parameter, then the copy of the DB snapshot is encrypted with the same KMS key as the source DB snapshot\.
  + If you copy an encrypted DB snapshot that is shared from another AWS account, then you must specify a value for this parameter\.
  + If you specify this parameter when you copy an unencrypted snapshot, the copy is encrypted\.
  + If you copy an encrypted snapshot to a different AWS Region, then you must specify a KMS key for the destination AWS Region\. KMS keys are specific to the AWS Region that they are created in, and you cannot use encryption keys from one AWS Region in another AWS Region\.

**Example from unencrypted, to the same Region**  
The following code creates a copy of a snapshot, with the new name `mydbsnapshotcopy`, in the same AWS Region as the source snapshot\. When the copy is made, the DB option group and tags on the original snapshot are copied to the snapshot copy\.  
For Linux, macOS, or Unix:  

```
aws rds copy-db-snapshot \
    --source-db-snapshot-identifier arn:aws:rds:us-west-2:123456789012:snapshot:mysql-instance1-snapshot-20130805 \
    --target-db-snapshot-identifier mydbsnapshotcopy \
    --copy-option-group \
    --copy-tags
```
For Windows:  

```
aws rds copy-db-snapshot ^
    --source-db-snapshot-identifier arn:aws:rds:us-west-2:123456789012:snapshot:mysql-instance1-snapshot-20130805 ^
    --target-db-snapshot-identifier mydbsnapshotcopy ^
    --copy-option-group ^
    --copy-tags
```

**Example from unencrypted, across Regions**  
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

**Example from encrypted, across Regions**  
The following code example copies an encrypted DB snapshot from the US West \(Oregon\) Region in the US East \(N\. Virginia\) Region\. Run the command in the destination \(us\-east\-1\) Region\.  
For Linux, macOS, or Unix:  

```
aws rds copy-db-snapshot \
    --source-db-snapshot-identifier arn:aws:rds:us-west-2:123456789012:snapshot:mysql-instance1-snapshot-20161115 \
    --target-db-snapshot-identifier mydbsnapshotcopy \
    --kms-key-id my-us-east-1-key \ 
    --option-group-name custom-option-group-name
```
For Windows:  

```
aws rds copy-db-snapshot ^
    --source-db-snapshot-identifier arn:aws:rds:us-west-2:123456789012:snapshot:mysql-instance1-snapshot-20161115 ^
    --target-db-snapshot-identifier mydbsnapshotcopy ^
    --kms-key-id my-us-east-1-key ^
    --option-group-name custom-option-group-name
```

The `--source-region` parameter is required when you're copying an encrypted snapshot between the AWS GovCloud \(US\-East\) and AWS GovCloud \(US\-West\) Regions\. For `--source-region`, specify the AWS Region of the source DB instance\.

If `--source-region` isn't specified, specify a `--pre-signed-url` value\. A *presigned URL* is a URL that contains a Signature Version 4 signed request for the `copy-db-snapshot` command that's called in the source AWS Region\. To learn more about the `pre-signed-url` option, see [ copy\-db\-snapshot](https://docs.aws.amazon.com/cli/latest/reference/rds/copy-db-snapshot.html) in the *AWS CLI Command Reference*\.

### RDS API<a name="USER_CopySnapshot.API"></a>

You can copy a DB snapshot by using the Amazon RDS API operation [CopyDBSnapshot](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CopyDBSnapshot.html)\. If you are copying the snapshot to a new AWS Region, perform the action in the new AWS Region\.

The following parameters are used to copy a DB snapshot\. Not all parameters are required for all scenarios\. Use the descriptions and the examples that follow to determine which parameters to use\.
+ `SourceDBSnapshotIdentifier` – The identifier for the source DB snapshot\.
  + If the source snapshot is in the same AWS Region as the copy, specify a valid DB snapshot identifier\. For example, `rds:mysql-instance1-snapshot-20130805`\.
  + If the source snapshot is in the same AWS Region as the copy, and has been shared with your AWS account, specify a valid DB snapshot ARN\. For example, `arn:aws:rds:us-west-2:123456789012:snapshot:mysql-instance1-snapshot-20130805`\.
  + If the source snapshot is in a different AWS Region than the copy, specify a valid DB snapshot ARN\. For example, `arn:aws:rds:us-west-2:123456789012:snapshot:mysql-instance1-snapshot-20130805`\.
  + If you are copying from a shared manual DB snapshot, this parameter must be the Amazon Resource Name \(ARN\) of the shared DB snapshot\.
  + If you are copying an encrypted snapshot this parameter must be in the ARN format for the source AWS Region, and must match the `SourceDBSnapshotIdentifier` in the `PreSignedUrl` parameter\.
+ `TargetDBSnapshotIdentifier` – The identifier for the new copy of the encrypted DB snapshot\.
+ `CopyOptionGroup` – Set this parameter to `true` to copy the option group from a shared snapshot to the copy of the snapshot\. The default is `false`\.
+ `CopyTags` – Set this parameter to `true` to copy tags and values from the snapshot to the copy of the snapshot\. The default is `false`\.
+ `OptionGroupName` – The option group to associate with the copy of the snapshot\.

  Specify this parameter if you are copying a snapshot from one AWS Region to another, and your DB instance uses a non\-default option group\.

  If your source DB instance uses Transparent Data Encryption for Oracle or Microsoft SQL Server, you must specify this parameter when copying across Regions\. For more information, see [Option group considerations](#USER_CopySnapshot.Options)\.
+ `KmsKeyId` – The KMS key identifier for an encrypted DB snapshot\. The KMS key identifier is the Amazon Resource Name \(ARN\), key identifier, or key alias for the KMS key\.
  + If you copy an encrypted DB snapshot from your AWS account, you can specify a value for this parameter to encrypt the copy with a new KMS key\. If you don't specify a value for this parameter, then the copy of the DB snapshot is encrypted with the same KMS key as the source DB snapshot\.
  + If you copy an encrypted DB snapshot that is shared from another AWS account, then you must specify a value for this parameter\.
  + If you specify this parameter when you copy an unencrypted snapshot, the copy is encrypted\.
  + If you copy an encrypted snapshot to a different AWS Region, then you must specify a KMS key for the destination AWS Region\. KMS keys are specific to the AWS Region that they are created in, and you cannot use encryption keys from one AWS Region in another AWS Region\.
+ `PreSignedUrl` – The URL that contains a Signature Version 4 signed request for the `CopyDBSnapshot` API operation in the source AWS Region that contains the source DB snapshot to copy\.

  Specify this parameter when you copy an encrypted DB snapshot from another AWS Region by using the Amazon RDS API\. You can specify the source Region option instead of this parameter when you copy an encrypted DB snapshot from another AWS Region by using the AWS CLI\.

  The presigned URL must be a valid request for the `CopyDBSnapshot` API operation that can be run in the source AWS Region containing the encrypted DB snapshot to be copied\. The presigned URL request must contain the following parameter values:
  + `DestinationRegion` – The AWS Region that the encrypted DB snapshot will be copied to\. This AWS Region is the same one where the `CopyDBSnapshot` operation is called that contains this presigned URL\.

    For example, suppose that you copy an encrypted DB snapshot from the us\-west\-2 Region to the us\-east\-1 Region\. You then call the `CopyDBSnapshot` operation in the us\-east\-1 Region and provide a presigned URL that contains a call to the `CopyDBSnapshot` operation in the us\-west\-2 Region\. For this example, the `DestinationRegion` in the presigned URL must be set to the us\-east\-1 Region\.
  + `KmsKeyId` – The KMS key identifier for the key to use to encrypt the copy of the DB snapshot in the destination AWS Region\. This is the same identifier for both the `CopyDBSnapshot` operation that is called in the destination AWS Region, and the operation contained in the presigned URL\.
  + `SourceDBSnapshotIdentifier` – The DB snapshot identifier for the encrypted snapshot to be copied\. This identifier must be in the Amazon Resource Name \(ARN\) format for the source AWS Region\. For example, if you are copying an encrypted DB snapshot from the us\-west\-2 Region, then your `SourceDBSnapshotIdentifier` looks like the following example: `arn:aws:rds:us-west-2:123456789012:snapshot:mysql-instance1-snapshot-20161115`\.

  For more information on Signature Version 4 signed requests, see the following:
  + [Authenticating requests: Using query parameters \(AWS signature version 4\)](https://docs.aws.amazon.com/AmazonS3/latest/API/sigv4-query-string-auth.html) in the Amazon Simple Storage Service API Reference
  + [Signature version 4 signing process](https://docs.aws.amazon.com/general/latest/gr/signature-version-4.html) in the AWS General Reference

**Example from unencrypted, to the same Region**  
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

**Example from unencrypted, across Regions**  
The following code creates a copy of a snapshot, with the new name `mydbsnapshotcopy`, in the US West \(N\. California\) Region\.  

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

**Example from encrypted, across Regions**  
The following code creates a copy of a snapshot, with the new name `mydbsnapshotcopy`, in the US East \(N\. Virginia\) Region\.  

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