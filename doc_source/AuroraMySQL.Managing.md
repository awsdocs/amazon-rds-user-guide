# Managing Amazon Aurora MySQL<a name="AuroraMySQL.Managing"></a>

The following sections discuss managing performance, scaling, fault tolerance, backup, and restoring for an Amazon Aurora MySQL DB cluster\. 

## Managing Performance and Scaling for Amazon Aurora MySQL<a name="AuroraMySQL.Managing.Performance"></a>

### Scaling Aurora MySQL DB Instances<a name="AuroraMySQL.Managing.Performance.InstanceScaling"></a>

You can scale Aurora MySQL DB instances in two ways, instance scaling and read scaling\. For more information about read scaling, see [Read Scaling](Aurora.Managing.md#Aurora.Managing.Performance.ReadScaling)\.

You can scale your Aurora MySQL DB cluster by modifying the DB instance class for each DB instance in the DB cluster\. Aurora MySQL supports several DB instance classes optimized for Aurora\. The following table describes the specifications of the DB instance classes supported by Aurora MySQL\.


| Instance Class | vCPU | ECU | Memory \(GiB\) | 
| --- | --- | --- | --- | 
|  db\.t2\.small  |  1  | 1 | 2 | 
|  db\.t2\.medium  |  2  | 2 | 4 | 
|  db\.r3\.large  |  2  | 6\.5 | 15\.25 | 
|  db\.r3\.xlarge  |  4  | 13 | 30\.5 | 
|  db\.r3\.2xlarge  |  8  | 26 | 61 | 
|  db\.r3\.4xlarge  |  16  | 52 | 122 | 
|  db\.r3\.8xlarge  |  32  | 104 | 244 | 
|  db\.r4\.large  |  2  | 7 | 15\.25 | 
|  db\.r4\.xlarge  |  4  | 13\.5 | 30\.5 | 
|  db\.r4\.2xlarge  |  8  | 27 | 61 | 
|  db\.r4\.4xlarge  |  16  | 53 | 122 | 
|  db\.r4\.8xlarge  |  32  | 99 | 244 | 
|  db\.r4\.16xlarge  |  64  | 195 | 488 | 

### Maximum Connections to an Aurora MySQL DB Instance<a name="AuroraMySQL.Managing.MaxConnections"></a>

The maximum number of connections allowed to an Aurora MySQL DB instance is determined by the `max_connections` parameter in the instance\-level parameter group for the DB instance\. By default, this value is set to the following equation \(the log function represents log base 2\):

`GREATEST({log(DBInstanceClassMemory/805306368)*45},{log(DBInstanceClassMemory/8187281408)*1000})`\.

Setting the `max_connections` parameter to this equation makes sure that the number of allowed connection scales well with the size of the instance\. For example, suppose your DB instance class is db\.r3\.xlarge, which has 30\.5 gigabytes \(GB\) of memory\. Then the maximum connections allowed is 2000, as shown in the following equation:

```
log( (30.5 * 1073741824) / 8187281408 ) * 1000 = 2000
```

The following table lists the resulting default value of `max_connections` for each DB instance class available to Aurora MySQL\. You can increase the maximum number of connections to your Aurora MySQL DB instance by scaling the instance up to a DB instance class with more memory, or by setting a larger value for the `max_connections` parameter, up to 16,000\.


| Instance Class | max\_connections Default Value | 
| --- | --- | 
|  db\.t2\.small  |  45  | 
|  db\.t2\.medium  |  90  | 
|  db\.r3\.large  |  1000  | 
|  db\.r3\.xlarge  |  2000  | 
|  db\.r3\.2xlarge  |  3000  | 
|  db\.r3\.4xlarge  |  4000  | 
|  db\.r3\.8xlarge  |  5000  | 
|  db\.r4\.large  |  1000  | 
|  db\.r4\.xlarge  |  2000  | 
|  db\.r4\.2xlarge  |  3000  | 
|  db\.r4\.4xlarge  |  4000  | 
|  db\.r4\.8xlarge  |  5000  | 
|  db\.r4\.16xlarge  |  6000  | 

## Testing Amazon Aurora Using Fault Injection Queries<a name="AuroraMySQL.Managing.FaultInjectionQueries"></a>

You can test the fault tolerance of your Amazon Aurora DB cluster by using fault injection queries\. Fault injection queries are issued as SQL commands to an Amazon Aurora instance and they enable you to schedule a simulated occurrence of one of the following events:
+ A crash of the master instance or an Aurora Replica
+ A failure of an Aurora Replica
+ A disk failure
+ Disk congestion

Fault injection queries that specify a crash force a crash of the Amazon Aurora instance\. The other fault injection queries result in simulations of failure events, but don't cause the event to occur\. When you submit a fault injection query, you also specify an amount of time for the failure event simulation to occur for\.

You can submit a fault injection query to one of your Aurora Replica instances by connecting to the endpoint for the Aurora Replica\. For more information, see [Aurora Endpoints](Aurora.Overview.md#Aurora.Overview.Endpoints)\.

### Testing an Instance Crash<a name="AuroraMySQL.Managing.FaultInjectionQueries.Crash"></a>

You can force a crash of an Amazon Aurora instance using the `ALTER SYSTEM CRASH` fault injection query\.

For this fault injection query, a failover will not occur\. If you want to test a failover, then you can choose the **Failover** instance action for your DB cluster in the RDS console, or use the [failover\-db\-cluster](http://docs.aws.amazon.com/cli/latest/reference/rds/failover-db-cluster.html) AWS CLI command or the [FailoverDBCluster](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_FailoverDBCluster.html) RDS API action\. 

#### Syntax<a name="AuroraMySQL.Managing.FaultInjectionQueries.Crash-Syntax"></a>

```
1. ALTER SYSTEM CRASH [ INSTANCE | DISPATCHER | NODE ];
```

#### Options<a name="AuroraMySQL.Managing.FaultInjectionQueries.Crash-Options"></a>

This fault injection query takes one of the following crash types:
+ **`INSTANCE`—**A crash of the MySQL\-compatible database for the Amazon Aurora instance is simulated\.
+ **`DISPATCHER`—**A crash of the dispatcher on the master instance for the Aurora DB cluster is simulated\. The *dispatcher* writes updates to the cluster volume for an Amazon Aurora DB cluster\.
+ **`NODE`—**A crash of both the MySQL\-compatible database and the dispatcher for the Amazon Aurora instance is simulated\. For this fault injection simulation, the cache is also deleted\.

The default crash type is `INSTANCE`\.

### Testing an Aurora Replica Failure<a name="AuroraMySQL.Managing.FaultInjectionQueries.ReplicaFailure"></a>

You can simulate the failure of an Aurora Replica using the `ALTER SYSTEM SIMULATE READ REPLICA FAILURE` fault injection query\.

An Aurora Replica failure will block all requests to an Aurora Replica or all Aurora Replicas in the DB cluster for a specified time interval\. When the time interval completes, the affected Aurora Replicas will be automatically synced up with master instance\. 

#### Syntax<a name="AuroraMySQL.Managing.FaultInjectionQueries.ReplicaFailure-Syntax"></a>

```
1. ALTER SYSTEM SIMULATE percentage_of_failure PERCENT READ REPLICA FAILURE
2.     [ TO ALL | TO "replica name" ]
3.     FOR INTERVAL quantity { YEAR | QUARTER | MONTH | WEEK | DAY | HOUR | MINUTE | SECOND };
```

#### Options<a name="AuroraMySQL.Managing.FaultInjectionQueries.ReplicaFailure-Options"></a>

This fault injection query takes the following parameters:
+ **`percentage_of_failure`—**The percentage of requests to block during the failure event\. This value can be a double between 0 and 100\. If you specify 0, then no requests are blocked\. If you specify 100, then all requests are blocked\.
+ **Failure type—**The type of failure to simulate\. Specify `TO ALL` to simulate failures for all Aurora Replicas in the DB cluster\. Specify `TO` and the name of the Aurora Replica to simulate a failure of a single Aurora Replica\. The default failure type is `TO ALL`\.
+ **`quantity`—**The amount of time for which to simulate the Aurora Replica failure\. The interval is an amount followed by a time unit\. The simulation will occur for that amount of the specified unit\. For example, `20 MINUTE` will result in the simulation running for 20 minutes\.
**Note**  
Take care when specifying the time interval for your Aurora Replica failure event\. If you specify too long of a time interval, and your master instance writes a large amount of data during the failure event, then your Aurora DB cluster might assume that your Aurora Replica has crashed and replace it\.

### Testing a Disk Failure<a name="AuroraMySQL.Managing.FaultInjectionQueries.DiskFailure"></a>

You can simulate a disk failure for an Aurora DB cluster using the `ALTER SYSTEM SIMULATE DISK FAILURE` fault injection query\.

During a disk failure simulation, the Aurora DB cluster randomly marks disk segments as faulting\. Requests to those segments will be blocked for the duration of the simulation\.

#### Syntax<a name="AuroraMySQL.Managing.FaultInjectionQueries.DiskFailure-Syntax"></a>

```
1. ALTER SYSTEM SIMULATE percentage_of_failure PERCENT DISK FAILURE
2.     [ IN DISK index | NODE index ]
3.     FOR INTERVAL quantity { YEAR | QUARTER | MONTH | WEEK | DAY | HOUR | MINUTE | SECOND };
```

#### Options<a name="AuroraMySQL.Managing.FaultInjectionQueries.DiskFailure-Options"></a>

This fault injection query takes the following parameters:
+ **`percentage_of_failure`** — The percentage of the disk to mark as faulting during the failure event\. This value can be a double between 0 and 100\. If you specify 0, then none of the disk is marked as faulting\. If you specify 100, then the entire disk is marked as faulting\.
+ **`DISK index`** — A specific logical block of data to simulate the failure event for\. If you exceed the range of available logical blocks of data, you will receive an error that tells you the maximum index value that you can specify\. For more information, see [Displaying Volume Status for an Aurora DB Cluster](#AuroraMySQL.Managing.VolumeStatus)\.
+ **`NODE index`** — A specific storage node to simulate the failure event for\. If you exceed the range of available storage nodes, you will receive an error that tells you the maximum index value that you can specify\. For more information, see [Displaying Volume Status for an Aurora DB Cluster](#AuroraMySQL.Managing.VolumeStatus)\.
+ **`quantity`** — The amount of time for which to simulate the disk failure\. The interval is an amount followed by a time unit\. The simulation will occur for that amount of the specified unit\. For example, `20 MINUTE` will result in the simulation running for 20 minutes\.

### Testing Disk Congestion<a name="AuroraMySQL.Managing.FaultInjectionQueries.DiskCongestion"></a>

You can simulate a disk failure for an Aurora DB cluster using the `ALTER SYSTEM SIMULATE DISK CONGESTION` fault injection query\.

During a disk congestion simulation, the Aurora DB cluster randomly marks disk segments as congested\. Requests to those segments will be delayed between the specified minimum and maximum delay time for the duration of the simulation\.

#### Syntax<a name="AuroraMySQL.Managing.FaultInjectionQueries.DiskCongestion-Syntax"></a>

```
1. ALTER SYSTEM SIMULATE percentage_of_failure PERCENT DISK CONGESTION
2.     BETWEEN minimum AND maximum MILLISECONDS
3.     [ IN DISK index | NODE index ]
4.     FOR INTERVAL quantity { YEAR | QUARTER | MONTH | WEEK | DAY | HOUR | MINUTE | SECOND };
```

#### Options<a name="AuroraMySQL.Managing.FaultInjectionQueries.DiskCongestion-Options"></a>

This fault injection query takes the following parameters:
+ **`percentage_of_failure`—**The percentage of the disk to mark as congested during the failure event\. This value can be a double between 0 and 100\. If you specify 0, then none of the disk is marked as congested\. If you specify 100, then the entire disk is marked as congested\.
+ **`DISK index` or `NODE index`—**A specific disk or node to simulate the failure event for\. If you exceed the range of indexes for the disk or node, you will receive an error that tells you the maximum index value that you can specify\.
+ **`minimum` and `maximum`—**The minimum and maximum amount of congestion delay, in milliseconds\. Disk segments marked as congested will be delayed for a random amount of time within the range of the minimum and maximum amount of milliseconds for the duration of the simulation\.
+ **`quantity`—**The amount of time for which to simulate the disk congestion\. The interval is an amount followed by a time unit\. The simulation will occur for that amount of the specified time unit\. For example, `20 MINUTE` will result in the simulation running for 20 minutes\.

## Altering Tables in Amazon Aurora Using Fast DDL<a name="AuroraMySQL.Managing.FastDDL"></a>

In MySQL, many data definition language \(DDL\) operations have a significant performance impact\. Performance impacts occur even with recent online DDL improvements\.

For example, suppose that you use an ALTER TABLE operation to add a column to a table\. Depending on the algorithm specified for the operation, this operation can involve the following:
+ Creating a full copy of the table
+ Creating a temporary table to process concurrent data manipulation language \(DML\) operations
+ Rebuilding all indexes for the table
+ Applying table locks while applying concurrent DML changes
+ Slowing concurrent DML throughput

In Amazon Aurora, you can use fast DDL to execute an ALTER TABLE operation in place, nearly instantaneously\. The operation completes without requiring the table to be copied and without having a material impact on other DML statements\. Because the operation doesn't consume temporary storage for a table copy, it makes DDL statements practical even for large tables on small instance types\.

**Note**  
Fast DDL is available for Aurora version 1\.12 and later\. For more information about Aurora versions, see [Amazon Aurora MySQL Database Engine Updates](AuroraMySQL.Updates.md)

### Limitations<a name="AuroraMySQL.Managing.FastDDL.Limitations"></a>

Currently, fast DDL has the following limitations:
+ Fast DDL only supports adding nullable columns, without default values, to the end of an existing table\.
+ Fast DDL does not support partitioned tables\.
+ Fast DDL does not support InnoDB tables that use the REDUNDANT row format\.
+ If the maximum possible record size for the DDL operation is too large, fast DDL is not used\. A record size is too large if it is greater than half the page size\. The maximum size of a record is computed by adding the maximum sizes of all columns\. For variable sized columns, according to InnoDB standards, extern bytes are not included for computation\.
**Note**  
The maximum record size check was added in Aurora 1\.15\.

### Syntax<a name="AuroraMySQL.Managing.FastDDL.Syntax"></a>

```
ALTER TABLE tbl_name ADD COLUMN col_name column_definition
```

### Options<a name="AuroraMySQL.Managing.FaultInjectionQueries.FastDDL.Options"></a>

This statement takes the following options:
+ **`tbl_name` — **The name of the table to be modified\.
+ **`col_name` — **The name of the column to be added\.
+ **`col_definition` — **The definition of the column to be added\.
**Note**  
You must specify a nullable column definition without a default value\. Otherwise, fast DDL isn't used\.

## Displaying Volume Status for an Aurora DB Cluster<a name="AuroraMySQL.Managing.VolumeStatus"></a>

In Amazon Aurora, a DB cluster volume consists of a collection of logical blocks\. Each of these represents 10 gigabytes of allocated storage\. These blocks are called protection groups\.

The data in each protection group is replicated across six physical storage devices, called storage nodes\. These storage nodes are allocated across three Availability Nodes \(AZs\) in the region where the DB cluster resides\. In turn, each storage node contains one or more logical blocks of data for the DB cluster volume\. For more information about protection groups and storage nodes, see [ Introducing the Aurora Storage Engine](https://aws.amazon.com/blogs/database/introducing-the-aurora-storage-engine/) on the AWS Database Blog\.

You can simulate the failure of an entire storage node, or a single logical block of data within a storage node\. To do so, you use the `ALTER SYSTEM SIMULATE DISK FAILURE` fault injection query\. For the query, you specify the index value of a specific logical block of data or storage node\. However, if you specify an index value greater than the number of logical blocks of data or storage nodes used by the DB cluster volume, the query returns an error\. For more information about fault injection queries, see [Testing Amazon Aurora Using Fault Injection Queries](#AuroraMySQL.Managing.FaultInjectionQueries)\.

You can avoid that error by using the `SHOW VOLUME STATUS` query\. The query returns two server status variables, `Disks` and `Nodes`\. These variables represent the total number of logical blocks of data and storage nodes, respectively, for the DB cluster volume\.

**Note**  
The SHOW VOLUME STATUS query is available for Aurora version 1\.12 and later\. For more information about Aurora versions, see [Amazon Aurora MySQL Database Engine Updates](AuroraMySQL.Updates.md)\.

### Syntax<a name="AuroraMySQL.Managing.VolumeStatus.Syntax"></a>

```
SHOW VOLUME STATUS
```

### Example<a name="AuroraMySQL.Managing.VolumeStatus.Example"></a>

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