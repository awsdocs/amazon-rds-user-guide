# Migrating Data from a PostgreSQL DB Instance to an Aurora PostgreSQL DB Cluster by Using an Aurora Read Replica<a name="AuroraPostgreSQL.Migrating.RDSPostgreSQL.Replica"></a>

Amazon RDS uses the PostgreSQL DB engines' streaming replication functionality to create a special type of DB cluster called an Aurora Read Replica for a source PostgreSQL DB instance\. Updates made to the source PostgreSQL DB instance are asynchronously replicated to the Aurora Read Replica\. 

We recommend using this functionality to migrate from a PostgreSQL DB instance to an Aurora PostgreSQL DB cluster by creating an Aurora Read Replica of your source PostgreSQL DB instance\. When the replica lag between the PostgreSQL DB instance and the Aurora Read Replica is 0, you can promote the Aurora Read Replica to be a standalone Aurora PostgreSQL DB cluster which can accept write loads\. Be prepared for migration to take a while, roughly several hours per terabyte \(TB\) of data\. While the migration is in progress, your Amazon RDS PostgreSQL instance will accumulate Write Ahead Log Segments \(WAL\)\. You should make sure your Amazon RDS instance has sufficient storage capacity for these segments\. 

For a list of regions where Aurora is available, see [Availability for Amazon Aurora PostgreSQL](Aurora.AuroraPostgreSQL.md#Aurora.AuroraPostgreSQL.Availability)\.

When you create an Aurora Read Replica of a PostgreSQL DB instance, Amazon RDS creates a DB snapshot of your source PostgreSQL DB instance \(private to Amazon RDS, and incurring no charges\)\. Amazon RDS then migrates the data from the DB snapshot to the Aurora Read Replica\. After the data from the DB snapshot has been migrated to the new Aurora PostgreSQL DB cluster, Amazon RDS starts replication between your PostgreSQL DB instance and the Aurora PostgreSQL DB cluster\. 

You can only have one Aurora Read Replica for a PostgreSQL DB instance\. Also, if you attempt to create an Aurora Read Replica for your Amazon RDS PostgreSQL instance and you already have a Read Replica, the request will be rejected\. 

**Note**  
Replication issues can arise due to feature differences between Amazon Aurora PostgreSQL and the PostgreSQL database engine version of your Amazon RDS PostgreSQL DB instance that is the replication master\. You can only replicate from an Amazon RDS PostgreSQL instance that is compatible with the Aurora PostgreSQL version\. For example, if the supported Aurora PostgreSQL version is 9\.6\.3, the Amazon RDS PostgreSQL DB instance must be running versions 9\.6\.1 or greater\. If you encounter an error, you can find help in the [Amazon RDS Community Forum](https://forums.aws.amazon.com/forum.jspa?forumID=60) or by contacting AWS Support\.

For more information on PostgreSQL Read Replicas, see [Working with Read Replicas of MariaDB, MySQL, and PostgreSQL DB Instances](USER_ReadRepl.md)\.

## Prepare to migrate data from Amazon RDS PostgreSQL to Aurora PostgreSQL<a name="AuroraPostgreSQL.Migrating.RDSPostgreSQL.Replica.Prepare"></a>

 Before you migrate data from your Amazon RDS PostgreSQL instance to an Aurora PostgreSQL cluster you should make sure your instance has sufficient storage capacity for the Write Ahead Log Segments \(WAL\) that accumulate during the migration\. There are several metrics you can use to check for this\. 


| Metric | Description | 
| --- | --- | 
|  FreeStorageSpace  |  The available storage space\. Units: Bytes  | 
|  OldestReplicationSlotLag  |  The size of the lag for WAL data in the replica that is lagging the most\. Units: Megabytes  | 
|  RDSToAuroraPostgreSQLReplicaLag  |  The amount of time in seconds an Aurora PostgreSQL DB cluster lags behind the source RDS database instance\.  | 
|  TransactionLogsDiskUsage  |  The disk space used by the transaction logs\. Units: Megabytes  | 

For more information about monitoring your Amazon RDS instance see [[ERROR] BAD/MISSING LINK TEXT](CHAP_Monitoring.md)\.

## Creating an Aurora Read Replica<a name="AuroraPostgreSQL.Migrating.RDSPostgreSQL.Replica.Create"></a>

You can create an Aurora Read Replica for a PostgreSQL DB instance by using the console or the AWS CLI\.

### AWS Management Console<a name="AuroraPostgreSQL.Migrating.RDSPostgreSQL.Replica.Create.Console"></a>

**To create an Aurora Read Replica from a source PostgreSQL DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Instances**\. 

1. Choose the PostgreSQL DB instance that you want to use as the source for your Aurora Read Replica and choose **Create Aurora Read Replica** from **Instance Actions**\.   
![\[Create Aurora Read Replica\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/Aurorapgres-migrate.png)

1. Choose the DB cluster specifications you want to use for the Aurora Read Replica, as described in the following table\.     
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/AuroraPostgreSQL.Migrating.RDSPostgreSQL.Replica.html)

1. Choose **Create Read Replica**\.

### CLI<a name="AuroraPostgreSQL.Migrating.RDSPostgreSQL.Replica.Create.CLI"></a>

To create an Aurora Read Replica from a source PostgreSQL DB instance, use the [http://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html](http://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html) and [ create\-db\-instance](http://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html) AWS CLI commands to create a new Aurora PostgreSQL DB cluster\. When you call the create\-db\-cluster command, include the `--replication-source-identifier` parameter to identify the Amazon Resource Name \(ARN\) for the source MySQL DB instance\. For more information about Amazon RDS ARNs, see [Amazon Relational Database Service \(Amazon RDS\)](http://docs.aws.amazon.com/general/latest/gr/aws-arns-and-namespaces.html#arn-syntax-rds)\.

Don't specify the master username, master password, or database name as the Aurora Read Replica uses the same master username, master password, and database name as the source PostgreSQL DB instance\. 

For Linux, OS X, or Unix:

```
aws rds create-db-cluster --db-cluster-identifier sample-replica-cluster --engine aurora-postgresql \
    --db-subnet-group-name mysubnetgroup --vpc-security-group-ids sg-c7e5b0d2 \
    --replication-source-identifier arn:aws:rds:us-west-2:123456789012:db:master-postgresql-instance
```

For Windows:

```
aws rds create-db-cluster --db-cluster-identifier sample-replica-cluster --engine aurora-postgresql ^
    --db-subnet-group-name mysubnetgroup --vpc-security-group-ids sg-c7e5b0d2 ^
    --replication-source-identifier arn:aws:rds:us-west-2:123456789012:db:master-postgresql-instance
```

If you use the console to create an Aurora Read Replica, then Amazon RDS automatically creates the primary instance for your DB cluster Aurora Read Replica\. If you use the AWS CLI to create an Aurora Read Replica, you must explicitly create the primary instance for your DB cluster\. The primary instance is the first instance that is created in a DB cluster\.

You can create a primary instance for your DB cluster by using the [http://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html](http://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html) AWS CLI command with the following parameters\.

+ `--db-cluster-identifier`

  The name of your DB cluster\.

+ `--db-instance-class`

  The name of the DB instance class to use for your primary instance\.

+ `--db-instance-identifier`

  The name of your primary instance\.

+ `--engine aurora-postgresql`

  The database engine to use\.

In this example, you create a primary instance named *myreadreplicainstance* for the DB cluster named *myreadreplicacluster*, using the DB instance class specified in *myinstanceclass*\.

**Example**  
For Linux, OS X, or Unix:  

```
aws rds create-db-instance \
    --db-cluster-identifier myreadreplicacluster \
    --db-instance-class myinstanceclass
    --db-instance-identifier myreadreplicainstance \
    --engine aurora-postgresql
```
For Windows:  

```
aws rds create-db-instance \
    --db-cluster-identifier myreadreplicacluster \
    --db-instance-class myinstanceclass
    --db-instance-identifier myreadreplicainstance \
    --engine aurora-postgresql
```

### API<a name="Aurora.Migration.RDSPostgreSQL.Create.API"></a>

To create an Aurora Read Replica from a source PostgreSQL DB instance, use the [http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html) and [http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html) Amazon RDS API commands to create a new Aurora DB cluster and primary instance\. Do not specify the master username, master password, or database name as the Aurora Read Replica uses the same master username, master password, and database name as the source PostgreSQL DB instance\. 

You can create a new Aurora DB cluster for an Aurora Read Replica from a source PostgreSQL DB instance by using the [http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html) Amazon RDS API command with the following parameters:

+ `DBClusterIdentifier`

  The name of the DB cluster to create\.

+ `DBSubnetGroupName`

  The name of the DB subnet group to associate with this DB cluster\.

+ `Engine=aurora-postgresql`

  The name of the engine to use\.

+ `ReplicationSourceIdentifier`

  The Amazon Resource Name \(ARN\) for the source PostgreSQL DB instance\. For more information about Amazon RDS ARNs, see [Amazon Relational Database Service \(Amazon RDS\)](http://docs.aws.amazon.com/general/latest/gr/aws-arns-and-namespaces.html#arn-syntax-rds)\. 

+ `VpcSecurityGroupIds`

  The list of EC2 VPC security groups to associate with this DB cluster\.

In this example, you create a DB cluster named *myreadreplicacluster* from a source PostgrSQL DB instance with an ARN set to *mysqlmasterARN*, associated with a DB subnet group named *mysubnetgroup* and a VPC security group named *mysecuritygroup*\.

**Example**  

```
https://rds.us-east-1.amazonaws.com/
    ?Action=CreateDBCluster
    &DBClusterIdentifier=myreadreplicacluster
    &DBSubnetGroupName=mysubnetgroup
    &Engine=aurora-postgresql
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

+ `Engine=aurora-postgresql`

  The name of the engine to use\.

In this example, you create a primary instance named *myreadreplicainstance* for the DB cluster named *myreadreplicacluster*, using the DB instance class specified in *myinstanceclass*\.

**Example**  

```
https://rds.us-east-1.amazonaws.com/
    ?Action=CreateDBInstance
    &DBClusterIdentifier=myreadreplicacluster
    &DBInstanceClass=myinstanceclass
    &DBInstanceIdentifier=myreadreplicainstance
    &Engine=aurora-postgresql
    &SignatureMethod=HmacSHA256
    &SignatureVersion=4
    &Version=2014-09-01
    &X-Amz-Algorithm=AWS4-HMAC-SHA256
    &X-Amz-Credential=AKIADQKE4SARGYLE/20140424/us-east-1/rds/aws4_request
    &X-Amz-Date=20140424T194844Z
    &X-Amz-SignedHeaders=content-type;host;user-agent;x-amz-content-sha256;x-amz-date
    &X-Amz-Signature=bee4aabc750bf7dad0cd9e22b952bd6089d91e2a16592c2293e532eeaab8bc77
```

## Promoting an Aurora Read Replica<a name="AuroraPostgreSQL.Migrating.RDSPostgreSQL.Replica.Promote"></a>

After migration completes, you can promote the Aurora Read Replica to a stand\-alone DB cluster and direct your client applications to the endpoint for the Aurora Read Replica\. For more information on the Aurora endpoints, see [Aurora Endpoints](Aurora.Overview.md#Aurora.Overview.Endpoints)\. Promotion should complete fairly quickly\. You can't delete the master PostgreSQL DB instance or unlink the DB Instance and the Aurora Read Replica until the promotion is complete\.

Before you promote your Aurora Read Replica, stop any transactions from being written to the source PostgreSQL DB instance, and then wait for the replica lag on the Aurora Read Replica to reach 0\.  

After you promote, confirm that the promotion has completed by choosing **Instances** in the navigation pane and confirming that there is a **Promoted Read Replica cluster to stand\-alone database cluster** event for the Aurora Read Replica\. After promotion is complete, the master PostgreSQL DB Instance and the Aurora Read Replica are unlinked, and you can safely delete the DB instance if you want to\.

### AWS Management Console<a name="AuroraPostgreSQL.Migrating.RDSPostgreSQL.Replica.Promote.Console"></a>

**To promote an Aurora Read Replica to an Aurora DB cluster**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Instances**\. 

1. Choose the DB instance for the Aurora Read Replica and choose **Promote Read Replica** from **Instance Actions**\. 

1. Choose **Promote Read Replica**\.

### CLI<a name="AuroraPostgreSQL.Migrating.RDSPostgreSQL.Replica.Promote.CLI"></a>

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

## Related Topics<a name="AuroraPostgreSQL.Migrating.RDSPostgreSQL.RelatedTopics"></a>

+ [Amazon Aurora on Amazon RDS](CHAP_Aurora.md)

+ [Migrating Data to an Amazon Aurora DB Cluster](Aurora.Migrate.md)