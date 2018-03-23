# Creating a DB Cluster and Connecting to a Database on an Amazon Aurora DB Instance<a name="CHAP_GettingStarted.CreatingConnecting.Aurora"></a>

The easiest way to create an Amazon Aurora DB cluster is to use the Amazon RDS console\. Once you have created the DB cluster, you can use standard MySQL utilities, such as MySQL Workbench, or PostgreSQL utilities, such as pgAdmin, to connect to a database on the DB cluster\.

**Important**  
You must complete the tasks in the [Setting Up for Amazon RDS](CHAP_SettingUp.md) section before you can create or connect to a DB cluster\.

**Topics**
+ [Create a DB Cluster](#CHAP_GettingStarted.Aurora.CreateDBCluster)
+ [Connect to an Instance in a DB Cluster](#CHAP_GettingStarted.Aurora.Connect)
+ [Delete the Sample DB Cluster, DB Subnet Group, and VPC](#CHAP_GettingStarted.Deleting.Aurora)

## Create a DB Cluster<a name="CHAP_GettingStarted.Aurora.CreateDBCluster"></a>

Before you create a DB cluster, you must first have an Amazon Virtual Private Cloud \(VPC\) and an Amazon RDS DB subnet group\. Your VPC must have at least one subnet in each of at least two Availability Zones\. You can use the default VPC for your AWS account, or you can create your own VPC\. The Amazon RDS console makes it easy for you to create your own VPC for use with Amazon Aurora or use an existing VPC with your Aurora DB cluster\.

If you want to create a VPC and DB subnet group for use with your Amazon Aurora DB cluster yourself, rather than having Amazon RDS create the VPC and DB subnet group for you, then follow the instructions in [How to Create a VPC for Use with Amazon Aurora](Aurora.CreateVPC.md)\. Otherwise, follow the instructions in this topic to create your DB cluster and have Amazon RDS create a VPC and DB subnet group for you\.

**Note**  
Aurora is not available in all AWS Regions\. For a list of AWS Regions where Aurora is available, see [Availability](Aurora.Overview.md#Aurora.Overview.Availability)\.

**To launch an Aurora MySQL DB cluster**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the top\-right corner of the AWS Management Console, choose the AWS Region that you want to create your DB cluster in\. For a list of AWS Regions where Aurora is available, see [Availability](Aurora.Overview.md#Aurora.Overview.Availability)\.

1. In the navigation pane, choose **Instances**\.

1. Choose **Launch DB instance** to start the Launch DB Instance wizard\. The wizard opens on the **Select engine** page\.

1. On the **Select engine** page, choose Amazon Aurora and choose the MySQL\-compatible edition\.  
![\[Amazon Aurora Launch DB Instance Wizard Select Engine\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/AuroraLaunch01.png)

1. Choose **Next**\.

1. Set the following values on the **Specify DB details** page: 
   + **DB instance class:** `db.r4.large`
   + **DB instance identifier:** `gs-db-instance1`
   + **Master username:** Using alphanumeric characters, type a master user name, used to log on to your DB instances in the DB cluster\.
   + **Master password** and **Confirm Password:** Type a password in the **Master Password** box that contains from 8 to 41 printable ASCII characters \(excluding /,", and @\) for your master user password, used to log on to your database\. Then type the password again in the **Confirm Password** box\.  
![\[Specify DB details page\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/AuroraLaunch02.png)

1. Choose **Next** and set the following values on the **Configure Advanced Settings** page: 
   + **Virtual Private Cloud \(VPC\):** If you have an existing VPC, then you can use that VPC with your Amazon Aurora DB cluster by choosing your VPC identifier, for example `vpc-a464d1c1`\. For information on using an existing VPC, see [How to Create a VPC for Use with Amazon Aurora](Aurora.CreateVPC.md)\.

     Otherwise, you can choose to have Amazon RDS create a VPC for you by choosing **Create a new VPC**\. This example uses the **Create a new VPC** option\.
   + **Subnet group:** If you have an existing subnet group, then you can use that subnet group with your Amazon Aurora DB cluster by choosing your subnet group identifier, for example, `gs-subnet-group1`\.

     Otherwise, you can choose to have Amazon RDS create a subnet group for you by choosing **Create a new subnet group**\. This example uses the **Create a new subnet group** option\.
   + **Public accessibility:** `Yes`
**Note**  
Your production DB cluster might not need to be in a public subnet, because only your application servers require access to your DB cluster\. If your DB cluster doesn't need to be in a public subnet, set **Publicly Accessible** to `No`\.
   + **Availability zone:** `No Preference`
   + **VPC security groups:** If you have one or more existing VPC security groups, then you can use one or more of those VPC security groups with your Amazon Aurora DB cluster by choosing your VPC security group identifiers, for example, `gs-security-group1`\.

     Otherwise, you can choose to have Amazon RDS create a VPC security group for you by choosing **Create a new Security group**\. This example uses the **Create a new Security group** option\.
   + **DB Cluster Identifier:** `gs-db-cluster1`
   + **Database name:** `sampledb`
**Note**  
This creates the default database\. To create additional databases, connect to the DB cluster and use the SQL command `CREATE DATABASE`\. 
   + **Database port:** `3306`
**Note**  
You might be behind a corporate firewall that does not allow access to default ports such as the Aurora MySQL default port, 3306\. In this case, provide a port value that your corporate firewall allows\. Remember that port value later when you connect to the Aurora DB cluster\.

1. Leave the rest of the values as their defaults, and choose **Launch DB instance** to create the DB cluster and primary instance\.

## Connect to an Instance in a DB Cluster<a name="CHAP_GettingStarted.Aurora.Connect"></a>

Once Amazon RDS provisions your DB cluster and creates the primary instance, you can use any standard SQL client application to connect to a database on the DB cluster\. In this example, you connect to a database on the Aurora MySQL DB cluster using MySQL monitor commands\. One GUI\-based application that you can use to connect is MySQL Workbench\. For more information, go to the [ Download MySQL Workbench](http://dev.mysql.com/downloads/workbench/) page\.

 **To connect to a database on an Aurora MySQL DB cluster using the MySQL monitor** 

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. Choose **Clusters** and click the DB cluster to show the DB cluster details\. On the details page, copy the value for the **Cluster endpoint**\.   
![\[DB Cluster Details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/AuroraLaunch04.png)

1. Type the following command at a command prompt on a client computer to connect to a database on an Aurora MySQL DB cluster using the MySQL monitor\. Use the cluster endpoint to connect to the primary instance, and the master user name that you created previously\. \(You are prompted for a password\.\) If you supplied a port value other than 3306, use that for the `-P` parameter instead\.

   ```
   PROMPT> mysql -h <cluster endpoint> -P 3306 -u <mymasteruser> -p						
   ```

   You should see output similar to the following\.

   ```
   Welcome to the MySQL monitor.  Commands end with ; or \g.
   Your MySQL connection id is 350
   Server version: 5.6.10-log MySQL Community Server (GPL)
   
   Type 'help;' or '\h' for help. Type '\c' to clear the buffer.
   
   mysql>
   ```

For more information about connecting to the DB cluster, see [Connecting to an Amazon Aurora DB Cluster](Aurora.Connecting.md)\.

## Delete the Sample DB Cluster, DB Subnet Group, and VPC<a name="CHAP_GettingStarted.Deleting.Aurora"></a>

Once you have connected to the sample DB cluster that you created, you can delete the DB cluster, DB subnet group, and VPC \(if you created a VPC\)\. 

**To delete a DB cluster**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. Choose **Instances** and then choose the `gs-db-instance1` DB instance\.

1. Choose **Instance actions**, and then choose **Delete**\.

1. Choose **Delete**\. 

**To delete a DB subnet group**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. Choose **Subnet groups** and then choose the `gs-subnet-group1` DB subnet group\.

1. Choose **Delete**\.

1. Choose **Delete**\. 

**To delete a VPC**

1. Sign in to the AWS Management Console and open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. Choose **Your VPCs** and then choose the VPC that was created for this procedure\.

1. Choose **Actions** and then choose **Delete VPC**\.

1. Choose **Delete**\. 