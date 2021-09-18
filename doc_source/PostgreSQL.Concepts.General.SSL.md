# Using SSL with a PostgreSQL DB instance<a name="PostgreSQL.Concepts.General.SSL"></a>

Amazon RDS supports Secure Socket Layer \(SSL\) encryption for PostgreSQL DB instances\. Using SSL, you can encrypt a PostgreSQL connection between your applications and your PostgreSQL DB instances\. You can also force all connections to your PostgreSQL DB instance to use SSL\. Amazon RDS for PostgreSQL supports Transport Layer Security \(TLS\) versions 1\.1 and 1\.2\.

For general information about SSL support and PostgreSQL databases, see [SSL support](https://www.postgresql.org/docs/11/libpq-ssl.html) in the PostgreSQL documentation\. For information about using an SSL connection over JDBC, see [Configuring the client](https://jdbc.postgresql.org/documentation/head/ssl-client.html) in the PostgreSQL documentation\.

SSL support is available in all AWS Regions for PostgreSQL\. Amazon RDS creates an SSL certificate for your PostgreSQL DB instance when the instance is created\. If you enable SSL certificate verification, then the SSL certificate includes the DB instance endpoint as the Common Name \(CN\) for the SSL certificate to guard against spoofing attacks\. 

**Topics**
+ [Connecting to a PostgreSQL DB instance over SSL](#PostgreSQL.Concepts.General.SSL.Connecting)
+ [Requiring an SSL connection to a PostgreSQL DB instance](#PostgreSQL.Concepts.General.SSL.Requiring)
+ [Determining the SSL connection status](#PostgreSQL.Concepts.General.SSL.Status)
+ [SSL cipher suites in RDS for PostgreSQL](#PostgreSQL.Concepts.General.SSL.Ciphers)

## Connecting to a PostgreSQL DB instance over SSL<a name="PostgreSQL.Concepts.General.SSL.Connecting"></a>

**To connect to a PostgreSQL DB instance over SSL**

1. Download the certificate\.

   For information about downloading certificates, see [Using SSL/TLS to encrypt a connection to a DB instance](UsingWithRDS.SSL.md)\.

1. Import the certificate into your operating system\.

   For sample scripts that import certificates, see [Sample script for importing certificates into your trust store](UsingWithRDS.SSL-certificate-rotation.md#UsingWithRDS.SSL-certificate-rotation-sample-script)\.

1. Connect to your PostgreSQL DB instance over SSL\.

   When you connect using SSL, your client can choose whether to verify the certificate chain\. If your connection parameters specify `sslmode=verify-ca` or `sslmode=verify-full`, then your client requires the RDS CA certificates to be in their trust store or referenced in the connection URL\. This requirement is to verify the certificate chain that signs your database certificate\.

   When a client, such as psql or JDBC, is configured with SSL support, the client first tries to connect to the database with SSL by default\. If the client can't connect with SSL, it reverts to connecting without SSL\. The default `sslmode` mode used is different between libpq\-based clients \(such as psql\) and JDBC\. The libpq\-based clients default to `prefer`, and JDBC clients default to `verify-full`\.

   Use the `sslrootcert` parameter to reference the certificate, for example `sslrootcert=rds-ssl-ca-cert.pem`\.

The following is an example of using psql to connect to a PostgreSQL DB instance\.

```
$ psql -h testpg.cdhmuqifdpib.us-east-1.rds.amazonaws.com -p 5432 \
    "dbname=testpg user=testuser sslrootcert=rds-ca-2019-root.pem sslmode=verify-full"
```

## Requiring an SSL connection to a PostgreSQL DB instance<a name="PostgreSQL.Concepts.General.SSL.Requiring"></a>

You can require that connections to your PostgreSQL DB instance use SSL by using the `rds.force_ssl` parameter\. By default, the `rds.force_ssl` parameter is set to 0 \(off\)\. You can set the `rds.force_ssl` parameter to 1 \(on\) to require SSL for connections to your DB instance\. Updating the `rds.force_ssl` parameter also sets the PostgreSQL `ssl` parameter to 1 \(on\) and modifies your DB instance's `pg_hba.conf` file to support the new SSL configuration\.

You can set the `rds.force_ssl` parameter value by updating the parameter group for your DB instance\. If the parameter group for your DB instance isn't the default one, and the `ssl` parameter is already set to 1 when you set `rds.force_ssl` to 1, you don't need to reboot your DB instance\. Otherwise, you must reboot your DB instance for the change to take effect\. For more information on parameter groups, see [Working with DB parameter groups](USER_WorkingWithParamGroups.md)\.

When the `rds.force_ssl` parameter is set to 1 for a DB instance, you see output similar to the following when you connect, indicating that SSL is now required:

```
$ psql postgres -h SOMEHOST.amazonaws.com -p 8192 -U someuser
. . .
SSL connection (cipher: DHE-RSA-AES256-SHA, bits: 256)
Type "help" for help.

postgres=>
```

## Determining the SSL connection status<a name="PostgreSQL.Concepts.General.SSL.Status"></a>

The encrypted status of your connection is shown in the logon banner when you connect to the DB instance:

```
Password for user master: 
psql (10.3) 
SSL connection (cipher: DHE-RSA-AES256-SHA, bits: 256) 
Type "help" for help.   

postgres=>
```

You can also load the `sslinfo` extension and then call the `ssl_is_used()` function to determine if SSL is being used\. The function returns `t` if the connection is using SSL, otherwise it returns `f`\.

```
postgres=> create extension sslinfo;
CREATE EXTENSION

postgres=> select ssl_is_used();
 ssl_is_used
---------
t
(1 row)
```

You can use the `select ssl_cipher()` command to determine the SSL cipher:

```
postgres=> select ssl_cipher();
ssl_cipher
--------------------
DHE-RSA-AES256-SHA
(1 row)
```

 If you enable `set rds.force_ssl` and restart your instance, non\-SSL connections are refused with the following message:

```
$ export PGSSLMODE=disable
$ psql postgres -h SOMEHOST.amazonaws.com -p 8192 -U someuser
psql: FATAL: no pg_hba.conf entry for host "host.ip", user "someuser", database "postgres", SSL off
$
```

For information about the `sslmode` option, see [Database connection control functions](https://www.postgresql.org/docs/11/libpq-connect.html#LIBPQ-CONNECT-SSLMODE) in the PostgreSQL documentation\.

## SSL cipher suites in RDS for PostgreSQL<a name="PostgreSQL.Concepts.General.SSL.Ciphers"></a>

The PostgreSQL configuration parameter [ssl\_ciphers](https://www.postgresql.org/docs/current/runtime-config-connection.html#RUNTIME-CONFIG-CONNECTION-SSL) specifies the categories of cipher suites that are allowed for SSL connections\. The following table lists the default cipher suites used in RDS for PostgreSQL\.


| PostgreSQL engine version | Cipher suites | 
| --- | --- | 
| 13 | HIGH:\!aNULL:\!3DES | 
| 12 | HIGH:\!aNULL:\!3DES | 
| 11\.4 and higher minor versions | HIGH:MEDIUM:\+3DES:\!aNULL:\!RC4 | 
| 11\.1, 11\.2 | HIGH:MEDIUM:\+3DES:\!aNULL | 
| 10\.9 and higher minor versions | HIGH:MEDIUM:\+3DES:\!aNULL:\!RC4 | 
| 10\.7 and lower minor versions | HIGH:MEDIUM:\+3DES:\!aNULL | 
| 9\.6\.14 and higher minor versions | HIGH:MEDIUM:\+3DES:\!aNULL:\!RC4 | 
| 9\.6\.12 and lower minor versions | HIGH:MEDIUM:\+3DES:\!aNULL | 