# Sharing a DB Snapshot or DB Cluster Snapshot<a name="USER_ShareSnapshot"></a>

Using Amazon RDS, you can share a manual DB snapshot or DB cluster snapshot in the following ways:
+ Sharing a manual DB snapshot or DB cluster snapshot, whether encrypted or unencrypted, enables authorized AWS accounts to copy the snapshot\.
+ Sharing an unencrypted manual DB snapshot enables authorized AWS accounts to directly restore a DB instance from the snapshot instead of taking a copy of it and restoring from that\. This isn't supported for encrypted manual DB snapshots\.
+ Sharing a manual DB cluster snapshot, whether encrypted or unencrypted, enables authorized AWS accounts to directly restore a DB cluster from the snapshot instead of taking a copy of it and restoring from that\.

**Note**  
To share an automated DB snapshot or DB cluster snapshot, copy it to make a manual version of it, and then share that copy\.

For more information on copying a snapshot, see [Copying a DB Snapshot or DB Cluster Snapshot](USER_CopySnapshot.md)\. For more information on restoring a DB instance from a DB snapshot, see [Restoring from a DB Snapshot](USER_RestoreFromSnapshot.md)\. For more information on restoring a DB cluster from a DB cluster snapshot, see [Backing Up and Restoring an Aurora DB Cluster](Aurora.Managing.md#Aurora.Managing.Backups)\.

You can share a manual snapshot with up to 20 other AWS accounts\. You can also share an unencrypted manual snapshot as public, which makes the snapshot available to all AWS accounts\. Take care when sharing a snapshot as public so that none of your private information is included in any of your public snapshots\. 

The following limitations apply when sharing manual snapshots with other AWS accounts:
+ When you restore a DB instance or DB cluster from a shared snapshot using the AWS Command Line Interface \(AWS CLI\) or Amazon RDS API, you must specify the Amazon Resource Name \(ARN\) of the shared snapshot as the snapshot identifier\.
+ You cannot share a DB snapshot that uses an option group with permanent or persistent options\.

  A *permanent option* cannot be removed from an option group\. Option groups with persistent options cannot be removed from a DB instance once the option group has been assigned to the DB instance\.

  The following table lists permanent and persistent options and their related DB engines\.     
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_ShareSnapshot.html)

## Sharing an Encrypted Snapshot<a name="USER_ShareSnapshot.Encrypted"></a>

You can share DB snapshots or DB cluster snapshots that have been encrypted "at rest" using the AES\-256 encryption algorithm, as described in [Encrypting Amazon RDS Resources](Overview.Encryption.md)\. To do this, you must take the following steps:

