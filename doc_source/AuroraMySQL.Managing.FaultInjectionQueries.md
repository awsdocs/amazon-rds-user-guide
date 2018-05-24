# Testing Amazon Aurora Using Fault Injection Queries<a name="AuroraMySQL.Managing.FaultInjectionQueries"></a>

You can test the fault tolerance of your Amazon Aurora DB cluster by using fault injection queries\. Fault injection queries are issued as SQL commands to an Amazon Aurora instance and they enable you to schedule a simulated occurrence of one of the following events:
+ A crash of the master instance or an Aurora Replica
+ A failure of an Aurora Replica
+ A disk failure
+ Disk congestion

Fault injection queries that specify a crash force a crash of the Amazon Aurora instance\. The other fault injection queries result in simulations of failure events, but don't cause the event to occur\. When you submit a fault injection query, you also specify an amount of time for the failure event simulation to occur for\.

You can submit a fault injection query to one of your Aurora Replica instances by connecting to the endpoint for the Aurora Replica\. For more information, see [Aurora Endpoints](Aurora.Overview.md#Aurora.Overview.Endpoints)\.

## Testing an Instance Crash<a name="AuroraMySQL.Managing.FaultInjectionQueries.Crash"></a>

You can force a crash of an Amazon Aurora instance using the `ALTER SYSTEM CRASH` fault injection query\.

For this fault injection query, a failover will not occur\. If you want to test a failover, then you can choose the **Failover** instance action for your DB cluster in the RDS console, or use the [failover\-db\-cluster](http://docs.aws.amazon.com/cli/latest/reference/rds/failover-db-cluster.html) AWS CLI command or the [FailoverDBCluster](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_FailoverDBCluster.html) RDS API action\. 

### Syntax<a name="AuroraMySQL.Managing.FaultInjectionQueries.Crash-Syntax"></a>

```
1. ALTER SYSTEM CRASH [ INSTANCE | DISPATCHER | NODE ];
```

### Options<a name="AuroraMySQL.Managing.FaultInjectionQueries.Crash-Options"></a>

This fault injection query takes one of the following crash types:
+ **`INSTANCE`—**A crash of the MySQL\-compatible database for the Amazon Aurora instance is simulated\.
+ **`DISPATCHER`—**A crash of the dispatcher on the master instance for the Aurora DB cluster is simulated\. The *dispatcher* writes updates to the cluster volume for an Amazon Aurora DB cluster\.
+ **`NODE`—**A crash of both the MySQL\-compatible database and the dispatcher for the Amazon Aurora instance is simulated\. For this fault injection simulation, the cache is also deleted\.

The default crash type is `INSTANCE`\.

## Testing an Aurora Replica Failure<a name="AuroraMySQL.Managing.FaultInjectionQueries.ReplicaFailure"></a>

You can simulate the failure of an Aurora Replica using the `ALTER SYSTEM SIMULATE READ REPLICA FAILURE` fault injection query\.

An Aurora Replica failure will block all requests to an Aurora Replica or all Aurora Replicas in the DB cluster for a specified time interval\. When the time interval completes, the affected Aurora Replicas will be automatically synced up with master instance\. 

### Syntax<a name="AuroraMySQL.Managing.FaultInjectionQueries.ReplicaFailure-Syntax"></a>

```
1. ALTER SYSTEM SIMULATE percentage_of_failure PERCENT READ REPLICA FAILURE
2.     [ TO ALL | TO "replica name" ]
3.     FOR INTERVAL quantity { YEAR | QUARTER | MONTH | WEEK | DAY | HOUR | MINUTE | SECOND };
```

### Options<a name="AuroraMySQL.Managing.FaultInjectionQueries.ReplicaFailure-Options"></a>

This fault injection query takes the following parameters:
+ **`percentage_of_failure`—**The percentage of requests to block during the failure event\. This value can be a double between 0 and 100\. If you specify 0, then no requests are blocked\. If you specify 100, then all requests are blocked\.
+ **Failure type—**The type of failure to simulate\. Specify `TO ALL` to simulate failures for all Aurora Replicas in the DB cluster\. Specify `TO` and the name of the Aurora Replica to simulate a failure of a single Aurora Replica\. The default failure type is `TO ALL`\.
+ **`quantity`—**The amount of time for which to simulate the Aurora Replica failure\. The interval is an amount followed by a time unit\. The simulation will occur for that amount of the specified unit\. For example, `20 MINUTE` will result in the simulation running for 20 minutes\.
**Note**  
Take care when specifying the time interval for your Aurora Replica failure event\. If you specify too long of a time interval, and your master instance writes a large amount of data during the failure event, then your Aurora DB cluster might assume that your Aurora Replica has crashed and replace it\.

## Testing a Disk Failure<a name="AuroraMySQL.Managing.FaultInjectionQueries.DiskFailure"></a>

You can simulate a disk failure for an Aurora DB cluster using the `ALTER SYSTEM SIMULATE DISK FAILURE` fault injection query\.

During a disk failure simulation, the Aurora DB cluster randomly marks disk segments as faulting\. Requests to those segments will be blocked for the duration of the simulation\.

### Syntax<a name="AuroraMySQL.Managing.FaultInjectionQueries.DiskFailure-Syntax"></a>

```
1. ALTER SYSTEM SIMULATE percentage_of_failure PERCENT DISK FAILURE
2.     [ IN DISK index | NODE index ]
3.     FOR INTERVAL quantity { YEAR | QUARTER | MONTH | WEEK | DAY | HOUR | MINUTE | SECOND };
```

### Options<a name="AuroraMySQL.Managing.FaultInjectionQueries.DiskFailure-Options"></a>

This fault injection query takes the following parameters:
+ **`percentage_of_failure`** — The percentage of the disk to mark as faulting during the failure event\. This value can be a double between 0 and 100\. If you specify 0, then none of the disk is marked as faulting\. If you specify 100, then the entire disk is marked as faulting\.
+ **`DISK index`** — A specific logical block of data to simulate the failure event for\. If you exceed the range of available logical blocks of data, you will receive an error that tells you the maximum index value that you can specify\. For more information, see [Displaying Volume Status for an Aurora DB Cluster](AuroraMySQL.Managing.VolumeStatus.md)\.
+ **`NODE index`** — A specific storage node to simulate the failure event for\. If you exceed the range of available storage nodes, you will receive an error that tells you the maximum index value that you can specify\. For more information, see [Displaying Volume Status for an Aurora DB Cluster](AuroraMySQL.Managing.VolumeStatus.md)\.
+ **`quantity`** — The amount of time for which to simulate the disk failure\. The interval is an amount followed by a time unit\. The simulation will occur for that amount of the specified unit\. For example, `20 MINUTE` will result in the simulation running for 20 minutes\.

## Testing Disk Congestion<a name="AuroraMySQL.Managing.FaultInjectionQueries.DiskCongestion"></a>

You can simulate a disk failure for an Aurora DB cluster using the `ALTER SYSTEM SIMULATE DISK CONGESTION` fault injection query\.

During a disk congestion simulation, the Aurora DB cluster randomly marks disk segments as congested\. Requests to those segments will be delayed between the specified minimum and maximum delay time for the duration of the simulation\.

### Syntax<a name="AuroraMySQL.Managing.FaultInjectionQueries.DiskCongestion-Syntax"></a>

```
1. ALTER SYSTEM SIMULATE percentage_of_failure PERCENT DISK CONGESTION
2.     BETWEEN minimum AND maximum MILLISECONDS
3.     [ IN DISK index | NODE index ]
4.     FOR INTERVAL quantity { YEAR | QUARTER | MONTH | WEEK | DAY | HOUR | MINUTE | SECOND };
```

### Options<a name="AuroraMySQL.Managing.FaultInjectionQueries.DiskCongestion-Options"></a>

This fault injection query takes the following parameters:
+ **`percentage_of_failure`—**The percentage of the disk to mark as congested during the failure event\. This value can be a double between 0 and 100\. If you specify 0, then none of the disk is marked as congested\. If you specify 100, then the entire disk is marked as congested\.
+ **`DISK index` or `NODE index`—**A specific disk or node to simulate the failure event for\. If you exceed the range of indexes for the disk or node, you will receive an error that tells you the maximum index value that you can specify\.
+ **`minimum` and `maximum`—**The minimum and maximum amount of congestion delay, in milliseconds\. Disk segments marked as congested will be delayed for a random amount of time within the range of the minimum and maximum amount of milliseconds for the duration of the simulation\.
+ **`quantity`—**The amount of time for which to simulate the disk congestion\. The interval is an amount followed by a time unit\. The simulation will occur for that amount of the specified time unit\. For example, `20 MINUTE` will result in the simulation running for 20 minutes\.