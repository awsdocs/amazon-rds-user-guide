# Troubleshooting for RDS Proxy<a name="rds-proxy.troubleshooting"></a>

 Following, you can find troubleshooting ideas for some common RDS Proxy issues and information on CloudWatch logs for RDS Proxy\. 

 In the RDS Proxy logs, each entry is prefixed with the name of the associated proxy endpoint\. This name can be the name that you specified for a user\-defined endpoint\. Or it can be the special name `default` for read/write requests using the default endpoint of a proxy\. For more information about proxy endpoints, see [Working with Amazon RDS Proxy endpoints](rds-proxy-endpoints.md)\. 

**Topics**
+ [Verifying connectivity for a proxy](#rds-proxy-verifying)
+ [Common issues and solutions](#rds-proxy-diagnosis)

## Verifying connectivity for a proxy<a name="rds-proxy-verifying"></a>

 You can use the following commands to verify that all components of the connection mechanism can communicate with the other components\. 

 Examine the proxy itself using the [describe\-db\-proxies](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-proxies.html) command\. Also examine the associated target group using the [describe\-db\-proxy\-target\-groups](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-proxy-target-groups.html) command\. Check that the details of the targets match the RDS DB instance or Aurora DB cluster that you intend to associate with the proxy\. Use commands such as the following\. 

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

## Common issues and solutions<a name="rds-proxy-diagnosis"></a>

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
|   `ERROR 1045 (28000): Access denied for user 'DB_USER'@'%' (using password: YES)`   |   Some possible reasons include the following:  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/rds-proxy.troubleshooting.html)  | 
|  ERROR 1105 \(HY000\): Unknown error  |  An unknown error occurred\.  | 
|  ERROR 1231 \(42000\): Variable ''character\_set\_client'' can't be set to the value of value  |   The value set for the `character_set_client` parameter is not valid\. For example, the value `ucs2` is not valid because it can crash the MySQL server\.   | 
|  ERROR 3159 \(HY000\): This RDS Proxy requires TLS connections\.  |   You enabled the setting **Require Transport Layer Security** in the proxy but your connection included the parameter `ssl-mode=DISABLED` in the MySQL client\. Do either of the following:  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/rds-proxy.troubleshooting.html)  | 
|  ERROR 2026 \(HY000\): SSL connection error: Internal Server Error  |   The TLS handshake to the proxy failed\. Some possible reasons include the following:  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/rds-proxy.troubleshooting.html)  | 
|  ERROR 9501 \(HY000\): Timed\-out waiting to acquire database connection  |   The proxy timed\-out waiting to acquire a database connection\. Some possible reasons include the following:  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/rds-proxy.troubleshooting.html)  | 

 You might encounter the following issues while connecting to a PostgreSQL proxy\. 


|  Error  |  Cause  |  Solution  | 
| --- | --- | --- | 
|   `IAM authentication is allowed only with SSL connections.`   |   The user tried to connect to the database using IAM authentication with the setting `sslmode=disable` in the PostgreSQL client\.   |   The user needs to connect to the database using the minimum setting of `sslmode=require` in the PostgreSQL client\. For more information, see the [PostgreSQL SSL support](https://www.postgresql.org/docs/current/libpq-ssl.html) documentation\.   | 
|   `This RDS Proxy requires TLS connections.`   |   The user enabled the option **Require Transport Layer Security** but tried to connect with `sslmode=disable` in the PostgreSQL client\.   |   To fix this error, do one of the following:  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/rds-proxy.troubleshooting.html)  | 
|   `IAM authentication failed for user user_name. Check the IAM token for this user and try again.`   |   This error might be due to the following reasons:  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/rds-proxy.troubleshooting.html)  |   To fix this error, do the following:  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/rds-proxy.troubleshooting.html)  | 
|   `This RDS proxy has no credentials for the role role_name. Check the credentials for this role and try again.`   |   There is no Secrets Manager secret for this role\.   |   Add a Secrets Manager secret for this role\.   | 
|   `RDS supports only IAM or MD5 authentication.`   |   The database client being used to connect to the proxy is using an authentication mechanism not currently supported by the proxy\.   |   If you're not using IAM authentication, use the MD5 password authentication only\.   | 
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
|   `Unknown error.`   |   An unknown error occurred\.   |   Reach out to AWS Support to investigate the issue\.   | 
|   `Timed-out waiting to acquire database connection.`   |   The proxy timed\-out waiting to acquire a database connection\. Some possible reasons include the following:  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/rds-proxy.troubleshooting.html)  |   Possible solutions are the following:  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/rds-proxy.troubleshooting.html)  | 
|   `Request returned an error: database_error.`   |   The database connection established from the proxy returned an error\.   |   The solution depends on the specific database error\. One example is: `Request returned an error: database "your-database-name" does not exist`\. This means that the specified database name doesn't exist on the database server\. Or it means that the user name used as a database name \(if a database name isn't specified\) doesn't exist on the server\.   | 