# Managing an RDS Proxy<a name="rds-proxy-managing"></a>

 Following, you can find an explanation of how to manage RDS proxy operation and configuration\. These procedures help your application make the most efficient use of database connections and achieve maximum connection reuse\. The more that you can take advantage of connection reuse, the more CPU and memory overhead that you can save\. This in turn reduces latency for your application and enables the database to devote more of its resources to processing application requests\. 

**Topics**
+ [Modifying an RDS Proxy](#rds-proxy-modifying-proxy)
+ [Adding a new database user](#rds-proxy-new-db-user)
+ [Changing the password for a database user](#rds-proxy-changing-db-user-password)
+ [Controlling connection limits and timeouts](#rds-proxy-connection-limits)
+ [Managing and monitoring connection pooling](#rds-proxy-connection-pooling-tuning)
+ [Avoiding pinning](#rds-proxy-pinning)
+ [Deleting an RDS Proxy](#rds-proxy-deleting)

## Modifying an RDS Proxy<a name="rds-proxy-modifying-proxy"></a>

 You can change certain settings associated with a proxy after you create the proxy\. You do so by modifying the proxy itself, its associated target group, or both\. Each proxy has an associated target group\. 

### AWS Management Console<a name="rds-proxy-modifying-proxy.console"></a>

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
   +  \(Optional\) Add an intialization query, or modify the current one\. You can specify one or more SQL statements for the proxy to run when opening each new database connection\. The setting is typically used with `SET` statements to make sure that each connection has identical settings such as time zone and character set\. For multiple statements, use semicolons as the separator\. You can also include multiple variables in a single `SET` statement, such as `SET x=1, y=2`\. Initialization query is not currently supported for PostgreSQL\.

    You can't change certain properties, such as the target group identifier and the database engine\.  

1.  Choose **Modify target group**\. 

### AWS CLI<a name="rds-proxy-modifying-proxy.cli"></a>

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

### RDS API<a name="rds-proxy-modifying-proxy.api"></a>

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

## Adding a new database user<a name="rds-proxy-new-db-user"></a>

 In some cases, you might add a new database user to an RDS DB instance or Aurora cluster that's associated with a proxy\. If so, add or repurpose a Secrets Manager secret to store the credentials for that user\. To do this, choose one of the following options: 
+  Create a new Secrets Manager secret, using the procedure described in [Setting up database credentials in AWS Secrets Manager](rds-proxy-setup.md#rds-proxy-secrets-arns)\. 
+  Update the IAM role to give RDS Proxy access to the new Secrets Manager secret\. To do so, update the resources section of the IAM role policy\. 
+  If the new user takes the place of an existing one, update the credentials stored in the proxy's Secrets Manager secret for the existing user\. 

## Changing the password for a database user<a name="rds-proxy-changing-db-user-password"></a>

 In some cases, you might change the password for a database user in an RDS DB instance or Aurora cluster that's associated with a proxy\. If so, update the corresponding Secrets Manager secret with the new password\. 

## Controlling connection limits and timeouts<a name="rds-proxy-connection-limits"></a>

 RDS Proxy uses the `max_connections` setting for your RDS DB instance or Aurora DB cluster\. This setting represents the overall upper limit on the connections that the proxy can open at any one time\. In Aurora clusters and RDS Multi\-AZ configurations, the `max_connections` value that the proxy uses is the one for the Aurora primary instance or the RDS writer instance\. 

 To set this value for your RDS DB instance or Aurora DB cluster, follow the procedures in [Working with DB parameter groups](USER_WorkingWithParamGroups.md)\. These procedures demonstrate how to associate a parameter group with your database and edit the `max_connections` value in the parameter group\. 

 The proxy setting for maximum connections represents a percentage of the `max_connections` value for the database that's associated with the proxy\. If you have multiple applications all using the same database, you can effectively divide their connection quotas by using a proxy for each application with a specific percentage of `max_connections`\. If you do so, ensure that the percentages add up to 100 or less for all proxies associated with the same database\. 

 RDS Proxy periodically disconnects idle connections and returns them to the connection pool\. You can adjust this timeout interval\. Doing so helps your applications to deal with stale resources, especially if the application mistakenly leaves a connection open while holding important database resources\. 

## Managing and monitoring connection pooling<a name="rds-proxy-connection-pooling-tuning"></a>

 As described in [Connection pooling](rds-proxy.howitworks.md#rds-proxy-connection-pooling), connection pooling is a crucial RDS Proxy feature\. Following, you can learn how to make the most efficient use of connection pooling and transaction\-level connection reuse \(multiplexing\)\. 

 Because the connection pool is managed by RDS Proxy, you can monitor it and adjust connection limits and timeout intervals without changing your application code\. 

 For each proxy, you can specify an upper limit on the number of connections used by the connection pool\. You specify the limit as a percentage\. This percentage applies to the maximum connections configured in the database\. The exact number varies depending on the DB instance size and configuration settings\. 

 For example, suppose that you configured RDS Proxy to use 75 percent of the maximum connections for the database\. For MySQL, the maximum value is defined by the `max_connections` configuration parameter\. In this case, the other 25 percent of maximum connections remain available to assign to other proxies or for connections that don't go through a proxy\. In some cases, the proxy might keep less than 75 percent of the maximum connections open at a particular time\. Those cases might include situations where the database doesn't have many simultaneous connections, or some connections stay idle for long periods\. 

 The overall number of connections available for the connection pool changes as you update the `max_connections` configuration setting that applies to an RDS DB instance or an Aurora cluster\. 

 The proxy doesn't reserve all of these connections in advance\. Thus, you can specify a relatively large percentage, and those connections are only opened when the proxy becomes busy enough to need them\. 

 You can choose how long to wait for a connection to become available for use by your application\. This setting is represented by the **Connection borrow timeout** option when you create a proxy\. This setting specifies how long to wait for a connection to become available in the connection pool before returning a timeout error\. It applies when the number of connections is at the maximum, and so no connections are available in the connection pool\. It also applies if no writer instance is available because a failover operation is in process\. Using this setting, you can set the best wait period for your application without having to change the query timeout in your application code\. 

## Avoiding pinning<a name="rds-proxy-pinning"></a>

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
+  See how frequently pinning occurs by monitoring the CloudWatch metric `DatabaseConnectionsCurrentlySessionPinned`\. For information about this and other CloudWatch metrics, see [Monitoring RDS Proxy using Amazon CloudWatchMonitoring RDS Proxy](rds-proxy.monitoring.md)\. 
+  If you use `SET` statements to perform identical initialization for each client connection, you can do so while preserving transaction\-level multiplexing\. In this case, you move the statements that set up the initial session state into the initialization query used by a proxy\. This property is a string containing one or more SQL statements, separated by semicolons\. 

   For example, you can define an initialization query for a proxy that sets certain configuration parameters\. Then, RDS Proxy applies those settings whenever it sets up a new connection for that proxy\. You can remove the corresponding `SET` statements from your application code, so that they don't interfere with transaction\-level multiplexing\. 
**Important**  
 For proxies associated with MySQL databases, don't set the configuration parameter `sql_auto_is_null` to `true` or a nonzero value in the initialization query\. Doing so might cause incorrect application behavior\. 

 The proxy pins the session to the current connection in the following situations where multiplexing might cause unexpected behavior: 
+  Any statement with a text size greater than 16 KB causes the proxy to pin the session\. 
+  Prepared statements cause the proxy to pin the session\. This rule applies whether the prepared statement uses SQL text or the binary protocol\. 
+  Explicit MySQL statements `LOCK TABLE`, `LOCK TABLES`, or `FLUSH TABLES WITH READ LOCK` cause the proxy to pin the session\. 
+  Setting a user variable or a system variable \(with some exceptions\) causes the proxy to pin the session\. If this situation reduces your connection reuse too much, you can choose for `SET` operations not to cause pinning\. For information about how to do so by setting the `SessionPinningFilters` property, see [Creating an RDS Proxy](rds-proxy-setup.md#rds-proxy-creating)\. 
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

 For metrics about how often pinning occurs for a proxy, see [Monitoring RDS Proxy using Amazon CloudWatchMonitoring RDS Proxy](rds-proxy.monitoring.md)\. 

## Deleting an RDS Proxy<a name="rds-proxy-deleting"></a>

 You can delete a proxy if you no longer need it\. You might delete a proxy because the application that was using it is no longer relevant\. Or you might delete a proxy if you take the DB instance or cluster associated with it out of service\. 

### AWS Management Console<a name="rds-proxy-deleting.console"></a>

**To delete a proxy**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1.  In the navigation pane, choose **Proxies**\. 

1.  Choose the proxy to delete from the list\. 

1.  Choose **Delete Proxy**\. 

### AWS CLI<a name="rds-proxy-deleting.CLI"></a>

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

### RDS API<a name="rds-proxy-deleting.API"></a>

 To delete a DB proxy, call the Amazon RDS API function [DeleteDBProxy](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DeleteDBProxy.html)\. To delete related items and associations, you also call the functions [DeleteDBProxyTargetGroup](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DeleteDBProxyTargetGroup.html) and [DeregisterDBProxyTargets](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DeregisterDBProxyTargets.html)\. 