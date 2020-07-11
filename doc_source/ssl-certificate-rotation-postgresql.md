# Updating Applications to Connect to PostgreSQL DB Instances Using New SSL/TLS Certificates<a name="ssl-certificate-rotation-postgresql"></a>

As of September 19, 2019, Amazon RDS has published new Certificate Authority \(CA\) certificates for connecting to your RDS DB instances using Secure Socket Layer or Transport Layer Security \(SSL/TLS\)\. Following, you can find information about updating your applications to use the new certificates\.

This topic can help you to determine whether any client applications use SSL/TLS to connect to your DB instances\. If they do, you can further check whether those applications require certificate verification to connect\. 

**Note**  
Some applications are configured to connect to PostgreSQL DB instances only if they can successfully verify the certificate on the server\.   
For such applications, you must update your client application trust stores to include the new CA certificates\. 

After you update your CA certificates in the client application trust stores, you can rotate the certificates on your DB instances\. We strongly recommend testing these procedures in a development or staging environment before implementing them in your production environments\.

For more information about certificate rotation, see [Rotating Your SSL/TLS Certificate](UsingWithRDS.SSL-certificate-rotation.md)\. For more information about downloading certificates, see [Using SSL/TLS to Encrypt a Connection to a DB Instance](UsingWithRDS.SSL.md)\. For information about using SSL/TLS with PostgreSQL DB instances, see [Using SSL with a PostgreSQL DB Instance](CHAP_PostgreSQL.md#PostgreSQL.Concepts.General.SSL)\.

**Topics**
+ [Determining Whether Applications Are Connecting to PostgreSQL DB Instances Using SSL](#ssl-certificate-rotation-postgresql.determining-server)
+ [Determining Whether a Client Requires Certificate Verification in Order to Connect](#ssl-certificate-rotation-postgresql.determining-client)
+ [Updating Your Application Trust Store](#ssl-certificate-rotation-postgresql.updating-trust-store)
+ [Using SSL/TLS Connections for Different Types of Applications](#ssl-certificate-rotation-postgresql.applications)

## Determining Whether Applications Are Connecting to PostgreSQL DB Instances Using SSL<a name="ssl-certificate-rotation-postgresql.determining-server"></a>

Check the DB instance configuration for the value of the `rds.force_ssl` parameter\. By default, the `rds.force_ssl` parameter is set to `0` \(off\)\. If the `rds.force_ssl` parameter is set to `1` \(on\), clients are required to use SSL/TLS for connections\. For more information about parameter groups, see [Working with DB Parameter Groups](USER_WorkingWithParamGroups.md)\.

If you are using RDS PostgreSQL version 9\.5 or later major version and `rds.force_ssl` is not set to `1` \(on\), query the `pg_stat_ssl` view to check connections using SSL\. For example, the following query returns only SSL connections and information about the clients using SSL\.

```
select datname, usename, ssl, client_addr from pg_stat_ssl inner join pg_stat_activity on pg_stat_ssl.pid = pg_stat_activity.pid where ssl is true and usename<>'rdsadmin';                
```

Only rows using SSL/TLS connections are displayed with information about the connection\. The following is sample output\.

```
 datname  | usename | ssl | client_addr 
----------+---------+-----+-------------
 benchdb  | pgadmin | t   | 53.95.6.13
 postgres | pgadmin | t   | 53.95.6.13
(2 rows)
```

This query displays only the current connections at the time of the query\. The absence of results doesn't indicate that no applications are using SSL connections\. Other SSL connections might be established at a different time\.

## Determining Whether a Client Requires Certificate Verification in Order to Connect<a name="ssl-certificate-rotation-postgresql.determining-client"></a>

When a client, such as psql or JDBC, is configured with SSL support, the client first tries to connect to the database with SSL by default\. If the client can't connect with SSL, it reverts to connecting without SSL\. The default `sslmode` mode used is different between libpq\-based clients \(such as psql\) and JDBC\. The libpq\-based clients default to `prefer`, where JDBC clients default to `verify-full`\. The certificate on the server is verified only when `sslrootcert` is provided with `sslmode` set to `require`, `verify-ca`, or `verify-full`\. An error is thrown if the certificate is invalid\.

Use `PGSSLROOTCERT` to verify the certificate with the `PGSSLMODE` environment variable, with `PGSSLMODE` set to `require`, `verify-ca`, or `verify-full`\.

```
PGSSLMODE=require PGSSLROOTCERT=/fullpath/rds-ca-2019-root.pem psql -h pgdbidentifier.cxxxxxxxx.us-east-2.rds.amazonaws.com -U masteruser -d postgres                
```

Use the `sslrootcert` argument to verify the certificate with `sslmode` in connection string format, with `sslmode` set to `require`, `verify-ca`, or `verify-full` to verify the certificate\.

```
psql "host=pgdbidentifier.cxxxxxxxx.us-east-2.rds.amazonaws.com sslmode=require sslrootcert=/full/path/rds-ca-2019-root.pem user=masteruser dbname=postgres"                
```

For example, in the preceding case, if you are using an invalid root certificate, then you see an error similar to the following on your client\.

```
psql: SSL error: certificate verify failed               
```

## Updating Your Application Trust Store<a name="ssl-certificate-rotation-postgresql.updating-trust-store"></a>

For information about updating the trust store for PostgreSQL applications, see [Secure TCP/IP Connections with SSL](https://www.postgresql.org/docs/9.5/ssl-tcp.html) in the PostgreSQL documentation\.

**Note**  
When you update the trust store, you can retain older certificates in addition to adding the new certificates\.

### Updating Your Application Trust Store for JDBC<a name="ssl-certificate-rotation-postgresql.updating-trust-store.jdbc"></a>

You can update the trust store for applications that use JDBC for SSL/TLS connections\.

**To update the trust store for JDBC applications**

1. Download the 2019 root certificate that works for all AWS Regions and put the file in your trust store directory\.

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

   Your output should contain the following:

   ```
   rds-root,date, trustedCertEntry, 
   Certificate fingerprint (SHA1): D4:0D:DB:29:E3:75:0D:FF:A6:71:C3:14:0B:BF:5F:47:8D:1C:80:96
   # This fingerprint should match the output from the below command
   openssl x509 -fingerprint -in rds-ca-2019-root.pem -noout
   ```

## Using SSL/TLS Connections for Different Types of Applications<a name="ssl-certificate-rotation-postgresql.applications"></a>

The following provides information about using SSL/TLS connections for different types of applications:
+ **psql**

  The client is invoked from the command line by specifying options either as a connection string or as environment variables\. For SSL/TLS connections, the relevant options are `sslmode` \(environment variable `PGSSLMODE`\), `sslrootcert` \(environment variable `PGSSLROOTCERT`\)\.

  For the complete list of options, see [Parameter Key Words](https://www.postgresql.org/docs/11/libpq-connect.html#LIBPQ-PARAMKEYWORDS) in the PostgreSQL documentation\. For the complete list of environment variables, see [Environment Variables](https://www.postgresql.org/docs/11/libpq-envars.html) in the PostgreSQL documentation\.
+ **pgAdmin**

  This browser\-based client is a more user\-friendly interface for connecting to a PostgreSQL database\.

  For information about configuring connections, see the [pgAdmin documentation](https://www.pgadmin.org/docs/pgadmin4/latest/server_dialog.html)\.
+ **JDBC**

  JDBC enables database connections with Java applications\.

  For general information about connecting to a PostgreSQL database with JDBC, see [Connecting to the Database](https://jdbc.postgresql.org/documentation/head/connect.html) in the PostgreSQL documentation\. For information about connecting with SSL/TLS, see [Configuring the Client](https://jdbc.postgresql.org/documentation/head/ssl-client.html) in the PostgreSQL documentation\. 
+ **Python**

  A popular Python library for connecting to PostgreSQL databases is `psycopg2`\.

  For information about using `psycopg2`, see the [psycopg2 documentation](https://pypi.org/project/psycopg2/)\. For a short tutorial on how to connect to a PostgreSQL database, see [Psycopg2 Tutorial](https://wiki.postgresql.org/wiki/Psycopg2_Tutorial)\. You can find information about the options the connect command accepts in [The psycopg2 module content](http://initd.org/psycopg/docs/module.html#module-psycopg2)\.

**Important**  
After you have determined that your database connections use SSL/TLS and have updated your application trust store, you can update your database to use the rds\-ca\-2019 certificates\. For instructions, see step 3 in [Updating Your CA Certificate by Modifying Your DB Instance](UsingWithRDS.SSL-certificate-rotation.md#UsingWithRDS.SSL-certificate-rotation-updating)\.