# Encrypting client connections to MySQL DB instances with SSL/TLS<a name="mysql-ssl-connections"></a>

Secure Sockets Layer \(SSL\) is an industry\-standard protocol for securing network connections between client and server\. After SSL version 3\.0, the name was changed to Transport Layer Security \(TLS\)\. Amazon RDS supports SSL/TLS encryption for MySQL DB instances\. Using SSL/TLS, you can encrypt a connection between your application client and your MySQL DB instance\. SSL/TLS support is available in all AWS Regions for MySQL\.

**Topics**
+ [Using SSL/TLS with a MySQL DB instance](#MySQL.Concepts.SSLSupport)
+ [Requiring SSL/TLS for all connections to a MySQL DB instance](#mysql-ssl-connections.require-ssl)
+ [Connecting from the MySQL command\-line client with SSL/TLS \(encrypted\)](#USER_ConnectToInstanceSSL.CLI)

## Using SSL/TLS with a MySQL DB instance<a name="MySQL.Concepts.SSLSupport"></a>

Amazon RDS creates an SSL/TLS certificate and installs the certificate on the DB instance when Amazon RDS provisions the instance\. These certificates are signed by a certificate authority\. The SSL/TLS certificate includes the DB instance endpoint as the Common Name \(CN\) for the SSL/TLS certificate to guard against spoofing attacks\. 

An SSL/TLS certificate created by Amazon RDS is the trusted root entity and should work in most cases but might fail if your application does not accept certificate chains\. If your application does not accept certificate chains, you might need to use an intermediate certificate to connect to your AWS Region\. For example, you must use an intermediate certificate to connect to the AWS GovCloud \(US\) Regions using SSL/TLS\.

For information about downloading certificates, see [Using SSL/TLS to encrypt a connection to a DB instance](UsingWithRDS.SSL.md)\. For more information about using SSL/TLS with MySQL, see [Using new SSL/TLS certificates for MySQL DB instances](ssl-certificate-rotation-mysql.md)\.

MySQL uses OpenSSL for secure connections\. Amazon RDS for MySQL supports Transport Layer Security \(TLS\) versions 1\.0, 1\.1, and 1\.2\. The following table shows the TLS support for MySQL versions\. 


****  

| MySQL version | TLS 1\.0 | TLS 1\.1 | TLS 1\.2 | 
| --- | --- | --- | --- | 
|  MySQL 8\.0  |  Supported for MySQL 8\.0\.27 and lower  |  Supported for MySQL 8\.0\.27 and lower  |  Supported  | 
|  MySQL 5\.7  |  Supported  |  Supported  |  Supported  | 

You can require SSL/TLS connections for specific users accounts\. For example, you can use one of the following statements, depending on your MySQL version, to require SSL/TLS connections on the user account `encrypted_user`\.

To do so, use the following statement\.

```
ALTER USER 'encrypted_user'@'%' REQUIRE SSL;            
```

For more information on SSL/TLS connections with MySQL, see the [ Using encrypted connections](https://dev.mysql.com/doc/refman/8.0/en/encrypted-connections.html) in the MySQL documentation\.

## Requiring SSL/TLS for all connections to a MySQL DB instance<a name="mysql-ssl-connections.require-ssl"></a>

Use the `require_secure_transport` parameter to require that all user connections to your MySQL DB instance use SSL/TLS\. By default, the `require_secure_transport` parameter is set to `OFF`\. You can set the `require_secure_transport` parameter to `ON` to require SSL/TLS for connections to your DB instance\.

You can set the `require_secure_transport` parameter value by updating the DB parameter group for your DB instance\. You don't need to reboot your DB instance for the change to take effect\.

When the `require_secure_transport` parameter is set to `ON` for a DB instance, a database client can connect to it if it can establish an encrypted connection\. Otherwise, an error message similar to the following is returned to the client:

```
MySQL Error 3159 (HY000): Connections using insecure transport are prohibited while --require_secure_transport=ON.
```

For information about setting parameters, see [Modifying parameters in a DB parameter group](USER_WorkingWithDBInstanceParamGroups.md#USER_WorkingWithParamGroups.Modifying)\.

For more information about the `require_secure_transport` parameter, see the [ MySQL documentation](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_require_secure_transport)\.

## Connecting from the MySQL command\-line client with SSL/TLS \(encrypted\)<a name="USER_ConnectToInstanceSSL.CLI"></a>

The `mysql` client program parameters are slightly different if you are using the MySQL 5\.7 version, the MySQL 8\.0 version, or the MariaDB version\.

To find out which version you have, run the `mysql` command with the `--version` option\. In the following example, the output shows that the client program is from MariaDB\.

```
$ mysql --version
mysql  Ver 15.1 Distrib 10.5.15-MariaDB, for osx10.15 (x86_64) using readline 5.1
```

Most Linux distributions, such as Amazon Linux, CentOS, SUSE, and Debian have replaced MySQL with MariaDB, and the `mysql` version in them is from MariaDB\.

To connect to your DB instance using SSL/TLS, follow these steps:

**To connect to a DB instance with SSL/TLS using the MySQL command\-line client**

1. Download a root certificate that works for all AWS Regions\.

   For information about downloading certificates, see [Using SSL/TLS to encrypt a connection to a DB instance](UsingWithRDS.SSL.md)\.

1. Use a MySQL command\-line client to connect to a DB instance with SSL/TLS encryption\. For the `-h` parameter, substitute the DNS name \(endpoint\) for your DB instance\. For the `--ssl-ca` parameter, substitute the SSL/TLS certificate file name\. For the `-P` parameter, substitute the port for your DB instance\. For the `-u` parameter, substitute the user name of a valid database user, such as the master user\. Enter the master user password when prompted\.

   The following example shows how to launch the client using the `--ssl-ca` parameter using the MySQL 5\.7 client or later:

   ```
   mysql -h mysql–instance1.123456789012.us-east-1.rds.amazonaws.com --ssl-ca=global-bundle.pem --ssl-mode=REQUIRED -P 3306 -u myadmin -p
   ```

   To require that the SSL/TLS connection verifies the DB instance endpoint against the endpoint in the SSL/TLS certificate, enter the following command:

   ```
   mysql -h mysql–instance1.123456789012.us-east-1.rds.amazonaws.com --ssl-ca=global-bundle.pem --ssl-mode=VERIFY_IDENTITY -P 3306 -u myadmin -p
   ```

   The following example shows how to launch the client using the `--ssl-ca` parameter using the MariaDB client:

   ```
   mysql -h mysql–instance1.123456789012.us-east-1.rds.amazonaws.com --ssl-ca=global-bundle.pem --ssl -P 3306 -u myadmin -p
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