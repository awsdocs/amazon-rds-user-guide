# Oracle Secure Sockets Layer<a name="Appendix.Oracle.Options.SSL"></a>

You enable Secure Sockets Layer \(SSL\) encryption for an RDS for Oracle DB instance by adding the Oracle SSL option to the option group associated with an RDS for Oracle DB instance\. You specify the port you want to communicate using SSL\. You must configure SQL\*Plus as shown in this following section\.

You enable SSL encryption for an RDS for Oracle DB instance by adding the Oracle SSL option to the option group associated with the DB instance\. Amazon RDS uses a second port, as required by Oracle, for SSL connections\. This approach allows both clear text and SSL\-encrypted communication to occur at the same time between a DB instance and SQL\*Plus\. For example, you can use the port with clear text communication to communicate with other resources inside a VPC while using the port with SSL\-encrypted communication to communicate with resources outside the VPC\.

**Note**  
You can use SSL or Native Network Encryption \(NNE\) on the same RDS for Oracle DB instance, but not both\. If you use SSL encryption, make sure to turn off any other connection encryption\. For more information, see [Oracle native network encryption](Appendix.Oracle.Options.NetworkEncryption.md)\.

SSL/TLS and NNE and are no longer part of Oracle Advanced Security\. In RDS for Oracle, you can use SSL encryption with all licensed editions of the following database versions:
+ Oracle Database 21c \(21\.0\.0\)
+ Oracle Database 19c \(19\.0\.0\)
+ Oracle Database 12c Release 2 \(12\.2\) – this release is no longer supported
+ Oracle Database 12c Release 1 \(12\.1\) – this release is no longer supported

The following table summarizes SSL support for RDS for Oracle\. The specified Oracle Database releases support all editions\.


| Cipher suite \(SQLNET\.CIPHER\_SUITE\) | TLS version support \(SQLNET\.SSL\_VERSION\) | Supported Oracle Database releases | FIPS support | FedRAMP compliant | 
| --- | --- | --- | --- | --- | 
| SSL\_RSA\_WITH\_AES\_256\_CBC\_SHA \(default\) | 1\.0 and 1\.2 | 12c, 19c, 21c | Yes | No | 
| SSL\_RSA\_WITH\_AES\_256\_CBC\_SHA256 | 1\.2 | 12c, 19c, 21c | Yes | No | 
| SSL\_RSA\_WITH\_AES\_256\_GCM\_SHA384 | 1\.2 | 12c, 19c, 21c | Yes | No | 
| TLS\_ECDHE\_RSA\_WITH\_AES\_256\_GCM\_SHA384 | 1\.2 | 19c, 21c | Yes | Yes | 
| TLS\_ECDHE\_RSA\_WITH\_AES\_128\_GCM\_SHA256 | 1\.2 | 19c, 21c | Yes | Yes | 
| TLS\_ECDHE\_RSA\_WITH\_AES\_256\_CBC\_SHA384 | 1\.2 | 19c, 21c | Yes | Yes | 
| TLS\_ECDHE\_RSA\_WITH\_AES\_128\_CBC\_SHA256 | 1\.2 | 19c, 21c | Yes | Yes | 
| TLS\_ECDHE\_RSA\_WITH\_AES\_256\_CBC\_SHA | 1\.2 | 19c, 21c | Yes | Yes | 
| TLS\_ECDHE\_RSA\_WITH\_AES\_128\_CBC\_SHA | 1\.2 | 19c, 21c | Yes | Yes | 

## TLS versions for the Oracle SSL option<a name="Appendix.Oracle.Options.SSL.TLS"></a>

Amazon RDS for Oracle supports Transport Layer Security \(TLS\) versions 1\.0 and 1\.2\. When you add a new Oracle SSL option, you must set `SQLNET.SSL_VERSION` explicitly to a valid value\. The following values are allowed for this option setting:
+ `"1.0"` – Clients can connect to the DB instance using TLS 1\.0 only\. For existing Oracle SSL options, `SQLNET.SSL_VERSION` is set to `"1.0"` automatically\. You can change the setting if necessary\.
+ `"1.2"` – Clients can connect to the DB instance using TLS 1\.2 only\.
+ `"1.2 or 1.0"` – Clients can connect to the DB instance using either TLS 1\.2 or 1\.0\.

## Cipher suites for the Oracle SSL option<a name="Appendix.Oracle.Options.SSL.CipherSuites"></a>

