# Migrating Data to Amazon Aurora PostgreSQL<a name="AuroraPostgreSQL.Migrating"></a>

You have several options for migrating data from your existing database to an Amazon Aurora PostgreSQL DB cluster\. Your migration options also depend on the database that you are migrating from and the size of the data that you are migrating\. The following table describes your options\.


| Migrating From | Solution | 
| --- | --- | 
| An RDS PostgreSQL DB instance |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/AuroraPostgreSQL.Migrating.html)  | 
| A database that is not PostgreSQL\-compatible |  You can use AWS Database Migration Service \(AWS DMS\) to migrate data from a database that is not PostgreSQL\-compatible\. For more information on AWS DMS, see [What Is AWS Database Migration Service?](http://docs.aws.amazon.com/dms/latest/userguide/Welcome.html) | 

## AWS Management Console<a name="AuroraPostgreSQL.Migrating.RDSPostgreSQL.Import.Console"></a>

You can migrate a DB snapshot of an Amazon RDS PostgreSQL DB instance to create an Amazon Aurora PostgreSQL DB cluster\. The new Amazon Aurora PostgreSQL DB cluster will be populated with the data from the original Amazon RDS PostgreSQL DB instance\. The DB snapshot must have been made from an Amazon RDS DB instance running PostgreSQL 9\.6\.1 or 9\.6\.3\. For information about creating a DB snapshot, see [Creating a DB Snapshot](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_CreateSnapshot.html)\.

If the DB snapshot is not in the AWS Region where you want to locate your data, use the Amazon RDS console to copy the DB snapshot to that AWS Region\. For information about copying a DB snapshot, see [Copying a DB Snapshot](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_CopySnapshot.html)\.

When you migrate the DB snapshot by using the AWS Management Console, the console takes the actions necessary to create both the DB cluster and the primary instance\.

You can also choose for your new Amazon Aurora PostgreSQL DB cluster to be encrypted at rest using an AWS Key Management Service \(AWS KMS\) encryption key\. This option is available only for unencrypted DB snapshots\.

**To migrate a PostgreSQL DB snapshot by using the AWS Management Console**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. Choose **Snapshots**\.

1. On the **Snapshots** page, choose the snapshot that you want to migrate into an Amazon Aurora PostgreSQL DB cluster\.

1. Choose **Migrate Database**\.

1. Set the following values on the **Migrate Database** page:

   + **DB Instance Class**: Select a DB instance class that has the required storage and capacity for your database, for example `db.r3.large`\. Aurora cluster volumes automatically grow as the amount of data in your database increases, up to a maximum size of 64 terabytes \(TB\)\. So you only need to select a DB instance class that meets your current storage requirements\. For more information, see [Amazon Aurora Storage](Aurora.Overview.md#Aurora.Overview.Storage)\.

   + **DB Instance Identifier**: Type a name for the DB cluster that is unique for your account in the region you selected\. This identifier is used in the endpoint addresses for the instances in your DB cluster\. You might choose to add some intelligence to the name, such as including the region and DB engine you selected, for example **aurora\-cluster1**\.

     The DB instance identifier has the following constraints:

     + It must contain from 1 to 63 alphanumeric characters or hyphens\.

     + Its first character must be a letter\.

     + It cannot end with a hyphen or contain two consecutive hyphens\.

     + It must be unique for all DB instances per AWS account, per AWS Region\.

   + **VPC**: If you have an existing VPC, then you can use that VPC with your Amazon Aurora PostgreSQL DB cluster by selecting your VPC identifier, for example `vpc-a464d1c1`\. For information on using an existing VPC, see [How to Create a VPC for Use with Amazon Aurora](Aurora.CreateVPC.md)\.

     Otherwise, you can choose to have Amazon RDS create a VPC for you by selecting **Create a new VPC**\. 

   + **Subnet Group**: If you have an existing subnet group, then you can use that subnet group with your Amazon Aurora PostgreSQL DB cluster by selecting your subnet group identifier, for example `gs-subnet-group1`\.

     Otherwise, you can choose to have Amazon RDS create a subnet group for you by selecting **Create a new subnet group**\. 

   + **Publicly Accessible**: Select **No** to specify that instances in your DB cluster can only be accessed by resources inside of your VPC\. Select **Yes** to specify that instances in your DB cluster can be accessed by resources on the public network\. The default is **Yes**\.
**Note**  
Your production DB cluster might not need to be in a public subnet, because only your application servers will require access to your DB cluster\. If your DB cluster doesn't need to be in a public subnet, set **Publicly Accessible** to **No**\.

   + **Availability Zone**: Select the Availability Zone to host the primary instance for your Amazon Aurora PostgreSQL DB cluster\. To have Amazon RDS select an Availability Zone for you, select **No Preference**\.

   + **Database Port**: Type the default port to be used when connecting to instances in the Amazon Aurora PostgreSQL DB cluster\. The default is `5432`\.
**Note**  
You might be behind a corporate firewall that doesn't allow access to default ports such as the PostgreSQL default port, 5432\. In this case, provide a port value that your corporate firewall allows\. Remember that port value later when you connect to the Amazon Aurora PostgreSQL DB cluster\.

   + **Enable Encryption**: Choose **Yes** for your new Amazon Aurora PostgreSQL DB cluster to be encrypted at rest\. If you choose **Yes**, you will be required to choose an AWS KMS encryption key as the **Master Key** value\.

   + **Auto Minor Version Upgrade**: Select **Yes** if you want to enable your Amazon Aurora PostgreSQL DB cluster to receive minor PostgreSQL DB engine version upgrades automatically when they become available\.

     The **Auto Minor Version Upgrade** option only applies to upgrades to PostgreSQL minor engine versions for your Amazon Aurora PostgreSQL DB cluster\. It doesn't apply to regular patches applied to maintain system stability\.

1. Choose **Migrate** to migrate your DB snapshot\. 

1. Choose **Instances**, and then choose the arrow icon to show the DB cluster details and monitor the progress of the migration\. On the details page, you will find the cluster endpoint used to connect to the primary instance of the DB cluster\. For more information on connecting to an Amazon Aurora PostgreSQL DB cluster, see [Connecting to an Amazon Aurora DB Cluster](Aurora.Connecting.md)\. 