# Connecting to a Multi\-AZ DB cluster<a name="multi-az-db-clusters-concepts-connection-management"></a>

 A Multi\-AZ DB cluster has three DB instances instead of a single DB instance\. Each connection is handled by a specific DB instance\. When you connect to a Multi\-AZ DB cluster, the hostname and port that you specify point to a fully qualified domain name called an *endpoint*\. The Multi\-AZ DB cluster uses the endpoint mechanism to abstract these connections so that you don't need to specify exactly which DB instance in the DB cluster to connect to\. Thus, you don't have to hardcode all the hostnames or write your own logic for rerouting connections when some DB instances aren't available\. 

The writer endpoint connects to the writer DB instance of the DB cluster, which supports both read and write operations\. The reader endpoint connects to either of the two reader DB instances, which support only read operations\.

 Using endpoints, you can map each connection to the appropriate DB instance or group of DB instances based on your use case\. For example, to perform DDL and DML statements, you can connect to whichever DB instance is the writer DB instance\. To perform queries, you can connect to the reader endpoint, with the Multi\-AZ DB cluster automatically managing connections among the reader DB instances\. For diagnosis or tuning, you can connect to a specific DB instance endpoint to examine details about a specific DB instance\.

For information about connecting to a DB instance, see [Connecting to an Amazon RDS DB instance](CHAP_CommonTasks.Connect.md)\.

