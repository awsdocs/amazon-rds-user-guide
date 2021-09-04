# Connecting to a database through RDS Proxy<a name="rds-proxy-connecting"></a>

 You connect to an RDS DB instance or Aurora DB cluster through a proxy in generally the same way as you connect directly to the database\. The main difference is that you specify the proxy endpoint instead of the instance or cluster endpoint\. For an Aurora DB cluster, by default all proxy connections have read/write capability and use the writer instance\. If you normally use the reader endpoint for read\-only connections, you can create an additional read\-only endpoint for the proxy and use that endpoint the same way\. For more information, see [Overview of proxy endpoints](rds-proxy-endpoints.md#rds-proxy-endpoints-overview)\. 

**Topics**
+ [Connecting to a proxy using native authentication](#rds-proxy-connecting-native)
+ [Connecting to a proxy using IAM authentication](#rds-proxy-connecting-iam)
+ [Considerations for connecting to a proxy with PostgreSQL](#rds-proxy-connecting-postgresql)

## Connecting to a proxy using native authentication<a name="rds-proxy-connecting-native"></a>

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

## Connecting to a proxy using IAM authentication<a name="rds-proxy-connecting-iam"></a>

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

## Considerations for connecting to a proxy with PostgreSQL<a name="rds-proxy-connecting-postgresql"></a>

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

For more information, see [Avoiding pinning](rds-proxy-managing.md#rds-proxy-pinning)\. For more information about connecting using JDBC, see [Connecting to the database](https://jdbc.postgresql.org/documentation/head/connect.html) in the PostgreSQL documentation\.