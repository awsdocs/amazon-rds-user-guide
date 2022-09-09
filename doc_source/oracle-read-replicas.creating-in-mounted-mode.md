# Creating an RDS for Oracle replica in mounted mode<a name="oracle-read-replicas.creating-in-mounted-mode"></a>

By default, Oracle replicas are read\-only\. To create a replica in mounted mode, use the console, the AWS CLI, or the RDS API\.

## Console<a name="oracle-read-replicas.creating-in-mounted-mode.console"></a>

**To create a mounted replica from a source Oracle DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose the Oracle DB instance that you want to use as the source for a mounted replica\.

1. For **Actions**, choose **Create replica**\. 

1. For **Replica mode**, choose **Mounted**\.

1. Choose the settings that you want to use\. For **DB instance identifier**, enter a name for the read replica\. Adjust other settings as needed\.

1. For **Regions**, choose the Region where the mounted replica will be launched\. 

1. Choose your instance size and storage type\. We recommend that you use the same DB instance class and storage type as the source DB instance for the read replica\.

1. For **Multi\-AZ deployment**, choose **Create a standby instance** to create a standby of your replica in another Availability Zone for failover support for the mounted replica\. Creating your mounted replica as a Multi\-AZ DB instance is independent of whether the source database is a Multi\-AZ DB instance\.

1. Choose the other settings that you want to use\.

1. Choose **Create replica**\.

In the **Databases** page, the mounted replica has the role Replica\.

## AWS CLI<a name="oracle-read-replicas.creating-in-mounted-mode.cli"></a>

To create an Oracle replica in mounted mode, set `--replica-mode` to `mounted` in the AWS CLI command [create\-db\-instance\-read\-replica](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance-read-replica.html)\.

**Example**  
For Linux, macOS, or Unix:  

```
aws rds create-db-instance-read-replica \
    --db-instance-identifier myreadreplica \
    --source-db-instance-identifier mydbinstance \
    --replica-mode mounted
```
For Windows:  

```
aws rds create-db-instance-read-replica ^
    --db-instance-identifier myreadreplica ^
    --source-db-instance-identifier mydbinstance ^
    --replica-mode mounted
```

To change a read\-only replica to a mounted state, set `--replica-mode` to `mounted` in the AWS CLI command [modify\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html)\. To place a mounted replica in read\-only mode, set `--replica-mode` to `open-read-only`\. 

## RDS API<a name="oracle-read-replicas.creating-in-mounted-mode.api"></a>

To create an Oracle replica in mounted mode, specify `ReplicaMode=mounted` in the RDS API operation [CreateDBInstanceReadReplica](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstanceReadReplica.html)\.