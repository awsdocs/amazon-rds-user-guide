# Managing a Multi\-AZ deployment for RDS Custom for SQL Server<a name="custom-sqlserver-multiaz"></a>

 In a Multi\-AZ DB instance deployment for RDS Custom for SQL Server, Amazon RDS automatically provisions and maintains a synchronous standby replica in a different Availability Zone \(AZ\)\. The primary DB instance is synchronously replicated across Availability Zones to a standby replica to provide data redundancy\.

**Important**  
A Multi\-AZ deployment for RDS Custom for SQL Server is different than Multi\-AZ for RDS for SQL Server\. Unlike Multi\-AZ for RDS for SQL Server, you must set up prerequisites for RDS Custom for SQL Server before creating your Multi\-AZ DB instance because RDS Custom runs inside your own account, which requires permissions\.  
If you don't complete the prerequisites, your Multi\-AZ DB instance might fail to run, or automatically revert to a Single\-AZ DB instance\. For more information about prerequisites, see [Prerequisites for a Multi\-AZ deployment with RDS Custom for SQL Server](#custom-sqlserver-multiaz.prerequisites)\.

Running a DB instance with high availability can enhance availability during planned system maintenance\. In the event of planned database maintenance or unplanned service disruption, Amazon RDS automatically fails over to the up\-to\-date secondary DB instance\. This functionality lets database operations resume quickly without manual intervention\. The primary and standby instances use the same endpoint, whose physical network address transitions to the secondary replica as part of the failover process\. You don't have to reconfigure your application when a failover occurs\.

