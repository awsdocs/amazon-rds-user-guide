# Creating an Oracle DB instance and connecting to a database on an Oracle DB instance<a name="CHAP_GettingStarted.CreatingConnecting.Oracle"></a>

The basic building block of Amazon RDS is the DB instance\. Your Amazon RDS DB instance is similar to your on\-premises Oracle database\. 

**Important**  
Before you can create or connect to a DB instance, make sure to complete the tasks in [Setting up for Amazon RDS](CHAP_SettingUp.md)\.

There's no charge for creating an AWS account\. However, by completing this tutorial, you might incur costs for the AWS resources that you use\. You can delete these resources after you complete the tutorial if they are no longer needed\.

In this topic, you create a sample Oracle DB instance and connect to it\. Finally, you delete the sample DB instance\. 

## Creating a sample Oracle DB instance<a name="CHAP_GettingStarted.Creating.Oracle"></a>

The DB instance is where you run your Oracle databases\.

**Note**  
RDS for Oracle supports a single\-tenant architecture, where a pluggable database \(PDB\) resides in a multitenant container database \(CDB\)\. For more information, see [RDS for Oracle architecture](Oracle.Concepts.single-tenant.md)\.

You can use **Easy create** to create a DB instance running Oracle with the AWS Management Console\. With **Easy create**, you specify only the DB engine type, DB instance size, and DB instance identifier\. **Easy create** uses the default settings for the other configuration options\. When you use **Standard create** instead of **Easy create**, you specify more configuration options when you create a database, including ones for availability, security, backups, and maintenance\.

In this example, you use **Easy create** to create a DB instance running the Oracle database engine with a db\.m4\.large DB instance class\.

For information about creating DB instances with **Standard create**, see [Creating an Amazon RDS DB instance](USER_CreateDBInstance.md)\. If you want to use free tier, use **Standard create**\.

**To create an Oracle DB instance with Easy create enabled**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the upper\-right corner of the Amazon RDS console, choose the AWS Region in which you want to create the DB instance\.

1. In the navigation pane, choose **Databases**\.

