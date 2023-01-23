# Setting up for Amazon RDS<a name="CHAP_SettingUp"></a>

Before you use Amazon Relational Database Service for the first time, complete the following tasks\.

**Topics**
+ [Sign up for an AWS account](#sign-up-for-aws)
+ [Create an administrative user](#create-an-admin)
+ [Grant programmatic access](#getting-started-iam-user-access-keys)
+ [Determine requirements](#CHAP_SettingUp.Requirements)
+ [Provide access to your DB instance in your VPC by creating a security group](#CHAP_SettingUp.SecurityGroup)

If you already have an AWS account, know your Amazon RDS requirements, and prefer to use the defaults for IAM and VPC security groups, skip ahead to [Getting started with Amazon RDS](CHAP_GettingStarted.md)\. 

## Sign up for an AWS account<a name="sign-up-for-aws"></a>

If you do not have an AWS account, complete the following steps to create one\.

**To sign up for an AWS account**

1. Open [https://portal\.aws\.amazon\.com/billing/signup](https://portal.aws.amazon.com/billing/signup)\.

1. Follow the online instructions\.

   Part of the sign\-up procedure involves receiving a phone call and entering a verification code on the phone keypad\.

   When you sign up for an AWS account, an *AWS account root user* is created\. The root user has access to all AWS services and resources in the account\. As a security best practice, [assign administrative access to an administrative user](https://docs.aws.amazon.com/singlesignon/latest/userguide/getting-started.html), and use only the root user to perform [tasks that require root user access](https://docs.aws.amazon.com/accounts/latest/reference/root-user-tasks.html)\.

AWS sends you a confirmation email after the sign\-up process is complete\. At any time, you can view your current account activity and manage your account by going to [https://aws\.amazon\.com/](https://aws.amazon.com/) and choosing **My Account**\.

## Create an administrative user<a name="create-an-admin"></a>

After you sign up for an AWS account, create an administrative user so that you don't use the root user for everyday tasks\.

**Secure your AWS account root user**

1.  Sign in to the [AWS Management Console](https://console.aws.amazon.com/) as the account owner by choosing **Root user** and entering your AWS account email address\. On the next page, enter your password\.

   For help signing in by using root user, see [Signing in as the root user](https://docs.aws.amazon.com/signin/latest/userguide/console-sign-in-tutorials.html#introduction-to-root-user-sign-in-tutorial) in the *AWS Sign\-In User Guide*\.

1. Turn on multi\-factor authentication \(MFA\) for your root user\.

   For instructions, see [Enable a virtual MFA device for your AWS account root user \(console\)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_mfa_enable_virtual.html#enable-virt-mfa-for-root) in the *IAM User Guide*\.

**Create an administrative user**
+ For your daily administrative tasks, grant administrative access to an administrative user in AWS IAM Identity Center \(successor to AWS Single Sign\-On\)\.

  For instructions, see [Getting started](https://docs.aws.amazon.com/singlesignon/latest/userguide/getting-started.html) in the *AWS IAM Identity Center \(successor to AWS Single Sign\-On\) User Guide*\.

**Sign in as the administrative user**
+ To sign in with your IAM Identity Center user, use the sign\-in URL that was sent to your email address when you created the IAM Identity Center user\.

  For help signing in using an IAM Identity Center user, see [Signing in to the AWS access portal](https://docs.aws.amazon.com/signin/latest/userguide/iam-id-center-sign-in-tutorial.html) in the *AWS Sign\-In User Guide*\.

## Grant programmatic access<a name="getting-started-iam-user-access-keys"></a>

Users need programmatic access if they want to interact with AWS outside of the AWS Management Console\. The way to grant programmatic access depends on the type of user that's accessing AWS:
+ If you manage identities in IAM Identity Center, the AWS APIs require a profile, and the AWS Command Line Interface requires a profile or an environment variable\.
+ If you have IAM users, the AWS APIs and the AWS Command Line Interface require access keys\. Whenever possible, create temporary credentials that consist of an access key ID, a secret access key, and a security token that indicates when the credentials expire\.

To grant users programmatic access, choose one of the following options\.


****  

| Which user needs programmatic access? | To | By | 
| --- | --- | --- | 
|  Workforce identity \(Users managed in IAM Identity Center\)  | Use short\-term credentials to sign programmatic requests to the AWS CLI or AWS APIs \(directly or by using the AWS SDKs\)\. |  Following the instructions for the interface that you want to use: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_SettingUp.html)  | 
| IAM | Use short\-term credentials to sign programmatic requests to the AWS CLI or AWS APIs \(directly or by using the AWS SDKs\)\. | Following the instructions in [Using temporary credentials with AWS resources](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp_use-resources.html) in the IAM User Guide\. | 
| IAM | Use long\-term credentials to sign programmatic requests to the AWS CLI or AWS APIs \(directly or by using the AWS SDKs\)\.\(Not recommended\) | Following the instructions in [Managing access keys for IAM users](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_access-keys.html) in the IAM User Guide\. | 

## Determine requirements<a name="CHAP_SettingUp.Requirements"></a>

The basic building block of Amazon RDS is the DB instance\. In a DB instance, you create your databases\. A DB instance provides a network address called an *endpoint*\. Your applications use this endpoint to connect to your DB instance\. When you create a DB instance, you specify details like storage, memory, database engine and version, network configuration, security, and maintenance periods\. You control network access to a DB instance through a security group\. 

Before you create a DB instance and a security group, you must know your DB instance and network needs\. Here are some important things to consider:
+ **Resource requirements **– What are the memory and processor requirements for your application or service? You use these settings to help you determine what DB instance class to use\. For specifications about DB instance classes, see [DB instance classes](Concepts.DBInstanceClass.md)\.
+ **VPC, subnet, and security group – **Your DB instance will most likely be in a virtual private cloud \(VPC\)\. To connect to your DB instance, you need to set up security group rules\. These rules are set up differently depending on what kind of VPC you use and how you use it\. For example, you can use: a default VPC or a user\-defined VPC\. 

  The following list describes the rules for each VPC option:
  + **Default VPC** – If your AWS account has a default VPC in the current AWS Region, that VPC is configured to support DB instances\. If you specify the default VPC when you create the DB instance, do the following:
    + Make sure to create a *VPC security group* that authorizes connections from the application or service to the Amazon RDS DB instance\. Use the **Security Group** option on the VPC console or the AWS CLI to create VPC security groups\. For information, see [Step 3: Create a VPC security group](USER_VPC.WorkingWithRDSInstanceinaVPC.md#USER_VPC.CreateVPCSecurityGroup)\.
    + Specify the default DB subnet group\. If this is the first DB instance you have created in this AWS Region, Amazon RDS creates the default DB subnet group when it creates the DB instance\.
  + **User\-defined VPC – **If you want to specify a user\-defined VPC when you create a DB instance, be aware of the following:
    + Make sure to create a *VPC security group* that authorizes connections from the application or service to the Amazon RDS DB instance\. Use the **Security Group** option on the VPC console or the AWS CLI to create VPC security groups\. For information, see [Step 3: Create a VPC security group](USER_VPC.WorkingWithRDSInstanceinaVPC.md#USER_VPC.CreateVPCSecurityGroup)\.
    + The VPC must meet certain requirements in order to host DB instances, such as having at least two subnets, each in a separate Availability Zone\. For information, see [Amazon VPC VPCs and Amazon RDS](USER_VPC.md)\.
    + Make sure to specify a DB subnet group that defines which subnets in that VPC can be used by the DB instance\. For information, see the DB subnet group section in [Working with a DB instance in a VPC](USER_VPC.WorkingWithRDSInstanceinaVPC.md#Overview.RDSVPC.Create)\.
+ **High availability – **Do you need failover support? On Amazon RDS, a Multi\-AZ deployment creates a primary DB instance and a secondary standby DB instance in another Availability Zone for failover support\. We recommend Multi\-AZ deployments for production workloads to maintain high availability\. For development and test purposes, you can use a deployment that isn't Multi\-AZ\. For more information, see [Multi\-AZ deployments for high availability](Concepts.MultiAZ.md)\. 
+ **IAM policies **– Does your AWS account have policies that grant the permissions needed to perform Amazon RDS operations? If you are connecting to AWS using IAM credentials, your IAM account must have IAM policies that grant the permissions required to perform Amazon RDS operations\. For more information, see [Identity and access management for Amazon RDS](UsingWithRDS.IAM.md)\.
+ **Open ports **– What TCP/IP port does your database listen on? The firewalls at some companies might block connections to the default port for your database engine\. If your company firewall blocks the default port, choose another port for the new DB instance\. When you create a DB instance that listens on a port you specify, you can change the port by modifying the DB instance\.
+ **AWS Region **– What AWS Region do you want your database in? Having your database in close proximity to your application or web service can reduce network latency\. For more information, see [Regions, Availability Zones, and Local Zones](Concepts.RegionsAndAvailabilityZones.md)\.
+ **DB disk subsystem **– What are your storage requirements? Amazon RDS provides three storage types:
  + General Purpose \(SSD\)
  + Provisioned IOPS \(PIOPS\)
  + Magnetic \(also known as standard storage\)

  For more information on Amazon RDS storage, see [Amazon RDS DB instance storage](CHAP_Storage.md)\.

When you have the information you need to create the security group and the DB instance, continue to the next step\.

## Provide access to your DB instance in your VPC by creating a security group<a name="CHAP_SettingUp.SecurityGroup"></a>

VPC security groups provide access to DB instances in a VPC\. They act as a firewall for the associated DB instance, controlling both inbound and outbound traffic at the DB instance level\. DB instances are created by default with a firewall and a default security group that protect the DB instance\. 

Before you can connect to your DB instance, you must add rules to a security group that enable you to connect\. Use your network and configuration information to create rules to allow access to your DB instance\.

For example, suppose that you have an application that accesses a database on your DB instance in a VPC\. In this case, you must add a custom TCP rule that specifies the port range and IP addresses that your application uses to access the database\. If you have an application on an Amazon EC2 instance, you can use the security group that you set up for the Amazon EC2 instance\.

You can configure connectivity between an Amazon EC2 instance a DB instance when you create the DB instance\. For more information, see [Configure automatic network connectivity with an EC2 instance](USER_CreateDBInstance.md#USER_CreateDBInstance.Prerequisites.VPC.Automatic)\.

**Tip**  
You can set up network connectivity between an Amazon EC2 instance and a DB instance automatically when you create the DB instance\. For more information, see [Configure automatic network connectivity with an EC2 instance](USER_CreateDBInstance.md#USER_CreateDBInstance.Prerequisites.VPC.Automatic)\.

For information about common scenarios for accessing a DB instance, see [Scenarios for accessing a DB instance in a VPC](USER_VPC.Scenarios.md)\.

**To create a VPC security group**

1. Sign in to the AWS Management Console and open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc](https://console.aws.amazon.com/vpc)\.
**Note**  
Make sure you are in the VPC console, not the RDS console\.

1. In the upper\-right corner of the AWS Management Console, choose the AWS Region where you want to create your VPC security group and DB instance\. In the list of Amazon VPC resources for that AWS Region, you should see at least one VPC and several subnets\. If you don't, you don't have a default VPC in that AWS Region\.

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

After you have completed the setup requirements, you can create a DB instance using your requirements and security group\. To do so, follow the instructions in [Creating an Amazon RDS DB instance](USER_CreateDBInstance.md)\. For information about getting started by creating a DB instance that uses a specific DB engine, see the relevant documentation in the following table\.


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