# Using Amazon RDS Proxy<a name="rds-proxy"></a>

 By using Amazon RDS Proxy, you can allow your applications to pool and share database connections to improve their ability to scale\. RDS Proxy makes applications more resilient to database failures by automatically connecting to a standby DB instance while preserving application connections\. By using RDS Proxy, you can also enforce AWS Identity and Access Management \(IAM\) authentication for databases, and securely store credentials in AWS Secrets Manager\. 

 Using RDS Proxy, you can handle unpredictable surges in database traffic\. Otherwise, these surges might cause issues due to oversubscribing connections or creating new connections at a fast rate\. RDS Proxy establishes a database connection pool and reuses connections in this pool\. This approach avoids the memory and CPU overhead of opening a new database connection each time\. To protect the database against oversubscription, you can control the number of database connections that are created\. 

 RDS Proxy queues or throttles application connections that can't be served immediately from the pool of connections\. Although latencies might increase, your application can continue to scale without abruptly failing or overwhelming the database\. If connection requests exceed the limits you specify, RDS Proxy rejects application connections \(that is, it sheds load\)\. At the same time, it maintains predictable performance for the load that can be served with the available capacity\. 

 You can reduce the overhead to process credentials and establish a secure connection for each new connection\. RDS Proxy can handle some of that work on behalf of the database\. 

 RDS Proxy is fully compatible with the engine versions that it supports\. You can enable RDS Proxy for most applications with no code changes\. 

