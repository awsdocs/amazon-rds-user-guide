# Client:ClientRead<a name="wait-event.clientread"></a>

The `Client:ClientRead` event occurs when RDS for PostgreSQL is waiting to receive data from the client\.

**Topics**
+ [Supported engine versions](#wait-event.clientread.context.supported)
+ [Context](#wait-event.clientread.context)
+ [Likely causes of increased waits](#wait-event.clientread.causes)
+ [Actions](#wait-event.clientread.actions)

## Supported engine versions<a name="wait-event.clientread.context.supported"></a>

This wait event information is supported for RDS for PostgreSQL version 10 and higher\.

## Context<a name="wait-event.clientread.context"></a>

An RDS for PostgreSQL DB instance is waiting to receive data from the client\. The RDS for PostgreSQL DB instance must receive the data from the client before it can send more data to the client\. The time that the instance waits before receiving data from the client is a `Client:ClientRead` event\.

## Likely causes of increased waits<a name="wait-event.clientread.causes"></a>

Common causes for the `Client:ClientRead` event to appear in top waits include the following: 

**Increased network latency**  
There might be increased network latency between the RDS for PostgreSQL DB instance and client\. Higher network latency increases the time required for DB instance to receive data from the client\.

**Increased load on the client**  
There might be CPU pressure or network saturation on the client\. An increase in load on the client can delay transmission of data from the client to the RDS for PostgreSQL DB instance\.

**Excessive network round trips**  
A large number of network round trips between the RDS for PostgreSQL DB instance and the client can delay transmission of data from the client to the RDS for PostgreSQL DB instance\.

**Large copy operation**  
During a copy operation, the data is transferred from the client's file system to the RDS for PostgreSQL DB instance\. Sending a large amount of data to the DB instance can delay transmission of data from the client to the DB instance\.

**Idle client connection**  
When a client connects to the RDS for PostgreSQL DB instance in an `idle in transaction` state, the DB instance might wait for the client to send more data or issue a command\. A connection in this state can lead to an increase in `Client:ClientRead` events\.

**PgBouncer used for connection pooling**  
PgBouncer has a low\-level network configuration setting called `pkt_buf`, which is set to 4,096 by default\. If the workload is sending query packets larger than 4,096 bytes through PgBouncer, we recommend increasing the `pkt_buf` setting to 8,192\. If the new setting doesn't decrease the number of `Client:ClientRead` events, we recommend increasing the `pkt_buf` setting to larger values, such as 16,384 or 32,768\. If the query text is large, the larger setting can be particularly helpful\.

## Actions<a name="wait-event.clientread.actions"></a>

We recommend different actions depending on the causes of your wait event\.

**Topics**
+ [Place the clients in the same Availability Zone and VPC subnet as the instance](#wait-event.clientread.actions.az-vpc-subnet)
+ [Scale your client](#wait-event.clientread.actions.scale-client)
+ [Use current generation instances](#wait-event.clientread.actions.db-instance-class)
+ [Increase network bandwidth](#wait-event.clientread.actions.increase-network-bandwidth)
+ [Monitor maximums for network performance](#wait-event.clientread.actions.monitor-network-performance)
+ [Monitor for transactions in the "idle in transaction" state](#wait-event.clientread.actions.check-idle-in-transaction)

### Place the clients in the same Availability Zone and VPC subnet as the instance<a name="wait-event.clientread.actions.az-vpc-subnet"></a>

To reduce network latency and increase network throughput, place clients in the same Availability Zone and virtual private cloud \(VPC\) subnet as the RDS for PostgreSQL DB instance\. Make sure that the clients are as geographically close to the DB instance as possible\.

### Scale your client<a name="wait-event.clientread.actions.scale-client"></a>

Using Amazon CloudWatch or other host metrics, determine if your client is currently constrained by CPU or network bandwidth, or both\. If the client is constrained, scale your client accordingly\.

### Use current generation instances<a name="wait-event.clientread.actions.db-instance-class"></a>

In some cases, you might not be using a DB instance class that supports jumbo frames\. If you're running your application on Amazon EC2, consider using a current generation instance for the client\. Also, configure the maximum transmission unit \(MTU\) on the client operating system\. This technique might reduce the number of network round trips and increase network throughput\. For more information, see [ Jumbo frames \(9001 MTU\)](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/network_mtu.html#jumbo_frame_instances) in the *Amazon EC2 User Guide for Linux Instances*\.

For information about DB instance classes, see [DB instance classes](Concepts.DBInstanceClass.md)\. To determine the DB instance class that is equivalent to an Amazon EC2 instance type, place `db.` before the Amazon EC2 instance type name\. For example, the `r5.8xlarge` Amazon EC2 instance is equivalent to the `db.r5.8xlarge` DB instance class\.

### Increase network bandwidth<a name="wait-event.clientread.actions.increase-network-bandwidth"></a>

Use `NetworkReceiveThroughput` and `NetworkTransmitThroughput` Amazon CloudWatch metrics to monitor incoming and outgoing network traffic on the DB instance\. These metrics can help you to determine if network bandwidth is sufficient for your workload\. 

If your network bandwidth isn't enough, increase it\. If the AWS client or your DB instance is reaching the network bandwidth limits, the only way to increase the bandwidth is to increase your DB instance size\. For more information, see [DB instance class types](Concepts.DBInstanceClass.md#Concepts.DBInstanceClass.Types)\.

For more information about CloudWatch metrics, see [Amazon CloudWatch metrics for Amazon RDS](rds-metrics.md)\. 

### Monitor maximums for network performance<a name="wait-event.clientread.actions.monitor-network-performance"></a>

If you are using Amazon EC2 clients, Amazon EC2 provides maximums for network performance metrics, including aggregate inbound and outbound network bandwidth\. It also provides connection tracking to ensure that packets are returned as expected and link\-local services access for services such as the Domain Name System \(DNS\)\. To monitor these maximums, use a current enhanced networking driver and monitor network performance for your client\. 

For more information, see [ Monitor network performance for your Amazon EC2 instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/monitoring-network-performance-ena.html) in the *Amazon EC2 User Guide for Linux Instances* and [Monitor network performance for your Amazon EC2 instance](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/monitoring-network-performance-ena.html) in the *Amazon EC2 User Guide for Windows Instances*\.

### Monitor for transactions in the "idle in transaction" state<a name="wait-event.clientread.actions.check-idle-in-transaction"></a>

Check whether you have an increasing number of `idle in transaction` connections\. To do this, monitor the `state` column in the `pg_stat_activity` table\. You might be able to identify the connection source by running a query similar to the following\.

```
select client_addr, state, count(1) from pg_stat_activity 
where state like 'idle in transaction%' 
group by 1,2 
order by 3 desc
```