1. Share the AWS Key Management Service \(AWS KMS\) encryption key that was used to encrypt the snapshot with any accounts that you want to be able to access the snapshot\.

   You can share AWS KMS encryption keys with another AWS account by adding the other account to the KMS key policy\. For details on updating a key policy, see [Key Policies](http://docs.aws.amazon.com/kms/latest/developerguide/key-policies.html) in the *AWS KMS Developer Guide*\. For an example of creating a key policy, see [Allowing Access to an AWS KMS Encryption Key](#USER_ShareSnapshot.Encrypted.KeyPolicy) later in this topic\.

1. Use the AWS Management Console, AWS CLI, or Amazon RDS API to share the encrypted snapshot with the other accounts\.

These restrictions apply to sharing encrypted snapshots:
+ You can't share encrypted snapshots as public\.
+ You can't share Oracle or Microsoft SQL Server snapshots that are encrypted using Transparent Data Encryption \(TDE\)\.
+ You can't share a snapshot that has been encrypted using the default AWS KMS encryption key of the AWS account that shared the snapshot\. 

### Allowing Access to an AWS KMS Encryption Key<a name="USER_ShareSnapshot.Encrypted.KeyPolicy"></a>

For another AWS account to copy an encrypted DB snapshot or DB cluster snapshot shared from your account, the account that you share your snapshot with must have access to the KMS key that encrypted the snapshot\. To allow another AWS account access to an AWS KMS key, update the key policy for the KMS key with the ARN of the AWS account that you are sharing to as a `Principal` in the KMS key policy, and then allow the `kms:CreateGrant` action\.

After you have given an AWS account access to your KMS encryption key, to copy your encrypted snapshot, that AWS account must create an AWS Identity and Access Management \(IAM\) user if it doesn’t already have one\. In addition, that AWS account must also attach an IAM policy to that IAM user that allows the IAM user to copy an encrypted DB snapshot using your KMS key\. The account must be an IAM user and cannot be a root AWS account identity due to KMS security restrictions\. 

In the following key policy example, user `111122223333` is the owner of the KMS encryption key, and user `444455556666` is the account that the key is being shared with\. This updated key policy gives the AWS account access to the KMS key by including the ARN for the root AWS account identity for user `444455556666` as a `Principal` for the policy, and by allowing the `kms:CreateGrant` action\. 

```
{
  "Id": "key-policy-1",
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "Allow use of the key",
      "Effect": "Allow",
      "Principal": {"AWS": [
        "arn:aws:iam::111122223333:user/KeyUser",
        "arn:aws:iam::444455556666:root"
      ]},
      "Action": [
        "kms:CreateGrant",
        "kms:Encrypt",
        "kms:Decrypt",
        "kms:ReEncrypt",
        "kms:GenerateDataKey*",
        "kms:DescribeKey"
      ],
      "Resource": "*"
    },
    {
      "Sid": "Allow attachment of persistent resources",
      "Effect": "Allow",
      "Principal": {"AWS": [
        "arn:aws:iam::111122223333:user/KeyUser",
        "arn:aws:iam::444455556666:root"
      ]},
      "Action": [
        "kms:CreateGrant",
        "kms:ListGrants",
        "kms:RevokeGrant"
      ],
      "Resource": "*",
      "Condition": {"Bool": {"kms:GrantIsForAWSResource": true}}
    }
  ]
}
```

#### Creating an IAM Policy to Enable Copying of the Encrypted Snapshot<a name="USER_ShareSnapshot.Encrypted.KeyPolicy.IAM"></a>

Once the external AWS account has access to your KMS key, the owner of that AWS account can create a policy that allows an IAM user created for that account to copy an encrypted snapshot encrypted with that KMS key\.

The following example shows a policy that can be attached to an IAM user for AWS account `444455556666` that enables the IAM user to copy a shared snapshot from AWS account `111122223333` that has been encrypted with the KMS key `c989c1dd-a3f2-4a5d-8d96-e793d082ab26` in the `us-west-2` region\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowUseOfTheKey",
            "Effect": "Allow",
            "Action": [
                "kms:Encrypt",
                "kms:Decrypt",
                "kms:ReEncrypt*",
                "kms:GenerateDataKey*",
                "kms:DescribeKey",
                "kms:CreateGrant",
                "kms:RetireGrant"
            ],
            "Resource": ["arn:aws:kms:us-west-2:111122223333:key/c989c1dd-a3f2-4a5d-8d96-e793d082ab26"]
        },
        {
            "Sid": "AllowAttachmentOfPersistentResources",
            "Effect": "Allow",
            "Action": [
                "kms:CreateGrant",
                "kms:ListGrants",
                "kms:RevokeGrant"
            ],
            "Resource": ["arn:aws:kms:us-west-2:111122223333:key/c989c1dd-a3f2-4a5d-8d96-e793d082ab26"],
            "Condition": {
                "Bool": {
                    "kms:GrantIsForAWSResource": true
                }
            }
        }
    ]
}
```

For details on updating a key policy, see [Key Policies](http://docs.aws.amazon.com/kms/latest/developerguide/key-policies.html) in the *AWS KMS Developer Guide*\.

## AWS Management Console<a name="USER_ShareSnapshot.Console"></a>

Using the Amazon RDS console, you can share a manual DB snapshot or DB cluster snapshot with up to 20 AWS accounts\. You can also use the console to stop sharing a manual snapshot with one or more accounts\.

**To share a manual DB snapshot or DB cluster snapshot by using the Amazon RDS console**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Snapshots**\.

1. For **Filter**, choose **Manual Snapshots**\.

1. Select the check box for the manual snapshot that you want to share\.

1. Choose **Snapshot Actions**, and then choose **Share Snapshot**\.

1. Choose one of the following options for **DB Snapshot Visibility**\.
   + If the source DB cluster is unencrypted, choose **Public** to permit all AWS accounts to restore a DB instance from your manual DB snapshot, or choose **Private** to permit only AWS accounts that you specify to restore a DB instance from your manual DB snapshot\.
**Warning**  
If you set **DB Snapshot Visibility** to **Public**, all AWS accounts can restore a DB instance from your manual DB snapshot and have access to your data\. Do not share any manual DB snapshots that contain private information as **Public**\.
   + If the source DB cluster is encrypted, **DB Snapshot Visibility** is set as **Private** because encrypted snapshots can't be shared as public\.

1. For **AWS Account ID**, type the AWS account identifier for an account that you want to permit to restore a DB instance or DB cluster from your manual snapshot, and then choose **Add**\. Repeat to include additional AWS account identifiers, up to 20 AWS accounts\.

   If you make an error when adding an AWS account identifier to the list of permitted accounts, you can delete it from the list by choosing **Delete** at the right of the incorrect AWS account identifier\.  
![\[Permit AWS accounts to restore a manual DB snapshot\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/ShareSnapshot_add.png)

1. After you have added identifiers for all of the AWS accounts that you want to permit to restore the manual snapshot, choose **Save** to save your changes\.

**To stop sharing a manual DB snapshot or DB cluster snapshot with an AWS account**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Snapshots**\.

1. For **Filter**, choose **Manual Snapshots**\.

1. Select the check box for the manual snapshot that you want to stop sharing\.

1. Choose **Snapshot Actions**, and then choose **Share Snapshot**\.

1. To remove permission for an AWS account, choose **Delete** for the AWS account identifier for that account from the list of authorized accounts\.  
![\[Permit AWS accounts to restore a manual DB snapshot\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/ShareSnapshot_delete.png)

1. Choose **Save** to save your changes\.

## AWS CLI<a name="USER_ShareSnapshot.CLI"></a>

To share a DB snapshot, use the `aws rds modify-db-snapshot-attribute` command\. Use the `--values-to-add` parameter to add a list of the IDs for the AWS accounts that are authorized to restore the manual snapshot\. 

The following example permits two AWS account identifiers, `123451234512` and `123456789012`, to restore the DB snapshot named `manual-snapshot1`, and removes the `all` attribute value to mark the snapshot as private\.

```
			aws rds modify-db-snapshot-attribute \ 
