# Using SSL with a Microsoft SQL Server DB Instance<a name="SQLServer.Concepts.General.SSL.Using"></a>

You can use Secure Sockets Layer \(SSL\) to encrypt connections between your client applications and your Amazon RDS DB instances running Microsoft SQL Server\. SSL support is available in all AWS regions for all supported SQL Server editions\. 

When you create a SQL Server DB instance, Amazon RDS creates an SSL certificate for it\. The SSL certificate includes the DB instance endpoint as the Common Name \(CN\) for the SSL certificate to guard against spoofing attacks\. 

There are 2 ways to use SSL to connect to your SQL Server DB instance: 
+ Force SSL for all connections — this happens transparently to the client, and the client doesn't have to do any work to use SSL\. 
+ Encrypt specific connections — this sets up an SSL connection from a specific client computer, and you must do work on the client to encrypt connections\. 

For information about Transport Layer Security \(TLS\) support for SQL Server, see [ TLS 1\.2 support for Microsoft SQL Server](https://support.microsoft.com/en-ca/help/3135244/tls-1-2-support-for-microsoft-sql-server)\.

## Forcing Connections to Your DB Instance to Use SSL<a name="SQLServer.Concepts.General.SSL.Forcing"></a>

You can force all connections to your DB instance to use SSL\. If you force connections to use SSL, it happens transparently to the client, and the client doesn't have to do any work to use SSL\. 

If you want to force SSL, use the `rds.force_ssl` parameter\. By default, the `rds.force_ssl` parameter is set to `0 (off)`\. Set the `rds.force_ssl` parameter to `1 (on)` to force connections to use SSL\. The `rds.force_ssl` parameter is static, so after you change the value, you must reboot your DB instance for the change to take effect\. 

**To force all connections to your DB instance to use SSL**

1. Determine the parameter group that is attached to your DB instance: 

   1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

   1. In the top right corner of the Amazon RDS console, choose the AWS Region of your DB instance\. 

   1. In the navigation pane, choose **Databases**, and then choose the name of your DB instance to show its details\. 

   1. Choose the **Configuration** tab\. Find the **Parameter group** in the section\.  

1. If necessary, create a new parameter group\. If your DB instance uses the default parameter group, you must create a new parameter group\. If your DB instance uses a nondefault parameter group, you can choose to edit the existing parameter group or to create a new parameter group\. If you edit an existing parameter group, the change affects all DB instances that use that parameter group\. 

   To create a new parameter group, follow the instructions in [Creating a DB Parameter Group](USER_WorkingWithParamGroups.md#USER_WorkingWithParamGroups.Creating)\. 

1. Edit your new or existing parameter group to set the `rds.force_ssl` parameter to `true`\. To edit the parameter group, follow the instructions in [Modifying Parameters in a DB Parameter Group](USER_WorkingWithParamGroups.md#USER_WorkingWithParamGroups.Modifying)\. 

1. If you created a new parameter group, modify your DB instance to attach the new parameter group\. Modify the **DB Parameter Group** setting of the DB instance\. For more information, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md)\. 

1. Reboot your DB instance\. For more information, see [Rebooting a DB Instance](USER_RebootInstance.md)\. 

## Encrypting Specific Connections<a name="SQLServer.Concepts.General.SSL.Client"></a>

You can force all connections to your DB instance to use SSL, or you can encrypting connections from specific client computers only\. To use SSL from a specific client, you must obtain certificates for the client computer, import certificates on the client computer, and then encrypt the connections from the client computer\. 

**Note**  
All SQL Server instances created after August 5, 2014, use the DB instance endpoint in the Common Name \(CN\) field of the SSL certificate\. Prior to August 5, 2014, SSL certificate verification was not available for VPC\-based SQL Server instances\. If you have a VPC\-based SQL Server DB instance that was created before August 5, 2014, and you want to use SSL certificate verification and ensure that the instance endpoint is included as the CN for the SSL certificate for that DB instance, then rename the instance\. When you rename a DB instance, a new certificate is deployed and the instance is rebooted to enable the new certificate\.

### Obtaining Certificates for Client Computers<a name="SQLServer.Concepts.General.SSL.Certificates"></a>

To encrypt connections from a client computer to an Amazon RDS DB instance running Microsoft SQL Server, you need a certificate on your client computer\. 

To obtain that certificate, download the certificate to your client computer\. You can download a root certificate that works for all regions\. You can also download a certificate bundle that contains both the old and new root certificate\. In addition, you can download region\-specific intermediate certificates\. For more information about downloading certificates, see [Using SSL/TLS to Encrypt a Connection to a DB Instance](UsingWithRDS.SSL.md)\.

After you have downloaded the appropriate certificate, import the certificate into your Microsoft Windows operating system by following the procedure in the section following\. 

### Importing Certificates on Client Computers<a name="SQLServer.Concepts.General.SSL.Importing"></a>

You can use the following procedure to import your certificate into the Microsoft Windows operating system on your client computer\. 

**To import the certificate into your Windows operating system:**

1. On the **Start** menu, type **Run** in the search box and press **Enter**\. 

1. In the **Open** box, type **MMC** and then choose **OK**\. 

1. In the MMC console, on the **File** menu, choose **Add/Remove Snap\-in**\. 

1. In the **Add or Remove Snap\-ins** dialog box, for **Available snap\-ins**, select **Certificates**, and then choose **Add**\. 

1. In the **Certificates snap\-in** dialog box, choose **Computer account**, and then choose **Next**\. 

1. In the **Select computer** dialog box, choose **Finish**\. 

1. In the **Add or Remove Snap\-ins** dialog box, choose **OK**\. 

1. In the MMC console, expand **Certificates**, open the context \(right\-click\) menu for **Trusted Root Certification Authorities**, choose **All Tasks**, and then choose **Import**\. 

1. On the first page of the Certificate Import Wizard, choose **Next**\. 

1. On the second page of the Certificate Import Wizard, choose **Browse**\. In the browse window, change the file type to **All files \(\*\.\*\)** because \.pem is not a standard certificate extension\. Locate the \.pem file that you downloaded previously\. 

1. Choose **Open** to select the certificate file, and then choose **Next**\. 

1. On the third page of the Certificate Import Wizard, choose **Next**\. 

1. On the fourth page of the Certificate Import Wizard, choose **Finish**\. A dialog box appears indicating that the import was successful\. 

1. In the MMC console, expand **Certificates**, expand **Trusted Root Certification Authorities**, and then choose **Certificates**\. Locate the certificate to confirm it exists, as shown here\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/rds_sql_ssl_cert.png)

1. Restart your computer\.

### Encrypting Connections to an Amazon RDS DB Instance Running Microsoft SQL Server<a name="SQLServer.Concepts.General.SSL.Encrypting"></a>

After you have imported a certificate into your client computer, you can encrypt connections from the client computer to an Amazon RDS DB instance running Microsoft SQL Server\. 

For SQL Server Management Studio, use the following procedure\. For more information about SQL Server Management Studio, see [Use SQL Server Management Studio](http://msdn.microsoft.com/en-us/library/ms174173.aspx)\. 

**To encrypt connections from SQL Server Management Studio**

1. Launch SQL Server Management Studio\. 

1. For **Connect to server**, type the server information, login user name, and password\. 

1. Choose **Options**\. 

1. Select **Encrypt connection**\. 

1. Choose **Connect**\.

1. Confirm that your connection is encrypted by running the following query\. Verify that the query returns `true` for `encrypt_option`\. 

   ```
   select ENCRYPT_OPTION from SYS.DM_EXEC_CONNECTIONS where SESSION_ID = @@SPID
   ```

For any other SQL client, use the following procedure\. 

**To encrypt connections from other SQL clients**

1. Append `encrypt=true` to your connection string\. This string might be available as an option, or as a property on the connection page in GUI tools\. 
**Note**  
To enable SSL encryption for clients that connect using JDBC, you might need to add the Amazon RDS SQL certificate to the Java CA certificate \(cacerts\) store\. You can do this by using the [ keytool](http://docs.oracle.com/javase/7/docs/technotes/tools/solaris/keytool.html) utility\. 

1. Confirm that your connection is encrypted by running the following query\. Verify that the query returns `true` for `encrypt_option`\. 

   ```
   select ENCRYPT_OPTION from SYS.DM_EXEC_CONNECTIONS where SESSION_ID = @@SPID
   ```