**Topics**
+ [Region and version availability](#rds-proxy.RegionVersionAvailability)
+ [Quotas and limitations for RDS Proxy](#rds-proxy.limitations)
+ [Planning where to use RDS Proxy](rds-proxy-planning.md)
+ [RDS Proxy concepts and terminology](rds-proxy.howitworks.md)
+ [Getting started with RDS Proxy](rds-proxy-setup.md)
+ [Managing an RDS Proxy](rds-proxy-managing.md)
+ [Working with Amazon RDS Proxy endpoints](rds-proxy-endpoints.md)
+ [Monitoring RDS Proxy metrics with Amazon CloudWatch](rds-proxy.monitoring.md)
+ [Working with RDS Proxy events](rds-proxy.events.md)
+ [RDS Proxy command\-line examples](rds-proxy.examples.md)
+ [Troubleshooting for RDS Proxy](rds-proxy.troubleshooting.md)
+ [Using RDS Proxy with AWS CloudFormation](rds-proxy-cfn.md)

## Region and version availability<a name="rds-proxy.RegionVersionAvailability"></a>

Feature availability and support varies across specific versions of each database engine, and across AWS Regions\. For more information on version and Region availability of Amazon RDS with RDS Proxy, see [Amazon RDS Proxy](Concepts.RDS_Fea_Regions_DB-eng.Feature.RDSProxy.md)\.

## Quotas and limitations for RDS Proxy<a name="rds-proxy.limitations"></a>

 The following quotas and limitations apply to RDS Proxy: 
+  You can have up to 20 proxies for each AWS account ID\. If your application requires more proxies, you can request additional proxies by opening a ticket with the AWS Support organization\.  
+  Each proxy can have up to 200 associated Secrets Manager secrets\. Thus, each proxy can connect to with up to 200 different user accounts at any given time\. 
+  You can create, view, modify, and delete up to 20 endpoints for each proxy\. These endpoints are in addition to the default endpoint that's automatically created for each proxy\. 
+  In an Aurora cluster, all of the connections using the default proxy endpoint are handled by the Aurora writer instance\. To perform load balancing for read\-intensive workloads, you can create a read\-only endpoint for a proxy\. That endpoint passes connections to the reader endpoint of the cluster\. That way, your proxy connections can take advantage of Aurora read scalability\. For more information, see [Overview of proxy endpoints](rds-proxy-endpoints.md#rds-proxy-endpoints-overview)\. 

  For RDS DB instances in replication configurations, you can associate a proxy only with the writer DB instance, not a read replica\.
+ You can use RDS Proxy with Aurora Serverless v2 clusters, but not with Aurora Serverless v1 clusters\.
+  Your RDS Proxy must be in the same virtual private cloud \(VPC\) as the database\. The proxy can't be publicly accessible, although the database can be\. For example, if you're prototyping on a local host, you can't connect to your RDS Proxy unless you set up dedicated networking\. This is the case because your local host is outside of the proxy's VPC\.
**Note**  
 For Aurora DB clusters, you can turn on cross\-VPC access\. To do this, create an additional endpoint for a proxy and specify a different VPC, subnets, and security groups with that endpoint\. For more information, see [Accessing Aurora and RDS databases across VPCs](rds-proxy-endpoints.md#rds-proxy-cross-vpc)\. 
+  You can't use RDS Proxy with a VPC that has its tenancy set to `dedicated`\. 
+  If you use RDS Proxy with an RDS DB instance or Aurora DB cluster that has IAM authentication enabled, check user authentication\. Make sure that all users who connect through a proxy authenticate through user names and passwords\. For details about IAM support in RDS Proxy, see [Setting up AWS Identity and Access Management \(IAM\) policies](rds-proxy-setup.md#rds-proxy-iam-setup)\. 
+  You can't use RDS Proxy with custom DNS\. 
+  Each proxy can be associated with a single target DB instance or cluster\. However, you can associate multiple proxies with the same DB instance or cluster\. 
+ Any statement with a text size greater than 16 KB causes the proxy to pin the session to the current connection\.

For additional limitations for each DB engine, see the following sections:
+ [Additional limitations for RDS for MariaDB](#rds-proxy.limitations-mdb)
+ [Additional limitations for RDS for Microsoft SQL Server](#rds-proxy.limitations-ms)
+ [Additional limitations for RDS for MySQL](#rds-proxy.limitations-my)
+ [Additional limitations for RDS for PostgreSQL](#rds-proxy.limitations-pg)

### Additional limitations for RDS for MariaDB<a name="rds-proxy.limitations-mdb"></a>

 The following additional limitations apply to RDS Proxy with RDS for MariaDB databases:
+  Currently, all proxies listen on port 3306 for MariaDB\. The proxies still connect to your database using the port that you specified in the database settings\. 
+ You can't use RDS Proxy with self\-managed MariaDB databases in Amazon EC2 instances\.
+ You can't use RDS Proxy with an RDS for MariaDB DB instance that has the `read_only` parameter in its DB parameter group set to `1`\.
+ RDS Proxy doesn't support compressed mode\. For example, it doesn't support the compression used by the `--compress` or `-C` options of the `mysql` command\.
+ Some SQL statements and functions can change the connection state without causing pinning\. For the most current pinning behavior, see [Avoiding pinning](rds-proxy-managing.md#rds-proxy-pinning)\.
+ RDS Proxy doesn't support the MariaDB `auth_ed25519` plugin\.
+ RDS Proxy doesn't support Transport Layer Security \(TLS\) version 1\.3 for MariaDB databases\.

**Important**  
 For proxies associated with MariaDB databases, don't set the configuration parameter `sql_auto_is_null` to `true` or a nonzero value in the initialization query\. Doing so might cause incorrect application behavior\. 

### Additional limitations for RDS for Microsoft SQL Server<a name="rds-proxy.limitations-ms"></a>

 The following additional limitations apply to RDS Proxy with RDS for Microsoft SQL Server databases:
+ The number of Secrets Manager secrets that you need to create for a proxy depends on the collation that your DB instance uses\. For example, suppose that your DB instance uses case\-sensitive collation\. If your application accepts both "Admin" and "admin," then your proxy needs two separate secrets\. For more information about collation in SQL Server, see the [ Microsoft SQL Server](https://docs.microsoft.com/en-us/sql/relational-databases/collations/collation-and-unicode-support?view=sql-server-ver16) documentation\.
+ RDS Proxy doesn't support connections that use Active Directory\.
+ You can't use IAM authentication with clients that don't support token properties\. For more information, see [Considerations for connecting to a proxy with Microsoft SQL Server](rds-proxy-setup.md#rds-proxy-connecting-sqlserver)\.
+ The results of `@@IDENTITY`, `@@ROWCOUNT`, and `SCOPE_IDENTITY` aren't always accurate\. As a work\-around, retrieve their values in the same session statement to ensure that they return the correct information\.
+ If the connection uses multiple active result sets \(MARS\), RDS Proxy doesn't run the initialization queries\. For information about MARS, see the [ Microsoft SQL Server](https://docs.microsoft.com/en-us/sql/relational-databases/native-client/features/using-multiple-active-result-sets-mars?view=sql-server-ver16) documentation\.

### Additional limitations for RDS for MySQL<a name="rds-proxy.limitations-my"></a>

 The following additional limitations apply to RDS Proxy with RDS for MySQL databases:
+ RDS Proxy doesn't support the MySQL `sha256_password` and `caching_sha2_password` authentication plugins\. These plugins implement SHA\-256 hashing for user account passwords\.
+  Currently, all proxies listen on port 3306 for MySQL\. The proxies still connect to your database using the port that you specified in the database settings\. 
+  You can't use RDS Proxy with self\-managed MySQL databases in EC2 instances\.
+  You can't use RDS Proxy with an RDS for MySQL DB instance that has the `read_only` parameter in its DB parameter group set to `1`\.
+ RDS Proxy doesn't support MySQL compressed mode\. For example, it doesn't support the compression used by the `--compress` or `-C` options of the `mysql` command\.
+  Some SQL statements and functions can change the connection state without causing pinning\. For the most current pinning behavior, see [Avoiding pinning](rds-proxy-managing.md#rds-proxy-pinning)\.

**Important**  
 For proxies associated with MySQL databases, don't set the configuration parameter `sql_auto_is_null` to `true` or a nonzero value in the initialization query\. Doing so might cause incorrect application behavior\. 

### Additional limitations for RDS for PostgreSQL<a name="rds-proxy.limitations-pg"></a>

 The following additional limitations apply to RDS Proxy with RDS for PostgreSQL databases:
+ RDS Proxy doesn't support session pinning filters for PostgreSQL\.
+  Currently, all proxies listen on port 5432 for PostgreSQL\.
+ For PostgreSQL, RDS Proxy doesn't currently support canceling a query from a client by issuing a `CancelRequest`\. This is the case, for example, when you cancel a long\-running query in an interactive psql session by using Ctrl\+C\. 
+  The results of the PostgreSQL function [lastval](https://www.postgresql.org/docs/current/functions-sequence.html) aren't always accurate\. As a work\-around, use the [INSERT](https://www.postgresql.org/docs/current/sql-insert.html) statement with the `RETURNING` clause\.
+ RDS Proxy doesn't multiplex connections when your client application drivers use the PostgreSQL extended query protocol\.
+ RDS Proxy currently doesn't support streaming replication mode\.

**Important**  
For existing proxies with PostgreSQL databases, if you modify the database authentication to use `SCRAM` only, the proxy becomes unavailable for up to 60 seconds\. To avoid the issue, do one of the following:  
Ensure that the database allows both `SCRAM` and `MD5` authentication\.
To use only `SCRAM` authentication, create a new proxy, migrate your application traffic to the new proxy, then delete the proxy previously associated with the database\.