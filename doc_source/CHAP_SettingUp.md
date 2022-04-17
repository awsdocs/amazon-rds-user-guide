# Setting up for Amazon RDS<a name="CHAP_SettingUp"></a>

Before you use Amazon Relational Database Service for the first time, complete the following tasks:

1. [Sign up for AWS](#CHAP_SettingUp.SignUp)

1. [Create an IAM user](#CHAP_SettingUp.IAM)

1. [Determine requirements](#CHAP_SettingUp.Requirements)

1. [Provide access to your DB instance in your VPC by creating a security group](#CHAP_SettingUp.SecurityGroup)

If you already have an AWS account, know your Amazon RDS requirements, and prefer to use the defaults for IAM and VPC security groups, skip ahead to [Getting started with Amazon RDS](CHAP_GettingStarted.md)\. 

## Sign up for AWS<a name="CHAP_SettingUp.SignUp"></a>

When you sign up for AWS, your AWS account is automatically signed up for all services in AWS, including Amazon RDS\. You are charged only for the services that you use\.

With Amazon RDS, you pay only for the resources you use\. The Amazon RDS DB instances that you create are live \(not running in a sandbox\)\. You incur the standard Amazon RDS usage fees for each DB instance until you terminate it\. For more information about Amazon RDS usage rates, see the [Amazon RDS product page](http://aws.amazon.com/rds)\. If you are a new AWS customer, you can get started with Amazon RDS for free; for more information, see [AWS free tier](http://aws.amazon.com/free/)\.

If you have an AWS account already, skip to the next section, [Create an IAM user](#CHAP_SettingUp.IAM)\. 

If you don't have an AWS account, you can use the following procedure to create one\.

**To create a new AWS account**

1. Open [https://portal\.aws\.amazon\.com/billing/signup](https://portal.aws.amazon.com/billing/signup)\.

1. Follow the online instructions\.

   Part of the sign\-up procedure involves receiving a phone call and entering a verification code on the phone keypad\.

Note your AWS account number, because you'll need it for the next task\.

## Create an IAM user<a name="CHAP_SettingUp.IAM"></a>

After you create an AWS account and successfully connect to the AWS Management Console, you can create an AWS Identity and Access Management \(IAM\) user\. Instead of signing in with your AWS root account, we recommend that you use an IAM administrative user with Amazon RDS\. 

One way to do this is to create a new IAM user and grant it administrator permissions\. Alternatively, you can add an existing IAM user to an IAM group with Amazon RDS administrative permissions\. You can then access AWS from a special URL using the credentials for the IAM user\. 

If you signed up for AWS but haven't created an IAM user for yourself, you can create one using the IAM console\.

**To create an administrator user for yourself and add the user to an administrators group \(console\)**

1. Sign in to the [IAM console](https://console.aws.amazon.com/iam/) as the account owner by choosing **Root user** and entering your AWS account email address\. On the next page, enter your password\.
**Note**  
We strongly recommend that you adhere to the best practice of using the **Administrator** IAM user that follows and securely lock away the root user credentials\. Sign in as the root user only to perform a few [account and service management tasks](https://docs.aws.amazon.com/general/latest/gr/aws_tasks-that-require-root.html)\.

1. In the navigation pane, choose **Users** and then choose **Add users**\.

1. For **User name**, enter **Administrator**\.

1. Select the check box next to **AWS Management Console access**\. Then select **Custom password**, and then enter your new password in the text box\.

1. \(Optional\) By default, AWS requires the new user to create a new password when first signing in\. You can clear the check box next to **User must create a new password at next sign\-in** to allow the new user to reset their password after they sign in\.

1. Choose **Next: Permissions**\.

1. Under **Set permissions**, choose **Add user to group**\.

1. Choose **Create group**\.

1. In the **Create group** dialog box, for **Group name** enter **Administrators**\.

1. Choose **Filter policies**, and then select **AWS managed \- job function** to filter the table contents\.

1. In the policy list, select the check box for **AdministratorAccess**\. Then choose **Create group**\.
**Note**  
You must activate IAM user and role access to Billing before you can use the `AdministratorAccess` permissions to access the AWS Billing and Cost Management console\. To do this, follow the instructions in [step 1 of the tutorial about delegating access to the billing console](https://docs.aws.amazon.com/IAM/latest/UserGuide/tutorial_billing.html)\.

1. Back in the list of groups, select the check box for your new group\. Choose **Refresh** if necessary to see the group in the list\.

1. Choose **Next: Tags**\.

1. \(Optional\) Add metadata to the user by attaching tags as key\-value pairs\. For more information about using tags in IAM, see [Tagging IAM entities](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_tags.html) in the *IAM User Guide*\.

1. Choose **Next: Review** to see the list of group memberships to be added to the new user\. When you are ready to proceed, choose **Create user**\.

You can use this same process to create more groups and users and to give your users access to your AWS account resources\. To learn about using policies that restrict user permissions to specific AWS resources, see [Access management](https://docs.aws.amazon.com/IAM/latest/UserGuide/access.html) and [Example policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_examples.html)\.

To sign in as the new IAM user, first sign out of the AWS Management Console\. Then use the following URL, where **your\_aws\_account\_id** is your AWS account number without the hyphens\. For example, if your AWS account number is `1234-5678-9012`, your AWS account ID is `123456789012`\.

```
https://your_aws_account_id.signin.aws.amazon.com/console/
```

Type the IAM user name and password that you just created\. When you're signed in, the navigation bar displays "*your\_user\_name* @ *your\_aws\_account\_id*"\.

If you don't want the URL for your sign\-in page to contain your AWS account ID, you can create an account alias\. From the IAM dashboard, choose **Customize** and type an alias, such as your company name\. To sign in after you create an account alias, use the following URL\.

```
https://your_account_alias.signin.aws.amazon.com/console/
```

To verify the sign\-in link for IAM users for your account, open the IAM console and check under **AWS Account Alias** on the dashboard\.

You can also create access keys for your AWS account\. These access keys can be used to access AWS through the AWS Command Line Interface \(AWS CLI\) or through the Amazon RDS API\. For more information, see [Programmatic access](https://docs.aws.amazon.com/general/latest/gr/managing-aws-access-keys.html), [Installing, updating, and uninstalling the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/installing.html), and the *[Amazon RDS API reference\.](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/Welcome.html)*

## Determine requirements<a name="CHAP_SettingUp.Requirements"></a>

The basic building block of Amazon RDS is the DB instance\. In a DB instance, you create your databases\. A DB instance provides a network address called an *endpoint*\. Your applications use this endpoint to connect to your DB instance\. When you create a DB instance, you specify details like storage, memory, database engine and version, network configuration, security, and maintenance periods\. You control network access to a DB instance through a security group\. 

Before you create a DB instance and a security group, you must know your DB instance and network needs\. Here are some important things to consider:
+ **Resource requirements **– What are the memory and processor requirements for your application or service? You use these settings to help you determine what DB instance class to use\. For specifications about DB instance classes, see [DB instance classes](Concepts.DBInstanceClass.md)\.
+ **VPC, subnet, and security group – **Your DB instance will most likely be in a virtual private cloud \(VPC\)\. To connect to your DB instance, you need to set up security group rules\. These rules are set up differently depending on what kind of VPC you use and how you use it: in a default VPC or in a user\-defined VPC\. 

  The following list describes the rules for each VPC option:
  + **Default VPC** – If your AWS account has a default VPC in the current AWS Region, that VPC is configured to support DB instances\. If you specify the default VPC when you create the DB instance, do the following:
    + Make sure to create a *VPC security group* that authorizes connections from the application or service to the Amazon RDS DB instance\. Use the **Security Group** option on the VPC console or the AWS CLI to create VPC security groups\. For information, see [Step 4: Create a VPC security group](USER_VPC.WorkingWithRDSInstanceinaVPC.md#USER_VPC.CreateVPCSecurityGroup)\.
    + Specify the default DB subnet group\. If this is the first DB instance you have created in this AWS Region, Amazon RDS creates the default DB subnet group when it creates the DB instance\.
  + **User\-defined VPC – **If you want to specify a user\-defined VPC when you create a DB instance, be aware of the following:
    + Make sure to create a *VPC security group* that authorizes connections from the application or service to the Amazon RDS DB instance\. Use the **Security Group** option on the VPC console or the AWS CLI to create VPC security groups\. For information, see [Step 4: Create a VPC security group](USER_VPC.WorkingWithRDSInstanceinaVPC.md#USER_VPC.CreateVPCSecurityGroup)\.
    + The VPC must meet certain requirements in order to host DB instances, such as having at least two subnets, each in a separate Availability Zone\. For information, see [Amazon Virtual Private Cloud VPCs and Amazon RDS](USER_VPC.md)\.
    + Make sure to specify a DB subnet group that defines which subnets in that VPC can be used by the DB instance\. For information, see the DB subnet group section in [Working with a DB instance in a VPC](USER_VPC.WorkingWithRDSInstanceinaVPC.md#Overview.RDSVPC.Create)\.
**Note**  
Some legacy accounts don't use a VPC\. If you are accessing a new AWS Region or you are a new RDS user \(after 2013\), you are most likely creating a DB instance inside a VPC\. For more information, see [Determining whether you are using the EC2\-VPC or EC2\-Classic platform](USER_VPC.FindDefaultVPC.md)\.
+ **High availability: **Do you need failover support? On Amazon RDS, a Multi\-AZ deployment creates a primary DB instance and a secondary standby DB instance in another Availability Zone for failover support\. We recommend Multi\-AZ deployments for production workloads to maintain high availability\. For development and test purposes, you can use a deployment that isn't Multi\-AZ\. For more information, see [Multi\-AZ deployments for high availability](Concepts.MultiAZ.md)\. 
+ **IAM policies: **Does your AWS account have policies that grant the permissions needed to perform Amazon RDS operations? If you are connecting to AWS using IAM credentials, your IAM account must have IAM policies that grant the permissions required to perform Amazon RDS operations\. For more information, see [Identity and access management in Amazon RDS](UsingWithRDS.IAM.md)\.
+ **Open ports: **What TCP/IP port does your database listen on? The firewall at some companies might block connections to the default port for your database engine\. If your company firewall blocks the default port, choose another port for the new DB instance\. When you create a DB instance that listens on a port you specify, you can change the port by modifying the DB instance\.
+ **AWS Region: **What AWS Region do you want your database in? Having your database in close proximity to your application or web service can reduce network latency\. For more information, see [Regions, Availability Zones, and Local Zones](Concepts.RegionsAndAvailabilityZones.md)\.
+ **DB disk subsystem: **What are your storage requirements? Amazon RDS provides three storage types: 
  + Magnetic \(Standard Storage\)
  + General Purpose \(SSD\)
  + Provisioned IOPS \(PIOPS\)

  Magnetic storage offers cost\-effective storage that is ideal for applications with light or burst I/O requirements\. General purpose, SSD\-backed storage, also called *gp2*, can provide faster access than disk\-based storage\. Provisioned IOPS storage is designed to meet the needs of I/O\-intensive workloads, particularly database workloads, which are sensitive to storage performance and consistency in random access I/O throughput\. For more information on Amazon RDS storage, see [Amazon RDS DB instance storage](CHAP_Storage.md)\.

When you have the information you need to create the security group and the DB instance, continue to the next step\.

## Provide access to your DB instance in your VPC by creating a security group<a name="CHAP_SettingUp.SecurityGroup"></a>

VPC security groups provide access to DB instances in a VPC\. They act as a firewall for the associated DB instance, controlling both inbound and outbound traffic at the DB instance level\. DB instances are created by default with a firewall and a default security group that protect the DB instance\. 

Before you can connect to your DB instance, you must add rules to a security group that enable you to connect\. Use your network and configuration information to create rules to allow access to your DB instance\.

For example, suppose that you have an application that accesses a database on your DB instance in a VPC\. In this case, you must add a custom TCP rule that specifies the port range and IP addresses that your application uses to access the database\. If you have an application on an Amazon EC2 instance, you can use the security group that you set up for the Amazon EC2 instance\.

For information about common scenarios for accessing a DB instance, see [Scenarios for accessing a DB instance in a VPC](USER_VPC.Scenarios.md)\.

**To create a VPC security group**

1. Sign in to the AWS Management Console and open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc](https://console.aws.amazon.com/vpc)\.
**Note**  
Make sure you are in the VPC console, not the RDS console\.

1. In the top right corner of the AWS Management Console, choose the AWS Region where you want to create your VPC security group and DB instance\. In the list of Amazon VPC resources for that AWS Region, you should see at least one VPC and several subnets\. If you don't, you don't have a default VPC in that AWS Region\.

1. In the navigation pane, choose **Security Groups**\.

1. Choose **Create security group**\.

   The **Create security group** page appears\.

1. In **Basic details**, enter the **Security group name** and **Description**\. For **VPC**, choose the VPC that you want to create your DB instance in\.

1. In **Inbound rules**, choose **Add rule**\.

   1. For **Type**, choose **Custom TCP**\.

   1. For **Port range**, enter the port value to use for your DB instance\.

   1. For **Source**, choose a security group name or type the IP address range \(CIDR value\) from where you access the DB instance\. If you choose **My IP**, this allows access to the DB instance from the IP address detected in your browser\.

1. If you need to add more IP addresses or different port ranges, choose **Add rule** and enter the information for the rule\.

1. \(Optional\) In **Outbound rules**, add rules for outbound traffic\. By default, all outbound traffic is allowed\.

1. Choose **Create security group**\.

You can use the VPC security group that you just created as the security group for your DB instance when you create it\.

**Note**  
If you use a default VPC, a default subnet group spanning all of the VPC's subnets is created for you\. When you create a DB instance, you can select the default VPC and use **default** for **DB Subnet Group**\.

Once you have completed the setup requirements, you can create a DB instance using your requirements and security group by following the instructions in [Creating an Amazon RDS DB instance](USER_CreateDBInstance.md)\. For information about getting started by creating a DB instance that uses a specific DB engine, see the relevant documentation in the following table\.


****  

| Database engine | Documentation | 
| --- | --- | 
| MariaDB | [Creating a MariaDB DB instance and connecting to a database on a MariaDB DB instance](CHAP_GettingStarted.CreatingConnecting.MariaDB.md) | 
| Microsoft SQL Server | [Creating a Microsoft SQL Server DB instance and connecting to it](CHAP_GettingStarted.CreatingConnecting.SQLServer.md) | 
| MySQL | [Creating a MySQL DB instance and connecting to a database on a MySQL DB instance](CHAP_GettingStarted.CreatingConnecting.MySQL.md) | 
| Oracle | [Creating an Oracle DB instance and connecting to a database on an Oracle DB instance](CHAP_GettingStarted.CreatingConnecting.Oracle.md) | 
| PostgreSQL | [Creating a PostgreSQL DB instance and connecting to a database on a PostgreSQL DB instance](CHAP_GettingStarted.CreatingConnecting.PostgreSQL.md) | 

**Note**  
If you can't connect to a DB instance after you create it, see the troubleshooting information in [Can't connect to Amazon RDS DB instance](CHAP_Troubleshooting.md#CHAP_Troubleshooting.Connecting)\.