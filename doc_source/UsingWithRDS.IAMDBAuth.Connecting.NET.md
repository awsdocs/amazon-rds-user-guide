# Connecting to your DB instance using IAM authentication and the AWS SDK for \.NET<a name="UsingWithRDS.IAMDBAuth.Connecting.NET"></a>

You can connect to an RDS for MySQL or PostgreSQL for DB instance with the AWS SDK for \.NET as described following\.

The following are prerequisites for connecting to your DB instance using IAM authentication:
+ [Enabling and disabling IAM database authentication](UsingWithRDS.IAMDBAuth.Enabling.md)
+ [Creating and using an IAM policy for IAM database access](UsingWithRDS.IAMDBAuth.IAMPolicy.md)
+ [Creating a database account using IAM authentication](UsingWithRDS.IAMDBAuth.DBAccounts.md)

The following code example shows how to generate an authentication token, and then use it to connect to a DB instance\. 

To run this code example, you need the [AWS SDK for \.NET](http://aws.amazon.com/sdk-for-net/), found on the AWS site\. The `AWSSDK.CORE` and the `AWSSDK.RDS` packages are required\. To connect to a DB instance, use the \.NET database connector for the DB engine, such as MySqlConnector for MySQL or Npgsql for PostgreSQL\.

Modify the values of the following variables as needed:
+ `server` – The endpoint of the DB instance that you want to access
+ `port` – The port number used for connecting to your DB instance
+ `user` – The database account that you want to access\.

This code connects to a MySQL DB instance\.

```
using System;
using System.Data;
using MySql.Data;
using MySql.Data.MySqlClient;
using Amazon;

namespace ubuntu
{
  class Program
  {
    static void Main(string[] args)
    {
      var pwd = Amazon.RDS.Util.RDSAuthTokenGenerator.GenerateAuthToken(RegionEndpoint.USEast1, "mysqldb.123456789012.us-east-1.rds.amazonaws.com", 3306, "jane_doe");
      // for debug only Console.Write("{0}\n", pwd);  //this verifies the token is generated

      MySqlConnection conn = new MySqlConnection($"server=mysqldb.123456789012.us-east-1.rds.amazonaws.com;user=jane_doe;database=mydB;port=3306;password={pwd};SslMode=Required;SslCa=../rds-ca-2019-root.pem");
      conn.Open();

      // Define a query
      MySqlCommand sampleCommand = new MySqlCommand("SHOW DATABASES;", conn);

      // Execute a query
      MySqlDataReader mysqlDataRdr = sampleCommand.ExecuteReader();

      // Read all rows and output the first column in each row
      while (mysqlDataRdr.Read())
        Console.WriteLine(mysqlDataRdr[0]);

      mysqlDataRdr.Close();
      // Close connection
      conn.Close();
    }
  }
}
```

This code connects to a PostgreSQL DB instance\.

```
using System;
using Npgsql;
using Amazon.RDS.Util;

namespace ConsoleApp1
{
    class Program
    {
        static void Main(string[] args)
        {
            using NpgsqlConnection conn = new NpgsqlConnection("Server=postgresqldb.123456789012.us-east-1.rds.amazonaws.com;User Id=jane_doe;Database=mydb;SSL Mode=Require;Trust Server Certificate=true;");

            // Add a callback to provide the password
            conn.ProvidePasswordCallback = (host, port, _, username) =>
                RDSAuthTokenGenerator.GenerateAuthToken(host, port, username);

            conn.Open();

            // Define a query
            using var cmd = new NpgsqlCommand("select count(*) FROM pg_user", conn);

            // Execute a query
            using var dr = cmd.ExecuteReader();

            // Read all rows and output the first column in each row
            while (dr.Read())
                Console.Write("{0}\n", dr[0]);
        }
    }
}
```