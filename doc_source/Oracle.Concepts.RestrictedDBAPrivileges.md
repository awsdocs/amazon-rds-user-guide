# Securing your Oracle DB instance<a name="Oracle.Concepts.RestrictedDBAPrivileges"></a>

The Oracle database engine uses role\-based security\. In the database, a role is a collection of privileges that you can grant to or revoke from a user\. A predefined role, named `DBA`, normally allows all administrative privileges on an Oracle database\. The following privileges are not available for the DBA role on an Amazon RDS DB instance using the Oracle engine: 
+ `ALTER DATABASE`
+ `ALTER SYSTEM`
+ `CREATE ANY DIRECTORY`
+ `DROP ANY DIRECTORY`
+ `GRANT ANY PRIVILEGE`
+ `GRANT ANY ROLE`

When you create a DB instance, your master user account gets DBA privileges \(with some limitations\)\. Use the master user account for administrative tasks such as creating additional user accounts in the database\. You can't use the `SYS` user, `SYSTEM` user, and other Oracle\-supplied administrative accounts\. 

Amazon RDS Oracle supports SSL/TLS encrypted connections and also the Oracle Native Network Encryption \(NNE\) option to encrypt connections between your application and your Oracle DB instance\. For more information about the Oracle Native Network Encryption option, see [Oracle native network encryption](Appendix.Oracle.Options.NetworkEncryption.md)\. 

## Encrypting client connections with SSL<a name="Oracle.Concepts.SSL"></a>

Secure Sockets Layer \(SSL\) is an industry\-standard protocol for securing network connections between client and server\. After SSL version 3\.0, the name was changed to Transport Layer Security \(TLS\), but it is still often referred to as SSL and we refer to the protocol as SSL\. Amazon RDS supports SSL encryption for Oracle DB instances\. Using SSL, you can encrypt a connection between your application client and your Oracle DB instance\. SSL support is available in all AWS regions for Oracle\. 

To enable SSL encryption for an Oracle DB instance, add the Oracle SSL option to the option group associated with the DB instance\. Amazon RDS uses a second port, as required by Oracle, for SSL connections\. Doing this allows both clear text and SSL\-encrypted communication to occur at the same time between a DB instance and an Oracle client\. For example, you can use the port with clear text communication to communicate with other resources inside a VPC while using the port with SSL\-encrypted communication to communicate with resources outside the VPC\. 

For more information, see [Oracle Secure Sockets Layer](Appendix.Oracle.Options.SSL.md)\. 

**Note**  
You can't use both SSL and Oracle native network encryption \(NNE\) on the same DB instance\. Before you can use SSL encryption, you must disable any other connection encryption\. 

## Updating applications to connect to Oracle DB instances using new SSL/TLS certificates<a name="ssl-certificate-rotation-oracle"></a>

As of September 19, 2019, Amazon RDS has published new Certificate Authority \(CA\) certificates for connecting to your RDS DB instances using Secure Socket Layer or Transport Layer Security \(SSL/TLS\)\. Following, you can find information about updating your applications to use the new certificates\.

This topic can help you to determine whether any client applications use SSL/TLS to connect to your DB instances\. 

**Important**  
When you change the certificate for an Amazon RDS for Oracle DB instance, only the database listener is restarted\. The DB instance isn't restarted\. Existing database connections are unaffected, but new connections will encounter errors for a brief period while the listener is restarted\.

**Note**  
For client applications that use SSL/TLS to connect to your DB instances, you must update your client application trust stores to include the new CA certificates\. 

After you update your CA certificates in the client application trust stores, you can rotate the certificates on your DB instances\. We strongly recommend testing these procedures in a development or staging environment before implementing them in your production environments\.

For more information about certificate rotation, see [Rotating your SSL/TLS certificate](UsingWithRDS.SSL-certificate-rotation.md)\. For more information about downloading certificates, see [Using SSL/TLS to encrypt a connection to a DB instance](UsingWithRDS.SSL.md)\. For information about using SSL/TLS with Oracle DB instances, see [Oracle Secure Sockets Layer](Appendix.Oracle.Options.SSL.md)\.