1. Choose **Create database** and ensure that **Easy create** is chosen\.  
![\[Easy create option\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/easy-create-option.png)

1. In **Configuration**, choose **Oracle**\.

1. For **DB instance size**, choose **Free tier**\. If **Free tier** isn't available, choose **Dev/Test**\.

1. For **DB instance identifier**, enter a name for the DB instance, or leave the default name of **database\-1**\.

1. For **Master username**, enter a name for the master user, or leave the default name\.

   The **Create database** page should look similar to the following image\.  
![\[Create database page\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/easy-create-oracle.png)

1. To use an automatically generated master password for the DB instance, make sure that the **Auto generate a password** box is selected\.

   To enter your master password, clear the **Auto generate a password** box, and then enter the same password in **Master password** and **Confirm password**\.

1. \(Optional\) Open **View default settings for Easy create**\.  
![\[Display Easy create settings for Oracle DB\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/easy-create-view-default-settings.png)

   You can examine the default settings used with **Easy create**\. The **Editable after database is created** column shows which options you can change after database creation\.
   + To change settings with **No** in that column, use **Standard create**\. 
   + To change settings with **Yes** in that column, either use **Standard create**, or modify the DB instance after it is created to change the settings\.

   The following are important considerations for changing the default settings:
   + In some cases, you might want your DB instance to use a specific virtual private cloud \(VPC\) based on the Amazon VPC service\. Or you might require a specific subnet group or security group\. If so, use **Standard create** to specify these resources\. You might have created these resources when you set up for Amazon RDS\. For more information, see [Provide access to your DB instance in your VPC by creating a security group](CHAP_SettingUp.md#CHAP_SettingUp.SecurityGroup)\.
   + If you want to be able to access the DB instance from a client outside of its VPC, use **Standard create** to set **Public access** to **Yes**\.

     If the DB instance should be private, leave **Public access** set to **No**\.
   + If you want to use free tier, use **Standard create** to set the Oracle version lower than version 12\.2, and then choose **Free tier** in **Templates**\.

1. Choose **Create database**\.

   If you used an automatically generated password, the **View credential details** button appears on the **Databases** page\.

   To view the master user name and password for the DB instance, choose **View credential details**\.

   To connect to the DB instance as the master user, use the user name and password that appear\.
**Important**  
You can't view the master user password again\. If you don't record it, you might have to change it\.   
If you need to change the master user password after the DB instance is available, you can modify the DB instance to do so\. For more information about modifying a DB instance, see [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)\.

1. For **Databases**, choose the name of the new Oracle DB instance\.

   On the RDS console, the details for new DB instance appear\. The DB instance has a status of **Creating** until the DB instance is ready to use\. When the state changes to **Available**, you can connect to the DB instance\. Depending on the DB instance class and the amount of storage, it can take up to 20 minutes before the new instance is available\.   
![\[Displays the DB instance details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/Oracle-Launch05.png)

## Connecting an EC2 instance and an Oracle DB instance automatically<a name="CHAP_GettingStarted.Connecting.EC2.Oracle"></a>

You can automatically connect an existing EC2 instance to the DB instance from the RDS console\. The RDS console simplifies setting up the connection between an EC2 instance and your Oracle DB instance\.

Before setting up a connection between an EC2 instance and an RDS database, make sure you meet the requirements described in [Overview of automatic connectivity with an EC2 instance](ec2-rds-connect.md#ec2-rds-connect-overview)\. If you make changes to required security groups after you configure connectivity, the changes might affect the connection between the EC2 instance and the RDS database\.

**Note**  
You can only set up a connection between an EC2 instance and an RDS database automatically by using the AWS Management Console\. You can't set up a connection automatically with the AWS CLI or RDS API\.

**To connect an EC2 instance and an RDS database automatically**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**, and then choose the RDS database\.

1. For **Actions**, choose **Set up EC2 connection**\.

   The **Set up EC2 connection** page appears\.  
![\[Set up EC2 connection page\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/auto-connect-rds-ec2-set-up.png)

1. On the **Set up EC2 connection** page, choose the EC2 instance\.

   If no EC2 instances exist in the same VPC, choose **Create EC2 instance** to create one\. In this case, make sure the new EC2 instance is in the same VPC as the RDS database\.

1. Choose **Continue**\.

   The **Review and confirm** page appears\.  
![\[EC2 connection review and confirmation page\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/auto-connect-rds-ec2-confirm.png)

1. On the **Review and confirm** page, review the changes that RDS will make to set up a connection with the EC2 instance\. Do one of the following:
   + If the changes are correct, choose **Set up connection**\.
   + If the changes aren't correct, choose **Previous** or **Cancel**\.

1. To verify that the connection is made, choose **Connectivity and security** in the console and find the EC2 resource identifier under **Connected compute resource**\.

## Connecting to your sample Oracle DB instance<a name="CHAP_GettingStarted.Connecting.Oracle"></a>

After Amazon RDS provisions your DB instance, you can use any standard SQL client application to connect to the DB instance\. In this procedure, you connect to your sample DB instance by using the Oracle sqlplus command line utility\. To download a standalone version of this utility, see [SQL\*Plus User's Guide and Reference](https://docs.oracle.com/en/database/oracle/oracle-database/19/sqpug/SQL-Plus-instant-client.html#GUID-9DC272F8-0805-4582-87C6-67B2BC816A2C)\.

**To connect to a DB instance using SQL\*Plus**

1. Make sure your DB instance is associated with a security group that provides access to it\. For more information, see [Provide access to your DB instance in your VPC by creating a security group](CHAP_SettingUp.md#CHAP_SettingUp.SecurityGroup)\.

   If you didn't specify the appropriate security group when you created the DB instance, you can modify the DB instance to change its security group\. For more information, see [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)\.

   If your DB instance is publicly accessible, make sure its associated security group has inbound rules for the IP addresses that you want to access it\. If your DB instance is private, make sure its associated security group has inbound rules for the security group of each resource to access it\. An example is the security group for an Amazon EC2 instance\.

1. Find the endpoint \(DNS name\) and port number for your DB instance\. 

   1. Open the RDS console and then choose **Databases** to display a list of your DB instances\. 

   1. Choose the Oracle DB instance name to display its details\. 

   1. On the **Connectivity & security** tab, copy the following pieces of information:
      + Endpoint
      + Port

      You need both the endpoint and the port number to connect to the DB instance\.   
![\[My DB instances list\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/OracleConnect1.png)

   1. On the **Configuration** tab, copy the following pieces of information:
      + DB name \(not the DB instance ID\)
      + Master user name

      You need both the DB name and the master user name to connect to the DB instance\. 

1. Enter the following command on one line at a command prompt to connect to your DB instance by using the sqlplus utility\. Use the following values:
   + For `dbuser`, enter the name of the master user that you copied in the preceding steps\.
   + For `HOST=endpoint`, enter the endpoint that you copied in the preceding steps\.
   + For `PORT=portnum`, enter the port number that you copied in the preceding steps\.
   + For `SID=DB_NAME`, enter the Oracle database name \(not the instance name\) that you copied in the preceding steps\.

   ```
   sqlplus 'dbuser@(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=endpoint)(PORT=portnum))(CONNECT_DATA=(SID=DB_NAME)))'
   ```

   You should see output similar to the following\. 

   ```
   SQL*Plus: Release 11.1.0.7.0 - Production on Wed May 25 15:13:59 2011
   
   SQL>
   ```

For more information about connecting to an Oracle DB instance, see [Connecting to your Oracle DB instance](USER_ConnectToOracleInstance.md)\. For information on connection issues, see [Can't connect to Amazon RDS DB instance](CHAP_Troubleshooting.md#CHAP_Troubleshooting.Connecting)\.

## Deleting your sample DB instance<a name="CHAP_GettingStarted.Deleting.Oracle"></a>

After you explore the sample DB instance that you created, delete it so that you're no longer charged for it\. 

**To delete a DB instance with no final DB snapshot**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose the DB instance that you want to delete\.

1. For **Actions**, choose **Delete**\.

1. For **Create final snapshot?**, choose **No**, and choose the acknowledgment\.

1. Choose **Delete**\. 