# Modifying the RDS for Oracle replica mode<a name="oracle-read-replicas.changing-replica-mode"></a>

To change the replica mode of an existing replica, use the console, AWS CLI, or RDS API\. When you change to mounted mode, the replica disconnects all active connections\. When you change to read\-only mode, Amazon RDS initializes Active Data Guard\.

The change operation can take a few minutes\. During the operation, the DB instance status changes to **modifying**\. For more information about status changes, see [Viewing Amazon RDS DB instance status](accessing-monitoring.md#Overview.DBInstance.Status)\.

## Console<a name="oracle-read-replicas.changing-replica-mode.console"></a>

**To change the replica mode of an Oracle replica from mounted to read\-only**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose the mounted replica database\.

1. Choose **Modify**\.

1. For **Replica mode**, choose **Read\-only**\.

1. Choose the other settings that you want to change\.

1. Choose **Continue**\.

1. For **Scheduling of modifications**, choose **Apply immediately**\.

1. Choose **Modify DB instance**\.

## AWS CLI<a name="oracle-read-replicas.changing-replica-mode.cli"></a>

To change a read replica to mounted mode, set `--replica-mode` to `mounted` in the AWS CLI command [modify\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html)\. To change a mounted replica to read\-only mode, set `--replica-mode` to `open-read-only`\.

**Example**  
For Linux, macOS, or Unix:  

```
aws rds modify-db-instance \
    --db-instance-identifier myreadreplica \
    --replica-mode mode
```
For Windows:  

```
aws rds modify-db-instance ^
    --db-instance-identifier myreadreplica ^
    --replica-mode mode
```

## RDS API<a name="oracle-read-replicas.changing-replica-mode.api"></a>

To change a read\-only replica to mounted mode, set `ReplicaMode=mounted` in [ModifyDBInstance](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstanceReadReplica.html)\. To change a mounted replica to read\-only mode, set `ReplicaMode=read-only`\.