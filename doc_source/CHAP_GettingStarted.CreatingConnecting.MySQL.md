# Creating and connecting to a MySQL DB instance<a name="CHAP_GettingStarted.CreatingConnecting.MySQL"></a>

This tutorial creates an EC2 instance and an RDS for MySQL DB instance\. The tutorial shows you how to access the DB instance from the EC2 instance using a standard MySQL client\. As a best practice, this tutorial creates a private DB instance in a virtual private cloud \(VPC\)\. In most cases, other resources in the same VPC, such as EC2 instances, can access the DB instance, but resources outside of the VPC can't access it\.

After you complete the tutorial, there is a public and private subnet in each Availability Zone in your VPC\. In one Availability Zone, the EC2 instance is in the public subnet, and the DB instance is in the private subnet\.

**Important**  
There's no charge for creating an AWS account\. However, by completing this tutorial, you might incur costs for the AWS resources you use\. You can delete these resources after you complete the tutorial if they are no longer needed\.

The following diagram shows the configuration when the tutorial is complete\.

![\[EC2 instance and MySQL DB instance.\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/getting-started-mysql.png)

This tutorial uses **Easy create** to create a DB instance running MySQL with the AWS Management Console\. With **Easy create**, you specify only the DB engine type, DB instance size, and DB instance identifier\. **Easy create** uses the default settings for the other configuration options\. The DB instance created by **Easy create** is private\. 

When you use **Standard create** instead of **Easy create**, you can specify more configuration options when you create a DB instance, including ones for availability, security, backups, and maintenance\. To create a public DB instance, you must use **Standard create**\. For information about creating DB instances with **Standard create**, see [Creating an Amazon RDS DB instance](USER_CreateDBInstance.md)\.

**Topics**
+ [Prerequisites](#CHAP_GettingStarted.Prerequisites.MySQL)
+ [Step 1: Create an EC2 instance](#CHAP_GettingStarted.Creating.MySQL.EC2)
+ [Step 2: Create a MySQL DB instance](#CHAP_GettingStarted.Creating.MySQL)
+ [Step 3: Connect to a MySQL DB instance](#CHAP_GettingStarted.Connecting.MySQL)
+ [Step 4: Delete the EC2 instance and DB instance](#CHAP_GettingStarted.Deleting.MySQL)

## Prerequisites<a name="CHAP_GettingStarted.Prerequisites.MySQL"></a>

Before you begin, complete the steps in the following sections:
+ [Sign up for an AWS account](CHAP_SettingUp.md#sign-up-for-aws)
+ [Create an administrative user](CHAP_SettingUp.md#create-an-admin)

## Step 1: Create an EC2 instance<a name="CHAP_GettingStarted.Creating.MySQL.EC2"></a>

Create an Amazon EC2 instance that you will use to connect to your database\.

**To create an EC2 instance**

1. Sign in to the AWS Management Console and open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the upper\-right corner of the AWS Management Console, choose the AWS Region in which you want to create the EC2 instance\.

1. Choose **EC2 Dashboard**, and then choose **Launch instance**, as shown in the following image\.  
![\[EC2 Dashboard.\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/Tutorial_WebServer_11.png)

   The **Launch an instance** page opens\.

1. Choose the following settings on the **Launch an instance** page\.

   1. Under **Name and tags**, for **Name**, enter **ec2\-database\-connect**\.

   1. Under **Application and OS Images \(Amazon Machine Image\)**, choose **Amazon Linux**, and then choose the **Amazon Linux 2023 AMI**\. Keep the default selections for the other choices\.  
![\[Choose an Amazon Machine Image.\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/Tutorial_WebServer_12.png)

   1. Under **Instance type**, choose **t2\.micro**\.

   1. Under **Key pair \(login\)**, choose a **Key pair name** to use an existing key pair\. To create a new key pair for the Amazon EC2 instance, choose **Create new key pair** and then use the **Create key pair** window to create it\.

      For more information about creating a new key pair, see [Create a key pair](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/get-set-up-for-amazon-ec2.html#create-a-key-pair) in the *Amazon EC2 User Guide for Linux Instances*\.

   1. For **Allow SSH traffic** in **Network settings**, choose the source of SSH connections to the EC2 instance\. 

      You can choose **My IP** if the displayed IP address is correct for SSH connections\. Otherwise, you can determine the IP address to use to connect to EC2 instances in your VPC using Secure Shell \(SSH\)\. To determine your public IP address, in a different browser window or tab, you can use the service at [https://checkip\.amazonaws\.com](https://checkip.amazonaws.com/)\. An example of an IP address is 192\.0\.2\.1/32\.

       In many cases, you might connect through an internet service provider \(ISP\) or from behind your firewall without a static IP address\. If so, make sure to determine the range of IP addresses used by client computers\.
**Warning**  
If you use `0.0.0.0/0` for SSH access, you make it possible for all IP addresses to access your public EC2 instances using SSH\. This approach is acceptable for a short time in a test environment, but it's unsafe for production environments\. In production, authorize only a specific IP address or range of addresses to access your EC2 instances using SSH\.

      The following image shows an example of the **Network settings** section\.  
![\[Network settings for an EC2 instance.\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/EC2_RDS_Connect_NtwkSettings.png)

   1. Leave the default values for the remaining sections\.

   1. Review a summary of your EC2 instance configuration in the **Summary** panel, and when you're ready, choose **Launch instance**\.

1. On the **Launch Status** page, note the identifier for your new EC2 instance, for example: `i-1234567890abcdef0`\.  
![\[EC2 instance identifier on Launch Status page.\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/getting-started-ec2-id.png)

1. Choose the EC2 instance identifier to open the list of EC2 instances, and then select your EC2 instance\.

1. In the **Details** tab, note the following values, which you need when you connect using SSH:

   1. In **Instance summary**, note the value for **Public IPv4 DNS**\.  
![\[EC2 public DNS name on Details tab of Instances page.\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/easy-create-ec2-public-dns.png)

   1. In **Instance details**, note the value for **Key pair name**\.  
![\[EC2 key pair name on Details tab of Instance page.\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/easy-create-ec2-key-pair.png)

1. Wait until the **Instance state** for your EC2 instance has a status of **Running** before continuing\.

## Step 2: Create a MySQL DB instance<a name="CHAP_GettingStarted.Creating.MySQL"></a>

The basic building block of Amazon RDS is the DB instance\. This environment is where you run your MySQL databases\.

In this example, you use **Easy create** to create a DB instance running the MySQL database engine with a db\.t3\.micro DB instance class\.

**To create a MySQL DB instance with Easy create**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the upper\-right corner of the Amazon RDS console, choose the AWS Region you used for the EC2 instance previously\.

1. In the navigation pane, choose **Databases**\.

1. Choose **Create database** and make sure that **Easy create** is chosen\.   
![\[Easy create option.\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/easy-create-option.png)

1. In **Configuration**, choose **MySQL**\.

1. For **DB instance size**, choose **Free tier**\.

1. For **DB instance identifier**, enter **database\-test1**\.

1. For **Master username**, enter a name for the master user, or keep the default name\.

   The **Create database** page should look similar to the following image\.  
![\[Create database page.\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/easy-create-mysql.png)

1. To use an automatically generated master password for the DB instance, select **Auto generate a password**\.

   To enter your master password, make sure **Auto generate a password** is cleared, and then enter the same password in **Master password** and **Confirm password**\.

1. To set up a connection with the EC2 instance you created previously, open **Set up EC2 connection \- *optional***\.

   Select **Connect to an EC2 compute resource**\. Choose the EC2 instance you created previously\.  
![\[Set up EC2 connection option.\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/EC2_RDS_Setup_Conn-EasyCreate.png)

1. \(Optional\) Open **View default settings for Easy create**\.  
![\[Easy create default settings.\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/easy-create-view-default-mysql.png)

   You can examine the default settings used with **Easy create**\. The **Editable after database is created** column shows which options you can change after you create the database\.
   + If a setting has **No** in that column, and you want a different setting, you can use **Standard create** to create the DB instance\.
   + If a setting has **Yes** in that column, and you want a different setting, you can either use **Standard create** to create the DB instance, or modify the DB instance after you create it to change the setting\.

1. Choose **Create database**\.

   To view the master username and password for the DB instance, choose **View credential details**\.

   You can use the username and password that appears to connect to the DB instance as the master user\.
**Important**  
You can't view the master user password again\. If you don't record it, you might have to change it\.   
If you need to change the master user password after the DB instance is available, you can modify the DB instance to do so\. For more information about modifying a DB instance, see [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)\.

1. In the **Databases** list, choose the name of the new MySQL DB instance to show its details\.

   The DB instance has a status of **Creating** until it is ready to use\.  
![\[DB instance details.\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/MySQL-Launch06.png)

   When the status changes to **Available**, you can connect to the DB instance\. Depending on the DB instance class and the amount of storage, it can take up to 20 minutes before the new instance is available\.

## Step 3: Connect to a MySQL DB instance<a name="CHAP_GettingStarted.Connecting.MySQL"></a>

You can use any standard SQL client application to connect to the DB instance\. In this example, you connect to a MySQL DB instance using the mysql command\-line client\.

**To connect to a MySQL DB instance**

1. Find the endpoint \(DNS name\) and port number for your DB instance\. 

   1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

   1. In the upper\-right corner of the Amazon RDS console, choose the AWS Region for the DB instance\.

   1. In the navigation pane, choose **Databases**\.

   1. Choose the MySQL DB instance name to display its details\. 

   1. On the **Connectivity & security** tab, copy the endpoint\. Also, note the port number\. You need both the endpoint and the port number to connect to the DB instance\.   
![\[Connect to a MySQL DB instance.\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/MySQLConnect1.png)

1. Connect to the EC2 instance that you created earlier by following the steps in [Connect to your Linux instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstances.html) in the *Amazon EC2 User Guide for Linux Instances*\.

   We recommend that you connect to your EC2 instance using SSH\. If the SSH client utility is installed on Windows, Linux, or Mac, you can connect to the instance using the following command format:

   ```
   ssh -i location_of_pem_file ec2-user@ec2-instance-public-dns-name
   ```

   For example, assume that `ec2-database-connect-key-pair.pem` is stored in `/dir1` on Linux, and the public IPv4 DNS for your EC2 instance is `ec2-12-345-678-90.compute-1.amazonaws.com`\. Your SSH command would look as follows:

   ```
   ssh -i /dir1/ec2-database-connect-key-pair.pem ec2-user@ec2-12-345-678-90.compute-1.amazonaws.com
   ```

1. Get the latest bug fixes and security updates by updating the software on your EC2 instance\. To do this, use the following command\.
**Note**  
The `-y` option installs the updates without asking for confirmation\. To examine updates before installing, omit this option\.

   ```
   sudo dnf update -y
   ```

1.  To install the mysql command\-line client from MariaDB on Amazon Linux 2023, run the following command:

   ```
   sudo dnf install mariadb105
   ```

1. Connect to the MySQL DB instance\. For example, enter the following command\. This action lets you connect to the MySQL DB instance using the MySQL client\.

   Substitute the DB instance endpoint \(DNS name\) for `endpoint`, and substitute the master username that you used for `admin`\. Provide the master password that you used when prompted for a password\.

   ```
   mysql -h endpoint -P 3306 -u admin -p
   ```

   After you enter the password for the user, you should see output similar to the following\.

   ```
   Welcome to the MariaDB monitor.  Commands end with ; or \g.
   Your MySQL connection id is 3082
   Server version: 8.0.28 Source distribution
   
   Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.
   
   Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
   
   MySQL [(none)]>
   ```

   For more information about connecting to a MySQL DB instance, see [Connecting to a DB instance running the MySQL database engine](USER_ConnectToInstance.md)\. If you can't connect to your DB instance, see [Can't connect to Amazon RDS DB instance](CHAP_Troubleshooting.md#CHAP_Troubleshooting.Connecting)\.

   For security, it is a best practice to use encrypted connections\. Only use an unencrypted MySQL connection when the client and server are in the same VPC and the network is trusted\. For information about using encrypted connections, see [Connecting from the MySQL command\-line client with SSL/TLS \(encrypted\)](mysql-ssl-connections.md#USER_ConnectToInstanceSSL.CLI)\.

1. Run SQL commands\.

   For example, the following SQL command shows the current date and time:

   ```
   SELECT CURRENT_TIMESTAMP;
   ```

## Step 4: Delete the EC2 instance and DB instance<a name="CHAP_GettingStarted.Deleting.MySQL"></a>

After you connect to and explore the sample EC2 instance and DB instance that you created, delete them so you're no longer charged for them\.

**To delete the EC2 instance**

1. Sign in to the AWS Management Console and open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the EC2 instance, and choose **Instance state, Terminate instance**\.

1. Choose **Terminate** when prompted for confirmation\.

For more information about deleting an EC2 instance, see [Terminate your instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/terminating-instances.html) in the *Amazon EC2 User Guide for Linux Instances*\.

**To delete the DB instance with no final DB snapshot**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose the DB instance that you want to delete\.

1. For **Actions**, choose **Delete**\.

1. Clear **Create final snapshot?** and **Retain automated backups**\.

1. Complete the acknowledgement and choose **Delete**\. 