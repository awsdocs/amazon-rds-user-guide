# Setting Up for Amazon RDS<a name="CHAP_SettingUp"></a>

Complete the tasks in this section to set up Amazon Relational Database Service \(Amazon RDS\) for the first time\. If you already have an AWS account, know your Amazon RDS requirements, and prefer to use the defaults for IAM and VPC security groups, skip ahead to [Getting Started](Welcome.md#Welcome.WhatsNext.GettingStarted)\. 

A couple things you should know about Amazon Web Services \(AWS\):
+ When you sign up for AWS, your AWS account automatically has access to all services in AWS, including Amazon RDS\. However, you are charged only for the services that you use\.
+ With Amazon RDS, you pay only for the RDS instances that are active\. The Amazon RDS DB instance that you create is live \(not running in a sandbox\)\. You incur the standard Amazon RDS usage fees for the instance until you terminate it\. For more information about Amazon RDS usage rates, see the [Amazon RDS product page](http://aws.amazon.com/rds)\. 

**Topics**
+ [Sign Up for AWS](#CHAP_SettingUp.SignUp)
+ [Create an IAM User](#CHAP_SettingUp.IAM)
+ [Determine Requirements](#CHAP_SettingUp.Requirements)
+ [Provide Access to Your DB Instance in Your VPC by Creating a Security Group](#CHAP_SettingUp.SecurityGroup)

## Sign Up for AWS<a name="CHAP_SettingUp.SignUp"></a>

If you have an AWS account already, skip to the next section, [Create an IAM User](#CHAP_SettingUp.IAM)\. 

If you don't have an AWS account, you can use the following procedure to create one\. If you are a new AWS customer, you can get started with Amazon RDS for free; for more information, see [AWS Free Usage Tier](http://aws.amazon.com/free/)\.

**To create a new AWS account**

1. Open [https://portal\.aws\.amazon\.com/billing/signup](https://portal.aws.amazon.com/billing/signup)\.

1. Follow the online instructions\.

   Part of the sign\-up procedure involves receiving a phone call and entering a verification code on the phone keypad\.

## Create an IAM User<a name="CHAP_SettingUp.IAM"></a>

After you create an AWS account and successfully connect to the AWS Management Console, you can create an AWS Identity and Access Management \(IAM\) user\. Instead of signing in with your AWS root account, we recommend that you use an IAM administrative user with Amazon RDS\. 

One way to do this is to create a new IAM user and grant it administrator permissions\. Alternatively, you can add an existing IAM user to an IAM group with Amazon RDS administrative permissions\. You can then access AWS from a special URL using the credentials for the IAM user\. 

If you signed up for AWS but haven't created an IAM user for yourself, you can create one using the IAM console\.

**To create an administrator user for yourself and add the user to an administrators group \(console\)**

1. Sign in to the [IAM console](https://console.aws.amazon.com/iam/) as the account owner by choosing **Root user** and entering your AWS account email address\. On the next page, enter your password\.
**Note**  
We strongly recommend that you adhere to the best practice of using the **Administrator** IAM user below and securely lock away the root user credentials\. Sign in as the root user only to perform a few [account and service management tasks](https://docs.aws.amazon.com/general/latest/gr/aws_tasks-that-require-root.html)\.

1. In the navigation pane, choose **Users** and then choose **Add user**\.

1. For **User name**, enter **Administrator**\.

1. Select the check box next to **AWS Management Console access**\. Then select **Custom password**, and then enter your new password in the text box\.

1. \(Optional\) By default, AWS requires the new user to create a new password when first signing in\. You can clear the check box next to **User must create a new password at next sign\-in** to allow the new user to reset their password after they sign in\.

1. Choose **Next: Permissions**\.

1. Under **Set permissions**, choose **Add user to group**\.

1. Choose **Create group**\.

1. In the **Create group** dialog box, for **Group name** enter **Administrators**\.

1. Choose **Filter policies**, and then select **AWS managed \-job function** to filter the table contents\.

1. In the policy list, select the check box for **AdministratorAccess**\. Then choose **Create group**\.
**Note**  
You must activate IAM user and role access to Billing before you can use the `AdministratorAccess` permissions to access the AWS Billing and Cost Management console\. To do this, follow the instructions in [step 1 of the tutorial about delegating access to the billing console](https://docs.aws.amazon.com/IAM/latest/UserGuide/tutorial_billing.html)\.

1. Back in the list of groups, select the check box for your new group\. Choose **Refresh** if necessary to see the group in the list\.

1. Choose **Next: Tags**\.

1. \(Optional\) Add metadata to the user by attaching tags as key\-value pairs\. For more information about using tags in IAM, see [Tagging IAM Entities](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_tags.html) in the *IAM User Guide*\.

1. Choose **Next: Review** to see the list of group memberships to be added to the new user\. When you are ready to proceed, choose **Create user**\.

You can use this same process to create more groups and users and to give your users access to your AWS account resources\. To learn about using policies that restrict user permissions to specific AWS resources, see [Access Management](https://docs.aws.amazon.com/IAM/latest/UserGuide/access.html) and [Example Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_examples.html)\.

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

You can also create access keys for your AWS account\. These access keys can be used to access AWS through the AWS Command Line Interface \(AWS CLI\) or through the Amazon RDS API\. For more information, see [Programmatic access](https://docs.aws.amazon.com/general/latest/gr/managing-aws-access-keys.html), [Installing the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/installing.html), and the* [Amazon RDS API Reference\.](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/Welcome.html)*

## Determine Requirements<a name="CHAP_SettingUp.Requirements"></a>

The basic building block of Amazon RDS is the DB instance\. In a DB instance, you create your databases\. A DB instance provides a network address called an *endpoint*\. Your applications use this endpoint to connect to your DB instance\. When you create a DB instance, you specify details like storage, memory, database engine and version, network configuration, security, and maintenance periods\. You control network access to a DB instance through a security group\. 

Before you create a DB instance and a security group, you must know your DB instance and network needs\. Here are some important things to consider: 
+ **Resource requirements **– What are the memory and processor requirements for your application or service? You use these settings to help you determine what DB instance class to use\. For specifications about DB instance classes, see [DB Instance Classes](Concepts.DBInstanceClass.md)\.
+ **VPC, subnet, and security group – **Your DB instance is most likely in a virtual private cloud \(VPC\)\. To connect to your DB instance, you need to set up security group rules\. These rules are set up differently depending on what kind of VPC you use and how you use it: in a default VPC, in a user\-defined VPC, or outside of a VPC\. 
**Note**  
Some legacy accounts don't use a VPC\. If you are accessing a new AWS Region or you are a new RDS user \(after 2013\), you are most likely creating a DB instance inside a VPC\. 

  For information on how to determine if your account has a default VPC in a particular AWS Region, see [Determining Whether You Are Using the EC2\-VPC or EC2\-Classic Platform](USER_VPC.FindDefaultVPC.md)\.

  The following list describes the rules for each VPC option:
  + **Default VPC** – If your AWS account has a default VPC in the current AWS Region, that VPC is configured to support DB instances\. If you specify the default VPC when you create the DB instance, do the following:
    + Create a *VPC security group* that authorizes connections from the application or service to the Amazon RDS DB instance with the database\. Use the [Amazon EC2 API](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/Welcome.html) or the **Security Group** option on the VPC console to create VPC security groups\. For information, see [Step 4: Create a VPC Security Group](USER_VPC.WorkingWithRDSInstanceinaVPC.md#USER_VPC.CreateVPCSecurityGroup)\. 
    + Specify the default DB subnet group\. If this is the first DB instance you have created in this AWS Region, Amazon RDS creates the default DB subnet group when it creates the DB instance\.
  + **User\-defined VPC – **If you want to specify a user\-defined VPC when you create a DB instance, be aware of the following:
    + Make sure to create a *VPC security group* that authorizes connections from the application or service to the Amazon RDS DB instance with the database\. Use the [Amazon EC2 API](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/Welcome.html) or the **Security Group** option on the VPC console to create VPC security groups\. For information, see [Step 4: Create a VPC Security Group](USER_VPC.WorkingWithRDSInstanceinaVPC.md#USER_VPC.CreateVPCSecurityGroup)\.
    + The VPC must meet certain requirements in order to host DB instances, such as having at least two subnets, each in a separate availability zone\. For information, see [Amazon Virtual Private Cloud VPCs and Amazon RDS](USER_VPC.md)\.
    + Make sure to specify a DB subnet group that defines which subnets in that VPC can be used by the DB instance\. For information, see the DB subnet group section in [Working with a DB Instance in a VPC](USER_VPC.WorkingWithRDSInstanceinaVPC.md#Overview.RDSVPC.Create)\.
  + **No VPC – **If your AWS account doesn't have a default VPC and you don't specify a user\-defined VPC, create a DB security group\. A *DB security group* authorizes connections from the devices and Amazon RDS instances running the applications or utilities to access the databases in the DB instance\. For more information, see [Working with DB Security Groups \(EC2\-Classic Platform\)](USER_WorkingWithSecurityGroups.md)\.
+ **High availability: **Do you need failover support? On Amazon RDS, a Multi\-AZ deployment creates a primary DB instance and a secondary standby DB instance in another Availability Zone for failover support\. We recommend Multi\-AZ deployments for production workloads to maintain high availability\. For development and test purposes, you can use a deployment that isn't Multi\-AZ\. For more information, see [High Availability \(Multi\-AZ\) for Amazon RDS](Concepts.MultiAZ.md)\. 
+ **IAM policies: **Does your AWS account have policies that grant the permissions needed to perform Amazon RDS operations? If you are connecting to AWS using IAM credentials, your IAM account must have IAM policies that grant the permissions required to perform Amazon RDS operations\. For more information, see [Identity and Access Management in Amazon RDS](UsingWithRDS.IAM.md)\.
+ **Open ports: **What TCP/IP port does your database listen on? The firewall at some companies might block connections to the default port for your database engine\. If your company firewall blocks the default port, choose another port for the new DB instance\. When you create a DB instance that listens on a port you specify, you can change the port by modifying the DB instance\.
+ **AWS Region: **What AWS Region do you want your database in? Having your database in close proximity to your application or web service can reduce network latency\.
+ **DB disk subsystem: **What are your storage requirements? Amazon RDS provides three storage types: 
  + Magnetic \(Standard Storage\)
  + General Purpose \(SSD\)
  + Provisioned IOPS \(PIOPS\)

  Magnetic storage offers cost\-effective storage that is ideal for applications with light or burst I/O requirements\. General purpose, SSD\-backed storage, also called *gp2*, can provide faster access than disk\-based storage\. Provisioned IOPS storage is designed to meet the needs of I/O\-intensive workloads, particularly database workloads, which are sensitive to storage performance and consistency in random access I/O throughput\. For more information on Amazon RDS storage, see [Amazon RDS DB Instance Storage](CHAP_Storage.md)\.

When you have the information you need to create the security group and the DB instance, continue to the next step\.

## Provide Access to Your DB Instance in Your VPC by Creating a Security Group<a name="CHAP_SettingUp.SecurityGroup"></a>

VPC security groups provide access to DB instances in a VPC\. They act as a firewall for the associated DB instance, controlling both inbound and outbound traffic at the instance level\. DB instances are created by default with a firewall and a default security group that protect the DB instance\. 

Before you can connect to your DB instance, you must add rules to security group that enable you to connect\. Use your network and configuration information to create rules to allow access to your DB instance\.

**Note**  
If your legacy DB instance was created before March 2013 and isn't in a VPC, it might not have associated security groups\. If your DB instance was created after this date, it might be inside a default VPC\. 

For example, suppose that you have an application that accesses a database on your DB instance in a VPC\. In this case, you must add a custom TCP rule that specifies the port range and IP addresses that your application uses to access the database\. If you have an application on an Amazon EC2 instance, you can use the security group that you set up for the Amazon EC2 instance\.

**To create a VPC security group**

1. Sign in to the AWS Management Console and open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc](https://console.aws.amazon.com/vpc)\.

1. In the top right corner of the AWS Management Console, choose the AWS Region where you want to create your VPC security group and DB instance\. In the list of Amazon VPC resources for that AWS Region, you should see at least one VPC and several subnets\. If you don't, you don't have a default VPC in that AWS Region\.

1. In the navigation pane, choose **Security Groups**\.

1. Choose **Create Security Group**\.

1. In the **Create Security Group** window, type **Name tag**, **Group name**, and **Description** values for your security group\. For **VPC**, choose the VPC that you want to create your DB instance in\. Choose **Yes, Create**\.

1. The VPC security group that you created should still be selected\. If not, locate it in the list, and choose it\. The details pane at the bottom of the console window displays the details for the security group, and tabs for working with inbound and outbound rules\. Choose the **Inbound Rules** tab\.

1. On the **Inbound Rules** tab, choose **Edit**\.

   1. For **Type**, choose **Custom TCP Rule**\.

   1. For **Port Range**, type the port value to use for your DB instance\.

   1. For **Source**, choose a security group name or type the IP address range \(CIDR value\) from where you access the instance\. If you choose **My IP**, this allows access to the DB instance from the IP address detected in your browser\.

1. Choose **Add another rule** if you need to add more IP addresses or different port ranges\.

1. \(Optional\) Use the **Outbound Rules** tab to add rules for outbound traffic\. By default, all outbound traffic is allowed\.

You can use the VPC security group that you just created as the security group for your DB instance when you create it\. If your DB instance isn't going to be in a VPC, see [Working with DB Security Groups \(EC2\-Classic Platform\)](USER_WorkingWithSecurityGroups.md) to create a DB security group to use when you create your DB instance\.

**Note**  
If you use a default VPC, a default subnet group spanning all of the VPC's subnets is created for you\. When you create a DB instance, you can select the default VPC and use **default** for **DB Subnet Group**\.

Once you have completed the setup requirements, you can launch a DB instance using your requirements and security group\. For information on creating a DB instance, see the relevant documentation in the following table\.


****  

| Database Engine | Documentation | 
| --- | --- | 
| MariaDB | [Creating a MariaDB DB Instance and Connecting to a Database on a MariaDB DB Instance](CHAP_GettingStarted.CreatingConnecting.MariaDB.md) | 
| Microsoft SQL Server | [Creating a Microsoft SQL Server DB Instance and Connecting to a DB Instance](CHAP_GettingStarted.CreatingConnecting.SQLServer.md) | 
| MySQL | [Creating a MySQL DB Instance and Connecting to a Database on a MySQL DB Instance](CHAP_GettingStarted.CreatingConnecting.MySQL.md) | 
| Oracle | [Creating an Oracle DB Instance and Connecting to a Database on an Oracle DB Instance](CHAP_GettingStarted.CreatingConnecting.Oracle.md) | 
| PostgreSQL | [Creating a PostgreSQL DB Instance and Connecting to a Database on a PostgreSQL DB Instance](CHAP_GettingStarted.CreatingConnecting.PostgreSQL.md) | 

**Note**  
If you can't connect to a DB instance after you create it, see the troubleshooting information in [Can't Connect to Amazon RDS DB Instance](CHAP_Troubleshooting.md#CHAP_Troubleshooting.Connecting)\.