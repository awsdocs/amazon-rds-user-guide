# Creating an Amazon Aurora DB Cluster<a name="Aurora.CreateInstance"></a>

An Amazon Aurora DB cluster consists of a DB instance, compatible with either MySQL or PostgreSQL, and a cluster volume that represents the data for the DB cluster, copied across three Availability Zones as a single, virtual volume\. The DB cluster contains a primary instance and, optionally, up to 15 Aurora Replicas\. For more information about Aurora DB clusters, see [Overview of Amazon Aurora](Aurora.Overview.md)\.

The following topic shows how to create an Aurora DB cluster and then add an Aurora Replica for that DB cluster\.

**Important**  
You must complete the tasks in the [Setting Up for Amazon RDS](CHAP_SettingUp.md) section before you can create an Aurora DB cluster\.

This topic describes how you can create an Aurora DB cluster using either the AWS Management Console or the AWS CLI\. For simple instructions on connecting to your Aurora DB cluster, see [Connecting to an Amazon Aurora DB Cluster](Aurora.Connecting.md)\. For a detailed guide on connecting to an Amazon Aurora DB cluster, see [RDS Aurora Connectivity](https://s3-us-west-2.amazonaws.com/jsmiley-share/Aurora/RDS+Aurora+Connectivity+Guide+-+v4.pdf)\.

## DB Cluster Prerequisites<a name="Aurora.CreateInstance.Prerequisites"></a>

The following are prerequisites to create a DB cluster\.

### VPC<a name="Aurora.CreateInstance.Prerequisites.VPC"></a>

You can only create an Amazon Aurora DB cluster in a Virtual Private Cloud \(VPC\) that spans two Availability Zones, and each zone must contain at least one subnet\. By distributing your cluster instances across at least two Availability Zones, you ensure that there will be instances available in your DB cluster in the unlikely case of an Availability Zone failure\. Note that the cluster volume for your Aurora DB cluster will always span three Availability Zones to provide durable storage with less possibility of data loss\. 

If you are using the AWS Management Console to create your Aurora DB cluster, then you can have Amazon RDS automatically create a VPC for you\. Alternatively, you can use an existing VPC or create a new VPC for your Aurora DB cluster\. Your VPC must have at least one subnet in each of at least two Availability Zones in order for you to use it with an Amazon Aurora DB cluster\. For more information, see [How to Create a VPC for Use with Amazon Aurora](Aurora.CreateVPC.md)\. For information on VPCs, see [Amazon Virtual Private Cloud \(VPCs\) and Amazon RDS](USER_VPC.md)\.

**Note**  
You can communicate with an EC2 instance that is not in a VPC and an Amazon Aurora DB cluster using ClassicLink\. For more information, see [A DB Instance in a VPC Accessed by an EC2 Instance Not in a VPC](USER_VPC.Scenarios.md#USER_VPC.ClassicLink)\.

If you don't have a default VPC or you have not created a VPC, you can have Amazon RDS automatically create a VPC for you when you create an Aurora DB cluster using the AWS Management Console\. Otherwise, you must do the following:
+ Create a VPC with at least one subnet in each of at least two of the Availability Zones in the region where you want to deploy your DB cluster\. For more information, see [How to Create a VPC for Use with Amazon Aurora](Aurora.CreateVPC.md)\.
+ Specify a VPC security group that authorizes connections to your Aurora DB cluster\. For more information, see [Working with a DB Instance in a VPC](USER_VPC.WorkingWithRDSInstanceinaVPC.md#Overview.RDSVPC.Create)\.
+ Specify an RDS DB subnet group that defines at least two subnets in the VPC that can be used by the Aurora DB cluster\. For more information, see [Working with DB Subnet Groups](USER_VPC.WorkingWithRDSInstanceinaVPC.md#USER_VPC.Subnets)\.

### Additional Prerequisites<a name="Aurora.CreateInstance.Prerequisites.Additional"></a>
+ If you are connecting to AWS using IAM credentials, your IAM account must have IAM policies that grant the permissions required to perform Amazon RDS operations\. For more information, see [Authentication and Access Control for Amazon RDS](UsingWithRDS.IAM.md)\.

  If you are using an IAM account to access the Amazon RDS console, you must first log on to the AWS Management Console with your IAM account, and then go to the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.
+ If you want to tailor the configuration parameters for your DB cluster, you must specify a DB cluster parameter group and DB parameter group with the required parameter settings\. For information about creating or modifying a DB cluster parameter group or DB parameter group, see [Working with DB Parameter Groups](USER_WorkingWithParamGroups.md)\.
+ You must determine the TCP/IP port number you will specify for your DB cluster\. The firewalls at some companies block connections to the default ports \(3306 for MySQL, 5432 for PostgreSQL\) for Aurora\. If your company firewall blocks the default port, choose another port for your DB cluster\. All instances in a DB cluster use the same port\.

## AWS Management Console<a name="Aurora.CreateInstance.Console"></a>

### Launching an Aurora DB Cluster<a name="Aurora.CreateInstance.Console.DBInstance"></a>

The following procedures describe how to use the AWS Management Console to launch an Aurora DB cluster and create an Aurora Replica\.<a name="Aurora.CreateInstance.Console.Create"></a>

**To launch an Aurora DB cluster using the AWS Management Console**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the top\-right corner of the AWS Management Console, select the AWS Region in which you want to create the Aurora DB cluster\.

1. In the navigation pane, choose **Instances**\.

1. Choose **Launch DB instance** to start the Launch DB Instance wizard\. The wizard opens on the **Select engine** page\.

1. On the **Select Engine** page, choose MySQL 5\.6\-compatible, MySQL 5\.7\-compatible, or PostgreSQL\-compatible edition of Aurora\.  
![\[Amazon Aurora Launch DB Instance Wizard Select Engine\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/AuroraLaunch01.png)

1. Choose **Next**\.

1. On the **Specify DB details** page, specify your DB instance information\. The following table shows settings for a DB instance\.    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Aurora.CreateInstance.html)

   A typical **Specify DB details** page looks like the following\.   
![\[Amazon Aurora Launch DB Instance Wizard DB Instance Details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/AuroraLaunch02.png)

1. Confirm your master password and choose **Next**\.

1. On the **Configure advanced settings** page, you can customize additional settings for your Aurora DB cluster\. The following table shows the advanced settings for a DB cluster\.     
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Aurora.CreateInstance.html)

1. Click **Launch DB instance** to launch your Aurora DB instance, and then click **Close** to close the wizard\. 

   On the Amazon RDS console, the new DB instance appears in the list of DB instances\. The DB instance will have a status of **creating** until the DB instance is created and ready for use\. When the state changes to available, you can connect to the primary instance for your DB cluster\. Depending on the DB instance class and store allocated, it can take several minutes for the new instance to be available\.

   To view the newly created cluster, choose the **Clusters** view in the Amazon RDS console and click the DB cluster to show the DB cluster details\. For more information, see [Viewing an Amazon Aurora DB Cluster](Aurora.Viewing.md)\.  
![\[Amazon Aurora DB Instances List\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/AuroraLaunch04.png)

   Note the port and the endpoint of the cluster\. Use the endpoint and port of the cluster in your JDBC and ODBC connection strings for any application that performs write or read operations\.

### Creating an Aurora Replica Using the Console<a name="Aurora.CreateInstance.Console.ReadOnlyInstance"></a>

After creating the primary instance for your Aurora DB cluster, you can add up to 15 Aurora Replicas by using the Create Aurora Replica wizard\.

**Note**  
Amazon Aurora also supports replication with an external database, or an RDS DB instance\. When using Amazon Aurora, your RDS DB instance must be in the same region\. For more information, see [Replication with Amazon Aurora](Aurora.Replication.md)\.

**To create an Aurora Replica by using the AWS Management Console**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the check box to the left of the primary instance for your Aurora DB cluster\.

1. Choose **Instance actions**, and then choose **Create Aurora replica**\.

1. On the **Create Aurora replica** page, specify options for your Aurora Replica\. The following table shows settings for an Aurora Replica\.    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Aurora.CreateInstance.html)

1. Click **Create Aurora Replica** to create the Aurora Replica\.

Note the endpoint of the Aurora Replica\. Use the endpoint of the Aurora Replica in your JDBC and ODBC connection strings for any application that performs only read operations\.

## CLI<a name="Aurora.CreateInstance.CLI"></a>

**Note**  
Before you can create an Aurora DB cluster using the AWS CLI, you must fulfill the required prerequisites, such as creating a VPC and an RDS DB subnet group\. For more information, see [DB Cluster Prerequisites](#Aurora.CreateInstance.Prerequisites)\.<a name="Aurora.CreateInstance.CLI.DBCluster.MySQL"></a>

**To launch an Aurora MySQL DB cluster using the AWS CLI**

When you create an Aurora MySQL DB cluster or DB instance, ensure that you specify the correct value for the `--engine` parameter value based on the MySQL compatibility of the DB cluster or DB instance\.
+ When you create an Aurora MySQL 5\.7 DB cluster or DB instance, you must specify `aurora-mysql` for the `--engine` parameter\.
+ When you create an Aurora MySQL 5\.6 DB cluster or DB instance, you must specify `aurora` for the `--engine` parameter\.

Complete the following steps:

1. Identify the DB subnet group and VPC security group ID for your new DB cluster, and then call the [create\-db\-cluster](http://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html) AWS CLI command to create the Aurora MySQL DB cluster\.

   For example, the following command creates a new MySQL 5\.7–compatible DB cluster named `sample-cluster`\.

   For Linux, OS X, or Unix:

   ```
   aws rds create-db-cluster --db-cluster-identifier sample-cluster --engine aurora-mysql \
        --engine-version 5.7.12 --master-username user-name --master-user-password password \
        --db-subnet-group-name mysubnetgroup --vpc-security-group-ids sg-c7e5b0d2
   ```

   For Windows:

   ```
   aws rds create-db-cluster --db-cluster-identifier sample-cluster --engine aurora-mysql ^
       --engine-version 5.7.12 --master-username user-name --master-user-password password ^
       --db-subnet-group-name mysubnetgroup --vpc-security-group-ids sg-c7e5b0d2
   ```

   The following command creates a new MySQL 5\.6–compatible DB cluster named `sample-cluster`\.

   For Linux, OS X, or Unix:

   ```
   aws rds create-db-cluster --db-cluster-identifier sample-cluster --engine aurora \
        --engine-version 5.6.10a --master-username user-name --master-user-password password \
        --db-subnet-group-name mysubnetgroup --vpc-security-group-ids sg-c7e5b0d2
   ```

   For Windows:

   ```
   aws rds create-db-cluster --db-cluster-identifier sample-cluster --engine aurora ^
       --engine-version 5.6.10a --master-username user-name --master-user-password password ^
       --db-subnet-group-name mysubnetgroup --vpc-security-group-ids sg-c7e5b0d2
   ```

1. If you use the console to create a DB cluster, then Amazon RDS automatically creates the primary instance \(writer\) for your DB cluster\. If you use the AWS CLI to create a DB cluster, you must explicitly create the primary instance for your DB cluster\. The primary instance is the first instance that is created in a DB cluster\.

   Call the [create\-db\-instance](http://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html) AWS CLI command to create the primary instance for your DB cluster\. Include the name of the DB cluster as the `--db-cluster-identifier` parameter value\.

   For example, the following command creates a new MySQL 5\.7–compatible DB instance named `sample-instance`\.

   For Linux, OS X, or Unix:

   ```
   aws rds create-db-instance --db-instance-identifier sample-instance \
        --db-cluster-identifier sample-cluster --engine aurora-mysql --db-instance-class db.r4.large
   ```

   For Windows:

   ```
   aws rds create-db-instance --db-instance-identifier sample-instance ^
        --db-cluster-identifier sample-cluster --engine aurora-mysql --db-instance-class db.r4.large
   ```

   The following command creates a new MySQL 5\.6–compatible DB instance named `sample-instance`\.

   For Linux, OS X, or Unix:

   ```
   aws rds create-db-instance --db-instance-identifier sample-instance \
        --db-cluster-identifier sample-cluster --engine aurora --db-instance-class db.r4.large
   ```

   For Windows:

   ```
   aws rds create-db-instance --db-instance-identifier sample-instance ^
        --db-cluster-identifier sample-cluster --engine aurora --db-instance-class db.r4.large
   ```<a name="Aurora.CreateInstance.CLI.DBCluster.PostgreSQL"></a>

**To launch an Aurora PostgreSQL DB cluster using the AWS CLI**

1. Identify the DB subnet group and VPC security group ID for your new DB cluster, and then call the [create\-db\-cluster](http://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html) AWS CLI command to create the Aurora PostgreSQL DB cluster\.

   For example, the following command creates a new DB cluster named `sample-cluster`\.

   For Linux, OS X, or Unix:

   ```
   aws rds create-db-cluster --db-cluster-identifier sample-cluster --engine aurora-postgresql \
        --master-username user-name --master-user-password password \
        --db-subnet-group-name mysubnetgroup --vpc-security-group-ids sg-c7e5b0d2
   ```

   For Windows:

   ```
   aws rds create-db-cluster --db-cluster-identifier sample-cluster --engine aurora-postgresql ^
       --master-username user-name --master-user-password password ^
       --db-subnet-group-name mysubnetgroup --vpc-security-group-ids sg-c7e5b0d2
   ```

1. If you use the console to create a DB cluster, then Amazon RDS automatically creates the primary instance \(writer\) for your DB cluster\. If you use the AWS CLI to create a DB cluster, you must explicitly create the primary instance for your DB cluster\. The primary instance is the first instance that is created in a DB cluster\.

   Call the [create\-db\-instance](http://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html) AWS CLI command to create the primary instance for your DB cluster\. Include the name of the DB cluster as the `--db-cluster-identifier` parameter value\.

   For Linux, OS X, or Unix:

   ```
   aws rds create-db-instance --db-instance-identifier sample-instance \
        --db-cluster-identifier sample-cluster --engine aurora-postgresql --db-instance-class db.r4.large
   ```

   For Windows:

   ```
   aws rds create-db-instance --db-instance-identifier sample-instance ^
        --db-cluster-identifier sample-cluster --engine aurora-postgresql --db-instance-class db.r4.large
   ```

### To create an Aurora Replica in a DB cluster using the AWS CLI<a name="Aurora.CreateInstance.CLI.Replica"></a>

After you create the primary instance for a DB cluster, you can create up to 15 Aurora Replicas in your DB cluster to support read\-only queries\.

We recommend that you distribute the primary instance and Aurora Replicas in your DB cluster over multiple Availability Zones to improve the availability of your DB cluster\. For more information, see [Availability](Aurora.Overview.md#Aurora.Overview.Availability)\.

Call the [create\-db\-instance](http://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html) AWS CLI command to create an Aurora Replica in your DB cluster\. Include the name of the DB cluster as the `--db-cluster-identifier` parameter value\. You can optionally specify an Availability Zone for the Aurora Replica using the `--availability-zone` parameter, as shown in the following examples\.

For example, the following command creates a new MySQL 5\.7–compatible Aurora Replica named `sample-instance-us-west-2a`\.

For Linux, OS X, or Unix:

```
aws rds create-db-instance --db-instance-identifier sample-instance-us-west-2a \
    --db-cluster-identifier sample-cluster --engine aurora-mysql --db-instance-class db.r4.large \
    --availability-zone us-west-2a
```

For Windows:

```
aws rds create-db-instance --db-instance-identifier sample-instance-us-west-2a ^
    --db-cluster-identifier sample-cluster --engine aurora-mysql --db-instance-class db.r4.large ^
    --availability-zone us-west-2a
```

The following command creates a new MySQL 5\.6–compatible Aurora Replica named `sample-instance-us-west-2a`\.

For Linux, OS X, or Unix:

```
aws rds create-db-instance --db-instance-identifier sample-instance-us-west-2a \
    --db-cluster-identifier sample-cluster --engine aurora --db-instance-class db.r4.large \
    --availability-zone us-west-2a
```

For Windows:

```
aws rds create-db-instance --db-instance-identifier sample-instance-us-west-2a ^
    --db-cluster-identifier sample-cluster --engine aurora --db-instance-class db.r4.large ^
    --availability-zone us-west-2a
```

## Related Topics<a name="Aurora.CreateInstance.RelatedTopics"></a>
+ [Amazon Aurora on Amazon RDS](CHAP_Aurora.md)