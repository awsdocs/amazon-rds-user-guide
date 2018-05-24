# Migrating an RDS MySQL Snapshot to Aurora<a name="AuroraMySQL.Migrating.RDSMySQL.Import"></a>

You can migrate a DB snapshot of an Amazon RDS MySQL DB instance to create an Aurora MySQL DB cluster\. The new Aurora MySQL DB cluster is populated with the data from the original Amazon RDS MySQL DB instance\. The DB snapshot must have been made from an Amazon RDS DB instance running MySQL version 5\.6 or 5\.7\.

You can migrate either a manual or automated DB snapshot\. After the DB cluster is created, you can then create optional Aurora Replicas\.

When the MySQL DB instance and the Aurora DB cluster are running the same version of MySQL, you can restore the MySQL snapshot directly to the Aurora DB cluster\. For example, you can restore a MySQL version 5\.6 snapshot directly to Aurora MySQL version 5\.6, but you can't restore a MySQL version 5\.6 snapshot directly to Aurora MySQL version 5\.7\.

If you want to migrate a MySQL version 5\.6 snapshot to Aurora MySQL version 5\.7, you can perform the migration in one of the following ways:
+ Migrate the MySQL version 5\.6 snapshot to Aurora MySQL version 5\.6, take a snapshot of the Aurora MySQL version 5\.6 DB cluster, and then restore the Aurora MySQL version 5\.6 snapshot to Aurora MySQL version 5\.7\.
+ Upgrade the MySQL version 5\.6 snapshot to MySQL version 5\.7, take a snapshot of the MySQL version 5\.7 DB instance, and then restore the MySQL version 5\.7 snapshot to Aurora MySQL version 5\.7\.

You can't migrate a MySQL version 5\.7 snapshot to Aurora MySQL version 5\.6\.

**Note**  
 You can also migrate a MySQL DB instance to an Aurora MySQL DB cluster by creating an Aurora Read Replica of your source MySQL DB instance\. For more information, see [Migrating Data from a MySQL DB Instance to an Amazon Aurora MySQL DB Cluster by Using an Aurora Read Replica](AuroraMySQL.Migrating.RDSMySQL.Replica.md)\. 

The general steps you must take are as follows:

