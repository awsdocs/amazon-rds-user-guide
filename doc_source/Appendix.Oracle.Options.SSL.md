# Oracle Secure Sockets Layer<a name="Appendix.Oracle.Options.SSL"></a>

You enable Secure Sockets Layer \(SSL\) encryption for an Oracle DB instance by adding the Oracle SSL option to the option group associated with an Oracle DB instance\. You specify the port you want to communicate over using SSL\. You must configure SQL\*Plus as shown in this following section\. 

You enable SSL encryption for an Oracle DB instance by adding the Oracle SSL option to the option group associated with the DB instance\. Amazon RDS uses a second port, as required by Oracle, for SSL connections\. This approach allows both clear text and SSL\-encrypted communication to occur at the same time between a DB instance and SQL\*Plus\. For example, you can use the port with clear text communication to communicate with other resources inside a VPC while using the port with SSL\-encrypted communication to communicate with resources outside the VPC\. 

**Note**  
You can use Secure Sockets Layer or Native Network Encryption, but not both\. For more information, see [Oracle Native Network Encryption](Appendix.Oracle.Options.NetworkEncryption.md)\. 

You can use SSL encryption with the following Oracle database versions and editions: 
+ 19\.0\.0\.0: All versions, all editions including Standard Edition Two
+ 18\.0\.0\.0: All versions, all editions including Standard Edition Two
+ 12\.2\.0\.1: All versions, all editions including Standard Edition Two
+ 12\.1\.0\.2: All versions, all editions including Standard Edition Two
+ 11\.2\.0\.4: All versions, Enterprise Edition
+ 11\.2\.0\.4: Version 6 and later, Standard Edition, Standard Edition One, Enterprise Edition

**Note**  
You cannot use both SSL and Oracle native network encryption \(NNE\) on the same instance\. If you use SSL encryption, you must disable any other connection encryption\.

## TLS Versions for the Oracle SSL Option<a name="Appendix.Oracle.Options.SSL.TLS"></a>

Amazon RDS for Oracle supports Transport Layer Security \(TLS\) versions 1\.0 and 1\.2\. To use the Oracle SSL option, use the `SQLNET.SSL_VERSION` option setting\. The following values are allowed for this option setting:
+ `"1.0"` – Clients can connect to the DB instance using TLS 1\.0 only\.
+ `"1.2"` – Clients can connect to the DB instance using TLS 1\.2 only\.
+ `"1.2 or 1.0"` – Clients can connect to the DB instance using either TLS 1\.2 or 1\.0\.

To use the Oracle SSL option, the `SQLNET.SSL_VERSION` option setting is also required:
+ For existing Oracle SSL options, `SQLNET.SSL_VERSION` is set to `"1.0"` automatically\. You can change the setting if necessary\.
+ When you add a new Oracle SSL option, you must set `SQLNET.SSL_VERSION` explicitly to a valid value\.

The following table shows the TLS option settings that are supported for different Oracle engine versions and editions\.


****  

| Oracle Engine Version | SQLNET\.SSL\_VERSION = "1\.0" | SQLNET\.SSL\_VERSION = "1\.2" | SQLNET\.SSL\_VERSION = "1\.2 or 1\.0" | 
| --- | --- | --- | --- | 
|  19\.0\.0\.0 \(All editions\)  |  Supported  |  Supported  |  Supported  | 
|  18\.0\.0\.0 \(All editions\)  |  Supported  |  Supported  |  Supported  | 
|  12\.2\.0\.1 \(All editions\)  |  Supported  |  Supported  |  Supported  | 
|  12\.1\.0\.2 \(All editions\)  |  Supported  |  Supported  |  Supported  | 
|  11\.2\.0\.4 \(Oracle EE\)  |  Supported  |  Supported for 11\.2\.0\.4\.v8 and later  |  Supported for 11\.2\.0\.4\.v8 and later  | 
|  11\.2\.0\.4 \(Oracle SE1\)  |  Supported  |  Not supported  |  Not supported  | 
|  11\.2\.0\.4 \(Oracle SE\)  |  Supported  |  Not supported  |  Not supported  | 

