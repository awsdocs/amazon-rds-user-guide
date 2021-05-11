# Managing connections with Amazon RDS Proxy<a name="rds-proxy"></a>

 By using Amazon RDS Proxy, you can allow your applications to pool and share database connections to improve their ability to scale\. RDS Proxy makes applications more resilient to database failures by automatically connecting to a standby DB instance while preserving application connections\. RDS Proxy also enables you to enforce AWS Identity and Access Management \(IAM\) authentication for databases, and securely store credentials in AWS Secrets Manager\. 

**Note**  
 RDS Proxy is fully compatible with MySQL and PostgreSQL\. You can enable RDS Proxy for most applications with no code changes\. 

 Using RDS Proxy, you can handle unpredictable surges in database traffic that otherwise might cause issues due to oversubscribing connections or creating new connections at a fast rate\. RDS Proxy establishes a database connection pool and reuses connections in this pool without the memory and CPU overhead of opening a new database connection each time\. To protect the database against oversubscription, you can control the number of database connections that are created\. 

 RDS Proxy queues or throttles application connections that can't be served immediately from the pool of connections\. Although latencies might increase, your application can continue to scale without abruptly failing or overwhelming the database\. If connection requests exceed the limits you specify, RDS Proxy rejects application connections \(that is, it sheds load\)\. At the same time, it maintains predictable performance for the load that can be served with the available capacity\. 

 You can reduce the overhead to process credentials and establish a secure connection for each new connection\. RDS Proxy can handle some of that work on behalf of the database\. 

