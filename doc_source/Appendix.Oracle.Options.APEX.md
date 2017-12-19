# Oracle Application Express<a name="Appendix.Oracle.Options.APEX"></a>

Amazon RDS supports Oracle Application Express \(APEX\) through the use of the `APEX` and `APEX-DEV` options\. Oracle APEX can be deployed as a run\-time environment or as a full development environment for web\-based applications\. Using Oracle APEX, developers can build applications entirely within the web browser\. For more information, see [Oracle Application Express](https://apex.oracle.com/) in the Oracle documentation\. 

Oracle APEX consists of two main components:

+ A *repository* that stores the metadata for APEX applications and components\. The repository consists of tables, indexes, and other objects that are installed in your Amazon RDS DB instance\. 

+ A *listener* that manages HTTP communications with Oracle APEX clients\. The listener accepts incoming connections from web browsers, forwards them to the Amazon RDS DB instance for processing, and then sends results from the repository back to the browsers\. The APEX Listener was renamed Oracle Rest Data Services \(ORDS\) in Oracle 12c\. 

When you add the Amazon RDS APEX options to your DB instance, Amazon RDS installs the Oracle APEX repository only\. You must install the Oracle APEX Listener on a separate host, such as an Amazon EC2 instance, an on\-premises server at your company, or your desktop computer\. 

Amazon RDS supports the following versions of Oracle APEX for Oracle 12c: 

+ Oracle APEX version 5\.1\.2\.v1

+ Oracle APEX version 5\.0\.4\.v1

+ Oracle APEX version 4\.2\.6\.v1

Amazon RDS supports the following versions of Oracle APEX for Oracle 11g: 

+ Oracle APEX version 5\.1\.2\.v1

+ Oracle APEX version 5\.0\.4\.v1

+ Oracle APEX version 4\.2\.6\.v1

+ Oracle APEX version 4\.1\.1\.v1

## Prerequisites for Oracle APEX and APEX Listener<a name="Appendix.Oracle.Options.APEX.PreReqs"></a>

The following are prerequisites for using Oracle APEX and APEX Listener: 

+ You must have SQL\*Plus to perform administrative tasks on your DB instance\. 

+ You must have the following software installed on the host computer that acts as the Oracle APEX Listener: 

  + The Java Runtime Environment \(JRE\)\.

  + Oracle Net Services, to enable the Oracle APEX Listener to connect to your Amazon RDS instance\. 

## Adding the Amazon RDS APEX Options<a name="Appendix.Oracle.Options.APEX.Add"></a>

The general process for adding the Amazon RDS APEX options to a DB instance is the following: 

1. Create a new option group, or copy or modify an existing option group\.

1. Add the options to the option group\.

1. Associate the option group with the DB instance\.

When you add the Amazon RDS APEX options, a brief outage occurs while your DB instance is automatically restarted\. 

**To add the APEX options to a DB instance**

1. Determine the option group you want to use\. You can create a new option group or use an existing option group\. If you want to use an existing option group, skip to the next step\. Otherwise, create a custom DB option group with the following settings: 

   1. For **Engine**, choose the Oracle edition that you want to use\. The APEX options are supported on all editions\. 

   1. For **Major Engine Version**, choose **11\.2** or **12\.1**\. 

   1. For **APEX Version**, choose the version of `APEX` that you want to use\. If you don't choose a version, version 4\.1\.1\.v1 is the default for 11g, and version 4\.2\.6\.v1 is the default for 12c\. 

   For more information, see [Creating an Option Group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.Create)\. 

1. Add the options to the option group\. If you want to deploy only the Oracle APEX run\-time environment, add only the `APEX` option\. If you want to deploy the full development environment, add both the `APEX` and `APEX-DEV` options\. 

   + For Oracle 12c, add the **APEX** and **APEX\-DEV** options\.

   + For Oracle 11g, first add the **XMLDB** option as a prerequisite, then add the **APEX** and **APEX\-DEV** options\. 
**Important**  
If you add the APEX options to an existing option group that is already attached to one or more DB instances, a brief outage occurs while all the DB instances are automatically restarted\. 

   For more information about adding options, see [Adding an Option to an Option Group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.AddOption)\. 

1. Apply the option group to a new or existing DB instance: 

   + For a new DB instance, you apply the option group when you launch the instance\. For more information, see [Creating a DB Instance Running the Oracle Database Engine](USER_CreateOracleInstance.md)\. 

   + For an existing DB instance, you apply the option group by modifying the instance and attaching the new option group\. When you add the APEX options to an existing DB instance, a brief outage occurs while your DB instance is automatically restarted\. For more information, see [Modifying a DB Instance Running the Oracle Database Engine](USER_ModifyInstance.Oracle.md)\. 

## Unlocking the Public User Account<a name="Appendix.Oracle.Options.APEX.PublicUser"></a>

After the Amazon RDS APEX options are installed, you must change the password for the APEX public user account, and then unlock the account\. You can do this by using the Oracle SQL\*Plus command line utility\. Connect to your DB instance as the master user, and issue the following commands\. Replace `new_password` with a password of your choice\. 

```
1. alter user APEX_PUBLIC_USER identified by new_password;
2. alter user APEX_PUBLIC_USER account unlock;
```

## Installing and Configuring the APEX Listener<a name="Appendix.Oracle.Options.APEX.Listener"></a>

You are now ready to install and configure a listener for use with Oracle APEX\. You can use one of these products for this purpose: 

+ For APEX version 5\.0 and later, use Oracle Rest Data Services \(ORDS\)

+ For APEX version 4\.1\.1, use Oracle APEX Listener version 1\.1\.4

+ Oracle HTTP Server and `mod_plsql`

**Note**  
Amazon RDS doesn't support the Oracle XML DB HTTP server with the embedded PL/SQL gateway; you can't use this as an APEX Listener\. In general, Oracle recommends against using the embedded PL/SQL gateway for applications that run on the internet\. 

You must install the APEX Listener on a separate host such as an Amazon EC2 instance, an on\-premises server at your company, or your desktop computer\. 

The following procedure shows you how to install and configure the APEX Listener\. We assume that the name of your host is `myapexhost.example.com`, and that your host is running Linux\. 

**To install and configure the APEX Listener**

1. Log in to `myapexhost.example.com` as `root`\. 

1. Create a nonprivileged OS user to own the APEX Listener installation\. The following command creates a new user named *apexuser*\. 

   ```
   1. useradd -d /home/apexuser apexuser
   ```

   The following command assigns a password to the new user\. 

   ```
   1. passwd apexuser;
   ```

1. Log in to `myapexhost.example.com` as `apexuser`, and download the APEX and APEX Listener installation files from Oracle: 

   + [http://www\.oracle\.com/technetwork/developer\-tools/apex/downloads/index\.html](http://www.oracle.com/technetwork/developer-tools/apex/downloads/index.html) 

   + [http://www\.oracle\.com/technetwork/developer\-tools/apex\-listener/downloads/index\.html](http://www.oracle.com/technetwork/developer-tools/apex-listener/downloads/index.html) 

   + [Oracle Application Express Prior Release Archives](http://www.oracle.com/technetwork/developer-tools/apex/downloads/all-archives-099381.html) 

1. Unzip the APEX file:  
****    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Appendix.Oracle.Options.APEX.html)

1. Create a new directory and open the APEX Listener file:  
****    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Appendix.Oracle.Options.APEX.html)

1. While you are still in the directory from the previous step, run the listener program\.   
****    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Appendix.Oracle.Options.APEX.html)

1. You must set a password for the APEX `admin` user\. To do this, use SQL\*Plus to connect to your DB instance as the master user, and then issue the following commands: 

   ```
   1. EXEC rdsadmin.rdsadmin_util.grant_apex_admin_role;
   2. grant APEX_ADMINISTRATOR_ROLE to master;
   3. @/home/apexuser/apex/apxchpwd.sql
   ```

   Replace `master` with your master user name\. When the `apxchpwd.sql` script prompts you, type a new `admin` password\. 

1. For ORDS, start the APEX Listener\. Run the following code: 

   ```
   java -jar ords.war
   ```

   The first time you start the APEX Listener, you are prompted to provide the location of the APEX Static resources\. This images folder is located in the /apex/images directory in the installation directory for APEX\. 

1. Return to the APEX administration window in your browser and choose **Administration**\. Next, choose **Application Express Internal Administration**\. When you are prompted for credentials, type the following information: 

   + **User name** – `admin` 

   + **Password** – the password you set using the `apxchpwd.sql` script 

   Choose **Login**, and then set a new password for the `admin` user\. 

The APEX Listener is now ready for use\.

## Upgrading the APEX Version<a name="Appendix.Oracle.Options.APEX.Upgrade"></a>

If you are planning to do a major version upgrade of your DB instance, and you are using an APEX version that is not compatible with your target database version, you can upgrade your version of APEX at the same time as your DB instance\. This can reduce the time it takes to upgrade your DB instance\. 

**Important**  
Back up your DB instance before you upgrade APEX\. For more information, see [Creating a DB Snapshot](USER_CreateSnapshot.md) and [Testing an Upgrade](USER_UpgradeDBInstance.Oracle.md#USER_UpgradeDBInstance.Oracle.UpgradeTesting)\. 

To upgrade APEX with your DB instance, do the following: 

+ Create a new option group for the upgraded version of your DB instance\. 

+ Add the upgraded versions of APEX and APEX\-DEV to the new option group\. Be sure to include any other options that your DB instance uses\. For more information, see [Option Group Considerations](USER_UpgradeDBInstance.Oracle.md#USER_UpgradeDBInstance.Oracle.OGPG.OG)\. 

+ When you upgrade your DB instance, specify the new option group for your upgraded DB instance\. 

After you upgrade your version of APEX, the APEX schema for the previous version might still exist in your database\. If you don't need it anymore, you can drop the old APEX schema from your database after you upgrade\. 

## Removing the APEX Option<a name="Appendix.Oracle.Options.APEX.Remove"></a>

You can remove the Amazon RDS APEX options from a DB instance\. To remove the APEX options from a DB instance, do one of the following: 

+ To remove the APEX options from multiple DB instances, remove the APEX options from the option group they belong to\. This change affects all DB instances that use the option group\. When you remove the APEX options from an option group that is attached to multiple DB instances, a brief outage occurs while all the DB instances are restarted\. 

  For more information, see [Removing an Option from an Option Group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.RemoveOption)\. 

+ To remove the APEX options from a single DB instance, modify the DB instance and specify a different option group that doesn't include the APEX options\. You can specify the default \(empty\) option group, or a different custom option group\. When you remove the APEX options, a brief outage occurs while your DB instance is automatically restarted\. 

  For more information, see [Modifying a DB Instance Running the Oracle Database Engine](USER_ModifyInstance.Oracle.md)\. 

When you remove the APEX options from a DB instance, the APEX schema is removed from your database\. 

## Related Topics<a name="Appendix.Oracle.Options.APEX.Related"></a>

+ [Working with Option Groups](USER_WorkingWithOptionGroups.md)

+ [Options for Oracle DB Instances](Appendix.Oracle.Options.md)