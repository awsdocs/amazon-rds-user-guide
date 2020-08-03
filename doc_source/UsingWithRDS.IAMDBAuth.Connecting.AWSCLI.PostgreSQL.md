# Connecting to Your DB Instance Using IAM Authentication from the Command Line: AWS CLI and psql Client<a name="UsingWithRDS.IAMDBAuth.Connecting.AWSCLI.PostgreSQL"></a>

You can connect from the command line to an Amazon RDS for PostgreSQL DB instance with the AWS CLI and psql command line tool as described following\.

The following are prerequisites for connecting to your DB instance using IAM authentication:
+ [Enabling and Disabling IAM Database Authentication](UsingWithRDS.IAMDBAuth.Enabling.md)
+ [Creating and Using an IAM Policy for IAM Database Access](UsingWithRDS.IAMDBAuth.IAMPolicy.md)
+ [Creating a Database Account Using IAM Authentication](UsingWithRDS.IAMDBAuth.DBAccounts.md)

**Topics**
+ [Generating an IAM Authentication Token](#UsingWithRDS.IAMDBAuth.Connecting.AWSCLI.AuthToken.PostgreSQL)
+ [Connecting to an Amazon RDS PostgreSQL Instance](#UsingWithRDS.IAMDBAuth.Connecting.AWSCLI.Connect.PostgreSQL)

## Generating an IAM Authentication Token<a name="UsingWithRDS.IAMDBAuth.Connecting.AWSCLI.AuthToken.PostgreSQL"></a>

The authentication token consists of several hundred characters so it can be unwieldy on the command line\. One way to work around this is to save the token to an environment variable, and then use that variable when you connect\. The following example shows how to use the AWS CLI to get a signed authentication token using the `generated-db-auth-token` command, and store it in a `PGPASSWORD` environment variable\.

```
export RDSHOST="rdspostgres.123456789012.us-west-2.rds.amazonaws.com"
export PGPASSWORD="$(aws rds generate-db-auth-token --hostname $RDSHOST --port 5432 --region us-west-2 --username jane_doe )"
```

In the example, the parameters to the `generate-db-auth-token` command are as follows:
+ `--hostname` – The host name of the DB instance that you want to access\.
+ `--port` – The port number used for connecting to your DB instance\.
+ `--region` – The AWS Region where the DB instance is running\. 
+ `--username` – The database account that you want to access\.

The first several characters of the generated token look like the following\.

```
rdspostgres.123456789012.us-west-2.rds.amazonaws.com:5432/?Action=connect&DBUser=jane_doe&X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Expires=900...
```

## Connecting to an Amazon RDS PostgreSQL Instance<a name="UsingWithRDS.IAMDBAuth.Connecting.AWSCLI.Connect.PostgreSQL"></a>

The general format for using psql to connect is shown following\.

```
psql "host=hostName port=portNumber sslmode=verify-full sslrootcert=certificateFile dbname=DBName user=userName password=authToken"
```

The parameters are as follows:
+ `host` – The host name of the DB instance that you want to access\.
+ `port` – The port number used for connecting to your DB instance\.
+ `sslmode` – The SSL mode to use\. When you use `sslmode=verify-full`, the SSL connection verifies the DB instance endpoint against the endpoint in the SSL certificate\.
+ `sslrootcert` – The SSL certificate file that contains the public key\. For more information, see [ Using SSL with a PostgreSQL DB Instance](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_PostgreSQL.html#PostgreSQL.Concepts.General.SSL)\. 
+ `dbname` – The database that you want to access\.
+ `user` – The database account that you want to access\.
+ `password` – A signed IAM authentication token\.

The following example shows using psql to connect\. In the example psql uses the environment variable `PGPASSWORD` that was set when the token was generated in the previous section\.

```
psql "host=$RDSHOST port=5432 sslmode=verify-full sslrootcert=/sample_dir/rds-combined-ca-bundle.pem dbname=DBName user=jane_doe password=$PGPASSWORD"
```