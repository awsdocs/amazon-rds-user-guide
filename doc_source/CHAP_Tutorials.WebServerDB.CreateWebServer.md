# Create an EC2 Instance and Install a Web Server<a name="CHAP_Tutorials.WebServerDB.CreateWebServer"></a>

In this step, you create a web server to connect to the Amazon RDS DB instance that you created in [Create a DB Instance](CHAP_Tutorials.WebServerDB.CreateDBInstance.md)\. 

## Launch an EC2 Instance<a name="CHAP_Tutorials.WebServerDB.CreateWebServer.LaunchEC2"></a>

First, you create an Amazon EC2 instance in the public subnet of your VPC\. 

**To launch an EC2 instance**

1. Sign in to the AWS Management Console and open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Choose **EC2 Dashboard**, and then choose **Launch instance**, as shown following\.  
![\[EC2 Dashboard\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/Tutorial_WebServer_11.png)

1. Choose the **Amazon Linux AMI**, as shown following\.  
![\[Choose an Amazon Machine Image\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/Tutorial_WebServer_12.png)
**Important**  
Don't choose **Amazon Linux 2 AMI** because it doesn't have the software packages required for this tutorial\.

1. Choose the **t2\.small** instance type, as shown following, and then choose **Next: Configure Instance Details**\.  
![\[Choose an Instance Type\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/Tutorial_WebServer_13.png)

1. On the **Configure Instance Details** page, shown following, set these values and keep the other values as their defaults:
   + **Network:** Choose the VPC with both public and private subnets that you chose for the DB instance, such as the `vpc-identifier | tutorial-vpc` created in [Create a VPC with Private and Public Subnets](CHAP_Tutorials.WebServerDB.CreateVPC.md#CHAP_Tutorials.WebServerDB.CreateVPC.VPCAndSubnets)\.
   + **Subnet:** Choose an existing public subnet, such as `subnet-identifier | Tutorial public | us-west-2a` created in [ Create a VPC Security Group for a Public Web Server](CHAP_Tutorials.WebServerDB.CreateVPC.md#CHAP_Tutorials.WebServerDB.CreateVPC.SecurityGroupEC2)\.
   + **Auto\-assign Public IP:** Choose **Enable**\.  
![\[Configure Instance Details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/Tutorial_WebServer_14.png)

1. Choose **Next: Add Storage**\.

1. On the **Add Storage** page, keep the default values and choose **Next: Add Tags**\.

1. On the **Add Tags** page, shown following, choose **Add Tag**, then enter **Name** for **Key** and enter **tutorial\-web\-server** for **Value**\.  
![\[Tag Instance\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/Tutorial_WebServer_15.png)

1. Choose **Next: Configure Security Group**\.

1. On the **Configure Security Group** page, shown following, choose **Select an existing security group**\. Then choose an existing security group, such as the `tutorial-securitygroup` created in [ Create a VPC Security Group for a Public Web Server](CHAP_Tutorials.WebServerDB.CreateVPC.md#CHAP_Tutorials.WebServerDB.CreateVPC.SecurityGroupEC2)\. Make sure that the security group that you choose includes inbound rules for Secure Shell \(SSH\) and HTTP access\.   
![\[Configure Security Group\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/Tutorial_WebServer_16.png)

1. Choose **Review and Launch**\.

1. On the **Review Instance Launch** page, shown following, verify your settings and then choose **Launch**\.  
![\[Review Instance Launch\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/Tutorial_WebServer_17.png)

1. On the **Select an existing key pair or create a new key pair** page, shown following, choose **Create a new key pair** and set **Key pair name** to `tutorial-key-pair`\. Choose **Download Key Pair**, and then save the key pair file on your local machine\. You use this key pair file to connect to your EC2 instance\.  
![\[Select an Existing Key Pair or Create a New Key Pair\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/Tutorial_WebServer_18.png)

1. To launch your EC2 instance, choose **Launch Instances**\. On the **Launch Status** page, shown following, note the identifier for your new EC2 instance, for example: `i-0288d65fd4470b6a9`\.  
![\[Launch Status\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/Tutorial_WebServer_19.png)

1. Choose **View Instances** to find your instance\. 

1. Wait until **Instance Status** for your instance reads as **Running** before continuing\. 

## Install an Apache Web Server with PHP<a name="CHAP_Tutorials.WebServerDB.CreateWebServer.Apache"></a>

Next, you connect to your EC2 instance and install the web server\.

**Note**  
This tutorial is designed to work with a MySQL version 5\.6 DB instance\. If you are using a MySQL 8\.0 DB instance instead, you must set the following parameters to the values specified in a customer\-created DB parameter group:  
`character_set_server` – `utf8`
`collation_server` – `utf8_general_ci`
The default settings for these parameters cause the database connection to fail\. Other parameter settings might also correct the problem\. For more information about setting parameters, see [Working with DB Parameter Groups](USER_WorkingWithParamGroups.md)\.  
After you reset the parameters, modify your DB instance to use the DB parameter group, and reboot the DB instance\. For more information, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md) and [Rebooting a DB Instance](USER_RebootInstance.md)\.

**To connect to your EC2 instance and install the Apache web server with PHP**

1. Connect to the EC2 instance that you created earlier by following the steps in [Connect to Your Linux Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstances.html)\.

1. Get the latest bug fixes and security updates by updating the software on your EC2 instance\. To do this, use the following command\.
**Note**  
The `-y` option installs the updates without asking for confirmation\. To examine updates before installing, omit this option\.

   ```
   [ec2-user ~]$ sudo yum update -y
   ```

1. After the updates complete, install the Apache web server with the PHP software package using the `yum install` command\. This command installs multiple software packages and related dependencies at the same time\.

   ```
   [ec2-user ~]$ sudo yum install -y httpd24 php56 php56-mysqlnd
   ```

   If you get the error message `No package package-name available`, then your instance was not launched with the Amazon Linux AMI\. You might be using the Amazon Linux 2 AMI instead\. You can view your version of Amazon Linux with the following command\.

   ```
   cat /etc/system-release
   ```

   For more information, see [Updating Instance Software](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/install-updates.html)\.

1. Start the web server with the command shown following\.

   ```
   [ec2-user ~]$ sudo service httpd start
   ```

   You can test that your web server is properly installed and started\. To do this, enter the public Domain Name System \(DNS\) name of your EC2 instance in the address bar of a web browser, for example: `http://ec2-42-8-168-21.us-west-1.compute.amazonaws.com`\. If your web server is running, then you see the Apache test page\. 

   If you don't see the Apache test page, check your inbound rules for the VPC security group that you created in [Tutorial: Create an Amazon VPC for Use with a DB Instance](CHAP_Tutorials.WebServerDB.CreateVPC.md)\. Make sure that your inbound rules include a rule allowing HTTP \(port 80\) access for the IP address you use to connect to the web server\.
**Note**  
The Apache test page appears only when there is no content in the document root directory, `/var/www/html`\. After you add content to the document root directory, your content appears at the public DNS address of your EC2 instance instead of the Apache test page\.

1. Configure the web server to start with each system boot using the `chkconfig` command\.

   ```
   [ec2-user ~]$ sudo chkconfig httpd on
   ```

To allow `ec2-user` to manage files in the default root directory for your Apache web server, modify the ownership and permissions of the `/var/www` directory\. In this tutorial, you add a group named `www` to your EC2 instance\. Then you give that group ownership of the `/var/www` directory and add write permissions for the group\. Any members of that group can then add, delete, and modify files for the web server\.

**To set file permissions for the Apache web server**

1. Add the `www` group to your EC2 instance with the following command\.

   ```
   [ec2-user ~]$ sudo groupadd www                
   ```

1. Add the `ec2-user` user to the `www` group\.

   ```
   [ec2-user ~]$ sudo usermod -a -G www ec2-user                
   ```

1. Log out to refresh your permissions and include the new `www` group\.

   ```
   [ec2-user ~]$ exit                
   ```

1. Log back in again and verify that the `www` group exists with the `groups` command\.

   ```
   [ec2-user ~]$ groups
   ec2-user wheel www
   ```

1. Change the group ownership of the `/var/www` directory and its contents to the `www` group\.

   ```
   [ec2-user ~]$ sudo chgrp -R www /var/www                
   ```

1. Change the directory permissions of `/var/www` and its subdirectories to add group write permissions and set the group ID on subdirectories created in the future\.

   ```
   [ec2-user ~]$ sudo chmod 2775 /var/www
   [ec2-user ~]$ find /var/www -type d -exec sudo chmod 2775 {} +
   ```

1. Recursively change the permissions for files in the `/var/www` directory and its subdirectories to add group write permissions\.

   ```
   [ec2-user ~]$ find /var/www -type f -exec sudo chmod 0664 {} +                
   ```

## Connect Your Apache Web Server to Your DB Instance<a name="CHAP_Tutorials.WebServerDB.CreateWebServer.PHPContent"></a>

Next, you add content to your Apache web server that connects to your Amazon RDS DB instance\.

**To add content to the Apache web server that connects to your DB instance**

1. While still connected to your EC2 instance, change the directory to `/var/www` and create a new subdirectory named `inc`\.

   ```
   [ec2-user ~]$ cd /var/www
   [ec2-user ~]$ mkdir inc
   [ec2-user ~]$ cd inc
   ```

1. Create a new file in the `inc` directory named `dbinfo.inc`, and then edit the file by calling nano \(or the editor of your choice\)\.

   ```
   [ec2-user ~]$ >dbinfo.inc
   [ec2-user ~]$ nano dbinfo.inc
   ```

1. Add the following contents to the `dbinfo.inc` file\. Here, *db\_instance\_endpoint* is your DB instance endpoint, without the port, and *master password* is the master password for your DB instance\.
**Note**  
We recommend placing the user name and password information in a folder that isn't part of the document root for your web server\. Doing this reduces the possibility of your security information being exposed\.

   ```
   <?php
   
   define('DB_SERVER', 'db_instance_endpoint');
   define('DB_USERNAME', 'tutorial_user');
   define('DB_PASSWORD', 'master password');
   define('DB_DATABASE', 'sample');
   
   ?>
   ```

1. Save and close the `dbinfo.inc` file\.

1. Change the directory to `/var/www/html`\.

   ```
   [ec2-user ~]$ cd /var/www/html
   ```

1. Create a new file in the `html` directory named `SamplePage.php`, and then edit the file by calling nano \(or the editor of your choice\)\.

   ```
   [ec2-user ~]$ >SamplePage.php
   [ec2-user ~]$ nano SamplePage.php
   ```

1. Add the following contents to the `SamplePage.php` file:
**Note**  
We recommend placing the user name and password information in a folder that isn't part of the document root for your web server\. Doing this reduces the possibility of your security information being exposed\.

   ```
   <?php include "../inc/dbinfo.inc"; ?>
   <html>
   <body>
   <h1>Sample page</h1>
   <?php
   
     /* Connect to MySQL and select the database. */
     $connection = mysqli_connect(DB_SERVER, DB_USERNAME, DB_PASSWORD);
   
     if (mysqli_connect_errno()) echo "Failed to connect to MySQL: " . mysqli_connect_error();
   
     $database = mysqli_select_db($connection, DB_DATABASE);
   
     /* Ensure that the EMPLOYEES table exists. */
     VerifyEmployeesTable($connection, DB_DATABASE);
   
     /* If input fields are populated, add a row to the EMPLOYEES table. */
     $employee_name = htmlentities($_POST['NAME']);
     $employee_address = htmlentities($_POST['ADDRESS']);
   
     if (strlen($employee_name) || strlen($employee_address)) {
       AddEmployee($connection, $employee_name, $employee_address);
     }
   ?>
   
   <!-- Input form -->
   <form action="<?PHP echo $_SERVER['SCRIPT_NAME'] ?>" method="POST">
     <table border="0">
       <tr>
         <td>NAME</td>
         <td>ADDRESS</td>
       </tr>
       <tr>
         <td>
           <input type="text" name="NAME" maxlength="45" size="30" />
         </td>
         <td>
           <input type="text" name="ADDRESS" maxlength="90" size="60" />
         </td>
         <td>
           <input type="submit" value="Add Data" />
         </td>
       </tr>
     </table>
   </form>
   
   <!-- Display table data. -->
   <table border="1" cellpadding="2" cellspacing="2">
     <tr>
       <td>ID</td>
       <td>NAME</td>
       <td>ADDRESS</td>
     </tr>
   
   <?php
   
   $result = mysqli_query($connection, "SELECT * FROM EMPLOYEES");
   
   while($query_data = mysqli_fetch_row($result)) {
     echo "<tr>";
     echo "<td>",$query_data[0], "</td>",
          "<td>",$query_data[1], "</td>",
          "<td>",$query_data[2], "</td>";
     echo "</tr>";
   }
   ?>
   
   </table>
   
   <!-- Clean up. -->
   <?php
   
     mysqli_free_result($result);
     mysqli_close($connection);
   
   ?>
   
   </body>
   </html>
   
   
   <?php
   
   /* Add an employee to the table. */
   function AddEmployee($connection, $name, $address) {
      $n = mysqli_real_escape_string($connection, $name);
      $a = mysqli_real_escape_string($connection, $address);
   
      $query = "INSERT INTO EMPLOYEES (NAME, ADDRESS) VALUES ('$n', '$a');";
   
      if(!mysqli_query($connection, $query)) echo("<p>Error adding employee data.</p>");
   }
   
   /* Check whether the table exists and, if not, create it. */
   function VerifyEmployeesTable($connection, $dbName) {
     if(!TableExists("EMPLOYEES", $connection, $dbName))
     {
        $query = "CREATE TABLE EMPLOYEES (
            ID int(11) UNSIGNED AUTO_INCREMENT PRIMARY KEY,
            NAME VARCHAR(45),
            ADDRESS VARCHAR(90)
          )";
   
        if(!mysqli_query($connection, $query)) echo("<p>Error creating table.</p>");
     }
   }
   
   /* Check for the existence of a table. */
   function TableExists($tableName, $connection, $dbName) {
     $t = mysqli_real_escape_string($connection, $tableName);
     $d = mysqli_real_escape_string($connection, $dbName);
   
     $checktable = mysqli_query($connection,
         "SELECT TABLE_NAME FROM information_schema.TABLES WHERE TABLE_NAME = '$t' AND TABLE_SCHEMA = '$d'");
   
     if(mysqli_num_rows($checktable) > 0) return true;
   
     return false;
   }
   ?>
   ```

1. Save and close the `SamplePage.php` file\.

1. Verify that your web server successfully connects to your DB instance by opening a web browser and browsing to `http://EC2 instance endpoint/SamplePage.php`, for example: `http://ec2-55-122-41-31.us-west-2.compute.amazonaws.com/SamplePage.php`\.

You can use `SamplePage.php` to add data to your DB instance\. The data that you add is then displayed on the page\. To verify that the data was inserted into the table, you can install MySQL on the Amazon EC2 instance, connect to the DB instance, and query the table\. 

To make sure that your DB instance is as secure as possible, verify that sources outside of the VPC can't connect to your DB instance\. 