--db-snapshot-identifier manual-snapshot1 \ 
--attribute-name restore \ 
--values-to-add '["111122223333","444455556666"]'
```

To remove an AWS account identifier from the list, use the `-- values-to-remove` parameter\. The following example prevents AWS account ID 444455556666 from restoring the snapshot\.

```
			aws rds modify-db-snapshot-attribute \ 
--db-snapshot-identifier manual-snapshot1 \ 
--attribute-name restore \ 
--values-to-remove '["444455556666 "]'
```

## API<a name="USER_ShareSnapshot.API"></a>

You can also share a manual DB snapshot or DB cluster snapshot with other AWS accounts by using the Amazon RDS API\. To do so, call the [http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBSnapshotAttribute.html](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBSnapshotAttribute.html) action for DB instances, or the [http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBClusterSnapshotAttribute.html](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBClusterSnapshotAttribute.html) action for Amazon Aurora DB clusters\. Specify `restore` for `AttributeName`, and use the `ValuesToAdd` parameter to add a list of the IDs for the AWS accounts that are authorized to restore the manual snapshot\. 

To make a manual snapshot public and restorable by all AWS accounts, use the value `all`\. However, take care not to add the `all` value for any manual snapshots that contain private information that you don't want to be available to all AWS accounts\. Also, don't specify `all` for encrypted snapshots, because making such snapshots public isn't supported\.

To remove sharing permission for an AWS account, use the [http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBSnapshotAttribute.html](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBSnapshotAttribute.html) or [http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBClusterSnapshotAttribute.html](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBClusterSnapshotAttribute.html) action with `AttributeName` set to `restore` and the `ValuesToRemove` parameter\. To mark a manual snapshot as private, remove the value `all` from the values list for the `restore` attribute\.

The following example permits two AWS account identifiers, `123451234512` and `123456789012`, to restore the DB snapshot named `manual-snapshot1`, and removes the `all` attribute value to mark the snapshot as private\.

```
https://rds.us-west-2.amazonaws.com/
	?Action=ModifyDBSnapshotAttribute
	&AttributeName=restore
	&DBSnapshotIdentifier=manual-snapshot1
	&SignatureMethod=HmacSHA256&SignatureVersion=4
	&ValuesToAdd.member.1=123451234512
	&ValuesToAdd.member.2=123456789012
	&ValuesToRemove.member.1=all
	&Version=2014-10-31
	&X-Amz-Algorithm=AWS4-HMAC-SHA256
	&X-Amz-Credential=AKIADQKE4SARGYLE/20150922/us-west-2/rds/aws4_request
	&X-Amz-Date=20150922T220515Z
	&X-Amz-SignedHeaders=content-type;host;user-agent;x-amz-content-sha256;x-amz-date
	&X-Amz-Signature=ef38f1ce3dab4e1dbf113d8d2a265c67d17ece1999ffd36be85714ed36dddbb3
```

To list all of the AWS accounts permitted to restore a snapshot, use the [http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBSnapshotAttributes.html](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBSnapshotAttributes.html) or [http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBClusterSnapshotAttributes.html](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBClusterSnapshotAttributes.html) API action\.

## Related Topics<a name="USER_ShareSnapshot.Related"></a>
+ [Creating a DB Snapshot](USER_CreateSnapshot.md)
+ [Copying a DB Snapshot or DB Cluster Snapshot](USER_CopySnapshot.md)
+ [Restoring from a DB Snapshot](USER_RestoreFromSnapshot.md)