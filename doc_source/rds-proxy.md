# Managing Connections with Amazon RDS Proxy<a name="rds-proxy"></a>

 By using Amazon RDS Proxy, you can allow your applications to pool and share database connections to improve their ability to scale\. RDS Proxy makes applications more resilient to database failures by automatically connecting to a standby DB instance while preserving application connections\. RDS Proxy also enables you to enforce AWS Identity and Access Management \(IAM\) authentication for databases, and securely store credentials in AWS Secrets Manager\. 

**Note**  
 RDS Proxy is fully compatible with MySQL and PostgreSQL\. You can enable RDS Proxy for most applications with no code changes\. 

 Using RDS Proxy, you can handle unpredictable surges in database traffic that otherwise might cause issues due to oversubscribing connections or creating new connections at a fast rate\. RDS Proxy establishes a database connection pool and reuses connections in this pool without the memory and CPU overhead of opening a new database connection each time\. To protect the database against oversubscription, you can control the number of database connections that are created\. 

 RDS Proxy queues or throttles application connections that can't be served immediately from the pool of connections\. Although latencies might increase, your application can continue to scale without abruptly failing or overwhelming the database\. If connection requests exceed the limits you specify, RDS Proxy rejects application connections \(that is, it sheds load\)\. At the same time, it maintains predictable performance for the load that can be served with the available capacity\. 

 You can reduce the overhead to process credentials and establish a secure connection for each new connection\. RDS Proxy can handle some of that work on behalf of the database\. 

