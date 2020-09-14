# Connecting to Your DB Instance Using IAM Authentication and the AWS SDK for Python \(Boto3\)<a name="UsingWithRDS.IAMDBAuth.Connecting.Python"></a>

You can connect from the command line to an Amazon RDS MySQL or PostgreSQL DB instance with the AWS SDK for Python \(Boto3\) as described following\.

The following are prerequisites for connecting to your DB instance using IAM authentication:
+ [Enabling and Disabling IAM Database Authentication](UsingWithRDS.IAMDBAuth.Enabling.md)
+ [Creating and Using an IAM Policy for IAM Database Access](UsingWithRDS.IAMDBAuth.IAMPolicy.md)
+ [Creating a Database Account Using IAM Authentication](UsingWithRDS.IAMDBAuth.DBAccounts.md)

**Topics**
+ [Generating an IAM Authentication Token](#UsingWithRDS.IAMDBAuth.Connecting.Python.AuthToken)
+ [Connecting to a DB Instance](#UsingWithRDS.IAMDBAuth.Connecting.Python.AuthToken.Connect)

## Generating an IAM Authentication Token<a name="UsingWithRDS.IAMDBAuth.Connecting.Python.AuthToken"></a>

You can call the `generate_db_auth_token` method to obtain a signed token\. Provide the DB instance endpoint, port, user name, AWS Region, and DB engine to generate the token for connecting to a DB instance with IAM credentials\.

This code generates an IAM authentication token for a MySQL DB instance\.

```
import sys
import boto3

ENDPOINT="mysqldb.123456789012.us-east-1.rds.amazonaws.com"
PORT="3306"
USR="jane_doe"
REGION="us-east-1"
os.environ['LIBMYSQL_ENABLE_CLEARTEXT_PLUGIN'] = '1'

#gets the credentials from .aws/credentials
session = boto3.Session(profile_name='RDSCreds')
client = boto3.client('rds')

token = client.generate_db_auth_token(DBHostname=ENDPOINT, Port=PORT, DBUsername=USR, Region=REGION)
```

This code generates an IAM authentication token for a PostgreSQL DB instance\.

```
import sys
import boto3

ENDPOINT="postgresmydb.123456789012.us-east-1.rds.amazonaws.com"
PORT="5432"
USR="jane_doe"
REGION="us-east-1"

#gets the credentials from .aws/credentials
session = boto3.Session(profile_name='RDSCreds')
client = boto3.client('rds')

token = client.generate_db_auth_token(DBHostname=ENDPOINT, Port=PORT, DBUsername=USR, Region=REGION)
```

## Connecting to a DB Instance<a name="UsingWithRDS.IAMDBAuth.Connecting.Python.AuthToken.Connect"></a>

The following code example shows how to generate an authentication token, and then use it to connect to a DB instance\. 

To run this code example, you need the [AWS SDK for Python \(Boto3\)](http://aws.amazon.com/sdk-for-python/), found on the AWS site\.

Modify the values of the following variables as needed:
+ `ENDPOINT` – The endpoint of the DB instance that you want to access
+ `PORT` – The port number used for connecting to your DB instance
+ `USER` – The database account that you want to access\.
+ `REGION` – The AWS Region where the DB instance is running
+ `DBNAME` – The DB engine, either  `mysql` or `postgres`

This code connects to a MySQL DB instance\.

```
import mysql.connector
import sys
import boto3
import os

ENDPOINT="mysqldb.123456789012.us-east-1.rds.amazonaws.com"
PORT="3306"
USR="jane_doe"
REGION="us-east-1"
DBNAME="mysql"
os.environ['LIBMYSQL_ENABLE_CLEARTEXT_PLUGIN'] = '1'

#gets the credentials from .aws/credentials
session = boto3.Session(profile_name='default')
client = boto3.client('rds')

token = client.generate_db_auth_token(DBHostname=ENDPOINT, Port=PORT, DBUsername=USR, Region=REGION)

try:
    conn =  mysql.connector.connect(host=ENDPOINT, user=USR, passwd=token, port=PORT, database=DBNAME)
    cur = conn.cursor()
    cur.execute("""SELECT now()""")
    query_results = cur.fetchall()
    print(query_results)
except Exception as e:
    print("Database connection failed due to {}".format(e))
```

This code connects to a PostgreSQL DB instance\.

```
import psycopg2
import sys
import boto3

ENDPOINT="postgresmydb.123456789012.us-east-1.rds.amazonaws.com"
PORT="5432"
USR="jane_doe"
REGION="us-east-1"
DBNAME="postgres"

#gets the credentials from .aws/credentials
session = boto3.Session(profile_name='RDSCreds')
client = boto3.client('rds')

token = client.generate_db_auth_token(DBHostname=ENDPOINT, Port=PORT, DBUsername=USR, Region=REGION)

try:
    conn = psycopg2.connect(host=ENDPOINT, port=PORT, database=DBNAME, user=USR, password=token)
    cur = conn.cursor()
    cur.execute("""SELECT now()""")
    query_results = cur.fetchall()
    print(query_results)
except Exception as e:
    print("Database connection failed due to {}".format(e))
```