Amazon RDS for Oracle supports multiple SSL cipher suites\. By default, the Oracle SSL option is configured to use the `SSL_RSA_WITH_AES_256_CBC_SHA` cipher suite\. To specify a different cipher suite to use over SSL connections, use the `SQLNET.CIPHER_SUITE` option setting\.

## FIPS support<a name="Appendix.Oracle.Options.SSL.FIPS"></a>

RDS for Oracle allows you to use the Federal Information Processing Standard \(FIPS\) standard for 140\-2\. FIPS 140\-2 is a United States government standard that defines cryptographic module security requirements\. You turn on the FIPS standard by setting `FIPS.SSLFIPS_140` to `TRUE` for the Oracle SSL option\. When FIPS 140\-2 is configured for SSL, the cryptographic libraries encrypt data between the client and the RDS for Oracle DB instance\.

Clients must use the cipher suite that is FIPS\-compliant\. When establishing a connection, the client and RDS for Oracle DB instance negotiate which cipher suite to use when transmitting messages back and forth\. The following table shows the FIPS\-compliant SSL cipher suites for each TLS version\. For more information, see [Oracle database FIPS 140\-2 settings](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dbseg/oracle-database-fips-140-settings.html#GUID-DDBEB3F9-B216-44BB-8C18-43B5E468CBBB) in the Oracle documentation\.

## Adding the SSL option<a name="Appendix.Oracle.Options.SSL.OptionGroup"></a>

To use SSL, your RDS for Oracle DB instance must be associated with an option group that includes the `SSL` option\.

### Console<a name="Appendix.Oracle.Options.SSL.OptionGroup.Console"></a>

**To add the SSL option to an option group**

1. Create a new option group or identify an existing option group to which you can add the `SSL` option\.

   For information about creating an option group, see [Creating an option group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.Create)\.

1. Add the `SSL` option to the option group\.

   If you want to use only FIPS\-verified cipher suites for SSL connections, set the option `FIPS.SSLFIPS_140` to `TRUE`\. For information about the FIPS standard, see [FIPS support](#Appendix.Oracle.Options.SSL.FIPS)\.

   For information about adding an option to an option group, see [Adding an option to an option group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.AddOption)\.

1. Create a new RDS for Oracle DB instance and associate the option group with it, or modify an RDS for Oracle DB instance to associate the option group with it\.

   For information about creating an DB instance, see [Creating an Amazon RDS DB instance](USER_CreateDBInstance.md)\.

   For information about modifying an DB instance, see [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)\.

### AWS CLI<a name="Appendix.Oracle.Options.SSL.OptionGroup.CLI"></a>

**To add the SSL option to an option group**

1. Create a new option group or identify an existing option group to which you can add the `SSL` option\.

   For information about creating an option group, see [Creating an option group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.Create)\.

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

1. Create a new RDS for Oracle DB instance and associate the option group with it, or modify an RDS for Oracle DB instance to associate the option group with it\.

   For information about creating an DB instance, see [Creating an Amazon RDS DB instance](USER_CreateDBInstance.md)\.

   For information about modifying an DB instance, see [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)\.

## Configuring SQL\*Plus to use SSL with an RDS for Oracle DB instance<a name="Appendix.Oracle.Options.SSL.ClientConfiguration"></a>

You must configure SQL\*Plus before connecting to an RDS for Oracle DB instance that uses the Oracle SSL option\.

**Note**  
To allow access to the DB instance from the appropriate clients, ensure that your security groups are configured correctly\. For more information, see [Controlling access with security groups](Overview.RDSSecurityGroups.md)\. Also, these instructions are for SQL\*Plus and other clients that directly use an Oracle home\. For JDBC connections, see [Setting up an SSL connection over JDBC](#Appendix.Oracle.Options.SSL.JDBC)\.

**To configure SQL\*Plus to use SSL to connect to an RDS for Oracle DB instance**

1. Set the `ORACLE_HOME` environment variable to the location of your Oracle home directory\.

   The path to your Oracle home directory depends on your installation\. The following example sets the `ORACLE_HOME` environment variable\.

   ```
   prompt>export ORACLE_HOME=/home/user/app/user/product/12.1.0/dbhome_1
   ```

   For information about setting Oracle environment variables, see [SQL\*Plus environment variables](http://docs.oracle.com/database/121/SQPUG/ch_two.htm#SQPUG331) in the Oracle documentation, and also see the Oracle installation guide for your operating system\.

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

   For information about downloading the root certificate, see [Using SSL/TLS to encrypt a connection to a DB instance](UsingWithRDS.SSL.md)\.

1. In the `$ORACLE_HOME/network/admin` directory, modify or create the tnsnames\.ora file and include the following entry\.

   ```
   <net_service_name>= (DESCRIPTION = (ADDRESS_LIST = (ADDRESS = (PROTOCOL = TCPS) 
      (HOST = <endpoint>) (PORT = <ssl port number>)))(CONNECT_DATA = (SID = <database name>))
      (SECURITY = (SSL_SERVER_CERT_DN = "C=US,ST=Washington,L=Seattle,O=Amazon.com,OU=RDS,CN=<endpoint>")))
   ```

1. In the same directory, modify or create the sqlnet\.ora file and include the following parameters\.
**Note**  
To communicate with entities over a TLS secured connection, Oracle requires a wallet with the necessary certificates for authentication\. You can use Oracle's ORAPKI utility to create and maintain Oracle wallets, as shown in step 7\. For more information, see [Setting up Oracle wallet using ORAPKI](https://docs.oracle.com/cd/E87544_01/pt856pbr1/eng/pt/tsvt/task_SettingUpOracleWalletUsingORAPKI.html) in the Oracle documentation\.

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

## Connecting to an RDS for Oracle DB instance using SSL<a name="Appendix.Oracle.Options.SSL.Connecting"></a>

After you configure SQL\*Plus to use SSL as described previously, you can connect to the RDS for Oracle DB instance with the SSL option\. Optionally, you can first export the `TNS_ADMIN` value that points to the directory that contains the tnsnames\.ora and sqlnet\.ora files\. Doing so ensures that SQL\*Plus can find these files consistently\. The following example exports the `TNS_ADMIN` value\.

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

You can also connect to the RDS for Oracle DB instance without using SSL\. For example, the following command connects to the DB instance through the clear text port without SSL encryption\.

```
sqlplus '<mydbuser>@(DESCRIPTION = (ADDRESS = (PROTOCOL = TCP)(HOST = <endpoint>) (PORT = <port number>))(CONNECT_DATA = (SID = <database name>)))'          
```

If you want to close Transmission Control Protocol \(TCP\) port access, create a security group with no IP address ingresses and add it to the instance\. This addition closes connections over the TCP port, while still allowing connections over the SSL port that are specified from IP addresses within the range permitted by the SSL option security group\.

## Setting up an SSL connection over JDBC<a name="Appendix.Oracle.Options.SSL.JDBC"></a>

To use an SSL connection over JDBC, you must create a keystore, trust the Amazon RDS root CA certificate, and use the code snippet specified following\.

To create the keystore in JKS format, use the following command\. For more information about creating the keystore, see the [Oracle documentation](https://docs.oracle.com/cd/E19509-01/820-3503/ggfen/index.html)\.

```
keytool -keystore clientkeystore -genkey -alias client            
```

Next, take the following steps to trust the Amazon RDS root CA certificate\.

**To trust the Amazon RDS root CA certificate**

1. Download the root certificate that works for all AWS Regions and put the file in the ssl\_wallet directory\.

   For information about downloading the root certificate, see [Using SSL/TLS to encrypt a connection to a DB instance](UsingWithRDS.SSL.md)\.

1.  Convert the certificate to \.der format using the following command\.

   ```
   openssl x509 -outform der -in rds-ca-2019-root.pem -out rds-ca-2019-root.der                
   ```

   Replace the file name with the one you downloaded\.

1.  Import the certificate into the keystore using the following command\.

   ```
   keytool -import -alias rds-root -keystore clientkeystore.jks -file rds-ca-2019-root.der                
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

## Enforcing a DN match with an SSL connection<a name="Appendix.Oracle.Options.SSL.DNMatch"></a>

You can use the Oracle parameter `SSL_SERVER_DN_MATCH` to enforce that the distinguished name \(DN\) for the database server matches its service name\. If you enforce the match verifications, then SSL ensures that the certificate is from the server\. If you don't enforce the match verification, then SSL performs the check but allows the connection, regardless if there is a match\. If you do not enforce the match, you allow the server to potentially fake its identify\.

To enforce DN matching, add the DN match property and use the connection string specified below\.

Add the property to the client connection to enforce DN matching\.

```
properties.put("oracle.net.ssl_server_dn_match", "TRUE");            
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