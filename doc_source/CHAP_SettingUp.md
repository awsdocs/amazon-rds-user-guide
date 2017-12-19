# Setting Up for Amazon RDS<a name="CHAP_SettingUp"></a>

Before you use Amazon RDS for the first time, complete the following tasks:

1. [Sign Up for AWS](#CHAP_SettingUp.SignUp)

1. [Create an IAM User](#CHAP_SettingUp.IAM)

1. [Determine Requirements](#CHAP_SettingUp.Requirements)

1. [Provide Access to the DB Instance in the VPC by Creating a Security Group](#CHAP_SettingUp.SecurityGroup)

## Sign Up for AWS<a name="CHAP_SettingUp.SignUp"></a>

When you sign up for Amazon Web Services \(AWS\), your AWS account is automatically signed up for all services in AWS, including Amazon RDS\. You are charged only for the services that you use\.

With Amazon RDS, you pay only for the resources you use\. The Amazon RDS DB instance that you create is live \(not running in a sandbox\)\. You incur the standard Amazon RDS usage fees for the instance until you terminate it\. For more information about Amazon RDS usage rates, see the [Amazon RDS product page](http://aws.amazon.com/rds)\. If you are a new AWS customer, you can get started with Amazon RDS for free; for more information, see [AWS Free Usage Tier](http://aws.amazon.com/free/)\.

If you have an AWS account already, skip to the next task\. If you don't have an AWS account, use the following procedure to create one\.

**To create an AWS account**

1. Open [https://aws\.amazon\.com/](https://aws.amazon.com/), and then choose **Create an AWS Account**\.
**Note**  
This might be unavailable in your browser if you previously signed into the AWS Management Console\. In that case, choose **Sign In to the Console**, and then choose **Create a new AWS account**\.

1. Follow the online instructions\.

   Part of the sign\-up procedure involves receiving a phone call and entering a PIN using the phone keypad\.

Note your AWS account number, because you'll need it for the next task\.

## Create an IAM User<a name="CHAP_SettingUp.IAM"></a>

Services in AWS, such as Amazon RDS, require that you provide credentials when you access them, so that the service can determine whether you have permission to access its resources\. The console requires your password\. You can create access keys for your AWS account to access the command line interface or API\. However, we don't recommend that you access AWS using the credentials for your AWS account; we recommend that you use AWS Identity and Access Management \(IAM\) instead\. Create an IAM user, and then add the user to an IAM group with administrative permissions or grant this user administrative permissions\. You can then access AWS using a special URL and the credentials for the IAM user\.

If you signed up for AWS but have not created an IAM user for yourself, you can create one using the IAM console\.

**To create an IAM user for yourself and add the user to an Administrators group**

1. Use your AWS account email address and password to sign in to the [AWS Management Console](https://console.aws.amazon.com/) as the *[AWS account root user](http://docs.aws.amazon.com/IAM/latest/UserGuide/id_root-user.html)*\.

1. In the navigation pane of the console, choose **Users**, and then choose **Add user**\.

1. For **User name**, type ** Administrator**\.

1. Select the check box next to **AWS Management Console access**, select **Custom password**, and then type the new user's password in the text box\. You can optionally select **Require password reset** to force the user to select a new password the next time the user signs in\.

1. Choose **Next: Permissions**\.

1. On the **Set permissions for user** page, choose **Add user to group**\.

1. Choose **Create group**\.

1. In the **Create group** dialog box, type ** Administrators**\.

1. For **Filter**, choose **Job function**\.

1. In the policy list, select the check box for ** AdministratorAccess**\. Then choose **Create group**\.

1. Back in the list of groups, select the check box for your new group\. Choose **Refresh** if necessary to see the group in the list\.

1. Choose **Next: Review** to see the list of group memberships to be added to the new user\. When you are ready to proceed, choose **Create user**\.

You can use this same process to create more groups and users, and to give your users access to your AWS account resources\. To learn about using policies to restrict users' permissions to specific AWS resources, go to [Access Management](http://docs.aws.amazon.com/IAM/latest/UserGuide/access.html) and [Example Policies](http://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_examples.html)\.

To sign in as this new IAM user, sign out of the AWS console, then use the following URL, where *your\_aws\_account\_id* is your AWS account number without the hyphens \(for example, if your AWS account number is `1234-5678-9012`, your AWS account ID is `123456789012`\):

```
https://your_aws_account_id.signin.aws.amazon.com/console/
```

Enter the IAM user name and password that you just created\. When you're signed in, the navigation bar displays "*your\_user\_name* @ *your\_aws\_account\_id*"\.

If you don't want the URL for your sign\-in page to contain your AWS account ID, you can create an account alias\. From the IAM dashboard, click **Customize** and enter an alias, such as your company name\. To sign in after you create an account alias, use the following URL:

```
https://your_account_alias.signin.aws.amazon.com/console/
```

To verify the sign\-in link for IAM users for your account, open the IAM console and check under **AWS Account Alias** on the dashboard\.

## Determine Requirements<a name="CHAP_SettingUp.Requirements"></a>

The basic building block of Amazon RDS is the DB instance\. The DB instance is where you create your databases\. A DB instance provides a network address called the **Endpoint**\. Your applications connect to the endpoint exposed by the DB instance whenever they need to access the databases created in that DB instance\. The information you specify when you create the DB instance controls configuration elements such as storage, memory, database engine and version, network configuration, security, and maintenance periods\.

You must know your DB instance and network needs before you create a security group and before you create a DB instance\. For example, you must know the following:

+ What are the memory and processor requirements for your application or service? You will use these settings when you determine what DB instance class you will use when you create your DB instance\. For specifications about DB instance classes, see [DB Instance Class](Concepts.DBInstanceClass.md)\.

+ Your DB instance is most likely in a virtual private cloud \(VPC\); some legacy instances are not in a VPC, but if you are a new RDS user \(two years or less\) or accessing a new region, you are most likely creating an DB instance inside a VPC\. The security group rules you need to connect to a DB instance depend on whether your DB instance is in a default VPC, in a user\-defined VPC, or outside of a VPC\. For information on determining if your account has a default VPC in a region, see [Determining Whether You Are Using the EC2\-VPC or EC2\-Classic Platform](USER_VPC.FindDefaultVPC.md)\. The follow list describes the rules for each VPC option:

  + **Default VPC** — If your AWS account has a default VPC in the region, that VPC is configured to support DB instances\. If you specify the default VPC when you create the DB instance:

    + You must create a **VPC security group** that authorizes connections from the application or service to the Amazon RDS DB instance with the database\. Note that you must use the [Amazon EC2 API](http://docs.aws.amazon.com/AWSEC2/latest/APIReference/Welcome.html) or the **Security Group** option on the VPC Console to create VPC security groups\. For information, see [Step 4: Create a VPC Security Group](USER_VPC.WorkingWithRDSInstanceinaVPC.md#USER_VPC.CreateVPCSecurityGroup)\.

    + You must specify the default DB subnet group\. If this is the first DB instance you have created in the region, Amazon RDS will create the default DB subnet group when it creates the DB instance\.

  + **User\-defined VPC** — If you want to specify a user\-defined VPC when you create a DB instance:

    + You must create a **VPC security group** that authorizes connections from the application or service to the Amazon RDS DB instance with the database\. Note that you must use the [Amazon EC2 API](http://docs.aws.amazon.com/AWSEC2/latest/APIReference/Welcome.html) or the **Security Group** option on the VPC Console to create VPC security groups\. For information, see [Step 4: Create a VPC Security Group](USER_VPC.WorkingWithRDSInstanceinaVPC.md#USER_VPC.CreateVPCSecurityGroup)\.\.

    + The VPC must meet certain requirements in order to host DB instances, such as having at least two subnets, each in a separate availability zone\. For information, see [Amazon Virtual Private Cloud \(VPCs\) and Amazon RDS](USER_VPC.md)\.

    + You must specify a DB subnet group that defines which subnets in that VPC can be used by the DB instance\. For information, see the DB Subnet Group section in [Working with a DB Instance in a VPC](USER_VPC.WorkingWithRDSInstanceinaVPC.md#Overview.RDSVPC.Create)\.

  + **No VPC** — if your AWS account does not have a default VPC, and you do not specify a user\-defined VPC:

    + You must create a **DB security group** that authorizes connections from the devices and Amazon RDS instances running the applications or utilities that will access the databases in the DB instance\. For more information, see [Working with DB Security Groups \(EC2\-Classic Platform\)](USER_WorkingWithSecurityGroups.md)\.

+ Do you need failover support? On Amazon RDS, a standby replica of your DB instance that can be used in the event of a failover is called a Multi\-AZ deployment\. If you have production workloads, you should use a Multi\-AZ deployment\. For test purposes, you can usually get by with a single instance, non\-Multi\-AZ deployment\.

+ Does your AWS account have policies that grant the permissions needed to perform Amazon RDS operations? If you are connecting to AWS using IAM credentials, your IAM account must have IAM policies that grant the permissions required to perform Amazon RDS operations\. For more information, see [Authentication and Access Control for Amazon RDS](UsingWithRDS.IAM.md)\.

+ What TCP/IP port will your database be listening on? The firewall at some companies may block connections to the default port for your database engine\. If your company firewall blocks the default port, choose another port for the new DB instance\. Note that once you create a DB instance that listens on a port you specify, you can change the port by modifying the DB instance\.

+ What region do you want your database in? Having the database close in proximity to the application or web service could reduce network latency\.

+ What are your storage requirements? Do you need to use Provisioned IOPS? Amazon RDS provides three storage types: magnetic, General Purpose \(SSD\), and Provisioned IOPS \(input/output operations per second\) \. Magnetic storage, also called standard storage, offers cost\-effective storage that is ideal for applications with light or burst I/O requirements\. General purpose, SSD\-backed storage, also called *gp2*, can provide faster access than disk\-based storage\. Provisioned IOPS storage is designed to meet the needs of I/O\-intensive workloads, particularly database workloads, that are sensitive to storage performance and consistency in random access I/O throughput\. For more information on Amazon RDS storage, see [Storage for Amazon RDS](CHAP_Storage.md)\.

Once you have the information you need to create the security group and the DB instance, continue to the next step\.

## Provide Access to the DB Instance in the VPC by Creating a Security Group<a name="CHAP_SettingUp.SecurityGroup"></a>

Your DB instance will most likely be created in a VPC\. Security groups provide access to the DB instance in the VPC\. They act as a firewall for the associated DB instance, controlling both inbound and outbound traffic at the instance level\. DB instances are created by default with a firewall and a default security group that prevents access to the DB instance\. You must therefore add rules to a security group that enable you to connect to your DB instance\. Use the network and configuration information you determined in the previous step to create rules to allow access to your DB instance\.

The security group you need to create is a *VPC security group*, unless you have a legacy DB instance not in a VPC that requires a* DB security group*\. If you created your AWS account after March 2013, chances are very good that you have a default VPC, and your DB instance will be created in that VPC\. DB instances in a VPC require that you add rules to a VPC security group to allow access to the instance\.

For example, if you have an application that will access a database on your DB instance in a VPC, you must add a Custom TCP rule that specifies the port range and IP addresses that application will use to access the database\. If you have an application on an Amazon EC2 instance, you can use the VPC or EC2 security group you set up for the Amazon EC2 instance\.

**To create a VPC security group**

1. Sign in to the AWS Management Console and open the Amazon VPC console at https://console\.aws\.amazon\.com/vpc\.

1. In the top right corner of the AWS Management Console, select the region in which you want to create the VPC security group and the DB instance\. In the list of Amazon VPC resources for that region, it should show that you have at least one VPC and several Subnets\. If it does not, you do not have a default VPC in that region\.

1. In the navigation pane, click **Security Groups**\.

1. Click **Create Security Group**\.

1. In the **Create Security Group** window, type the **Name tag**, **Group name**, and **Description** of your security group\. Select the **VPC** that you want to create your DB instance in\. Click **Yes, Create**\.

1. The VPC security group you created should still be selected\. The details pane at the bottom of the console window displays the details for the security group, and tabs for working with inbound and outbound rules\. Click the **Inbound Rules** tab\.

1.  On the **Inbound Rules** tab, click **Edit**\. Select **Custom TCP Rule** from the **Type** list\. Type the port value you will use for your DB instance in the **PortRange** text box, and then type the IP address range \(CIDR value\) from where you will access the instance, or select a security group name in the **Source** text box\.

1. If you need to add more IP addresses or different port ranges, click **Add another rule**\.

1.  If you need to, you can use the **Outbound Rules** tab to add rules for outbound traffic\.

1. When you have finished, click **Save**\.

   You will use the VPC security group you just created as the security group for your DB instance when you create it\. If your DB instance is not going to be in a VPC, then see the topic [Working with DB Security Groups \(EC2\-Classic Platform\)](USER_WorkingWithSecurityGroups.md) to create a DB security group that you will use when you create your DB instance\.

   Finally, a quick note about VPC subnets: If you use a default VPC, a default subnet group spanning all of the VPC's subnets has already been created for you\. When you use the **Launch a DB Instance** wizard to create a DB instance, you can select the default VPC and use **default** for the **DB Subnet Group**\.

   Once you have completed the setup requirements, you can use your requirements and the security group you created to launch a DB instance\. For information on creating a DB instance, see the relevant documentation in the following table:   
****    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_SettingUp.html)