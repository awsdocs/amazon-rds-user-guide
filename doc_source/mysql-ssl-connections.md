# Encrypting client connections to MySQL DB instances with SSL/TLS<a name="mysql-ssl-connections"></a>

Secure Sockets Layer \(SSL\) is an industry\-standard protocol for securing network connections between client and server\. After SSL version 3\.0, the name was changed to Transport Layer Security \(TLS\)\. Amazon RDS supports SSL/TLS encryption for MySQL DB instances\. Using SSL/TLS, you can encrypt a connection between your application client and your MySQL DB instance\. SSL/TLS support is available in all AWS Regions for MySQL\.

**Topics**
+ [Using SSL/TLS with a MySQL DB instance](#MySQL.Concepts.SSLSupport)
+ [Connecting from the MySQL command\-line client with SSL/TLS \(encrypted\)](#USER_ConnectToInstanceSSL.CLI)

## Using SSL/TLS with a MySQL DB instance<a name="MySQL.Concepts.SSLSupport"></a>

Amazon RDS supports Secure Sockets Layer \(SSL\) and Transport Layer Security \(TLS\) connections with DB instances running the MySQL database engine\. 

Amazon RDS creates an SSL/TLS certificate and installs the certificate on the DB instance when Amazon RDS provisions the instance\. These certificates are signed by a certificate authority\. The SSL/TLS certificate includes the DB instance endpoint as the Common Name \(CN\) for the SSL/TLS certificate to guard against spoofing attacks\. 

An SSL/TLS certificate created by Amazon RDS is the trusted root entity and should work in most cases but might fail if your application does not accept certificate chains\. If your application does not accept certificate chains, you might need to use an intermediate certificate to connect to your AWS Region\. For example, you must use an intermediate certificate to connect to the AWS GovCloud \(US\) Regions using SSL/TLS\.

For information about downloading certificates, see [Using SSL/TLS to encrypt a connection to a DB instance](UsingWithRDS.SSL.md)\. For more information about using SSL/TLS with MySQL, see [Using new SSL/TLS certificates for MySQL DB instances](ssl-certificate-rotation-mysql.md)\.

MySQL uses OpenSSL for secure connections\. Amazon RDS for MySQL supports Transport Layer Security \(TLS\) versions 1\.0, 1\.1, and 1\.2\. The following table shows the TLS support for MySQL versions\. 


****  

| MySQL version | TLS 1\.0 | TLS 1\.1 | TLS 1\.2 | 
| --- | --- | --- | --- | 
|  MySQL 8\.0  |  Supported for MySQL 8\.0\.27 and lower  |  Supported for MySQL 8\.0\.27 and lower  |  Supported  | 
|  MySQL 5\.7  |  Supported  |  Supported  |  Supported  | 

To encrypt connections using the default `mysql` client, launch the mysql client using the `--ssl-ca` parameter to reference the public key, as shown in the examples following\. 

The following example shows how to launch the client using the `--ssl-ca` parameter for MySQL\.

```
mysql -h myinstance.c9akciq32.rds-us-east-1.amazonaws.com
--ssl-ca=[full path]global-bundle.pem --ssl-mode=VERIFY_IDENTITY
```

For information about downloading certificate bundles, see [Using SSL/TLS to encrypt a connection to a DB instance](UsingWithRDS.SSL.md)\.

You can require SSL/TLS connections for specific users accounts\. For example, you can use one of the following statements, depending on your MySQL version, to require SSL/TLS connections on the user account `encrypted_user`\.

To do so, use the following statement\.

```
ALTER USER 'encrypted_user'@'%' REQUIRE SSL;            
```

For more information on SSL/TLS connections with MySQL, see the [ Using encrypted connections](https://dev.mysql.com/doc/refman/8.0/en/encrypted-connections.html) in the MySQL documentation\.

## Connecting from the MySQL command\-line client with SSL/TLS \(encrypted\)<a name="USER_ConnectToInstanceSSL.CLI"></a>

Amazon RDS creates an SSL/TLS certificate for your DB instance when the instance is created\. If you enable SSL/TLS certificate verification, then the SSL/TLS certificate includes the DB instance endpoint as the Common Name \(CN\) for the SSL/TLS certificate to guard against spoofing attacks\. To connect to your DB instance using SSL/TLS, you can use native password authentication or IAM database authentication\. To connect to your DB instance using IAM database authentication, see [IAM database authentication for MariaDB, MySQL, and PostgreSQL](UsingWithRDS.IAMDBAuth.md)\. To connect to your DB instance using native password authentication, you can follow these steps: 

**To connect to a DB instance with SSL/TLS using the MySQL command\-line client**

1. Download a root certificate that works for all AWS Regions\.

   For information about downloading certificates, see [Using SSL/TLS to encrypt a connection to a DB instance](UsingWithRDS.SSL.md)\.

1. Enter the following command at a command prompt to connect to a DB instance with SSL/TLS using the MySQL command\-line client\. For the \-h parameter, substitute the DNS name \(endpoint\) for your DB instance\. For the \-\-ssl\-ca parameter, substitute the SSL/TLS certificate file name as appropriate\. For the \-P parameter, substitute the port for your DB instance\. For the \-u parameter, substitute the user name of a valid database user, such as the master user\. Enter the master user password when prompted\.

   ```
   mysql -h mysql–instance1.123456789012.us-east-1.rds.amazonaws.com --ssl-ca=global-bundle.pem -P 3306 -u mymasteruser -p
   ```

1. You can require that the SSL/TLS connection verifies the DB instance endpoint against the endpoint in the SSL/TLS certificate\. 

   ```
   mysql -h mysql–instance1.123456789012.us-east-1.rds.amazonaws.com --ssl-ca=global-bundle.pem --ssl-mode=VERIFY_IDENTITY -P 3306 -u mymasteruser -p
   ```

1. Enter the master user password when prompted\.

You will see output similar to the following\.

```
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 9738
Server version: 8.0.23 Source distribution

Type 'help;' or '\h' for help. Type '\c' to clear the buffer.

mysql>
```