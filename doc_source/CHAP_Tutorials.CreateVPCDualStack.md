# Tutorial: Create a virtual private cloud \(VPC\) for use with a DB instance \(dual\-stack mode\)<a name="CHAP_Tutorials.CreateVPCDualStack"></a>

A common scenario includes a DB instance in a virtual private cloud \(VPC\) based on the Amazon VPC service\. This VPC shares data with a public Amazon EC2 instance that is running in the same VPC\. In this tutorial, you create the VPC for this scenario that works with a database running in dual\-stack mode\.

The following diagram shows this scenario\.

 

![\[VPC scenario for dual-stack mode\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/con-VPC-sec-grp-dual-stack.png)

For information about other scenarios, see [Scenarios for accessing a DB instance in a VPC](USER_VPC.Scenarios.md)\.

Because your DB instance needs to be available only to your Amazon EC2 instance, and not to the public internet, you create a VPC with both public and private subnets\. The Amazon EC2 instance is hosted in the public subnet, so that it can reach the public internet\. The DB instance is hosted in a private subnet\. The Amazon EC2 instance can connect to the DB instance because it's hosted within the same VPC\. However, the DB instance is not available to the public internet, providing greater security\.

To create a DB instance that uses dual\-stack mode, specify **Dual\-stack mode** for the **Network type** setting\. You can also modify a DB instance with the same setting\. For more information, see [Creating an Amazon RDS DB instance](USER_CreateDBInstance.md) and [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)\.

This tutorial describes configuring a VPC for Amazon RDS DB instances\. For more information about Amazon VPC, see [Amazon VPC Getting Started Guide](https://docs.aws.amazon.com/AmazonVPC/latest/GettingStartedGuide/) and [Amazon VPC User Guide](https://docs.aws.amazon.com/vpc/latest/userguide/)\. 

## Create a VPC with private and public subnets<a name="CHAP_Tutorials.CreateVPCDualStack.VPCAndSubnets"></a>

Use the following procedure to create a VPC with both public and private subnets\. 

**To create a VPC and subnets**

1. If you don't have an Elastic IP address to associate with a network address translation \(NAT\) gateway, allocate one now\. A NAT gateway is required for this tutorial\. If you have an available Elastic IP address, move on to the next step\.

   1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

   1. In the upper\-right corner of the AWS Management Console, choose the AWS Region to allocate your Elastic IP address in\. The Region of your Elastic IP address should be the same as the Region where you want to create your VPC\. This example uses the US East \(Ohio\) Region\.

   1. In the navigation pane, choose **Elastic IPs**\.

   1. Choose **Allocate Elastic IP address**\.

   1. If the console shows the **Network Border Group** field, keep the default value for it\.

   1. For **Public IPv4 address pool**, choose **Amazon's pool of IPv4 addresses**\.

   1. Choose **Allocate**\.

      Note the allocation ID of the new Elastic IP address because you need this information when you create your VPC\.

   For more information about Elastic IP addresses, see [Elastic IP addresses](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/elastic-ip-addresses-eip.html) in the *Amazon EC2 User Guide*\. For more information about NAT gateways, see [NAT gateways](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html) in the *Amazon VPC User Guide*\.

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the upper\-right corner of the AWS Management Console, choose the Region to create your VPC in\. This example uses the US East \(Ohio\) Region\.

1. In the upper\-left corner, choose **VPC Dashboard**\. To begin creating a VPC, choose **Launch VPC Wizard**\.

1. On the **Step 1: Select a VPC Configuration** page, choose **VPC with Public and Private Subnets**, and then choose **Select**\.

1. On the **Step 2: VPC with Public and Private Subnets** page, set these values:
   + **IPv4 CIDR block:** **10\.0\.0\.0/16**
   + **IPv6 CIDR block:** **Amazon\-provided IPv6 CIDR block**
   + **VPC name:** **tutorial\-dual\-stack\-vpc**
   + **Public subnet's IPv4 CIDR:** **10\.0\.0\.0/24**
   + **Public subnet's IPv6 CIDR:** Choose **Specify a custom IPv6 CIDR** and then enter **00** for the end of the custom IPv6 CIDR
   + **Availability Zone:** **us\-east\-2a**
   + **Public subnet name:** **Tutorial dual\-stack public**
   + **Private subnet's IPv4 CIDR:** **10\.0\.1\.0/24**
   + **Private subnet's IPv6 CIDR:** Choose **Specify a custom IPv6 CIDR** and then enter **01** for the end of the custom IPv6 CIDR
   + **Availability Zone:** **us\-east\-2b**
   + **Private subnet name:** **Tutorial dual\-stack private 1** 
   + **Elastic IP Allocation ID:** An Elastic IP address to associate with the NAT gateway
   + **Service endpoints:** Skip this field
   + **Enable DNS hostnames:** **Yes**
   + **Hardware tenancy:** **Default**

1. Choose **Create VPC**\.

## Create additional subnets<a name="CHAP_Tutorials.CreateVPCDualStack.AdditionalSubnets"></a>

You must have either two private subnets or two public subnets available to create a DB subnet group for a DB instance to use in a VPC\. Because the DB instance for this tutorial is private, add a second private subnet to the VPC\. 

**To create an additional subnet**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. Choose **VPC Dashboard**, choose **Subnets**, and then choose **Create subnet** to add the second private subnet to your VPC\.

1. On the **Create subnet** page, set these values: 
   + **VPC ID:** Choose the VPC that you created in the previous step, for example **vpc\-*identifier* \(tutorial\-dual\-stack\-vpc\)**
   + **Subnet name:** **Tutorial dual\-stack private 2**
   + **Availability Zone:** **us\-east\-2c** 
**Note**  
Choose an Availability Zone that is different from the one that you chose for the first private subnet\.
   + **IPv6\-only:** Keep this clear
   + **IPv4 CIDR block:** **10\.0\.2\.0/24**
   + **IPv6 CIDR block:** Choose **Custom IPv6** and then enter **02** for the end of the custom IPv6 CIDR 

1. Choose **Create subnet**\.

1. Do the following to make sure that the second private subnet that you created uses the same route table as the first private subnet:

   1. Choose **VPC Dashboard**, choose **Subnets**, and then choose the first private subnet that you created for the VPC, **Tutorial dual\-stack private 1**\. 

   1. Below the list of subnets, choose the **Route table** tab, and note the value for **Route Table**, for example **rtb\-98b613fd**\. 

   1. In the list of subnets, clear the option for the first private subnet\.

   1. In the list of subnets, choose the second private subnet **Tutorial private 2**, and choose the **Route table** tab\. 

   1. If the current route table isn't the same as the route table for the first private subnet, choose **Edit route table association**\. For **Route table ID**, choose the route table that you noted earlier, for example **rtb\-98b613fd**\. To save your selection, choose **Save**\.

## Create a VPC security group for a public Amazon EC2 instance<a name="CHAP_Tutorials.CreateVPCDualStack.SecurityGroupEC2"></a>

Next, you create a security group for public access\. To connect to public instances in your VPC, add inbound rules to your VPC security group that allow traffic to connect from the internet\. 

**To create a VPC security group**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. Choose **VPC Dashboard**, choose **Security Groups**, and then choose **Create security group**\. 

1. On the **Create security group** page, set these values: 
   + **Security group name:** **tutorial\-dual\-stack\-securitygroup**
   + **Description:** **Tutorial Dual\-Stack Security Group**
   + **VPC:** Choose the VPC that you created earlier, for example: **vpc\-*identifier* \(tutorial\-dual\-stack\-vpc\)** 

1. Add inbound rules to the security group\.

   1. Determine the IP address to use to connect to instances in your VPC\. 

      To determine your public IP address, in a different browser window or tab use the service at [https://checkip\.amazonaws\.com](https://checkip.amazonaws.com)\. An example of an IPv4 IP address is `203.0.113.25/32`\. An example of an IPv6 IP address is `2001:DB8::/32`\.

      If you are connecting through an internet service provider \(ISP\) or from behind your firewall without a static IP address, find out the range of IP addresses used by client computers\.
**Warning**  
If you use `0.0.0.0/0` for IPv4 or `::0` for IPv6, you enable all IP addresses to access your public instances\. This approach is acceptable for a short time in a test environment, but it's unsafe for production environments\. In production, authorize only a specific IP address or range of addresses to access your instances\.

   1. In the **Inbound rules** section, choose **Add rule**\.

   1. Set the following values for your new inbound rule to allow Secure Shell \(SSH\) access to your Amazon EC2 instance\. If you do this, you can connect to your EC2 instance to install SQL clients and other applications\. Specify an IP address to allow to access your EC2 instance:
      + **Type:** `SSH`
      + **Source:** The IP address or range from step a\. An example of an IPv4 IP address is `203.0.113.25/32`\. An example of an IPv6 IP address is `2001:DB8::/32`\.

1. Choose **Create security group** to create the security group\.

   Note the security group ID because you need it later in this tutorial\.

## Create a VPC security group for a private DB instance<a name="CHAP_Tutorials.CreateVPCDualStack.SecurityGroupDB"></a>

To keep your DB instance private, create a second security group for private access\. To connect to private instances in your VPC, add inbound rules to your VPC security group that allow traffic from your Amazon EC2 instance only\. 

**To create a VPC security group**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. Choose **VPC Dashboard**, choose **Security Groups**, and then choose **Create security group**\.

1. On the **Create security group** page, set these values:
   + **Security group name:** **tutorial\-dual\-stack\-db\-securitygroup**
   + **Description:** **Tutorial Dual\-Stack DB Instance Security Group**
   + **VPC:** Choose the VPC that you created earlier, for example: **vpc\-*identifier* \(tutorial\-dual\-stack\-vpc\)**

1. Add inbound rules to the security group:

   1. In the **Inbound rules** section, choose **Add rule**\.

   1. Set the following values for your new inbound rule to allow MySQL traffic on port 3306 from your Amazon EC2 instance\. If you do this, you can connect from your EC2 instance to your DB instance to store and retrieve data from your EC2 instance to your database\. 
      + **Type:** **MySQL/Aurora**
      + **Source:** The identifier of the `tutorial-dual-stack-securitygroup` security group that you created previously in this tutorial, for example **sg\-9edd5cfb**\.

1. To create the security group, choose **Create security group**\.

## Create a DB subnet group<a name="CHAP_Tutorials.CreateVPCDualStack.DBSubnetGroup"></a>

A *DB subnet group* is a collection of subnets that you create in a VPC and that you then designate for your DB instances\. By using a DB subnet group, you can specify a particular VPC when creating DB instances\. To create a DB subnet group that is `DUAL` compatible, all subnets must be `DUAL` compatible\. To be `DUAL` compatible, a subnet must have an IPv6 CIDR associated with it\.

**To create a DB subnet group**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

   Make sure that you connect to the Amazon RDS console, not to the Amazon VPC console\.

1. In the navigation pane, choose **Subnet groups**\.

1. Choose **Create DB subnet group**\.

1. On the **Create DB subnet group** page, set these values in **Subnet group details**:
   + **Name:** **tutorial\-dual\-stack\-db\-subnet\-group**
   + **Description:** **Tutorial Dual\-Stack DB Subnet Group**
   + **VPC:** **tutorial\-dual\-stack\-vpc \(vpc\-*identifier*\)** 

1. In the **Add subnets** section, choose values for the **Availability Zones** and **Subnets** options\.

   For this tutorial, choose **us\-east\-2b** and **us\-east\-2c** for the **Availability Zones**\. For **Subnets**, choose the subnets that have IPv4 and IPv6 CIDR blocks associated with them\. For this tutorial, choose the subnets for IPv4 CIDR block 10\.0\.1\.0/24 and 10\.0\.2\.0/24\.
**Note**  
If you have enabled a Local Zone, you can choose an Availability Zone group on the **Create DB subnet group** page\. In this case, choose values for the **Availability Zone group**, **Availability Zones**, and **Subnets** options\.

1. Choose **Create**\. 

Your new DB subnet group appears in the DB subnet groups list on the RDS console\. You can choose the DB subnet group to see its details\. These include the supported addressing protocols and all of the subnets associated with the group and the network type supported by the DB subnet group\.

## Create an Amazon EC2 instance in dual\-stack mode<a name="CHAP_Tutorials.CreateVPCDualStack.CreateEC2Instance"></a>

To create an Amazon EC2 instance, follow the instructions in [Launch an instance using the Launch Instance Wizard](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/launching-instance.html) in the *Amazon EC2 User Guide for Linux Instances*\.

On the **Configure Instance Details** page, shown following, set these values and keep the other values as their defaults:
+ **Network:** Choose the VPC with both public and private subnets that you chose for the DB instance, such as **vpc\-*identifier* \| tutorial\-dual\-stack\-vpc** created in [Create a VPC with private and public subnets](#CHAP_Tutorials.CreateVPCDualStack.VPCAndSubnets)\.
+ **Subnet:** Choose an existing public subnet, such as **subnet\-*identifier* \| Tutorial dual\-stack public \| us\-east\-2a** created in [ Create a VPC security group for a public Amazon EC2 instance](#CHAP_Tutorials.CreateVPCDualStack.SecurityGroupEC2)\.
+ **Auto\-assign Public IP:** Choose **Enable**\.

On the **Configure Security Group** page, shown following, choose **Select an existing security group**\. Then choose an existing security group, such as **tutorial\-dual\-stack\-securitygroup** created in [ Create a VPC security group for a public Amazon EC2 instance](#CHAP_Tutorials.CreateVPCDualStack.SecurityGroupEC2)\. Make sure that the security group that you choose includes inbound rules for Secure Shell \(SSH\)\. 

## Create a DB instance in dual\-stack mode<a name="CHAP_Tutorials.CreateVPCDualStack.CreateDBInstance"></a>

In this step, you create an Amazon RDS DB instance that runs in dual\-stack mode\.

**To create a MySQL DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the upper\-right corner of the console, choose the AWS Region where you want to create the DB instance\. This example uses the US East \(Ohio\) Region\.

1. In the navigation pane, choose **Databases**\.

1. Choose **Create database**\.

1. On the **Create database** page, shown following, make sure that the **Standard create** option is chosen, and then choose **MySQL**\. 

1. In the **Connectivity** section, set these values:
   + **Network type** – Choose **Dual\-stack mode**  
![\[Network type section in the console with Dual-stack mode selected\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/dual-stack-mode.png)
   + **Virtual private cloud \(VPC\)** – Choose an existing VPC with both public and private subnets, such as **tutorial\-dual\-stack\-vpc** \(vpc\-*identifier*\) created in [Create a VPC with private and public subnets](#CHAP_Tutorials.CreateVPCDualStack.VPCAndSubnets)

     The VPC must have subnets in different Availability Zones\.
   + **Subnet group** – The DB subnet group for the VPC, such as **tutorial\-dual\-stack\-db\-subnet\-group** created in [Create a DB subnet group](#CHAP_Tutorials.CreateVPCDualStack.DBSubnetGroup)
   + **Public access** – **No**
   + **VPC security group** – **Choose existing**
   + **Existing VPC security groups** – Choose an existing VPC security group that is configured for private access, such as **tutorial\-dual\-stack\-db\-securitygroup** created in [ Create a VPC security group for a private DB instance](#CHAP_Tutorials.CreateVPCDualStack.SecurityGroupDB)\.

     Remove other security groups, such as the default security group, by choosing the **X** associated with each\.
   + **Availability Zone** – **No preference**
   + Open **Additional configuration**, and make sure **Database port** uses the default value **3306**\.

1. For the remaining sections, specify your DB instance settings\. For information about each setting, see [Settings for DB instances](USER_CreateDBInstance.md#USER_CreateDBInstance.Settings)\. 

## Connect to your Amazon EC2 instance and DB instance<a name="CHAP_Tutorials.CreateVPCDualStack.Connect"></a>

After your Amazon EC2 instance and DB instance are created in dual\-stack mode, you can connect to each one using the IPv6 protocol\. To connect to an Amazon EC2 instance using the IPv6 protocol, follow the instructions in [Connect to your Linux instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstances.html) in the *Amazon EC2 User Guide for Linux Instances*\. To connect to your DB instance, follow the instructions in [Connecting to an Amazon RDS DB instance](CHAP_CommonTasks.Connect.md)\.

## Deleting the VPC<a name="CHAP_Tutorials.CreateVPCDualStack.Delete"></a>

After you create the VPC and other resources for this tutorial, you can delete them if they are no longer needed\.

If you added resources in the VPC that you created for this tutorial, such as Amazon EC2 instances or Amazon RDS DB instances, you might need to delete these resources before you can delete the VPC\. For more information, see [Delete your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/working-with-vpcs.html#VPC_Deleting) in the *Amazon VPC User Guide*\.

**To delete a VPC and related resources**

1. Delete the DB subnet group:

   1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

   1. In the navigation pane, choose **Subnet groups**\.

   1. Select the DB subnet group to delete, such as **tutorial\-db\-subnet\-group**\.

   1. Choose **Delete**, and then choose **Delete** in the confirmation window\.

1. Note the VPC ID:

   1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

   1. Choose **VPC Dashboard**, and then choose **VPCs**\.

   1. In the list, identify the VPC you created, such as **tutorial\-dual\-stack\-vpc**\.

   1. Note the **VPC ID** value of the VPC that you created\. You need this VPC ID in subsequent steps\.

1. Delete the security groups:

   1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

   1. Choose **VPC Dashboard**, and then choose **Security Groups**\.

   1. Select the security group for the Amazon RDS DB instance, such as **tutorial\-dual\-stack\-db\-securitygroup**\.

   1. For **Actions**, choose **Delete security groups**, and then choose **Delete** on the confirmation page\.

   1. On the **Security Groups** page, select the security group for the Amazon EC2 instance, such as **tutorial\-dual\-stack\-securitygroup**\.

   1. For **Actions**, choose **Delete security groups**, and then choose **Delete** on the confirmation page\.

1. Delete the NAT gateway:

   1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

   1. Choose **VPC Dashboard**, and then choose **NAT Gateways**\.

   1. Select the NAT gateway of the VPC that you created\. Use the VPC ID to identify the correct NAT gateway\.

   1. For **Actions**, choose **Delete NAT gateway**\.

   1. On the confirmation page, enter **delete**, and then choose **Delete**\.

1. Delete the VPC:

   1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

   1. Choose **VPC Dashboard**, and then choose **VPCs**\.

   1. Select the VPC that you want to delete, such as **tutorial\-dual\-stack\-vpc**\.

   1. For **Actions**, choose **Delete VPC**\.

      The confirmation page shows other resources that are associated with the VPC that will also be deleted, including the subnets associated with it\.

   1. On the confirmation page, enter **delete**, and then choose **Delete**\.

1. Release the Elastic IP addresses:

   1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

   1. Choose **EC2 Dashboard**, and then choose **Elastic IPs**\.

   1. Select the Elastic IP address that you want to release\.

   1. For **Actions**, choose **Release Elastic IP addresses**\.

   1. On the confirmation page, choose **Release**\.