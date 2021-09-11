# Planning for and setting up RDS Proxy<a name="rds-proxy-setup"></a>

 In the following sections, you can find how to set up RDS Proxy\. You can also find how to set the related security options that control who can access each proxy and how each proxy connects to DB instances\. 

**Topics**
+ [Limitations for RDS Proxy](#rds-proxy.limits)
+ [Identifying DB instances, clusters, and applications to use with RDS Proxy](#rds-proxy-planning)
+ [Setting up network prerequisites](#rds-proxy-network-prereqs)
+ [Setting up database credentials in AWS Secrets Manager](#rds-proxy-secrets-arns)
+ [Setting up AWS Identity and Access Management \(IAM\) policies](#rds-proxy-iam-setup)
+ [Creating an RDS Proxy](#rds-proxy-creating)
+ [Viewing an RDS Proxy](#rds-proxy-viewing)

## Limitations for RDS Proxy<a name="rds-proxy.limits"></a>

 The following limitations apply to RDS Proxy: 
+  RDS Proxy is available only in certain AWS Regions\. For more information, see [Amazon RDS Proxy](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Concepts.AuroraFeaturesRegionsDBEngines.grids.html#Concepts.Aurora_Fea_Regions_DB-eng.Feature.RDS_Proxy)\. 

   You can have up to 20 proxies for each AWS account ID\. If your application requires more proxies, you can request additional proxies by opening a ticket with the AWS Support organization\.  
+  Each proxy can have up to 200 associated Secrets Manager secrets\. Thus, each proxy can connect to with up to 200 different user accounts at any given time\. 
+  You can create, view, modify, and delete up to 20 endpoints for each proxy\. These endpoints are in addition to the default endpoint that's automatically created for each proxy\. 
+  In an Aurora cluster, all of the connections using the default proxy endpoint are handled by the Aurora writer instance\. To perform load balancing for read\-intensive workloads, you can create a read\-only endpoint for a proxy\. That endpoint passes connections to the reader endpoint of the cluster\. That way, your proxy connections can take advantage of Aurora read scalability\. For more information, see [Overview of proxy endpoints](rds-proxy-endpoints.md#rds-proxy-endpoints-overview)\. 

  For RDS DB instances in replication configurations, you can associate a proxy only with the writer DB instance, not a read replica\.
+ You can't use RDS Proxy with Aurora Serverless clusters\.
+ Using RDS Proxy with Aurora clusters that are part of an Aurora global database isn't currently supported\.
+  Your RDS Proxy must be in the same VPC as the database\. The proxy can't be publicly accessible, although the database can be\. 
**Note**  
 For Aurora DB clusters, you can enable cross\-VPC access by creating an additional endpoint for a proxy and specifying a different VPC, subnets, and security groups with that endpoint\. For more information, see [Accessing Aurora and RDS databases across VPCs](rds-proxy-endpoints.md#rds-proxy-cross-vpc)\. 
+  You can't use RDS Proxy with a VPC that has its tenancy set to `dedicated`\. 
+  If you use RDS Proxy with an RDS DB instance or Aurora DB cluster that has IAM authentication enabled, make sure that all users who connect through a proxy authenticate through user names and passwords\. See [Setting up AWS Identity and Access Management \(IAM\) policies](#rds-proxy-iam-setup) for details about IAM support in RDS Proxy\. 
+  You can't use RDS Proxy with custom DNS\. 
+  RDS Proxy is available for the MySQL and PostgreSQL engine families\. 
+  Each proxy can be associated with a single target DB instance or cluster\. However, you can associate multiple proxies with the same DB instance or cluster\. 

 The following RDS Proxy prerequisites and limitations apply to MySQL: 
+  For RDS for MySQL, RDS Proxy supports MySQL 5\.6 and 5\.7\. For Aurora MySQL, RDS Proxy supports version 1 \(compatible with MySQL 5\.6\) and version 2 \(compatible with MySQL 5\.7\)\. 
+  Currently, all proxies listen on port 3306 for MySQL\. The proxies still connect to your database using the port that you specified in the database settings\. 
+  You can't use RDS Proxy with RDS for MySQL 8\.0\.
+  You can't use RDS Proxy with self\-managed MySQL databases in EC2 instances\.
+  You can't use RDS Proxy with an RDS for MySQL DB instance that has the `read_only` parameter in its DB parameter group set to `1`\.
+  Proxies don't support MySQL compressed mode\. For example, they don't support the compression used by the `--compress` or `-C` options of the `mysql` command\.
+  Some SQL statements and functions can change the connection state without causing pinning\. For the most current pinning behavior, see [Avoiding pinning](rds-proxy-managing.md#rds-proxy-pinning)\.

 The following RDS Proxy prerequisites and limitations apply to PostgreSQL:
+  For RDS PostgreSQL, RDS Proxy supports version 10\.10 and higher minor versions, and version 11\.5 and higher minor versions\. For Aurora PostgreSQL, RDS Proxy supports version 10\.11 and higher minor versions, and 11\.6 and higher minor versions\.
+  Currently, all proxies listen on port 5432 for PostgreSQL\.
+  Query cancellation isn't supported for PostgreSQL\.
+  The results of the PostgreSQL function [lastval](https://www.postgresql.org/docs/current/functions-sequence.html) aren't always accurate\. As a work\-around, use the [INSERT](https://www.postgresql.org/docs/current/sql-insert.html) statement with the `RETURNING` clause\.

## Identifying DB instances, clusters, and applications to use with RDS Proxy<a name="rds-proxy-planning"></a>

 You can determine which of your DB instances, clusters, and applications might benefit the most from using RDS Proxy\. To do so, consider these factors: 
+  RDS Proxy is highly available and deployed over multiple Availability Zones \(AZs\)\. To ensure overall high availability for your database, deploy your Amazon RDS DB instance or Aurora cluster in a Multi\-AZ configuration\. 
+  Any DB instance or cluster that encounters "too many connections" errors is a good candidate for associating with a proxy\. The proxy enables applications to open many client connections, while the proxy manages a smaller number of long\-lived connections to the DB instance or cluster\. 
+  For DB instances or clusters that use smaller AWS instance classes, such as T2 or T3, using a proxy can help avoid out\-of\-memory conditions\. It can also help reduce the CPU overhead for establishing connections\. These conditions can occur when dealing with large numbers of connections\. 
+  You can monitor certain Amazon CloudWatch metrics to determine whether a DB instance or cluster is approaching certain types of limit\. These limits are for the number of connections and the memory associated with connection management\. You can also monitor certain CloudWatch metrics to determine whether a DB instance or cluster is handling many short\-lived connections\. Opening and closing such connections can impose performance overhead on your database\. For information about the metrics to monitor, see [Monitoring RDS Proxy using Amazon CloudWatchMonitoring RDS Proxy](rds-proxy.monitoring.md)\. 
+  AWS Lambda functions can also be good candidates for using a proxy\. These functions make frequent short database connections that benefit from connection pooling offered by RDS Proxy\. You can take advantage of any IAM authentication you already have for Lambda functions, instead of managing database credentials in your Lambda application code\. 
+  Applications that use languages and frameworks such as PHP and Ruby on Rails are typically good candidates for using a proxy\. Such applications typically open and close large numbers of database connections, and don't have built\-in connection pooling mechanisms\. 
+  Applications that keep a large number of connections open for long periods are typically good candidates for using a proxy\. Applications in industries such as software as a service \(SaaS\) or ecommerce often minimize the latency for database requests by leaving connections open\. With RDS Proxy, an application can keep more connections open than it can when connecting directly to the DB instance or cluster\. 
+  You might not have adopted IAM authentication and Secrets Manager due to the complexity of setting up such authentication for all DB instances and clusters\. If so, you can leave the existing authentication methods in place and delegate the authentication to a proxy\. The proxy can enforce the authentication policies for client connections for particular applications\. You can take advantage of any IAM authentication you already have for Lambda functions, instead of managing database credentials in your Lambda application code\. 

## Setting up network prerequisites<a name="rds-proxy-network-prereqs"></a>

 Using RDS Proxy requires you to have a common virtual private cloud \(VPC\) between your Aurora DB cluster or RDS DB instance and RDS Proxy\. This VPC should have a minimum of two subnets that are in different Availability Zones\. Your account can either own these subnets or share them with other accounts\. For information about VPC sharing, see [Work with shared VPCs](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-sharing.html)\. Your client application resources such as Amazon EC2, Lambda, or Amazon ECS can be in the same VPC or in a separate VPC from the proxy\. Note that if you've successfully connected to any RDS DB instances or Aurora DB clusters, you already have the required network resources\.

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
$ aws rds describe-db-instances --db-instance-identifier my_instance_id --query '*[].[DBSubnetGroup]|[0]|[0].VpcId' --output text
my_vpc_id

$ aws ec2 describe-subnets --filters Name=vpc-id,Values=my_vpc_id --query '*[].[SubnetId]' --output text
subnet_id_1
subnet_id_2
subnet_id_3
subnet_id_4
subnet_id_5
subnet_id_6
```

## Setting up database credentials in AWS Secrets Manager<a name="rds-proxy-secrets-arns"></a>

 For each proxy that you create, you first use the Secrets Manager service to store sets of user name and password credentials\. You create a separate Secrets Manager secret for each database user account that the proxy connects to on the RDS DB instance or Aurora DB cluster\. 

 In Secrets Manager, you create these secrets with values for the `username` and `password` fields\. Doing so allows the proxy to connect to the corresponding database users on whichever RDS DB instances or Aurora DB clusters that you associate with the proxy\. To do this, you can use the setting **Credentials for other database**, **Credentials for RDS database**, or **Other type of secrets**\. Fill in the appropriate values for the **User name** and **Password** fields, and placeholder values for any other required fields\. The proxy ignores other fields such as **Host** and **Port** if they're present in the secret\. Those details are automatically supplied by the proxy\. 

 You can also choose **Other type of secrets**\. In this case, you create the secret with keys named `username` and `password`\. 

 Because the secrets used by your proxy aren't tied to a specific database server, you can reuse a secret across multiple proxies if you use the same credentials across multiple database servers\. For example, you might use the same credentials across a group of development and test servers\. 

 To connect through the proxy as a specific user, make sure that the password associated with a secret matches the database password for that user\. If there's a mismatch, you can update the associated secret in Secrets Manager\. In this case, you can still connect to other accounts where the secret credentials and the database passwords do match\. 

 When you create a proxy through the AWS CLI or RDS API, you specify the Amazon Resource Names \(ARNs\) of the corresponding secrets for all the DB user accounts that the proxy can access\. In the AWS Management Console, you choose the secrets by their descriptive names\. 

 For instructions about creating secrets in Secrets Manager, see the [Creating a secret](https://docs.aws.amazon.com/secretsmanager/latest/userguide/manage_create-basic-secret.html) page in the Secrets Manager documentation\. Use one of the following techniques: 
+  Use [Secrets Manager](http://aws.amazon.com/secrets-manager/) in the console\. 
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

## Setting up AWS Identity and Access Management \(IAM\) policies<a name="rds-proxy-iam-setup"></a>

 After you create the secrets in Secrets Manager, you create an IAM policy that can access those secrets\. For general information about using IAM with RDS and Aurora, see [Identity and access management in Amazon RDS](UsingWithRDS.IAM.md)\. 

**Tip**  
 The following procedure applies if you use the IAM console\. If you use the AWS Management Console for RDS, RDS can create the IAM policy for you automatically\. In that case, you can skip the following procedure\. 

**To create an IAM policy that accesses your Secrets Manager secrets for use with your proxy**

1.  Sign in to the IAM console\. Follow the **Create role** process, as described in [Creating IAM roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create.html)\. Include the **Add Role to Database** step\. 

1.  For the new role, perform the **Add inline policy** step\. Use the same general procedures as in [Editing IAM policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_manage-edit.html)\. Paste the following JSON into the JSON text box\. Substitute your own account ID\. Substitute your AWS Region for `us-east-2`\. Substitute the Amazon Resource Names \(ARNs\) for the secrets that you created\. For the `kms:Decrypt` action, substitute the ARN of the default AWS KMS key or your own KMS key depending on which one you used to encrypt the Secrets Manager secrets\. 

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

## Creating an RDS Proxy<a name="rds-proxy-creating"></a>

 To manage connections for a specified set of DB instances, you can create a proxy\. You can associate a proxy with an RDS for MySQL DB instance, PostgreSQL DB instance, or an Aurora DB cluster\. 

### AWS Management Console<a name="rds-proxy-creating.console"></a>

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
   +  **Connection pool maximum connections**\. Specify a value from 1 through 100\. This setting represents the percentage of the `max_connections` value that RDS Proxy can use for its connections\. If you only intend to use one proxy with this DB instance or cluster, you can set this value to 100\. For details about how RDS Proxy uses this setting, see [Controlling connection limits and timeouts](rds-proxy-managing.md#rds-proxy-connection-limits)\. 
   +  **Session pinning filters**\. \(Optional\) This is an advanced setting, for troubleshooting performance issues with particular applications\. Currently, the only choice is `EXCLUDE_VARIABLE_SETS`\. Choose a filter only if both of following are true: Your application isn't reusing connections due to certain kinds of SQL statements, and you can verify that reusing connections with those SQL statements doesn't affect application correctness\. For more information, see [Avoiding pinning](rds-proxy-managing.md#rds-proxy-pinning)\. 
   +  **Connection borrow timeout**\. In some cases, you might expect the proxy to sometimes use all available database connections\. In such cases, you can specify how long the proxy waits for a database connection to become available before returning a timeout error\. You can specify a period up to a maximum of five minutes\. This setting only applies when the proxy has the maximum number of connections open and all connections are already in use\.
   +  **Initialization query**\. \(Optional\) You can specify one or more SQL statements for the proxy to run when opening each new database connection\. The setting is typically used with `SET` statements to make sure that each connection has identical settings such as time zone and character set\. For multiple statements, use semicolons as the separator\. You can also include multiple variables in a single `SET` statement, such as `SET x=1, y=2`\. Initialization query is not currently supported for PostgreSQL\.

    For **Connectivity**, provide information for the following: 
   +  **Secrets Manager ARNs**\. Choose at least one Secrets Manager secret that contains DB user credentials for the RDS DB instance or Aurora DB cluster that you intend to access with this proxy\. 
   +  **IAM role**\. Choose an IAM role that has permission to access the Secrets Manager secrets that you chose earlier\. You can also choose for the AWS Management Console to create a new IAM role for you and use that\. 
   +  **IAM Authentication**\. Choose whether to require or disallow IAM authentication for connections to your proxy\. The choice of IAM authentication or native database authentication applies to all DB users that access this proxy\. 
   +  **Subnets**\. This field is prepopulated with all the subnets associated with your VPC\. You can remove any subnets that you don't need for this proxy\. You must leave at least two subnets\. 

    Provide additional connectivity configuration: 
   +  **VPC security group**\. Choose an existing VPC security group\. You can also choose for the AWS Management Console to create a new security group for you and use that\. 
**Note**  
 This security group must allow access to the database the proxy connects to\. The same security group is used for ingress from your applications to the proxy, and for egress from the proxy to the database\. For example, suppose that you use the same security group for your database and your proxy\. In this case, make sure that you specify that resources in that security group can communicate with other resources in the same security group\.  
When using a shared VPC, you can't use the default security group for the VPC, or one that belongs to another account\. Choose a security group that belongs to your account\. If one doesn't exist, create one\. For more information about this limitation, see [Work with shared VPCs](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-sharing.html#vpc-share-limitations)\. 

    \(Optional\) Provide advanced configuration: 
   +  **Enable enhanced logging**\. You can enable this setting to troubleshoot proxy compatibility or performance issues\. 

      When this setting is enabled, RDS Proxy includes detailed information about SQL statements in its logs\. This information helps you to debug issues involving SQL behavior or the performance and scalability of the proxy connections\. The debug information includes the text of SQL statements that you submit through the proxy\. Thus, only enable this setting when needed for debugging, and only when you have security measures in place to safeguard any sensitive information that appears in the logs\. 

      To minimize overhead associated with your proxy, RDS Proxy automatically turns this setting off 24 hours after you enable it\. Enable it temporarily to troubleshoot a specific issue\. 

1.  Choose **Create Proxy**\. 

### AWS CLI<a name="rds-proxy-creating.CLI"></a>

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

**Note**  
The security group must allow access to the database the proxy connects to\. The same security group is used for ingress from your applications to the proxy, and for egress from the proxy to the database\. For example, suppose that you use the same security group for your database and your proxy\. In this case, make sure that you specify that resources in that security group can communicate with other resources in the same security group\.  
When using a shared VPC, you can't use the default security group for the VPC, or one that belongs to another account\. Choose a security group that belongs to your account\. If one doesn't exist, create one\. For more information about this limitation, see [Work with shared VPCs](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-sharing.html#vpc-share-limitations)\. 

 To create the required information and associations for the proxy, you also use the [register\-db\-proxy\-targets](https://docs.aws.amazon.com/cli/latest/reference/rds/register-db-proxy-targets.html) command\. Specify the target group name `default`\. RDS Proxy automatically creates a target group with this name when you create each proxy\. 

```
aws rds register-db-proxy-targets
    --db-proxy-name value
    [--target-group-name target_group_name]
    [--db-instance-identifiers space_separated_list]  # rds db instances, or
    [--db-cluster-identifiers cluster_id]        # rds db cluster (all instances), or
    [--db-cluster-endpoint endpoint_name]          # rds db cluster endpoint (all instances)
```

### RDS API<a name="rds-proxy-creating.API"></a>

 To create an RDS proxy, call the Amazon RDS API operation [CreateDBProxy](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBProxy.html)\. You pass a parameter with the [AuthConfig](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_AuthConfig.html) data structure\. 

  RDS Proxy automatically creates a target group named `default` when you create each proxy\. You associate an RDS DB instance or Aurora DB cluster with the target group by calling the function [RegisterDBProxyTargets](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RegisterDBProxyTargets.html)\. 

## Viewing an RDS Proxy<a name="rds-proxy-viewing"></a>

 After you create one or more RDS proxies, you can view them all to examine their configuration details and choose which ones to modify, delete, and so on\. 

 Any database applications that use the proxy require the proxy endpoint to use in the connection string\. 

### AWS Management Console<a name="rds-proxy-viewing.console"></a>

**To view your proxy**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1.  In the upper\-right corner of the AWS Management Console, choose the AWS Region in which you created the RDS Proxy\. 

1.  In the navigation pane, choose **Proxies**\. 

1.  Choose the name of an RDS proxy to display its details\. 

1.  On the details page, the **Target groups** section shows how the proxy is associated with a specific RDS DB instance or Aurora DB cluster\. You can follow the link to the **default** target group page to see more details about the association between the proxy and the database\. This page is where you see settings that you specified when creating the proxy, such as maximum connection percentage, connection borrow timeout, engine compatibility, and session pinning filters\. 

### CLI<a name="rds-proxy-viewing.cli"></a>

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

### RDS API<a name="rds-proxy-viewing.api"></a>

 To view your proxies using the RDS API, use the [DescribeDBProxies](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBProxies.html) operation\. It returns values of the [DBProxy](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DBProxy.html) data type\. 

 To see details of the connection settings for the proxy, use the proxy identifiers from this return value with the [DescribeDBProxyTargetGroups](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBProxyTargetGroups.html) operation\. It returns values of the [DBProxyTargetGroup](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DBProxyTargetGroup.html) data type\. 

 To see the RDS instance or Aurora DB cluster associated with the proxy, use the [DescribeDBProxyTargets](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBProxyTargets.html) operation\. It returns values of the [DBProxyTarget](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DBProxyTarget.html) data type\. 