# Connecting to your DB instance using IAM authentication and the AWS SDK for \.NET<a name="UsingWithRDS.IAMDBAuth.Connecting.NET"></a>

You can connect to an RDS for MariaDB, MySQL, or PostgreSQL DB instance with the AWS SDK for \.NET as described following\.

**Prerequisites**  
The following are prerequisites for connecting to your DB instance using IAM authentication:
+ [Enabling and disabling IAM database authentication](UsingWithRDS.IAMDBAuth.Enabling.md)
+ [Creating and using an IAM policy for IAM database access](UsingWithRDS.IAMDBAuth.IAMPolicy.md)
+ [Creating a database account using IAM authentication](UsingWithRDS.IAMDBAuth.DBAccounts.md)

**Examples**  
The following code examples show how to generate an authentication token, and then use it to connect to a DB instance\.

To run this code example, you need the [AWS SDK for \.NET](http://aws.amazon.com/sdk-for-net/), found on the AWS site\. The `AWSSDK.CORE` and the `AWSSDK.RDS` packages are required\. To connect to a DB instance, use the \.NET database connector for the DB engine, such as MySqlConnector for MariaDB or MySQL, or Npgsql for PostgreSQL\.

This code connects to a MariaDB or MySQL DB instance\.

Modify the values of the following variables as needed:
+ `server` – The endpoint of the DB instance that you want to access
+ `user` – The database account that you want to access
+ `database` – The database that you want to access
+ `port` – The port number used for connecting to your DB instance
+ `SslMode` – The SSL mode to use

  When you use `SslMode=Required`, the SSL connection verifies the DB instance endpoint against the endpoint in the SSL certificate\.
+ `SslCa` – The full path to the SSL certificate for Amazon RDS

  To download a certificate, see [Using SSL/TLS to encrypt a connection to a DB instance](UsingWithRDS.SSL.md)\.

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

      MySqlConnection conn = new MySqlConnection($"server=mysqldb.123456789012.us-east-1.rds.amazonaws.com;user=jane_doe;database=mydB;port=3306;password={pwd};SslMode=Required;SslCa=full_path_to_ssl_certificate");
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

Modify the values of the following variables as needed:
+ `Server` – The endpoint of the DB instance that you want to access
+ `User ID` – The database account that you want to access
+ `Database` – The database that you want to access
+ `Port` – The port number used for connecting to your DB instance
+ `SSL Mode` – The SSL mode to use

  When you use `SSL Mode=Required`, the SSL connection verifies the DB instance endpoint against the endpoint in the SSL certificate\.
+ `Root Certificate` – The full path to the SSL certificate for Amazon RDS

  To download a certificate, see [Using SSL/TLS to encrypt a connection to a DB instance](UsingWithRDS.SSL.md)\.

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
            var pwd = RDSAuthTokenGenerator.GenerateAuthToken("postgresmydb.123456789012.us-east-1.rds.amazonaws.com", 5432, "jane_doe");
// for debug only Console.Write("{0}\n", pwd);  //this verifies the token is generated

            NpgsqlConnection conn = new NpgsqlConnection($"Server=postgresmydb.123456789012.us-east-1.rds.amazonaws.com;User Id=jane_doe;Password={pwd};Database=mydb;SSL Mode=Require;Root Certificate=full_path_to_ssl_certificate");
            conn.Open();

            // Define a query
                   NpgsqlCommand cmd = new NpgsqlCommand("select count(*) FROM pg_user", conn);

            // Execute a query
            NpgsqlDataReader dr = cmd.ExecuteReader();

            // Read all rows and output the first column in each row
            while (dr.Read())
                Console.Write("{0}\n", dr[0]);

            // Close connection
            conn.Close();
        }
    }
}
```