1. Determine the amount of space to provision for your Aurora MySQL DB cluster\. For more information, see [How Much Space Do I Need?](#AuroraMySQL.Migrating.RDSMySQL.Space)

1. Use the console to create the snapshot in the AWS Region where the Amazon RDS MySQL instance is located\. For information about creating a DB snapshot, see [Creating a DB Snapshot](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_CreateSnapshot.html)\.

1. If the DB snapshot is not in the same AWS Region as your DB cluster, use the Amazon RDS console to copy the DB snapshot to that AWS Region\. For information about copying a DB snapshot, see [Copying a DB Snapshot](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_CopySnapshot.html)\.

1. Use the console to migrate the DB snapshot and create an Aurora MySQL DB cluster with the same databases as the original MySQL DB instance\. 

**Warning**  
Amazon RDS limits each AWS account to one snapshot copy into each AWS Region at a time\.

## How Much Space Do I Need?<a name="AuroraMySQL.Migrating.RDSMySQL.Space"></a>

When you migrate a snapshot of a MySQL DB instance into an Aurora MySQL DB cluster, Aurora uses an Amazon Elastic Block Store \(Amazon EBS\) volume to format the data from the snapshot before migrating it\. In some cases, additional space is needed to format the data for migration\.

Tables that are not MyISAM tables and are not compressed can be up to 16 TB in size\. If you have MyISAM tables, then Aurora must use additional space in the volume to convert the tables to be compatible with Aurora MySQL\. If you have compressed tables, then Aurora must use additional space in the volume to expand these tables before storing them on the Aurora cluster volume\. Because of this additional space requirement, you should ensure that none of the MyISAM and compressed tables being migrated from your MySQL DB instance exceeds 8 TB in size\.

## Reducing the Amount of Space Required to Migrate Data into Amazon Aurora MySQL<a name="AuroraMySQL.Migrating.RDSMySQL.PreImport"></a>

You might want to modify your database schema prior to migrating it into Amazon Aurora\. Such modification can be helpful in the following cases: 
+ You want to speed up the migration process\.
+ You are unsure of how much space you need to provision\.
+ You have attempted to migrate your data and the migration has failed due to a lack of provisioned space\.

You can make the following changes to improve the process of migrating a database into Amazon Aurora\.

**Important**  
Be sure to perform these updates on a new DB instance restored from a snapshot of a production database, rather than on a production instance\. You can then migrate the data from the snapshot of your new DB instance into your Aurora DB cluster to avoid any service interruptions on your production database\.


| Table Type | Limitation or Guideline | 
| --- | --- | 
|  MyISAM tables  |  Aurora MySQL supports InnoDB tables only\. If you have MyISAM tables in your database, then those tables must be converted before being migrated into Aurora MySQL\. The conversion process requires additional space for the MyISAM to InnoDB conversion during the migration procedure\. To reduce your chances of running out of space or to speed up the migration process, convert all of your MyISAM tables to InnoDB tables before migrating them\. The size of the resulting InnoDB table is equivalent to the size required by Aurora MySQL for that table\. To convert a MyISAM table to InnoDB, run the following command:  `alter table <schema>.<table_name> engine=innodb, algorithm=copy;`   | 
|  Compressed tables  |  Aurora MySQL doesn't support compressed tables \(that is, tables created with `ROW_FORMAT=COMPRESSED`\)\.  To reduce your chances of running out of space or to speed up the migration process, expand your compressed tables by setting `ROW_FORMAT` to `DEFAULT`, `COMPACT`, `DYNAMIC`, or `REDUNDANT`\. For more information, see [https://dev\.mysql\.com/doc/refman/5\.6/en/innodb\-row\-format\.html](https://dev.mysql.com/doc/refman/5.6/en/innodb-row-format.html)\.   | 

You can use the following SQL script on your existing MySQL DB instance to list the tables in your database that are MyISAM tables or compressed tables\.

```
-- This script examines a MySQL database for conditions that block
-- migrating the database into Amazon Aurora.
-- It needs to be run from an account that has read permission for the
-- INFORMATION_SCHEMA database.

-- Verify that this is a supported version of MySQL.

select msg as `==> Checking current version of MySQL.`
from
  (
  select
    'This script should be run on MySQL version 5.6. ' +
    'Earlier versions are not supported.' as msg,
    cast(substring_index(version(), '.', 1) as unsigned) * 100 + 
      cast(substring_index(substring_index(version(), '.', 2), '.', -1) 
      as unsigned) 
    as major_minor
  ) as T
where major_minor <> 506;


-- List MyISAM and compressed tables. Include the table size.

select concat(TABLE_SCHEMA, '.', TABLE_NAME) as `==> MyISAM or Compressed Tables`,
round(((data_length + index_length) / 1024 / 1024), 2) "Approx size (MB)"
from INFORMATION_SCHEMA.TABLES
where
  ENGINE <> 'InnoDB'
  and
  (
    -- User tables
    TABLE_SCHEMA not in ('mysql', 'performance_schema', 
                         'information_schema')
    or
    -- Non-standard system tables
    (
      TABLE_SCHEMA = 'mysql' and TABLE_NAME not in
        (
          'columns_priv', 'db', 'event', 'func', 'general_log',
          'help_category', 'help_keyword', 'help_relation',
          'help_topic', 'host', 'ndb_binlog_index', 'plugin',
          'proc', 'procs_priv', 'proxies_priv', 'servers', 'slow_log',
          'tables_priv', 'time_zone', 'time_zone_leap_second',
          'time_zone_name', 'time_zone_transition', 
          'time_zone_transition_type', 'user'
        )
    )
  )
  or
  (
    -- Compressed tables
       ROW_FORMAT = 'Compressed'
  );
```

The script produces output similar to the output in the following example\. The example shows two tables that must be converted from MyISAM to InnoDB\. The output also includes the approximate size of each table in megabytes \(MB\)\. 

```
+---------------------------------+------------------+
| ==> MyISAM or Compressed Tables | Approx size (MB) |
+---------------------------------+------------------+
| test.name_table                 |          2102.25 |
| test.my_table                   |            65.25 |
+---------------------------------+------------------+
2 rows in set (0.01 sec)
```

## AWS Management Console<a name="AuroraMySQL.Migrating.RDSMySQL.Import.Console"></a>

You can migrate a DB snapshot of an Amazon RDS MySQL DB instance to create an Aurora MySQL DB cluster\. The new Aurora MySQL DB cluster is populated with the data from the original Amazon RDS MySQL DB instance\. The DB snapshot must have been made from an Amazon RDS DB instance running MySQL version 5\.6 or 5\.7\. For information about creating a DB snapshot, see [Creating a DB Snapshot](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_CreateSnapshot.html)\.

If the DB snapshot is not in the AWS Region where you want to locate your data, use the Amazon RDS console to copy the DB snapshot to that AWS Region\. For information about copying a DB snapshot, see [Copying a DB Snapshot](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_CopySnapshot.html)\.

When you migrate the DB snapshot by using the AWS Management Console, the console takes the actions necessary to create both the DB cluster and the primary instance\.

You can also choose for your new Aurora MySQL DB cluster to be encrypted at rest using an AWS Key Management Service \(AWS KMS\) encryption key\.

**To migrate a MySQL DB snapshot by using the AWS Management Console**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. Either start the migration from the MySQL DB instance or from the snapshot:

   To start the migration from the DB instance:

   1. In the navigation pane, choose **Instances**, and then select the MySQL DB instance\.

   1. Choose **Instance Actions**, and then choose **Migrate latest snapshot**\.

   To start the migration from the snapshot:

   1. Choose **Snapshots**\.

   1. On the **Snapshots** page, choose the snapshot that you want to migrate into an Aurora MySQL DB cluster\.

   1. Choose **Snapshot Actions**, and then choose **Migrate Snapshot**\.

   The **Migrate Database** page appears\.

1. Set the following values on the **Migrate Database** page:
   + **Migrate to DB Engine**: Select `aurora`\.
   + **DB Engine Version**: Select the DB engine version for the Aurora MySQL DB cluster\.
   + **DB Instance Class**: Select a DB instance class that has the required storage and capacity for your database, for example `db.r3.large`\. Aurora cluster volumes automatically grow as the amount of data in your database increases, up to a maximum size of 64 tebibytes \(TiB\)\. So you only need to select a DB instance class that meets your current storage requirements\. For more information, see [Amazon Aurora Storage](Aurora.Overview.md#Aurora.Overview.Storage)\.
   + **DB Instance Identifier**: Type a name for the DB cluster that is unique for your account in the AWS Region you selected\. This identifier is used in the endpoint addresses for the instances in your DB cluster\. You might choose to add some intelligence to the name, such as including the AWS Region and DB engine you selected, for example **aurora\-cluster1**\.

     The DB instance identifier has the following constraints:
     + It must contain from 1 to 63 alphanumeric characters or hyphens\.
     + Its first character must be a letter\.
     + It cannot end with a hyphen or contain two consecutive hyphens\.
     + It must be unique for all DB instances per AWS account, per AWS Region\.
   + **Virtual Private Cloud \(VPC\)**: If you have an existing VPC, then you can use that VPC with your Aurora MySQL DB cluster by selecting your VPC identifier, for example `vpc-a464d1c1`\. For information on using an existing VPC, see [How to Create a VPC for Use with Amazon Aurora](Aurora.CreateVPC.md)\.

     Otherwise, you can choose to have Amazon RDS create a VPC for you by selecting **Create a new VPC**\. 
   + **Subnet group**: If you have an existing subnet group, then you can use that subnet group with your Aurora MySQL DB cluster by selecting your subnet group identifier, for example `gs-subnet-group1`\.

     Otherwise, you can choose to have Amazon RDS create a subnet group for you by selecting **Create a new subnet group**\. 
   + **Public accessibility**: Select **No** to specify that instances in your DB cluster can only be accessed by resources inside of your VPC\. Select **Yes** to specify that instances in your DB cluster can be accessed by resources on the public network\. The default is **Yes**\.
**Note**  
Your production DB cluster might not need to be in a public subnet, because only your application servers require access to your DB cluster\. If your DB cluster doesn't need to be in a public subnet, set **Publicly Accessible** to **No**\.
   + **Availability Zone**: Select the Availability Zone to host the primary instance for your Aurora MySQL DB cluster\. To have Amazon RDS select an Availability Zone for you, select **No Preference**\.
   + **Database Port**: Type the default port to be used when connecting to instances in the Aurora MySQL DB cluster\. The default is `3306`\.
**Note**  
You might be behind a corporate firewall that doesn't allow access to default ports such as the MySQL default port, 3306\. In this case, provide a port value that your corporate firewall allows\. Remember that port value later when you connect to the Aurora MySQL DB cluster\.
   + **Encryption**: Choose **Enable Encryption** for your new Aurora MySQL DB cluster to be encrypted at rest\. If you choose **Enable Encryption**, you must choose an AWS KMS encryption key as the **Master Key** value\.

     If your DB snapshot isn't encrypted, specify an encryption key to have your DB cluster encrypted at rest\.

     If your DB snapshot is encrypted, specify an encryption key to have your DB cluster encrypted at rest using the specified encryption key\. You can specify the encryption key used by the DB snapshot or a different key\. You can't create an unencrypted DB cluster from an encrypted DB snapshot\.
   + **Auto Minor Version Upgrade**: Select **Yes** if you want to enable your Aurora MySQL DB cluster to receive minor MySQL DB engine version upgrades automatically when they become available\.

     The **Auto Minor Version Upgrade** option only applies to upgrades to MySQL minor engine versions for your Amazon Aurora MySQL DB cluster\. It doesn't apply to regular patches applied to maintain system stability\.

1. Choose **Migrate** to migrate your DB snapshot\. 

1. Choose **Instances**, and then choose the arrow icon to show the DB cluster details and monitor the progress of the migration\. On the details page, you can find the cluster endpoint used to connect to the primary instance of the DB cluster\. For more information on connecting to an Aurora MySQL DB cluster, see [Connecting to an Amazon Aurora DB Cluster](Aurora.Connecting.md)\. 

## CLI<a name="USER_ImportAuroraCluster.CLI"></a>

You can migrate a DB snapshot of an Amazon RDS MySQL DB instance to create an Aurora DB cluster\. The new DB cluster is then populated with the data from the DB snapshot\. The DB snapshot must come from an Amazon RDS DB instance running MySQL version 5\.6 or 5\.7\. For more information, see [Creating a DB Snapshot](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_CreateSnapshot.html)\.

If the DB snapshot is not in the AWS Region where you want to locate your data, copy the DB snapshot to that AWS Region\. For more information, see [Copying a DB Snapshot](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_CopySnapshot.html)\.

You can create an Aurora DB cluster from a DB snapshot of an Amazon RDS MySQL DB instance by using the [http://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-cluster-from-snapshot.html](http://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-cluster-from-snapshot.html) command with the following parameters:
+ `--db-cluster-identifier`

  The name of the DB cluster to create\.
+ Either `--engine aurora-mysql` for a MySQL 5\.7–compatible DB cluster, or `--engine aurora` for a MySQL 5\.6–compatible DB cluster
+ `--kms-key-id`

  The AWS Key Management Service \(AWS KMS\) encryption key to optionally encrypt the DB cluster with, depending on whether your DB snapshot is encrypted\.
  + If your DB snapshot isn't encrypted, specify an encryption key to have your DB cluster encrypted at rest\. Otherwise, your DB cluster isn't encrypted\.
  + If your DB snapshot is encrypted, specify an encryption key to have your DB cluster encrypted at rest using the specified encryption key\. Otherwise, your DB cluster is encrypted at rest using the encryption key for the DB snapshot\.
**Note**  
You can't create an unencrypted DB cluster from an encrypted DB snapshot\.
+ `--snapshot-identifier`

  The Amazon Resource Name \(ARN\) of the DB snapshot to migrate\. For more information about Amazon RDS ARNs, see [Amazon Relational Database Service \(Amazon RDS\)](http://docs.aws.amazon.com/general/latest/gr/aws-arns-and-namespaces.html#arn-syntax-rds)\.

When you migrate the DB snapshot by using the `RestoreDBClusterFromSnapshot` command, the command creates both the DB cluster and the primary instance\.

In this example, you create a MySQL 5\.7–compatible DB cluster named *mydbcluster* from a DB snapshot with an ARN set to *mydbsnapshotARN*\.

For Linux, OS X, or Unix:

```
aws rds restore-db-cluster-from-snapshot \
    --db-cluster-identifier mydbcluster \
    --snapshot-identifier mydbsnapshotARN \
    --engine aurora-mysql
```

For Windows:

```
aws rds restore-db-cluster-from-snapshot ^
    --db-cluster-identifier mydbcluster ^
    --snapshot-identifier mydbsnapshotARN ^
    --engine aurora-mysql
```

In this example, you create a MySQL 5\.6–compatible DB cluster named *mydbcluster* from a DB snapshot with an ARN set to *mydbsnapshotARN*\.

For Linux, OS X, or Unix:

```
aws rds restore-db-cluster-from-snapshot \
    --db-cluster-identifier mydbcluster \
    --snapshot-identifier mydbsnapshotARN \
    --engine aurora
```

For Windows:

```
aws rds restore-db-cluster-from-snapshot ^
    --db-cluster-identifier mydbcluster ^
    --snapshot-identifier mydbsnapshotARN ^
    --engine aurora
```

## <a name="AuroraMySQL.Migrating.RDSMySQL.RelatedTopics"></a>