# Connecting to your DB instance using IAM authentication from the command line: AWS CLI and mysql client<a name="UsingWithRDS.IAMDBAuth.Connecting.AWSCLI"></a>

You can connect from the command line to an Amazon RDS DB instance with the AWS CLI and `mysql` command line tool as described following\.

**Prerequisites**  
The following are prerequisites for connecting to your DB instance using IAM authentication:
+ [Enabling and disabling IAM database authentication](UsingWithRDS.IAMDBAuth.Enabling.md)
+ [Creating and using an IAM policy for IAM database access](UsingWithRDS.IAMDBAuth.IAMPolicy.md)
+ [Creating a database account using IAM authentication](UsingWithRDS.IAMDBAuth.DBAccounts.md)

**Note**  
For information about connecting to your database using SQL Workbench/J with IAM authentication, see the blog post [Use IAM authentication to connect with SQL Workbench/J to Aurora MySQL or Amazon RDS for MySQL](http://aws.amazon.com/blogs/database/use-iam-authentication-to-connect-with-sql-workbenchj-to-amazon-aurora-mysql-or-amazon-rds-for-mysql/)\.

**Topics**
+ [Generating an IAM authentication token](#UsingWithRDS.IAMDBAuth.Connecting.AWSCLI.AuthToken)
+ [Connecting to a DB instance](#UsingWithRDS.IAMDBAuth.Connecting.AWSCLI.Connect)

## Generating an IAM authentication token<a name="UsingWithRDS.IAMDBAuth.Connecting.AWSCLI.AuthToken"></a>

The following example shows how to get a signed authentication token using the AWS CLI\.

```
aws rds generate-db-auth-token \
   --hostname rdsmysql.123456789012.us-west-2.rds.amazonaws.com \
   --port 3306 \
   --region us-west-2 \
   --username jane_doe
```

In the example, the parameters are as follows:
+ `--hostname` – The host name of the DB instance that you want to access
+ `--port` – The port number used for connecting to your DB instance
+ `--region` – The AWS Region where the DB instance is running
+ `--username` – The database account that you want to access

The first several characters of the token look like the following\.

```
rdsmysql.123456789012.us-west-2.rds.amazonaws.com:3306/?Action=connect&DBUser=jane_doe&X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Expires=900...
```

## Connecting to a DB instance<a name="UsingWithRDS.IAMDBAuth.Connecting.AWSCLI.Connect"></a>

The general format for connecting is shown following\.

```
mysql --host=hostName --port=portNumber --ssl-ca=full_path_to_ssl_certificate --enable-cleartext-plugin --user=userName --password=authToken
```

The parameters are as follows:
+ `--host` – The host name of the DB instance that you want to access
+ `--port` – The port number used for connecting to your DB instance
+ `--ssl-ca` – The full path to the SSL certificate file that contains the public key

  For more information about SSL/TLS support for MariaDB, see [Using SSL/TLS with a MariaDB DB instance](mariadb-ssl-connections.md#MariaDB.Concepts.SSLSupport)\.

  For more information about SSL/TLS support for MySQL, see [Using SSL/TLS with a MySQL DB instance](mysql-ssl-connections.md#MySQL.Concepts.SSLSupport)\.

  To download an SSL certificate, see [Using SSL/TLS to encrypt a connection to a DB instance](UsingWithRDS.SSL.md)\.
+ `--enable-cleartext-plugin` – A value that specifies that `AWSAuthenticationPlugin` must be used for this connection

  If you are using a MariaDB client, the `--enable-cleartext-plugin` option isn't required\.
+ `--user` – The database account that you want to access
+ `--password` – A signed IAM authentication token

The authentication token consists of several hundred characters\. It can be unwieldy on the command line\. One way to work around this is to save the token to an environment variable, and then use that variable when you connect\. The following example shows one way to perform this workaround\. In the example, */sample\_dir/* is the full path to the SSL certificate file that contains the public key\.

```
RDSHOST="mysqldb.123456789012.us-east-1.rds.amazonaws.com"
TOKEN="$(aws rds generate-db-auth-token --hostname $RDSHOST --port 3306 --region us-west-2 --username jane_doe )"

mysql --host=$RDSHOST --port=3306 --ssl-ca=/sample_dir/global-bundle.pem --enable-cleartext-plugin --user=jane_doe --password=$TOKEN
```

When you connect using `AWSAuthenticationPlugin`, the connection is secured using SSL\. To verify this, type the following at the `mysql>` command prompt\.

```
show status like 'Ssl%';
```

The following lines in the output show more details\.

```
+---------------+-------------+
| Variable_name | Value                                                                                                                                                                                                                                |
+---------------+-------------+
| ...           | ...
| Ssl_cipher    | AES256-SHA                                                                                                                                                                                                                           |
| ...           | ...
| Ssl_version   | TLSv1.1                                                                                                                                                                                                                              |
| ...           | ...
+-----------------------------+
```