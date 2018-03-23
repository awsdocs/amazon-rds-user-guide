# Cloning Databases in an Aurora DB Cluster<a name="Aurora.Managing.Clone"></a>

Using database cloning, you can quickly and cost\-effectively create clones of all your databases\. The clone databases require only minimal additional space when first created\. Database cloning uses a copy\-on\-write protocol, in which data is copied at the time that data changes, either on the source databases or the clone databases\. You can make multiple clones from the same DB cluster\. You can also create additional clones from other clones\. For more information on how the copy\-on\-write protocol works in the context of Aurora storage, see [Copy\-on\-Write Protocol for Database Cloning](#Aurora.Managing.Clone.Protocol)\. 

You can use database cloning in a variety of use cases, especially where you don't want to have an impact on your production environment, such as the following:  
+ Experiment with and assess the impact of changes, such as schema changes or parameter group changes
+ Perform workload\-intensive operations, such as exporting data or running analytical queries
+ Create a copy of a production DB cluster in a non\-production environment for development or testing

## Limitations<a name="Aurora.Managing.Clone.Limitations"></a>

There are some limitations involved with database cloning, described following:
+ Cloning is only supported for Aurora MySQL instances\. 
+ You cannot create clone databases across AWS regions\. The clone databases must be created in the same region as the source databases\.
+ Currently, you are limited to up to 15 clones based on a copy, including clones based on other clones\. After that, only copies can be created\. However, each copy can also have up to 15 clones\. 
+ Cross\-account database cloning is not currently supported\.
+ You can provide a different virtual private cloud \(VPC\) for your clone\. However, the subnets in those VPCs must map to the same set of Availability Zones\.

## Copy\-on\-Write Protocol for Database Cloning<a name="Aurora.Managing.Clone.Protocol"></a>

The following scenarios illustrate how the copy\-on\-write protocol works\.
+ [Before Database Cloning](#Aurora.Managing.Clone.Protocol.Before)
+ [After Database Cloning](#Aurora.Managing.Clone.After)
+ [When a Change Occurs on the Source Database](#Aurora.Managing.Clone.Protocol.SourceWrite)
+ [When a Change Occurs on the Clone Database](#Aurora.Managing.Clone.Protocol.CloneWrite)

### Before Database Cloning<a name="Aurora.Managing.Clone.Protocol.Before"></a>

Data in a source database is stored in pages\. In the following diagram, the source database has four pages\.

![\[Amazon Aurora source database, before database cloning\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/AuroraClone001.png)

### After Database Cloning<a name="Aurora.Managing.Clone.After"></a>

As shown in the following diagram, there are no changes in the source database after database cloning\. Both the source database and the clone database point to the same four pages\. None of the pages has been physically copied, so no additional storage is required\.

![\[Amazon Aurora source database and clone database, after database cloning\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/AuroraClone002.png)

### When a Change Occurs on the Source Database<a name="Aurora.Managing.Clone.Protocol.SourceWrite"></a>

In the following example, the source database makes a change to the data in `Page 1`\. Instead of writing to the original `Page 1`, additional storage is used to create a new page, called `Page 1'`\. The source database now points to the new `Page 1'`, and also to `Page 2`, `Page 3`, and `Page 4`\. The clone database continues to point to `Page 1` through `Page 4`\.

![\[Amazon Aurora source database and clone database, after change occurs in source database\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/AuroraClone003.png)

### When a Change Occurs on the Clone Database<a name="Aurora.Managing.Clone.Protocol.CloneWrite"></a>

In the following diagram, the clone database has also made a change, this time in `Page 4`\. Instead of writing to the original `Page 4`, additional storage is used to create a new page, called `Page 4'`\. The source database continues to point to `Page 1'`, and also `Page 2` through Page 4, but the clone database now points to `Page 1` through `Page 3`, and also `Page 4'`\.

![\[Amazon Aurora source database and clone database, after change occurs on clone database\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/AuroraClone004.png)

As shown in the second scenario, after database cloning there is no additional storage required at the point of clone creation\. However, as changes occur in the source database and clone database, only the changed pages are created, as shown in the third and fourth scenarios\. As more changes occur over time in both the source database and clone database, you need incrementally more storage to capture and store the changes\. 

## Deleting Source Databases<a name="Aurora.Managing.Clone.Deleting"></a>

When deleting a source database that has one or more clone databases associated with it, the clone databases are not affected\. The clone databases continue to point to the pages that were previously owned by the source database\. 

## AWS Management Console<a name="Aurora.Managing.Clone.Console"></a>

The following procedure describes how to clone an Aurora DB cluster using the AWS Management Console\.

**To create a clone of a DB cluster using the AWS Management Console**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Instances**\. Choose the primary instance for the DB cluster that you want to create a clone of\.

1. Choose **Instance actions**, and then choose **Create clone**\.

1. On the **Create Clone** page, type a name for the primary instance of the clone DB cluster as the **DB instance identifier**\.

   If you want to, set any other settings for the clone DB cluster\. For information about the different DB cluster settings, see [AWS Management Console](Aurora.CreateInstance.md#Aurora.CreateInstance.Console)\.

1. Choose **Create Clone** to launch the clone DB cluster\.

## CLI<a name="Aurora.Managing.Clone.CLI"></a>

The following procedure describes how to clone an Aurora DB cluster using the AWS CLI\.

**To create a clone of a DB cluster using the AWS CLI**
+ Call the [restore\-db\-cluster\-to\-point\-in\-time](http://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-cluster-to-point-in-time.html) AWS CLI command and supply the following values:
  + `--source-db-cluster-identifier` – the name of the source DB cluster to create a clone of\.
  + `--db-cluster-identifier` – the name of the clone DB cluster\.
  + `--restore-type copy-on-write` – values that indicate to create a clone DB cluster\.
  + `--use-latest-restorable-time` – specifies that the latest restorable backup time is used\.

  The following example creates a clone of the DB cluster named `sample-source-cluster`\. The name of the clone DB cluster is `sample-cluster-clone`\.

  For Linux, OS X, or Unix:

  ```
  aws rds restore-db-cluster-to-point-in-time \
      --source-db-cluster-identifier sample-source-cluster \
      --db-cluster-identifier sample-cluster-clone \
      --restore-type copy-on-write \
      --use-latest-restorable-time
  ```

  For Windows:

  ```
  aws rds restore-db-cluster-to-point-in-time ^
      --source-db-cluster-identifier sample-source-cluster ^
      --db-cluster-identifier sample-cluster-clone ^
      --restore-type copy-on-write ^
      --use-latest-restorable-time
  ```

**Note**  
The [restore\-db\-cluster\-to\-point\-in\-time](http://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-cluster-to-point-in-time.html) AWS CLI command only restores the DB cluster, not the DB instances for that DB cluster\. You must invoke the [create\-db\-instance](http://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html) command to create DB instances for the restored DB cluster, specifying the identifier of the restored DB cluster in `--db-instance-identifier`\. You can create DB instances only after the `restore-db-cluster-to-point-in-time` command has completed and the DB cluster is available\.