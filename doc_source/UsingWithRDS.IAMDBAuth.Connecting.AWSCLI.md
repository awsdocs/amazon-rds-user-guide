# Connecting to Your DB Instance Using IAM Authentication from the Command Line: AWS CLI and mysql Client<a name="UsingWithRDS.IAMDBAuth.Connecting.AWSCLI"></a>

You can connect from the command line to an Amazon RDS DB instance with the AWS CLI and `mysql` command line tool as described following\.

The following are prerequisites for connecting to your DB instance using IAM authentication:
+ [Enabling and Disabling IAM Database Authentication](UsingWithRDS.IAMDBAuth.Enabling.md)
+ [Creating and Using an IAM Policy for IAM Database Access](UsingWithRDS.IAMDBAuth.IAMPolicy.md)
+ [Creating a Database Account Using IAM Authentication](UsingWithRDS.IAMDBAuth.DBAccounts.md)

**Topics**
+ [Generating an IAM Authentication Token](#UsingWithRDS.IAMDBAuth.Connecting.AWSCLI.AuthToken)
+ [Connecting to a DB Instance](#UsingWithRDS.IAMDBAuth.Connecting.AWSCLI.Connect)

## Generating an IAM Authentication Token<a name="UsingWithRDS.IAMDBAuth.Connecting.AWSCLI.AuthToken"></a>

The following example shows how to get a signed authentication token using the AWS CLI\.

```
aws rds generate-db-auth-token \
   --hostname rdsmysql.123456789012.us-west-2.rds.amazonaws.com \
   --port 3306 \
   --region us-west-2 \
   --username jane_doe
```

In the example, the parameters are as follows:
+ `--hostname` – The host name of the DB instance that you want to access\.
+ `--port` – The port number used for connecting to your DB instance\.
+ `--region` – The AWS Region where the DB instance is running\. 
+ `--username` – The database account that you want to access\.

The first several characters of the token look like the following\.

```
rdsmysql.123456789012.us-west-2.rds.amazonaws.com:3306/?Action=connect&DBUser=jane_doe&X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Expires=900...
```

## Connecting to a DB Instance<a name="UsingWithRDS.IAMDBAuth.Connecting.AWSCLI.Connect"></a>

The general format for connecting is shown following\.

```
mysql --host=hostName --port=portNumber --ssl-ca=[full path]rds-combined-ca-bundle.pem --enable-cleartext-plugin --user=userName --password=authToken
```

The parameters are as follows:
+ `--host` – The host name of the DB instance that you want to access\.
+ `--port` – The port number used for connecting to your DB instance\.
+ `--ssl-ca` – The SSL certificate file that contains the public key\. For more information, see [Using SSL/TLS to Encrypt a Connection to a DB Instance](UsingWithRDS.SSL.md)\.
+ `--enable-cleartext-plugin` – A value that specifies that `AWSAuthenticationPlugin` must be used for this connection\.
+ `--user` – The database account that you want to access\.
+ `--password` – A signed IAM authentication token\.

The authentication token consists of several hundred characters\. It can be unwieldy on the command line\. One way to work around this is to save the token to an environment variable, and then use that variable when you connect\. The following example shows one way to perform this workaround\.

```
RDSHOST="rdsmysql.123456789012.us-west-2.rds.amazonaws.com"
TOKEN="$(aws rds generate-db-auth-token --hostname $RDSHOST --port 3306 --region us-west-2 --username jane_doe )"

mysql --host=$RDSHOST --port=3306 --ssl-ca=/sample_dir/rds-combined-ca-bundle.pem --enable-cleartext-plugin --user=jane_doe --password=$TOKEN
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