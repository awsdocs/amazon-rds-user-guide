# Encrypting client connections to MariaDB DB instances with SSL/TLS<a name="mariadb-ssl-connections"></a>

Secure Sockets Layer \(SSL\) is an industry\-standard protocol for securing network connections between client and server\. After SSL version 3\.0, the name was changed to Transport Layer Security \(TLS\)\. Amazon RDS supports SSL/TLS encryption for MariaDB DB instances\. Using SSL/TLS, you can encrypt a connection between your application client and your MariaDB DB instance\. SSL/TLS support is available in all AWS Regions\.

**Topics**
+ [Using SSL/TLS with a MariaDB DB instance](#MariaDB.Concepts.SSLSupport)
+ [Connecting from the MySQL command\-line client with SSL/TLS \(encrypted\)](#USER_ConnectToMariaDBInstanceSSL.CLI)

## Using SSL/TLS with a MariaDB DB instance<a name="MariaDB.Concepts.SSLSupport"></a>

Amazon RDS supports Secure Sockets Layer \(SSL\) and Transport Layer Security \(TLS\) connections with DB instances running the MariaDB database engine\. MariaDB uses OpenSSL for secure connections\.

Amazon RDS creates an SSL/TLS certificate and installs the certificate on the DB instance when Amazon RDS provisions the instance\. These certificates are signed by a certificate authority\. The SSL/TLS certificate includes the DB instance endpoint as the Common Name \(CN\) for the SSL/TLS certificate to guard against spoofing attacks\. 

For information about downloading certificates, see [Using SSL/TLS to encrypt a connection to a DB instance](UsingWithRDS.SSL.md)\.

Amazon RDS for MariaDB supports Transport Layer Security \(TLS\) versions 1\.0, 1\.1, 1\.2, and 1\.3\. The following table shows the TLS support for MySQL versions\. 


****  

| MariaDB version | TLS 1\.0 | TLS 1\.1 | TLS 1\.2 | TLS 1\.3 | 
| --- | --- | --- | --- | --- | 
|  MariaDB 10\.6  |  Supported  |  Supported  |  Supported  |  Supported  | 
|  MariaDB 10\.5  |  Supported  |  Supported  |  Supported  |  Supported  | 
|  MariaDB 10\.4  |  Supported  |  Supported  |  Supported  |  Supported  | 
|  MariaDB 10\.3  |  Supported  |  Supported  |  Supported  |  Supported  | 
|  MariaDB 10\.2  |  Supported  |  Supported  |  Supported  |  Supported  | 

**Important**  
The `mysql` client program parameters are slightly different if you are using the MySQL 5\.7 version, the MySQL 8\.0 version, or the MariaDB version\.  
To find out which version you have, run the `mysql` command with the `--version` option\. In the following example, the output shows that the client program is from MariaDB\.  

```
$ mysql --version
mysql  Ver 15.1 Distrib 10.5.15-MariaDB, for osx10.15 (x86_64) using readline 5.1
```
Most Linux distributions, such as Amazon Linux, CentOS, SUSE, and Debian have replaced MySQL with MariaDB, and the `mysql` version in them is from MariaDB\.

To encrypt connections using the default mysql client, launch the mysql client using the `--ssl-ca` parameter to reference the public key, as shown in the examples following\.

The following example shows how to launch the client using the `--ssl-ca` parameter using the MySQL 5\.7 client or later:

```
mysql -h myinstance.123456789012.rds-us-east-1.amazonaws.com
--ssl-ca=[full path]global-bundle.pem --ssl-mode=REQUIRED
```

The following example shows how to launch the client using the `--ssl-ca` parameter using the MariaDB client:

```
mysql -h myinstance.123456789012.rds-us-east-1.amazonaws.com
--ssl-ca=[full path]global-bundle.pem --ssl
```

For information about downloading certificate bundles, see [Using SSL/TLS to encrypt a connection to a DB instance](UsingWithRDS.SSL.md)\.

You can require SSL/TLS connections for specific users accounts\. For example, you can use one of the following statements, depending on your MariaDB version, to require SSL/TLS connections on the user account `encrypted_user`\.

Use the following statement\.

```
ALTER USER 'encrypted_user'@'%' REQUIRE SSL;            
```

For more information on SSL/TLS connections with MariaDB, see [SSL overview](http://mariadb.com/kb/en/mariadb/ssl-connections/) in the MariaDB documentation\. 

## Connecting from the MySQL command\-line client with SSL/TLS \(encrypted\)<a name="USER_ConnectToMariaDBInstanceSSL.CLI"></a>

Amazon RDS creates an SSL/TLS certificate for your DB instance when the instance is created\. If you enable SSL/TLS certificate verification, then the SSL/TLS certificate includes the DB instance endpoint as the Common Name \(CN\) for the SSL/TLS certificate to guard against spoofing attacks\. To connect to your DB instance using SSL/TLS, follow these steps:

**To connect to a DB instance with SSL/TLS using the MySQL command\-line client**

1. Download a root certificate that works for all AWS Regions\.

   For information about downloading certificates, see [Using SSL/TLS to encrypt a connection to a DB instance](UsingWithRDS.SSL.md)\.

1. Enter the following command at a command prompt to connect to a DB instance with SSL/TLS using the `mysql` utility\. For the `-h` parameter, substitute the DNS name for your DB instance\. For the `--ssl-ca` parameter, substitute the SSL/TLS certificate file name as appropriate\.

   ```
   mysql -h mariadb-instance1.123456789012.us-east-1.rds.amazonaws.com --ssl-ca=global-bundle.pem -p
   ```

1. Include the `--ssl-verify-server-cert` parameter so that the SSL/TLS connection verifies the DB instance endpoint against the endpoint in the SSL/TLS certificate\. For example:

   ```
   mysql -h mariadb-instance1.123456789012.us-east-1.rds.amazonaws.com --ssl-ca=global-bundle.pem --ssl-verify-server-cert -p
   ```

1. Enter the master user password when prompted\.

You should see output similar to the following\.

```
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 31
Server version: 10.5.15-MariaDB-log Source distribution
 
Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.
  
Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]>
```