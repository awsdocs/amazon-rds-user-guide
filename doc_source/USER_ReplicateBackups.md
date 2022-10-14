# Replicating automated backups to another AWS Region<a name="USER_ReplicateBackups"></a>

For added disaster recovery capability, you can configure your Amazon RDS database instance to replicate snapshots and transaction logs to a destination AWS Region of your choice\. When backup replication is configured for a DB instance, RDS initiates a cross\-Region copy of all snapshots and transaction logs as soon as they are ready on the DB instance\.

DB snapshot copy charges apply to the data transfer\. After the DB snapshot is copied, standard charges apply to storage in the destination Region\. For more details, see [RDS Pricing](http://aws.amazon.com/rds/oracle/pricing/)\.

For an example of using backup replication, see the AWS online tech talk [Managed Disaster Recovery with Amazon RDS for Oracle Cross\-Region Automated Backups](https://pages.awscloud.com/Managed-Disaster-Recovery-with-Amazon-RDS-for-Oracle-Cross-Region-Automated-Backups_2021_0908-DAT_OD.html)\.

**Topics**
+ [Region and version availability](#USER_ReplicateBackups.RegionVersionAvailability)
+ [Source and destination AWS Region support](#USER_ReplicateBackups.Regions)
+ [Enabling cross\-Region automated backups](#AutomatedBackups.Replicating.Enable)
+ [Finding information about replicated backups](#AutomatedBackups.Replicating.Describe)
+ [Restoring to a specified time from a replicated backup](#AutomatedBackups.PiTR)
+ [Stopping automated backup replication](#AutomatedBackups.StopReplicating)
+ [Deleting replicated backups](#AutomatedBackups.Delete)

## Region and version availability<a name="USER_ReplicateBackups.RegionVersionAvailability"></a>

Feature availability and support varies across specific versions of each database engine, and across AWS Regions\. For more information on version and Region availability with cross\-Region automated backups, see [Cross\-Region automated backups](Concepts.RDS_Fea_Regions_DB-eng.Feature.CrossRegionAutomatedBackups.md)\. 

## Source and destination AWS Region support<a name="USER_ReplicateBackups.Regions"></a>

Backup replication is supported between the following AWS Regions\.


****  

| Source Region | Destination Regions available | 
| --- | --- | 
| Asia Pacific \(Mumbai\) |  Asia Pacific \(Singapore\) US East \(N\. Virginia\), US East \(Ohio\), US West \(Oregon\)  | 
| Asia Pacific \(Osaka\) | Asia Pacific \(Tokyo\) | 
| Asia Pacific \(Seoul\) |  Asia Pacific \(Singapore\), Asia Pacific \(Tokyo\) US East \(N\. Virginia\), US East \(Ohio\), US West \(Oregon\)  | 
| Asia Pacific \(Singapore\) |  Asia Pacific \(Mumbai\), Asia Pacific \(Seoul\), Asia Pacific \(Sydney\), Asia Pacific \(Tokyo\) US East \(N\. Virginia\), US East \(Ohio\), US West \(Oregon\)  | 
| Asia Pacific \(Sydney\) |  Asia Pacific \(Singapore\) US East \(N\. Virginia\), US West \(N\. California\), US West \(Oregon\)  | 
| Asia Pacific \(Tokyo\) |  Asia Pacific \(Osaka\), Asia Pacific \(Seoul\), Asia Pacific \(Singapore\) US East \(N\. Virginia\), US East \(Ohio\), US West \(Oregon\)  | 
| Canada \(Central\) |  Europe \(Ireland\) US East \(N\. Virginia\), US East \(Ohio\), US West \(N\. California\), US West \(Oregon\)  | 
| China \(Beijing\) | China \(Ningxia\) | 
| China \(Ningxia\) | China \(Beijing\) | 
| Europe \(Frankfurt\) |  Europe \(Ireland\), Europe \(London\), Europe \(Paris\), Europe \(Stockholm\) US East \(N\. Virginia\), US East \(Ohio\), US West \(Oregon\)  | 
| Europe \(Ireland\) |  Canada \(Central\) Europe \(Frankfurt\), Europe \(London\), Europe \(Paris\), Europe \(Stockholm\) US East \(N\. Virginia\), US East \(Ohio\), US West \(N\. California\), US West \(Oregon\)  | 
| Europe \(London\) |  Europe \(Frankfurt\), Europe \(Ireland\), Europe \(Paris\), Europe \(Stockholm\) US East \(N\. Virginia\)  | 
| Europe \(Paris\) |  Europe \(Frankfurt\), Europe \(Ireland\), Europe \(London\), Europe \(Stockholm\) US East \(N\. Virginia\)  | 
| Europe \(Stockholm\) |  Europe \(Frankfurt\), Europe \(Ireland\), Europe \(London\), Europe \(Paris\) US East \(N\. Virginia\)  | 
| South America \(São Paulo\) | US East \(N\. Virginia\), US East \(Ohio\) | 
| AWS GovCloud \(US\-East\) | AWS GovCloud \(US\-West\) | 
| AWS GovCloud \(US\-West\) | AWS GovCloud \(US\-East\) | 
| US East \(N\. Virginia\) |  Asia Pacific \(Mumbai\), Asia Pacific \(Seoul\), Asia Pacific \(Singapore\), Asia Pacific \(Sydney\), Asia Pacific \(Tokyo\) Canada \(Central\) Europe \(Frankfurt\), Europe \(Ireland\), Europe \(London\), Europe \(Paris\), Europe \(Stockholm\) South America \(São Paulo\) US East \(Ohio\), US West \(N\. California\), US West \(Oregon\)  | 
| US East \(Ohio\) |  Asia Pacific \(Mumbai\), Asia Pacific \(Seoul\), Asia Pacific \(Singapore\), Asia Pacific \(Tokyo\) Canada \(Central\) Europe \(Frankfurt\), Europe \(Ireland\) South America \(São Paulo\) US East \(N\. Virginia\), US West \(N\. California\), US West \(Oregon\)  | 
| US West \(N\. California\) |  Asia Pacific \(Sydney\) Canada \(Central\) Europe \(Ireland\) US East \(N\. Virginia\), US East \(Ohio\), US West \(Oregon\)  | 
| US West \(Oregon\) |  Asia Pacific \(Mumbai\), Asia Pacific \(Seoul\), Asia Pacific \(Singapore\), Asia Pacific \(Sydney\), Asia Pacific \(Tokyo\) Canada \(Central\) Europe \(Frankfurt\), Europe \(Ireland\) US East \(N\. Virginia\), US East \(Ohio\), US West \(N\. California\)  | 

You can also use the `describe-source-regions` AWS CLI command to find out which AWS Regions can replicate to each other\. For more information, see [Finding information about replicated backups](#AutomatedBackups.Replicating.Describe)\.

## Enabling cross\-Region automated backups<a name="AutomatedBackups.Replicating.Enable"></a>

You can enable backup replication on new or existing DB instances using the Amazon RDS console\. You can also use the `start-db-instance-automated-backups-replication` AWS CLI command or the `StartDBInstanceAutomatedBackupsReplication` RDS API operation\. You can replicate up to 20 backups to each destination AWS Region for each AWS account\.

**Note**  
To be able to replicate automated backups, make sure to enable them\. For more information, see [Enabling automated backups](USER_WorkingWithAutomatedBackups.md#USER_WorkingWithAutomatedBackups.Enabling)\.

### Console<a name="AutomatedBackups.Replicating.Enable.Console"></a>

You can enable backup replication for a new or existing DB instance:
+ For a new DB instance, enable it when you launch the instance\. For more information, see [Settings for DB instances](USER_CreateDBInstance.md#USER_CreateDBInstance.Settings)\.
+ For an existing DB instance, use the following procedure\.

**To enable backup replication for an existing DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Automated backups**\.

1. On the **Current Region** tab, choose the DB instance for which you want to enable backup replication\.

1. For **Actions**, choose **Manage cross\-Region replication**\.

1. Under **Backup replication**, choose **Enable replication to another AWS Region**\.

1. Choose the **Destination Region**\.

1. Choose the **Replicated backup retention period**\.

1. If you've enabled encryption on the source DB instance, choose the **AWS KMS key** for encrypting the backups\.

1. Choose **Save**\.

In the source Region, replicated backups are listed on the **Current Region** tab of the **Automated backups** page\. In the destination Region, replicated backups are listed on the **Replicated backups** tab of the **Automated backups** page\.

### AWS CLI<a name="AutomatedBackups.Replicating.Enable.CLI"></a>

Enable backup replication by using the [https://docs.aws.amazon.com/cli/latest/reference/rds/start-db-instance-automated-backups-replication.html](https://docs.aws.amazon.com/cli/latest/reference/rds/start-db-instance-automated-backups-replication.html) AWS CLI command\.

The following CLI example replicates automated backups from a DB instance in the US West \(Oregon\) Region to the US East \(N\. Virginia\) Region\. It also encrypts the replicated backups, using an AWS KMS key in the destination Region\.

**To enable backup replication**
+ Run one of the following commands\.

  For Linux, macOS, or Unix:

  ```
  aws rds start-db-instance-automated-backups-replication \
  --region us-east-1 \
  --source-db-instance-arn "arn:aws:rds:us-west-2:123456789012:db:mydatabase" \
  --kms-key-id "arn:aws:kms:us-east-1:123456789012:key/AKIAIOSFODNN7EXAMPLE" \
  --backup-retention-period 7
  ```

  For Windows:

  ```
  aws rds start-db-instance-automated-backups-replication ^
  --region us-east-1 ^
  --source-db-instance-arn "arn:aws:rds:us-west-2:123456789012:db:mydatabase" ^
  --kms-key-id "arn:aws:kms:us-east-1:123456789012:key/AKIAIOSFODNN7EXAMPLE" ^
  --backup-retention-period 7
  ```

  The `--source-region` option is required when you encrypt backups between the AWS GovCloud \(US\-East\) and AWS GovCloud \(US\-West\) Regions\. For `--source-region`, specify the AWS Region of the source DB instance\.

  If `--source-region` isn't specified, make sure to specify a `--pre-signed-url` value\. A *presigned URL* is a URL that contains a Signature Version 4 signed request for the `start-db-instance-automated-backups-replication` command that is called in the source AWS Region\. To learn more about the `pre-signed-url` option, see [ start\-db\-instance\-automated\-backups\-replication](https://docs.aws.amazon.com/cli/latest/reference/rds/start-db-instance-automated-backups-replication.html) in the *AWS CLI Command Reference*\.

### RDS API<a name="AutomatedBackups.Replicating.Enable.API"></a>

Enable backup replication by using the [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_StartDBInstanceAutomatedBackupsReplication.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_StartDBInstanceAutomatedBackupsReplication.html) RDS API operation with the following parameters:
+ `Region`
+ `SourceDBInstanceArn`
+ `BackupRetentionPeriod`
+ `KmsKeyId` \(optional\)
+ `PreSignedUrl` \(required if you use `KmsKeyId`\)

**Note**  
If you encrypt the backups, you must also include a presigned URL\. For more information on presigned URLs, see [Authenticating Requests: Using Query Parameters \(AWS Signature Version 4\)](https://docs.aws.amazon.com/AmazonS3/latest/API/sigv4-query-string-auth.html) in the *Amazon Simple Storage Service API Reference* and [Signature Version 4 signing process](https://docs.aws.amazon.com/general/latest/gr/signature-version-4.html) in the *AWS General Reference*\.

## Finding information about replicated backups<a name="AutomatedBackups.Replicating.Describe"></a>

You can use the following CLI commands to find information about replicated backups:
+ [https://docs.aws.amazon.com/cli/latest/reference/rds/describe-source-regions.html](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-source-regions.html)
+ [https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-instances.html](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-instances.html)
+ [https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-instance-automated-backups.html](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-instance-automated-backups.html)

The following `describe-source-regions` example lists the source AWS Regions from which automated backups can be replicated to the US West \(Oregon\) destination Region\.

**To show information about source Regions**
+ Run the following command\.

  ```
  aws rds describe-source-regions --region us-west-2
  ```

The output shows that backups can be replicated from US East \(N\. Virginia\), but not from US East \(Ohio\) or US West \(N\. California\), into US West \(Oregon\)\.

```
{
    "SourceRegions": [
        ...
        {
            "RegionName": "us-east-1",
            "Endpoint": "https://rds.us-east-1.amazonaws.com",
            "Status": "available",
            "SupportsDBInstanceAutomatedBackupsReplication": true
        },
        {
            "RegionName": "us-east-2",
            "Endpoint": "https://rds.us-east-2.amazonaws.com",
            "Status": "available",
            "SupportsDBInstanceAutomatedBackupsReplication": false
        },
            "RegionName": "us-west-1",
            "Endpoint": "https://rds.us-west-1.amazonaws.com",
            "Status": "available",
            "SupportsDBInstanceAutomatedBackupsReplication": false
        }
    ]
}
```

The following `describe-db-instances` example shows the automated backups for a DB instance\.

**To show the replicated backups for a DB instance**
+ Run one of the following commands\.

  For Linux, macOS, or Unix:

  ```
  aws rds describe-db-instances \
  --db-instance-identifier mydatabase
  ```

  For Windows:

  ```
  aws rds describe-db-instances ^
  --db-instance-identifier mydatabase
  ```

The output includes the replicated backups\.

```
{
    "DBInstances": [
        {
            "StorageEncrypted": false,
            "Endpoint": {
                "HostedZoneId": "Z1PVIF0B656C1W",
                "Port": 1521,
            ...

            "BackupRetentionPeriod": 7,
            "DBInstanceAutomatedBackupsReplications": [{"DBInstanceAutomatedBackupsArn": "arn:aws:rds:us-east-1:123456789012:auto-backup:ab-L2IJCEXJP7XQ7HOJ4SIEXAMPLE"}]
        }
    ]
}
```

The following `describe-db-instance-automated-backups` example shows the automated backups for a DB instance\.

**To show automated backups for a DB instance**
+ Run one of the following commands\.

  For Linux, macOS, or Unix:

  ```
  aws rds describe-db-instance-automated-backups \
  --db-instance-identifier mydatabase
  ```

  For Windows:

  ```
  aws rds describe-db-instance-automated-backups ^
  --db-instance-identifier mydatabase
  ```

The output shows the source DB instance and automated backups in US West \(Oregon\), with backups replicated to US East \(N\. Virginia\)\.

```
{
    "DBInstanceAutomatedBackups": [
        {
            "DBInstanceArn": "arn:aws:rds:us-west-2:868710585169:db:mydatabase",
            "DbiResourceId": "db-L2IJCEXJP7XQ7HOJ4SIEXAMPLE",
            "DBInstanceAutomatedBackupsArn": "arn:aws:rds:us-west-2:123456789012:auto-backup:ab-L2IJCEXJP7XQ7HOJ4SIEXAMPLE",
            "BackupRetentionPeriod": 7,
            "DBInstanceAutomatedBackupsReplications": [{"DBInstanceAutomatedBackupsArn": "arn:aws:rds:us-east-1:123456789012:auto-backup:ab-L2IJCEXJP7XQ7HOJ4SIEXAMPLE"}]
            "Region": "us-west-2",
            "DBInstanceIdentifier": "mydatabase",
            "RestoreWindow": {
                "EarliestTime": "2020-10-26T01:09:07Z",
                "LatestTime": "2020-10-31T19:09:53Z",
            }
            ...
        }
    ]
}
```

The following `describe-db-instance-automated-backups` example uses the `--db-instance-automated-backups-arn` option to show the replicated backups in the destination Region\.

**To show replicated backups**
+ Run one of the following commands\.

  For Linux, macOS, or Unix:

  ```
  aws rds describe-db-instance-automated-backups \
  --db-instance-automated-backups-arn "arn:aws:rds:us-east-1:123456789012:auto-backup:ab-L2IJCEXJP7XQ7HOJ4SIEXAMPLE"
  ```

  For Windows:

  ```
  aws rds describe-db-instance-automated-backups ^
  --db-instance-automated-backups-arn "arn:aws:rds:us-east-1:123456789012:auto-backup:ab-L2IJCEXJP7XQ7HOJ4SIEXAMPLE"
  ```

The output shows the source DB instance in US West \(Oregon\), with replicated backups in US East \(N\. Virginia\)\.

```
{
    "DBInstanceAutomatedBackups": [
        {
            "DBInstanceArn": "arn:aws:rds:us-west-2:868710585169:db:mydatabase",
            "DbiResourceId": "db-L2IJCEXJP7XQ7HOJ4SIEXAMPLE",
            "DBInstanceAutomatedBackupsArn": "arn:aws:rds:us-east-1:123456789012:auto-backup:ab-L2IJCEXJP7XQ7HOJ4SIEXAMPLE",
            "Region": "us-west-2",
            "DBInstanceIdentifier": "mydatabase",
            "RestoreWindow": {
                "EarliestTime": "2020-10-26T01:09:07Z",
                "LatestTime": "2020-10-31T19:01:23Z"
            },
            "AllocatedStorage": 50,
            "BackupRetentionPeriod": 7,
            "Status": "replicating",
            "Port": 1521,
            ...
        }
    ]
}
```

## Restoring to a specified time from a replicated backup<a name="AutomatedBackups.PiTR"></a>

You can restore a DB instance to a specific point in time from a replicated backup using the Amazon RDS console\. You can also use the `restore-db-instance-to-point-in-time` AWS CLI command or the `RestoreDBInstanceToPointInTime` RDS API operation\.

For general information on point\-in\-time recovery \(PITR\), see [Restoring a DB instance to a specified time](USER_PIT.md)\.

**Note**  
On RDS for SQL Server, option groups aren't copied across AWS Regions when automated backups are replicated\. If you've associated a custom option group with your RDS for SQL Server DB instance, you can re\-create that option group in the destination Region\. Then restore the DB instance in the destination Region and associate the custom option group with it\. For more information, see [Working with option groups](USER_WorkingWithOptionGroups.md)\.

### Console<a name="AutomatedBackups.PiTR.Console"></a>

**To restore a DB instance to a specified time from a replicated backup**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. Choose the destination Region \(where backups are replicated to\) from the Region selector\.

1. In the navigation pane, choose **Automated backups**\.

1. On the **Replicated backups** tab, choose the DB instance that you want to restore\.

1. For **Actions**, choose **Restore to point in time**\.

1. Choose **Latest restorable time** to restore to the latest possible time, or choose **Custom** to choose a time\.

   If you chose **Custom**, enter the date and time that you want to restore the instance to\.
**Note**  
Times are shown in your local time zone, which is indicated by an offset from Coordinated Universal Time \(UTC\)\. For example, UTC\-5 is Eastern Standard Time/Central Daylight Time\.

1. For **DB instance identifier**, enter the name of the target restored DB instance\.

1. \(Optional\) Choose other options as needed, such as enabling autoscaling\.

1. Choose **Restore to point in time**\.

### AWS CLI<a name="AutomatedBackups.PiTR.CLI"></a>

Use the [https://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-instance-to-point-in-time.html](https://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-instance-to-point-in-time.html) AWS CLI command to create a new DB instance\.

**To restore a DB instance to a specified time from a replicated backup**
+ Run one of the following commands\.

  For Linux, macOS, or Unix:

  ```
  1. aws rds restore-db-instance-to-point-in-time \
  2.     --source-db-instance-automated-backups-arn "arn:aws:rds:us-east-1:123456789012:auto-backup:ab-L2IJCEXJP7XQ7HOJ4SIEXAMPLE" \
  3.     --target-db-instance-identifier mytargetdbinstance \
  4.     --restore-time 2020-10-14T23:45:00.000Z
  ```

  For Windows:

  ```
  1. aws rds restore-db-instance-to-point-in-time ^
  2.     --source-db-instance-automated-backups-arn "arn:aws:rds:us-east-1:123456789012:auto-backup:ab-L2IJCEXJP7XQ7HOJ4SIEXAMPLE" ^
  3.     --target-db-instance-identifier mytargetdbinstance ^
  4.     --restore-time 2020-10-14T23:45:00.000Z
  ```

### RDS API<a name="AutomatedBackups.PiTR.API"></a>

To restore a DB instance to a specified time, call the [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBInstanceToPointInTime.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBInstanceToPointInTime.html) Amazon RDS API operation with the following parameters:
+ `SourceDBInstanceAutomatedBackupsArn`
+ `TargetDBInstanceIdentifier`
+ `RestoreTime`

## Stopping automated backup replication<a name="AutomatedBackups.StopReplicating"></a>

You can stop backup replication for DB instances using the Amazon RDS console\. You can also use the `stop-db-instance-automated-backups-replication` AWS CLI command or the `StopDBInstanceAutomatedBackupsReplication` RDS API operation\.

Replicated backups are retained, subject to the backup retention period set when they were created\.

### Console<a name="AutomatedBackups.StopReplicating.Console"></a>

Stop backup replication from the **Automated backups** page in the source Region\.

**To stop backup replication to an AWS Region**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. Choose the source Region from the **Region selector**\.

1. In the navigation pane, choose **Automated backups**\.

1. On the **Current Region** tab, choose the DB instance for which you want to stop backup replication\.

1. For **Actions**, choose **Manage cross\-Region replication**\.

1. Under **Backup replication**, clear the **Enable replication to another AWS Region** check box\.

1. Choose **Save**\.

Replicated backups are listed on the **Retained** tab of the **Automated backups** page in the destination Region\.

### AWS CLI<a name="AutomatedBackups.StopReplicating.CLI"></a>

Stop backup replication by using the [https://docs.aws.amazon.com/cli/latest/reference/rds/stop-db-instance-automated-backups-replication.html](https://docs.aws.amazon.com/cli/latest/reference/rds/stop-db-instance-automated-backups-replication.html) AWS CLI command\.

The following CLI example stops automated backups of a DB instance from replicating in the US West \(Oregon\) Region\.

**To stop backup replication**
+ Run one of the following commands\.

  For Linux, macOS, or Unix:

  ```
  aws rds stop-db-instance-automated-backups-replication \
  --region us-east-1 \
  --source-db-instance-arn "arn:aws:rds:us-west-2:123456789012:db:mydatabase"
  ```

  For Windows:

  ```
  aws rds stop-db-instance-automated-backups-replication ^
  --region us-east-1 ^
  --source-db-instance-arn "arn:aws:rds:us-west-2:123456789012:db:mydatabase"
  ```

### RDS API<a name="AutomatedBackups.StopReplicating.API"></a>

Stop backup replication by using the [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_StopDBInstanceAutomatedBackupsReplication.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_StopDBInstanceAutomatedBackupsReplication.html) RDS API operation with the following parameters:
+ `Region`
+ `SourceDBInstanceArn`

## Deleting replicated backups<a name="AutomatedBackups.Delete"></a>

You can delete replicated backups for DB instances using the Amazon RDS console\. You can also use the `delete-db-instance-automated-backups` AWS CLI command or the `DeleteDBInstanceAutomatedBackup` RDS API operation\.

### Console<a name="AutomatedBackups.Delete.Console"></a>

Delete replicated backups in the destination Region from the **Automated backups** page\.

**To delete replicated backups**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. Choose the destination Region from the **Region selector**\.

1. In the navigation pane, choose **Automated backups**\.

1. On the **Replicated backups** tab, choose the DB instance for which you want to delete the replicated backups\.

1. For **Actions**, choose **Delete**\.

1. On the confirmation page, enter **delete me** and choose **Delete**\.

### AWS CLI<a name="AutomatedBackups.Delete.CLI"></a>

Delete replicated backups by using the [https://docs.aws.amazon.com/cli/latest/reference/rds/delete-db-instance-automated-backup.html](https://docs.aws.amazon.com/cli/latest/reference/rds/delete-db-instance-automated-backup.html) AWS CLI command\.

You can use the [https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-instances.html](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-instances.html) CLI command to find the Amazon Resource Names \(ARNs\) of the replicated backups\. For more information, see [Finding information about replicated backups](#AutomatedBackups.Replicating.Describe)\.

**To delete replicated backups**
+ Run one of the following commands\.

  For Linux, macOS, or Unix:

  ```
  aws rds delete-db-instance-automated-backup \
  --db-instance-automated-backups-arn "arn:aws:rds:us-east-1:123456789012:auto-backup:ab-L2IJCEXJP7XQ7HOJ4SIEXAMPLE"
  ```

  For Windows:

  ```
  aws rds delete-db-instance-automated-backup ^
  --db-instance-automated-backups-arn "arn:aws:rds:us-east-1:123456789012:auto-backup:ab-L2IJCEXJP7XQ7HOJ4SIEXAMPLE"
  ```

### RDS API<a name="AutomatedBackups.Delete.API"></a>

Delete replicated backups by using the [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DeleteDBInstanceAutomatedBackup.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DeleteDBInstanceAutomatedBackup.html) RDS API operation with the `DBInstanceAutomatedBackupsArn` parameter\.