![\[RDS Custom for SQL Server supports Multi-AZ.\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/custom-sqlserver-multiaz-architecture.png)

You can create an RDS Custom for SQL Server Multi\-AZ deployment by specifying Multi\-AZ when creating an RDS Custom DB instance\. You can use the console to convert existing RDS Custom for SQL Server DB instances to Multi\-AZ deployments by modifying the DB instance and specifying the Multi\-AZ option\. You can also specify a Multi\-AZ DB instance deployment with the AWS CLI or Amazon RDS API\.

The RDS console shows the Availability Zone of the standby replica \(the secondary AZ\)\. You can also use the `describe-db-instances` CLI command or the `DescribeDBInstances` API operation to find the secondary AZ\.

RDS Custom for SQL Server DB instances with Multi\-AZ deployment can have increased write and commit latency compared to a Single\-AZ deployment\. This increase can happen because of the synchronous data replication between DB instances\. You might have a change in latency if your deployment fails over to the standby replica, although AWS is engineered with low\-latency network connectivity between Availability Zones\.

**Note**  
For production workloads, we recommend that you use a DB instance class with Provisioned IOPS \(input/output operations per second\) for fast, consistent performance\. For more information about DB instance classes, see [Requirements and limitations for Amazon RDS Custom for SQL Server](custom-reqs-limits-MS.md)\.

**Topics**
+ [Region and version availability](#custom-sqlserver-multiaz.regionversion)
+ [Limitations for a Multi\-AZ deployment with RDS Custom for SQL Server](#custom-sqlserver-multiaz.limitations)
+ [Prerequisites for a Multi\-AZ deployment with RDS Custom for SQL Server](#custom-sqlserver-multiaz.prerequisites)
+ [Creating an RDS Custom for SQL Server Multi\-AZ deployment](#custom-sqlserver-multiaz.creating)
+ [Modifying an RDS Custom for SQL Server Single\-AZ deployment to a Multi\-AZ deployment](#custom-sqlserver-multiaz.modify-saztomaz)
+ [Modifying an RDS Custom for SQL Server Multi\-AZ deployment to a Single\-AZ deployment](#custom-sqlserver-multiaz.modify-maztosaz)
+ [Failover process for an RDS Custom for SQL Server Multi\-AZ deployment](#custom-sqlserver-multiaz.failover)
+ [Time to live \(TTL\) settings with applications using an RDS Custom for SQL Server Multi\-AZ deployment](#custom-sqlserver-multiaz.ttldns)

## Region and version availability<a name="custom-sqlserver-multiaz.regionversion"></a>

Multi\-AZ deployments for RDS Custom for SQL Server are supported for the following SQL Server editions:
+ SQL Server 2019 Enterprise Edition
+ SQL Server 2019 Standard Edition
+ SQL Server 2019 Web Edition

Multi\-AZ deployments for RDS Custom for SQL Server are supported for the following SQL Server versions:
+ SQL Server 2019 CU18 \(15\.0\.4261\.1\)
+ SQL Server 2019 CU17 \(15\.0\.4249\.2\)

Multi\-AZ deployments for RDS Custom for SQL Server are available in all Regions where RDS Custom for SQL Server is available\. For more information on Region availability of Multi\-AZ deployments for RDS Custom for SQL Server, see [RDS Custom for SQL Server](Concepts.RDS_Fea_Regions_DB-eng.Feature.RDSCustom.md#Concepts.RDS_Fea_Regions_DB-eng.Feature.RDSCustom.sq)\.

## Limitations for a Multi\-AZ deployment with RDS Custom for SQL Server<a name="custom-sqlserver-multiaz.limitations"></a>

Multi\-AZ deployments with RDS Custom for SQL Server have the following limitations:
+ Cross\-Region Multi\-AZ deployments aren't supported\.
+ You can’t configure the secondary DB instance to accept database read activity\.
+ When you use a Custom Engine Version \(CEV\) with a Multi\-AZ deployment, your secondary DB instance will also use the same CEV\. The secondary DB instance can't use a different CEV\. 

## Prerequisites for a Multi\-AZ deployment with RDS Custom for SQL Server<a name="custom-sqlserver-multiaz.prerequisites"></a>

If you have an existing RDS Custom for SQL Server Single\-AZ deployment, the following additional prerequisites are required before modifying it to a Multi\-AZ deployment\. You can choose to complete the prerequisites manually or with the provided CloudFormation template\. The latest CloudFormation template contains the prerequisites for both Single\-AZ and Multi\-AZ deployments\.

**Important**  
To simplify setup, we recommend that you use the latest AWS CloudFormation template file provided in the network setup instructions to create the prerequisites\. For more information, see [Configuring with AWS CloudFormation](custom-setup-sqlserver.md#custom-setup-sqlserver.cf)\.

**Note**  
When you modify an existing RDS Custom for SQL Server Single\-AZ deployment to a Multi\-AZ deployment, you must complete these prerequisites\. If you don't complete the prerequisites, the Multi\-AZ setup will fail\. To complete the prerequisites, follow the steps in [Modifying an RDS Custom for SQL Server Single\-AZ deployment to a Multi\-AZ deployment](#custom-sqlserver-multiaz.modify-saztomaz)\.
+ Update the RDS security group inbound and outbound rules to allow port 1120\.
+ Add a rule in your private network Access Control List \(ACL\) that allows TCP ports `0-65535` for the DB instance VPC\.
+ Create new Amazon SQS VPC endpoints that allow the RDS Custom for SQL Server DB instance to communicate with SQS\.
+ Update the SQS permissions in the instance profile role\.

## Creating an RDS Custom for SQL Server Multi\-AZ deployment<a name="custom-sqlserver-multiaz.creating"></a>

To create an RDS Custom for SQL Server Multi\-AZ deployment, follow the steps in [Creating and connecting to a DB instance for Amazon RDS Custom for SQL Server](custom-creating-sqlserver.md)\.

**Important**  
To simplify setup, we recommend that you use the latest AWS CloudFormation template file provided in the network setup instructions\. For more information, see [Configuring with AWS CloudFormation](custom-setup-sqlserver.md#custom-setup-sqlserver.cf)\.

Creating a Multi\-AZ deployment takes a few minutes to complete\.

## Modifying an RDS Custom for SQL Server Single\-AZ deployment to a Multi\-AZ deployment<a name="custom-sqlserver-multiaz.modify-saztomaz"></a>

You can modify an existing RDS Custom for SQL Server DB instance from a Single\-AZ deployment to a Multi\-AZ deployment\. When you modify the DB instance,Amazon RDS performs several actions:
+ Takes a snapshot of the primary DB instance\.
+ Creates new volumes for the standby replica from the snapshot\. These volumes initialize in the background, and maximum volume performance is achieved after the data is fully initialized\.
+ Turns on synchronous block\-level replication between the primary and secondary DB instances\.

**Important**  
We recommend that you avoid modifying your RDS Custom for SQL Server DB instance from a Single\-AZ to a Multi\-AZ deployment on a production DB instance during periods of peak activity\.

AWS uses a snapshot to create the standby instance to avoid downtime when you convert from Single\-AZ to Multi\-AZ, but performance might be impacted during and after converting to Multi\-AZ\. This impact can be significant for workloads that are sensitive to write latency\. While this capability allows large volumes to quickly be restored from snapshots, it can cause increase in the latency of I/O operations because of the synchronous replication\. This latency can impact your database performance\.

**Topics**
+ [Configuring prerequisites to modify a Single\-AZ to a Multi\-AZ deployment using CloudFormation](#custom-sqlserver-multiaz.modify-saztomaz-prereqs.cf)
+ [Configuring prerequisites to modify a Single\-AZ to a Multi\-AZ deployment manually](#custom-sqlserver-multiaz.modify-saztomaz-prereqs.manual)
+ [Modify using the RDS console, AWS CLI, or RDS API\.](#custom-sqlserver-multiaz.modify-saztomaz-afterprereqs)

### Configuring prerequisites to modify a Single\-AZ to a Multi\-AZ deployment using CloudFormation<a name="custom-sqlserver-multiaz.modify-saztomaz-prereqs.cf"></a>

To use a Multi\-AZ deployment, you must ensure you've applied the latest CloudFormation template with prerequisites, or manually configure the latest prerequisites\. If you've already applied the latest CloudFormation prerequisite template, you can skip these steps\.

To configure the RDS Custom for SQL Server Multi\-AZ deployment prerequisites using CloudFormation

1. Open the CloudFormation console at [https://console\.aws\.amazon\.com/cloudformation](https://console.aws.amazon.com/cloudformation/)\.

1. To start the Create Stack wizard, select the existing stack you used to create a Single\-AZ deployment and choose** Update**\.

   The **Update stack** page appears\.

1. For **Prerequisite \- Prepare template**, choose **Replace current template**\.

1. For **Specify template**, do the following:

   1. Download the latest AWS CloudFormation template file\. Open the context \(right\-click\) menu for the link [custom\-sqlserver\-onboard\.zip](samples/custom-sqlserver-onboard.zip) and choose **Save Link As**\.

   1. Save and extract the `custom-sqlserver-onboard.json` file to your computer\.

   1. For **Template source**, choose **Upload a template file**\.

   1. For **Choose file**, navigate to and then choose `custom-sqlserver-onboard.json`\.

1. Choose **Next**\.

   The **Specify stack details** page appears\.

1. To keep the default options, choose **Next**\.

   The **Advanced Options** page appears\.

1. To keep the default options, choose **Next**\.

1. To keep the default options, choose **Next**\.

1. On the **Review Changes** page, do the following:

   1. For **Capabilities**, select the ****I acknowledge that AWS CloudFormation might create IAM resources with custom names**** check box\.

   1. Choose **Submit**\.

1. Verify the update is successful\. The status of a successful operation shows `UPDATE_COMPLETE`\.

If the update fails, any new configuration specified in the update process will be rolled back\. The existing resource will still be usable\. For example, if you add network ACL rules numbered 18 and 19, but there were existing rules with same numbers, the update would return the following error: `Resource handler returned message: "The network acl entry identified by 18 already exists.` In this scenario you can modify the existing ACL rules to use a number lower than 18, then retry the update\.

### Configuring prerequisites to modify a Single\-AZ to a Multi\-AZ deployment manually<a name="custom-sqlserver-multiaz.modify-saztomaz-prereqs.manual"></a>

**Important**  
To simplify setup, we recommend that you use the latest AWS CloudFormation template file provided in the network setup instructions\. For more information, see [Configuring prerequisites to modify a Single\-AZ to a Multi\-AZ deployment using CloudFormation](#custom-sqlserver-multiaz.modify-saztomaz-prereqs.cf)\.

If you choose to configure the prerequisites manually, perform the following tasks\.

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. Choose **Endpoint**\. The **Create Endpoint** page appears\.

1. For **Service Category**, choose **AWS services**\.

1. In **Services**, search for *SQS*

1. In **VPC**, choose the VPC where your RDS Custom for SQL Server DB instance is deployed\.

1. In **Subnets**, choose the subnets where your RDS Custom for SQL Server DB instance is deployed\.

1. In **Security Groups**, choose the *\-vpc\-endpoint\-sg* group\.

1. For **Policy**, choose **Custom**

1. In your custom policy, replace the *AWS partition*, *Region*, *accountId*,and *IAM\-Instance\-role* with your own values\.

   ```
                        {
           "Version": "2012-10-17",
           "Statement": [
               {
                   "Condition": {
                       "StringLike": {
                           "aws:ResourceTag/AWSRDSCustom": "custom-sqlserver"
                       }
                   },
                   "Action": [
                       "SQS:SendMessage",
                       "SQS:ReceiveMessage",
                       "SQS:DeleteMessage",
                       "SQS:GetQueueUrl"
                   ],
                   "Resource": "arn:${AWS::Partition}:sqs:${AWS::Region}:${AWS::AccountId}:do-not-delete-rds-custom-*",
                   "Effect": "Allow",
                   "Principal": {
                       "AWS": "arn:${AWS::Partition}:iam::${AWS::AccountId}:role/{IAM-Instance-role}"
                   }
               }
           ]
       }
   ```

1.  Update the **Instance profile** with permission to access Amazon SQS\. Replace the *AWS partition*, *Region*, and *accountId* with your own values\.

   ```
                           {
       "Sid": "SendMessageToSQSQueue",
       "Effect": "Allow",
       "Action": [
         "SQS:SendMessage",
         "SQS:ReceiveMessage",
         "SQS:DeleteMessage",                                    
         "SQS:GetQueueUrl"
   
       ],
       "Resource": [
         {
           "Fn::Sub": "arn:${AWS::Partition}:sqs:${AWS::Region}:${AWS::AccountId}:do-not-delete-rds-custom-*"
         }
       ],
       "Condition": {
         "StringLike": {
           "aws:ResourceTag/AWSRDSCustom": "custom-sqlserver"
         }
       }
     } 
                           >
   ```

1. Update the Amazon RDS security group inbound and outbound rules to allow port 1120\.

   1. In **Security Groups**, choose the *\-rds\-custom\-instance\-sg* group\.

   1. For **Inbound Rules**, create a **Custom TCP** rule to allow port *1120* from the source *\-rds\-custom\-instance\-sg* group\.

   1. For **Outbound Rules**, create a **Custom TCP** rule to allow port *1120* to the destination *\-rds\-custom\-instance\-sg* group\.

1. Add a rule in your private network Access Control List \(ACL\) that allows TCP ports `0-65535` for the source subnet of the DB instance\.
**Note**  
When creating an **Inbound Rule** and **Outbound Rule**, take note of the highest existing **Rule number**\. The new rules you create must have a **Rule number** lower than 100 and not match any existing **Rule number**\.

   1. In **Network ACLs**, choose the *\-private\-network\-acl* group\.

   1. For **Inbound Rules**, create an **All TCP** rule to allow TCP ports `0-65535` with a source from *privatesubnet1* and *privatesubnet2*\.

   1. For **Outbound Rules**, create an **All TCP** rule to allow TCP ports `0-65535` to destination *privatesubnet1* and *privatesubnet2*\.

### Modify using the RDS console, AWS CLI, or RDS API\.<a name="custom-sqlserver-multiaz.modify-saztomaz-afterprereqs"></a>

After you've completed the prerequisites, you can modify an RDS Custom for SQL Server DB instance from a Single\-AZ to Multi\-AZ deployment using the RDS console, AWS CLI, or RDS API\.

#### Console<a name="custom-sqlserver-multiaz.modify-saztomaz.Console"></a>

**To modify an existing RDS Custom for SQL Server Single\-AZ to Multi\-AZ deployment**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the Amazon RDS console, choose **Databases**\.

   The **Databases** pane appears\.

1. Choose the RDS Custom for SQL Server DB instance that you want to modify\.

1. For **Actions**, choose **Convert to Multi\-AZ deployment**\.

1. On the **Confirmation** page, choose **Apply immediately** to apply the changes immediately\. Choosing this option doesn't cause downtime, but there is a possible performance impact\. Alternatively, you can choose to apply the update during the next maintenance window\. For more information, see [Using the Apply Immediately setting](Overview.DBInstance.Modifying.md#USER_ModifyInstance.ApplyImmediately)\.

1. On the **Confirmation** page, choose **Convert to Multi\-AZ**\.

#### AWS CLI<a name="custom-sqlserver-multiaz.modify-saztomaz.CLI"></a>

To convert to a Multi\-AZ DB instance deployment by using the AWS CLI, call the [modify\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) command and set the `--multi-az` option\. Specify the DB instance identifier and the values for other options that you want to modify\. For information about each option, see [Settings for DB instances](Overview.DBInstance.Modifying.md#USER_ModifyInstance.Settings)\. 

**Example**  
The following code modifies `mycustomdbinstance` by including the `--multi-az` option\. The changes are applied during the next maintenance window by using `--no-apply-immediately`\. Use `--apply-immediately` to apply the changes immediately\. For more information, see [Using the Apply Immediately setting](Overview.DBInstance.Modifying.md#USER_ModifyInstance.ApplyImmediately)\.   
For Linux, macOS, or Unix:  

```
aws rds modify-db-instance \
    --db-instance-identifier mycustomdbinstance \
    --multi-az \
    --no-apply-immediately
```
For Windows:  

```
aws rds modify-db-instance ^
    --db-instance-identifier mycustomdbinstance ^
    --multi-az  \ ^
    --no-apply-immediately
```

#### RDS API<a name="custom-sqlserver-multiaz.modify-saztomaz.API"></a>

To convert to a Multi\-AZ DB instance deployment with the RDS API, call the [ModifyDBInstance](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html) operation and set the `MultiAZ` parameter to true\.

## Modifying an RDS Custom for SQL Server Multi\-AZ deployment to a Single\-AZ deployment<a name="custom-sqlserver-multiaz.modify-maztosaz"></a>

You can modify an existing RDS Custom for SQL Server DB instance from a Multi\-AZ to a Single\-AZ deployment\. 

### Console<a name="custom-sqlserver-multiaz.modify-maztosaz.Console"></a>

**To modify an RDS Custom for SQL Server DB instance from a Multi\-AZ to Single\-AZ deployment\.**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the Amazon RDS console, choose **Databases**\.

   The **Databases** pane appears\.

1. Choose the RDS Custom for SQL Server DB instance that you want to modify\.

1. For **Multi\-AZ deployment**, choose **No**\.

1. On the **Confirmation** page, choose **Apply immediately** to apply the changes immediately\. Choosing this option doesn't cause downtime, but there is a possible performance impact\. Alternatively, you can choose to apply the update during the next maintenance window\. For more information, see [Using the Apply Immediately setting](Overview.DBInstance.Modifying.md#USER_ModifyInstance.ApplyImmediately)\.

1. On the **Confirmation** page, choose **Modify DB Instance**\.

### AWS CLI<a name="custom-sqlserver-multiaz.modify-maztosaz.CLI"></a>

To modify a Multi\-AZ deployment to a Single\-AZ deployment by using the AWS CLI, call the [modify\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) command and include the `--no-multi-az` option\. Specify the DB instance identifier and the values for other options that you want to modify\. For information about each option, see [Settings for DB instances](Overview.DBInstance.Modifying.md#USER_ModifyInstance.Settings)\. 

**Example**  
The following code modifies `mycustomdbinstance` by including the `--no-multi-az` option\. The changes are applied during the next maintenance window by using `--no-apply-immediately`\. Use `--apply-immediately` to apply the changes immediately\. For more information, see [Using the Apply Immediately setting](Overview.DBInstance.Modifying.md#USER_ModifyInstance.ApplyImmediately)\.   
For Linux, macOS, or Unix:  

```
aws rds modify-db-instance \
    --db-instance-identifier mycustomdbinstance \
    --no-multi-az  \
    --no-apply-immediately
```
For Windows:  

```
aws rds modify-db-instance ^
    --db-instance-identifier mycustomdbinstance ^
    --no-multi-az \ ^
    --no-apply-immediately
```

### RDS API<a name="custom-sqlserver-multiaz.modify-maztosaz.API"></a>

To modify a Multi\-AZ deployment to a Single\-AZ deployment by using the RDS API, call the [ModifyDBInstance](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html) operation and set the `MultiAZ` parameter to `false`\.

## Failover process for an RDS Custom for SQL Server Multi\-AZ deployment<a name="custom-sqlserver-multiaz.failover"></a>

If a planned or unplanned outage of your DB instance results from an infrastructure defect, Amazon RDS automatically switches to a standby replica in another Availability Zone if you have turned on Multi\-AZ\. The time that it takes for the failover to complete depends on the database activity and other conditions at the time that the primary DB instance became unavailable\. Failover times are typically 60 – 120 seconds\. However, large transactions or a lengthy recovery process can increase failover time\. When the failover is complete, it can take additional time for the RDS console to show the new Availability Zone\.

**Note**  
You can force a failover manually when you reboot a DB instance with failover\. For more information on rebooting a DB instance, see [Rebooting a DB instance](USER_RebootInstance.md) 

Amazon RDS handles failovers automatically so you can resume database operations as quickly as possible without administrative intervention\. The primary DB instance switches over automatically to the standby replica if any of the conditions described in the following table occurs\. You can view these failover reasons in the RDS event log\.


****  

| Failover reason | Description | 
| --- | --- | 
| `The operating system for the RDS Custom for SQL Server Multi-AZ DB instance is being patched in an offline operation` | A failover was triggered during the maintenance window for an OS patch or a security update\. For more information, see [Maintaining a DB instance](USER_UpgradeDBInstance.Maintenance.md)\.  | 
| `The primary host of the RDS Custom for SQL Server Multi-AZ DB instance is unhealthy.` | The Multi\-AZ DB instance deployment detected an impaired primary DB instance and failed over\. | 
| `The primary host of the RDS Custom for SQL Server Multi-AZ DB instance is unreachable due to loss of network connectivity.` | RDS monitoring detected a network reachability failure to the primary DB instance and triggered a failover\. | 
| `The RDS Custom for SQL Server Multi-AZ DB instance was modified by the customer.` | A DB instance modification triggered a failover\. For more information, see [Modifying an RDS Custom for SQL Server DB instance](custom-managing-sqlserver.md#custom-managing.modify-sqlserver)\.  | 
| `The storage volume of the primary host of the RDS Custom for SQL Server Multi-AZ DB instance experienced a failure.` | The Multi\-AZ DB instance deployment detected a storage issue on the primary DB instance and failed over\. | 
| `The user requested a failover of the RDS Custom for SQL Server Multi-AZ DB instance.` | The RDS Custom for SQL Server Multi\-AZ DB instance was rebooted with failover\. For more information, see [Rebooting a DB instance](USER_RebootInstance.md)\. | 
| `The RDS Custom for SQL Server Multi-AZ primary DB instance is busy or unresponsive.` | The primary DB instance is unresponsive\. We recommend that you try the following steps:  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/custom-sqlserver-multiaz.html)  | 

To determine if your Multi\-AZ DB instance has failed over, you can do the following:
+ Set up DB event subscriptions to notify you by email or SMS that a failover has been initiated\. For more information about events, see [Working with Amazon RDS event notification](USER_Events.md)\.
+ View your DB events by using the RDS console or API operations\.
+ View the current state of your RDS Custom for SQL Server Multi\-AZ DB instance deployment by using the RDS console, CLI, or API operations\.

## Time to live \(TTL\) settings with applications using an RDS Custom for SQL Server Multi\-AZ deployment<a name="custom-sqlserver-multiaz.ttldns"></a>

The failover mechanism automatically changes the Domain Name System \(DNS\) record of the DB instance to point to the standby DB instance\. As a result, you need to re\-establish any existing connections to your DB instance\. Ensure that any DNS cache time\-to\-live \(TTL\) configuration value is low, and validate that your application will not cache DNS for an extended time\. A high TTL value might prevent your application from quickly reconnecting to the DB instance after failover\.