**Topics**
+ [RDS Proxy Concepts and Terminology](#rds-proxy.howitworks)
+ [Planning for and Setting Up RDS Proxy](#rds-proxy-setup)
+ [Connecting to a Database Through RDS Proxy](#rds-proxy-connecting)
+ [Managing an RDS Proxy](#rds-proxy-managing)
+ [Monitoring RDS Proxy Using Amazon CloudWatch](#rds-proxy.monitoring)
+ [Command\-Line Examples for RDS Proxy](#rds-proxy.examples)
+ [Troubleshooting for RDS Proxy](#rds-proxy.troubleshooting)
+ [Using RDS Proxy with AWS CloudFormation](#rds-proxy-cfn)

## RDS Proxy Concepts and Terminology<a name="rds-proxy.howitworks"></a>

 You can simplify connection management for your Amazon RDS DB instances and Amazon Aurora DB clusters by using RDS Proxy\. 

 RDS Proxy handles the network traffic between the client application and the database\. It does so in an active way first by understanding the database protocol\. It then adjusts its behavior based on the SQL operations from your application and the result sets from the database\. 

 RDS Proxy reduces the memory and CPU overhead for connection management on your database\. The database needs less memory and CPU resources when applications open many simultaneous connections\. It also doesn't require logic in your applications to close and reopen connections that stay idle for a long time\. Similarly, it requires less application logic to reestablish connections in case of a database problem\. 

 The infrastructure for RDS Proxy is highly available and deployed over multiple Availability Zones \(AZs\)\. The computation, memory, and storage for RDS Proxy are independent of your RDS DB instances and Aurora DB clusters\. This separation helps lower overhead on your database servers, so that they can devote their resources to serving database workloads\. The RDS Proxy compute resources are serverless, automatically scaling based on your database workload\. 

**Topics**
+ [Overview of RDS Proxy Concepts](#rds-proxy-overview)
+ [Connection Pooling](#rds-proxy-connection-pooling)
+ [RDS Proxy Security](#rds-proxy-security)
+ [Failover](#rds-proxy-failover)
+ [Transactions](#rds-proxy-transactions)

### Overview of RDS Proxy Concepts<a name="rds-proxy-overview"></a>

 RDS Proxy handles the infrastructure to perform connection pooling and the other features described following\. You see the proxies represented in the RDS console on the **Proxies** page\. 

 Each proxy handles connections to a single RDS DB instance or Aurora DB cluster\. The proxy automatically determines the current writer instance for RDS Multi\-AZ DB instances and Aurora provisioned clusters\. For Aurora multi\-master clusters, the proxy connects to one of the writer instances and uses the other writer instances as hot standby targets\.  

 The connections that a proxy keeps open and available for your database application to use form the *connection pool*\. 

 By default, RDS Proxy can reuse a connection after each transaction in your session\. This transaction\-level reuse is called *multiplexing*\. When RDS Proxy temporarily removes a connection from the connection pool to reuse it, that operation is called *borrowing* the connection\. When it's safe to do so, RDS Proxy returns that connection to the connection pool\. 

 In some cases, RDS Proxy can't be sure that it's safe to reuse a database connection outside of the current session\. In these cases, it keeps the session on the same connection until the session ends\. This fallback behavior is called *pinning*\. 

 A proxy has an endpoint\. You connect to this endpoint when you work with an RDS DB instance or Aurora DB cluster, instead of connecting to the read/write endpoint that connects directly to the instance or cluster\. The special\-purpose endpoints for an Aurora cluster remain available for you to use\. 

 For example, you can still connect to the cluster endpoint for read/write connections without connection pooling\. You can still connect to the reader endpoint for load\-balanced read\-only connections\. You can still connect to the instance endpoints for diagnosis and troubleshooting of specific DB instances within an Aurora cluster\. If you are using other AWS services such as AWS Lambda to connect to RDS databases, you change their connection settings to use the proxy endpoint\. For example, you specify the proxy endpoint to allow Lambda functions to access your database while taking advantage of RDS Proxy functionality\. 

 Each proxy contains a target group\. This *target group* embodies the RDS DB instance or Aurora DB cluster that the proxy can connect to\. For an Aurora cluster, by default the target group is associated with all the DB instances in that cluster\. That way, the proxy can connect to whichever Aurora DB instance is promoted to be the writer instance in the cluster\. The RDS DB instance associated with a proxy, or the Aurora DB cluster and its instances, are called the *targets* of that proxy\. For convenience, when you create a proxy through the console, RDS Proxy also creates the corresponding target group and registers the associated targets automatically\. 

 An *engine family* is a related set of database engines that use the same DB protocol\. You choose the engine family for each proxy that you create\.  

### Connection Pooling<a name="rds-proxy-connection-pooling"></a>

 Each proxy performs connection pooling for the writer instance of its associated RDS or Aurora database\. *Connection pooling* is an optimization that reduces the overhead associated with opening and closing connections and with keeping many connections open simultaneously\. This overhead includes memory needed to handle each new connection\. It also involves CPU overhead to close each connection and open a new one, such as Transport Layer Security/Secure Sockets Layer \(TLS/SSL\) handshaking, authentication, negotiating capabilities, and so on\. Connection pooling simplifies your application logic\. You don't need to write application code to minimize the number of simultaneous open connections\. 

 Each proxy also performs connection multiplexing, also known as connection reuse\. With *multiplexing*, RDS Proxy perform all the operations for a transaction using one underlying database connection, then can use a different connection for the next transaction\. You can open many simultaneous connections to the proxy, and the proxy keeps a smaller number of connections open to the DB instance or cluster\. Doing so further minimizes the memory overhead for connections on the database server\. This technique also reduces the chance of "too many connections" errors\. 

### RDS Proxy Security<a name="rds-proxy-security"></a>

 RDS Proxy uses the existing RDS security mechanisms such as TLS/SSL and AWS Identity and Access Management \(IAM\)\. For general information about those security features, see [Security in Amazon RDS](UsingWithRDS.md)\. If you aren't familiar with how RDS and Aurora work with authentication, authorization, and other areas of security, make sure to familiarize yourself with how RDS and Aurora work with those areas first\.  

 RDS Proxy can act as an additional layer of security between client applications and the underlying database\. For example, you can connect to the proxy using TLS 1\.2, even if the underlying DB instance supports only TLS 1\.0 or 1\.1\. You can connect to the proxy using an IAM role, even if the proxy connects to the database using the native user and password authentication method\. By using this technique, you can enforce strong authentication requirements for database applications without a costly migration effort for the DB instances themselves\. 

 You store the database credentials used by RDS Proxy in AWS Secrets Manager\. Each database user for the RDS DB instance or Aurora DB cluster accessed by a proxy must have a corresponding secret in Secrets Manager\. You can also set up IAM authentication for users of RDS Proxy\. By doing so, you can enforce IAM authentication for database access even if the databases use native password authentication\. We recommend using these security features instead of embedding database credentials in your application code\. 

#### Using TLS/SSL with RDS Proxy<a name="rds-proxy-security.tls"></a>

 You can connect to RDS Proxy using the TLS/SSL protocol\. 

**Note**  
 RDS Proxy uses certificates from the AWS Certificate Manager \(ACM\)\. If you use RDS Proxy, when you rotate your TLS/SSL certificate you don't need to update applications that use RDS Proxy connections\. 

 To enforce TLS for all connections between the proxy and your database, you can specify a setting **Require Transport Layer Security** when you create or modify a proxy\. 

 RDS Proxy can also ensure that your session uses TLS/SSL between your client and the RDS Proxy endpoint\. To have RDS Proxy do so, specify the requirement on the client side\. SSL session variables are not set for SSL connections to a database using RDS Proxy\. 
+  For Amazon RDS MySQL and Aurora MySQL, specify the requirement on the client side with the `--ssl-mode` parameter when you run the `mysql` command\. 
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

 When using a client with `--ssl-mode` `VERIFY_CA` or `VERIFY_IDENTITY`, specify the `--ssl-ca` option pointing to a CA in \.pem format\. For a \.pem file that you can use, download the [Amazon Root CA 1 trust store](https://www.amazontrust.com/repository/AmazonRootCA1.pem) from Amazon Trust Services\. 

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
+  Connection reuse can happen after each individual statement when the RDS MySQL or Aurora MySQL `autocommit` setting is enabled\. 
+  Conversely, when the `autocommit` setting is disabled, the first statement you issue in a session begins a new transaction\. Thus, if you enter a sequence of `SELECT`, `INSERT`, `UPDATE`, and other data manipulation language \(DML\) statements, connection reuse doesn't happen until you issue a `COMMIT`, `ROLLBACK`, or otherwise end the transaction\. 
+  Entering a data definition language \(DDL\) statement causes the transaction to end after that statement completes\. 

 RDS Proxy detects when a transaction ends through the network protocol used by the database client application\. Transaction detection doesn't rely on keywords such as `COMMIT` or `ROLLBACK` appearing in the text of the SQL statement\. 

 In some cases, RDS Proxy might detect a database request that makes it impractical to move your session to a different connection\. In these cases, it turns off multiplexing for that connection the remainder of your session\. The same rule applies if RDS Proxy can't be certain that multiplexing is practical for the session\. This operation is called *pinning*\. For ways to detect and minimize pinning, see [Avoiding Pinning](#rds-proxy-pinning)\. 

## Planning for and Setting Up RDS Proxy<a name="rds-proxy-setup"></a>

 In the following sections, you can find how to set up RDS Proxy\. You can also find how to set the related security options that control who can access each proxy and how each proxy connects to DB instances\. 

**Topics**
+ [Limitations for RDS Proxy](#rds-proxy.limitations)
+ [Identifying DB Instances, Clusters, and Applications to Use with RDS Proxy](#rds-proxy-planning)
+ [Setting Up Network Prerequisites](#rds-proxy-network-prereqs)
+ [Setting Up Database Credentials in AWS Secrets Manager](#rds-proxy-secrets-arns)
+ [Setting Up AWS Identity and Access Management \(IAM\) Policies](#rds-proxy-iam-setup)
+ [Creating an RDS Proxy](#rds-proxy-creating)
+ [Viewing an RDS Proxy](#rds-proxy-viewing)

### Limitations for RDS Proxy<a name="rds-proxy.limitations"></a>

 The following limitations apply to RDS Proxy: 
+  RDS Proxy is available only in these AWS Regions: 
  +  US East \(N\. Virginia\) Region 
  +  US East \(Ohio\) Region 
  +  US West \(N\. California\) Region 
  +  US West \(Oregon\) Region 
  +  Asia Pacific \(Mumbai\) Region 
  +  Asia Pacific \(Seoul\) Region 
  +  Asia Pacific \(Singapore\) Region 
  +  Asia Pacific \(Sydney\) Region 
  +  Asia Pacific \(Tokyo\) Region 
  +  Canada \(Central\) Region 
  +  Europe \(Frankfurt\) Region 
  +  Europe \(Ireland\) Region 
  +  Europe \(London\) Region 
+  You can have up to 20 proxies for each AWS account ID\. If your application requires more proxies, you can request additional proxies by opening a ticket with the AWS Support organization\.  
+  Each proxy can have up to 200 associated Secrets Manager secrets\. Thus, each proxy can connect to with up to 200 different user accounts at any given time\. 
+  In an Aurora cluster, all of the connections in the connection pool are handled by the Aurora writer instance\. To perform load balancing for read\-intensive workloads, you still use the reader endpoint directly for the Aurora cluster\. 

   The same consideration applies for RDS DB instances in replication configurations\. You can associate a proxy only with the writer DB instance, not a read replica\. 
+  You can't use RDS Proxy with Aurora Serverless clusters\. 
+  You can't use RDS Proxy with Aurora clusters that are part of an Aurora global database\. 
+  Your RDS Proxy must be in the same VPC as the database\. The proxy can't be publicly accessible, although the database can be\. 
+  You can't use RDS Proxy with a VPC that has dedicated tenancy\. 
+  If you use RDS Proxy with an RDS DB instance or Aurora DB cluster that has IAM authentication enabled, make sure that all users who connect through a proxy authenticate through user names and passwords\. See [Setting Up AWS Identity and Access Management \(IAM\) Policies](#rds-proxy-iam-setup) for details about IAM support in RDS Proxy\. 
+  You can't use RDS Proxy with custom DNS\. 
+  RDS Proxy is available for the MySQL and PostgreSQL engine families\. 
+  Each proxy can be associated with a single target DB instance or cluster\. However, you can associate multiple proxies with the same DB instance or cluster\. 

 The following RDS Proxy limitations apply to MySQL: 
+  The MySQL engine family includes RDS MySQL 5\.6 and 5\.7, and Aurora MySQL versions 1 and 2\. 
+  Currently, all proxies listen on port 3306 for MySQL\. The proxies still connect to your database using the port that you specified in the database settings\. 
+  You can't use RDS Proxy with RDS MySQL 8\.0\. 
+  You can't use RDS Proxy with self\-managed MySQL databases in EC2 instances\. 
+  Proxies don't support MySQL compressed mode\. For example, they don't support the compression used by the `--compress` or `-C` options of the `mysql` command\. 
+  Some SQL statements and functions can change the connection state without causing pinning\. For the most current pinning behavior, see [Avoiding Pinning](#rds-proxy-pinning)\. 

 The following RDS Proxy limitations apply to PostgreSQL: 
+  The RDS PostgreSQL engine family includes version 10\.10 and higher minor versions and version 11\.5 and higher minor versions\. 
+  Currently, all proxies listen on port 5432 for PostgreSQL\. 
+  Query cancellation isn't supported for PostgreSQL\. 
+  The results of the PostgreSQL function [lastval\(\)](https://www.postgresql.org/docs/current/functions-sequence.html) aren't always accurate\. As a work\-around, use the [INSERT](https://www.postgresql.org/docs/current/sql-insert.html) statement with the `RETURNING` clause\. 

### Identifying DB Instances, Clusters, and Applications to Use with RDS Proxy<a name="rds-proxy-planning"></a>

 You can determine which of your DB instances, clusters, and applications might benefit the most from using RDS Proxy\. To do so, consider these factors: 
+  RDS Proxy is highly available and deployed over multiple Availability Zones \(AZs\)\. To ensure overall high availability for your database, deploy your Amazon RDS DB instance or Aurora cluster in a Multi\-AZ configuration\. 
+  Any DB instance or cluster that encounters "too many connections" errors is a good candidate for associating with a proxy\. The proxy enables applications to open many client connections, while the proxy manages a smaller number of long\-lived connections to the DB instance or cluster\. 
+  For DB instances or clusters that use smaller AWS instance classes, such as T2 or T3, using a proxy can help avoid out\-of\-memory conditions\. It can also help reduce the CPU overhead for establishing connections\. These conditions can occur when dealing with large numbers of connections\. 
+  You can monitor certain Amazon CloudWatch metrics to determine whether a DB instance or cluster is approaching certain types of limit\. These limits are for the number of connections and the memory associated with connection management\. You can also monitor certain CloudWatch metrics to determine whether a DB instance or cluster is handling many short\-lived connections\. Opening and closing such connections can impose performance overhead on your database\. For information about the metrics to monitor, see [Monitoring RDS Proxy Using Amazon CloudWatchMonitoring RDS Proxy](#rds-proxy.monitoring)\. 
+  AWS Lambda functions can also be good candidates for using a proxy\. These functions make frequent short database connections that benefit from connection pooling offered by RDS Proxy\. You can take advantage of any IAM authentication you already have for Lambda functions, instead of managing database credentials in your Lambda application code\. 
+  Applications that use languages and frameworks such as PHP and Ruby on Rails are typically good candidates for using a proxy\. Such applications typically open and close large numbers of database connections, and don't have built\-in connection pooling mechanisms\. 
+  Applications that keep a large number of connections open for long periods are typically good candidates for using a proxy\. Applications in industries such as software as a service \(SaaS\) or ecommerce often minimize the latency for database requests by leaving connections open\. With RDS Proxy, an application can keep more connections open than it can when connecting directly to the DB instance or cluster\. 
+  You might not have adopted IAM authentication and Secrets Manager due to the complexity of setting up such authentication for all DB instances and clusters\. If so, you can leave the existing authentication methods in place and delegate the authentication to a proxy\. The proxy can enforce the authentication policies for client connections for particular applications\. You can take advantage of any IAM authentication you already have for Lambda functions, instead of managing database credentials in your Lambda application code\. 

### Setting Up Network Prerequisites<a name="rds-proxy-network-prereqs"></a>

 Using RDS Proxy requires you to have a set of networking resources in place\. These include a virtual private cloud \(VPC\), two or more subnets, an Amazon EC2 instance within the same VPC, and an internet gateway\. If you've successfully connected to any RDS DB instances or Aurora DB clusters, you already have the required network resources\.  

### Setting Up Database Credentials in AWS Secrets Manager<a name="rds-proxy-secrets-arns"></a>

 For each proxy that you create, you first use the Secrets Manager service to store sets of user name and password credentials\. You create a separate Secrets Manager secret for each database user account that the proxy connects to on the RDS DB instance or Aurora DB cluster\. 

 In Secrets Manager, you create these secrets with values for the `username` and `password` fields\. Doing so allows the proxy to connect to the corresponding database users on whichever RDS DB instances or Aurora DB clusters that you associate with the proxy\. To do this, you can use the setting **Credentials for other database**, **Credentials for RDS database**, or **Other type of secrets**\. Fill in the appropriate values for the **User name** and **Password** fields, and placeholder values for any other required fields\. The proxy ignores other fields such as **Host** and **Port** if they're present in the secret\. Those details are automatically supplied by the proxy\. 

 You can also choose **Other type of secrets**\. In this case, you create the secret with keys named `username` and `password`\. 

 Because the secrets used by your proxy aren't tied to a specific database server, you can reuse a secret across multiple proxies if you use the same credentials across multiple database servers\. For example, you might use the same credentials across a group of development and test servers\. 

 To connect through the proxy as a specific user, make sure that the password associated with a secret matches the database password for that user\. If there's a mismatch, you can update the associated secret in Secrets Manager\. In this case, you can still connect to other accounts where the secret credentials and the database passwords do match\. 

 When you create a proxy through the AWS CLI or RDS API, you specify the Amazon Resource Names \(ARNs\) of the corresponding secrets for all the DB user accounts that the proxy can access\. In the AWS Management Console, you choose the secrets by their descriptive names\. 

 For instructions about creating secrets in Secrets Manager, see the [Creating a Secret](https://docs.aws.amazon.com/secretsmanager/latest/userguide/manage_create-basic-secret.html) page in the Secrets Manager documentation\. Use one of the following techniques\. For instructions about creating secrets in Secrets Manager, see [Creating a Secret](https://docs.aws.amazon.com/secretsmanager/latest/userguide/manage_create-basic-secret.html) in the *AWS Secrets Manager User Guide*\. Use one of the following techniques: 
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

### Setting Up AWS Identity and Access Management \(IAM\) Policies<a name="rds-proxy-iam-setup"></a>

 After you create the secrets in Secrets Manager, you create an IAM policy that can access those secrets\. For general information about using IAM with RDS and Aurora, see [Identity and Access Management in Amazon RDS](UsingWithRDS.IAM.md)\. 

**Tip**  
 The following procedure applies if you use the IAM console\. If you use the AWS Management Console for RDS, RDS can create the IAM policy for you automatically\. In that case, you can skip the following procedure\. 

**To create an IAM policy that accesses your Secrets Manager secrets for use with your proxy**

1.  Sign in to the IAM console\. Follow the **Create role** process, as described in [Creating IAM Roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create.html)\. Include the **Add Role to Database** step\. 

1.  For the new role, perform the **Add inline policy** step\. Use the same general procedures as in [Editing IAM Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_manage-edit.html)\. Paste the following JSON into the JSON text box\. Substitute your own account ID\. Substitute your AWS Region for `us-east-2`\. Substitute the Amazon Resource Names \(ARNs\) for the secrets that you created\. For the `kms:Decrypt` action, substitute the ARN of the default AWS KMS customer master key \(CMK\) or your own AWS KMS CMK, depending on which one you used to encrypt the Secrets Manager secrets\. 

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

1.  Edit the trust policy for this IAM policy\. Paste the following JSON into the JSON text box\. 

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

 To manage connections for a specified set of DB instances, you can create a proxy\. You can associate a proxy with an RDS MySQL DB instance, PostgreSQL DB instance, or an Aurora DB cluster\. 

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
   +  **Database**\. Choose one RDS DB instance or Aurora DB cluster to access through this proxy\. The list only includes DB instances and clusters with compatible database engines, engine versions, and other settings\. If the list is empty, create a new DB instance or cluster that's compatible with RDS Proxy\. To do so, follow the procedure in [Creating an Amazon RDS DB Instance](USER_CreateDBInstance.md) \. Then try creating the proxy again\. 
   +  **Connection pool maximum connections**\. Specify a value from 1 through 100\. This setting represents the percentage of the `max_connections` value that RDS Proxy can use for its connections\. If you only intend to use one proxy with this DB instance or cluster, you can set this value to 100\. For details about how RDS Proxy uses this setting, see [Controlling Connection Limits and Timeouts](#rds-proxy-connection-limits)\. 
   +  **Session pinning filters**\. \(Optional\) This is an advanced setting, for troubleshooting performance issues with particular applications\. Currently, the only choice is `EXCLUDE_VARIABLE_SETS`\. Choose a filter only if both of following are true: Your application isn't reusing connections due to certain kinds of SQL statements, and you can verify that reusing connections with those SQL statements doesn't affect application correctness\. For more information, see [Avoiding Pinning](#rds-proxy-pinning)\. 
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
    --role-arn iam_role \
    --engine-family { MYSQL | POSTGRESQL } \
    --vpc-subnet-ids space_separated_list \
    [--vpc-security-group-ids space_separated_list] \
    [--auth ProxyAuthenticationConfig_JSON_string] \
    [--require-tls | --no-require-tls] \
    [--idle-client-timeout value] \
    [--debug-logging | --no-debug-logging] \
    [--tags comma_separated_list]
```
For Windows:  

```
aws rds create-db-proxy ^
    --db-proxy-name proxy_name ^
    --role-arn iam_role ^
    --engine-family { MYSQL | POSTGRESQL } ^
    --vpc-subnet-ids space_separated_list ^
    [--vpc-security-group-ids space_separated_list] ^
    [--auth ProxyAuthenticationConfig_JSON_string] ^
    [--require-tls | --no-require-tls] ^
    [--idle-client-timeout value] ^
    [--debug-logging | --no-debug-logging] ^
    [--tags comma_separated_list]
```

 To create the required information and associations for the proxy, you also use the  [register\-db\-proxy\-targets](https://docs.aws.amazon.com/cli/latest/reference/rds/register-db-proxy-targets.html) command\. Specify the target group name `default`\. RDS Proxy automatically creates a target group with this name when you create each proxy\. 

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

1.  To show connection parameters such as the maximum percentage of connections that the proxy can use, run [describe\-db\-proxy\-target\-groups](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-proxy-target-groups.html) `-—db-proxy-name` and use the name of the proxy as the parameter value\. 

1.  To see the details of the RDS DB instance or Aurora DB cluster associated with the returned target group, run [describe\-db\-proxy\-targets](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-proxy-targets.html)\. 

#### RDS API<a name="rds-proxy-viewing.api"></a>

 To view your proxies using the RDS API, use the [DescribeDBProxies](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBProxies.html) operation\. It returns values of the [DBProxy](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DBProxy.html) data type\. 

 To see details of the connection settings for the proxy, use the proxy identifiers from this return value with the [DescribeDBProxyTargetGroups](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBProxyTargetGroups.html) operation\. It returns values of the [DBProxyTargetGroup](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DBProxyTargetGroup.html) data type\. 

 To see the RDS instance or Aurora DB cluster associated with the proxy, use the [DescribeDBProxyTargets](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBProxyTargets.html) operation\. It returns values of the [DBProxyTarget](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DBProxyTarget.html) data type\. 

## Connecting to a Database Through RDS Proxy<a name="rds-proxy-connecting"></a>

 You connect to an RDS DB instance or Aurora DB cluster through a proxy in generally the same way as you connect directly to the database\. The main difference is that you specify the proxy endpoint instead of the instance or cluster endpoint\. For an Aurora DB cluster, all proxy connections have read/write capability and use the writer instance\. If you use the reader endpoint for read\-only connections, you continue using the reader endpoint the same way\. 

**Topics**
+ [Connecting to a Proxy Using Native Authentication](#rds-proxy-connecting-native)
+ [Connecting to a Proxy Using IAM Authentication](#rds-proxy-connecting-iam)
+ [Considerations for Connecting to a Proxy with PostgreSQL](#rds-proxy-connecting-postgresql)

### Connecting to a Proxy Using Native Authentication<a name="rds-proxy-connecting-native"></a>

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

### Connecting to a Proxy Using IAM Authentication<a name="rds-proxy-connecting-iam"></a>

 When you use IAM authentication with RDS Proxy, set up your database users to authenticate with regular user names and passwords\. The IAM authentication applies to RDS Proxy retrieving the user name and password credentials from Secrets Manager\. The connection from RDS Proxy to the underlying database doesn't go through IAM\. 

 To connect to RDS Proxy using IAM authentication, follow the same general procedure as for connecting to an RDS DB instance or Aurora cluster using IAM authentication\. For general information about using IAM with RDS and Aurora, see [Security in Amazon RDS](UsingWithRDS.md)\. 

 The major differences in IAM usage for RDS Proxy include the following: 
+  You don't configure each individual database user with an authorization plugin\. The database users still have regular user names and passwords within the database\. You set up Secrets Manager secrets containing these user names and passwords, and authorize RDS Proxy to retrieve the credentials from Secrets Manager\. 
**Important**  
 The IAM authentication applies to the connection between your client program and the proxy\. The proxy then authenticates to the database using the user name and password credentials retrieved from Secrets Manager\. When you use IAM for the connection to a proxy, make sure that the underlying RDS DB instance or Aurora DB cluster doesn't have IAM enabled\. 
+  Instead of the instance, cluster, or reader endpoint, you specify the proxy endpoint\. For details about the proxy endpoint, see [Connecting to Your DB Instance Using IAM Authentication](UsingWithRDS.IAMDBAuth.Connecting.md)\. 
+  In the direct DB IAM auth case, you selectively pick database users and configure them to be identified with a special auth plugin\. You can then connect to those users using IAM auth\. 

   In the proxy use case, you need to provide the proxy with Secrets that contain some user's username and password \(native auth\)\. You then connect to the proxy using IAM auth \(by generating an auth token with the proxy endpoint, not the database endpoint\) and using a username which matches one of the usernames for the secrets you previously provided\. 
+  Make sure that you use Transport Layer Security \(TLS\) / Secure Sockets Layer \(SSL\) when connecting to a proxy using IAM authentication\. 

 You can grant a specific user access to the proxy by modifying the IAM policy\. An example follows\. 

```
"Resource": "arn:aws:rds-db:us-east-2:1234567890:dbuser:prx-ABCDEFGHIJKL01234/db_user”
```

### Considerations for Connecting to a Proxy with PostgreSQL<a name="rds-proxy-connecting-postgresql"></a>

 For PostgreSQL, when a client starts a connection to a PostgreSQL database, it sends a startup message that includes pairs of parameter name and value strings\. For details, see the `StartupMessage` in [PostgreSQL Message Formats](https://www.postgresql.org/docs/current/protocol-message-formats.html) in the PostgreSQL documentation\. 

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

 For more information about PostgreSQL messaging, see the [Frontend/Backend Protocol](https://www.postgresql.org/docs/current/protocol.html) in the PostgreSQL documentation\.

 For PostgreSQL, if you use JDBC we recommend the following to avoid pinning:
+ Set the JDBC connection parameter `assumeMinServerVersion` to at least `9.0` to avoid pinning\. Doing this prevents the JDBC driver from performing an extra round trip during connection startup when it runs `SET extra_float_digits = 3`\. 
+ Set the JDBC connection parameter `ApplicationName` to `any/your-application-name` to avoid pinning\. Doing this prevents the JDBC driver from performing an extra round trip during connection startup when it runs `SET application_name = ”PostgreSQL JDBC Driver“`\. Note the JDBC parameter is `ApplicationName` but the PostgreSQL `StartupMessage` parameter is `application_name`\.
+ Set the JDBC connection parameter `preferQueryMode` to `extendedForPrepared` to avoid pinning\. The `extendedForPrepared` ensures that the extended mode is used only for prepared statements\. 

  The default for the `preferQueryMode` parameter is `extended`, which uses the extended mode for all queries\. The extended mode uses a series of `Prepare`, `Bind`, `Execute`, and `Sync` requests and corresponding responses\. This type of series causes connection pinning in an RDS proxy\. 

For more information, see [Avoiding Pinning](#rds-proxy-pinning)\. For more information about connecting using JDBC, see [Connecting to the Database](https://jdbc.postgresql.org/documentation/head/connect.html) in the PostgreSQL documentation\.

## Managing an RDS Proxy<a name="rds-proxy-managing"></a>

 Following, you can find an explanation of how to manage RDS proxy operation and configuration\. These procedures help your application make the most efficient use of database connections and achieve maximum connection reuse\. The more that you can take advantage of connection reuse, the more CPU and memory overhead that you can save\. This in turn reduces latency for your application and enables the database to devote more of its resources to processing application requests\. 

**Topics**
+ [Modifying an RDS Proxy](#rds-proxy-modifying-proxy)
+ [Adding a New Database User](#rds-proxy-new-db-user)
+ [Changing the Password for a Database User](#rds-proxy-changing-db-user-password)
+ [Controlling Connection Limits and Timeouts](#rds-proxy-connection-limits)
+ [Managing and Monitoring Connection Pooling](#rds-proxy-connection-pooling-tuning)
+ [Avoiding Pinning](#rds-proxy-pinning)
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

 The following example shows how to first check the `MaxConnectionsPercent` setting for a proxy and then change it, using the target group\. 

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

### Adding a New Database User<a name="rds-proxy-new-db-user"></a>

 In some cases, you might add a new database user to an RDS DB instance or Aurora cluster that's associated with a proxy\. If so, add or repurpose a Secrets Manager secret to store the credentials for that user\. To do this, choose one of the following options: 
+  Create a new Secrets Manager secret, using the procedure described in [Setting Up Database Credentials in AWS Secrets Manager](#rds-proxy-secrets-arns)\. 
+  Update the IAM role to give RDS Proxy access to the new Secrets Manager secret\. To do so, update the resources section of the IAM role policy\. 
+  If the new user takes the place of an existing one, update the credentials stored in the proxy's Secrets Manager secret for the existing user\. 

### Changing the Password for a Database User<a name="rds-proxy-changing-db-user-password"></a>

 In some cases, you might change the password for a database user in an RDS DB instance or Aurora cluster that's associated with a proxy\. If so, update the corresponding Secrets Manager secret with the new password\. 

### Controlling Connection Limits and Timeouts<a name="rds-proxy-connection-limits"></a>

 RDS Proxy uses the `max_connections` setting for your RDS DB instance or Aurora DB cluster\. This setting represents the overall upper limit on the connections that the proxy can open at any one time\. In Aurora clusters and RDS Multi\-AZ configurations, the `max_connections` value that the proxy uses is the one for the Aurora primary instance or the RDS writer instance\. 

 To set this value for your RDS DB instance or Aurora DB cluster, follow the procedures in [Working with DB Parameter Groups](USER_WorkingWithParamGroups.md)\. These procedures demonstrate how to associate a parameter group with your database and edit the `max_connections` value in the parameter group\. 

 The proxy setting for maximum connections represents a percentage of the `max_connections` value for the database that's associated with the proxy\. If you have multiple applications all using the same database, you can effectively divide their connection quotas by using a proxy for each application with a specific percentage of `max_connections`\. If you do so, ensure that the percentages add up to 100 or less for all proxies associated with the same database\. 

 RDS Proxy periodically disconnects idle connections and returns them to the connection pool\. You can adjust this timeout interval\. Doing so helps your applications to deal with stale resources, especially if the application mistakenly leaves a connection open while holding important database resources\. 

### Managing and Monitoring Connection Pooling<a name="rds-proxy-connection-pooling-tuning"></a>

 As described in [Connection Pooling](#rds-proxy-connection-pooling), connection pooling is a crucial RDS Proxy feature\. Following, you can learn how to make the most efficient use of connection pooling and transaction\-level connection reuse \(multiplexing\)\. 

 Because the connection pool is managed by RDS Proxy, you can monitor it and adjust connection limits and timeout intervals without changing your application code\. 

 For each proxy, you can specify an upper limit on the number of connections used by the connection pool\. You specify the limit as a percentage\. This percentage applies to the maximum connections configured in the database\. The exact number varies depending on the DB instance size and configuration settings\. 

 For example, suppose that you configured RDS Proxy to use 75 percent of the maximum connections for the database\. For MySQL, the maximum value is defined by the `max_connections` configuration parameter\. In this case, the other 25 percent of maximum connections remain available to assign to other proxies or for connections that don't go through a proxy\. In some cases, the proxy might keep less than 75 percent of the maximum connections open at a particular time\. Those cases might include situations where the database doesn't have many simultaneous connections, or some connections stay idle for long periods\. 

 The overall number of connections available for the connection pool changes as you update the `max_connections` configuration setting that applies to an RDS DB instance or an Aurora cluster\. 

 The proxy doesn't reserve all of these connections in advance\. Thus, you can specify a relatively large percentage, and those connections are only opened when the proxy becomes busy enough to need them\. 

 You can choose how long to wait for a connection to become available for use by your application\. This setting is represented by the **Connection borrow timeout** option when you create a proxy\. This setting specifies how long to wait for a connection to become available in the connection pool before returning a timeout error\. It applies when the number of connections is at the maximum, and so no connections are available in the connection pool\. It also applies if no writer instance is available because a failover operation is in process\. Using this setting, you can set the best wait period for your application without having to change the query timeout in your application code\. 

### Avoiding Pinning<a name="rds-proxy-pinning"></a>

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
+  See how frequently pinning occurs by monitoring the CloudWatch metric `DatabaseConnectionsCurrentlySessionPinned`\. For information about this and other CloudWatch metrics, see [Monitoring RDS Proxy Using Amazon CloudWatchMonitoring RDS Proxy](#rds-proxy.monitoring)\. 
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
+  Manipulating sequences using functions such as `nextval()` and `setval()` 
+  Interacting with locks using functions such as `pg_advisory_lock()` and `pg_try_advisory_lock()` 
+  Using prepared statements, setting parameters, or resetting a parameter to its default 

 If you have expert knowledge about your application behavior, you can skip the pinning behavior for certain application statements\. To do so, choose the **Session pinning filters** option when creating the proxy\. Currently, you can opt out of session pinning for setting session variables and configuration settings\. 

 For metrics about how often pinning occurs for a proxy, see [Monitoring RDS Proxy Using Amazon CloudWatchMonitoring RDS Proxy](#rds-proxy.monitoring)\. 

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

## Monitoring RDS Proxy Using Amazon CloudWatch<a name="rds-proxy.monitoring"></a>

You can monitor RDS Proxy by using Amazon CloudWatch\. CloudWatch collects and processes raw data from the proxies into readable, near\-real\-time metrics\. To find these metrics in the CloudWatch console, choose **Metrics**, then choose **RDS**, and choose **Per\-Proxy Metrics**\. For more information, see [ Using Amazon CloudWatch Metrics](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/working_with_metrics.html) in the Amazon CloudWatch User Guide\.

**Note**  
 RDS publishes these metrics for each underlying Amazon EC2 instance associated with a proxy\. A single proxy might be served by more than one EC2 instance\. Use CloudWatch statistics to aggregate the values for a proxy across all the associated instances\.   
 Some of these metrics might not be visible until after the first successful connection by a proxy\. 

 All RDS Proxy metrics are in the group `proxy`\.


|  Metric  |  Description  |  Valid Period  | CloudWatch Dimensions | 
| --- | --- | --- | --- | 
|  `AvailabilityPercentage`  |  The percentage of time for which the target group was available in the role indicated by the dimension\. This metric is reported every minute\. The most useful statistic for this metric is `Average`\.  | 1 minute | ProxyName, TargetGroup, TargetRole | 
|  ClientConnections  |   The current number of client connections\. This metric is reported every minute\. The most useful statistic for this metric is `Sum`\.   |   1 minute   | ProxyName | 
|  ClientConnectionsClosed  |   The number of client connections closed\. The most useful statistic for this metric is `Sum`\.   |   1 minute and above   | ProxyName | 
|  `ClientConnectionsNoTLS`  | The current number of client connections without Transport Layer Security \(TLS\)\. This metric is reported every minute\. The most useful statistic for this metric is Sum\.  | 1 minute and above | ProxyName | 
|   `ClientConnectionsReceived`   |   The number of client connection requests received\. The most useful statistic for this metric is `Sum`\.   |   1 minute and above   | ProxyName | 
|  ClientConnectionsSetupFailedAuth  |   The number of client connection attempts that failed due to misconfigured authentication or TLS\. The most useful statistic for this metric is `Sum`\.   |   1 minute and above   | ProxyName | 
|  ClientConnectionsSetupSucceeded  |   The number of client connections successfully established with any authentication mechanism with or without TLS\. The most useful statistic for this metric is `Sum`\.   |   1 minute and above   | ProxyName | 
| ClientConnectionsTLS | The current number of client connections with TLS\. This metric is reported every minute\. The most useful statistic for this metric is Sum\. | 1 minute and above | ProxyName | 
|  DatabaseConnectionRequests  |   The number of requests to create a database connection\. The most useful statistic for this metric is `Sum`\.   |   1 minute and above   | ProxyName, TargetGroup, Target | 
|  `DatabaseConnectionRequestsWithTLS`  | The number of requests to create a database connection with TLS\. The most useful statistic for this metric is Sum\.  | 1 minute and above | ProxyName, TargetGroup, Target | 
|  DatabaseConnections  |   The current number of database connections\. This metric is reported every minute\. The most useful statistic for this metric is `Sum`\.   |   1 minute   | ProxyName, TargetGroup, Target | 
|  `DatabaseConnectionsBorrowLatency`  | The time in microseconds that it takes for the proxy being monitored to get a database connection\. The most useful statistic for this metric is Average\.  | 1 minute and above | ProxyName | 
|  DatabaseConnectionsCurrentlyBorrowed  |  The current number of database connections in the borrow state\. This metric is reported every minute\. The most useful statistic for this metric is `Sum`\.   |   1 minute   | ProxyName, TargetGroup, Target | 
| DatabaseConnectionsCurrentlyInTransaction  |   The current number of database connections in a transaction\. This metric is reported every minute\. The most useful statistic for this metric is `Sum`\.  |   1 minute   | ProxyName, TargetGroup, Target | 
| DatabaseConnectionsCurrentlySessionPinned  |   The current number of database connections currently pinned because of operations in client requests that change session state\. This metric is reported every minute\. The most useful statistic for this metric is `Sum`\.   |   1 minute   | ProxyName, TargetGroup, Target | 
|  DatabaseConnectionsSetupFailed  |   The number of database connection requests that failed\. The most useful statistic for this metric is `Sum`\.   |   1 minute and above   | ProxyName, TargetGroup, Target | 
|  DatabaseConnectionsSetupSucceeded  |   The number of database connections successfully established with or without TLS\. The most useful statistic for this metric is `Sum`\.   |   1 minute and above   | ProxyName, TargetGroup, Target | 
|  `DatabaseConnectionsWithTLS`  | The current number of database connections with TLS\. This metric is reported every minute\. The most useful statistic for this metric is Sum\.  | 1 minute | ProxyName, TargetGroup, Target | 
|  MaxDatabaseConnectionsAllowed  |   The maximum number of database connections allowed\. This metric is reported every minute\. The most useful statistic for this metric is `Sum`\.   |   1 minute   | ProxyName, TargetGroup, Target | 
|  `QueryDatabaseResponseLatency`  | The time in microseconds that the database took to respond to the query\. The most useful statistic for this metric is Average\.  | 1 minute and above | ProxyName, TargetGroup, Target | 
|  QueryRequests  |   The number of queries received\. A query including multiple statements is counted as one query\. The most useful statistic for this metric is `Sum`\.   |   1 minute and above   | ProxyName | 
| QueryRequestsNoTLS | The number of queries received from non\-TLS connections\. A query including multiple statements is counted as one query\. The most useful statistic for this metric is Sum\.  | 1 minute and above | ProxyName | 
|  `QueryRequestsTLS`  | The number of queries received from TLS connections\. A query including multiple statements is counted as one query\. The most useful statistic for this metric is Sum\.  | 1 minute and above | ProxyName | 
| QueryResponseLatency | The time in microseconds between getting a query request and the proxy responding to it\. The most useful statistic for this metric is Average\.  | 1 minute and above | ProxyName | 

## Command\-Line Examples for RDS Proxy<a name="rds-proxy.examples"></a>

 To see how combinations of connection commands and SQL statements interact with RDS Proxy, look at the following examples\. 

**Examples**
+  [Preserving Connections to a MySQL Database Across a Failover](#example-mysql-preserve-connections) 
+  [Adjusting the max_connections Setting for an Aurora DB Cluster](#example-adjust-cluster-max-connections) 

**Example Preserving Connections to a MySQL Database Across a Failover**  
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
$ aws rds failover-db-cluster --db-cluster-id cluster-56-2019-11-14-1399
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
$ aws rds failover-db-cluster --db-cluster-id cluster-56-2019-11-14-1399
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

**Example Adjusting the max\_connections Setting for an Aurora DB Cluster**  
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

**Topics**
+ [Common Issues and Solutions](#rds-proxy-diagnosis)
+ [Working with CloudWatch Logs for RDS Proxy](#rds-proxy-logs)
+ [Verifying Connectivity for a Proxy](#rds-proxy-verifying)

### Common Issues and Solutions<a name="rds-proxy-diagnosis"></a>

 For possible causes and solutions to some common problems that you might encounter using RDS Proxy, see the following\. 

 You might encounter the following issues while creating a new proxy or connecting to a proxy\. 


|  Error  |  Causes or Workarounds  | 
| --- | --- | 
|   `403: The security token included in the request is invalid`   |  Select an existing IAM role instead of choosing to create a new one\.  | 

 You might encounter the following issues while connecting to a MySQL proxy\. 


|  Error  |  Causes or Workarounds  | 
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
|   `IAM authentication is allowed only with SSL connections.`   |   The user tried to connect to the database using IAM authentication with the setting `sslmode=disable` in the PostgreSQL client\.   |   The user needs to connect to the database using the minimum setting of `sslmode=require` in the PostgreSQL client\. For more information, see the [PostgreSQL SSL Support](https://www.postgresql.org/docs/current/libpq-ssl.html) documentation\.   | 
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

### Working with CloudWatch Logs for RDS Proxy<a name="rds-proxy-logs"></a>

 You can find logs of RDS Proxy activity under CloudWatch in the AWS Management Console\. Each proxy has an entry in the **Log groups** page\. 

**Important**  
 These logs are intended for human consumption for troubleshooting purposes and not for programmatic access\. The format and content of the logs is subject to change\. 

### Verifying Connectivity for a Proxy<a name="rds-proxy-verifying"></a>

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
     DbProxyName: CanaryProxy
     TargetGroupName: default
     InstanceIdentifiers: 
       - Fn::ImportValue: DBInstanceName
   DependsOn: DBProxy
```

 For more information about the Amazon RDS and Aurora resources that you can create using AWS CloudFormation, see [RDS Resource Type Reference](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/AWS_RDS.html)\. 