## Cipher Suites for the Oracle SSL Option<a name="Appendix.Oracle.Options.SSL.CipherSuites"></a>

Amazon RDS for Oracle supports multiple SSL cipher suites\. By default, the Oracle SSL option is configured to use the `SSL_RSA_WITH_AES_256_CBC_SHA` cipher suite\. To specify a different cipher suite to use over SSL connections, use the `SQLNET.CIPHER_SUITE` option setting\. Following are the allowed values for this option setting:
+ `"SSL_RSA_WITH_AES_256_CBC_SHA"` – The default setting, which is compatible with TLS 1\.0 and TLS 1\.2
+ `"SSL_RSA_WITH_AES_256_CBC_SHA256"` – Only compatible with TLS 1\.2
+ `"SSL_RSA_WITH_AES_256_GCM_SHA384"` – Only compatible with TLS 1\.2

For existing Oracle SSL options, `SQLNET.CIPHER_SUITE` is set to `"SSL_RSA_WITH_AES_256_CBC_SHA"` automatically\. You can change the setting if necessary\.

The following table shows the cipher suite option settings that are supported for different Oracle engine versions and editions\.


****  

| Oracle Engine Version | SQLNET\.CIPHER\_SUITE = "SSL\_RSA\_WITH\_AES\_256\_CBC\_SHA" | SQLNET\.CIPHER\_SUITE = "SSL\_RSA\_WITH\_AES\_256\_CBC\_SHA256" | SQLNET\.CIPHER\_SUITE = "SSL\_RSA\_WITH\_AES\_256\_GCM\_SHA384" | 
| --- | --- | --- | --- | 
|  19\.0\.0\.0 \(All editions\)  |  Supported  |  Supported  |  Supported  | 
|  18\.0\.0\.0 \(All editions\)  |  Supported  |  Supported  |  Supported  | 
|  12\.2\.0\.1 \(All editions\)  |  Supported  |  Supported  |  Supported  | 
|  12\.1\.0\.2 \(All editions\)  |  Supported  |  Supported  |  Supported  | 
|  11\.2\.0\.4 \(Oracle EE\)  |  Supported  |  Not supported  |  Not supported  | 
|  11\.2\.0\.4 \(Oracle SE1\)  |  Supported  |  Not supported  |  Not supported  | 
|  11\.2\.0\.4 \(Oracle SE\)  |  Supported  |  Not supported  |  Not supported  | 

## FIPS Support<a name="Appendix.Oracle.Options.SSL.FIPS"></a>

Amazon RDS for Oracle enables you to use the Federal Information Processing Standard \(FIPS\) standard for 140\-2\. FIPS 140\-2 is a United States government standard that defines cryptographic module security requirements\. You enable the FIPS standard by setting the setting `FIPS.SSLFIPS_140` to `TRUE` for the Oracle SSL option\. When FIPS 140\-2 is configured for SSL, the cryptographic libraries are designed to encrypt data between the client and the Oracle DB instance\. 

You can enable the FIPS setting with the following Oracle database versions and editions: 
+ 19\.0\.0\.0: All versions, all editions including Standard Edition Two
+ 18\.0\.0\.0: All versions, all editions including Standard Edition Two
+ 12\.2\.0\.1: All versions, all editions including Standard Edition Two
+ 12\.1\.0\.2: Version 2 and later, all editions including Standard Edition Two

Clients must use the cipher suite that is FIPS\-compliant\. When establishing a connection, the client and Oracle DB instance negotiate which cipher suite to use when transmitting messages back and forth\. The following table shows the FIPS\-compliant SSL cipher suites for each TLS version\. 


****  

| SQLNET\.SSL\_VERSION | Supported Cipher Suites | 
| --- | --- | 
|  1\.0  |  SSL\_RSA\_WITH\_AES\_256\_CBC\_SHA  | 
|  1\.2  |  SSL\_RSA\_WITH\_AES\_256\_CBC\_SHA SSL\_RSA\_WITH\_AES\_256\_GCM\_SHA384  | 