**Topics**
+ [Determining whether applications use SSL to your DB instance](#ssl-certificate-rotation-oracle.determining)
+ [Updating your application trust store](#ssl-certificate-rotation-oracle.updating-trust-store)
+ [Example Java code for establishing SSL connections](#ssl-certificate-rotation-oracle.java-example)

### Determining whether applications use SSL to your DB instance<a name="ssl-certificate-rotation-oracle.determining"></a>

If your Oracle DB instance uses an option group with the `SSL` option added, you might be using SSL\. Check this by following the instructions in [Listing the options and option settings for an option group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.ListOption)\. For information about the `SSL` option, see [Oracle Secure Sockets Layer](Appendix.Oracle.Options.SSL.md)\.

Check the listener log to determine whether there are SSL connections\. The following is sample output in a listener log\.

```
date time * (CONNECT_DATA=(CID=(PROGRAM=program)
(HOST=host)(USER=user))(SID=sid)) * 
(ADDRESS=(PROTOCOL=tcps)(HOST=host)(PORT=port)) * establish * ORCL * 0
```

When `PROTOCOL` has the value `tcps` for an entry, it shows an SSL connection\. However, when `HOST` is `127.0.0.1`, you can ignore the entry\. Connections from `127.0.0.1` are a local management agent on the DB instance\. These connections aren't external SSL connections\. Therefore, you have applications connecting using SSL if you see listener log entries where `PROTOCOL` is `tcps` and `HOST` is *not* `127.0.0.1`\.

To check the listener log, you can publish the log to Amazon CloudWatch Logs\. For more information, see [Publishing Oracle logs to Amazon CloudWatch Logs](USER_LogAccess.Concepts.Oracle.md#USER_LogAccess.Oracle.PublishtoCloudWatchLogs)\.

### Updating your application trust store<a name="ssl-certificate-rotation-oracle.updating-trust-store"></a>

You can update the trust store for applications that use SQL\*Plus or JDBC for SSL/TLS connections\.

#### Updating your application trust store for SQL\*Plus<a name="ssl-certificate-rotation-oracle.updating-trust-store.sqlplus"></a>

You can update the trust store for applications that use SQL\*Plus for SSL/TLS connections\.

**Note**  
When you update the trust store, you can retain older certificates in addition to adding the new certificates\.

**To update the trust store for SQL\*Plus applications**

1. Download the 2019 root certificate that works for all AWS Regions and put the file in the `ssl_wallet` directory\.

   For information about downloading the root certificate, see [Using SSL/TLS to encrypt a connection to a DB instance](UsingWithRDS.SSL.md)\.

1. Run the following command to update the Oracle wallet\.

   ```
   prompt>orapki wallet add -wallet $ORACLE_HOME/ssl_wallet -trusted_cert -cert
         $ORACLE_HOME/ssl_wallet/rds-ca-2019-root.pem -auto_login_only
   ```

   Replace the file name with the one that you downloaded\.

1. Run the following command to confirm that the wallet was updated successfully\.

   ```
   prompt>orapki wallet display -wallet $ORACLE_HOME/ssl_wallet                     
   ```

   Your output should contain the following\.

   ```
   Trusted Certificates: 
   Subject: CN=Amazon RDS Root 2019 CA,OU=Amazon RDS,O=Amazon Web Services\, Inc.,L=Seattle,ST=Washington,C=US
   ```

#### Updating your application trust store for JDBC<a name="ssl-certificate-rotation-oracle.updating-trust-store.jdbc"></a>

You can update the trust store for applications that use JDBC for SSL/TLS connections\.

**To update the trust store for JDBC applications**

1. Download the 2019 root certificate that works for all AWS Regions and put the file in the ssl\_wallet directory\.

   For information about downloading the root certificate, see [Using SSL/TLS to encrypt a connection to a DB instance](UsingWithRDS.SSL.md)\.

1. Convert the certificate to \.der format using the following command\.

   ```
   openssl x509 -outform der -in rds-ca-2019-root.pem -out rds-ca-2019-root.der                
   ```

   Replace the file name with the one that you downloaded\.

1. Import the certificate into the key store using the following command\. 

   ```
   keytool -import -alias rds-root -keystore clientkeystore -file rds-ca-2019-root.der                                    
   ```

1. Confirm that the key store was updated successfully\.

   ```
   keytool -list -v -keystore clientkeystore.jks
   ```

   Enter the key store password when you are prompted for it\.

   Your output should contain the following\.

   ```
   rds-root,date, trustedCertEntry, 
   Certificate fingerprint (SHA1): D4:0D:DB:29:E3:75:0D:FF:A6:71:C3:14:0B:BF:5F:47:8D:1C:80:96
   # This fingerprint should match the output from the below command
   openssl x509 -fingerprint -in rds-ca-2019-root.pem -noout
   ```

### Example Java code for establishing SSL connections<a name="ssl-certificate-rotation-oracle.java-example"></a>

The following code example shows how to set up the SSL connection using JDBC\.

```
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;
import java.util.Properties;
 
public class OracleSslConnectionTest {
    private static final String DB_SERVER_NAME = "<dns-name-provided-by-amazon-rds>";
    private static final Integer SSL_PORT = "<ssl-option-port-configured-in-option-group>";
    private static final String DB_SID = "<oracle-sid>";
    private static final String DB_USER = "<user name>";
    private static final String DB_PASSWORD = "<password>";
    // This key store has only the prod root ca.
    private static final String KEY_STORE_FILE_PATH = "<file-path-to-keystore>";
    private static final String KEY_STORE_PASS = "<keystore-password>";
 
    public static void main(String[] args) throws SQLException {
        final Properties properties = new Properties();
        final String connectionString = String.format(
                "jdbc:oracle:thin:@(DESCRIPTION=(ADDRESS=(PROTOCOL=TCPS)(HOST=%s)(PORT=%d))(CONNECT_DATA=(SID=%s)))",
                DB_SERVER_NAME, SSL_PORT, DB_SID);
        properties.put("user", DB_USER);
        properties.put("password", DB_PASSWORD);
        properties.put("oracle.jdbc.J2EE13Compliant", "true");
        properties.put("javax.net.ssl.trustStore", KEY_STORE_FILE_PATH);
        properties.put("javax.net.ssl.trustStoreType", "JKS");
        properties.put("javax.net.ssl.trustStorePassword", KEY_STORE_PASS);
        final Connection connection = DriverManager.getConnection(connectionString, properties);
        // If no exception, that means handshake has passed, and an SSL connection can be opened
    }
}
```

**Important**  
After you have determined that your database connections use SSL/TLS and have updated your application trust store, you can update your database to use the rds\-ca\-2019 certificates\. For instructions, see step 3 in [Updating your CA certificate by modifying your DB instance](UsingWithRDS.SSL-certificate-rotation.md#UsingWithRDS.SSL-certificate-rotation-updating)\.

## Configuring Kerberos authentication for Amazon RDS for Oracle<a name="oracle-kerberos"></a>

You can use Kerberos authentication to authenticate users when they connect to your Amazon RDS DB instance running Oracle\. In this configuration, your DB instance works with AWS Directory Service for Microsoft Active Directory, also called AWS Managed Microsoft AD\. When users authenticate with an Oracle DB instance joined to the trusting domain, authentication requests are forwarded to the directory that you create with AWS Directory Service\.

Keeping all of your credentials in the same directory can save you time and effort\. You have a centralized place for storing and managing credentials for multiple database instances\. A directory can also improve your overall security profile\.

Amazon RDS supports Kerberos authentication for Oracle DB instances in the following AWS Regions: 
+ US East \(Ohio\)
+ US East \(N\. Virginia\)
+ US West \(N\. California\)
+ US West \(Oregon\)
+ Asia Pacific \(Mumbai\)
+ Asia Pacific \(Seoul\)
+ Asia Pacific \(Singapore\)
+ Asia Pacific \(Sydney\)
+ Asia Pacific \(Tokyo\)
+ Canada \(Central\)
+ Europe \(Frankfurt\)
+ Europe \(Ireland\)
+ Europe \(London\)
+ Europe \(Stockholm\)
+ South America \(São Paulo\)
+ AWS GovCloud \(US\-East\)
+ AWS GovCloud \(US\-West\)

**Note**  
Kerberos authentication isn't supported for DB instance classes that are deprecated for Oracle DB instances\. For more information, see [DB instance class support for Oracle](CHAP_Oracle.md#Oracle.Concepts.InstanceClasses)\.

**Topics**
+ [Setting up Kerberos authentication for Oracle DB instances](oracle-kerberos-setting-up.md)
+ [Managing a DB instance in a domain](oracle-kerberos-managing.md)
+ [Connecting to Oracle with Kerberos authentication](oracle-kerberos-connecting.md)