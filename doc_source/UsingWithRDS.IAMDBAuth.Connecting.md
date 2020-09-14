# Connecting to Your DB Instance Using IAM Authentication<a name="UsingWithRDS.IAMDBAuth.Connecting"></a>

With IAM database authentication, you use an authentication token when you connect to your DB instance\. An *authentication token* is a string of characters that you use instead of a password\. After you generate an authentication token, it's valid for 15 minutes before it expires\. If you try to connect using an expired token, the connection request is denied\.

Every authentication token must be accompanied by a valid signature, using AWS signature version 4\. \(For more information, see [Signature Version 4 Signing Process](https://docs.aws.amazon.com/general/latest/gr/signature-version-4.html) in the* AWS General Reference\.*\) The AWS CLI and an AWS SDK, such as the AWS SDK for Java or AWS SDK for Python \(Boto3\), can automatically sign each token you create\.

You can use an authentication token when you connect to Amazon RDS from another AWS service, such as AWS Lambda\. By using a token, you can avoid placing a password in your code\. Alternatively, you can use an AWS SDK to programmatically create and programmatically sign an authentication token\.

After you have a signed IAM authentication token, you can connect to an Amazon RDS DB instance\. Following, you can find out how to do this using either a command line tool or an AWS SDK, such as the AWS SDK for Java or AWS SDK for Python \(Boto3\)\.

For more information, see the following blog posts:
+ [Use IAM authentication to connect with SQL Workbench/J to Amazon Aurora MySQL or Amazon RDS for MySQL](http://aws.amazon.com/blogs/database/use-iam-authentication-to-connect-with-sql-workbenchj-to-amazon-aurora-mysql-or-amazon-rds-for-mysql/)
+ [Using IAM authentication to connect with pgAdmin Amazon Aurora PostgreSQL or Amazon RDS for PostgreSQL](http://aws.amazon.com/blogs/database/using-iam-authentication-to-connect-with-pgadmin-amazon-aurora-postgresql-or-amazon-rds-for-postgresql/)

The following are prerequisites for connecting to your DB instance using IAM authentication:
+ [Enabling and Disabling IAM Database Authentication](UsingWithRDS.IAMDBAuth.Enabling.md)
+ [Creating and Using an IAM Policy for IAM Database Access](UsingWithRDS.IAMDBAuth.IAMPolicy.md)
+ [Creating a Database Account Using IAM Authentication](UsingWithRDS.IAMDBAuth.DBAccounts.md)

**Topics**
+ [Connecting to Your DB Instance Using IAM Authentication from the Command Line: AWS CLI and mysql Client](UsingWithRDS.IAMDBAuth.Connecting.AWSCLI.md)
+ [Connecting to Your DB Instance Using IAM Authentication from the Command Line: AWS CLI and psql Client](UsingWithRDS.IAMDBAuth.Connecting.AWSCLI.PostgreSQL.md)
+ [Connecting to Your DB Instance Using IAM Authentication and the AWS SDK for Python \(Boto3\)](UsingWithRDS.IAMDBAuth.Connecting.Python.md)
+ [Connecting to Your DB Instance Using IAM Authentication and the AWS SDK for \.NET](UsingWithRDS.IAMDBAuth.Connecting.NET.md)
+ [Connecting to Your DB Instance Using IAM Authentication and the AWS SDK for Java](UsingWithRDS.IAMDBAuth.Connecting.Java.md)