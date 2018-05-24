# Displaying Volume Status for an Aurora DB Cluster<a name="AuroraMySQL.Managing.VolumeStatus"></a>

In Amazon Aurora, a DB cluster volume consists of a collection of logical blocks\. Each of these represents 10 gigabytes of allocated storage\. These blocks are called protection groups\.

The data in each protection group is replicated across six physical storage devices, called storage nodes\. These storage nodes are allocated across three Availability Nodes \(AZs\) in the region where the DB cluster resides\. In turn, each storage node contains one or more logical blocks of data for the DB cluster volume\. For more information about protection groups and storage nodes, see [ Introducing the Aurora Storage Engine](https://aws.amazon.com/blogs/database/introducing-the-aurora-storage-engine/) on the AWS Database Blog\.

You can simulate the failure of an entire storage node, or a single logical block of data within a storage node\. To do so, you use the `ALTER SYSTEM SIMULATE DISK FAILURE` fault injection query\. For the query, you specify the index value of a specific logical block of data or storage node\. However, if you specify an index value greater than the number of logical blocks of data or storage nodes used by the DB cluster volume, the query returns an error\. For more information about fault injection queries, see [Testing Amazon Aurora Using Fault Injection Queries](AuroraMySQL.Managing.FaultInjectionQueries.md)\.

You can avoid that error by using the `SHOW VOLUME STATUS` query\. The query returns two server status variables, `Disks` and `Nodes`\. These variables represent the total number of logical blocks of data and storage nodes, respectively, for the DB cluster volume\.

**Note**  
The SHOW VOLUME STATUS query is available for Aurora version 1\.12 and later\. For more information about Aurora versions, see [Amazon Aurora MySQL Database Engine Updates](AuroraMySQL.Updates.md)\.

## Syntax<a name="AuroraMySQL.Managing.VolumeStatus.Syntax"></a>

```
SHOW VOLUME STATUS
```

## Example<a name="AuroraMySQL.Managing.VolumeStatus.Example"></a>

The following example illustrates a typical SHOW VOLUME STATUS result\.

```
mysql> SHOW VOLUME STATUS;
+---------------+-------+
| Variable_name | Value |
+---------------+-------+
| Disks         | 96    |
| Nodes         | 74    |
+---------------+-------+
```