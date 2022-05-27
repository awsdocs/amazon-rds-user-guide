# Creating a read replica in a different AWS Region<a name="USER_ReadRepl.XRgn"></a>

With Amazon RDS, you can create a MariaDB, MySQL, Oracle, or PostgreSQL read replica in a different AWS Region from the source DB instance\. Creating a cross\-Region read replica isn't supported for SQL Server on Amazon RDS\.

![\[Cross-Region read replica configuration\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/read-replica-cross-region.png)

You create a read replica in a different AWS Region to do the following:
+ Improve your disaster recovery capabilities\.
+ Scale read operations into an AWS Region closer to your users\.
+ Make it easier to migrate from a data center in one AWS Region to a data center in another AWS Region\.

Creating a read replica in a different AWS Region from the source instance is similar to creating a replica in the same AWS Region\. You can use the AWS Management Console, run the [https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance-read-replica.html](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance-read-replica.html) command, or call the [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstanceReadReplica.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstanceReadReplica.html) API operation\.

**Note**  
To create an encrypted read replica in a different AWS Region from the source DB instance, the source DB instance must be encrypted\.

## Creating a cross\-Region read replica<a name="ReadRepl.XRgn"></a>

The following procedures show how to create a read replica from a source MariaDB, MySQL, Oracle, or PostgreSQL DB instance in a different AWS Region\.

### Console<a name="USER_ReadRepl.XRgn.CON"></a>

You can create a read replica across AWS Regions using the AWS Management Console\.

**To create a read replica across AWS Regions with the console**

1.  Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose the MariaDB, MySQL, Oracle, or PostgreSQL DB instance that you want to use as the source for a read replica\.

1. For **Actions**, choose **Create read replica**\.

1. For **DB instance identifier**, enter a name for the read replica\.

1. Choose the **Destination Region\.**

1. Choose the instance specifications you want to use\. We recommend that you use the same DB instance class and storage type for the read replica\.

1. To create an encrypted read replica in another AWS Region:

   1. Choose **Enable encryption**\.

   1. For **AWS KMS key**, choose the AWS KMS key identifier of the KMS key in the destination AWS Region\.
**Note**  
 To create an encrypted read replica, the source DB instance must be encrypted\. To learn more about encrypting the source DB instance, see [Encrypting Amazon RDS resources](Overview.Encryption.md)\.

1. Choose other options, such as storage autoscaling\.

1. Choose **Create read replica**\.

### AWS CLI<a name="USER_ReadRepl.XRgn.CLI"></a>

 To create a read replica from a source MySQL, MariaDB, Oracle, or PostgreSQL DB instance in a different AWS Region, you can use the [https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance-read-replica.html](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance-read-replica.html) command\. In this case, you use [https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance-read-replica.html](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance-read-replica.html) from the AWS Region where you want the read replica \(destination Region\) and specify the Amazon Resource Name \(ARN\) for the source DB instance\. An ARN uniquely identifies a resource created in Amazon Web Services\.

For example, if your source DB instance is in the US East \(N\. Virginia\) Region, the ARN looks similar to this example:

```
arn:aws:rds:us-east-1:123456789012:db:mydbinstance
```

For information about ARNs, see [Working with Amazon Resource Names \(ARNs\) in Amazon RDS](USER_Tagging.ARN.md)\.

 To create a read replica in a different AWS Region from the source DB instance, you can use the AWS CLI [https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance-read-replica.html](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance-read-replica.html) command from the destination AWS Region\. The following parameters are required for creating a read replica in another AWS Region:
+  `--region` – The destination AWS Region where the read replica is created\.
+  `--source-db-instance-identifier` – The DB instance identifier for the source DB instance\. This identifier must be in the ARN format for the source AWS Region\.
+  `--db-instance-identifier` – The identifier for the read replica in the destination AWS Region\.

**Example of a cross\-Region read replica**  
The following code creates a read replica in the US West \(Oregon\) Region from a source DB instance in the US East \(N\. Virginia\) Region\.  
For Linux, macOS, or Unix:  

```
aws rds create-db-instance-read-replica \
    --db-instance-identifier myreadreplica \
    --region us-west-2 \
    --source-db-instance-identifier arn:aws:rds:us-east-1:123456789012:db:mydbinstance
```
For Windows:  

```
aws rds create-db-instance-read-replica ^
    --db-instance-identifier myreadreplica ^
    --region us-west-2 ^
    --source-db-instance-identifier arn:aws:rds:us-east-1:123456789012:db:mydbinstance
```

The following parameter is also required for creating an encrypted read replica in another AWS Region:
+  `--kms-key-id` – The AWS KMS key identifier of the KMS key to use to encrypt the read replica in the destination AWS Region\.

**Example of an encrypted cross\-Region read replica**  
The following code creates an encrypted read replica in the US West \(Oregon\) Region from a source DB instance in the US East \(N\. Virginia\) Region\.  
For Linux, macOS, or Unix:  

```
aws rds create-db-instance-read-replica \
    --db-instance-identifier myreadreplica \
    --region us-west-2 \
    --source-db-instance-identifier arn:aws:rds:us-east-1:123456789012:db:mydbinstance \
    --kms-key-id my-us-west-2-key
```
For Windows:  

```
aws rds create-db-instance-read-replica ^
    --db-instance-identifier myreadreplica ^
    --region us-west-2 ^
    --source-db-instance-identifier arn:aws:rds:us-east-1:123456789012:db:mydbinstance ^
    --kms-key-id my-us-west-2-key
```

The `--source-region` option is required when you're creating an encrypted read replica between the AWS GovCloud \(US\-East\) and AWS GovCloud \(US\-West\) Regions\. For `--source-region`, specify the AWS Region of the source DB instance\.

If `--source-region` isn't specified, specify a `--pre-signed-url` value\. A *presigned URL* is a URL that contains a Signature Version 4 signed request for the `create-db-instance-read-replica` command that's called in the source AWS Region\. To learn more about the `pre-signed-url` option, see [create\-db\-instance\-read\-replica](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance-read-replica.html) in the *AWS CLI Command Reference*\.

### RDS API<a name="USER_ReadRepl.XRgn.API"></a>

To create a read replica from a source MySQL, MariaDB, Oracle, or PostgreSQL DB instance in a different AWS Region, you can call the Amazon RDS API operation [CreateDBInstanceReadReplica](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstanceReadReplica.html)\. In this case, you call [CreateDBInstanceReadReplica](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstanceReadReplica.html) from the AWS Region where you want the read replica \(destination Region\) and specify the Amazon Resource Name \(ARN\) for the source DB instance\. An ARN uniquely identifies a resource created in Amazon Web Services\.

To create an encrypted read replica in a different AWS Region from the source DB instance, you can use the Amazon RDS API [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstanceReadReplica.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstanceReadReplica.html) operation from the destination AWS Region\. To create an encrypted read replica in another AWS Region, you must specify a value for `PreSignedURL`\. `PreSignedURL` should contain a request for the [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstanceReadReplica.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstanceReadReplica.html) operation to call in the source AWS Region where the read replica is created in\. To learn more about `PreSignedUrl`, see [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstanceReadReplica.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstanceReadReplica.html)\.

For example, if your source DB instance is in the US East \(N\. Virginia\) Region, the ARN looks similar to the following\.

```
arn:aws:rds:us-east-1:123456789012:db:mydbinstance
```

For information about ARNs, see [Working with Amazon Resource Names \(ARNs\) in Amazon RDS](USER_Tagging.ARN.md)\.

**Example**  

```
https://us-west-2.rds.amazonaws.com/
    ?Action=CreateDBInstanceReadReplica
    &KmsKeyId=my-us-east-1-key
    &PreSignedUrl=https%253A%252F%252Frds.us-west-2.amazonaws.com%252F
         %253FAction%253DCreateDBInstanceReadReplica
         %2526DestinationRegion%253Dus-east-1
         %2526KmsKeyId%253Dmy-us-east-1-key
         %2526SourceDBInstanceIdentifier%253Darn%25253Aaws%25253Ards%25253Aus-west-2%123456789012%25253Adb%25253Amydbinstance
         %2526SignatureMethod%253DHmacSHA256
         %2526SignatureVersion%253D4%2526SourceDBInstanceIdentifier%253Darn%25253Aaws%25253Ards%25253Aus-west-2%25253A123456789012%25253Ainstance%25253Amydbinstance
         %2526Version%253D2014-10-31
         %2526X-Amz-Algorithm%253DAWS4-HMAC-SHA256
         %2526X-Amz-Credential%253DAKIADQKE4SARGYLE%252F20161117%252Fus-west-2%252Frds%252Faws4_request
         %2526X-Amz-Date%253D20161117T215409Z
         %2526X-Amz-Expires%253D3600
         %2526X-Amz-SignedHeaders%253Dcontent-type%253Bhost%253Buser-agent%253Bx-amz-content-sha256%253Bx-amz-date
         %2526X-Amz-Signature%253D255a0f17b4e717d3b67fad163c3ec26573b882c03a65523522cf890a67fca613
    &DBInstanceIdentifier=myreadreplica
    &SourceDBInstanceIdentifier=&region-arn;rds:us-east-1:123456789012:db:mydbinstance
    &Version=2012-01-15						
    &SignatureVersion=2
    &SignatureMethod=HmacSHA256
    &Timestamp=2012-01-20T22%3A06%3A23.624Z
    &AWSAccessKeyId=<&AWS; Access Key ID>
    &Signature=<Signature>
```

## How Amazon RDS does cross\-Region replication<a name="USER_ReadRepl.XRgn.Process"></a>

Amazon RDS uses the following process to create a cross\-Region read replica\. Depending on the AWS Regions involved and the amount of data in the databases, this process can take hours to complete\. You can use this information to determine how far the process has proceeded when you create a cross\-Region read replica:

1. Amazon RDS begins configuring the source DB instance as a replication source and sets the status to *modifying*\.

1. Amazon RDS begins setting up the specified read replica in the destination AWS Region and sets the status to *creating*\.

1. Amazon RDS creates an automated DB snapshot of the source DB instance in the source AWS Region\. The format of the DB snapshot name is `rds:<InstanceID>-<timestamp>`, where `<InstanceID>` is the identifier of the source instance, and `<timestamp>` is the date and time the copy started\. For example, `rds:mysourceinstance-2013-11-14-09-24` was created from the instance `mysourceinstance` at `2013-11-14-09-24`\. During the creation of an automated DB snapshot, the source DB instance status remains *modifying*, the read replica status remains *creating*, and the DB snapshot status is *creating*\. The progress column of the DB snapshot page in the console reports how far the DB snapshot creation has progressed\. When the DB snapshot is complete, the status of both the DB snapshot and source DB instance are set to *available*\.

1. Amazon RDS begins a cross\-Region snapshot copy for the initial data transfer\. The snapshot copy is listed as an automated snapshot in the destination AWS Region with a status of *creating*\. It has the same name as the source DB snapshot\. The progress column of the DB snapshot display indicates how far the copy has progressed\. When the copy is complete, the status of the DB snapshot copy is set to *available*\.

1. Amazon RDS then uses the copied DB snapshot for the initial data load on the read replica\. During this phase, the read replica is in the list of DB instances in the destination, with a status of *creating*\. When the load is complete, the read replica status is set to *available*, and the DB snapshot copy is deleted\.

1. When the read replica reaches the available status, Amazon RDS starts by replicating the changes made to the source instance since the start of the create read replica operation\. During this phase, the replication lag time for the read replica is greater than 0\.

   For information about replication lag time, see [Monitoring read replication](USER_ReadRepl.md#USER_ReadRepl.Monitoring)\.

## Cross\-Region replication considerations<a name="USER_ReadRepl.XRgn.Cnsdr"></a>

All of the considerations for performing replication within an AWS Region apply to cross\-Region replication\. The following extra considerations apply when replicating between AWS Regions:
+ You can only replicate between AWS Regions when using the following Amazon RDS DB instances:
  + MariaDB \(all versions\)
  + MySQL \(all versions\)
  + Oracle Enterprise Edition \(EE\) of Oracle Database 12c Release 1 \(12\.1\) using 12\.1\.0\.2\.v10 and higher, Oracle Database 12c Release 2 \(12\.2\), and Oracle Database 19c using the non\-CDB architecture
**Note**  
Oracle DB instances that you create using the CDB architecture aren't supported\.

    An Active Data Guard license is required\. For information about limitations for Oracle cross\-Region read replicas, see [Replica requirements for Oracle](oracle-read-replicas.limitations.md)\.
  + PostgreSQL \(all versions\)
+ A source DB instance can have cross\-Region read replicas in multiple AWS Regions\.
+ You can only create a cross\-Region Amazon RDS read replica from a source Amazon RDS DB instance that is not a read replica of another Amazon RDS DB instance\.
+ You can replicate between the AWS GovCloud \(US\-East\) and AWS GovCloud \(US\-West\) Regions, but not into or out of AWS GovCloud \(US\)\.
+ You can expect to see a higher level of lag time for any read replica that is in a different AWS Region than the source instance\. This lag time comes from the longer network channels between regional data centers\.
+ For cross\-Region read replicas, any of the create read replica commands that specify the `--db-subnet-group-name` parameter must specify a DB subnet group from the same VPC\.
+ Due to the limit on the number of access control list \(ACL\) entries for a VPC, we can't guarantee more than five cross\-Region read replica instances\. 
+ In most cases, the read replica uses the default DB parameter group and DB option group for the specified DB engine\.

  For the MySQL and Oracle DB engines, you can specify a custom parameter group for the read replica in the `--db-parameter-group-name` option of the AWS CLI command [https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance-read-replica.html](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance-read-replica.html)\. You can't specify a custom parameter group when you use the AWS Management Console\.
+ The read replica uses the default security group\.
+ For MariaDB, MySQL, and Oracle DB instances, when the source DB instance for a cross\-Region read replica is deleted, the read replica is promoted\.
+ For PostgreSQL DB instances, when the source DB instance for a cross\-Region read replica is deleted, the replication status of the read replica is set to `terminated`\. The read replica isn't promoted\.

  You have to promote the read replica manually or delete it\.

### Requesting a cross\-Region read replica<a name="ReadRepl.XRgn.Policy"></a>

To communicate with the source Region to request the creation of a cross\-Region read replica, the requester \(IAM role or IAM user\) must have access to the source DB instance and the source Region\.

Certain conditions in the requester's IAM policy can cause the request to fail\. The following examples assume that the source DB instance is in US East \(Ohio\) and the read replica is created in US East \(N\. Virginia\)\. These examples show conditions in the requester's IAM policy that cause the request to fail:
+ The requester's policy has a condition for `aws:RequestedRegion`\.

  ```
  ...
  "Effect": "Allow",
  "Action": "rds:CreateDBInstanceReadReplica",
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
  "Action": "rds:CreateDBInstanceReadReplica",
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
+ The requester's policy doesn't allow access to the source DB instance\.

  ```
  ...
  "Effect": "Allow",
  "Action": "rds:CreateDBInstanceReadReplica",
  "Resource": "arn:aws:rds:us-east-1:123456789012:db:myreadreplica"
  ...
  ```

  For a successful request, specify both the source instance and the replica\.

  ```
  ...
  "Effect": "Allow",
  "Action": "rds:CreateDBInstanceReadReplica",
  "Resource": [
      "arn:aws:rds:us-east-1:123456789012:db:myreadreplica",
      "arn:aws:rds:us-east-2:123456789012:db:mydbinstance"
  ]
  ...
  ```
+ The requester's policy denies `aws:ViaAWSService`\.

  ```
  ...
  "Effect": "Allow",
  "Action": "rds:CreateDBInstanceReadReplica",
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
"Action": "rds:CreateDBInstanceReadReplica",
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
        "rds:CreateDBInstanceReadReplica"
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

### Authorizing the read replica<a name="ReadRepl.XRgn.Auth"></a>

After a cross\-Region DB read replica creation request returns `success`, RDS starts the replica creation in the background\. An authorization for RDS to access the source DB instance is created\. This authorization links the source DB instance to the read replica, and allows RDS to copy only to the specified read replica\.

 The authorization is verified by RDS using the `rds:CrossRegionCommunication` permission in the service\-linked IAM role\. If the replica is authorized, RDS communicates with the source Region and completes the replica creation\.

 RDS doesn't have access to DB instances that weren't authorized previously by a `CreateDBInstanceReadReplica` request\. The authorization is revoked when read replica creation completes\.

 RDS uses the service\-linked role to verify the authorization in the source Region\. If you delete the service\-linked role during the replication creation process, the creation fails\.

For more information, see [Using service\-linked roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/using-service-linked-roles.html) in the *IAM User Guide*\.

### Using AWS Security Token Service credentials<a name="ReadRepl.XRgn.assumeRole"></a>

Session tokens from the global AWS Security Token Service \(AWS STS\) endpoint are valid only in AWS Regions that are enabled by default \(commercial Regions\)\. If you use credentials from the `assumeRole` API operation in AWS STS, use the regional endpoint if the source Region is an opt\-in Region\. Otherwise, the request fails\. This happens because your credentials must be valid in both Regions, which is true for opt\-in Regions only when the regional AWS STS endpoint is used\.

To use the global endpoint, make sure that it's enabled for both Regions in the operations\. Set the global endpoint to `Valid in all AWS Regions` in the AWS STS account settings\.

 The same rule applies to credentials in the presigned URL parameter\.

For more information, see [Managing AWS STS in an AWS Region](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp_enable-regions.html) in the *IAM User Guide*\.

## Cross\-Region replication costs<a name="USER_ReadRepl.XRgn.Costs"></a>

The data transferred for cross\-Region replication incurs Amazon RDS data transfer charges\. These cross\-Region replication actions generate charges for the data transferred out of the source AWS Region:
+ When you create a read replica, Amazon RDS takes a snapshot of the source instance and transfers the snapshot to the read replica AWS Region\.
+ For each data modification made in the source databases, Amazon RDS transfers data from the source AWS Region to the read replica AWS Region\.

For more information about data transfer pricing, see [Amazon RDS pricing](https://aws.amazon.com/rds/pricing/)\.

For MySQL and MariaDB instances, you can reduce your data transfer costs by reducing the number of cross\-Region read replicas that you create\. For example, suppose that you have a source DB instance in one AWS Region and want to have three read replicas in another AWS Region\. In this case, you create only one of the read replicas from the source DB instance\. You create the other two replicas from the first read replica instead of the source DB instance\.

For example, if you have `source-instance-1` in one AWS Region, you can do the following:
+ Create `read-replica-1` in the new AWS Region, specifying `source-instance-1` as the source\.
+ Create `read-replica-2` from `read-replica-1`\.
+ Create `read-replica-3` from `read-replica-1`\.

In this example, you are only charged for the data transferred from `source-instance-1` to `read-replica-1`\. You aren't charged for the data transferred from `read-replica-1` to the other two replicas because they are all in the same AWS Region\. If you create all three replicas directly from `source-instance-1`, you are charged for the data transfers to all three replicas\.