**Topics**
+ [Types of Multi\-AZ DB cluster endpoints](#multi-az-db-clusters-concepts-connection-management-endpoint-types)
+ [Viewing the endpoints for a Multi\-AZ DB cluster](#multi-az-db-clusters-concepts-connection-management-viewing)
+ [Using the cluster endpoint](#multi-az-db-clusters-concepts-connection-management-endpoints-cluster)
+ [Using the reader endpoint](#multi-az-db-clusters-concepts-connection-management-endpoints-reader)
+ [Using the instance endpoints](#multi-az-db-clusters-concepts-connection-management-endpoints-instance)
+ [How Multi\-AZ DB endpoints work with high availability](#multi-az-db-clusters-concepts-connection-management-endpoints-ha)

## Types of Multi\-AZ DB cluster endpoints<a name="multi-az-db-clusters-concepts-connection-management-endpoint-types"></a>

 An endpoint is represented by a unique identifier that contains a host address\. The following types of endpoints are available from a Multi\-AZ DB cluster: 

**Cluster endpoint**  
 A *cluster endpoint* \(or *writer endpoint*\) for a Multi\-AZ DB cluster connects to the current writer DB instance for that DB cluster\. This endpoint is the only one that can perform write operations such as DDL and DML statements\. This endpoint can also perform read operations\.   
 Each Multi\-AZ DB cluster has one cluster endpoint and one writer DB instance\.   
 You use the cluster endpoint for all write operations on the DB cluster, including inserts, updates, deletes, and DDL changes\. You can also use the cluster endpoint for read operations, such as queries\.   
 If the current writer DB instance of a DB cluster fails, the Multi\-AZ DB cluster automatically fails over to a new writer DB instance\. During a failover, the DB cluster continues to serve connection requests to the cluster endpoint from the new writer DB instance, with minimal interruption of service\.   
 The following example illustrates a cluster endpoint for a Multi\-AZ DB cluster\.   
 `mydbcluster.cluster-123456789012.us-east-1.rds.amazonaws.com` 

**Reader endpoint**  
 A *reader endpoint* for a Multi\-AZ DB cluster provides support for read\-only connections to the DB cluster\. Use the reader endpoint for read operations, such as `SELECT` queries\. By processing those statements on the reader DB instances, this endpoint reduces the overhead on the writer DB instance\. It also helps the cluster to scale the capacity to handle simultaneous `SELECT` queries\. Each Multi\-AZ DB cluster has one reader endpoint\.   
 The reader endpoint sends each connection request to one of the reader DB instances\. When you use the reader endpoint for a session, you can only perform read\-only statements such as `SELECT` in that session\.   
 The following example illustrates a reader endpoint for a Multi\-AZ DB cluster\. The read\-only intent of a reader endpoint is denoted by the `-ro` within the cluster endpoint name\.   
 `mydbcluster.cluster-ro-123456789012.us-east-1.rds.amazonaws.com` 

**Instance endpoint**  
 An *instance endpoint* connects to a specific DB instance within a Multi\-AZ DB cluster\. Each DB instance in a DB cluster has its own unique instance endpoint\. So there is one instance endpoint for the current writer DB instance of the DB cluster, and there is one instance endpoint for each of the reader DB instances in the DB cluster\.   
 The instance endpoint provides direct control over connections to the DB cluster\. This control can help you address scenarios where using the cluster endpoint or reader endpoint might not be appropriate\. For example, your client application might require more fine\-grained load balancing based on workload type\. In this case, you can configure multiple clients to connect to different reader DB instances in a DB cluster to distribute read workloads\.   
 The following example illustrates an instance endpoint for a DB instance in a Multi\-AZ DB cluster\.   
 `mydbinstance.123456789012.us-east-1.rds.amazonaws.com` 

## Viewing the endpoints for a Multi\-AZ DB cluster<a name="multi-az-db-clusters-concepts-connection-management-viewing"></a>

 In the AWS Management Console, you see the cluster endpoint and the reader endpoint on the details page for each Multi\-AZ DB cluster\. You see the instance endpoint in the details page for each DB instance\. 

 With the AWS CLI, you see the writer and reader endpoints in the output of the [describe\-db\-clusters](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-clusters.html) command\. For example, the following command shows the endpoint attributes for all clusters in your current AWS Region\. 

```
aws rds describe-db-cluster-endpoints
```

 With the Amazon RDS API, you retrieve the endpoints by calling the [DescribeDBClusterEndpoints](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBClusterEndpoints.html) action\. The output also shows Amazon Aurora DB cluster endpoints, if any exist\.

## Using the cluster endpoint<a name="multi-az-db-clusters-concepts-connection-management-endpoints-cluster"></a>

Each Multi\-AZ DB cluster has a single built\-in cluster endpoint, whose name and other attributes are managed by Amazon RDS\. You can't create, delete, or modify this kind of endpoint\. 

 You use the cluster endpoint when you administer your DB cluster, perform extract, transform, load \(ETL\) operations, or develop and test applications\. The cluster endpoint connects to the writer DB instance of the cluster\. The writer DB instance is the only DB instance where you can create tables and indexes, run `INSERT` statements, and perform other DDL and DML operations\. 

 The physical IP address pointed to by the cluster endpoint changes when the failover mechanism promotes a new DB instance to be the writer DB instance for the cluster\. If you use any form of connection pooling or other multiplexing, be prepared to flush or reduce the time\-to\-live for any cached DNS information\. Doing so ensures that you don't try to establish a read/write connection to a DB instance that became unavailable or is now read\-only after a failover\. 

## Using the reader endpoint<a name="multi-az-db-clusters-concepts-connection-management-endpoints-reader"></a>

 You use the reader endpoint for read\-only connections to your Multi\-AZ DB cluster\. This endpoint helps your DB cluster handle a query\-intensive workload\. The reader endpoint is the endpoint that you supply to applications that do reporting or other read\-only operations on the cluster\. The reader endpoint sends connections to available reader DB instances in a Multi\-AZ DB cluster\. 

 Each Multi\-AZ cluster has a single built\-in reader endpoint, whose name and other attributes are managed by Amazon RDS\. You can't create, delete, or modify this kind of endpoint\. 

## Using the instance endpoints<a name="multi-az-db-clusters-concepts-connection-management-endpoints-instance"></a>

 Each DB instance in a Multi\-AZ DB cluster has its own built\-in instance endpoint, whose name and other attributes are managed by Amazon RDS\. You can't create, delete, or modify this kind of endpoint\. With a Multi\-AZ DB cluster, you typically use the writer and reader endpoints more often than the instance endpoints\. 

In day\-to\-day operations, the main way that you use instance endpoints is to diagnose capacity or performance issues that affect one specific DB instance in a Multi\-AZ DB cluster\. While connected to a specific DB instance, you can examine its status variables, metrics, and so on\. Doing this can help you determine what's happening for that DB instance that's different from what's happening for other DB instances in the cluster\. 

## How Multi\-AZ DB endpoints work with high availability<a name="multi-az-db-clusters-concepts-connection-management-endpoints-ha"></a>

For Multi\-AZ DB clusters where high availability is important, use the writer endpoint for read/write or general\-purpose connections and the reader endpoint for read\-only connections\. The writer and reader endpoints manage DB instance failover better than instance endpoints do\. Unlike the instance endpoints, the writer and reader endpoints automatically change which DB instance they connect to if a DB instance in your cluster becomes unavailable\. 

 If the writer DB instance of a DB cluster fails, Amazon RDS automatically fails over to a new writer DB instance\. It does so by promoting a reader DB instance to a new writer DB instance\. If a failover occurs, you can use the writer endpoint to reconnect to the newly promoted writer DB instance\. Or you can use the reader endpoint to reconnect to one of the reader DB instances in the DB cluster\. During a failover, the reader endpoint might direct connections to the new writer DB instance of a DB cluster for a short time after a reader DB instance is promoted to the new writer DB instance\. If you design your own application logic to manage instance endpoint connections, you can manually or programmatically discover the resulting set of available DB instances in the DB cluster\. 