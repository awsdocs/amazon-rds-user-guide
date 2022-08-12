# Configuring UTL\_HTTP access using certificates and an Oracle wallet<a name="Oracle.Concepts.ONA"></a>

Amazon RDS supports outbound network access on your Oracle DB instances\. To connect your DB instance to the network, you can use the following PL/SQL packages:

`UTL_HTTP`  
This package makes HTTP calls from SQL and PL/SQL\. You can use it to access data on the Internet over HTTP\. For more information, see [UTL\_HTTP](https://docs.oracle.com/en/database/oracle/oracle-database/19/arpls/UTL_HTTP.html#GUID-A85D2D1F-90FC-45F1-967F-34368A23C9BB) in the Oracle documentation\.

`UTL_TCP`  
This package provides TCP/IP client\-side access functionality in PL/SQL\. This package is useful to PL/SQL applications that use Internet protocols and email\. For more information, see [UTL\_TCP](https://docs.oracle.com/en/database/oracle/oracle-database/19/arpls/UTL_TCP.html#GUID-348AFFE8-78B2-4217-AE73-384F46A1D292) in the Oracle documentation\.

`UTL_SMTP`  
This package provides interfaces to the SMTP commands that enable a client to dispatch emails to an SMTP server\. For more information, see [UTL\_SMTP](https://docs.oracle.com/en/database/oracle/oracle-database/19/arpls/UTL_SMTP.html#GUID-F0065C52-D618-4F8A-A361-7B742D44C520) in the Oracle documentation\.

Before configuring your instance for network access, review the following requirements and considerations:
+ To use SMTP with the UTL\_MAIL option, see [Oracle UTL\_MAIL](Oracle.Options.UTLMAIL.md)\.
+ The Domain Name Server \(DNS\) name of the remote host can be any of the following: 
  + Publicly resolvable\.
  + The endpoint of an Amazon RDS DB instance\.
  + Resolvable through a custom DNS server\. For more information, see [Setting up a custom DNS server](Appendix.Oracle.CommonDBATasks.System.md#Appendix.Oracle.CommonDBATasks.CustomDNS)\. 
  + The private DNS name of an Amazon EC2 instance in the same VPC or a peered VPC\. In this case, make sure that the name is resolvable through a custom DNS server\. Alternatively, to use the DNS provided by Amazon, you can enable the `enableDnsSupport` attribute in the VPC settings and enable DNS resolution support for the VPC peering connection\. For more information, see [DNS support in your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-dns.html#vpc-dns-support) and [Modifying your VPC peering connection](https://docs.aws.amazon.com/vpc/latest/peering/working-with-vpc-peering.html#modify-peering-connections)\. 
  + To connect securely to remote SSL/TLS resources, we recommend that you create and upload customized Oracle wallets\. By using the Amazon S3 integration with Amazon RDS for Oracle feature, you can download a wallet from Amazon S3 into Oracle DB instances\. For information about Amazon S3 integration for Oracle, see [Amazon S3 integration](oracle-s3-integration.md)\.
+ You can establish database links between Oracle DB instances over an SSL/TLS endpoint if the Oracle SSL option is configured for each instance\. No further configuration is required\. For more information, see [Oracle Secure Sockets Layer](Appendix.Oracle.Options.SSL.md)\.

By completing the following tasks, you can configure `UTL_HTTP.REQUEST` to work with websites that require client authentication certificates during the SSL handshake\. You can also configure password authentication for `UTL_HTTP` access to websites by modifying the Oracle wallet generation commands and the `DBMS_NETWORK_ACL_ADMIN.APPEND_WALLET_ACE` procedure\. For more information, see [ DBMS\_NETWORK\_ACL\_ADMIN](https://docs.oracle.com/en/database/oracle/oracle-database/21/arpls/DBMS_NETWORK_ACL_ADMIN.html) in the Oracle Database documentation\.

**Note**  
You can adapt the following tasks for `UTL_SMTP`, which enables you to send emails over SSL/TLS \(including [Amazon Simple Email Service](http://aws.amazon.com/ses/)\)\.

**Topics**
+ [Step 1: Get the root certificate for a website](#website-root-certificate)
+ [Step 2: Create an Oracle wallet](#create-oracle-wallet)
+ [Step 3: Download your Oracle wallet to your RDS for Oracle instance](#upload-wallet-to-instance)
+ [Step 4: Grant user permissions for the Oracle wallet](#config-oracle-wallet-user)
+ [Step 5: Configure access to a website from your DB instance](#config-website-access)
+ [Step 6: Test connections from your DB instance to a website](#test_utl_http)

## Step 1: Get the root certificate for a website<a name="website-root-certificate"></a>

For the RDS for Oracle instance to make secure connections to a website, add the root CA certificate\. Amazon RDS uses the root certificate to sign the website certificate to the Oracle wallet\.

You can get the root certificate in various ways\. For example, you can do the following:

1. Use a web server to visit the website secured by the certificate\.

1. Download the root certificate that was used for signing\.

For AWS services, root certificates typically reside in the [Amazon trust services repository](https://www.amazontrust.com/repository/)\.

## Step 2: Create an Oracle wallet<a name="create-oracle-wallet"></a>

Create an Oracle wallet that contains both the web server certificates and the client authentication certificates\. The RDS Oracle instance uses the web server certificate to establish a secure connection to the website\. The website needs the client certificate to authenticate the Oracle database user\.

You might want to configure secure connections without using client certificates for authentication\. In this case, you can skip the Java keystore steps in the following procedure\.

**To create an Oracle wallet**

1. Place the root and client certificates in a single directory, and then change into this directory\.

1. Convert the \.p12 client certificate to the Java keystore\.
**Note**  
If you're not using client certificates for authentication, you can skip this step\.

   The following example converts the client certificate named *client\_certificate\.p12* to the Java keystore named *client\_keystore\.jks*\. The keystore is then included in the Oracle wallet\. The keystore password is *P12PASSWORD*\.

   ```
   orapki wallet pkcs12_to_jks -wallet ./client_certificate.p12 -jksKeyStoreLoc ./client_keystore.jks -jksKeyStorepwd P12PASSWORD
   ```

1. Create a directory for your Oracle wallet that is different from the certificate directory\.

   The following example creates the directory `/tmp/wallet`\.

   ```
   mkdir -p /tmp/wallet
   ```

1. Create an Oracle wallet in your wallet directory\.

   The following example sets the Oracle wallet password to *P12PASSWORD*, which is the same password used by the Java keystore in a previous step\. Using the same password is convenient, but not necessary\. The `-auto_login` parameter turns on the automatic login feature, so that you don’t need to specify a password every time you want to access it\.

   ```
   orapki wallet create -wallet /tmp/wallet -pwd P12PASSWORD -auto_login
   ```

1. Add the Java keystore to your Oracle wallet\.
**Note**  
If you're not using client certificates for authentication, you can skip this step\.

   The following example adds the keystore *client\_keystore\.jks* to the Oracle wallet named */tmp/wallet*\. In this example, you specify the same password for the Java keystore and the Oracle wallet\.

   ```
   orapki wallet jks_to_pkcs12 -wallet /tmp/wallet -pwd P12PASSWORD -keystore ./client_keystore.jks -jkspwd P12PASSWORD
   ```

1. Add the root certificate for your target website to the Oracle wallet\.

   The following example adds a certificate named *Root\_CA\.cer*\.

   ```
   orapki wallet add -wallet /tmp/wallet -trusted_cert -cert ./Root_CA.cer -pwd P12PASSWORD
   ```

1. Add any intermediate certificates\.

   The following example adds a certificate named *Intermediate\.cer*\. Repeat this step as many times as need to load all intermediate certificates\.

   ```
   orapki wallet add -wallet /tmp/wallet -trusted_cert -cert ./Intermediate.cer -pwd P12PASSWORD
   ```

1. Confirm that your newly created Oracle wallet has the required certificates\.

   ```
   orapki wallet display -wallet /tmp/wallet -pwd P12PASSWORD
   ```

## Step 3: Download your Oracle wallet to your RDS for Oracle instance<a name="upload-wallet-to-instance"></a>

In this step, you upload your Oracle wallet to Amazon S3, and then download the wallet from Amazon S3 to your RDS for Oracle instance\.

**To download your Oracle wallet to your RDS for Oracle DB instance**

1. Complete the prerequisites for Amazon S3 integration with Oracle, and add the `S3_INTEGRATION` option to your Oracle DB instance\. Ensure that the IAM role for the option has access to the Amazon S3 bucket you are using\.

   For more information, see [Amazon S3 integration](oracle-s3-integration.md)\.

1. Log in to your DB instance as the master user, and then create an Oracle directory to hold the Oracle wallet\.

   The following example creates an Oracle directory named *WALLET\_DIR*\.

   ```
   EXEC rdsadmin.rdsadmin_util.create_directory('WALLET_DIR');
   ```

   For more information, see [Creating and dropping directories in the main data storage space](Appendix.Oracle.CommonDBATasks.Misc.md#Appendix.Oracle.CommonDBATasks.NewDirectories)\.

1. Upload the Oracle wallet to your Amazon S3 bucket\.

   You can use any supported upload technique\.

1. If you're re\-uploading an Oracle wallet, delete the existing wallet\. Otherwise, skip to the next step\.

   The following example removes the existing wallet, which is named *cwallet\.sso*\.

   ```
   EXEC UTL_FILE.FREMOVE ('WALLET_DIR','cwallet.sso');
   ```

1. Download the Oracle wallet from your Amazon S3 bucket to the Oracle DB instance\.

   The following example downloads the wallet named *cwallet\.sso* from the Amazon S3 bucket named *my\_s3\_bucket* to the DB instance directory named *WALLET\_DIR*\.

   ```
   SELECT rdsadmin.rdsadmin_s3_tasks.download_from_s3(
         p_bucket_name    =>  'my_s3_bucket', 
         p_s3_prefix      =>  'cwallet.sso', 
         p_directory_name =>  'WALLET_DIR') 
      AS TASK_ID FROM DUAL;
   ```

1. \(Optional\) Download a password\-protected Oracle wallet\.

   Download this wallet only if you want to require a password for every use of the wallet\. The following example downloads password\-protected wallet *ewallet\.p12*\.

   ```
   SELECT rdsadmin.rdsadmin_s3_tasks.download_from_s3(
         p_bucket_name    =>  'my_s3_bucket', 
         p_s3_prefix      =>  'ewallet.p12', 
         p_directory_name =>  'WALLET_DIR') 
      AS TASK_ID FROM DUAL;
   ```

1. Check the status of your DB task\.

   Substitute the task ID returned from the preceding steps for *dbtask\-1234567890123\-4567\.log* in the following example\.

   ```
   SELECT TEXT FROM TABLE(rdsadmin.rds_file_util.read_text_file('BDUMP','dbtask-1234567890123-4567.log'));
   ```

1. Check the contents of the directory that you're using to store the Oracle wallet\.

   ```
   SELECT * FROM TABLE(rdsadmin.rds_file_util.listdir(p_directory => 'WALLET_DIR'));
   ```

   For more information, see [Listing files in a DB instance directory](Appendix.Oracle.CommonDBATasks.Misc.md#Appendix.Oracle.CommonDBATasks.ListDirectories)\.

## Step 4: Grant user permissions for the Oracle wallet<a name="config-oracle-wallet-user"></a>

You can either create a new database user or configure an existing user\. In either case, you must configure the user to access the Oracle wallet for secure connections and client authentication using certificates\.

**To grant user permissions for the Oracle wallet**

1. Log in your RDS for Oracle DB instance as the master user\.

1. If you don't want to configure an existing database user, create a new user\. Otherwise, skip to the next step\.

   The following example creates a database user named *my\-user*\.

   ```
   CREATE USER my-user IDENTIFIED BY my-user-pwd;
   GRANT CONNECT TO my-user;
   ```

1. Grant permission to your database user on the directory containing your Oracle wallet\.

   The following example grants read access to user *my\-user* on directory *WALLET\_DIR*\.

   ```
   GRANT READ ON DIRECTORY WALLET_DIR TO my-user;
   ```

1. Grant permission to your database user to use the `UTL_HTTP` package\.

   The following PL/SQL program grants `UTL_HTTP` access to user *my\-user*\.

   ```
   BEGIN 
     rdsadmin.rdsadmin_util.grant_sys_object('UTL_HTTP', UPPER('my-user')); 
     END;
   /
   ```

1. Grant permission to your database user to use the `UTL_FILE` package\.

   The following PL/SQL program grants `UTL_FILE` access to user *my\-user*\.

   ```
   BEGIN 
     rdsadmin.rdsadmin_util.grant_sys_object('UTL_FILE', UPPER('my-user')); 
     END;
   /
   ```

## Step 5: Configure access to a website from your DB instance<a name="config-website-access"></a>

In this step, you configure your Oracle database user so that it can connect to your target website using `UTL_HTTP`, your uploaded Oracle Wallet, and the client certificate\. For more information, see [Configuring Access Control to an Oracle Wallet ](https://docs.oracle.com/en/database/oracle/oracle-database/19/dbseg/managing-fine-grained-access-in-pl-sql-packages-and-types.html#GUID-0BCB5925-A40F-4507-95F9-5DA4A1919EBD) in the Oracle Database documentation\.

**To configure access to a website from your RDS for Oracle DB instance**

1. Log in your RDS for Oracle DB instance as the master user\.

1. Create a Host Access Control Entry \(ACE\) for your user and the target website on a secure port\.

   The following example configures *my\-user* to access *secret\.encrypted\-website\.com* on secure port 443\.

   ```
   BEGIN
     DBMS_NETWORK_ACL_ADMIN.APPEND_HOST_ACE(
       host       => 'secret.encrypted-website.com', 
       lower_port => 443,
       upper_port => 443,
       ace        => xs$ace_type(privilege_list => xs$name_list('http'),
                                 principal_name => 'my-user',
                                 principal_type => xs_acl.ptype_db)); 
   END;
   /
   ```

   For more information, see [Configuring Access Control for External Network Services ](https://docs.oracle.com/en/database/oracle/oracle-database/19/dbseg/managing-fine-grained-access-in-pl-sql-packages-and-types.html#GUID-3D5B66BC-0277-4887-9CD1-97DB44EB5213) in the Oracle Database documentation\.

1. \(Optional\) Create an ACE for your user and target website on the standard port\. 

   You might need to use the standard port if some web pages are served from the standard web server port \(80\) instead of the secure port \(443\)\.

   ```
   BEGIN
     DBMS_NETWORK_ACL_ADMIN.APPEND_HOST_ACE(
       host       => 'secret.encrypted-website.com', 
       lower_port => 80,
       upper_port => 80,
       ace        => xs$ace_type(privilege_list => xs$name_list('http'),
                                 principal_name => 'my-user',
                                 principal_type => xs_acl.ptype_db)); 
   END;
   /
   ```

1. Confirm that the access control entries exist\.

   ```
   SET LINESIZE 150
   COLUMN HOST FORMAT A40
   COLUMN ACL FORMAT A50
   
   SELECT HOST, LOWER_PORT, UPPER_PORT, ACL
     FROM DBA_NETWORK_ACLS
   ORDER BY HOST;
   ```

1. Grant permission to your database user to use the `UTL_HTTP` package\.

   The following PL/SQL program grants `UTL_HTTP` access to user *my\-user*\.

   ```
   BEGIN 
     rdsadmin.rdsadmin_util.grant_sys_object('UTL_HTTP', UPPER('my-user')); 
     END;
   /
   ```

1. Confirm that related access control lists exist\.

   ```
   SET LINESIZE 150
   COLUMN ACL FORMAT A50
   COLUMN PRINCIPAL FORMAT A20
   COLUMN PRIVILEGE FORMAT A10
   
   SELECT ACL, PRINCIPAL, PRIVILEGE, IS_GRANT,
          TO_CHAR(START_DATE, 'DD-MON-YYYY') AS START_DATE,
          TO_CHAR(END_DATE, 'DD-MON-YYYY') AS END_DATE
     FROM DBA_NETWORK_ACL_PRIVILEGES
   ORDER BY ACL, PRINCIPAL, PRIVILEGE;
   ```

1. Grant permission to your database user to use certificates for client authentication and your Oracle wallet for connections\.
**Note**  
If you're not using client certificates for authentication, you can skip this step\.

   ```
   DECLARE
     l_wallet_path all_directories.directory_path%type;
   BEGIN
     SELECT DIRECTORY_PATH 
       INTO l_wallet_path 
       FROM ALL_DIRECTORIES
      WHERE UPPER(DIRECTORY_NAME)='WALLET_DIR';
     DBMS_NETWORK_ACL_ADMIN.APPEND_WALLET_ACE(
       wallet_path => 'file:/' || l_wallet_path,
       ace         =>  xs$ace_type(privilege_list => xs$name_list('use_client_certificates'),
                                   principal_name => 'my-user',
                                   principal_type => xs_acl.ptype_db));
   END;
   /
   ```

## Step 6: Test connections from your DB instance to a website<a name="test_utl_http"></a>

In this step, you configure your database user so that it can connect to the website using `UTL_HTTP`, your uploaded Oracle Wallet, and the client certificate\.

**To configure access to a website from your RDS for Oracle DB instance**

1. Log in your RDS for Oracle DB instance as a database user with `UTL_HTTP` permissions\.

1. Confirm that a connection to your target website can resolve the host address\.

   The following example gets the host address from *secret\.encrypted\-website\.com*\.

   ```
   SELECT UTL_INADDR.GET_HOST_ADDRESS(host => 'secret.encrypted-website.com')
     FROM DUAL;
   ```

1. Test a failed connection\.

   The following query fails because `UTL_HTTP` requires the location of the Oracle wallet with the certificates\.

   ```
   SELECT UTL_HTTP.REQUEST('secret.encrypted-website.com') FROM DUAL;
   ```

1. Test website access by using `UTL_HTTP.SET_WALLET` and selecting from `DUAL`\.

   ```
   DECLARE
     l_wallet_path all_directories.directory_path%type;
   BEGIN
     SELECT DIRECTORY_PATH
       INTO l_wallet_path 
       FROM ALL_DIRECTORIES
      WHERE UPPER(DIRECTORY_NAME)='WALLET_DIR';
     UTL_HTTP.SET_WALLET('file:/' || l_wallet_path);
   END;
   /
   
   SELECT UTL_HTTP.REQUEST('secret.encrypted-website.com') FROM DUAL;
   ```

1. \(Optional\) Test website access by storing your query in a variable and using `EXECUTE IMMEDIATE`\.

   ```
   DECLARE
     l_wallet_path all_directories.directory_path%type;
     v_webpage_sql VARCHAR2(1000);
     v_results     VARCHAR2(32767);
   BEGIN
     SELECT DIRECTORY_PATH
       INTO l_wallet_path 
       FROM ALL_DIRECTORIES
      WHERE UPPER(DIRECTORY_NAME)='WALLET_DIR';
     v_webpage_sql := 'SELECT UTL_HTTP.REQUEST(''secret.encrypted-website.com'', '''', ''file:/' ||l_wallet_path||''') FROM DUAL';
     DBMS_OUTPUT.PUT_LINE(v_webpage_sql);
     EXECUTE IMMEDIATE v_webpage_sql INTO v_results;
     DBMS_OUTPUT.PUT_LINE(v_results);
   END;
   /
   ```

1. \(Optional\) Find the file system location of your Oracle wallet directory\.

   ```
   SELECT * FROM TABLE(rdsadmin.rds_file_util.listdir(p_directory => 'WALLET_DIR'));
   ```

   Use the output from the previous command to make an HTTP request\. For example, if the directory is *rdsdbdata/userdirs/01*, run the following query\.

   ```
   SELECT UTL_HTTP.REQUEST('https://secret.encrypted-website.com/', '', 'file://rdsdbdata/userdirs/01') 
   FROM   DUAL;
   ```