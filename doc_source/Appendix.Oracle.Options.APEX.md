# Oracle Application Express<a name="Appendix.Oracle.Options.APEX"></a>

Amazon RDS supports Oracle Application Express \(APEX\) through the use of the `APEX` and `APEX-DEV` options\. Oracle APEX can be deployed as a run\-time environment or as a full development environment for web\-based applications\. Using Oracle APEX, developers can build applications entirely within the web browser\. For more information, see [Oracle Application Express](https://apex.oracle.com/) in the Oracle documentation\. 

Oracle APEX consists of the following main components:
+ A *repository* that stores the metadata for APEX applications and components\. The repository consists of tables, indexes, and other objects that are installed in your Amazon RDS DB instance\. 
+ A *listener* that manages HTTP communications with Oracle APEX clients\. The listener accepts incoming connections from web browsers, forwards them to the Amazon RDS DB instance for processing, and then sends results from the repository back to the browsers\. Amazon RDS for Oracle supports the following types of listeners:
  + For APEX version 5\.0 and later, use Oracle Rest Data Services \(ORDS\) version 19\.1 and higher\. We recommend that you use the latest supported version of Oracle APEX and ORDS\. The documentation describes older versions for backwards compatibility only\.
  + For APEX version 4\.1\.1, you can use Oracle APEX Listener version 1\.1\.4\.
  + Oracle HTTP Server and `mod_plsql`\.
**Note**  
Amazon RDS doesn't support the Oracle XML DB HTTP server with the embedded PL/SQL gateway; you can't use this as a listener for APEX\. In general, Oracle recommends against using the embedded PL/SQL gateway for applications that run on the internet\. 

  For more information about these listener types, see [About Choosing a Web Listener](https://docs.oracle.com/database/apex-5.1/HTMIG/choosing-web-listener.htm#HTMIG29321) in the Oracle documentation\.

When you add the Amazon RDS APEX options to your DB instance, Amazon RDS installs the Oracle APEX repository only\. Install your listener on a separate host, such as an Amazon EC2 instance, an on\-premises server at your company, or your desktop computer\.

The APEX option uses storage on the DB instance class for your DB instance\. Following are the supported versions and approximate storage requirements for Oracle APEX\.


****  

| APEX Version | Storage Requirements | Supported Oracle Database Versions | Notes | 
| --- | --- | --- | --- | 
|  Oracle APEX version 20\.1\.v1  |  173 MiB  |  All  |  APEX version 20\.1\.v1 includes patch 30990551\. You can see the patch number and date by running the following query: <pre>SELECT PATCH_VERSION, PATCH_NUMBER <br />FROM   APEX_PATCHES;</pre>  | 
|  Oracle APEX version 19\.2\.v1  |  149 MiB  |  All  |  | 
|  Oracle APEX version 19\.1\.v1  |  148 MiB  |  All  |  | 
|  Oracle APEX version 18\.2\.v1  |  146 MiB  |  All except 19c  |  | 
|  Oracle APEX version 18\.1\.v1  |  145 MiB  | All except 19c |  | 
|  Oracle APEX version 5\.1\.4\.v1  |  220 MiB  |  All except 19c \(see Notes\)  |  Oracle APEX 5\.1\.4 for Oracle 11g isn't supported when the DB instance class used by the DB instance has only one vCPU\. For information about DB instance classes, see [DB Instance Classes](Concepts.DBInstanceClass.md)\.  | 
|  Oracle APEX version 5\.1\.2\.v1  |  150 MiB  |  12\.1 and 11g only \(see Notes\)  |  Oracle APEX 5\.1\.2 for Oracle 11g isn't supported when the DB instance class used by the DB instance has only one vCPU\. For information about DB instance classes, see [DB Instance Classes](Concepts.DBInstanceClass.md)\.  | 
|  Oracle APEX version 5\.0\.4\.v1  |  140 MiB  |  12\.1 and 11g only \(see Notes\)  |  Oracle APEX 5\.0\.4 for Oracle 11g isn't supported when the DB instance class used by the DB instance has only one vCPU\. For information about DB instance classes, see [DB Instance Classes](Concepts.DBInstanceClass.md)\.  | 
|  Oracle APEX version 4\.2\.6\.v1  |  160 MiB  |  12\.1 and 11g only  |  | 
|  Oracle APEX version 4\.1\.1\.v1  |  130 MiB  |  11g only  |  | 

## Prerequisites for Oracle APEX and ORDS<a name="Appendix.Oracle.Options.APEX.PreReqs"></a>

To use Oracle APEX and ORDS, make sure you have the following: 
+ The Java Runtime Environment \(JRE\)
+ An Oracle client installation that includes the following:
  + SQL\*Plus or SQL Developer for administration tasks
  + Oracle Net Services for configuring connections to your Oracle instance

## Adding the Amazon RDS APEX Options<a name="Appendix.Oracle.Options.APEX.Add"></a>

The general process for adding the Amazon RDS APEX options to a DB instance is the following: 

1. Create a new option group, or copy or modify an existing option group\.

1. Add the options to the option group\.

1. Associate the option group with the DB instance\.

When you add the Amazon RDS APEX options, a brief outage occurs while your DB instance is automatically restarted\. 

**To add the APEX options to a DB instance**

1. Determine the option group you want to use\. You can create a new option group or use an existing option group\. If you want to use an existing option group, skip to the next step\. Otherwise, create a custom DB option group with the following settings: 

   1. For **Engine**, choose the Oracle edition that you want to use\. The APEX options are supported on all editions\. 

   1. For **Major engine version**, choose the version of your DB instance\. 

   For more information, see [Creating an Option Group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.Create)\. 

1. Add the options to the option group\. If you want to deploy only the Oracle APEX run\-time environment, add only the `APEX` option\. If you want to deploy the full development environment, add both the `APEX` and `APEX-DEV` options\. 
   + For Oracle 12c, add the **APEX** and **APEX\-DEV** options\.
   + For Oracle 11g, first add the **XMLDB** option as a prerequisite, then add the **APEX** and **APEX\-DEV** options\. 

   For **Version**, choose the version of `APEX` that you want to use\. If you don't choose a version, version 4\.1\.1\.v1 is the default for 11g, and version 4\.2\.6\.v1 is the default for 12c\. 
**Important**  
If you add the APEX options to an existing option group that is already attached to one or more DB instances, a brief outage occurs\. During this outage, all the DB instances are automatically restarted\. 

   For more information about adding options, see [Adding an Option to an Option Group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.AddOption)\. 

1. Apply the option group to a new or existing DB instance: 
   + For a new DB instance, you apply the option group when you launch the instance\. For more information, see [Creating an Amazon RDS DB Instance](USER_CreateDBInstance.md)\. 
   + For an existing DB instance, you apply the option group by modifying the instance and attaching the new option group\. When you add the APEX options to an existing DB instance, a brief outage occurs while your DB instance is automatically restarted\. For more information, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md)\. 

## Unlocking the Public User Account<a name="Appendix.Oracle.Options.APEX.PublicUser"></a>

After the Amazon RDS APEX options are installed, you must change the password for the APEX public user account, and then unlock the account\. You can do this by using the Oracle SQL\*Plus command line utility\. Connect to your DB instance as the master user, and issue the following commands\. Replace `new_password` with a password of your choice\. 

```
1. alter user APEX_PUBLIC_USER identified by new_password;
2. alter user APEX_PUBLIC_USER account unlock;
```

## Configuring RESTful Services for Oracle APEX<a name="Appendix.Oracle.Options.APEX.ConfigureRESTful"></a>

To configure RESTful services in APEX \(not needed for APEX 4\.1\.1\.V1\), use SQL\*Plus to connect to your DB instance as the master user\. After you do this, run the `rdsadmin.rdsadmin_run_apex_rest_config` stored procedure\. When you run the stored procedure, you provide passwords for the following users:
+ `APEX_LISTENER`
+ `APEX_REST_PUBLIC_USER`

The stored procedure runs the `apex_rest_config.sql` script, which creates new database accounts for these users\.

**Note**  
Configuration isn't required for Oracle APEX version 4\.1\.1\.v1\. For this Oracle APEX version only, you don't need to run the stored procedure\.

The following command runs the stored procedure\.

```
1. exec rdsadmin.rdsadmin_run_apex_rest_config('apex_listener_password', 'apex_rest_public_user_password');
```

## Setting Up ORDS for Oracle APEX<a name="Appendix.Oracle.Options.APEX.ORDS"></a>

You are now ready to install and configure Oracle Rest Data Services \(ORDS\) for use with Oracle APEX\. For APEX version 5\.0 and later, use Oracle Rest Data Services \(ORDS\) version 19\.1 and higher\. 

Install the listener on a separate host such as an Amazon EC2 instance, an on\-premises server at your company, or your desktop computer\. For the examples in this section, we assume that the name of your host is `myapexhost.example.com`, and that your host is running Linux\.

### Preparing to Install ORDS<a name="Appendix.Oracle.Options.APEX.ORDS.ords-setup"></a>

Before you can install ORDS, you need to create a nonprivileged OS user, and then download and unzip the APEX installation file\.

**To prepare for ORDS installation**

1. Log in to `myapexhost.example.com` as `root`\. 

1. Create a nonprivileged OS user to own the listener installation\. The following command creates a new user named *apexuser*\. 

   ```
   useradd -d /home/apexuser apexuser
   ```

   The following command assigns a password to the new user\. 

   ```
   passwd apexuser;
   ```

1. Log in to `myapexhost.example.com` as `apexuser`, and download the APEX installation file from Oracle to your `/home/apexuser` directory: 
   + [http://www\.oracle\.com/technetwork/developer\-tools/apex/downloads/index\.html](http://www.oracle.com/technetwork/developer-tools/apex/downloads/index.html) 
   + [Oracle Application Express Prior Release Archives](http://www.oracle.com/technetwork/developer-tools/apex/downloads/all-archives-099381.html) 

1. Unzip the file in the `/home/apexuser` directory\.

   ```
   unzip apex_<version>.zip                
   ```

   After you unzip the file, there is an `apex` directory in the `/home/apexuser` directory\.

1. While you are still logged into `myapexhost.example.com` as `apexuser`, download the Oracle REST Data Services file from Oracle to your `/home/apexuser` directory: [http://www\.oracle\.com/technetwork/developer\-tools/apex\-listener/downloads/index\.html](http://www.oracle.com/technetwork/developer-tools/apex-listener/downloads/index.html)\.

### Installing and Configuring ORDS<a name="Appendix.Oracle.Options.APEX.ORDS.ords-install"></a>

Before you can use APEX, you need to download the ords\.war file, use Java to install ORDS, and then start the listener\.

**To install and configure ORDS for use with Oracle APEX**

1. Create a new directory based on ORDS, and then unzip the listener file\.

   ```
   mkdir /home/apexuser/ORDS
   cd /home/apexuser/ORDS
   ```

1. Download the file ords\.*version\.number*\.zip from [Oracle REST Data Services](http://www.oracle.com/technetwork/developer-tools/rest-data-services/downloads/index.html)\.

1. Unzip the file into the `/home/apexuser/ORDS` directory\.

1. Grant the master user the required privileges to install ORDS\.

   After the Amazon RDS APEX option is installed, give the master user the required privileges to install the ORDS schema\. You can do this by connecting to the database and running the following commands\. Replace `master_user` with the name of your master user\.

   ```
   exec rdsadmin.rdsadmin_util.grant_sys_object('DBA_OBJECTS', 'master_user', 'SELECT', true);
   exec rdsadmin.rdsadmin_util.grant_sys_object('DBA_ROLE_PRIVS', 'master_user', 'SELECT', true);
   exec rdsadmin.rdsadmin_util.grant_sys_object('DBA_TAB_COLUMNS', 'master_user', 'SELECT', true);
   exec rdsadmin.rdsadmin_util.grant_sys_object('USER_CONS_COLUMNS', 'master_user', 'SELECT', true);
   exec rdsadmin.rdsadmin_util.grant_sys_object('USER_CONSTRAINTS', 'master_user', 'SELECT', true);
   exec rdsadmin.rdsadmin_util.grant_sys_object('USER_OBJECTS', 'master_user', 'SELECT', true);
   exec rdsadmin.rdsadmin_util.grant_sys_object('USER_PROCEDURES', 'master_user', 'SELECT', true);
   exec rdsadmin.rdsadmin_util.grant_sys_object('USER_TAB_COLUMNS', 'master_user', 'SELECT', true);
   exec rdsadmin.rdsadmin_util.grant_sys_object('USER_TABLES', 'master_user', 'SELECT', true);
   exec rdsadmin.rdsadmin_util.grant_sys_object('USER_VIEWS', 'master_user', 'SELECT', true);
   exec rdsadmin.rdsadmin_util.grant_sys_object('WPIUTL', 'master_user', 'EXECUTE', true);
   exec rdsadmin.rdsadmin_util.grant_sys_object('DBMS_SESSION', 'master_user', 'EXECUTE', true);
   exec rdsadmin.rdsadmin_util.grant_sys_object('DBMS_UTILITY', 'master_user', 'EXECUTE', true);
   ```
**Note**  
These commands apply to ORDS version 19\.1 and later\.

1. Install the ORDS schema using the downloaded ords\.war file\.

   ```
   java -jar ords.war install advanced
   ```

   The program prompts you for the following information\. The default values are in brackets\. For more information, see [Introduction to Oracle REST Data Services](https://docs.oracle.com/en/database/oracle/oracle-rest-data-services/20.2/aelig/installing-REST-data-services.html#GUID-6F7B4E61-B730-4E73-80B8-F53299123730) in the Oracle documentation\.
   + Enter the location to store configuration data:

     Enter `/home/apexuser/ORDS`\. This is the location of the ORDS configuration files\.
   + Specify the database connection type to use\. Enter number for \[1\] Basic \[2\] TNS \[3\] Custom URL \[1\]:

     Choose the desired connection type\.
   + Enter the name of the database server \[localhost\]: *DB\_instance\_endpoint*

     Choose the default or enter the correct value\.
   + Enter the database listener port \[1521\]: *DB\_instance\_port*

     Choose the default or enter the correct value\.
   + Enter 1 to specify the database service name, or 2 to specify the database SID \[1\]:

     Choose `2` to specify the database SID\. 
   + Database SID \[xe\]

     Choose the default or enter the correct value\.
   + Enter 1 if you want to verify/install Oracle REST Data Services schema or 2 to skip this step \[1\]:

     Choose `1`\. This step creates the Oracle REST Data Services proxy user named ORDS\_PUBLIC\_USER\.
   + Enter the database password for ORDS\_PUBLIC\_USER:

     Enter the password, and then confirm it\.
   + Requires to login with administrator privileges to verify Oracle REST Data Services schema\.

     Enter the administrator user name: *master\_user*

     Enter the database password for *master\_user*: *master\_user\_password*

     Confirm the password: *master\_user\_password*
   + Enter the default tablespace for ORDS\_METADATA \[SYSAUX\]\.

     Enter the temporary tablespace for ORDS\_METADATA \[TEMP\]\.

     Enter the default tablespace for ORDS\_PUBLIC\_USER \[USERS\]\.

     Enter the temporary tablespace for ORDS\_PUBLIC\_USER \[TEMP\]\.
   + Enter 1 if you want to use PL/SQL Gateway or 2 to skip this step\. If you're using Oracle Application Express or migrating from mod\_plsql, you must enter 1 \[1\]\.

     Choose the default\.
   + Enter the PL/SQL Gateway database user name \[APEX\_PUBLIC\_USER\]

     Choose the default\.
   + Enter the database password for APEX\_PUBLIC\_USER:

     Enter the password, and then confirm it\.
   + Enter 1 to specify passwords for Application Express RESTful Services database users \(APEX\_LISTENER, APEX\_REST\_PUBLIC\_USER\) or 2 to skip this step \[1\]:

     Choose `2` for APEX 4\.1\.1\.V1; choose `1` for all other APEX versions\.
   + \[Not needed for APEX 4\.1\.1\.v1\] Database password for APEX\_LISTENER

     Enter the password \(if required\), and then confirm it\.
   + \[Not needed for APEX 4\.1\.1\.v1\] Database password for APEX\_REST\_PUBLIC\_USER

     Enter the password \(if required\), and then confirm it\.
   + Enter a number to select a feature to enable:

     Enter `1` to enable all features: SQL Developer Web, REST Enabled SQL, and Database API\.
   + Enter 1 if you wish to start in standalone mode or 2 to exit \[1\]:

     Enter `1`\.
   + Enter the APEX static resources location:

     If you unzipped APEX installation files into `/home/apexuser`, enter `/home/apexuser/apex/images`\. Otherwise, enter `unzip_path/apex/images`, where *unzip\_path* is the directory where you unzipped the file\.
   + Enter 1 if using HTTP or 2 if using HTTPS \[1\]:

     If you enter `1`, specify the HTTP port\. If you enter `2`, specify the HTTPS port and the SSL host name\. The HTTPS option prompts you to specify how you will provide the certificate:
     + Enter `1` to use the self\-signed certificate\.
     + Enter `2` to provide your own certificate\. If you enter `2`, specify the path for the SSL certificate and the path for the SSL certificate private key\.

1. Set a password for the APEX `admin` user\. To do this, use SQL\*Plus to connect to your DB instance as the master user, and then run the following commands\.

   ```
   1. EXEC rdsadmin.rdsadmin_util.grant_apex_admin_role;
   2. grant APEX_ADMINISTRATOR_ROLE to master;
   3. @/home/apexuser/apex/apxchpwd.sql
   ```

   Replace `master` with your master user name\. When the `apxchpwd.sql` script prompts you, enter a new `admin` password\. 

1. Start the ORDS listener\. Run the following code\.

   ```
   java -jar ords.war
   ```

   The first time you start ORDS, you are prompted to provide the location of the APEX Static resources\. This images folder is located in the `/apex/images` directory in the installation directory for APEX\. 

1. Return to the APEX administration window in your browser and choose **Administration**\. Next, choose **Application Express Internal Administration**\. When you are prompted for credentials, enter the following information: 
   + **User name** – `admin` 
   + **Password** – the password you set using the `apxchpwd.sql` script 

   Choose **Login**, and then set a new password for the `admin` user\. 

Your listener is now ready for use\.

## Setting Up Oracle APEX Listener<a name="Appendix.Oracle.Options.APEX.Listener"></a>

**Note**  
Oracle APEX Listener is deprecated\. 

Amazon RDS for Oracle continues to support APEX version 4\.1\.1 and Oracle APEX Listener version 1\.1\.4\. We recommend that you use the latest supported versions of Oracle APEX and ORDS\.

Install Oracle APEX Listener on a separate host such as an Amazon EC2 instance, an on\-premises server at your company, or your desktop computer\. We assume that the name of your host is `myapexhost.example.com`, and that your host is running Linux\.

### Preparing to Install Oracle APEX Listener<a name="Appendix.Oracle.Options.APEX.Listener.preparing"></a>

Before you can install Oracle APEX Listener, you need to create a nonprivileged OS user, and then download and unzip the APEX installation file\.

**To prepare for Oracle APEX Listener installation**

1. Log in to `myapexhost.example.com` as `root`\. 

1. Create a nonprivileged OS user to own the listener installation\. The following command creates a new user named *apexuser*\. 

   ```
   useradd -d /home/apexuser apexuser
   ```

   The following command assigns a password to the new user\. 

   ```
   passwd apexuser;
   ```

1. Log in to `myapexhost.example.com` as `apexuser`, and download the APEX installation file from Oracle to your `/home/apexuser` directory: 
   + [http://www\.oracle\.com/technetwork/developer\-tools/apex/downloads/index\.html](http://www.oracle.com/technetwork/developer-tools/apex/downloads/index.html) 
   + [Oracle Application Express Prior Release Archives](http://www.oracle.com/technetwork/developer-tools/apex/downloads/all-archives-099381.html) 

1. Unzip the file in the `/home/apexuser` directory\.

   ```
   unzip apex_<version>.zip                
   ```

   After you unzip the file, there is an `apex` directory in the `/home/apexuser` directory\.

1. While you are still logged into `myapexhost.example.com` as `apexuser`, download the Oracle APEX Listener file from Oracle to your `/home/apexuser` directory\.

### Installing and Configuring Oracle APEX Listener<a name="Appendix.Oracle.Options.APEX.Listener.installing"></a>

Before you can use APEX, you need to download the apex\.war file, use Java to install Oracle APEX Listener, and then start the listener\.

**To install and configure Oracle APEX Listener**

1. Create a new directory based on Oracle APEX Listener and open the listener file\.

   Run the following code:

   ```
   mkdir /home/apexuser/apexlistener
   cd /home/apexuser/apexlistener 
   unzip ../apex_listener.version.zip
   ```

1. Run the following code\.

   ```
   java -Dapex.home=./apex -Dapex.images=/home/apexuser/apex/images -Dapex.erase -jar ./apex.war
   ```

1. Enter information for the program prompts following: 
   + The APEX Listener Administrator user name\. The default is *adminlistener*\. 
   + A password for the APEX Listener Administrator\. 
   + The APEX Listener Manager user name\. The default is *managerlistener*\. 
   + A password for the APEX Listener Administrator\. 

   The program prints a URL that you need to complete the configuration, as follows\.

   ```
   INFO: Please complete configuration at: http://localhost:8080/apex/listenerConfigure
   Database is not yet configured
   ```

1. Leave Oracle APEX Listener running so that you can use Oracle Application Express\. When you have finished this configuration procedure, you can run the listener in the background\. 

1. From your web browser, go to the URL provided by the APEX Listener program\. The Oracle Application Express Listener administration window appears\. Enter the following information: 
   + **Username** – `APEX_PUBLIC_USER`
   + **Password** – the password for *APEX\_PUBLIC\_USER*\. This password is the one that you specified earlier when you configured the APEX repository\. For more information, see [Unlocking the Public User Account](#Appendix.Oracle.Options.APEX.PublicUser)\. 
   + **Connection Type** – Basic 
   + **Hostname** – the endpoint of your Amazon RDS DB instance, such as `mydb.f9rbfa893tft.us-east-1.rds.amazonaws.com`\. 
   + **Port** – 1521
   + **SID** – the name of the database on your Amazon RDS DB instance, such as `mydb`\. 

1. Choose **Apply**\. The APEX administration window appears\. 

1. Set a password for the APEX `admin` user\. To do this, use SQL\*Plus to connect to your DB instance as the master user, and then run the following commands\.

   ```
   1. EXEC rdsadmin.rdsadmin_util.grant_apex_admin_role;
   2. grant APEX_ADMINISTRATOR_ROLE to master;
   3. @/home/apexuser/apex/apxchpwd.sql
   ```

   Replace `master` with your master user name\. When the `apxchpwd.sql` script prompts you, enter a new `admin` password\. 

1. Return to the APEX administration window in your browser and choose **Administration**\. Next, choose **Application Express Internal Administration**\. When you are prompted for credentials, enter the following information: 
   + **User name** – `admin` 
   + **Password** – the password you set using the `apxchpwd.sql` script 

   Choose **Login**, and then set a new password for the `admin` user\. 

Your listener is now ready for use\.

## Upgrading the APEX Version<a name="Appendix.Oracle.Options.APEX.Upgrade"></a>

**Important**  
Back up your DB instance before you upgrade APEX\. For more information, see [Creating a DB Snapshot](USER_CreateSnapshot.md) and [Testing an Upgrade](USER_UpgradeDBInstance.Oracle.md#USER_UpgradeDBInstance.Oracle.UpgradeTesting)\. 

To upgrade APEX with your DB instance, do the following: 
+ Create a new option group for the upgraded version of your DB instance\. 
+ Add the upgraded versions of APEX and APEX\-DEV to the new option group\. Be sure to include any other options that your DB instance uses\. For more information, see [Option Group Considerations](USER_UpgradeDBInstance.Oracle.md#USER_UpgradeDBInstance.Oracle.OGPG.OG)\. 
+ When you upgrade your DB instance, specify the new option group for your upgraded DB instance\. 

After you upgrade your version of APEX, the APEX schema for the previous version might still exist in your database\. If you don't need it anymore, you can drop the old APEX schema from your database after you upgrade\. 

If you upgrade the APEX version and RESTful services were not configured in the previous APEX version, we recommend that you configure RESTful services\. For more information, see [ Configuring RESTful Services for Oracle APEX](#Appendix.Oracle.Options.APEX.ConfigureRESTful)\.

In some cases when you plan to do a major version upgrade of your DB instance, you might find that you're using an APEX version that isn't compatible with your target database version\. In these cases, you can upgrade your version of APEX before you upgrade your DB instance\. Upgrading APEX first can reduce the amount of time that it takes to upgrade your DB instance\. 

**Note**  
After upgrading APEX, install and configure a listener for use with the upgraded version\. For instructions, see [Setting Up Oracle APEX Listener](#Appendix.Oracle.Options.APEX.Listener)\.

## Removing the APEX Option<a name="Appendix.Oracle.Options.APEX.Remove"></a>

You can remove the Amazon RDS APEX options from a DB instance\. To remove the APEX options from a DB instance, do one of the following: 
+ To remove the APEX options from multiple DB instances, remove the APEX options from the option group they belong to\. This change affects all DB instances that use the option group\. When you remove the APEX options from an option group that is attached to multiple DB instances, a brief outage occurs while all the DB instances are restarted\. 

  For more information, see [Removing an Option from an Option Group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.RemoveOption)\. 
+ To remove the APEX options from a single DB instance, modify the DB instance and specify a different option group that doesn't include the APEX options\. You can specify the default \(empty\) option group, or a different custom option group\. When you remove the APEX options, a brief outage occurs while your DB instance is automatically restarted\. 

  For more information, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md)\. 

When you remove the APEX options from a DB instance, the APEX schema is removed from your database\. 