**Topics**
+ [RDS Proxy concepts and terminology](#rds-proxy.howitworks)
+ [Planning for and setting up RDS Proxy](#rds-proxy-setup)
+ [Connecting to a database through RDS Proxy](#rds-proxy-connecting)
+ [Managing an RDS Proxy](#rds-proxy-managing)
+ [Monitoring RDS Proxy using Amazon CloudWatch](#rds-proxy.monitoring)
+ [Endpoints for Amazon RDS Proxy](#rds-proxy-endpoints)
+ [Command\-line examples for RDS Proxy](#rds-proxy.examples)
+ [Troubleshooting for RDS Proxy](#rds-proxy.troubleshooting)
+ [Using RDS Proxy with AWS CloudFormation](#rds-proxy-cfn)

## RDS Proxy concepts and terminology<a name="rds-proxy.howitworks"></a>

 You can simplify connection management for your Amazon RDS DB instances and Amazon Aurora DB clusters by using RDS Proxy\. 

 RDS Proxy handles the network traffic between the client application and the database\. It does so in an active way first by understanding the database protocol\. It then adjusts its behavior based on the SQL operations from your application and the result sets from the database\. 

 RDS Proxy reduces the memory and CPU overhead for connection management on your database\. The database needs less memory and CPU resources when applications open many simultaneous connections\. It also doesn't require logic in your applications to close and reopen connections that stay idle for a long time\. Similarly, it requires less application logic to reestablish connections in case of a database problem\. 

 The infrastructure for RDS Proxy is highly available and deployed over multiple Availability Zones \(AZs\)\. The computation, memory, and storage for RDS Proxy are independent of your RDS DB instances and Aurora DB clusters\. This separation helps lower overhead on your database servers, so that they can devote their resources to serving database workloads\. The RDS Proxy compute resources are serverless, automatically scaling based on your database workload\. 

**Topics**
+ [Overview of RDS Proxy concepts](#rds-proxy-overview)
+ [Connection pooling](#rds-proxy-connection-pooling)
+ [RDS Proxy security](#rds-proxy-security)
+ [Failover](#rds-proxy-failover)
+ [Transactions](#rds-proxy-transactions)

### Overview of RDS Proxy concepts<a name="rds-proxy-overview"></a>

 RDS Proxy handles the infrastructure to perform connection pooling and the other features described following\. You see the proxies represented in the RDS console on the **Proxies** page\. 

 Each proxy handles connections to a single RDS DB instance or Aurora DB cluster\. The proxy automatically determines the current writer instance for RDS Multi\-AZ DB instances and Aurora provisioned clusters\. For Aurora multi\-master clusters, the proxy connects to one of the writer instances and uses the other writer instances as hot standby targets\.  

 The connections that a proxy keeps open and available for your database application to use make up the *connection pool*\. 

 By default, RDS Proxy can reuse a connection after each transaction in your session\. This transaction\-level reuse is called *multiplexing*\. When RDS Proxy temporarily removes a connection from the connection pool to reuse it, that operation is called *borrowing* the connection\. When it's safe to do so, RDS Proxy returns that connection to the connection pool\. 

 In some cases, RDS Proxy can't be sure that it's safe to reuse a database connection outside of the current session\. In these cases, it keeps the session on the same connection until the session ends\. This fallback behavior is called *pinning*\. 

 A proxy has a default endpoint\. You connect to this endpoint when you work with an RDS DB instance or Aurora DB cluster, instead of connecting to the read/write endpoint that connects directly to the instance or cluster\. The special\-purpose endpoints for an Aurora cluster remain available for you to use\. For Aurora DB clusters, you can also create additional read/write and read\-only endpoints\. For more information, see [Overview of proxy endpoints](#rds-proxy-endpoints-overview)\. 

 For example, you can still connect to the cluster endpoint for read/write connections without connection pooling\. You can still connect to the reader endpoint for load\-balanced read\-only connections\. You can still connect to the instance endpoints for diagnosis and troubleshooting of specific DB instances within an Aurora cluster\. If you are using other AWS services such as AWS Lambda to connect to RDS databases, you change their connection settings to use the proxy endpoint\. For example, you specify the proxy endpoint to allow Lambda functions to access your database while taking advantage of RDS Proxy functionality\. 

 Each proxy contains a target group\. This *target group* embodies the RDS DB instance or Aurora DB cluster that the proxy can connect to\. For an Aurora cluster, by default the target group is associated with all the DB instances in that cluster\. That way, the proxy can connect to whichever Aurora DB instance is promoted to be the writer instance in the cluster\. The RDS DB instance associated with a proxy, or the Aurora DB cluster and its instances, are called the *targets* of that proxy\. For convenience, when you create a proxy through the console, RDS Proxy also creates the corresponding target group and registers the associated targets automatically\. 

 An *engine family* is a related set of database engines that use the same DB protocol\. You choose the engine family for each proxy that you create\.  

### Connection pooling<a name="rds-proxy-connection-pooling"></a>

 Each proxy performs connection pooling for the writer instance of its associated RDS or Aurora database\. *Connection pooling* is an optimization that reduces the overhead associated with opening and closing connections and with keeping many connections open simultaneously\. This overhead includes memory needed to handle each new connection\. It also involves CPU overhead to close each connection and open a new one, such as Transport Layer Security/Secure Sockets Layer \(TLS/SSL\) handshaking, authentication, negotiating capabilities, and so on\. Connection pooling simplifies your application logic\. You don't need to write application code to minimize the number of simultaneous open connections\. 

 Each proxy also performs connection multiplexing, also known as connection reuse\. With *multiplexing*, RDS Proxy perform all the operations for a transaction using one underlying database connection, then can use a different connection for the next transaction\. You can open many simultaneous connections to the proxy, and the proxy keeps a smaller number of connections open to the DB instance or cluster\. Doing so further minimizes the memory overhead for connections on the database server\. This technique also reduces the chance of "too many connections" errors\. 

### RDS Proxy security<a name="rds-proxy-security"></a>

 RDS Proxy uses the existing RDS security mechanisms such as TLS/SSL and AWS Identity and Access Management \(IAM\)\. For general information about those security features, see [Security in Amazon RDS](UsingWithRDS.md)\. If you aren't familiar with how RDS and Aurora work with authentication, authorization, and other areas of security, make sure to familiarize yourself with how RDS and Aurora work with those areas first\.  

 RDS Proxy can act as an additional layer of security between client applications and the underlying database\. For example, you can connect to the proxy using TLS 1\.2, even if the underlying DB instance supports only TLS 1\.0 or 1\.1\. You can connect to the proxy using an IAM role, even if the proxy connects to the database using the native user and password authentication method\. By using this technique, you can enforce strong authentication requirements for database applications without a costly migration effort for the DB instances themselves\. 

 You store the database credentials used by RDS Proxy in AWS Secrets Manager\. Each database user for the RDS DB instance or Aurora DB cluster accessed by a proxy must have a corresponding secret in Secrets Manager\. You can also set up IAM authentication for users of RDS Proxy\. By doing so, you can enforce IAM authentication for database access even if the databases use native password authentication\. We recommend using these security features instead of embedding database credentials in your application code\. 

#### Using TLS/SSL with RDS Proxy<a name="rds-proxy-security.tls"></a>

 You can connect to RDS Proxy using the TLS/SSL protocol\. 

**Note**  
 RDS Proxy uses certificates from the AWS Certificate Manager \(ACM\)\. If you use RDS Proxy, when you rotate your TLS/SSL certificate you don't need to update applications that use RDS Proxy connections\. 

 To enforce TLS for all connections between the proxy and your database, you can specify a setting **Require Transport Layer Security** when you create or modify a proxy\. 

 RDS Proxy can also ensure that your session uses TLS/SSL between your client and the RDS Proxy endpoint\. To have RDS Proxy do so, specify the requirement on the client side\. SSL session variables are not set for SSL connections to a database using RDS Proxy\. 
+  For RDS for MySQL and Aurora MySQL, specify the requirement on the client side with the `--ssl-mode` parameter when you run the `mysql` command\. 
+  For Amazon RDS PostgreSQL and Aurora PostgreSQL, specify `sslmode=require` as part of the `conninfo` string when you run the `psql` command\. 

 RDS Proxy supports TLS protocol version 1\.0, 1\.1, and 1\.2\. You can connect to the proxy using a higher version of TLS than you use in the underlying database\.  

 By default, client programs establish an encrypted connection with RDS Proxy, with further control available through the `--ssl-mode` option\. From the client side, RDS Proxy supports all SSL modes\. 

 For the client, the SSL modes are the following: 

**PREFERRED**  
 SSL is the first choice, but it isn't required\. 

**DISABLED**  
 No SSL is allowed\. 

**REQUIRED**  
 Enforce SSL\. 

**VERIFY\_CA**  
 Enforce SSL and verify the certificate authority \(CA\)\. 

**VERIFY\_IDENTITY**  
 Enforce SSL and verify the CA and CA hostname\.   
 You can use the SSL mode `VERIFY_IDENTITY` when connecting to the default proxy endpoint\. You can't use that SSL mode when you connect to proxy endpoints that you create\. 

 When using a client with `--ssl-mode` `VERIFY_CA` or `VERIFY_IDENTITY`, specify the `--ssl-ca` option pointing to a CA in `.pem` format\. For a `.pem` file that you can use, download the [Amazon root CA 1 trust store](https://www.amazontrust.com/repository/AmazonRootCA1.pem) from Amazon Trust Services\. 

 RDS Proxy uses wildcard certificates, which apply to a both a domain and its subdomains\. If you use the `mysql` client to connect with SSL mode `VERIFY_IDENTITY`, currently you must use the MySQL 8\.0\-compatible `mysql` command\. 

### Failover<a name="rds-proxy-failover"></a>

 *Failover* is a high\-availability feature that replaces a database instance with another one when the original instance becomes unavailable\. A failover might happen because of a problem with a database instance\. It might also be part of normal maintenance procedures, such as during a database upgrade\. Failover applies to RDS DB instances in a Multi\-AZ configuration, and Aurora DB clusters with one or more reader instances in addition to the writer instance\. 

 Connecting through a proxy makes your application more resilient to database failovers\. When the original DB instance becomes unavailable, RDS Proxy connects to the standby database without dropping idle application connections\. Doing so helps to speed up and simplify the failover process\. The result is faster failover that's less disruptive to your application than a typical reboot or database problem\. 

 Without RDS Proxy, a failover involves a brief outage\. During the outage, you can't perform write operations on that database\. Any existing database connections are disrupted and your application must reopen them\. The database becomes available for new connections and write operations when a read\-only DB instance is promoted to take the place of the one that's unavailable\. 

 During DB failovers, RDS Proxy continues to accept connections at the same IP address and automatically directs connections to the new primary DB instance\. Clients connecting through RDS Proxy are not susceptible to the following: 
+  Domain Name System \(DNS\) propagation delays on failover\. 
+  Local DNS caching\. 
+  Connection timeouts\. 
+  Uncertainty about which DB instance is the current writer\. 
+  Waiting for a query response from a former writer that became unavailable without closing connections\. 

 For applications that maintain their own connection pool, going through RDS Proxy means that most connections stay alive during failovers or other disruptions\. Only connections that are in the middle of a transaction or SQL statement are canceled\. RDS Proxy immediately accepts new connections\. When the database writer is unavailable, RDS Proxy queues up incoming requests\. 

 For applications that don't maintain their own connection pools, RDS Proxy offers faster connection rates and more open connections\. It offloads the expensive overhead of frequent reconnects from the database\. It does so by reusing database connections maintained in the RDS Proxy connection pool\. This approach is particularly important for TLS connections, where setup costs are significant\. 

### Transactions<a name="rds-proxy-transactions"></a>

 All the statements within a single transaction always use the same underlying database connection\. The connection becomes available for use by a different session when the transaction ends\. Using the transaction as the unit of granularity has the following consequences: 
+  Connection reuse can happen after each individual statement when the RDS for MySQL or Aurora MySQL `autocommit` setting is enabled\. 
+  Conversely, when the `autocommit` setting is disabled, the first statement you issue in a session begins a new transaction\. Thus, if you enter a sequence of `SELECT`, `INSERT`, `UPDATE`, and other data manipulation language \(DML\) statements, connection reuse doesn't happen until you issue a `COMMIT`, `ROLLBACK`, or otherwise end the transaction\. 
+  Entering a data definition language \(DDL\) statement causes the transaction to end after that statement completes\. 

 RDS Proxy detects when a transaction ends through the network protocol used by the database client application\. Transaction detection doesn't rely on keywords such as `COMMIT` or `ROLLBACK` appearing in the text of the SQL statement\. 

 In some cases, RDS Proxy might detect a database request that makes it impractical to move your session to a different connection\. In these cases, it turns off multiplexing for that connection the remainder of your session\. The same rule applies if RDS Proxy can't be certain that multiplexing is practical for the session\. This operation is called *pinning*\. For ways to detect and minimize pinning, see [Avoiding pinning](#rds-proxy-pinning)\. 

## Planning for and setting up RDS Proxy<a name="rds-proxy-setup"></a>

 In the following sections, you can find how to set up RDS Proxy\. You can also find how to set the related security options that control who can access each proxy and how each proxy connects to DB instances\. 

**Topics**
+ [Limits for RDS Proxy](#rds-proxy.limits)
+ [Identifying DB instances, clusters, and applications to use with RDS Proxy](#rds-proxy-planning)
+ [Setting up network prerequisites](#rds-proxy-network-prereqs)
+ [Setting up database credentials in AWS Secrets Manager](#rds-proxy-secrets-arns)
+ [Setting up AWS Identity and Access Management \(IAM\) policies](#rds-proxy-iam-setup)
+ [Creating an RDS Proxy](#rds-proxy-creating)
+ [Viewing an RDS Proxy](#rds-proxy-viewing)

### Limits for RDS Proxy<a name="rds-proxy.limits"></a>

 The following limitations apply to RDS Proxy: 
+  RDS Proxy is available only in certain AWS Regions only\. For more information, see [Amazon RDS Proxy](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Concepts.AuroraFeaturesRegionsDBEngines.grids.html#Concepts.Aurora_Fea_Regions_DB-eng.Feature.RDS_Proxy)\. 

   You can have up to 20 proxies for each AWS account ID\. If your application requires more proxies, you can request additional proxies by opening a ticket with the AWS Support organization\.  
+  Each proxy can have up to 200 associated Secrets Manager secrets\. Thus, each proxy can connect to with up to 200 different user accounts at any given time\. 
+  You can create, view, modify, and delete up to 20 endpoints for each proxy\. These endpoints are in addition to the default endpoint that's automatically created for each proxy\. 
+  In an Aurora cluster, all of the connections using the default proxy endpoint are handled by the Aurora writer instance\. To perform load balancing for read\-intensive workloads, you can create a read\-only endpoint for a proxy\. That endpoint passes connections to the reader endpoint of the cluster\. That way, your proxy connections can take advantage of Aurora read scalability\. For more information, see [Overview of proxy endpoints](#rds-proxy-endpoints-overview)\. 

   For RDS DB instances in replication configurations, you can associate a proxy only with the writer DB instance, not a read replica\. 
+  You can't use RDS Proxy with Aurora Serverless clusters\. 
+  You can't use RDS Proxy with Aurora clusters that are part of an Aurora global database\. 
+  Your RDS Proxy must be in the same VPC as the database\. The proxy can't be publicly accessible, although the database can be\. 
**Note**  
 For Aurora DB clusters, you can enable cross\-VPC access by creating an additional endpoint for a proxy and specifying a different VPC, subnets, and security groups with that endpoint\. For more information, see [Accessing Aurora and RDS databases across VPCs](#rds-proxy-cross-vpc)\. 
+  You can't use RDS Proxy with a VPC that has dedicated tenancy\. 
+  If you use RDS Proxy with an RDS DB instance or Aurora DB cluster that has IAM authentication enabled, make sure that all users who connect through a proxy authenticate through user names and passwords\. See [Setting up AWS Identity and Access Management \(IAM\) policies](#rds-proxy-iam-setup) for details about IAM support in RDS Proxy\. 
+  You can't use RDS Proxy with custom DNS\. 
+  RDS Proxy is available for the MySQL and PostgreSQL engine families\. 
+  Each proxy can be associated with a single target DB instance or cluster\. However, you can associate multiple proxies with the same DB instance or cluster\. 

 The following RDS Proxy prerequisites and limitations apply to MySQL: 
+  For RDS for MySQL, RDS Proxy supports MySQL 5\.6 and 5\.7\. For Aurora MySQL, RDS Proxy supports version 1 \(compatible with MySQL 5\.6\) and version 2 \(compatible with MySQL 5\.7\)\. 
+  Currently, all proxies listen on port 3306 for MySQL\. The proxies still connect to your database using the port that you specified in the database settings\. 
+  You can't use RDS Proxy with RDS for MySQL 8\.0\. 
+  You can't use RDS Proxy with self\-managed MySQL databases in EC2 instances\. 
+  Proxies don't support MySQL compressed mode\. For example, they don't support the compression used by the `--compress` or `-C` options of the `mysql` command\. 
+  Some SQL statements and functions can change the connection state without causing pinning\. For the most current pinning behavior, see [Avoiding pinning](#rds-proxy-pinning)\. 

 The following RDS Proxy prerequisites and limitations apply to PostgreSQL: 
+  For RDS PostgreSQL, RDS Proxy supports version 10\.10 and higher minor versions, and version 11\.5 and higher minor versions\. For Aurora PostgreSQL, RDS Proxy supports version 10\.11 and higher minor versions, and 11\.6 and higher minor versions\. 
+  Currently, all proxies listen on port 5432 for PostgreSQL\. 
+  Query cancellation isn't supported for PostgreSQL\. 
+  The results of the PostgreSQL function [lastval](https://www.postgresql.org/docs/current/functions-sequence.html) aren't always accurate\. As a work\-around, use the [INSERT](https://www.postgresql.org/docs/current/sql-insert.html) statement with the `RETURNING` clause\. 

### Identifying DB instances, clusters, and applications to use with RDS Proxy<a name="rds-proxy-planning"></a>

 You can determine which of your DB instances, clusters, and applications might benefit the most from using RDS Proxy\. To do so, consider these factors: 
+  RDS Proxy is highly available and deployed over multiple Availability Zones \(AZs\)\. To ensure overall high availability for your database, deploy your Amazon RDS DB instance or Aurora cluster in a Multi\-AZ configuration\. 
+  Any DB instance or cluster that encounters "too many connections" errors is a good candidate for associating with a proxy\. The proxy enables applications to open many client connections, while the proxy manages a smaller number of long\-lived connections to the DB instance or cluster\. 
+  For DB instances or clusters that use smaller AWS instance classes, such as T2 or T3, using a proxy can help avoid out\-of\-memory conditions\. It can also help reduce the CPU overhead for establishing connections\. These conditions can occur when dealing with large numbers of connections\. 
+  You can monitor certain Amazon CloudWatch metrics to determine whether a DB instance or cluster is approaching certain types of limit\. These limits are for the number of connections and the memory associated with connection management\. You can also monitor certain CloudWatch metrics to determine whether a DB instance or cluster is handling many short\-lived connections\. Opening and closing such connections can impose performance overhead on your database\. For information about the metrics to monitor, see [Monitoring RDS Proxy using Amazon CloudWatchMonitoring RDS Proxy](#rds-proxy.monitoring)\. 
+  AWS Lambda functions can also be good candidates for using a proxy\. These functions make frequent short database connections that benefit from connection pooling offered by RDS Proxy\. You can take advantage of any IAM authentication you already have for Lambda functions, instead of managing database credentials in your Lambda application code\. 
+  Applications that use languages and frameworks such as PHP and Ruby on Rails are typically good candidates for using a proxy\. Such applications typically open and close large numbers of database connections, and don't have built\-in connection pooling mechanisms\. 
+  Applications that keep a large number of connections open for long periods are typically good candidates for using a proxy\. Applications in industries such as software as a service \(SaaS\) or ecommerce often minimize the latency for database requests by leaving connections open\. With RDS Proxy, an application can keep more connections open than it can when connecting directly to the DB instance or cluster\. 
+  You might not have adopted IAM authentication and Secrets Manager due to the complexity of setting up such authentication for all DB instances and clusters\. If so, you can leave the existing authentication methods in place and delegate the authentication to a proxy\. The proxy can enforce the authentication policies for client connections for particular applications\. You can take advantage of any IAM authentication you already have for Lambda functions, instead of managing database credentials in your Lambda application code\. 

### Setting up network prerequisites<a name="rds-proxy-network-prereqs"></a>

 Using RDS Proxy requires you to have a set of networking resources in place\. These include a virtual private cloud \(VPC\), two or more subnets, an Amazon EC2 instance within the same VPC, and an internet gateway\. If you've successfully connected to any RDS DB instances or Aurora DB clusters, you already have the required network resources\. 

 The following Linux example shows AWS CLI commands that examine the VPCs and subnets owned by your AWS account\. In particular, you pass subnet IDs as parameters when you create a proxy using the CLI\. 

```
aws ec2 describe-vpcs
aws ec2 describe-internet-gateways
aws ec2 describe-subnets --query '*[].[VpcId,SubnetId]' --output text | sort
```

 The following Linux example shows AWS CLI commands to determine the subnet IDs corresponding to a specific Aurora DB cluster or RDS DB instance\. For an Aurora cluster, first you find the ID for one of the associated DB instances\. You can extract the subnet IDs used by that DB instance by examining the nested fields within the `DBSubnetGroup` and `Subnets` attributes in the describe output for the DB instance\. You specify some or all of those subnet IDs when setting up a proxy for that database server\. 

```
$ # Optional first step, only needed if you're starting from an Aurora cluster. Find the ID of any DB instance in the cluster.
$  aws rds describe-db-clusters --db-cluster-identifier my_cluster_id --query '*[].[DBClusterMembers]|[0]|[0][*].DBInstanceIdentifier' --output text
my_instance_id
instance_id_2
instance_id_3
...

$ # From the DB instance, trace through the DBSubnetGroup and Subnets to find the subnet IDs.
$ aws rds describe-db-instances --db-instance-identifier my_instance_id --query '*[].[DBSubnetGroup]|[0]|[0]|[Subnets]|[0]|[*].SubnetIdentifier' --output text
subnet_id_1
subnet_id_2
subnet_id_3
...
```

 As an alternative, you can first find the VPC ID for the DB instance\. Then you can examine the VPC to find its subnets\. The following Linux example shows how\. 

```
$ # From the DB instance, find the VPC.
$ aws rds describe-db-instances --db-instance-identifier my_instance_id --query '*[].[VpcId]' --output text
my_vpc_id

$ aws ec2 describe-subnets --filters Name=vpc-id,Values=my_vpc_id --query '*[].[SubnetId]' --output text
subnet_id_1
subnet_id_2
subnet_id_3
subnet_id_4
subnet_id_5
subnet_id_6
```

### Setting up database credentials in AWS Secrets Manager<a name="rds-proxy-secrets-arns"></a>

 For each proxy that you create, you first use the Secrets Manager service to store sets of user name and password credentials\. You create a separate Secrets Manager secret for each database user account that the proxy connects to on the RDS DB instance or Aurora DB cluster\. 

 In Secrets Manager, you create these secrets with values for the `username` and `password` fields\. Doing so allows the proxy to connect to the corresponding database users on whichever RDS DB instances or Aurora DB clusters that you associate with the proxy\. To do this, you can use the setting **Credentials for other database**, **Credentials for RDS database**, or **Other type of secrets**\. Fill in the appropriate values for the **User name** and **Password** fields, and placeholder values for any other required fields\. The proxy ignores other fields such as **Host** and **Port** if they're present in the secret\. Those details are automatically supplied by the proxy\. 

 You can also choose **Other type of secrets**\. In this case, you create the secret with keys named `username` and `password`\. 

 Because the secrets used by your proxy aren't tied to a specific database server, you can reuse a secret across multiple proxies if you use the same credentials across multiple database servers\. For example, you might use the same credentials across a group of development and test servers\. 

 To connect through the proxy as a specific user, make sure that the password associated with a secret matches the database password for that user\. If there's a mismatch, you can update the associated secret in Secrets Manager\. In this case, you can still connect to other accounts where the secret credentials and the database passwords do match\. 

 When you create a proxy through the AWS CLI or RDS API, you specify the Amazon Resource Names \(ARNs\) of the corresponding secrets for all the DB user accounts that the proxy can access\. In the AWS Management Console, you choose the secrets by their descriptive names\. 

 For instructions about creating secrets in Secrets Manager, see the [Creating a secret](https://docs.aws.amazon.com/secretsmanager/latest/userguide/manage_create-basic-secret.html) page in the Secrets Manager documentation\. Use one of the following techniques: 
+  Use [Secrets Manager](https://aws.amazon.com/secrets-manager/) in the console\. 
+  To use the CLI to create a Secrets Manager secret for use with RDS Proxy, use a command such as the following\. 

  ```
  aws secretsmanager create-secret
    --name "secret_name"
    --description "secret_description"
    --region region_name
    --secret-string '{"username":"db_user","password":"db_user_password"}'
  ```

 For example, the following commands create Secrets Manager secrets for two database users, one named `admin` and the other named `app-user`\. 

```
aws secretsmanager create-secret \
  --name admin_secret_name --description "db admin user" \
  --secret-string '{"username":"admin","password":"choose_your_own_password"}'

aws secretsmanager create-secret \
  --name proxy_secret_name --description "application user" \
  --secret-string '{"username":"app-user","password":"choose_your_own_password"}'
```

 To see the secrets owned by your AWS account, use a command such as the following\. 

```
aws secretsmanager list-secrets
```

 When you create a proxy using the CLI, you pass the Amazon resource names \(ARNs\) of one or more secrets to the `--auth` parameter\. The following Linux example shows how to prepare a report with only the name and ARN of each secret owned by your AWS account\. This example uses the `--output table` parameter that is available in AWS CLI version 2\. If you are using AWS CLI version 1, use `--output text` instead\. 

```
aws secretsmanager list-secrets --query '*[].[Name,ARN]' --output table
```

 To verify that you stored the correct credentials and in the right format in a secret, use a command such as the following\. Substitute the short name or the ARN of the secret for *your\_secret\_name*\. 

```
aws secretsmanager get-secret-value --secret-id your_secret_name
```

 The output should include a line displaying a JSON\-encoded value like the following\. 

```
"SecretString": "{\"username\":\"your_username\",\"password\":\"your_password\"}",
```

### Setting up AWS Identity and Access Management \(IAM\) policies<a name="rds-proxy-iam-setup"></a>

 After you create the secrets in Secrets Manager, you create an IAM policy that can access those secrets\. For general information about using IAM with RDS and Aurora, see [Identity and access management in Amazon RDS](UsingWithRDS.IAM.md)\. 

**Tip**  
 The following procedure applies if you use the IAM console\. If you use the AWS Management Console for RDS, RDS can create the IAM policy for you automatically\. In that case, you can skip the following procedure\. 

**To create an IAM policy that accesses your Secrets Manager secrets for use with your proxy**

1.  Sign in to the IAM console\. Follow the **Create role** process, as described in [Creating IAM roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create.html)\. Include the **Add Role to Database** step\. 

1.  For the new role, perform the **Add inline policy** step\. Use the same general procedures as in [Editing IAM policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_manage-edit.html)\. Paste the following JSON into the JSON text box\. Substitute your own account ID\. Substitute your AWS Region for `us-east-2`\. Substitute the Amazon Resource Names \(ARNs\) for the secrets that you created\. For the `kms:Decrypt` action, substitute the ARN of the default AWS KMS customer master key \(CMK\) or your own AWS KMS CMK, depending on which one you used to encrypt the Secrets Manager secrets\. 

   ```
   {
       "Version": "2012-10-17",
       "Statement": [
           {
               "Sid": "VisualEditor0",
               "Effect": "Allow",
               "Action": "secretsmanager:GetSecretValue",
               "Resource": [
                   "arn:aws:secretsmanager:us-east-2:account_id:secret:secret_name_1",
                   "arn:aws:secretsmanager:us-east-2:account_id:secret:secret_name_2"
               ]
           },
           {
               "Sid": "VisualEditor1",
               "Effect": "Allow",
               "Action": "kms:Decrypt",
               "Resource": "arn:aws:kms:us-east-2:account_id:key/key_id",
               "Condition": {
                   "StringEquals": {
                       "kms:ViaService": "secretsmanager.us-east-2.amazonaws.com"
                   }
               }
           }
       ]
   }
   ```

1.  Edit the trust policy for this IAM role\. Paste the following JSON into the JSON text box\. 

   ```
   {
     "Version": "2012-10-17",
     "Statement": [
       {
         "Sid": "",
         "Effect": "Allow",
         "Principal": {
           "Service": "rds.amazonaws.com"
         },
         "Action": "sts:AssumeRole"
       }
     ]
   }
   ```

 The following commands perform the same operation through the AWS CLI\. 

```
PREFIX=choose_an_identifier

aws iam create-role --role-name choose_role_name \
  --assume-role-policy-document '{"Version":"2012-10-17","Statement":[{"Effect":"Allow","Principal":{"Service":["rds.amazonaws.com"]},"Action":"sts:AssumeRole"}]}'

aws iam put-role-policy --role-name same_role_name_as_previous \
  --policy-name $PREFIX-secret-reader-policy --policy-document """
same_json_as_in_previous_example
"""

aws kms create-key --description "$PREFIX-test-key" --policy """
{
  "Id":"$PREFIX-kms-policy",
  "Version":"2012-10-17",
  "Statement":
    [
      {
        "Sid":"Enable IAM User Permissions",
        "Effect":"Allow",
        "Principal":{"AWS":"arn:aws:iam::account_id:root"},
        "Action":"kms:*","Resource":"*"
      },
      {
        "Sid":"Allow access for Key Administrators",
        "Effect":"Allow",
        "Principal":
          {
            "AWS":
              ["$USER_ARN","arn:aws:iam::account_id:role/Admin"]
          },
        "Action":
          [
            "kms:Create*",
            "kms:Describe*",
            "kms:Enable*",
            "kms:List*",
            "kms:Put*",
            "kms:Update*",
            "kms:Revoke*",
            "kms:Disable*",
            "kms:Get*",
            "kms:Delete*",
            "kms:TagResource",
            "kms:UntagResource",
            "kms:ScheduleKeyDeletion",
            "kms:CancelKeyDeletion"
          ],
        "Resource":"*"
      },
      {
        "Sid":"Allow use of the key",
        "Effect":"Allow",
        "Principal":{"AWS":"$ROLE_ARN"},
        "Action":["kms:Decrypt","kms:DescribeKey"],
        "Resource":"*"
      }
    ]
}
"""
```

### Creating an RDS Proxy<a name="rds-proxy-creating"></a>

 To manage connections for a specified set of DB instances, you can create a proxy\. You can associate a proxy with an RDS for MySQL DB instance, PostgreSQL DB instance, or an Aurora DB cluster\. 

#### AWS Management Console<a name="rds-proxy-creating.console"></a>

**To create a proxy**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1.  In the navigation pane, choose **Proxies**\. 

1.  Choose **Create proxy**\. 

1.  Choose all the settings for your proxy\. 

    For **Proxy configuration**, provide information for the following: 
   +  **Proxy identifier**\. Specify a name of your choosing, unique within your AWS account ID and current AWS Region\. 
   +  **Engine compatibility**\. Choose either **MySQL** or **POSTGRESQL**\. 
   +  **Require Transport Layer Security**\. Choose this setting if you want the proxy to enforce TLS/SSL for all client connections\. When you use an encrypted or unencrypted connection to a proxy, the proxy uses the same encryption setting when it makes a connection to the underlying database\. 
   +  **Idle client connection timeout**\. Choose a time period that a client connection can be idle before the proxy can close it\. The default is 1,800 seconds \(30 minutes\)\. A client connection is considered idle when the application doesn't submit a new request within the specified time after the previous request completed\. The underlying database connection stays open and is returned to the connection pool\. Thus, it's available to be reused for new client connections\. 

      Consider lowering the idle client connection timeout if you want the proxy to proactively remove stale connections\. If your workload is spiking, consider raising the idle client connection timeout to save the cost of establishing connections\. 

    For **Target group configuration**, provide information for the following: 
   +  **Database**\. Choose one RDS DB instance or Aurora DB cluster to access through this proxy\. The list only includes DB instances and clusters with compatible database engines, engine versions, and other settings\. If the list is empty, create a new DB instance or cluster that's compatible with RDS Proxy\. To do so, follow the procedure in [Creating an Amazon RDS DB instance](USER_CreateDBInstance.md) \. Then try creating the proxy again\. 
   +  **Connection pool maximum connections**\. Specify a value from 1 through 100\. This setting represents the percentage of the `max_connections` value that RDS Proxy can use for its connections\. If you only intend to use one proxy with this DB instance or cluster, you can set this value to 100\. For details about how RDS Proxy uses this setting, see [Controlling connection limits and timeouts](#rds-proxy-connection-limits)\. 
   +  **Session pinning filters**\. \(Optional\) This is an advanced setting, for troubleshooting performance issues with particular applications\. Currently, the only choice is `EXCLUDE_VARIABLE_SETS`\. Choose a filter only if both of following are true: Your application isn't reusing connections due to certain kinds of SQL statements, and you can verify that reusing connections with those SQL statements doesn't affect application correctness\. For more information, see [Avoiding pinning](#rds-proxy-pinning)\. 
   +  **Connection borrow timeout**\. In some cases, you might expect the proxy to sometimes use all available database connections\. In such cases, you can specify how long the proxy waits for a database connection to become available before returning a timeout error\. You can specify a period up to a maximum of five minutes\. This setting only applies when the proxy has the maximum number of connections open and all connections are already in use\. 

    For **Connectivity**, provide information for the following: 
   +  **Secrets Manager ARNs**\. Choose at least one Secrets Manager secret that contains DB user credentials for the RDS DB instance or Aurora DB cluster that you intend to access with this proxy\. 
   +  **IAM role**\. Choose an IAM role that has permission to access the Secrets Manager secrets that you chose earlier\. You can also choose for the AWS Management Console to create a new IAM role for you and use that\. 
   +  **IAM Authentication**\. Choose whether to require or disallow IAM authentication for connections to your proxy\. The choice of IAM authentication or native database authentication applies to all DB users that access this proxy\. 
   +  **Subnets**\. This field is prepopulated with all the subnets associated with your VPC\. You can remove any subnets that you don't need for this proxy\. You must leave at least two subnets\. 

    Provide additional connectivity configuration: 
   +  **VPC security group**\. Choose an existing VPC security group\. You can also choose for the AWS Management Console to create a new security group for you and use that\. 
**Note**  
 This security group must allow access to the database the proxy connects to\. The same security group is used for ingress from your applications to the proxy, and for egress from the proxy to the database\. For example, suppose that you use the same security group for your database and your proxy\. In this case, make sure that you specify that resources in that security group can communicate with other resources in the same security group\. 

    \(Optional\) Provide advanced configuration: 
   +  **Enable enhanced logging**\. You can enable this setting to troubleshoot proxy compatibility or performance issues\. 

      When this setting is enabled, RDS Proxy includes detailed information about SQL statements in its logs\. This information helps you to debug issues involving SQL behavior or the performance and scalability of the proxy connections\. The debug information includes the text of SQL statements that you submit through the proxy\. Thus, only enable this setting when needed for debugging, and only when you have security measures in place to safeguard any sensitive information that appears in the logs\. 

      To minimize overhead associated with your proxy, RDS Proxy automatically turns this setting off 24 hours after you enable it\. Enable it temporarily to troubleshoot a specific issue\. 

1.  Choose **Create Proxy**\. 

#### AWS CLI<a name="rds-proxy-creating.CLI"></a>

 To create a proxy, use the AWS CLI command [create\-db\-proxy](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-proxy.html)\. The `--engine-family` value is case\-sensitive\. 

**Example**  
For Linux, macOS, or Unix:  

```
aws rds create-db-proxy \
    --db-proxy-name proxy_name \
    --engine-family { MYSQL | POSTGRESQL } \
    --auth ProxyAuthenticationConfig_JSON_string \
    --role-arn iam_role \
    --vpc-subnet-ids space_separated_list \
    [--vpc-security-group-ids space_separated_list] \
    [--require-tls | --no-require-tls] \
    [--idle-client-timeout value] \
    [--debug-logging | --no-debug-logging] \
    [--tags comma_separated_list]
```
For Windows:  

```
aws rds create-db-proxy ^
    --db-proxy-name proxy_name ^
    --engine-family { MYSQL | POSTGRESQL } ^
    --auth ProxyAuthenticationConfig_JSON_string ^
    --role-arn iam_role ^
    --vpc-subnet-ids space_separated_list ^
    [--vpc-security-group-ids space_separated_list] ^
    [--require-tls | --no-require-tls] ^
    [--idle-client-timeout value] ^
    [--debug-logging | --no-debug-logging] ^
    [--tags comma_separated_list]
```

**Tip**  
 If you don't already know the subnet IDs to use for the `--vpc-subnet-ids` parameter, see [Setting up network prerequisites](#rds-proxy-network-prereqs) for examples of how to find the subnet IDs that you can use\. 

 To create the required information and associations for the proxy, you also use the [register\-db\-proxy\-targets](https://docs.aws.amazon.com/cli/latest/reference/rds/register-db-proxy-targets.html) command\. Specify the target group name `default`\. RDS Proxy automatically creates a target group with this name when you create each proxy\. 

```
aws rds register-db-proxy-targets
    --db-proxy-name value
    [--target-group-name target_group_name]
    [--db-instance-identifiers space_separated_list]  # rds db instances, or
    [--db-cluster-identifiers cluster_id]        # rds db cluster (all instances), or
    [--db-cluster-endpoint endpoint_name]          # rds db cluster endpoint (all instances)
```

#### RDS API<a name="rds-proxy-creating.API"></a>

 To create an RDS proxy, call the Amazon RDS API operation [CreateDBProxy](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBProxy.html)\. You pass a parameter with the [AuthConfig](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_AuthConfig.html) data structure\. 

  RDS Proxy automatically creates a target group named `default` when you create each proxy\. You associate an RDS DB instance or Aurora DB cluster with the target group by calling the function [RegisterDBProxyTargets](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RegisterDBProxyTargets.html)\. 

### Viewing an RDS Proxy<a name="rds-proxy-viewing"></a>

 After you create one or more RDS proxies, you can view them all to examine their configuration details and choose which ones to modify, delete, and so on\. 

 Any database applications that use the proxy require the proxy endpoint to use in the connection string\. 

#### AWS Management Console<a name="rds-proxy-viewing.console"></a>

**To view your proxy**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1.  In the upper\-right corner of the AWS Management Console, choose the AWS Region in which you created the RDS Proxy\. 

1.  In the navigation pane, choose **Proxies**\. 

1.  Choose the name of an RDS proxy to display its details\. 

1.  On the details page, the **Target groups** section shows how the proxy is associated with a specific RDS DB instance or Aurora DB cluster\. You can follow the link to the **default** target group page to see more details about the association between the proxy and the database\. This page is where you see settings that you specified when creating the proxy, such as maximum connection percentage, connection borrow timeout, engine compatibility, and session pinning filters\. 

#### CLI<a name="rds-proxy-viewing.cli"></a>

 To view your proxy using the CLI, use the [describe\-db\-proxies](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-proxies.html) command\. By default, it displays all proxies owned by your AWS account\. To see details for a single proxy, specify its name with the `--db-proxy-name` parameter\. 

```
aws rds describe-db-proxies [--db-proxy-name proxy_name]
```

 To view the other information associated with the proxy, use the following commands\. 

```
aws rds describe-db-proxy-target-groups  --db-proxy-name proxy_name

aws rds describe-db-proxy-targets --db-proxy-name proxy_name
```

 Use the following sequence of commands to see more detail about the things that are associated with the proxy: 

1.  To get a list of proxies, run [describe\-db\-proxies](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-proxies.html)\. 

1.  To show connection parameters such as the maximum percentage of connections that the proxy can use, run [describe\-db\-proxy\-target\-groups](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-proxy-target-groups.html) `--db-proxy-name` and use the name of the proxy as the parameter value\. 

1.  To see the details of the RDS DB instance or Aurora DB cluster associated with the returned target group, run [describe\-db\-proxy\-targets](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-proxy-targets.html)\. 

#### RDS API<a name="rds-proxy-viewing.api"></a>

 To view your proxies using the RDS API, use the [DescribeDBProxies](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBProxies.html) operation\. It returns values of the [DBProxy](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DBProxy.html) data type\. 

 To see details of the connection settings for the proxy, use the proxy identifiers from this return value with the [DescribeDBProxyTargetGroups](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBProxyTargetGroups.html) operation\. It returns values of the [DBProxyTargetGroup](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DBProxyTargetGroup.html) data type\. 

 To see the RDS instance or Aurora DB cluster associated with the proxy, use the [DescribeDBProxyTargets](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBProxyTargets.html) operation\. It returns values of the [DBProxyTarget](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DBProxyTarget.html) data type\. 

## Connecting to a database through RDS Proxy<a name="rds-proxy-connecting"></a>

 You connect to an RDS DB instance or Aurora DB cluster through a proxy in generally the same way as you connect directly to the database\. The main difference is that you specify the proxy endpoint instead of the instance or cluster endpoint\. For an Aurora DB cluster, by default all proxy connections have read/write capability and use the writer instance\. If you normally use the reader endpoint for read\-only connections, you can create an additional read\-only endpoint for the proxy and use that endpoint the same way\. For more information, see [Overview of proxy endpoints](#rds-proxy-endpoints-overview)\. 

**Topics**
+ [Connecting to a proxy using native authentication](#rds-proxy-connecting-native)
+ [Connecting to a proxy using IAM authentication](#rds-proxy-connecting-iam)
+ [Considerations for connecting to a proxy with PostgreSQL](#rds-proxy-connecting-postgresql)

### Connecting to a proxy using native authentication<a name="rds-proxy-connecting-native"></a>

 Use the following basic steps to connect to a proxy using native authentication: 

1.  Find the proxy endpoint\. In the AWS Management Console, you can find the endpoint on the details page for the corresponding proxy\. With the AWS CLI, you can use the [describe\-db\-proxies](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-proxies.html) command\. The following example shows how\. 

   ```
   # Add --output text to get output as a simple tab-separated list.
   $ aws rds describe-db-proxies --query '*[*].{DBProxyName:DBProxyName,Endpoint:Endpoint}'
   [
       [
           {
               "Endpoint": "the-proxy.proxy-demo.us-east-1.rds.amazonaws.com",
               "DBProxyName": "the-proxy"
           },
           {
               "Endpoint": "the-proxy-other-secret.proxy-demo.us-east-1.rds.amazonaws.com",
               "DBProxyName": "the-proxy-other-secret"
           },
           {
               "Endpoint": "the-proxy-rds-secret.proxy-demo.us-east-1.rds.amazonaws.com",
               "DBProxyName": "the-proxy-rds-secret"
           },
           {
               "Endpoint": "the-proxy-t3.proxy-demo.us-east-1.rds.amazonaws.com",
               "DBProxyName": "the-proxy-t3"
           }
       ]
   ]
   ```

1.  Specify that endpoint as the host parameter in the connection string for your client application\. For example, specify the proxy endpoint as the value for the `mysql -h` option or `psql -h` option\. 

1.  Supply the same database user name and password as you usually do\. 

### Connecting to a proxy using IAM authentication<a name="rds-proxy-connecting-iam"></a>

 When you use IAM authentication with RDS Proxy, set up your database users to authenticate with regular user names and passwords\. The IAM authentication applies to RDS Proxy retrieving the user name and password credentials from Secrets Manager\. The connection from RDS Proxy to the underlying database doesn't go through IAM\. 

 To connect to RDS Proxy using IAM authentication, follow the same general procedure as for connecting to an RDS DB instance or Aurora cluster using IAM authentication\. For general information about using IAM with RDS and Aurora, see [Security in Amazon RDS](UsingWithRDS.md)\. 

 The major differences in IAM usage for RDS Proxy include the following: 
+  You don't configure each individual database user with an authorization plugin\. The database users still have regular user names and passwords within the database\. You set up Secrets Manager secrets containing these user names and passwords, and authorize RDS Proxy to retrieve the credentials from Secrets Manager\. 
**Important**  
 The IAM authentication applies to the connection between your client program and the proxy\. The proxy then authenticates to the database using the user name and password credentials retrieved from Secrets Manager\. When you use IAM for the connection to a proxy, make sure that the underlying RDS DB instance or Aurora DB cluster doesn't have IAM enabled\. 
+  Instead of the instance, cluster, or reader endpoint, you specify the proxy endpoint\. For details about the proxy endpoint, see [Connecting to your DB instance using IAM authentication](UsingWithRDS.IAMDBAuth.Connecting.md)\. 
+  In the direct DB IAM auth case, you selectively pick database users and configure them to be identified with a special auth plugin\. You can then connect to those users using IAM auth\. 

   In the proxy use case, you need to provide the proxy with Secrets that contain some user's username and password \(native auth\)\. You then connect to the proxy using IAM auth \(by generating an auth token with the proxy endpoint, not the database endpoint\) and using a username which matches one of the usernames for the secrets you previously provided\. 
+  Make sure that you use Transport Layer Security \(TLS\) / Secure Sockets Layer \(SSL\) when connecting to a proxy using IAM authentication\. 

 You can grant a specific user access to the proxy by modifying the IAM policy\. An example follows\. 

```
"Resource": "arn:aws:rds-db:us-east-2:1234567890:dbuser:prx-ABCDEFGHIJKL01234/db_user"
```

### Considerations for connecting to a proxy with PostgreSQL<a name="rds-proxy-connecting-postgresql"></a>

 For PostgreSQL, when a client starts a connection to a PostgreSQL database, it sends a startup message that includes pairs of parameter name and value strings\. For details, see the `StartupMessage` in [PostgreSQL message formats](https://www.postgresql.org/docs/current/protocol-message-formats.html) in the PostgreSQL documentation\. 

When connecting through an RDS proxy, the startup message can include the following currently recognized parameters: 
+  `user` 
+  `database`
+  `replication`

 The startup message can also include the following additional runtime parameters: 
+ `[application\_name](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-APPLICATION-NAME) `
+ `[client\_encoding](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-CLIENT-ENCODING) `
+ `[DateStyle](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-DATESTYLE) `
+ `[TimeZone](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-TIMEZONE) `
+  `[extra\_float\_digits](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-EXTRA-FLOAT-DIGITS) `

 For more information about PostgreSQL messaging, see the [Frontend/Backend protocol](https://www.postgresql.org/docs/current/protocol.html) in the PostgreSQL documentation\.

 For PostgreSQL, if you use JDBC we recommend the following to avoid pinning:
+ Set the JDBC connection parameter `assumeMinServerVersion` to at least `9.0` to avoid pinning\. Doing this prevents the JDBC driver from performing an extra round trip during connection startup when it runs `SET extra_float_digits = 3`\. 
+ Set the JDBC connection parameter `ApplicationName` to `any/your-application-name` to avoid pinning\. Doing this prevents the JDBC driver from performing an extra round trip during connection startup when it runs `SET application_name = "PostgreSQL JDBC Driver"`\. Note the JDBC parameter is `ApplicationName` but the PostgreSQL `StartupMessage` parameter is `application_name`\.
+ Set the JDBC connection parameter `preferQueryMode` to `extendedForPrepared` to avoid pinning\. The `extendedForPrepared` ensures that the extended mode is used only for prepared statements\. 

  The default for the `preferQueryMode` parameter is `extended`, which uses the extended mode for all queries\. The extended mode uses a series of `Prepare`, `Bind`, `Execute`, and `Sync` requests and corresponding responses\. This type of series causes connection pinning in an RDS proxy\. 

For more information, see [Avoiding pinning](#rds-proxy-pinning)\. For more information about connecting using JDBC, see [Connecting to the database](https://jdbc.postgresql.org/documentation/head/connect.html) in the PostgreSQL documentation\.

## Managing an RDS Proxy<a name="rds-proxy-managing"></a>

 Following, you can find an explanation of how to manage RDS proxy operation and configuration\. These procedures help your application make the most efficient use of database connections and achieve maximum connection reuse\. The more that you can take advantage of connection reuse, the more CPU and memory overhead that you can save\. This in turn reduces latency for your application and enables the database to devote more of its resources to processing application requests\. 

**Topics**
+ [Modifying an RDS Proxy](#rds-proxy-modifying-proxy)
+ [Adding a new database user](#rds-proxy-new-db-user)
+ [Changing the password for a database user](#rds-proxy-changing-db-user-password)
+ [Controlling connection limits and timeouts](#rds-proxy-connection-limits)
+ [Managing and monitoring connection pooling](#rds-proxy-connection-pooling-tuning)
+ [Avoiding pinning](#rds-proxy-pinning)
+ [Deleting an RDS Proxy](#rds-proxy-deleting)

### Modifying an RDS Proxy<a name="rds-proxy-modifying-proxy"></a>

 You can change certain settings associated with a proxy after you create the proxy\. You do so by modifying the proxy itself, its associated target group, or both\. Each proxy has an associated target group\. 

#### AWS Management Console<a name="rds-proxy-modifying-proxy.console"></a>

**To modify the settings for a proxy**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1.  In the navigation pane, choose **Proxies**\. 

1.  In the list of proxies, choose the proxy whose settings you want to modify or go to its details page\. 

1.  For **Actions**, choose **Modify**\. 

1.  Enter or choose the properties to modify\. You can do the following:  
   +  Rename the proxy by entering a new identifier\. 
   +  Turn the requirement for Transport layer Security \(TLS\) on or off\. 
   +  Enter a time period for the idle connection timeout\. 
   +  Add or remove Secrets Manager secrets\. These secrets correspond to database user names and passwords\. 
   +  Change the IAM role used to retrieve the secrets from Secrets Manager\. 
   +  Require or disallow IAM authentication for connections to the proxy\. 
   +  Add or remove VPC subnets for the proxy to use\. 
   +  Add or remove VPC security groups for the proxy to use\. 
   +  Enable or disable enhanced logging\. 

1.  Choose **Modify**\. 

 If you didn't find the settings listed that you want to change, use the following procedure to update the target group for the proxy\. The *target group *associated with a proxy controls the settings related to the physical database connections\. Each proxy has one associated target group named `default`, which is created automatically along with the proxy\. 

 You can only modify the target group from the proxy details page, not from the list on the **Proxies** page\. 

**To modify the settings for a proxy target group**

1.  On the **Proxies** page, go to the details page for a proxy\.  

1.  For **Target groups**, choose the `default` link\. Currently, all proxies have a single target group named `default`\. 

1.  On the details page for the **default** target group, choose **Modify**\. 

1.  Choose new settings for the properties that you can modify: 
   +  Choose a different RDS DB instance or Aurora cluster\. 
   +  Adjust what percentage of the maximum available connections the proxy can use\. 
   +  Choose a session pinning filter\. Doing this can help reduce performance issues due to insufficient transaction\-level reuse for connections\. Using this setting requires understanding of application behavior and the circumstances under which RDS Proxy pins a session to a database connection\. 
   +  Adjust the connection borrow timeout interval\. This setting applies when the maximum number of connections is already being used for the proxy\. The setting determines how long the proxy waits for a connection to become available before returning a timeout error\. 

    You can't change certain properties, such as the target group identifier and the database engine\.  

1.  Choose **Modify target group**\. 

#### AWS CLI<a name="rds-proxy-modifying-proxy.cli"></a>

 To modify a proxy using the AWS CLI, use the commands [modify\-db\-proxy](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-proxy.html), [modify\-db\-proxy\-target\-group](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-proxy-target-group.html), [deregister\-db\-proxy\-targets](https://docs.aws.amazon.com/cli/latest/reference/rds/deregister-db-proxy-targets.html), and [register\-db\-proxy\-targets](https://docs.aws.amazon.com/cli/latest/reference/rds/register-db-proxy-targets.html)\. 

 With the `modify-db-proxy` command, you can change properties such as the following: 
+  The set of Secrets Manager secrets used by the proxy\. 
+  Whether TLS is required\. 
+  The idle client timeout\. 
+  Whether to log additional information from SQL statements for debugging\. 
+  The IAM role used to retrieve Secrets Manager secrets\. 
+  The security groups used by the proxy\. 

 The following example shows how to rename an existing proxy\. 

```
aws rds modify-db-proxy --db-proxy-name the-proxy --new-db-proxy-name the_new_name
```

 To modify connection\-related settings or rename the target group, use the `modify-db-proxy-target-group` command\. Currently, all proxies have a single target group named `default`\. When working with this target group, you specify the name of the proxy and `default` for the name of the target group\. 

 The following example shows how to first check the `MaxIdleConnectionsPercent` setting for a proxy and then change it, using the target group\. 

```
aws rds describe-db-proxy-target-groups --db-proxy-name the-proxy

{
    "TargetGroups": [
        {
            "Status": "available",
            "UpdatedDate": "2019-11-30T16:49:30.342Z",
            "ConnectionPoolConfig": {
                "MaxIdleConnectionsPercent": 50,
                "ConnectionBorrowTimeout": 120,
                "MaxConnectionsPercent": 100,
                "SessionPinningFilters": []
            },
            "TargetGroupName": "default",
            "CreatedDate": "2019-11-30T16:49:27.940Z",
            "DBProxyName": "the-proxy",
            "IsDefault": true
        }
    ]
}

aws rds modify-db-proxy-target-group --db-proxy-name the-proxy --target-group-name default --connection-pool-config '
{ "MaxIdleConnectionsPercent": 75 }'

{
    "DBProxyTargetGroup": {
        "Status": "available",
        "UpdatedDate": "2019-12-02T04:09:50.420Z",
        "ConnectionPoolConfig": {
            "MaxIdleConnectionsPercent": 75,
            "ConnectionBorrowTimeout": 120,
            "MaxConnectionsPercent": 100,
            "SessionPinningFilters": []
        },
        "TargetGroupName": "default",
        "CreatedDate": "2019-11-30T16:49:27.940Z",
        "DBProxyName": "the-proxy",
        "IsDefault": true
    }
}
```

 With the `deregister-db-proxy-targets` and `register-db-proxy-targets` commands, you change which RDS DB instance or Aurora DB cluster the proxy is associated with through its target group\. Currently, each proxy can connect to one RDS DB instance or Aurora DB cluster\. The target group tracks the connection details for all the RDS DB instances in a Multi\-AZ configuration, or all the DB instances in an Aurora cluster\. 

 The following example starts with a proxy that is associated with an Aurora MySQL cluster named `cluster-56-2020-02-25-1399`\. The example shows how to change the proxy so that it can connect to a different cluster named `provisioned-cluster`\. 

 When you work with an RDS DB instance, you specify the `--db-instance-identifier` option\. When you work with an Aurora DB cluster, you specify the `--db-cluster-identifier` option instead\. 

 The following example modifies an Aurora MySQL proxy\. An Aurora PostgreSQL proxy has port 5432\. 

```
aws rds describe-db-proxy-targets --db-proxy-name the-proxy

{
    "Targets": [
        {
            "Endpoint": "instance-9814.demo.us-east-1.rds.amazonaws.com",
            "Type": "RDS_INSTANCE",
            "Port": 3306,
            "RdsResourceId": "instance-9814"
        },
        {
            "Endpoint": "instance-8898.demo.us-east-1.rds.amazonaws.com",
            "Type": "RDS_INSTANCE",
            "Port": 3306,
            "RdsResourceId": "instance-8898"
        },
        {
            "Endpoint": "instance-1018.demo.us-east-1.rds.amazonaws.com",
            "Type": "RDS_INSTANCE",
            "Port": 3306,
            "RdsResourceId": "instance-1018"
        },
        {
            "Type": "TRACKED_CLUSTER",
            "Port": 0,
            "RdsResourceId": "cluster-56-2020-02-25-1399"
        },
        {
            "Endpoint": "instance-4330.demo.us-east-1.rds.amazonaws.com",
            "Type": "RDS_INSTANCE",
            "Port": 3306,
            "RdsResourceId": "instance-4330"
        }
    ]
}

aws rds deregister-db-proxy-targets --db-proxy-name the-proxy --db-cluster-identifier cluster-56-2020-02-25-1399

aws rds describe-db-proxy-targets --db-proxy-name the-proxy

{
    "Targets": []
}

aws rds register-db-proxy-targets --db-proxy-name the-proxy --db-cluster-identifier provisioned-cluster

{
    "DBProxyTargets": [
        {
            "Type": "TRACKED_CLUSTER",
            "Port": 0,
            "RdsResourceId": "provisioned-cluster"
        },
        {
            "Endpoint": "gkldje.demo.us-east-1.rds.amazonaws.com",
            "Type": "RDS_INSTANCE",
            "Port": 3306,
            "RdsResourceId": "gkldje"
        },
        {
            "Endpoint": "provisioned-1.demo.us-east-1.rds.amazonaws.com",
            "Type": "RDS_INSTANCE",
            "Port": 3306,
            "RdsResourceId": "provisioned-1"
        }
    ]
}
```

#### RDS API<a name="rds-proxy-modifying-proxy.api"></a>

 To modify a proxy using the RDS API, you use the operations [ModifyDBProxy](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBProxy.html), [ModifyDBProxyTargetGroup](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBProxyTargetGroup.html), [DeregisterDBProxyTargets](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DeregisterDBProxyTargets.html), and [RegisterDBProxyTargets](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RegisterDBProxyTargets.html) operations\. 

 With `ModifyDBProxy`, you can change properties such as the following: 
+  The set of Secrets Manager secrets used by the proxy\. 
+  Whether TLS is required\. 
+  The idle client timeout\. 
+  Whether to log additional information from SQL statements for debugging\. 
+  The IAM role used to retrieve Secrets Manager secrets\. 
+  The security groups used by the proxy\. 

 With `ModifyDBProxyTargetGroup`, you can modify connection\-related settings or rename the target group\. Currently, all proxies have a single target group named `default`\. When working with this target group, you specify the name of the proxy and `default` for the name of the target group\. 

 With `DeregisterDBProxyTargets` and `RegisterDBProxyTargets`, you change which RDS DB instance or Aurora DB cluster the proxy is associated with through its target group\. Currently, each proxy can connect to one RDS DB instance or Aurora DB cluster\. The target group tracks the connection details for all the RDS DB instances in a Multi\-AZ configuration, or all the DB instances in an Aurora cluster\. 

### Adding a new database user<a name="rds-proxy-new-db-user"></a>

 In some cases, you might add a new database user to an RDS DB instance or Aurora cluster that's associated with a proxy\. If so, add or repurpose a Secrets Manager secret to store the credentials for that user\. To do this, choose one of the following options: 
+  Create a new Secrets Manager secret, using the procedure described in [Setting up database credentials in AWS Secrets Manager](#rds-proxy-secrets-arns)\. 
+  Update the IAM role to give RDS Proxy access to the new Secrets Manager secret\. To do so, update the resources section of the IAM role policy\. 
+  If the new user takes the place of an existing one, update the credentials stored in the proxy's Secrets Manager secret for the existing user\. 

### Changing the password for a database user<a name="rds-proxy-changing-db-user-password"></a>

 In some cases, you might change the password for a database user in an RDS DB instance or Aurora cluster that's associated with a proxy\. If so, update the corresponding Secrets Manager secret with the new password\. 

### Controlling connection limits and timeouts<a name="rds-proxy-connection-limits"></a>

 RDS Proxy uses the `max_connections` setting for your RDS DB instance or Aurora DB cluster\. This setting represents the overall upper limit on the connections that the proxy can open at any one time\. In Aurora clusters and RDS Multi\-AZ configurations, the `max_connections` value that the proxy uses is the one for the Aurora primary instance or the RDS writer instance\. 

 To set this value for your RDS DB instance or Aurora DB cluster, follow the procedures in [Working with DB parameter groups](USER_WorkingWithParamGroups.md)\. These procedures demonstrate how to associate a parameter group with your database and edit the `max_connections` value in the parameter group\. 

 The proxy setting for maximum connections represents a percentage of the `max_connections` value for the database that's associated with the proxy\. If you have multiple applications all using the same database, you can effectively divide their connection quotas by using a proxy for each application with a specific percentage of `max_connections`\. If you do so, ensure that the percentages add up to 100 or less for all proxies associated with the same database\. 

 RDS Proxy periodically disconnects idle connections and returns them to the connection pool\. You can adjust this timeout interval\. Doing so helps your applications to deal with stale resources, especially if the application mistakenly leaves a connection open while holding important database resources\. 

### Managing and monitoring connection pooling<a name="rds-proxy-connection-pooling-tuning"></a>

 As described in [Connection pooling](#rds-proxy-connection-pooling), connection pooling is a crucial RDS Proxy feature\. Following, you can learn how to make the most efficient use of connection pooling and transaction\-level connection reuse \(multiplexing\)\. 

 Because the connection pool is managed by RDS Proxy, you can monitor it and adjust connection limits and timeout intervals without changing your application code\. 

 For each proxy, you can specify an upper limit on the number of connections used by the connection pool\. You specify the limit as a percentage\. This percentage applies to the maximum connections configured in the database\. The exact number varies depending on the DB instance size and configuration settings\. 

 For example, suppose that you configured RDS Proxy to use 75 percent of the maximum connections for the database\. For MySQL, the maximum value is defined by the `max_connections` configuration parameter\. In this case, the other 25 percent of maximum connections remain available to assign to other proxies or for connections that don't go through a proxy\. In some cases, the proxy might keep less than 75 percent of the maximum connections open at a particular time\. Those cases might include situations where the database doesn't have many simultaneous connections, or some connections stay idle for long periods\. 

 The overall number of connections available for the connection pool changes as you update the `max_connections` configuration setting that applies to an RDS DB instance or an Aurora cluster\. 

 The proxy doesn't reserve all of these connections in advance\. Thus, you can specify a relatively large percentage, and those connections are only opened when the proxy becomes busy enough to need them\. 

 You can choose how long to wait for a connection to become available for use by your application\. This setting is represented by the **Connection borrow timeout** option when you create a proxy\. This setting specifies how long to wait for a connection to become available in the connection pool before returning a timeout error\. It applies when the number of connections is at the maximum, and so no connections are available in the connection pool\. It also applies if no writer instance is available because a failover operation is in process\. Using this setting, you can set the best wait period for your application without having to change the query timeout in your application code\. 

### Avoiding pinning<a name="rds-proxy-pinning"></a>

 Multiplexing is more efficient when database requests don't rely on state information from previous requests\. In that case, RDS Proxy can reuse a connection at the conclusion of each transaction\. Examples of such state information include most variables and configuration parameters that you can change through `SET` or `SELECT` statements\. SQL transactions on a client connection can multiplex between underlying database connections by default\. 

 Your connections to the proxy can enter a state known as *pinning*\. When a connection is pinned, each later transaction uses the same underlying database connection until the session ends\. Other client connections also can't reuse that database connection until the session ends\. The session ends when the client connection is dropped\. 

 RDS Proxy automatically pins a client connection to a specific DB connection when it detects a session state change that isn't appropriate for other sessions\. Pinning reduces the effectiveness of connection reuse\. If all or almost all of your connections experience pinning, consider modifying your application code or workload to reduce the conditions that cause the pinning\. 

 For example, if your application changes a session variable or configuration parameter, later statements can rely on the new variable or parameter to be in effect\. Thus, when RDS Proxy processes requests to change session variables or configuration settings, it pins that session to the DB connection\. That way, the session state remains in effect for all later transactions in the same session\. 

 This rule doesn't apply to all parameters you can set\. RDS Proxy tracks changes to the character set, collation, time zone, autocommit, current database, SQL mode, and `session_track_schema` settings\. Thus RDS Proxy doesn't pin the session when you modify these\. In this case, RDS Proxy only reuses the connection for other sessions that have the same values for those settings\. 

 Performance tuning for RDS Proxy involves trying to maximize transaction\-level connection reuse \(multiplexing\) by minimizing pinning\. You can do so by doing the following: 
+  Avoid unnecessary database requests that might cause pinning\. 
+  Set variables and configuration settings consistently across all connections\. That way, later sessions are more likely to reuse connections that have those particular settings\. 

   However, for PostgreSQL setting a variable leads to session pinning\. 
+  Apply a session pinning filter to the proxy\. You can exempt certain kinds of operations from pinning the session if you know that doing so doesn't affect the correct operation of your application\. 
+  See how frequently pinning occurs by monitoring the CloudWatch metric `DatabaseConnectionsCurrentlySessionPinned`\. For information about this and other CloudWatch metrics, see [Monitoring RDS Proxy using Amazon CloudWatchMonitoring RDS Proxy](#rds-proxy.monitoring)\. 
+  If you use `SET` statements to perform identical initialization for each client connection, you can do so while preserving transaction\-level multiplexing\. In this case, you move the statements that set up the initial session state into the initialization query used by a proxy\. This property is a string containing one or more SQL statements, separated by semicolons\. 

   For example, you can define an initialization query for a proxy that sets certain configuration parameters\. Then, RDS Proxy applies those settings whenever it sets up a new connection for that proxy\. You can remove the corresponding `SET` statements from your application code, so that they don't interfere with transaction\-level multiplexing\. 
**Important**  
 For proxies associated with MySQL databases, don't set the configuration parameter `sql_auto_is_null` to `true` or a nonzero value in the initialization query\. Doing so might cause incorrect application behavior\. 

 The proxy pins the session to the current connection in the following situations where multiplexing might cause unexpected behavior: 
+  Any statement with a text size greater than 16 KB causes the proxy to pin the session\. 
+  Prepared statements cause the proxy to pin the session\. This rule applies whether the prepared statement uses SQL text or the binary protocol\. 
+  Explicit MySQL statements `LOCK TABLE`, `LOCK TABLES`, or `FLUSH TABLES WITH READ LOCK` cause the proxy to pin the session\. 
+  Setting a user variable or a system variable \(with some exceptions\) causes the proxy to pin the session\. If this situation reduces your connection reuse too much, you can choose for `SET` operations not to cause pinning\. For information about how to do so by setting the `SessionPinningFilters` property, see [Creating an RDS Proxy](#rds-proxy-creating)\. 
+  Creating a temporary table causes the proxy to pin the session\. That way, the contents of the temporary table are preserved throughout the session regardless of transaction boundaries\. 
+  Calling the MySQL functions `ROW_COUNT`, `FOUND_ROWS`, and `LAST_INSERT_ID` sometimes causes pinning\.  

   The exact circumstances where these functions cause pinning might differ between Aurora MySQL versions that are compatible with MySQL 5\.6 and MySQL 5\.7\. 

 Calling MySQL stored procedures and stored functions doesn't cause pinning\. RDS Proxy doesn't detect any session state changes resulting from such calls\. Therefore, make sure that your application doesn't change session state inside stored routines and rely on that session state to persist across transactions\. For example, if a stored procedure creates a temporary table that is intended to persist across transactions, that application currently isn't compatible with RDS Proxy\. 

 For PostgreSQL, the following interactions cause pinning: 
+  Using SET commands 
+  Using the extended query protocol such as by using JDBC default settings 
+  Creating temporary sequences, tables, or views 
+  Declaring cursors 
+  Discarding the session state 
+  Listening on a notification channel 
+  Loading a library module such as `auto_explain` 
+  Manipulating sequences using functions such as `nextval` and `setval` 
+  Interacting with locks using functions such as `pg_advisory_lock` and `pg_try_advisory_lock`
+  Using prepared statements, setting parameters, or resetting a parameter to its default 

 If you have expert knowledge about your application behavior, you can skip the pinning behavior for certain application statements\. To do so, choose the **Session pinning filters** option when creating the proxy\. Currently, you can opt out of session pinning for setting session variables and configuration settings\. 

 For metrics about how often pinning occurs for a proxy, see [Monitoring RDS Proxy using Amazon CloudWatchMonitoring RDS Proxy](#rds-proxy.monitoring)\. 

### Deleting an RDS Proxy<a name="rds-proxy-deleting"></a>

 You can delete a proxy if you no longer need it\. You might delete a proxy because the application that was using it is no longer relevant\. Or you might delete a proxy if you take the DB instance or cluster associated with it out of service\. 

#### AWS Management Console<a name="rds-proxy-deleting.console"></a>

**To delete a proxy**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1.  In the navigation pane, choose **Proxies**\. 

1.  Choose the proxy to delete from the list\. 

1.  Choose **Delete Proxy**\. 

#### AWS CLI<a name="rds-proxy-deleting.CLI"></a>

 To delete a DB proxy, use the AWS CLI command [delete\-db\-proxy](https://docs.aws.amazon.com/cli/latest/reference/rds/delete-db-proxy.html)\. To remove related associations, also use the [deregister\-db\-proxy\-targets](https://docs.aws.amazon.com/cli/latest/reference/rds/deregister-db-proxy-targets.html) command\. 

```
aws rds delete-db-proxy --name proxy_name
```

```
aws rds deregister-db-proxy-targets
    --db-proxy-name proxy_name
    [--target-group-name target_group_name]
    [--target-ids comma_separated_list]       # or
    [--db-instance-identifiers instance_id]       # or
    [--db-cluster-identifiers cluster_id]
```

#### RDS API<a name="rds-proxy-deleting.API"></a>

 To delete a DB proxy, call the Amazon RDS API function [DeleteDBProxy](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DeleteDBProxy.html)\. To delete related items and associations, you also call the functions [DeleteDBProxyTargetGroup](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DeleteDBProxyTargetGroup.html) and [DeregisterDBProxyTargets](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DeregisterDBProxyTargets.html)\. 

## Monitoring RDS Proxy using Amazon CloudWatch<a name="rds-proxy.monitoring"></a>

 You can monitor RDS Proxy by using Amazon CloudWatch\. CloudWatch collects and processes raw data from the proxies into readable, near\-real\-time metrics\. To find these metrics in the CloudWatch console, choose **Metrics**, then choose **RDS**, and choose **Per\-Proxy Metrics**\. For more information, see [Using Amazon CloudWatch metrics](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/working_with_metrics.html) in the Amazon CloudWatch User Guide\. 

**Note**  
 RDS publishes these metrics for each underlying Amazon EC2 instance associated with a proxy\. A single proxy might be served by more than one EC2 instance\. Use CloudWatch statistics to aggregate the values for a proxy across all the associated instances\.   
 Some of these metrics might not be visible until after the first successful connection by a proxy\. 

 In the RDS Proxy logs, each entry is prefixed with the name of the associated proxy endpoint\. This name can be the name you specified for a user\-defined endpoint, or the special name `default` for read/write requests using the default endpoint of a proxy\. 

 All RDS Proxy metrics are in the group `proxy`\. 

 Each proxy endpoint has its own CloudWatch metrics\. You can monitor the usage of each proxy endpoint independently\. For more information about proxy endpoints, see [Endpoints for Amazon RDS Proxy](#rds-proxy-endpoints)\. 

 You can aggregate the values for each metric using one of the following dimension sets\. For example, by using the `ProxyName` dimension set, you can analyze all the traffic for a particular proxy\. By using the other dimension sets, you can split the metrics in different ways\. You can split the metrics based on the different endpoints or target databases of each proxy, or the read/write and read\-only traffic to each database\. 
+   Dimension set 1: `ProxyName` 
+   Dimension set 2: `ProxyName`, `EndpointName` 
+   Dimension set 3: `ProxyName`, `TargetGroup`, `Target` 
+   Dimension set 4: `ProxyName`, `TargetGroup`, `TargetRole` 


|  Metric  |  Description  |  Valid period  |  CloudWatch dimension set  | 
| --- | --- | --- | --- | 
|   `AvailabilityPercentage`   |   The percentage of time for which the target group was available in the role indicated by the dimension\. This metric is reported every minute\. The most useful statistic for this metric is `Average`\.   |  1 minute  |  [Dimension set 4](#proxy-dimension-set-4)  | 
|  ClientConnections  |   The current number of client connections\. This metric is reported every minute\. The most useful statistic for this metric is `Sum`\.   |   1 minute   |  [Dimension set 1](#proxy-dimension-set-1), [Dimension set 2](#proxy-dimension-set-2)  | 
|  ClientConnectionsClosed  |   The number of client connections closed\. The most useful statistic for this metric is `Sum`\.   |   1 minute and above   |  [Dimension set 1](#proxy-dimension-set-1), [Dimension set 2](#proxy-dimension-set-2)  | 
|   `ClientConnectionsNoTLS`   |  The current number of client connections without Transport Layer Security \(TLS\)\. This metric is reported every minute\. The most useful statistic for this metric is Sum\.  |  1 minute and above  |  [Dimension set 1](#proxy-dimension-set-1), [Dimension set 2](#proxy-dimension-set-2)  | 
|   `ClientConnectionsReceived`   |   The number of client connection requests received\. The most useful statistic for this metric is `Sum`\.   |   1 minute and above   |  [Dimension set 1](#proxy-dimension-set-1), [Dimension set 2](#proxy-dimension-set-2)  | 
|  ClientConnectionsSetupFailedAuth  |   The number of client connection attempts that failed due to misconfigured authentication or TLS\. The most useful statistic for this metric is `Sum`\.   |   1 minute and above   |  [Dimension set 1](#proxy-dimension-set-1), [Dimension set 2](#proxy-dimension-set-2)  | 
|  ClientConnectionsSetupSucceeded  |   The number of client connections successfully established with any authentication mechanism with or without TLS\. The most useful statistic for this metric is `Sum`\.   |   1 minute and above   |  [Dimension set 1](#proxy-dimension-set-1), [Dimension set 2](#proxy-dimension-set-2)  | 
|  ClientConnectionsTLS  |  The current number of client connections with TLS\. This metric is reported every minute\. The most useful statistic for this metric is Sum\.  |  1 minute and above  |  [Dimension set 1](#proxy-dimension-set-1), [Dimension set 2](#proxy-dimension-set-2)  | 
|  DatabaseConnectionRequests  |   The number of requests to create a database connection\. The most useful statistic for this metric is `Sum`\.   |   1 minute and above   |  [Dimension set 1](#proxy-dimension-set-1), [Dimension set 3](#proxy-dimension-set-3), [Dimension set 4](#proxy-dimension-set-4)  | 
|   `DatabaseConnectionRequestsWithTLS`   |  The number of requests to create a database connection with TLS\. The most useful statistic for this metric is Sum\.  |  1 minute and above  |  [Dimension set 1](#proxy-dimension-set-1), [Dimension set 3](#proxy-dimension-set-3), [Dimension set 4](#proxy-dimension-set-4)  | 
|  DatabaseConnections  |   The current number of database connections\. This metric is reported every minute\. The most useful statistic for this metric is `Sum`\.   |   1 minute   |  [Dimension set 1](#proxy-dimension-set-1), [Dimension set 3](#proxy-dimension-set-3), [Dimension set 4](#proxy-dimension-set-4)  | 
|   `DatabaseConnectionsBorrowLatency`   |  The time in microseconds that it takes for the proxy being monitored to get a database connection\. The most useful statistic for this metric is Average\.  |  1 minute and above  |  [Dimension set 1](#proxy-dimension-set-1), [Dimension set 2](#proxy-dimension-set-2)  | 
|  DatabaseConnectionsCurrentlyBorrowed  |   The current number of database connections in the borrow state\. This metric is reported every minute\. The most useful statistic for this metric is `Sum`\.   |   1 minute   |  [Dimension set 1](#proxy-dimension-set-1), [Dimension set 3](#proxy-dimension-set-3), [Dimension set 4](#proxy-dimension-set-4)  | 
|  DatabaseConnectionsCurrentlyInTransaction  |   The current number of database connections in a transaction\. This metric is reported every minute\. The most useful statistic for this metric is `Sum`\.   |   1 minute   |  [Dimension set 1](#proxy-dimension-set-1), [Dimension set 3](#proxy-dimension-set-3), [Dimension set 4](#proxy-dimension-set-4)  | 
|  DatabaseConnectionsCurrentlySessionPinned  |   The current number of database connections currently pinned because of operations in client requests that change session state\. This metric is reported every minute\. The most useful statistic for this metric is `Sum`\.   |   1 minute   |  [Dimension set 1](#proxy-dimension-set-1), [Dimension set 3](#proxy-dimension-set-3), [Dimension set 4](#proxy-dimension-set-4)  | 
|  DatabaseConnectionsSetupFailed  |   The number of database connection requests that failed\. The most useful statistic for this metric is `Sum`\.   |   1 minute and above   |  [Dimension set 1](#proxy-dimension-set-1), [Dimension set 3](#proxy-dimension-set-3), [Dimension set 4](#proxy-dimension-set-4)  | 
|  DatabaseConnectionsSetupSucceeded  |   The number of database connections successfully established with or without TLS\. The most useful statistic for this metric is `Sum`\.   |   1 minute and above   |  [Dimension set 1](#proxy-dimension-set-1), [Dimension set 3](#proxy-dimension-set-3), [Dimension set 4](#proxy-dimension-set-4)  | 
|   `DatabaseConnectionsWithTLS`   |  The current number of database connections with TLS\. This metric is reported every minute\. The most useful statistic for this metric is Sum\.  |  1 minute  |  [Dimension set 1](#proxy-dimension-set-1), [Dimension set 3](#proxy-dimension-set-3), [Dimension set 4](#proxy-dimension-set-4)  | 
|  MaxDatabaseConnectionsAllowed  |   The maximum number of database connections allowed\. This metric is reported every minute\. The most useful statistic for this metric is `Sum`\.   |   1 minute   |  [Dimension set 1](#proxy-dimension-set-1), [Dimension set 3](#proxy-dimension-set-3), [Dimension set 4](#proxy-dimension-set-4)  | 
|   `QueryDatabaseResponseLatency`   |  The time in microseconds that the database took to respond to the query\. The most useful statistic for this metric is Average\.  |  1 minute and above  |  [Dimension set 1](#proxy-dimension-set-1), [Dimension set 2](#proxy-dimension-set-2), [Dimension set 3](#proxy-dimension-set-3), [Dimension set 4](#proxy-dimension-set-4)  | 
|  QueryRequests  |   The number of queries received\. A query including multiple statements is counted as one query\. The most useful statistic for this metric is `Sum`\.   |   1 minute and above   |  [Dimension set 1](#proxy-dimension-set-1), [Dimension set 2](#proxy-dimension-set-2)  | 
|  QueryRequestsNoTLS  |  The number of queries received from non\-TLS connections\. A query including multiple statements is counted as one query\. The most useful statistic for this metric is Sum\.  |  1 minute and above  |  [Dimension set 1](#proxy-dimension-set-1), [Dimension set 2](#proxy-dimension-set-2)  | 
|   `QueryRequestsTLS`   |  The number of queries received from TLS connections\. A query including multiple statements is counted as one query\. The most useful statistic for this metric is Sum\.  |  1 minute and above  |  [Dimension set 1](#proxy-dimension-set-1), [Dimension set 2](#proxy-dimension-set-2)  | 
|  QueryResponseLatency  |  The time in microseconds between getting a query request and the proxy responding to it\. The most useful statistic for this metric is Average\.  |  1 minute and above  |  [Dimension set 1](#proxy-dimension-set-1), [Dimension set 2](#proxy-dimension-set-2)  | 

## Endpoints for Amazon RDS Proxy<a name="rds-proxy-endpoints"></a>

 Following, you can learn about endpoints for RDS Proxy and how to use them\. By using endpoints, you can take advantage of the following capabilities: 
+  You can use multiple endpoints with a proxy to monitor and troubleshoot connections from different applications independently\. 
+  You can use reader endpoints with Aurora DB clusters to improve read scalability and high availability for your query\-intensive applications\. 
+  You can use a cross\-VPC endpoint to allow access to databases in one VPC from resources such as Amazon EC2 instances in a different VPC\. 

**Topics**
+ [Overview of proxy endpoints](#rds-proxy-endpoints-overview)
+ [Reader endpoints](#rds-proxy-endpoints-reader-stub)
+ [Accessing Aurora and RDS databases across VPCs](#rds-proxy-cross-vpc)
+ [Creating a proxy endpoint](#rds-proxy-endpoints.CreatingEndpoint)
+ [Viewing proxy endpoints](#rds-proxy-endpoints.DescribingEndpoint)
+ [Modifying a proxy endpoint](#rds-proxy-endpoints.ModifyingEndpoint)
+ [Deleting a proxy endpoint](#rds-proxy-endpoints.DeletingEndpoint)
+ [Limits for proxy endpoints](#rds-proxy-endpoints-limits)

### Overview of proxy endpoints<a name="rds-proxy-endpoints-overview"></a>

 Working with RDS Proxy endpoints involves the same kinds of procedures as with Aurora DB cluster and reader endpoints and RDS instance endpoints\. If you aren't familiar with RDS endpoints, find more information in [Connecting to a DB instance running the MySQL database engine](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_ConnectToInstance.html) and [Connecting to a DB instance running the PostgreSQL database engine](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_ConnectToPostgreSQLInstance.html)\. 

 By default, the endpoint that you connect to when you use RDS Proxy with an Aurora cluster has read/write capability\. As a consequence, this endpoint sends all requests to the writer instance of the cluster, and all of those connections count against the `max_connections` value for the writer instance\. If your proxy is associated with an Aurora DB cluster, you can create additional read/write or read\-only endpoints for that proxy\. 

 You can use a read\-only endpoint with your proxy for read\-only queries, the same way that you use the reader endpoint for an Aurora provisioned cluster\. Doing so helps you to take advantage of the read scalability of an Aurora cluster with one or more reader DB instances\. You can run more simultaneous queries and make more simultaneous connections by using a read\-only endpoint and adding more reader DB instances to your Aurora cluster as needed\. 

 For a proxy endpoint that you create, you can also associate the endpoint with a different virtual private cloud \(VPC\) than the proxy itself uses\. By doing so, you can connect to the proxy from a different VPC, for example a VPC used by a different application within your organization\. Both VPCs must be owned by the same AWS account\. 

 For information about limits associated with proxy endpoints, see [Limits for proxy endpoints](#rds-proxy-endpoints-limits)\. 

 In the RDS Proxy logs, each entry is prefixed with the name of the associated proxy endpoint\. This name can be the name you specified for a user\-defined endpoint, or the special name `default` for read/write requests using the default endpoint of a proxy\. 

 Each proxy endpoint has its own set of CloudWatch metrics\. You can monitor the metrics for all endpoints of a proxy\. You can also monitor metrics for a specific endpoint, or for all the read/write or read\-only endpoints of a proxy\. For more information, see [Monitoring RDS Proxy using Amazon CloudWatchMonitoring RDS Proxy](#rds-proxy.monitoring)\. 

 A proxy endpoint uses the same authentication mechanism as its associated proxy\. RDS Proxy automatically sets up permissions and authorizations for the user\-defined endpoint, consistent with the properties of the associated proxy\. 

### Reader endpoints<a name="rds-proxy-endpoints-reader-stub"></a>

 With RDS Proxy, you can create and use reader endpoints\. However, these endpoints only work for proxies associated with Aurora DB clusters\. You might see references to reader endpoints in the AWS Management Console\. If you use the RDS CLI or API, you might see the `TargetRole` attribute with a value of `READ_ONLY`\. You can take advantage of these features by changing the target of a proxy from an RDS DB instance to an Aurora DB cluster\. To learn about reader endpoints, see [Managing connections with Amazon RDS Proxy](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/rds-proxy-.html) in the *Aurora User Guide\.* 

### Accessing Aurora and RDS databases across VPCs<a name="rds-proxy-cross-vpc"></a>

 By default, the components of your RDS and Aurora technology stack are all in the same Amazon VPC\. For example, suppose that an application running on an Amazon EC2 instance connects to an Amazon RDS DB instance or an Aurora DB cluster\. In this case, the application server and database must both be within the same VPC\. 

 With RDS Proxy, you can set up access to an Aurora cluster or RDS instance in one VPC from resources such as EC2 instances in another VPC\. For example, your organization might have multiple applications that access the same database resources\. Each application might be in its own VPC\. To use cross\-VPC capability with RDS Proxy, all the VPCs must be owned by the same AWS account\. 

 To enable cross\-VPC access, you create a new endpoint for the proxy\. If you aren't familiar with creating proxy endpoints, see [Endpoints for Amazon RDS Proxy](#rds-proxy-endpoints) for details\. The proxy itself resides in the same VPC as the Aurora DB cluster or RDS instance\. However, the cross\-VPC endpoint resides in the other VPC, along with the other resources such as the EC2 instances\. The cross\-VPC endpoint is associated with subnets and security groups from the same VPC as the EC2 and other resources\. These associations let you connect to the endpoint from the applications that otherwise can't access the database due to the VPC restrictions\. 

 The following steps explain how to create and access a cross\-VPC endpoint through RDS Proxy: 

1.  Create two VPCs, or choose two VPCs that you already use for Aurora and RDS work\. Each VPC should have its own associated network resources such as an Internet gateway, route tables, subnets, and security groups\. If you only have one VPC, you can consult [Getting started with Amazon RDS](CHAP_GettingStarted.md) for the steps to set up another VPC to use RDS successfully\. You can also examine your existing VPC in the Amazon EC2 console to see what kinds of resources to connect together\. 

1.  Create a DB proxy associated with the Aurora DB cluster or RDS instance that you want to connect to\. Follow the procedure in [Creating an RDS Proxy](#rds-proxy-creating)\. 

1.  On the **Details** page for your proxy in the RDS console, under the **Proxy endpoints** section, choose **Create endpoint**\. Follow the procedure in [Creating a proxy endpoint](#rds-proxy-endpoints.CreatingEndpoint)\. 

1.  Choose whether to make the cross\-VPC endpoint read/write or read\-only\. 

1.  Instead of accepting the default of the same VPC as the Aurora DB cluster or RDS instance, choose a different VPC\. This VPC must be in the same AWS Region as the VPC where the proxy resides\. 

1.  Now instead of accepting the defaults for subnets and security groups from the same VPC as the Aurora DB cluster or RDS instance, make new selections\. Make these based on the subnets and security groups from the VPC that you chose\. 

1.  You don't need to change any of the settings for the Secrets Manager secrets\. The same credentials work for all endpoints for your proxy, regardless of which VPC each endpoint is in\. 

1.  Wait for the new endpoint to reach the **Available** state\. 

1.  Make a note of the full endpoint name\. This is the value ending in `Region_name.rds.amazonaws.com` that you supply as part of the connection string for your database application\. 

1.  Access the new endpoint from a resource in the same VPC as the endpoint\. A simple way to test this process is to create a new EC2 instance in this VPC\. Then you can log into the EC2 instance and run the `mysql` or `psql` commands to connect by using the endpoint value in your connection string\. 

### Creating a proxy endpoint<a name="rds-proxy-endpoints.CreatingEndpoint"></a>



#### Console<a name="rds-proxy-endpoints.CreatingEndpoint.CON"></a>

**To create a proxy endpoint**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1.  In the navigation pane, choose **Proxies**\. 

1.  Click the name of the proxy that you want to create a new endpoint for\. 

    The details page for that proxy appears\. 

1.  In the **Proxy endpoints** section, choose **Create proxy endpoint**\. 

    The **Create proxy endpoint** window appears\. 

1.  For **Proxy endpoint name**, enter a descriptive name of your choice\. 

1.  For **Target role**, choose whether to make the endpoint read/write or read\-only\. 

    Connections that use a read/write endpoint can perform any kind of operation: data definition language \(DDL\) statements, data manipulation language \(DML\) statements, and queries\. These endpoints always connect to the primary instance of the Aurora cluster\. You can use read/write endpoints for general database operations when you only use a single endpoint in your application\. You can also use read/write endpoints for administrative operations, online transaction processing \(OLTP\) applications, and extract\-transform\-load \(ETL\) jobs\. 

    Connections that use a read\-only endpoint can only perform queries\. When there are multiple reader instances in the Aurora cluster, RDS Proxy can use a different reader instance for each connection to the endpoint\. That way, a query\-intensive application can take advantage of Aurora's clustering capability\. You can add more query capacity to the cluster by adding more reader DB instances\. These read\-only connections don't impose any overhead on the primary instance of the cluster\. That way, your reporting and analysis queries don't slow down the write operations of your OLTP applications\. 

1.  For **Virtual Private Cloud \(VPC\)**, choose the default if you plan to access the endpoint from the same EC2 instances or other resources where you normally access the proxy or its associated database\. If you want to set up cross\-VPC access for this proxy, choose a VPC other than the default\. For more information about cross\-VPC access, see [Accessing Aurora and RDS databases across VPCs](#rds-proxy-cross-vpc)\. 

1.  For **Subnets**, RDS Proxy fills in the same subnets as the associated proxy by default\. If you want to restrict access to the endpoint so that only a portion of the address range of the VPC can connect to it, remove one or more subnets from the set of choices\. 

1.  For **VPC security group**, you can choose an existing security group or create a new one\. RDS Proxy fills in the same security group or groups as the associated proxy by default\. If the inbound and outbound rules for the proxy are appropriate for this endpoint, you can leave the default choice\. 

    If you choose to create a new security group, specify a name for the security group on this page\. Then edit the security group settings from the EC2 console afterward\. 

1.  Choose **Create proxy endpoint**\. 

#### AWS CLI<a name="rds-proxy-endpoints.CreatingEndpoint.CLI"></a>

 To create a proxy endpoint, use the AWS CLI [create\-db\-proxy\-endpoint](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-proxy-endpoint.html) command\. 

 Include the following required parameters: 
+  `--db-proxy-name value` 
+  `--db-proxy-endpoint-name value` 
+  `--vpc-subnet-ids list_of_ids`\. Separate the subnet IDs with spaces\. You don't specify the ID of the VPC itself\. 

 You can also include the following optional parameters: 
+  `--target-role { READ_WRITE | READ_ONLY }`\. This parameter defaults to `READ_WRITE`\. The `READ_ONLY` value only has an effect on Aurora provisioned clusters that contain one or more reader DB instances\. When the proxy is associated with an RDS instance or with an Aurora cluster that only contains a writer DB instance, you can't specify `READ_ONLY`\. 
+  `--vpc-security-group-ids value`\. Separate the security group IDs with spaces\. If you omit this parameter, RDS Proxy uses the default security group for the VPC\. RDS Proxy determines the VPC based on the subnet IDs that you specify for the `--vpc-subnet-ids` parameter\. 

**Example**  
 The following example creates a proxy endpoint named `my-endpoint`\.   
For Linux, macOS, or Unix:  

```
aws rds create-db-proxy-endpoint \
  --db-proxy-name my-proxy \
  --db-proxy-endpoint-name my-endpoint \
  --vpc-subnet-ids subnet_id subnet_id subnet_id ... \
  --target-role READ_ONLY \
  --vpc-security-group-ids security_group_id ]
```
For Windows:  

```
aws rds create-db-proxy-endpoint ^
  --db-proxy-name my-proxy ^
  --db-proxy-endpoint-name my-endpoint ^
  --vpc-subnet-ids subnet_id_1 subnet_id_2 subnet_id_3 ... ^
  --target-role READ_ONLY ^
  --vpc-security-group-ids security_group_id
```

#### RDS API<a name="rds-proxy-endpoints.CreatingEndpoint.API"></a>

 To create a proxy endpoint, use the RDS API [CreateProxyEndpoint](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBClusterParameterGroup.html) action\. 

### Viewing proxy endpoints<a name="rds-proxy-endpoints.DescribingEndpoint"></a>



#### Console<a name="rds-proxy-endpoints.DescribingEndpoint.CON"></a>

**To view the details for a proxy endpoint**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1.  In the navigation pane, choose **Proxies**\. 

1.  In the list, choose the proxy whose endpoint you want to view\. Click the proxy name to view its details page\. 

1.  In the **Proxy endpoints** section, choose the endpoint that you want to view\. Click its name to view the details page\. 

1.  Examine the parameters whose values you're interested in\. You can check properties such as the following: 
   +  Whether the endpoint is read/write or read\-only\. 
   +  The endpoint address that you use in a database connection string\. 
   +  The VPC, subnets, and security groups associated with the endpoint\. 

#### AWS CLI<a name="rds-proxy-endpoints.DescribingEndpoint.CLI"></a>

 To view one or more DB proxy endpoints, use the AWS CLI [describe\-db\-proxy\-endpoints](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-proxy-endpoints.html) command\. 

 You can include the following optional parameters: 
+  `--db-proxy-endpoint-name` 
+  `--db-proxy-name` 

 The following example describes the `my-endpoint` proxy endpoint\. 

**Example**  
For Linux, macOS, or Unix:  

```
aws rds describe-db-proxy-endpoints \
  --db-proxy-endpoint-name my-endpoint
```
For Windows:  

```
aws rds describe-db-proxy-endpoints ^
  --db-proxy-endpoint-name my-endpoint
```

#### RDS API<a name="rds-proxy-endpoints.DescribingEndpoint.API"></a>

 To describe one or more proxy endpoints, use the RDS API [DescribeDBProxyEndpoints](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBProxyEndpoints.html) operation\. 

### Modifying a proxy endpoint<a name="rds-proxy-endpoints.ModifyingEndpoint"></a>



#### Console<a name="rds-proxy-endpoints.ModifyingEndpoint.CON"></a>

**To modify one or more proxy endpoints**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1.  In the navigation pane, choose **Proxies**\. 

1.  In the list, choose the proxy whose endpoint you want to modify\. Click the proxy name to view its details page\. 

1.  In the **Proxy endpoints** section, choose the endpoint that you want to modify\. You can select it in the list, or click its name to view the details page\. 

1.  On the proxy details page, under the **Proxy endpoints** section, choose **Edit**\. Or on the proxy endpoint details page, for **Actions**, choose **Edit**\. 

1.  Change the values of the parameters that you want to modify\. 

1.  Choose **Save changes**\. 

#### AWS CLI<a name="rds-proxy-endpoints.ModifyingEndpoint.CLI"></a>

 To modify a DB proxy endpoint, use the AWS CLI [modify\-db\-proxy\-endpoint](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-proxy-endpoint.html) command with the following required parameters: 
+  `--db-proxy-endpoint-name` 

 Specify changes to the endpoint properties by using one or more of the following parameters: 
+  `--new-db-proxy-endpoint-name` 
+  `--vpc-security-group-ids`\. Separate the security group IDs with spaces\. 

 The following example renames the `my-endpoint` proxy endpoint to `new-endpoint-name`\. 

**Example**  
For Linux, macOS, or Unix:  

```
aws rds modify-db-proxy-endpoint \
  --db-proxy-endpoint-name my-endpoint \
  --new-db-proxy-endpoint-name new-endpoint-name
```
For Windows:  

```
aws rds modify-db-proxy-endpoint ^
  --db-proxy-endpoint-name my-endpoint ^
  --new-db-proxy-endpoint-name new-endpoint-name
```

#### RDS API<a name="rds-proxy-endpoints.ModifyingEndpoint.API"></a>

 To modify a proxy endpoint, use the RDS API [ModifyDBProxyEndpoint](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBProxyEndpoint.html) operation\. 

### Deleting a proxy endpoint<a name="rds-proxy-endpoints.DeletingEndpoint"></a>

 You can delete an endpoint for your proxy using the console as described following\. 

**Note**  
 You can't delete the default endpoint that RDS Proxy automatically creates for each proxy\.   
 When you delete a proxy, RDS Proxy automatically deletes all the associated endpoints\. 

#### Console<a name="rds-proxy-endpoints.DeleteEndpoint.console"></a>

**To delete a proxy endpoint using the AWS Management Console**

1.  In the navigation pane, choose **Proxies**\. 

1.  In the list, choose the proxy whose endpoint you want to endpoint\. Click the proxy name to view its details page\. 

1.  In the **Proxy endpoints** section, choose the endpoint that you want to delete\. You can select one or more endpoints in the list, or click the name of a single endpoint to view the details page\. 

1.  On the proxy details page, under the **Proxy endpoints** section, choose **Delete**\. Or on the proxy endpoint details page, for **Actions**, choose **Delete**\. 

#### AWS CLI<a name="rds-proxy-endpoints.DeleteEndpoint.cli"></a>

 To delete a proxy endpoint, run the [delete\-db\-proxy\-endpoint](https://docs.aws.amazon.com/cli/latest/reference/rds/delete-db-proxy-endpoint.html) command with the following required parameters: 
+  `--db-proxy-endpoint-name` 

 The following command deletes the proxy endpoint named `my-endpoint`\. 

For Linux, macOS, or Unix:

```
aws rds delete-db-proxy-endpoint \
  --db-proxy-endpoint-name my-endpoint
```

For Windows:

```
aws rds delete-db-proxy-endpoint ^
  --db-proxy-endpoint-name my-endpoint
```

#### RDS API<a name="rds-proxy-endpoints.DeleteEndpoint.api"></a>

 To delete a proxy endpoint with the RDS API, run the [DeleteDBProxyEndpoint](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DeleteDBProxyEndpoint.html) operation\. Specify the name of the proxy endpoint for the `DBProxyEndpointName` parameter\. 

### Limits for proxy endpoints<a name="rds-proxy-endpoints-limits"></a>

 Each proxy has a default endpoint that you can modify but not create or delete\. 

 The maximum number of user\-defined endpoints for a proxy is 20\. Thus, a proxy can have up to 21 endpoints: the default endpoint, plus 20 that you create\. 

 When you associate additional endpoints with a proxy, RDS Proxy automatically determines which DB instances in your cluster to use for each endpoint\. You can't choose specific instances the way that you can with Aurora custom endpoints\. 

 To use cross\-VPC capability with RDS Proxy, all the VPCs must be owned by the same AWS account\. 

 Reader endpoints aren't available for Aurora multi\-writer clusters\. 

 You can connect to proxy endpoints that you create using the SSL modes `REQUIRED` and `VERIFY_CA`\. You can't connect to an endpoint that you create using the SSL mode `VERIFY_IDENTITY`\. 

## Command\-line examples for RDS Proxy<a name="rds-proxy.examples"></a>

 To see how combinations of connection commands and SQL statements interact with RDS Proxy, look at the following examples\. 

**Examples**
+  [Preserving Connections to a MySQL Database Across a Failover](#example-mysql-preserve-connections) 
+  [Adjusting the max_connections Setting for an Aurora DB Cluster](#example-adjust-cluster-max-connections) 

**Example Preserving connections to a MySQL database across a failover**  
 This MySQL example demonstrates how open connections continue working during a failover, for example when you reboot a database or it becomes unavailable due to a problem\. This example uses a proxy named `the-proxy` and an Aurora DB cluster with DB instances `instance-8898` and `instance-9814`\. When you run the `failover-db-cluster` command from the Linux command line, the writer instance that the proxy is connected to changes to a different DB instance\. You can see that the DB instance associated with the proxy changes while the connection remains open\.   

```
$ mysql -h the-proxy.proxy-demo.us-east-1.rds.amazonaws.com -u admin_user -p
Enter password:
...

mysql> select @@aurora_server_id;
+--------------------+
| @@aurora_server_id |
+--------------------+
| instance-9814      |
+--------------------+
1 row in set (0.01 sec)

mysql>
[1]+  Stopped                 mysql -h the-proxy.proxy-demo.us-east-1.rds.amazonaws.com -u admin_user -p
$ # Initially, instance-9814 is the writer.
$ aws rds failover-db-cluster --db-cluster-identifier cluster-56-2019-11-14-1399
JSON output
$ # After a short time, the console shows that the failover operation is complete.
$ # Now instance-8898 is the writer.
$ fg
mysql -h the-proxy.proxy-demo.us.us-east-1.rds.amazonaws.com -u admin_user -p

mysql> select @@aurora_server_id;
+--------------------+
| @@aurora_server_id |
+--------------------+
| instance-8898      |
+--------------------+
1 row in set (0.01 sec)

mysql>
[1]+  Stopped                 mysql -h the-proxy.proxy-demo.us-east-1.rds.amazonaws.com -u admin_user -p
$ aws rds failover-db-cluster --db-cluster-identifier cluster-56-2019-11-14-1399
JSON output
$ # After a short time, the console shows that the failover operation is complete.
$ # Now instance-9814 is the writer again.
$ fg
mysql -h the-proxy.proxy-demo.us-east-1.rds.amazonaws.com -u admin_user -p

mysql> select @@aurora_server_id;
+--------------------+
| @@aurora_server_id |
+--------------------+
| instance-9814      |
+--------------------+
1 row in set (0.01 sec)
+---------------+---------------+
| Variable_name | Value         |
+---------------+---------------+
| hostname      | ip-10-1-3-178 |
+---------------+---------------+
1 row in set (0.02 sec)
```

**Example Adjusting the max\_connections setting for an Aurora DB cluster**  
 This example demonstrates how you can adjust the `max_connections` setting for an Aurora MySQL DB cluster\. To do so, you create your own DB cluster parameter group based on the default parameter settings for clusters that are compatible with MySQL 5\.6 or 5\.7\. You specify a value for the `max_connections` setting, overriding the formula that sets the default value\. You associate the DB cluster parameter group with your DB cluster\.   

```
export REGION=us-east-1
export CLUSTER_PARAM_GROUP=rds-proxy-mysql-56-max-connections-demo
export CLUSTER_NAME=rds-proxy-mysql-56

aws rds create-db-parameter-group --region $REGION \
  --db-parameter-group-family aurora5.6 \
  --db-parameter-group-name $CLUSTER_PARAM_GROUP \
  --description "Aurora MySQL 5.6 cluster parameter group for RDS Proxy demo."

aws rds modify-db-cluster --region $REGION \
  --db-cluster-identifier $CLUSTER_NAME \
  --db-cluster-parameter-group-name $CLUSTER_PARAM_GROUP

echo "New cluster param group is assigned to cluster:"
aws rds describe-db-clusters --region $REGION \
  --db-cluster-identifier $CLUSTER_NAME \
  --query '*[*].{DBClusterParameterGroup:DBClusterParameterGroup}'

echo "Current value for max_connections:"
aws rds describe-db-cluster-parameters --region $REGION \
  --db-cluster-parameter-group-name $CLUSTER_PARAM_GROUP \
  --query '*[*].{ParameterName:ParameterName,ParameterValue:ParameterValue}' \
  --output text | grep "^max_connections"

echo -n "Enter number for max_connections setting: "
read answer

aws rds modify-db-cluster-parameter-group --region $REGION --db-cluster-parameter-group-name $CLUSTER_PARAM_GROUP \
  --parameters "ParameterName=max_connections,ParameterValue=$$answer,ApplyMethod=immediate"

echo "Updated value for max_connections:"
aws rds describe-db-cluster-parameters --region $REGION \
  --db-cluster-parameter-group-name $CLUSTER_PARAM_GROUP \
  --query '*[*].{ParameterName:ParameterName,ParameterValue:ParameterValue}' \
  --output text | grep "^max_connections"
```

## Troubleshooting for RDS Proxy<a name="rds-proxy.troubleshooting"></a>

 Following, you can find troubleshooting ideas for some common RDS Proxy issues and information on CloudWatch logs for RDS Proxy\. 

 In the RDS Proxy logs, each entry is prefixed with the name of the associated proxy endpoint\. This name can be the name you specified for a user\-defined endpoint, or the special name `default` for read/write requests using the default endpoint of a proxy\. For more information about proxy endpoints, see [Endpoints for Amazon RDS Proxy](#rds-proxy-endpoints)\. 

**Topics**
+ [Common issues and solutions](#rds-proxy-diagnosis)
+ [Working with CloudWatch logs for RDS Proxy](#rds-proxy-logs)
+ [Verifying connectivity for a proxy](#rds-proxy-verifying)

### Common issues and solutions<a name="rds-proxy-diagnosis"></a>

 For possible causes and solutions to some common problems that you might encounter using RDS Proxy, see the following\. 

 You might encounter the following issues while creating a new proxy or connecting to a proxy\. 


|  Error  |  Causes or workarounds  | 
| --- | --- | 
|   `403: The security token included in the request is invalid`   |  Select an existing IAM role instead of choosing to create a new one\.  | 

 You might encounter the following issues while connecting to a MySQL proxy\. 


|  Error  |  Causes or workarounds  | 
| --- | --- | 
|  ERROR 1040 \(HY000\): Connections rate limit exceeded \(limit\_value\)  |  The rate of connection requests from the client to the proxy has exceeded the limit\.  | 
|  ERROR 1040 \(HY000\): IAM authentication rate limit exceeded  |  The number of simultaneous requests with IAM authentication from the client to the proxy has exceeded the limit\.  | 
|  ERROR 1040 \(HY000\): Number simultaneous connections exceeded \(limit\_value\)  |  The number of simultaneous connection requests from the client to the proxy exceeded the limit\.  | 
|   `ERROR 1045 (28000): Access denied for user 'DB_USER'@'%' (using password: YES)`   |   Some possible reasons include the following:  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/rds-proxy.html)  | 
|  ERROR 1105 \(HY000\): Unknown error  |  An unknown error occurred\.  | 
|  ERROR 1231 \(42000\): Variable ''character\_set\_client'' can't be set to the value of value  |   The value set for the `character_set_client` parameter is not valid\. For example, the value `ucs2` is not valid because it can crash the MySQL server\.   | 
|  ERROR 3159 \(HY000\): This RDS Proxy requires TLS connections\.  |   You enabled the setting **Require Transport Layer Security** in the proxy but your connection included the parameter `ssl-mode=DISABLED` in the MySQL client\. Do either of the following:  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/rds-proxy.html)  | 
|  ERROR 2026 \(HY000\): SSL connection error: Internal Server Error  |   The TLS handshake to the proxy failed\. Some possible reasons include the following:  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/rds-proxy.html)  | 
|  ERROR 9501 \(HY000\): Timed\-out waiting to acquire database connection  |   The proxy timed\-out waiting to acquire a database connection\. Some possible reasons include the following:  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/rds-proxy.html)  | 

 You might encounter the following issues while connecting to a PostgreSQL proxy\. 


|  Error  |  Cause  |  Solution  | 
| --- | --- | --- | 
|   `IAM authentication is allowed only with SSL connections.`   |   The user tried to connect to the database using IAM authentication with the setting `sslmode=disable` in the PostgreSQL client\.   |   The user needs to connect to the database using the minimum setting of `sslmode=require` in the PostgreSQL client\. For more information, see the [PostgreSQL SSL support](https://www.postgresql.org/docs/current/libpq-ssl.html) documentation\.   | 
|   `This RDS Proxy requires TLS connections.`   |   The user enabled the option **Require Transport Layer Security** but tried to connect with `sslmode=disable` in the PostgreSQL client\.   |   To fix this error, do one of the following:  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/rds-proxy.html)  | 
|   `IAM authentication failed for user user_name. Check the IAM token for this user and try again.`   |   This error might be due to the following reasons:  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/rds-proxy.html)  |   To fix this error, do the following:  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/rds-proxy.html)  | 
|   `This RDS proxy has no credentials for the role role_name. Check the credentials for this role and try again.`   |   There is no Secrets Manager secret for this role\.   |   Add a Secrets Manager secret for this role\.   | 
|   `RDS supports only IAM or MD5 authentication.`   |   The database client being used to connect to the proxy is using an authentication mechanism not currently supported by the proxy, such as SCRAM\-SHA\-256\.   |   If you're not using IAM authentication, use the MD5 password authentication only\.   | 
|   `A user name is missing from the connection startup packet. Provide a user name for this connection.`   |   The database client being used to connect to the proxy isn't sending a user name when trying to establish a connection\.   |   Make sure to define a user name when setting up a connection to the proxy using the PostgreSQL client of your choice\.   | 
|   `Feature not supported: RDS Proxy supports only version 3.0 of the PostgreSQL messaging protocol.`   |   The PostgreSQL client used to connect to the proxy uses a protocol older than 3\.0\.   |   Use a newer PostgreSQL client that supports the 3\.0 messaging protocol\. If you're using the PostgreSQL `psql` CLI, use a version greater than or equal to 7\.4\.   | 
|   `Feature not supported: RDS Proxy currently doesn't support streaming replication mode.`   |   The PostgreSQL client used to connect to the proxy is trying to use the streaming replication mode, which isn't currently supported by RDS Proxy\.   |   Turn off the streaming replication mode in the PostgreSQL client being used to connect\.   | 
|   `Feature not supported: RDS Proxy currently doesn't support the option option_name.`   |   Through the startup message, the PostgreSQL client used to connect to the proxy is requesting an option that isn't currently supported by RDS Proxy\.   |   Turn off the option being shown as not supported from the message above in the PostgreSQL client being used to connect\.   | 
|   `The IAM authentication failed because of too many competing requests.`   |   The number of simultaneous requests with IAM authentication from the client to the proxy has exceeded the limit\.   |   Reduce the rate in which connections using IAM authentication from a PostgreSQL client are established\.   | 
|   `The maximum number of client connections to the proxy exceeded number_value.`   |   The number of simultaneous connection requests from the client to the proxy exceeded the limit\.   |   Reduce the number of active connections from PostgreSQL clients to this RDS proxy\.   | 
|   `Rate of connection to proxy exceeded number_value.`   |   The rate of connection requests from the client to the proxy has exceeded the limit\.   |   Reduce the rate in which connections from a PostgreSQL client are established\.   | 
|   `The password that was provided for the role role_name is wrong.`   |   The password for this role doesn't match the Secrets Manager secret\.   |   Check the secret for this role in Secrets Manager to see if the password is the same as what's being used in your PostgreSQL client\.   | 
|   `The IAM authentication failed for the role role_name. Check the IAM token for this role and try again.`   |   There is a problem with the IAM token used for IAM authentication\.   |   Generate a new authentication token and use it in a new connection\.   | 
|   `IAM is allowed only with SSL connections.`   |   A client tried to connect using IAM authentication, but SSL wasn't enabled\.   |   Enable SSL in the PostgreSQL client\.   | 
|   `Unknown error.`   |   An unknown error occurred\.   |   Reach out to AWS Support for us to investigate the issue\.   | 
|   `Timed-out waiting to acquire database connection.`   |   The proxy timed\-out waiting to acquire a database connection\. Some possible reasons include the following:  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/rds-proxy.html)  |   Possible solutions are:  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/rds-proxy.html)  | 
|   `Request returned an error: database_error.`   |   The database connection established from the proxy returned an error\.   |   The solution depends on the specific database error\. One example is: `Request returned an error: database "your-database-name" does not exist`\. This means the specified database name, or the user name used as a database name \(in case a database name hasn't been specified\), doesn't exist in the database server\.   | 

### Working with CloudWatch logs for RDS Proxy<a name="rds-proxy-logs"></a>

 You can find logs of RDS Proxy activity under CloudWatch in the AWS Management Console\. Each proxy has an entry in the **Log groups** page\. 

**Important**  
 These logs are intended for human consumption for troubleshooting purposes and not for programmatic access\. The format and content of the logs is subject to change\.   
 In particular, older logs don't contain any prefixes indicating the endpoint for each request\. In newer logs, each entry is prefixed with the name of the associated proxy endpoint\. This name can be the name that you specified for a user\-defined endpoint, or the special name `default` for requests using the default endpoint of a proxy\. 

### Verifying connectivity for a proxy<a name="rds-proxy-verifying"></a>

 You can use the following commands to verify that all components of the connection mechanism can communicate with the other components\. 

 Examine the proxy itself using the [describe\-db\-proxies](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-proxies.html) command\. Also examine the associated target group using the [describe\-db\-proxy\-target\-groups](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-proxy-target-groups.html) Check that the details of the targets match the RDS DB instance or Aurora DB cluster that you intend to associate with the proxy\. Use commands such as the following\. 

```
aws rds describe-db-proxies --db-proxy-name $DB_PROXY_NAME
aws rds describe-db-proxy-target-groups --db-proxy-name $DB_PROXY_NAME
```

 To confirm that the proxy can connect to the underlying database, examine the targets specified in the target groups using the [describe\-db\-proxy\-targets](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-proxy-targets.html) command\. Use a command such as the following\. 

```
aws rds describe-db-proxy-targets --db-proxy-name $DB_PROXY_NAME
```

 The output of the [describe\-db\-proxy\-targets](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-proxy-targets.html) command includes a `TargetHealth` field\. You can examine the fields `State`, `Reason`, and `Description` inside `TargetHealth` to check if the proxy can communicate with the underlying DB instance\. 
+  A `State` value of `AVAILABLE` indicates that the proxy can connect to the DB instance\. 
+  A `State` value of `UNAVAILABLE` indicates a temporary or permanent connection problem\. In this case, examine the `Reason` and `Description` fields\. For example, if `Reason` has a value of `PENDING_PROXY_CAPACITY`, try connecting again after the proxy finishes its scaling operation\. If `Reason` has a value of `UNREACHABLE`, `CONNECTION_FAILED`, or `AUTH_FAILURE`, use the explanation from the `Description` field to help you diagnose the issue\. 
+  The `State` field might have a value of `REGISTERING` for a brief time before changing to `AVAILABLE` or `UNAVAILABLE`\. 

 If the following Netcat command \(`nc`\) is successful, you can access the proxy endpoint from the EC2 instance or other system where you're logged in\. This command reports failure if you're not in the same VPC as the proxy and the associated database\. You might be able to log directly in to the database without being in the same VPC\. However, you can't log into the proxy unless you're in the same VPC\. 

```
nc -zx MySQL_proxy_endpoint 3306

nc -zx PostgreSQL_proxy_endpoint 5432
```

 You can use the following commands to make sure that your EC2 instance has the required properties\. In particular, the VPC for the EC2 instance must be the same as the VPC for the RDS DB instance or Aurora DB cluster that the proxy connects to\. 

```
aws ec2 describe-instances --instance-ids your_ec2_instance_id
```

 Examine the Secrets Manager secrets used for the proxy\. 

```
aws secretsmanager list-secrets
aws secretsmanager get-secret-value --secret-id your_secret_id
```

 Make sure that the `SecretString` field displayed by `get-secret-value` is encoded as a JSON string that includes `username` and `password` fields\. The following example shows the format of the `SecretString` field\. 

```
{
  "ARN": "some_arn",
  "Name": "some_name",
  "VersionId": "some_version_id",
  "SecretString": '{"username":"some_username","password":"some_password"}',
  "VersionStages": [ "some_stage" ],
  "CreatedDate": some_timestamp
}
```

## Using RDS Proxy with AWS CloudFormation<a name="rds-proxy-cfn"></a>

 You can use RDS Proxy with AWS CloudFormation\. Doing so helps you to create groups of related resources, including a proxy that can connect to a newly created Amazon RDS DB instance or Aurora DB cluster\. RDS Proxy support in AWS CloudFormation involves two new registry types: `DBProxy` and `DBProxyTargetGroup`\. 

 The following listing shows a sample AWS CloudFormation template for RDS Proxy\. 

```
Resources:
 DBProxy:
   Type: AWS::RDS::DBProxy
   Properties:
     DBProxyName: CanaryProxy
     EngineFamily: MYSQL
     RoleArn:
      Fn::ImportValue: SecretReaderRoleArn
     Auth:
       - {AuthScheme: SECRETS, SecretArn: !ImportValue ProxySecret, IAMAuth: DISABLED}
     VpcSubnetIds:
       Fn::Split: [",", "Fn::ImportValue": SubnetIds]

 ProxyTargetGroup: 
   Type: AWS::RDS::DBProxyTargetGroup
   Properties:
     DBProxyName: CanaryProxy
     TargetGroupName: default
     DBInstanceIdentifiers: 
       - Fn::ImportValue: DBInstanceName
   DependsOn: DBProxy
```

 For more information about the Amazon RDS and Aurora resources that you can create using AWS CloudFormation, see [RDS resource type reference](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/AWS_RDS.html)\. 