For more information, see [Oracle Database FIPS 140\-2 Settings](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dbseg/oracle-database-fips-140-settings.html#GUID-DDBEB3F9-B216-44BB-8C18-43B5E468CBBB) in the Oracle documentation\.

## Adding the SSL Option<a name="Appendix.Oracle.Options.SSL.OptionGroup"></a>

To use SSL, your Amazon RDS Oracle DB instance must be associated with an option group that includes the `SSL` option\.

### Console<a name="Appendix.Oracle.Options.SSL.OptionGroup.Console"></a>

**To add the SSL option to an option group**

1. Create a new option group or identify an existing option group to which you can add the `SSL` option\.

   For information about creating an option group, see [Creating an Option Group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.Create)\.

1. Add the `SSL` option to the option group\.

   If you want to use only FIPS\-verified cipher suites for SSL connections, set the option `FIPS.SSLFIPS_140` to `TRUE`\. For information about the FIPS standard, see [FIPS Support](#Appendix.Oracle.Options.SSL.FIPS)\.

   For information about adding an option to an option group, see [Adding an Option to an Option Group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.AddOption)\.

1. Create a new Oracle DB instance and associate the option group with it, or modify an Oracle DB instance to associate the option group with it\.

   For information about creating a DB instance, see [Creating an Amazon RDS DB Instance](USER_CreateDBInstance.md)\.

   For information about modifying a DB instance, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md)\.

### AWS CLI<a name="Appendix.Oracle.Options.SSL.OptionGroup.CLI"></a>

**To add the SSL option to an option group**

1. Create a new option group or identify an existing option group to which you can add the `SSL` option\.

   For information about creating an option group, see [Creating an Option Group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.Create)\.

1. Add the `SSL` option to the option group\.

   Specify the following option settings:
   + `Port` – The SSL port number
   + `VpcSecurityGroupMemberships` – The VPC security group for which the option is enabled
   + `SQLNET.SSL_VERSION` – The TLS version that client can use to connect to the DB instance

   For example, the following AWS CLI command adds the `SSL` option to an option group named `ora-option-group`\.  
**Example**  

   For Linux, macOS, or Unix:

   ```
   aws rds add-option-to-option-group --option-group-name ora-option-group \
     --options 'OptionName=SSL,Port=2484,VpcSecurityGroupMemberships="sg-68184619",OptionSettings=[{Name=SQLNET.SSL_VERSION,Value=1.0}]'
   ```

   For Windows:

   ```
   aws rds add-option-to-option-group --option-group-name ora-option-group ^
     --options 'OptionName=SSL,Port=2484,VpcSecurityGroupMemberships="sg-68184619",OptionSettings=[{Name=SQLNET.SSL_VERSION,Value=1.0}]'
   ```

1. Create a new Oracle DB instance and associate the option group with it, or modify an Oracle DB instance to associate the option group with it\.

   For information about creating a DB instance, see [Creating an Amazon RDS DB Instance](USER_CreateDBInstance.md)\.

   For information about modifying a DB instance, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md)\.

## Configuring SQL\*Plus to Use SSL with an Oracle DB Instance<a name="Appendix.Oracle.Options.SSL.ClientConfiguration"></a>

You must configure SQL\*Plus before connecting to an Oracle DB instance that uses the Oracle SSL option\.

