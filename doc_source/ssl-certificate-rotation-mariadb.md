# Updating Applications to Connect to MariaDB DB Instances Using New SSL/TLS Certificates<a name="ssl-certificate-rotation-mariadb"></a>

As of September 19, 2019, Amazon RDS has published new Certificate Authority \(CA\) certificates for connecting to your RDS DB instances using Secure Socket Layer or Transport Layer Security \(SSL/TLS\)\. Following, you can find information about updating your applications to use the new certificates\.

This topic can help you to determine whether your applications require certificate verification to connect to your DB instances\. 

**Note**  
Some applications are configured to connect to MariaDB only if they can successfully verify the certificate on the server\. For such applications, you must update your client application trust stores to include the new CA certificates\.   
You can specify the following SSL modes: `disabled`, `preferred`, and `required`\. When you use the `preferred` SSL mode and the CA certificate doesn't exist or isn't up to date, the following behavior applies:  
For MariaDB version 10\.2 and higher, and newer minor versions of 10\.0 and 10\.1, the connection falls back to not using SSL and still connects successfully\.  
Because these later versions use the OpenSSL protocol, an expired server certificate doesn't prevent successful connections unless the `required` SSL mode is specified\.
For older MariaDB 10\.0 and 10\.1 minor versions, an error is returned\.  
Because these older versions use the yaSSL protocol, certificate verification is strictly enforced and the connection is unsuccessful\.  
The following MariaDB minor versions use the yaSSL protocol:  
10\.0\.17, 10\.0\.24, 10\.0\.28, 10\.0\.31, 10\.0\.32
10\.1\.14, 10\.1\.19, 10\.1\.23, 10\.1\.26

After you update your CA certificates in the client application trust stores, you can rotate the certificates on your DB instances\. We strongly recommend testing these procedures in a development or staging environment before implementing them in your production environments\.

