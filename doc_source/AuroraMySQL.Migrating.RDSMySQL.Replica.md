# Migrating Data from a MySQL DB Instance to an Amazon Aurora MySQL DB Cluster by Using an Aurora Read Replica<a name="AuroraMySQL.Migrating.RDSMySQL.Replica"></a>

Amazon RDS uses the MySQL DB engines' binary log replication functionality to create a special type of DB cluster called an Aurora Read Replica for a source MySQL DB instance\. Updates made to the source MySQL DB instance are asynchronously replicated to the Aurora Read Replica\. 

We recommend using this functionality to migrate from a MySQL DB instance to an Aurora MySQL DB cluster by creating an Aurora Read Replica of your source MySQL DB instance\. When the replica lag between the MySQL DB instance and the Aurora Read Replica is 0, you can direct your client applications to the Aurora Read Replica and then stop replication to make the Aurora Read Replica a standalone Aurora MySQL DB cluster\. Be prepared for migration to take a while, roughly several hours per tebibyte \(TiB\) of data\.

For a list of regions where Aurora is available, see [Availability for Amazon Aurora MySQL](Aurora.AuroraMySQL.md#Aurora.AuroraMySQL.Availability)\.

When you create an Aurora Read Replica of a MySQL DB instance, Amazon RDS creates a DB snapshot of your source MySQL DB instance \(private to Amazon RDS, and incurring no charges\)\. Amazon RDS then migrates the data from the DB snapshot to the Aurora Read Replica\. After the data from the DB snapshot has been migrated to the new Aurora MySQL DB cluster, Amazon RDS starts replication between your MySQL DB instance and the Aurora MySQL DB cluster\. If your MySQL DB instance contains tables that use storage engines other than InnoDB, or that use compressed row format, you can speed up the process of creating an Aurora Read Replica by altering those tables to use the InnoDB storage engine and dynamic row format before you create your Aurora Read Replica\. For more information about the process of copying a MySQL DB snapshot to an Aurora MySQL DB cluster, see [Migrating Data from a MySQL DB Instance to an Amazon Aurora MySQL DB Cluster by Using a DB Snapshot](AuroraMySQL.Migrating.RDSMySQL.md)\.

You can only have one Aurora Read Replica for a MySQL DB instance\.

**Note**  
Replication issues can arise due to feature differences between Amazon Aurora MySQL and the MySQL database engine version of your RDS MySQL DB instance that is the replication master\. If you encounter an error, you can find help in the [Amazon RDS Community Forum](https://forums.aws.amazon.com/forum.jspa?forumID=60) or by contacting AWS Support\.

For more information on MySQL Read Replicas, see [Working with Read Replicas of MariaDB, MySQL, and PostgreSQL DB Instances](USER_ReadRepl.md)\.

## Creating an Aurora Read Replica<a name="AuroraMySQL.Migrating.RDSMySQL.Replica.Create"></a>

You can create an Aurora Read Replica for a MySQL DB instance by using the console or the AWS CLI\.

### AWS Management Console<a name="AuroraMySQL.Migrating.RDSMySQL.Replica.Create.Console"></a>

**To create an Aurora Read Replica from a source MySQL DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Instances**\. 

1. Choose the MySQL DB instance that you want to use as the source for your Aurora Read Replica and choose **Create Aurora read replica** from **Instance actions**\.

1. Choose the DB cluster specifications you want to use for the Aurora Read Replica, as described in the following table\.     
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/AuroraMySQL.Migrating.RDSMySQL.Replica.html)

1. Choose **Create read replica**\.

### CLI<a name="AuroraMySQL.Migrating.RDSMySQL.Replica.Create.CLI"></a>

To create an Aurora Read Replica from a source MySQL DB instance, use the [http://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html](http://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html) and [ `create-db-instance`](http://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html) AWS CLI commands to create a new Aurora MySQL DB cluster\. When you call the `create-db-cluster` command, include the `--replication-source-identifier` parameter to identify the Amazon Resource Name \(ARN\) for the source MySQL DB instance\. For more information about Amazon RDS ARNs, see [Amazon Relational Database Service \(Amazon RDS\)](http://docs.aws.amazon.com/general/latest/gr/aws-arns-and-namespaces.html#arn-syntax-rds)\.

Don't specify the master username, master password, or database name as the Aurora Read Replica uses the same master username, master password, and database name as the source MySQL DB instance\. 

For Linux, OS X, or Unix:

```
aws rds create-db-cluster --db-cluster-identifier sample-replica-cluster --engine aurora \
    --db-subnet-group-name mysubnetgroup --vpc-security-group-ids sg-c7e5b0d2 \
    --replication-source-identifier arn:aws:rds:us-west-2:123456789012:db:master-mysql-instance
```

For Windows:

```
aws rds create-db-cluster --db-cluster-identifier sample-replica-cluster --engine aurora ^
    --db-subnet-group-name mysubnetgroup --vpc-security-group-ids sg-c7e5b0d2 ^
    --replication-source-identifier arn:aws:rds:us-west-2:123456789012:db:master-mysql-instance
```

If you use the console to create an Aurora Read Replica, then Amazon RDS automatically creates the primary instance for your DB cluster Aurora Read Replica\. If you use the AWS CLI to create an Aurora Read Replica, you must explicitly create the primary instance for your DB cluster\. The primary instance is the first instance that is created in a DB cluster\.

You can create a primary instance for your DB cluster by using the [http://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html](http://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html) AWS CLI command with the following parameters\.
+ `--db-cluster-identifier`

  The name of your DB cluster\.
+ `--db-instance-class`

  The name of the DB instance class to use for your primary instance\.
+ `--db-instance-identifier`

  The name of your primary instance\.
+ `--engine aurora`

In this example, you create a primary instance named *myreadreplicainstance* for the DB cluster named *myreadreplicacluster*, using the DB instance class specified in *myinstanceclass*\.

**Example**  
For Linux, OS X, or Unix:  

```
aws rds create-db-instance \
    --db-cluster-identifier myreadreplicacluster \
    --db-instance-class myinstanceclass
    --db-instance-identifier myreadreplicainstance \
    --engine aurora
```
For Windows:  

```
aws rds create-db-instance \
    --db-cluster-identifier myreadreplicacluster \
    --db-instance-class myinstanceclass
    --db-instance-identifier myreadreplicainstance \
    --engine aurora
```

### API<a name="Aurora.Migration.RDSMySQL.Create.API"></a>

To create an Aurora Read Replica from a source MySQL DB instance, use the [http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html) and [http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html) Amazon RDS API commands to create a new Aurora DB cluster and primary instance\. Do not specify the master username, master password, or database name as the Aurora Read Replica uses the same master username, master password, and database name as the source MySQL DB instance\. 

You can create a new Aurora DB cluster for an Aurora Read Replica from a source MySQL DB instance by using the [http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html) Amazon RDS API command with the following parameters:
+ `DBClusterIdentifier`

  The name of the DB cluster to create\.
+ `DBSubnetGroupName`

  The name of the DB subnet group to associate with this DB cluster\.
+ `Engine=aurora`
+ `KmsKeyId`

  The AWS Key Management Service \(AWS KMS\) encryption key to optionally encrypt the DB cluster with, depending on whether your MySQL DB instance is encrypted\.
  + If your MySQL DB instance isn't encrypted, specify an encryption key to have your DB cluster encrypted at rest\. Otherwise, your DB cluster is encrypted at rest using the default encryption key for your account\.
  + If your MySQL DB instance is encrypted, specify an encryption key to have your DB cluster encrypted at rest using the specified encryption key\. Otherwise, your DB cluster is encrypted at rest using the encryption key for the MySQL DB instance\.
**Note**  
You can't create an unencrypted DB cluster from an encrypted MySQL DB instance\.
+ `ReplicationSourceIdentifier`

  The Amazon Resource Name \(ARN\) for the source MySQL DB instance\. For more information about Amazon RDS ARNs, see [Amazon Relational Database Service \(Amazon RDS\)](http://docs.aws.amazon.com/general/latest/gr/aws-arns-and-namespaces.html#arn-syntax-rds)\. 
+ `VpcSecurityGroupIds`

  The list of EC2 VPC security groups to associate with this DB cluster\.

In this example, you create a DB cluster named *myreadreplicacluster* from a source MySQL DB instance with an ARN set to *mysqlmasterARN*, associated with a DB subnet group named *mysubnetgroup* and a VPC security group named *mysecuritygroup*\.

**Example**  

```
https://rds.us-east-1.amazonaws.com/
    ?Action=CreateDBCluster
    &DBClusterIdentifier=myreadreplicacluster
    &DBSubnetGroupName=mysubnetgroup
    &Engine=aurora
    &ReplicationSourceIdentifier=mysqlmasterARN
    &SignatureMethod=HmacSHA256
    &SignatureVersion=4
    &Version=2014-10-31
    &VpcSecurityGroupIds=mysecuritygroup
    &X-Amz-Algorithm=AWS4-HMAC-SHA256
    &X-Amz-Credential=AKIADQKE4SARGYLE/20150927/us-east-1/rds/aws4_request
    &X-Amz-Date=20150927T164851Z
    &X-Amz-SignedHeaders=content-type;host;user-agent;x-amz-content-sha256;x-amz-date
    &X-Amz-Signature=6a8f4bd6a98f649c75ea04a6b3929ecc75ac09739588391cd7250f5280e716db
```

If you use the console to create an Aurora Read Replica, then Amazon RDS automatically creates the primary instance for your DB cluster Aurora Read Replica\. If you use the AWS CLI to create an Aurora Read Replica, you must explicitly create the primary instance for your DB cluster\. The primary instance is the first instance that is created in a DB cluster\.

You can create a primary instance for your DB cluster by using the [http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html) Amazon RDS API command with the following parameters:
+ `DBClusterIdentifier`

  The name of your DB cluster\.
+ `DBInstanceClass`

  The name of the DB instance class to use for your primary instance\.
+ `DBInstanceIdentifier`

  The name of your primary instance\.
+ `Engine=aurora`

In this example, you create a primary instance named *myreadreplicainstance* for the DB cluster named *myreadreplicacluster*, using the DB instance class specified in *myinstanceclass*\.

**Example**  

```
https://rds.us-east-1.amazonaws.com/
    ?Action=CreateDBInstance
    &DBClusterIdentifier=myreadreplicacluster
    &DBInstanceClass=myinstanceclass
    &DBInstanceIdentifier=myreadreplicainstance
    &Engine=aurora
    &SignatureMethod=HmacSHA256
    &SignatureVersion=4
    &Version=2014-09-01
    &X-Amz-Algorithm=AWS4-HMAC-SHA256
    &X-Amz-Credential=AKIADQKE4SARGYLE/20140424/us-east-1/rds/aws4_request
    &X-Amz-Date=20140424T194844Z
    &X-Amz-SignedHeaders=content-type;host;user-agent;x-amz-content-sha256;x-amz-date
    &X-Amz-Signature=bee4aabc750bf7dad0cd9e22b952bd6089d91e2a16592c2293e532eeaab8bc77
```

## Viewing an Aurora Read Replica<a name="AuroraMySQL.Migrating.RDSMySQL.Replica.View"></a>

You can view the MySQL to Aurora MySQL replication relationships for your Aurora MySQL DB clusters by using the AWS Management Console or the AWS CLI\.

### AWS Management Console<a name="AuroraMySQL.Migrating.RDSMySQL.Replica.View.Console"></a>

**To view the master MySQL DB instance for an Aurora Read Replica**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Clusters**\. 

1. Click the DB cluster for the Aurora Read Replica to display its details\. The master MySQL DB instance information will be in the **Replication source** field\.  
![\[View MySQL master\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/aurora-repl6.png)

### CLI<a name="AuroraMySQL.Migrating.RDSMySQL.Replica.View.CLI"></a>

To view the MySQL to Aurora MySQL replication relationships for your Aurora MySQL DB clusters by using the AWS CLI, use the [http://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-clusters.html](http://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-clusters.html) and [http://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-instances.html](http://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-instances.html) commands\. 

To determine which MySQL DB instance is the master, use the [http://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-clusters.html](http://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-clusters.html) and specify the cluster identifier of the Aurora Read Replica for the `--db-cluster-identifier` option\. Refer to the `ReplicationSourceIdentifier` element in the output for the ARN of the DB instance that is the replication master\. 

To determine which DB cluster is the Aurora Read Replica, use the [http://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-instances.html](http://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-instances.html) and specify the instance identifier of the MySQL DB instance for the `--db-instance-identifier` option\. Refer to the `ReadReplicaDBClusterIdentifiers` element in the output for the DB cluster identifier of the Aurora Read Replica\. 

**Example**  
For Linux, OS X, or Unix:  

```
aws rds describe-db-clusters \
    --db-cluster-identifier myreadreplicacluster
```

```
aws rds describe-db-instances \
    --db-instance-identifier mysqlmaster
```
For Windows:  

```
aws rds describe-db-clusters ^
    --db-cluster-identifier myreadreplicacluster
```

```
aws rds describe-db-instances ^
    --db-instance-identifier mysqlmaster
```

## Promoting an Aurora Read Replica<a name="AuroraMySQL.Migrating.RDSMySQL.Replica.Promote"></a>

After migration completes, you can promote the Aurora Read Replica to a stand\-alone DB cluster and direct your client applications to the endpoint for the Aurora Read Replica\. For more information on the Aurora endpoints, see [Aurora Endpoints](Aurora.Overview.md#Aurora.Overview.Endpoints)\. Promotion should complete fairly quickly, and you can read from and write to the Aurora Read Replica during promotion\. However, you can't delete the master MySQL DB instance or unlink the DB Instance and the Aurora Read Replica during this time\.

Before you promote your Aurora Read Replica, stop any transactions from being written to the source MySQL DB instance, and then wait for the replica lag on the Aurora Read Replica to reach 0\. You can view the replica lag for an Aurora Read Replica by calling the `SHOW SLAVE STATUS` command on your Aurora Read Replica and reading the **Seconds behind master** value\. 

You can start writing to the Aurora Read Replica after write transactions to the master have stopped and replica lag is 0\. If you write to the Aurora Read Replica before this and you modify tables that are also being modified on the MySQL master, you risk breaking replication to Aurora\. If this happens, you must delete and recreate your Aurora Read Replica\.

After you promote, confirm that the promotion has completed by choosing **Instances** in the navigation pane and confirming that there is a **Promoted Read Replica cluster to stand\-alone database cluster** event for the Aurora Read Replica\. After promotion is complete, the master MySQL DB Instance and the Aurora Read Replica are unlinked, and you can safely delete the DB instance if you want to\.

### AWS Management Console<a name="AuroraMySQL.Migrating.RDSMySQL.Replica.Promote.Console"></a>

**To promote an Aurora Read Replica to an Aurora DB cluster**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Instances**\. 

1. Choose the DB instance for the Aurora Read Replica and choose **Promote read replica** from **Instance actions**\. 

1. Choose **Promote Read Replica**\.

### CLI<a name="AuroraMySQL.Migrating.RDSMySQL.Replica.Promote.CLI"></a>

To promote an Aurora Read Replica to a stand\-alone DB cluster, use the [http://docs.aws.amazon.com/cli/latest/reference/rds/promote-read-replica-db-cluster.html](http://docs.aws.amazon.com/cli/latest/reference/rds/promote-read-replica-db-cluster.html) AWS CLI command\. 

**Example**  
For Linux, OS X, or Unix:  

```
aws rds promote-read-replica-db-cluster \
    --db-cluster-identifier myreadreplicacluster
```
For Windows:  

```
aws rds promote-read-replica-db-cluster ^
    --db-cluster-identifier myreadreplicacluster
```

## Related Topics<a name="AuroraMySQL.Migrating.RDSMySQL.Replica.RelatedTopics"></a>
+ [Migrating Data to an Amazon Aurora DB Cluster](Aurora.Migrate.md)
+ [Replication with Amazon Aurora](Aurora.Replication.md)
+ [Amazon Aurora on Amazon RDS](CHAP_Aurora.md)