**Note**  
To allow access to the DB instance from the appropriate clients, ensure that your security groups are configured correctly\. For more information, see [Controlling Access with Security Groups](Overview.RDSSecurityGroups.md)\. Also, these instructions are for SQL\*Plus and other clients that directly use an Oracle home\. For JDBC connections, see [Setting Up an SSL Connection Over JDBC](#Appendix.Oracle.Options.SSL.JDBC)\.

**To configure SQL\*Plus to use SSL to connect to an Oracle DB instance**

1. Set the `ORACLE_HOME` environment variable to the location of your Oracle home directory\.

   The path to your Oracle home directory depends on your installation\. The following example sets the `ORACLE_HOME` environment variable\.

   ```
   prompt>export ORACLE_HOME=/home/user/app/user/product/12.1.0/dbhome_1
   ```

   For information about setting Oracle environment variables, see [SQL\*Plus Environment Variables](http://docs.oracle.com/database/121/SQPUG/ch_two.htm#SQPUG331) in the Oracle documentation, and also see the Oracle installation guide for your operating system\.

1. Append `$ORACLE_HOME/lib` to the `LD_LIBRARY_PATH` environment variable\.

   The following is an example that sets the LD\_LIBRARY\_PATH environment variable\.

   ```
   prompt>export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$ORACLE_HOME/lib 
   ```

1. Create a directory for the Oracle wallet at `$ORACLE_HOME/ssl_wallet`\.

   The following is an example that creates the Oracle wallet directory\.

   ```
   prompt>mkdir $ORACLE_HOME/ssl_wallet 
   ```

1. Download the root certificate that works for all AWS Regions and put the file in the ssl\_wallet directory\.

   For information about downloading the root certificate, see [Using SSL/TLS to Encrypt a Connection to a DB Instance](UsingWithRDS.SSL.md)\.

1. In the `$ORACLE_HOME/network/admin` directory, modify or create the tnsnames\.ora file and include the following entry\.

   ```
   <net_service_name>= (DESCRIPTION = (ADDRESS_LIST = (ADDRESS = (PROTOCOL = TCPS) 
      (HOST = <endpoint>) (PORT = <ssl port number>)))(CONNECT_DATA = (SID = <database name>))
      (SECURITY = (SSL_SERVER_CERT_DN = "C=US,ST=Washington,L=Seattle,O=Amazon.com,OU=RDS,CN=<endpoint>")))
   ```

1. In the same directory, modify or create the sqlnet\.ora file and include the following parameters\.
**Note**  
To communicate with entities over a TLS secured connection, Oracle requires a wallet with the necessary certificates for authentication\. You can use Oracle's ORAPKI utility to create and maintain Oracle wallets, as shown in step 7\. For more information, see [Setting Up Oracle Wallet Using ORAPKI](https://docs.oracle.com/cd/E87544_01/pt856pbr1/eng/pt/tsvt/task_SettingUpOracleWalletUsingORAPKI.html) in the Oracle documentation\.

   ```
   WALLET_LOCATION = (SOURCE = (METHOD = FILE) (METHOD_DATA = (DIRECTORY = $ORACLE_HOME/ssl_wallet))) 
   SSL_CLIENT_AUTHENTICATION = FALSE 
   SSL_VERSION = 1.0 
   SSL_CIPHER_SUITES = (SSL_RSA_WITH_AES_256_CBC_SHA) 
   SSL_SERVER_DN_MATCH = ON
   ```
**Note**  
You can set `SSL_VERSION` to a higher value if your DB instance supports it\.

1. Run the following commands to create the Oracle wallet\.

   ```
   prompt>orapki wallet create -wallet $ORACLE_HOME/ssl_wallet -auto_login_only   
   
   prompt>orapki wallet add -wallet $ORACLE_HOME/ssl_wallet -trusted_cert -cert
         $ORACLE_HOME/ssl_wallet/rds-ca-2019-root.pem -auto_login_only
   ```

   Replace the file name with the one you downloaded\.

## Connecting to an Oracle DB Instance Using SSL<a name="Appendix.Oracle.Options.SSL.Connecting"></a>

After you configure SQL\*Plus to use SSL as described previously, you can connect to the Oracle DB instance with the SSL option\. Optionally, you can first export the `TNS_ADMIN` value that points to the directory that contains the tnsnames\.ora and sqlnet\.ora files\. Doing so ensures that SQL\*Plus can find these files consistently\. The following example exports the `TNS_ADMIN` value\.

```
  
export TNS_ADMIN = ${ORACLE_HOME}/network/admin
```

Connect to the DB instance\. For example, you can connect using SQL\*Plus and a *<net\_service\_name>* in a tnsnames\.ora file\.

```
sqlplus <mydbuser>@<net_service_name>
```

You can also connect to the DB instance using SQL\*Plus without using a tnsnames\.ora file by using the following command\.

```
sqlplus '<mydbuser>@(DESCRIPTION = (ADDRESS = (PROTOCOL = TCPS)(HOST = <endpoint>) (PORT = <ssl port number>))(CONNECT_DATA = (SID = <database name>)))'
```

You can also connect to the Oracle DB instance without using SSL\. For example, the following command connects to the DB instance through the clear text port without SSL encryption\.

```
sqlplus '<mydbuser>@(DESCRIPTION = (ADDRESS = (PROTOCOL = TCP)(HOST = <endpoint>) (PORT = <port number>))(CONNECT_DATA = (SID = <database name>)))'
```

If you want to close Transmission Control Protocol \(TCP\) port access, create a security group with no IP address ingresses and add it to the instance\. This addition closes connections over the TCP port, while still allowing connections over the SSL port that are specified from IP addresses within the range permitted by the SSL option security group\.

## Setting Up an SSL Connection Over JDBC<a name="Appendix.Oracle.Options.SSL.JDBC"></a>

To use an SSL connection over JDBC, you must create a keystore, trust the Amazon RDS root CA certificate, and use the code snippet specified following\.

To create the keystore in JKS format, use the following command\. For more information about creating the keystore, see the [Oracle documentation](https://docs.oracle.com/cd/E19509-01/820-3503/ggfen/index.html)\. 

```
keytool -keystore clientkeystore -genkey -alias client
```

Next, take the following steps to trust the Amazon RDS root CA certificate\.

**To trust the Amazon RDS root CA certificate**

1. Download the root certificate that works for all AWS Regions and put the file in the ssl\_wallet directory\.

   For information about downloading the root certificate, see [Using SSL/TLS to Encrypt a Connection to a DB Instance](UsingWithRDS.SSL.md)\.

1.  Convert the certificate to \.der format using the following command\.

   ```
   openssl x509 -outform der -in rds-ca-2019-root.pem -out rds-ca-2019-root.der                    
   ```

   Replace the file name with the one you downloaded\.

1.  Import the certificate into the keystore using the following command\. 

   ```
   keytool -import -alias rds-root -keystore clientkeystore -file rds-ca-2019-root.der                    
   ```

1. Confirm that the key store was created successfully\.

   ```
   keytool -list -v -keystore clientkeystore.jks                        
   ```

   Enter the keystore password when you are prompted for it\.

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

## Enforcing a DN Match with an SSL Connection<a name="Appendix.Oracle.Options.SSL.DNMatch"></a>

You can use the Oracle parameter `SSL_SERVER_DN_MATCH` to enforce that the distinguished name \(DN\) for the database server matches its service name\. If you enforce the match verifications, then SSL ensures that the certificate is from the server\. If you don't enforce the match verification, then SSL performs the check but allows the connection, regardless if there is a match\. If you do not enforce the match, you allow the server to potentially fake its identify\.

To enforce DN matching, add the DN match property and use the connection string specified below\.

Add the property to the client connection to enforce DN matching\.

```
properties.put("oracle.net.ssl_server_dn_match", "TRUE”);                
```

Use the following connection string to enforce DN matching when using SSL\.

```
final String connectionString = String.format(
    "jdbc:oracle:thin:@(DESCRIPTION=(ADDRESS=(PROTOCOL=TCPS)(HOST=%s)(PORT=%d))" +
    "(CONNECT_DATA=(SID=%s))" +
    "(SECURITY = (SSL_SERVER_CERT_DN = 
\"C=US,ST=Washington,L=Seattle,O=Amazon.com,OU=RDS,CN=%s\")))",
    DB_SERVER_NAME, SSL_PORT, DB_SID, DB_SERVER_NAME);
```