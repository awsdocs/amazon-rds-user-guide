# Using new SSL/TLS certificates for MariaDB DB instances<a name="ssl-certificate-rotation-mariadb"></a>

As of September 19, 2019, Amazon RDS has published new Certificate Authority \(CA\) certificates for connecting to your RDS DB instances using Secure Socket Layer or Transport Layer Security \(SSL/TLS\)\. Following, you can find information about updating your applications to use the new certificates\.

This topic can help you to determine whether your applications require certificate verification to connect to your DB instances\. 

**Note**  
Some applications are configured to connect to MariaDB only if they can successfully verify the certificate on the server\. For such applications, you must update your client application trust stores to include the new CA certificates\.   
You can specify the following SSL modes: `disabled`, `preferred`, and `required`\. When you use the `preferred` SSL mode and the CA certificate doesn't exist or isn't up to date, the connection falls back to not using SSL and still connects successfully\.  
We recommend avoiding `preferred` mode\. In `preferred` mode, if the connection encounters an invalid certificate, it stops using encryption and proceeds unencrypted\.

After you update your CA certificates in the client application trust stores, you can rotate the certificates on your DB instances\. We strongly recommend testing these procedures in a development or staging environment before implementing them in your production environments\.

For more information about certificate rotation, see [Rotating your SSL/TLS certificate](UsingWithRDS.SSL-certificate-rotation.md)\. For more information about downloading certificates, see [Using SSL/TLS to encrypt a connection to a DB instance](UsingWithRDS.SSL.md)\. For information about using SSL/TLS with MariaDB DB instances, see [Using SSL/TLS with a MariaDB DB instance](mariadb-ssl-connections.md#MariaDB.Concepts.SSLSupport)\.

**Topics**
+ [Determining whether a client requires certificate verification in order to connect](#ssl-certificate-rotation-mariadb.determining)
+ [Updating your application trust store](#ssl-certificate-rotation-mariadb.updating-trust-store)
+ [Example Java code for establishing SSL connections](#ssl-certificate-rotation-mariadb.java-example)

## Determining whether a client requires certificate verification in order to connect<a name="ssl-certificate-rotation-mariadb.determining"></a>

You can check whether JDBC clients and MySQL clients require certificate verification to connect\.

### JDBC<a name="ssl-certificate-rotation-mysql.determining-client.jdbc"></a>

The following example with MySQL Connector/J 8\.0 shows one way to check an application's JDBC connection properties to determine whether successful connections require a valid certificate\. For more information on all of the JDBC connection options for MySQL, see [ Configuration properties](https://dev.mysql.com/doc/connector-j/8.0/en/connector-j-reference-configuration-properties.html) in the MySQL documentation\.

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

The following examples with the MySQL Client show two ways to check a script's MySQL connection to determine whether successful connections require a valid certificate\. For more information on all of the connection options with the MySQL Client, see [ Client\-side configuration for encrypted connections](https://dev.mysql.com/doc/refman/8.0/en/using-encrypted-connections.html#using-encrypted-connections-client-side-configuration) in the MySQL documentation\.

When using the MySQL 5\.7 or MySQL 8\.0 Client, an SSL connection requires verification against the server CA certificate if for the `--ssl-mode` option you specify `VERIFY_CA` or `VERIFY_IDENTITY`, as in the following example\.

```
mysql -h mysql-database.rds.amazonaws.com -uadmin -ppassword --ssl-ca=/tmp/ssl-cert.pem --ssl-mode=VERIFY_CA                
```

When using the MySQL 5\.6 Client, an SSL connection requires verification against the server CA certificate if you specify the `--ssl-verify-server-cert` option, as in the following example\.

```
mysql -h mysql-database.rds.amazonaws.com -uadmin -ppassword --ssl-ca=/tmp/ssl-cert.pem --ssl-verify-server-cert            
```

## Updating your application trust store<a name="ssl-certificate-rotation-mariadb.updating-trust-store"></a>

For information about updating the trust store for MySQL applications, see [Using TLS/SSL with MariaDB Connector/J](https://mariadb.com/kb/en/library/using-tls-ssl-with-mariadb-java-connector/) in the MariaDB documentation\.

For information about downloading the root certificate, see [Using SSL/TLS to encrypt a connection to a DB instance](UsingWithRDS.SSL.md)\.

For sample scripts that import certificates, see [Sample script for importing certificates into your trust store](UsingWithRDS.SSL-certificate-rotation.md#UsingWithRDS.SSL-certificate-rotation-sample-script)\.

**Note**  
When you update the trust store, you can retain older certificates in addition to adding the new certificates\.

If you are using the MariaDB Connector/J JDBC driver in an application, set the following properties in the application\.

```
System.setProperty("javax.net.ssl.trustStore", certs);
System.setProperty("javax.net.ssl.trustStorePassword", "password");
```

When you start the application, set the following properties\.

```
java -Djavax.net.ssl.trustStore=/path_to_truststore/MyTruststore.jks -Djavax.net.ssl.trustStorePassword=my_truststore_password com.companyName.MyApplication        
```

## Example Java code for establishing SSL connections<a name="ssl-certificate-rotation-mariadb.java-example"></a>

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
After you have determined that your database connections use SSL/TLS and have updated your application trust store, you can update your database to use the rds\-ca\-2019 certificates\. For instructions, see step 3 in [Updating your CA certificate by modifying your DB instance](UsingWithRDS.SSL-certificate-rotation.md#UsingWithRDS.SSL-certificate-rotation-updating)\.