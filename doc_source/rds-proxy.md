# Using Amazon RDS Proxy<a name="rds-proxy"></a>

 By using Amazon RDS Proxy, you can allow your applications to pool and share database connections to improve their ability to scale\. RDS Proxy makes applications more resilient to database failures by automatically connecting to a standby DB instance while preserving application connections\. By using RDS Proxy, you can also enforce AWS Identity and Access Management \(IAM\) authentication for databases, and securely store credentials in AWS Secrets Manager\. 

**Note**  
 RDS Proxy is fully compatible with MySQL and PostgreSQL\. You can enable RDS Proxy for most applications with no code changes\. 

 Using RDS Proxy, you can handle unpredictable surges in database traffic that otherwise might cause issues due to oversubscribing connections or creating new connections at a fast rate\. RDS Proxy establishes a database connection pool and reuses connections in this pool without the memory and CPU overhead of opening a new database connection each time\. To protect the database against oversubscription, you can control the number of database connections that are created\. 

 RDS Proxy queues or throttles application connections that can't be served immediately from the pool of connections\. Although latencies might increase, your application can continue to scale without abruptly failing or overwhelming the database\. If connection requests exceed the limits you specify, RDS Proxy rejects application connections \(that is, it sheds load\)\. At the same time, it maintains predictable performance for the load that can be served with the available capacity\. 

 You can reduce the overhead to process credentials and establish a secure connection for each new connection\. RDS Proxy can handle some of that work on behalf of the database\. 

**Topics**
+ [Supported engines and Region availability for RDS Proxy](#rds-proxy.support)
+ [Quotas and limitations for RDS Proxy](#rds-proxy.limits)
+ [Planning where to use RDS Proxy](rds-proxy-planning.md)
+ [RDS Proxy concepts and terminology](rds-proxy.howitworks.md)
+ [Getting started with RDS Proxy](rds-proxy-setup.md)
+ [Managing an RDS Proxy](rds-proxy-managing.md)
+ [Working with Amazon RDS Proxy endpoints](rds-proxy-endpoints.md)
+ [Monitoring RDS Proxy using Amazon CloudWatch](rds-proxy.monitoring.md)
+ [RDS Proxy command\-line examples](rds-proxy.examples.md)
+ [Troubleshooting for RDS Proxy](rds-proxy.troubleshooting.md)
+ [Using RDS Proxy with AWS CloudFormation](rds-proxy-cfn.md)

## Supported engines and Region availability for RDS Proxy<a name="rds-proxy.support"></a>

RDS Proxy supports the following database engine versions:
+ RDS for MySQL – MySQL 5\.6, 5\.7, and 8\.0
+ RDS for PostgreSQL – version 10\.10 and higher minor versions, version 11\.5 and higher minor versions, and version 12\.5 and higher minor versions

RDS Proxy is available in the following Regions:
+ US East \(Ohio\)
+ US East \(N\. Virginia\)
+ US West \(N\. California\)
+ US West \(Oregon\)
+ Asia Pacific \(Mumbai\)
+ Asia Pacific \(Seoul\)
+ Asia Pacific \(Singapore\)
+ Asia Pacific \(Sydney\)
+ Asia Pacific \(Tokyo\)
+ Canada \(Central\)
+ Europe \(Frankfurt\)
+ Europe \(Ireland\)
+ Europe \(London\)

## Quotas and limitations for RDS Proxy<a name="rds-proxy.limits"></a>

 The following quotas and limitations apply to RDS Proxy: 
+  You can have up to 20 proxies for each AWS account ID\. If your application requires more proxies, you can request additional proxies by opening a ticket with the AWS Support organization\.  
+  Each proxy can have up to 200 associated Secrets Manager secrets\. Thus, each proxy can connect to with up to 200 different user accounts at any given time\. 
+  You can create, view, modify, and delete up to 20 endpoints for each proxy\. These endpoints are in addition to the default endpoint that's automatically created for each proxy\. 
+  In an Aurora cluster, all of the connections using the default proxy endpoint are handled by the Aurora writer instance\. To perform load balancing for read\-intensive workloads, you can create a read\-only endpoint for a proxy\. That endpoint passes connections to the reader endpoint of the cluster\. That way, your proxy connections can take advantage of Aurora read scalability\. For more information, see [Overview of proxy endpoints](rds-proxy-endpoints.md#rds-proxy-endpoints-overview)\. 

  For RDS DB instances in replication configurations, you can associate a proxy only with the writer DB instance, not a read replica\.
+ You can't use RDS Proxy with Aurora Serverless clusters\.
+ Using RDS Proxy with Aurora clusters that are part of an Aurora global database isn't currently supported\.
+  Your RDS Proxy must be in the same virtual private cloud \(VPC\) as the database\. The proxy can't be publicly accessible, although the database can be\. 
**Note**  
 For Aurora DB clusters, you can turn on cross\-VPC access\. To do this, create an additional endpoint for a proxy and specify a different VPC, subnets, and security groups with that endpoint\. For more information, see [Accessing Aurora and RDS databases across VPCs](rds-proxy-endpoints.md#rds-proxy-cross-vpc)\. 
+  You can't use RDS Proxy with a VPC that has its tenancy set to `dedicated`\. 
+  If you use RDS Proxy with an RDS DB instance or Aurora DB cluster that has IAM authentication enabled, make sure that all users who connect through a proxy authenticate through user names and passwords\. See [Setting up AWS Identity and Access Management \(IAM\) policies](rds-proxy-setup.md#rds-proxy-iam-setup) for details about IAM support in RDS Proxy\. 
+  You can't use RDS Proxy with custom DNS\. 
+  RDS Proxy is available for the MySQL and PostgreSQL engine families\. 
+  Each proxy can be associated with a single target DB instance or cluster\. However, you can associate multiple proxies with the same DB instance or cluster\. 

 The following RDS Proxy limitations apply to MySQL: 
+ RDS Proxy doesn't support the MySQL `sha256_password` and `caching_sha2_password` authentication plugins\. These plugins implement SHA\-256 hashing for user account passwords\. 
+  Currently, all proxies listen on port 3306 for MySQL\. The proxies still connect to your database using the port that you specified in the database settings\. 
+  You can't use RDS Proxy with self\-managed MySQL databases in EC2 instances\.
+  You can't use RDS Proxy with an RDS for MySQL DB instance that has the `read_only` parameter in its DB parameter group set to `1`\.
+  Proxies don't support MySQL compressed mode\. For example, they don't support the compression used by the `--compress` or `-C` options of the `mysql` command\.
+  Some SQL statements and functions can change the connection state without causing pinning\. For the most current pinning behavior, see [Avoiding pinning](rds-proxy-managing.md#rds-proxy-pinning)\.

 The following RDS Proxy limitations apply to PostgreSQL:
+  Currently, all proxies listen on port 5432 for PostgreSQL\.
+  Query cancellation isn't supported for PostgreSQL\.
+  The results of the PostgreSQL function [lastval](https://www.postgresql.org/docs/current/functions-sequence.html) aren't always accurate\. As a work\-around, use the [INSERT](https://www.postgresql.org/docs/current/sql-insert.html) statement with the `RETURNING` clause\.