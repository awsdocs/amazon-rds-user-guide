# Working with a DB Instance in a VPC<a name="USER_VPC.WorkingWithRDSInstanceinaVPC"></a>

Unless you are working with a legacy DB instance, your DB instance is in a virtual private cloud \(VPC\)\. A VPC is a virtual network that is logically isolated from other virtual networks in the AWS Cloud\. Amazon VPC lets you launch AWS resources, such as an Amazon RDS DB instance or Amazon EC2 instance, into a VPC\. The VPC can either be a default VPC that comes with your account or one that you create\. All VPCs are associated with your AWS account\. 

Your default VPC has three subnets you can use to isolate resources inside the VPC\. The default VPC also has an internet gateway that can be used to provide access to resources inside the VPC from outside the VPC\. 

For a list of scenarios involving Amazon RDS DB instances in a VPC  and outside of a VPC, see [Scenarios for Accessing a DB Instance in a VPC](USER_VPC.Scenarios.md)\. 

For a tutorial that shows you how to create a VPC that you can use with a common Amazon RDS scenario, see [Tutorial: Create an Amazon VPC for Use with a DB Instance](CHAP_Tutorials.WebServerDB.CreateVPC.md)\. 

To learn how to work with DB instances inside a VPC, see the following:

**Topics**
+ [Working with a DB Instance in a VPC](#Overview.RDSVPC.Create)
+ [Working with DB Subnet Groups](#USER_VPC.Subnets)
+ [Hiding a DB Instance in a VPC from the Internet](#USER_VPC.Hiding)
+ [Creating a DB Instance in a VPC](#USER_VPC.InstanceInVPC)

## Working with a DB Instance in a VPC<a name="Overview.RDSVPC.Create"></a>

Here are some tips on working with a DB instance in a VPC:
+ Your VPC must have at least two subnets\. These subnets must be in two different Availability Zones in the AWS Region where you want to deploy your DB instance\. A subnet is a segment of a VPC's IP address range that you can specify and that lets you group instances based on your security and operational needs\. 
**Note**  
The DB subnet group for a Local Zone can have only one subnet\.
+ If you want your DB instance in the VPC to be publicly accessible, you must enable the VPC attributes *DNS hostnames* and *DNS resolution*\. 
+ Your VPC must have a DB subnet group that you create \(for more information, see the next section\)\. You create a DB subnet group by specifying the subnets you created\. Amazon RDS uses that DB subnet group and your preferred Availability Zone to choose a subnet and an IP address within that subnet to assign to your DB instance\.
+ Your VPC must have a VPC security group that allows access to the DB instance\. 
+ The CIDR blocks in each of your subnets must be large enough to accommodate spare IP addresses for Amazon RDS to use during maintenance activities, including failover and compute scaling\. 
+  A VPC can have an *instance tenancy* attribute of either *default* or *dedicated*\. All default VPCs have the instance tenancy attribute set to default, and a default VPC can support any DB instance class\. 

  If you choose to have your DB instance in a dedicated VPC where the instance tenancy attribute is set to dedicated, the DB instance class of your DB instance must be one of the approved Amazon EC2 dedicated instance types\. For example, the m3\.medium EC2 dedicated instance corresponds to the db\.m3\.medium DB instance class\. For information about instance tenancy in a VPC, see [Dedicated Instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/dedicated-instance.html) in the *Amazon Elastic Compute Cloud User Guide*\. 

  For more information about the instance types that can be in a dedicated instance, see [Amazon EC2 Dedicated Instances](https://aws.amazon.com/ec2/purchasing-options/dedicated-instances/) on the EC2 pricing page\. 
**Note**  
When you set the instance tenancy attribute to dedicated for an Amazon RDS DB instance, it doesn't guarantee that the DB instance will run on a dedicated host\.
+ When an option group is assigned to a DB instance, it is linked to the supported platform the DB instance is on, either VPC or EC2\-Classic \(non\-VPC\)\. Furthermore, if a DB instance is in a VPC, the option group associated with the DB instance is linked to that VPC\. This linkage means that you cannot use the option group assigned to a DB instance if you attempt to restore the DB instance into a different VPC or onto a different platform\.
+ If you restore a DB instance into a different VPC or onto a different platform, you must either assign the default option group to the DB instance, assign an option group that is linked to that VPC or platform, or create a new option group and assign it to the DB instance\. With persistent or permanent options, such as Oracle TDE, you must create a new option group that includes the persistent or permanent option when restoring a DB instance into a different VPC\.

## Working with DB Subnet Groups<a name="USER_VPC.Subnets"></a>

Subnets are segments of a VPC's IP address range that you designate to group your resources based on security and operational needs\. A DB subnet group is a collection of subnets \(typically private\) that you create in a VPC and that you then designate for your DB instances\. A DB subnet group allows you to specify a particular VPC when creating DB instances using the CLI or API; if you use the console, you can just choose the VPC and subnets you want to use\. 

Each DB subnet group should have subnets in at least two Availability Zones in a given AWS Region\. When creating a DB instance in a VPC, you must choose a DB subnet group\. Amazon RDS uses that DB subnet group and your preferred Availability Zone to choose a subnet and an IP address within that subnet to associate with your DB instance\. If the primary DB instance of a Multi\-AZ deployment fails, Amazon RDS can promote the corresponding standby and subsequently create a new standby using an IP address of the subnet in one of the other Availability Zones\. 

The subnets in a DB subnet group are either public or private\. They can't be a mix of both public and private subnets\. The subnets are public or private, depending on the configuration that you set for their network access control lists \(network ACLs\) and routing tables\.

**Note**  
The DB subnet group for a Local Zone can have only one subnet\.

When Amazon RDS creates a DB instance in a VPC, it assigns a network interface to your DB instance by using an IP address from your DB subnet group\. However, we strongly recommend that you use the DNS name to connect to your DB instance because the underlying IP address changes during failover\. 

**Note**  
For each DB instance that you run in a VPC, make sure to reserve at least one address in each subnet in the DB subnet group for use by Amazon RDS for recovery actions\. 

## Hiding a DB Instance in a VPC from the Internet<a name="USER_VPC.Hiding"></a>

One common Amazon RDS scenario is to have a VPC in which you have an EC2 instance with a public\-facing web application and a DB instance with a database that is not publicly accessible\. For example, you can create a VPC that has a public subnet and a private subnet\. Amazon EC2 instances that function as web servers can be deployed in the public subnet, and the DB instances are deployed in the private subnet\. In such a deployment, only the web servers have access to the DB instances\. For an illustration of this scenario, see [A DB Instance in a VPC Accessed by an EC2 Instance in the Same VPC](USER_VPC.Scenarios.md#USER_VPC.Scenario1)\. 

When you launch a DB instance inside a VPC, the DB instance has a private IP address for traffic inside the VPC\. This private IP address isn't publicly accessible\. You can use the *Public accessibility* option to designate whether the DB instance also has a public IP address in addition to the private IP address\. If the DB instance is designated as publicly accessible, its DNS endpoint resolves to the private IP address from within the DB instance's VPC, and to the public IP address from outside of the DB instance's VPC\. Access to the DB instance is ultimately controlled by the security group it uses, and that public access is not permitted if the security group assigned to the DB instance doesn't permit it\.

You can modify a DB instance to turn on or off public accessibility by modifying the *Public accessibility* option\. This parameter is modified just like any other DB instance parameter\. For more information, see the modifying section for your DB engine\.

The following illustration shows the **Public accessibility** option in the **Network & Security** section\. 

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/VPC-example4.png)

For information about modifying a DB instance to set the **Public accessibility** option, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md)\.

## Creating a DB Instance in a VPC<a name="USER_VPC.InstanceInVPC"></a>

The following procedures help you create a DB instance in a VPC\. If your account has a default VPC, you can begin with step 3 because the VPC and DB subnet group have already been created for you\. If your AWS account doesn't have a default VPC, or if you want to create an additional VPC, you can create a new VPC\. 

If you don't know if you have a default VPC, see [Determining Whether You Are Using the EC2\-VPC or EC2\-Classic Platform](USER_VPC.FindDefaultVPC.md)\.

**Note**  
If you want your DB instance in the VPC to be publicly accessible, you must update the DNS information for the VPC by enabling the VPC attributes *DNS hostnames* and *DNS resolution*\. For information about updating the DNS information for a VPC instance, see [Updating DNS Support for Your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-dns.html)\. 

Follow these steps to create a DB instance in a VPC:
+ [Step 1: Create a VPC](#USER_VPC.CreatingVPC) 
+ [Step 2: Add Subnets to the VPC](#USER_VPC.AddingSubnets) 
+  [Step 3: Create a DB Subnet Group](#USER_VPC.CreateDBSubnetGroup)
+  [Step 4: Create a VPC Security Group](#USER_VPC.CreateVPCSecurityGroup)
+  [Step 5: Create a DB Instance in the VPC](#USER_VPC.CreateDBInstanceInVPC) 

### Step 1: Create a VPC<a name="USER_VPC.CreatingVPC"></a>

If your AWS account does not have a default VPC or if you want to create an additional VPC, follow the instructions for creating a new VPC\. See [Create a VPC with Private and Public Subnets](CHAP_Tutorials.WebServerDB.CreateVPC.md#CHAP_Tutorials.WebServerDB.CreateVPC.VPCAndSubnets), or see [Step 1: Create a VPC](https://docs.aws.amazon.com/AmazonVPC/latest/GettingStartedGuide/Wizard.html) in the Amazon VPC documentation\. 

### Step 2: Add Subnets to the VPC<a name="USER_VPC.AddingSubnets"></a>

Once you have created a VPC, you need to create subnets in at least two Availability Zones\. You use these subnets when you create a DB subnet group\. If you have a default VPC, a subnet is automatically created for you in each Availability Zone in the AWS Region\. 

For instructions on how to create subnets in a VPC, see [Create a VPC with Private and Public Subnets](CHAP_Tutorials.WebServerDB.CreateVPC.md#CHAP_Tutorials.WebServerDB.CreateVPC.VPCAndSubnets)\. 

### Step 3: Create a DB Subnet Group<a name="USER_VPC.CreateDBSubnetGroup"></a>

 A DB subnet group is a collection of subnets \(typically private\) that you create for a VPC and that you then designate for your DB instances\. A DB subnet group allows you to specify a particular VPC when you create DB instances using the CLI or API\. If you use the console, you can just choose the VPC and subnets you want to use\. Each DB subnet group must have at least one subnet in at least two Availability Zones in the AWS Region\. 

 For a DB instance to be publicly accessible, the subnets in the DB subnet group must have an internet gateway\. For more information about internet gateways for subnets, see [ Internet Gateways](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Internet_Gateway.html) in the Amazon VPC documentation\. 

**Note**  
The DB subnet group for a Local Zone can have only one subnet\.

When you create a DB instance in a VPC, make sure to choose a DB subnet group\. Amazon RDS then uses that DB subnet group and your preferred Availability Zone to choose a subnet and an IP address within that subnet\. Amazon RDS creates and associates an Elastic Network Interface to your DB instance with that IP address\. For Multi\-AZ deployments, defining a subnet for two or more Availability Zones in an AWS Region allows Amazon RDS to create a new standby in another Availability Zone should the need arise\. You need to do this even for Single\-AZ deployments, just in case you want to convert them to Multi\-AZ deployments at some point\. 

In this step, you create a DB subnet group and add the subnets that you created for your VPC\. 

**To create a DB subnet group**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Subnet groups**\.

1. Choose **Create DB Subnet Group**\.

1. For **Name**, type the name of your DB subnet group\.

1. For **Description**, type a description for your DB subnet group\. 

1.  For **VPC**, choose the VPC that you created\. 

1. In the **Add subnets** section, choose the Availability Zones that include the subnets from **Availability Zones**, and then choose the subnets from **Subnets**\.  
![\[Create DB Subnet Group button\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/RDSVPC101.png)
**Note**  
If you have enabled a Local Zone, you can choose an Availability Zone group on the **Create DB subnet group** page\. In this case, choose the **Availability Zone group**, **Availability Zones**, and **Subnets**\.

1. Choose **Create**\. 

    Your new DB subnet group appears in the DB subnet groups list on the RDS console\. You can choose the DB subnet group to see details, including all of the subnets associated with the group, in the details pane at the bottom of the window\. 

### Step 4: Create a VPC Security Group<a name="USER_VPC.CreateVPCSecurityGroup"></a>

Before you create your DB instance, you must create a VPC security group to associate with your DB instance\. For instructions on how to create a security group for your DB instance, see [ Create a VPC Security Group for a Private DB Instance](CHAP_Tutorials.WebServerDB.CreateVPC.md#CHAP_Tutorials.WebServerDB.CreateVPC.SecurityGroupDB), or see [Security Groups for Your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html) in the Amazon VPC documentation\. 

### Step 5: Create a DB Instance in the VPC<a name="USER_VPC.CreateDBInstanceInVPC"></a>

In this step, you create a DB instance and use the VPC name, the DB subnet group, and the VPC security group you created in the previous steps\. 

**Note**  
If you want your DB instance in the VPC to be publicly accessible, you must enable the VPC attributes *DNS hostnames* and *DNS resolution*\. For information on updating the DNS information for a VPC instance, see [Updating DNS Support for Your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-dns.html)\. 

For details on how to create a DB instance, see [Creating an Amazon RDS DB Instance](USER_CreateDBInstance.md)\.

When prompted in the **Network & Security** section, enter the VPC name, the DB subnet group, and the VPC security group you created in the previous steps\.