For more information about certificate rotation, see [Rotating Your SSL/TLS Certificate](UsingWithRDS.SSL-certificate-rotation.md)\. For more information about downloading certificates, see [Using SSL/TLS to Encrypt a Connection to a DB Instance](UsingWithRDS.SSL.md)\. For information about using SSL/TLS with MariaDB DB instances, see [Using SSL with a MariaDB DB Instance](CHAP_MariaDB.md#MariaDB.Concepts.SSLSupport)\.

**Topics**
+ [Determining Whether a Client Requires Certificate Verification in Order to Connect](#ssl-certificate-rotation-mariadb.determining)
+ [Updating Your Application Trust Store](#ssl-certificate-rotation-mariadb.updating-trust-store)
+ [Example Java Code for Establishing SSL Connections](#ssl-certificate-rotation-mariadb.java-example)

## Determining Whether a Client Requires Certificate Verification in Order to Connect<a name="ssl-certificate-rotation-mariadb.determining"></a>

You can check whether JDBC clients and MySQL clients require certificate verification to connect\.

### JDBC<a name="ssl-certificate-rotation-mysql.determining-client.jdbc"></a>

The following example with MySQL Connector/J 8\.0 shows one way to check an application's JDBC connection properties to determine whether successful connections require a valid certificate\. For more information on all of the JDBC connection options for MySQL, see [ Configuration Properties](https://dev.mysql.com/doc/connector-j/8.0/en/connector-j-reference-configuration-properties.html) in the MySQL documentation\.

When using the MySQL Connector/J 8\.0, an SSL connection requires verification against the server CA certificate if your connection properties have `sslMode` set to `VERIFY_CA` or `VERIFY_IDENTITY`, as in the following example\.

```
Properties properties = new Properties();
properties.setProperty("sslMode", "VERIFY_IDENTITY");
properties.put("user", DB_USER);
properties.put("password", DB_PASSWORD);
```

**Note**  
If you use either the MySQL Java Connector v5\.1\.38 or later, or the MySQL Java Connector v8\.0\.9 or later to connect to your databases, even if you haven't explicitly configured your applications to use SSL/TLS when connecting to your databases, these client drivers default to using SSL/TLS\. In addition, when using SSL/TLS, they perform partial certificate verification and fail to connect if the database server certificate is expired\.

### MySQL<a name="ssl-certificate-rotation-mysql.determining-client.mysql"></a>

The following examples with the MySQL Client show two ways to check a script's MySQL connection to determine whether successful connections require a valid certificate\. For more information on all of the connection options with the MySQL Client, see [ Client\-Side Configuration for Encrypted Connections](https://dev.mysql.com/doc/refman/8.0/en/using-encrypted-connections.html#using-encrypted-connections-client-side-configuration) in the MySQL documentation\.

When using the MySQL 5\.7 or MySQL 8\.0 Client, an SSL connection requires verification against the server CA certificate if for the `--ssl-mode` option you specify `VERIFY_CA` or `VERIFY_IDENTITY`, as in the following example\.

```
mysql -h mysql-database.rds.amazonaws.com -uadmin -ppassword --ssl-ca=/tmp/ssl-cert.pem --ssl-mode=VERIFY_CA                
```

When using the MySQL 5\.6 Client, an SSL connection requires verification against the server CA certificate if you specify the `--ssl-verify-server-cert` option, as in the following example\.

```
mysql -h mysql-database.rds.amazonaws.com -uadmin -ppassword --ssl-ca=/tmp/ssl-cert.pem --ssl-verify-server-cert            
```

## Updating Your Application Trust Store<a name="ssl-certificate-rotation-mariadb.updating-trust-store"></a>

For information about updating the trust store for MySQL applications, see [Using TLS/SSL with MariaDB Connector/J](https://mariadb.com/kb/en/library/using-tls-ssl-with-mariadb-java-connector/) in the MariaDB documentation\.

**Note**  
When you update the trust store, you can retain older certificates in addition to adding the new certificates\.

### Updating Your Application Trust Store for JDBC<a name="ssl-certificate-rotation-mariadb.updating-trust-store.jdbc"></a>

You can update the trust store for applications that use JDBC for SSL/TLS connections\.

**To update the trust store for JDBC applications**

1. Download the 2019 root certificate that works for all AWS Regions and put the file in the trust store directory\.

   For information about downloading the root certificate, see [Using SSL/TLS to Encrypt a Connection to a DB Instance](UsingWithRDS.SSL.md)\.

1.  Convert the certificate to \.der format using the following command\.

   ```
   openssl x509 -outform der -in rds-ca-2019-root.pem -out rds-ca-2019-root.der                    
   ```

   Replace the file name with the one that you downloaded\.

1.  Import the certificate into the key store using the following command\. 

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

If you are using the MariaDB Connector/J JDBC driver in an application, set the following properties in the application\.

```
System.setProperty("javax.net.ssl.trustStore", certs);
System.setProperty("javax.net.ssl.trustStorePassword", "password");
```

When you start the application, set the following properties\.

```
java -Djavax.net.ssl.trustStore=/path_to_truststore/MyTruststore.jks -Djavax.net.ssl.trustStorePassword=my_truststore_password com.companyName.MyApplication        
```

## Example Java Code for Establishing SSL Connections<a name="ssl-certificate-rotation-mariadb.java-example"></a>

The following code example shows how to set up the SSL connection using JDBC\.

```
private static final String DB_USER = "admin";

        private static final String DB_USER = "user name";
        private static final String DB_PASSWORD = "password";
        // This key store has only the prod root ca.
        private static final String KEY_STORE_FILE_PATH = "file-path-to-keystore";
        private static final String KEY_STORE_PASS = "keystore-password";
        
    public static void main(String[] args) throws Exception {
        Class.forName("org.mariadb.jdbc.Driver");

        System.setProperty("javax.net.ssl.trustStore", KEY_STORE_FILE_PATH);
        System.setProperty("javax.net.ssl.trustStorePassword", KEY_STORE_PASS);

        Properties properties = new Properties();
        properties.put("user", DB_USER);
        properties.put("password", DB_PASSWORD);


        Connection connection = DriverManager.getConnection("jdbc:mysql://ssl-mariadb-public.cni62e2e7kwh.us-east-1.rds.amazonaws.com:3306?useSSL=true",properties);
        Statement stmt=connection.createStatement();

        ResultSet rs=stmt.executeQuery("SELECT 1 from dual");

        return;
    }
```

**Important**  
After you have determined that your database connections use SSL/TLS and have updated your application trust store, you can update your database to use the rds\-ca\-2019 certificates\. For instructions, see step 3 in [Updating Your CA Certificate by Modifying Your DB Instance](UsingWithRDS.SSL-certificate-rotation.md#UsingWithRDS.SSL-certificate-rotation-updating)\.