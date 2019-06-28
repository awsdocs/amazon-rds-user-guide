# Setting Up Amazon RDS to Host Tools and Third\-Party Software for Oracle<a name="Oracle.Resources.Shared"></a>

You can use Amazon RDS to host an Oracle DB instance that supports software and components such as the following: 
+ Siebel Customer Relationship Management \(CRM\)
+ Oracle Fusion Middleware Metadata — installed by the Repository Creation Utility \(RCU\)

The following procedures help you create an Oracle DB instance on Amazon RDS that you can use to host additional software and components for Oracle\. 

## Creating an Amazon VPC for Use with an Oracle Database<a name="Oracle.Resources.Shared.VPC"></a>

In the following procedure, you create an Amazon VPC, a private subnet, and a security group\. Because your Amazon RDS DB instance needs to be available only to your middle\-tier components, and not to the public Internet, your Amazon RDS DB instance is hosted in a private subnet, providing greater security\. 

**To create an Amazon VPC**

1. Sign in to the AWS Management Console and open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the top\-right corner of the AWS Management Console, choose the AWS Region for your VPC\. This example uses the US West \(Oregon\) region\. 

1. In the upper\-left corner, choose **VPC Dashboard**, and then choose **Start VPC Wizard**\. 

1. On the page **Step 1: Select a VPC Configuration**, choose **VPC with Public and Private Subnets**, and then choose **Select**\. 

1. On the page **Step 2: VPC with Public and Private Subnets**, shown following, set these values:  
****    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Oracle.Resources.Shared.html)  
![\[VPC with Public and Private Subnets Wizard\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/Siebel-VPC.png)

1. Choose **Create VPC**\. 

An Amazon RDS DB instance in a VPC requires at least two private subnets or at least two public subnets, to support Multi\-AZ deployment\. For more information about working with multiple Availability Zones, see [Regions and Availability Zones](Concepts.RegionsAndAvailabilityZones.md)\. Because your database is private, add a second private subnet to your VPC\. 

**To create an additional subnet**

1. Sign in to the AWS Management Console and open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the top\-right corner of the AWS Management Console, confirm that you are in the correct AWS Region for your VPC\. 

1. In the upper\-left corner, choose **VPC Dashboard**, choose **Subnets**, and then choose **Create Subnet**\. 

1. On the **Create Subnet** page, set the following values\.   
****    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Oracle.Resources.Shared.html)

1. Choose **Yes, Create**\.

Both private subnets must use the same route table\. In the following procedure, you check to make sure the route tables match, and if not you edit one of them\. 

**To ensure the subnets use the same route table\.**

1. Sign in to the AWS Management Console and open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the top\-right corner of the AWS Management Console, confirm that you are in the correct AWS Region for your VPC\. 

1. In the upper\-left corner, choose **VPC Dashboard**, choose **Subnets**, and then choose your first private subnet, for example **subnet\-private\-1**\. 

1. At the bottom of the console, choose the **Route Table** tab, shown following\.   
![\[Route Table information\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/Siebel-RouteTable.png)

1. Make a note of the route table, for example `rtb-0d9fc668`\. 

1. In the list of subnets, choose the second private subnet, for example **subnet\-private\-2**\. 

1. At the bottom of the console, choose the **Route Table** tab\. 

1. If the route table for the second subnet is not the same as the route table for the first subnet, edit it to match: 

   1. Choose **Edit**\.

   1. For **Change to**, choose the route table that matches your first subnet\.

   1. Choose **Save**\.

A security group acts as a virtual firewall for your DB instance to control inbound and outbound traffic\. In the following procedure, you create a security group for your DB instance\. For more information about security groups, see [Security Groups for Your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html)\. 

**To create a VPC security group for a Private Amazon RDS DB Instance**

1. Sign in to the AWS Management Console and open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the top\-right corner of the AWS Management Console, confirm that you are in the correct AWS Region for your VPC\. 

1. In the upper\-left corner, choose **VPC Dashboard**, choose **Security Groups**, and then choose **Create Security Group**\. 

1. On the page **Create Security Group**, set the following values\.  
****    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Oracle.Resources.Shared.html)

1. Choose **Yes, Create**\.

In the following procedure, you add rules to your security group to control inbound traffic to your DB instance\. For more information about inbound rules, see [Security Group Rules](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html#SecurityGroupRules)\. 

**To add inbound rules to the security group**

1. Sign in to the AWS Management Console and open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the top\-right corner of the AWS Management Console, confirm that you are in the correct AWS Region for your VPC\. 

1. In the upper\-left corner, choose **VPC Dashboard**, choose **Security Groups**, and then choose your security group, for example **sgdb\-1**\. 

1. At the bottom of the console, choose the **Inbound Rules** tab, and then choose **Edit**\. 

1. Set these values, as shown following\.  
****    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Oracle.Resources.Shared.html)  
![\[Inbound Rules information\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/Siebel-InboundRules.png)

1. Choose **Save**\.

## Creating an Oracle DB Instance<a name="Oracle.Resources.Shared.Database.RDS"></a>

You can use Amazon RDS to host an Oracle DB instance\. In the following procedure, you create the Oracle DB instance\. 

**To launch an Oracle DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the top\-right corner of the AWS Management Console, choose the AWS Region for your DB instance\. Choose the same AWS Region as your VPC\. 

1. Choose **Databases** and then choose **Create database**\. 

1. On the page **Select engine**, choose **Oracle**, and then choose **Oracle Database Enterprise Edition**\.   
![\[Engine selection\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/OracleLaunchEE.png)

1. Choose **Next**\.

1. On the page **Choose use case**, choose **Production**, and then choose **Next**\. 
**Note**  
For a DB instance for development and testing, you can choose **Dev/Test**\.

1. On the page **Specify DB details**, shown following, set the following values\.  
****    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Oracle.Resources.Shared.html)  
![\[Specify DB Details form\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/Siebel-DB.png)

1. Choose **Next**\.

1. On the page **Configure advanced settings**, shown following, set the following values\.  
****    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Oracle.Resources.Shared.html)

1.  On the final page of the wizard, choose **View DB instance details**\. 

On the RDS console, the details for the new DB instance appear\. The DB instance has a status of **creating** until the DB instance is created and ready for use\. When the state changes to **available**, you can connect to the DB instance\. Depending on the DB instance class and storage allocated, it could take several minutes for the new instance to be available\. 

## Additional Amazon RDS Interfaces<a name="Oracle.Resources.Shared.CLIAPI"></a>

In the preceding procedures, we use the AWS Management Console to perform tasks\. Amazon Web Services also provides the AWS Command Line Interface \(AWS CLI\), and an application programming interface \(API\)\. You can use the AWS CLI or the API to automate many of the tasks for managing Amazon RDS, including tasks to manage an Oracle DB instance with Amazon RDS\. 

For more information, see [AWS Command Line Interface Reference for Amazon RDS](https://docs.aws.amazon.com/cli/latest/reference/rds/index.html) and [Amazon RDS API Reference](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/)\. 

## Related Topics<a name="w5aac30c95b7c15"></a>
+ [Setting Up for Amazon RDS](CHAP_SettingUp.md)
+ [Using the Oracle Repository Creation Utility on Amazon RDS for Oracle](Oracle.Resources.RCU.md)
+ [Installing a Siebel Database on Oracle on Amazon RDS](Oracle.Resources.Siebel.md)
+ [Scenarios for Accessing a DB Instance in a VPC](USER_VPC.Scenarios.md)
+ [Connecting to a DB Instance Running the Oracle Database Engine](USER_ConnectToOracleInstance.md)