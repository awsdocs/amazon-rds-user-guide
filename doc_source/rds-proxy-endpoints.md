# Working with Amazon RDS Proxy endpoints<a name="rds-proxy-endpoints"></a>

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
+ [Limitations for proxy endpoints](#rds-proxy-endpoints-limits)

## Overview of proxy endpoints<a name="rds-proxy-endpoints-overview"></a>

 Working with RDS Proxy endpoints involves the same kinds of procedures as with Aurora DB cluster and reader endpoints and RDS instance endpoints\. If you aren't familiar with RDS endpoints, find more information in [Connecting to a DB instance running the MySQL database engine](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_ConnectToInstance.html) and [Connecting to a DB instance running the PostgreSQL database engine](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_ConnectToPostgreSQLInstance.html)\. 

 By default, the endpoint that you connect to when you use RDS Proxy with an Aurora cluster has read/write capability\. As a result, this endpoint sends all requests to the writer instance of the cluster\. All of those connections count against the `max_connections` value for the writer instance\. If your proxy is associated with an Aurora DB cluster, you can create additional read/write or read\-only endpoints for that proxy\. 

 You can use a read\-only endpoint with your proxy for read\-only queries\. You do this the same way that you use the reader endpoint for an Aurora provisioned cluster\. Doing so helps you to take advantage of the read scalability of an Aurora cluster with one or more reader DB instances\. You can run more simultaneous queries and make more simultaneous connections by using a read\-only endpoint and adding more reader DB instances to your Aurora cluster as needed\. 

 For a proxy endpoint that you create, you can also associate the endpoint with a different virtual private cloud \(VPC\) than the proxy itself uses\. By doing so, you can connect to the proxy from a different VPC, for example a VPC used by a different application within your organization\.  

 For information about limits associated with proxy endpoints, see [Limitations for proxy endpoints](#rds-proxy-endpoints-limits)\. 

 In the RDS Proxy logs, each entry is prefixed with the name of the associated proxy endpoint\. This name can be the name you specified for a user\-defined endpoint\. Or it can be the special name `default` for read/write requests using the default endpoint of a proxy\. 

 Each proxy endpoint has its own set of CloudWatch metrics\. You can monitor the metrics for all endpoints of a proxy\. You can also monitor metrics for a specific endpoint, or for all the read/write or read\-only endpoints of a proxy\. For more information, see [Monitoring RDS Proxy metrics with Amazon CloudWatchMonitoring RDS Proxy with CloudWatch](rds-proxy.monitoring.md)\. 

 A proxy endpoint uses the same authentication mechanism as its associated proxy\. RDS Proxy automatically sets up permissions and authorizations for the user\-defined endpoint, consistent with the properties of the associated proxy\. 

## Reader endpoints<a name="rds-proxy-endpoints-reader-stub"></a>

 With RDS Proxy, you can create and use reader endpoints\. However, these endpoints only work for proxies associated with Aurora DB clusters\. You might see references to reader endpoints in the AWS Management Console\. If you use the RDS CLI or API, you might see the `TargetRole` attribute with a value of `READ_ONLY`\. You can take advantage of these features by changing the target of a proxy from an RDS DB instance to an Aurora DB cluster\. To learn about reader endpoints, see [Managing connections with Amazon RDS Proxy](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/rds-proxy-.html) in the *Aurora User Guide\.* 

## Accessing Aurora and RDS databases across VPCs<a name="rds-proxy-cross-vpc"></a>

 By default, the components of your RDS and Aurora technology stack are all in the same Amazon VPC\. For example, suppose that an application running on an Amazon EC2 instance connects to an Amazon RDS DB instance or an Aurora DB cluster\. In this case, the application server and database must both be within the same VPC\. 

 With RDS Proxy, you can set up access to an Aurora cluster or RDS instance in one VPC from resources such as EC2 instances in another VPC\. For example, your organization might have multiple applications that access the same database resources\. Each application might be in its own VPC\.  

 To enable cross\-VPC access, you create a new endpoint for the proxy\. If you aren't familiar with creating proxy endpoints, see [Working with Amazon RDS Proxy endpoints](#rds-proxy-endpoints) for details\. The proxy itself resides in the same VPC as the Aurora DB cluster or RDS instance\. However, the cross\-VPC endpoint resides in the other VPC, along with the other resources such as the EC2 instances\. The cross\-VPC endpoint is associated with subnets and security groups from the same VPC as the EC2 and other resources\. These associations let you connect to the endpoint from the applications that otherwise can't access the database due to the VPC restrictions\. 

 The following steps explain how to create and access a cross\-VPC endpoint through RDS Proxy: 

1.  Create two VPCs, or choose two VPCs that you already use for Aurora and RDS work\. Each VPC should have its own associated network resources such as an Internet gateway, route tables, subnets, and security groups\. If you only have one VPC, you can consult [Getting started with Amazon RDS](CHAP_GettingStarted.md) for the steps to set up another VPC to use RDS successfully\. You can also examine your existing VPC in the Amazon EC2 console to see what kinds of resources to connect together\. 

1.  Create a DB proxy associated with the Aurora DB cluster or RDS instance that you want to connect to\. Follow the procedure in [Creating an RDS Proxy](rds-proxy-setup.md#rds-proxy-creating)\. 

1.  On the **Details** page for your proxy in the RDS console, under the **Proxy endpoints** section, choose **Create endpoint**\. Follow the procedure in [Creating a proxy endpoint](#rds-proxy-endpoints.CreatingEndpoint)\. 

1.  Choose whether to make the cross\-VPC endpoint read/write or read\-only\. 

1.  Instead of accepting the default of the same VPC as the Aurora DB cluster or RDS instance, choose a different VPC\. This VPC must be in the same AWS Region as the VPC where the proxy resides\. 

1.  Now instead of accepting the defaults for subnets and security groups from the same VPC as the Aurora DB cluster or RDS instance, make new selections\. Make these based on the subnets and security groups from the VPC that you chose\. 

1.  You don't need to change any of the settings for the Secrets Manager secrets\. The same credentials work for all endpoints for your proxy, regardless of which VPC each endpoint is in\. 

1.  Wait for the new endpoint to reach the **Available** state\. 

1.  Make a note of the full endpoint name\. This is the value ending in `Region_name.rds.amazonaws.com` that you supply as part of the connection string for your database application\. 

1.  Access the new endpoint from a resource in the same VPC as the endpoint\. A simple way to test this process is to create a new EC2 instance in this VPC\. Then you can log into the EC2 instance and run the `mysql` or `psql` commands to connect by using the endpoint value in your connection string\. 

## Creating a proxy endpoint<a name="rds-proxy-endpoints.CreatingEndpoint"></a>



### Console<a name="rds-proxy-endpoints.CreatingEndpoint.CON"></a>

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

1.  For **Virtual Private Cloud \(VPC\)**, choose the default to access the endpoint from the same EC2 instances or other resources where you normally access the proxy or its associated database\. To set up cross\-VPC access for this proxy, choose a VPC other than the default\. For more information about cross\-VPC access, see [Accessing Aurora and RDS databases across VPCs](#rds-proxy-cross-vpc)\. 

1.  For **Subnets**, RDS Proxy fills in the same subnets as the associated proxy by default\. To restrict access to the endpoint so only a portion of the VPC's address range can connect to it, remove one or more subnets\. 

1.  For **VPC security group**, you can choose an existing security group or create a new one\. RDS Proxy fills in the same security group or groups as the associated proxy by default\. If the inbound and outbound rules for the proxy are appropriate for this endpoint, you can leave the default choice\. 

    If you choose to create a new security group, specify a name for the security group on this page\. Then edit the security group settings from the EC2 console afterward\. 

1.  Choose **Create proxy endpoint**\. 

### AWS CLI<a name="rds-proxy-endpoints.CreatingEndpoint.CLI"></a>

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

### RDS API<a name="rds-proxy-endpoints.CreatingEndpoint.API"></a>

 To create a proxy endpoint, use the RDS API [CreateDBProxyEndpoint](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBProxyEndpoint.html) action\. 

## Viewing proxy endpoints<a name="rds-proxy-endpoints.DescribingEndpoint"></a>



### Console<a name="rds-proxy-endpoints.DescribingEndpoint.CON"></a>

**To view the details for a proxy endpoint**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1.  In the navigation pane, choose **Proxies**\. 

1.  In the list, choose the proxy whose endpoint you want to view\. Click the proxy name to view its details page\. 

1.  In the **Proxy endpoints** section, choose the endpoint that you want to view\. Click its name to view the details page\. 

1.  Examine the parameters whose values you're interested in\. You can check properties such as the following: 
   +  Whether the endpoint is read/write or read\-only\. 
   +  The endpoint address that you use in a database connection string\. 
   +  The VPC, subnets, and security groups associated with the endpoint\. 

### AWS CLI<a name="rds-proxy-endpoints.DescribingEndpoint.CLI"></a>

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

### RDS API<a name="rds-proxy-endpoints.DescribingEndpoint.API"></a>

 To describe one or more proxy endpoints, use the RDS API [DescribeDBProxyEndpoints](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBProxyEndpoints.html) operation\. 

## Modifying a proxy endpoint<a name="rds-proxy-endpoints.ModifyingEndpoint"></a>



### Console<a name="rds-proxy-endpoints.ModifyingEndpoint.CON"></a>

**To modify one or more proxy endpoints**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1.  In the navigation pane, choose **Proxies**\. 

1.  In the list, choose the proxy whose endpoint you want to modify\. Click the proxy name to view its details page\. 

1.  In the **Proxy endpoints** section, choose the endpoint that you want to modify\. You can select it in the list, or click its name to view the details page\. 

1.  On the proxy details page, under the **Proxy endpoints** section, choose **Edit**\. Or on the proxy endpoint details page, for **Actions**, choose **Edit**\. 

1.  Change the values of the parameters that you want to modify\. 

1.  Choose **Save changes**\. 

### AWS CLI<a name="rds-proxy-endpoints.ModifyingEndpoint.CLI"></a>

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

### RDS API<a name="rds-proxy-endpoints.ModifyingEndpoint.API"></a>

 To modify a proxy endpoint, use the RDS API [ModifyDBProxyEndpoint](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBProxyEndpoint.html) operation\. 

## Deleting a proxy endpoint<a name="rds-proxy-endpoints.DeletingEndpoint"></a>

 You can delete an endpoint for your proxy using the console as described following\. 

**Note**  
 You can't delete the default endpoint that RDS Proxy automatically creates for each proxy\.   
 When you delete a proxy, RDS Proxy automatically deletes all the associated endpoints\. 

### Console<a name="rds-proxy-endpoints.DeleteEndpoint.console"></a>

**To delete a proxy endpoint using the AWS Management Console**

1.  In the navigation pane, choose **Proxies**\. 

1.  In the list, choose the proxy whose endpoint you want to endpoint\. Click the proxy name to view its details page\. 

1.  In the **Proxy endpoints** section, choose the endpoint that you want to delete\. You can select one or more endpoints in the list, or click the name of a single endpoint to view the details page\. 

1.  On the proxy details page, under the **Proxy endpoints** section, choose **Delete**\. Or on the proxy endpoint details page, for **Actions**, choose **Delete**\. 

### AWS CLI<a name="rds-proxy-endpoints.DeleteEndpoint.cli"></a>

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

### RDS API<a name="rds-proxy-endpoints.DeleteEndpoint.api"></a>

 To delete a proxy endpoint with the RDS API, run the [DeleteDBProxyEndpoint](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DeleteDBProxyEndpoint.html) operation\. Specify the name of the proxy endpoint for the `DBProxyEndpointName` parameter\. 

## Limitations for proxy endpoints<a name="rds-proxy-endpoints-limits"></a>

 Each proxy has a default endpoint that you can modify but not create or delete\. 

 The maximum number of user\-defined endpoints for a proxy is 20\. Thus, a proxy can have up to 21 endpoints: the default endpoint, plus 20 that you create\. 

 When you associate additional endpoints with a proxy, RDS Proxy automatically determines which DB instances in your cluster to use for each endpoint\. You can't choose specific instances the way that you can with Aurora custom endpoints\. 

 

 Reader endpoints aren't available for Aurora multi\-writer clusters\. 