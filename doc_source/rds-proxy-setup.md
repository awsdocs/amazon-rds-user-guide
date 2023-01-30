# Getting started with RDS Proxy<a name="rds-proxy-setup"></a>

 In the following sections, you can find how to set up RDS Proxy\. You can also find how to set related security options\. These control who can access each proxy and how each proxy connects to DB instances\. 

**Topics**
+ [Setting up network prerequisites](#rds-proxy-network-prereqs)
+ [Setting up database credentials in AWS Secrets Manager](#rds-proxy-secrets-arns)
+ [Setting up AWS Identity and Access Management \(IAM\) policies](#rds-proxy-iam-setup)
+ [Creating an RDS Proxy](#rds-proxy-creating)
+ [Viewing an RDS Proxy](#rds-proxy-viewing)
+ [Connecting to a database through RDS Proxy](#rds-proxy-connecting)

## Setting up network prerequisites<a name="rds-proxy-network-prereqs"></a>

 Using RDS Proxy requires you to have a common virtual private cloud \(VPC\) between your Aurora DB cluster or RDS DB instance and RDS Proxy\. This VPC should have a minimum of two subnets that are in different Availability Zones\. Your account can either own these subnets or share them with other accounts\. For information about VPC sharing, see [Work with shared VPCs](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-sharing.html)\. Your client application resources such as Amazon EC2, Lambda, or Amazon ECS can be in the same VPC as the proxy\. Or they can be in a separate VPC from the proxy\. If you successfully connected to any RDS DB instances or Aurora DB clusters, you already have the required network resources\.

**Topics**
+ [Getting information about your subnets](#rds-proxy-network-prereqs.subnet-info)
+ [Planning for IP address capacity](#rds-proxy-network-prereqs.plan-ip-address)

### Getting information about your subnets<a name="rds-proxy-network-prereqs.subnet-info"></a>

 The following Linux example shows AWS CLI commands that examine the VPCs and subnets owned by your AWS account\. In particular, you pass subnet IDs as parameters when you create a proxy using the CLI\. 

```
aws ec2 describe-vpcs
aws ec2 describe-internet-gateways
aws ec2 describe-subnets --query '*[].[VpcId,SubnetId]' --output text | sort
```

The following Linux example shows AWS CLI commands to determine the subnet IDs corresponding to a specific Aurora DB cluster or RDS DB instance\. For an Aurora cluster, first you find the ID for one of the associated DB instances\. You can extract the subnet IDs used by that DB instance\. To do so, examine the nested fields within the `DBSubnetGroup` and `Subnets` attributes in the describe output for the DB instance\. You specify some or all of those subnet IDs when setting up a proxy for that database server\. 

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

Or you can first find the VPC ID for the DB instance\. Then you can examine the VPC to find its subnets\. The following Linux example shows how\. 

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

### Planning for IP address capacity<a name="rds-proxy-network-prereqs.plan-ip-address"></a>

An RDS Proxy automatically adjusts its capacity as needed based on the size and number of DB instances registered with it\. Certain operations might also require additional proxy capacity\. Some examples are increasing the size of a registered database or internal RDS Proxy maintenance operations\. During these operations, your proxy might need more IP addresses to provision the extra capacity\. These additional addresses allow your proxy to scale without affecting your workload\. A lack of free IP addresses in your subnets prevents a proxy from scaling up\. This can lead to higher query latencies or client connection failures\.  RDS notifies you through event `RDS-EVENT-0243` when there aren't enough free IP addresses in your subnets\. For information about this event, see [Working with RDS Proxy eventsWorking with RDS Proxy events](rds-proxy.events.md)\.

Following are the recommended minimum number of IP addresses to leave free in your subnets for your proxy based on DB instance class sizes\.


|  DB instance class  |  Minimum free IP addresses  | 
| --- | --- | 
|  db\.\*\.xlarge or smaller   | 10  | 
|  db\.\*\.2xlarge   | 15  | 
|  db\.\*\.4xlarge   | 25  | 
|  db\.\*\.8xlarge   | 45  | 
|  db\.\*\.12xlarge   | 60  | 
|  db\.\*\.16xlarge   | 75  | 
|  db\.\*\.24xlarge   | 110  | 

These numbers of recommended IP addresses are estimates for a proxy with only the default endpoint\. A proxy with additional endpoints or read replicas might need more free IP addresses\. For each additional endpoint, we recommend that you reserve three more IP addresses\. For each read replica, we recommend that you reserve additional IP addresses as specified in the table based on that read replica's size\.

**Note**  
RDS Proxy never uses more than 215 IP addresses in a VPC\.

## Setting up database credentials in AWS Secrets Manager<a name="rds-proxy-secrets-arns"></a>

 For each proxy that you create, you first use the Secrets Manager service to store sets of user name and password credentials\. You create a separate Secrets Manager secret for each database user account that the proxy connects to on the RDS DB instance or Aurora DB cluster\. 

 In Secrets Manager, you create these secrets with values for the `username` and `password` fields\. Doing so allows the proxy to connect to the corresponding database users on RDS DB instances or Aurora DB clusters that you associate with the proxy\. To do this, you can use the setting **Credentials for other database**, **Credentials for RDS database**, or **Other type of secrets**\. Fill in the appropriate values for the **User name** and **Password** fields, and placeholder values for any other required fields\. The proxy ignores other fields such as **Host** and **Port** if they're present in the secret\. Those details are automatically supplied by the proxy\. 

 You can also choose **Other type of secrets**\. In this case, you create the secret with keys named `username` and `password`\. 

 Because the secrets used by your proxy aren't tied to a specific database server, you can reuse a secret across multiple proxies\. To do so, use the same credentials across multiple database servers\. For example, you might use the same credentials across a group of development and test servers\. 

 To connect through the proxy as a specific user, make sure that the password associated with a secret matches the database password for that user\. If there's a mismatch, you can update the associated secret in Secrets Manager\. In this case, you can still connect to other accounts where the secret credentials and the database passwords do match\. 

**Note**  
For RDS for SQL Server, the number of Secrets Manager secrets that you need to create for a proxy depends on the collation that your DB instance uses\. For example, suppose that your DB instance uses case\-sensitive collation\. If your application accepts both "Admin" and "admin," then your proxy needs two separate secrets\. For more information about collation in SQL Server, see the [ Microsoft SQL Server](https://docs.microsoft.com/en-us/sql/relational-databases/collations/collation-and-unicode-support?view=sql-server-ver16) documentation\.

 When you create a proxy through the AWS CLI or RDS API, you specify the Amazon Resource Names \(ARNs\) of the corresponding secrets\. You do so for all the DB user accounts that the proxy can access\. In the AWS Management Console, you choose the secrets by their descriptive names\. 

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

 When you create a proxy using the CLI, you pass the Amazon Resource Names \(ARNs\) of one or more secrets to the `--auth` parameter\. The following Linux example shows how to prepare a report with only the name and ARN of each secret owned by your AWS account\. This example uses the `--output table` parameter that is available in AWS CLI version 2\. If you are using AWS CLI version 1, use `--output text` instead\. 

```
aws secretsmanager list-secrets --query '*[].[Name,ARN]' --output table
```

 To verify that you stored the correct credentials and in the right format in a secret, use a command such as the following\. Substitute the short name or the ARN of the secret for `your_secret_name`\. 

```
aws secretsmanager get-secret-value --secret-id your_secret_name
```

 The output should include a line displaying a JSON\-encoded value like the following\. 

```
"SecretString": "{\"username\":\"your_username\",\"password\":\"your_password\"}",
```

## Setting up AWS Identity and Access Management \(IAM\) policies<a name="rds-proxy-iam-setup"></a>

 After you create the secrets in Secrets Manager, you create an IAM policy that can access those secrets\. For general information about using IAM with RDS and Aurora, see [Identity and access management for Amazon RDS](UsingWithRDS.IAM.md)\. 

**Tip**  
 The following procedure applies if you use the IAM console\. If you use the AWS Management Console for RDS, RDS can create the IAM policy for you automatically\. In that case, you can skip the following procedure\. 

**To create an IAM policy that accesses your Secrets Manager secrets for use with your proxy**

1.  Sign in to the IAM console\. Follow the **Create role** process, as described in [Creating IAM roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create.html)\. Include the **Add Role to Database** step\. 

1.  For the new role, perform the **Add inline policy** step\. Use the same general procedures as in [Editing IAM policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_manage-edit.html)\. Paste the following JSON into the JSON text box\. Substitute your own account ID\. Substitute your AWS Region for `us-east-2`\. Substitute the Amazon Resource Names \(ARNs\) for the secrets that you created\. For the `kms:Decrypt` action, substitute the ARN of the default AWS KMS key or your own KMS key\. Which one you use depends on which one you used to encrypt the Secrets Manager secrets\. 

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
PREFIX=my_identifier

aws iam create-role --role-name my_role_name \
  --assume-role-policy-document '{"Version":"2012-10-17","Statement":[{"Effect":"Allow","Principal":{"Service":["rds.amazonaws.com"]},"Action":"sts:AssumeRole"}]}'

aws iam put-role-policy --role-name my_role_name \
  --policy-name $PREFIX-secret-reader-policy --policy-document '{"Version":"2012-10-17","Statement":[{"Effect":"Allow","Principal":{"Service":["rds.amazonaws.com"]},"Action":"sts:AssumeRole"}]}'

aws kms create-key --description "$PREFIX-test-key" --policy '{
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
}'
```

## Creating an RDS Proxy<a name="rds-proxy-creating"></a>

 To manage connections for a specified set of DB instances, you can create a proxy\. You can associate a proxy with an RDS for MariaDB, RDS for Microsoft SQL Server, RDS for MySQL, or RDS for PostgreSQL DB instance\. 

### AWS Management Console<a name="rds-proxy-creating.console"></a>

**To create a proxy**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1.  In the navigation pane, choose **Proxies**\. 

1.  Choose **Create proxy**\. 

1.  Choose all the settings for your proxy\. 

    For **Proxy configuration**, provide information for the following: 
   +  **Engine family**\. This setting determines which database network protocol the proxy recognizes when it interprets network traffic to and from the database\. For RDS for MariaDB or RDS for MySQL, choose **MariaDB and MySQL**\. For RDS for PostgreSQL, choose **PostgreSQL**\. For RDS for SQL Server, choose **SQL Server**\. 
   +  **Proxy identifier**\. Specify a name of your choosing, unique within your AWS account ID and current AWS Region\. 
   +  **Idle client connection timeout**\. Choose a time period that a client connection can be idle before the proxy can close it\. The default is 1,800 seconds \(30 minutes\)\. A client connection is considered idle when the application doesn't submit a new request within the specified time after the previous request completed\. The underlying database connection stays open and is returned to the connection pool\. Thus, it's available to be reused for new client connections\. 

      Consider lowering the idle client connection timeout if you want the proxy to proactively remove stale connections\. If your workload is spiking, consider raising the idle client connection timeout to save the cost of establishing connections\.

    For **Target group configuration**, provide information for the following: 
   +  **Database**\. Choose one RDS DB instance or Aurora DB cluster to access through this proxy\. The list only includes DB instances and clusters with compatible database engines, engine versions, and other settings\. If the list is empty, create a new DB instance or cluster that's compatible with RDS Proxy\. To do so, follow the procedure in [Creating an Amazon RDS DB instance](USER_CreateDBInstance.md)\. Then try creating the proxy again\. 
   +  **Connection pool maximum connections**\. Specify a value from 1 through 100\. This setting represents the percentage of the `max_connections` value that RDS Proxy can use for its connections\. If you only intend to use one proxy with this DB instance or cluster, you can set this value to 100\. For details about how RDS Proxy uses this setting, see [MaxConnectionsPercent](rds-proxy-managing.md#rds-proxy-connection-pooling-tuning.maxconnectionspercent)\. 
   +  **Session pinning filters**\. \(Optional\) This is an advanced setting, for troubleshooting performance issues with particular applications\. Currently, the setting isn't supported for PostgreSQL and the only choice is `EXCLUDE_VARIABLE_SETS`\. Choose a filter only if both of following are true: First, your application isn't reusing connections due to certain kinds of SQL statements\. Also, you can verify that reusing connections with those SQL statements doesn't affect application correctness\. For more information, see [Avoiding pinning](rds-proxy-managing.md#rds-proxy-pinning)\. 
   +  **Connection borrow timeout**\. In some cases, you might expect the proxy to sometimes use all available database connections\. In such cases, you can specify how long the proxy waits for a database connection to become available before returning a timeout error\. You can specify a period up to a maximum of five minutes\. This setting only applies when the proxy has the maximum number of connections open and all connections are already in use\.
   +  **Initialization query**\. \(Optional\) You can specify one or more SQL statements for the proxy to run when opening each new database connection\. The setting is typically used with `SET` statements to make sure that each connection has identical settings such as time zone and character set\. For multiple statements, use semicolons as the separator\. You can also include multiple variables in a single `SET` statement, such as `SET x=1, y=2`\. Initialization query is not currently supported for PostgreSQL\.

    For **Authentication**, provide information for the following:
   +  **IAM role**\. Choose an IAM role that has permission to access the Secrets Manager secrets that you chose earlier\. You can also choose for the AWS Management Console to create a new IAM role for you and use that\. 
   +  **Secrets Manager secrets**\. Choose at least one Secrets Manager secret that contains database user credentials for the RDS DB instance or Aurora DB cluster to access with this proxy\. 
   + **Client authentication type**\. Choose the type of authentication the proxy uses for connections from clients\. Your choice applies to all Secrets Manager secrets that you associate with this proxy\. If you need to specify a different client authentication type for each secret, create your proxy by using the AWS CLI or the API instead\.
   +  **IAM authentication**\. Choose whether to require, allow, or disallow IAM authentication for connections to your proxy\. The allow option is only valid for proxies for RDS for SQL Server\.  Your choice applies to all Secrets Manager secrets that you associate with this proxy\. If you need to specify a different IAM authentication for each secret, create your proxy by using the AWS CLI or the API instead\. 

    For **Connectivity**, provide information for the following:
   +  **Require Transport Layer Security**\. Choose this setting if you want the proxy to enforce TLS/SSL for all client connections\. For an encrypted or unencrypted connection to a proxy, the proxy uses the same encryption setting when it makes a connection to the underlying database\.
   +  **Subnets**\. This field is prepopulated with all the subnets associated with your VPC\. You can remove any subnets that you don't need for this proxy\. You must leave at least two subnets\. 

    Provide additional connectivity configuration: 
   +  **VPC security group**\. Choose an existing VPC security group\. You can also choose for the AWS Management Console to create a new security group for you and use that\. 
**Note**  
 This security group must allow access to the database the proxy connects to\. The same security group is used for ingress from your applications to the proxy, and for egress from the proxy to the database\. For example, suppose that you use the same security group for your database and your proxy\. In this case, make sure that you specify that resources in that security group can communicate with other resources in the same security group\.  
When using a shared VPC, you can't use the default security group for the VPC, or one that belongs to another account\. Choose a security group that belongs to your account\. If one doesn't exist, create one\. For more information about this limitation, see [Work with shared VPCs](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-sharing.html#vpc-share-limitations)\. 

    \(Optional\) Provide advanced configuration: 
   +  **Enable enhanced logging**\. You can enable this setting to troubleshoot proxy compatibility or performance issues\. 

      When this setting is enabled, RDS Proxy includes detailed information about SQL statements in its logs\. This information helps you to debug issues involving SQL behavior or the performance and scalability of the proxy connections\. The debug information includes the text of SQL statements that you submit through the proxy\. Thus, only enable this setting when needed for debugging\. Also, only enable it when you have security measures in place to safeguard any sensitive information that appears in the logs\. 

      To minimize overhead associated with your proxy, RDS Proxy automatically turns this setting off 24 hours after you enable it\. Enable it temporarily to troubleshoot a specific issue\. 

1.  Choose **Create Proxy**\. 

### AWS CLI<a name="rds-proxy-creating.CLI"></a>

 To create a proxy, use the AWS CLI command [create\-db\-proxy](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-proxy.html)\. The `--engine-family` value is case\-sensitive\. 

**Example**  
For Linux, macOS, or Unix:  

```
aws rds create-db-proxy \
    --db-proxy-name proxy_name \
    --engine-family { MYSQL | POSTGRESQL | SQLSERVER } \
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
    --engine-family { MYSQL | POSTGRESQL | SQLSERVER } ^
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
 If you don't already know the subnet IDs to use for the `--vpc-subnet-ids` parameter, see [Setting up network prerequisites](#rds-proxy-network-prereqs) for examples of how to find them\. 

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

 After you create one or more RDS proxies, you can view them all\. Doing so makes it possible to examine their configuration details and choose which ones to modify, delete, and so on\. 

 Any database applications that use the proxy require the proxy endpoint to use in the connection string\. 

### AWS Management Console<a name="rds-proxy-viewing.console"></a>

**To view your proxy**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1.  In the upper\-right corner of the AWS Management Console, choose the AWS Region in which you created the RDS Proxy\. 

1.  In the navigation pane, choose **Proxies**\. 

1.  Choose the name of an RDS proxy to display its details\. 

1.  On the details page, the **Target groups** section shows how the proxy is associated with a specific RDS DB instance or Aurora DB cluster\. You can follow the link to the **default** target group page to see more details about the association between the proxy and the database\. This page is where you see settings that you specified when creating the proxy\. These include maximum connection percentage, connection borrow timeout, engine family, and session pinning filters\. 

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

1.  To show connection parameters such as the maximum percentage of connections that the proxy can use, run [describe\-db\-proxy\-target\-groups](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-proxy-target-groups.html) `--db-proxy-name`\. Use the name of the proxy as the parameter value\. 

1.  To see the details of the RDS DB instance or Aurora DB cluster associated with the returned target group, run [describe\-db\-proxy\-targets](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-proxy-targets.html)\. 

### RDS API<a name="rds-proxy-viewing.api"></a>

 To view your proxies using the RDS API, use the [DescribeDBProxies](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBProxies.html) operation\. It returns values of the [DBProxy](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DBProxy.html) data type\. 

 To see details of the connection settings for the proxy, use the proxy identifiers from this return value with the [DescribeDBProxyTargetGroups](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBProxyTargetGroups.html) operation\. It returns values of the [DBProxyTargetGroup](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DBProxyTargetGroup.html) data type\. 

 To see the RDS instance or Aurora DB cluster associated with the proxy, use the [DescribeDBProxyTargets](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBProxyTargets.html) operation\. It returns values of the [DBProxyTarget](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DBProxyTarget.html) data type\. 

## Connecting to a database through RDS Proxy<a name="rds-proxy-connecting"></a>

 You connect to an RDS DB instance or Aurora DB cluster through a proxy in generally the same way as you connect directly to the database\. The main difference is that you specify the proxy endpoint instead of the instance or cluster endpoint\. For an Aurora DB cluster, by default all proxy connections have read/write capability and use the writer instance\. If you normally use the reader endpoint for read\-only connections, you can create an additional read\-only endpoint for the proxy\. You can use that endpoint the same way\. For more information, see [Overview of proxy endpoints](rds-proxy-endpoints.md#rds-proxy-endpoints-overview)\. 

**Topics**
+ [Connecting to a proxy using native authentication](#rds-proxy-connecting-native)
+ [Connecting to a proxy using IAM authentication](#rds-proxy-connecting-iam)
+ [Considerations for connecting to a proxy with Microsoft SQL Server](#rds-proxy-connecting-sqlserver)
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

 To connect to RDS Proxy using IAM authentication, use the same general connection procedure as for IAM authentication with an RDS DB instance or Aurora cluster\. For general information about using IAM with RDS and Aurora, see [Security in Amazon RDS](UsingWithRDS.md)\. 

 The major differences in IAM usage for RDS Proxy include the following: 
+  You don't configure each individual database user with an authorization plugin\. The database users still have regular user names and passwords within the database\. You set up Secrets Manager secrets containing these user names and passwords, and authorize RDS Proxy to retrieve the credentials from Secrets Manager\. 

   The IAM authentication applies to the connection between your client program and the proxy\. The proxy then authenticates to the database using the user name and password credentials retrieved from Secrets Manager\. 
+  Instead of the instance, cluster, or reader endpoint, you specify the proxy endpoint\. For details about the proxy endpoint, see [Connecting to your DB instance using IAM authentication](UsingWithRDS.IAMDBAuth.Connecting.md)\. 
+  In the direct database IAM authentication case, you selectively choose database users and configure them to be identified with a special authentication plugin\. You can then connect to those users using IAM authentication\. 

   In the proxy use case, you provide the proxy with Secrets that contain some user's user name and password \(native authentication\)\. You then connect to the proxy using IAM authentication\. Here, you do this by generating an authentication token with the proxy endpoint, not the database endpoint\. You also use a user name that matches one of the user names for the secrets that you provided\. 
+  Make sure that you use Transport Layer Security \(TLS\)/Secure Sockets Layer \(SSL\) when connecting to a proxy using IAM authentication\. 

 You can grant a specific user access to the proxy by modifying the IAM policy\. An example follows\. 

```
"Resource": "arn:aws:rds-db:us-east-2:1234567890:dbuser:prx-ABCDEFGHIJKL01234/db_user"
```

### Considerations for connecting to a proxy with Microsoft SQL Server<a name="rds-proxy-connecting-sqlserver"></a>

For connecting to a proxy using IAM authentication, you don't use the password field\. Instead, you provide the appropriate token property for each type of database driver in the token field\. For example, use the `accessToken` property for JDBC, or the `sql_copt_ss_access_token` property for ODBC\. Or use the `AccessToken` property for the \.NET SqlClient driver\. You can't use IAM authentication with clients that don't support token properties\.

Under some conditions, a proxy can't share a database connection and instead pins the connection from your client application to the proxy to a dedicated database connection\. For more information about these conditions, see [Avoiding pinning](rds-proxy-managing.md#rds-proxy-pinning)\.

### Considerations for connecting to a proxy with PostgreSQL<a name="rds-proxy-connecting-postgresql"></a>

 For PostgreSQL, when a client starts a connection to a PostgreSQL database, it sends a startup message\. This message includes pairs of parameter name and value strings\. For details, see the `StartupMessage` in [PostgreSQL message formats](https://www.postgresql.org/docs/current/protocol-message-formats.html) in the PostgreSQL documentation\. 

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

For more information, see [Avoiding pinning](rds-proxy-managing.md#rds-proxy-pinning)\. For more information about connecting using JDBC, see [Connecting to the database](https://jdbc.postgresql.org/documentation/setup/) in the PostgreSQL documentation\.