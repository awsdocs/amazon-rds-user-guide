# Connecting to Your DB Instance Using IAM Authentication<a name="UsingWithRDS.IAMDBAuth.Connecting"></a>

With IAM database authentication, you use an authentication token when you connect to your DB instance\. An *authentication token* is a string of characters that you use instead of a password\. After you generate an authentication token, it's valid for 15 minutes before it expires\. If you try to connect using an expired token, the connection request is denied\.

Every authentication token must be accompanied by a valid signature, using AWS signature version 4\. \(For more information, see [Signature Version 4 Signing Process](https://docs.aws.amazon.com/general/latest/gr/signature-version-4.html) in the* AWS General Reference\.*\) The AWS CLI and the AWS SDK for Java can automatically sign each token you create\.

You can use an authentication token when you connect to Amazon RDS from another AWS service, such as AWS Lambda\. By using a token, you can avoid placing a password in your code\. Alternatively, you can use the AWS SDK for Java to manually create and manually sign an authentication token\.

After you have a signed IAM authentication token, you can connect to an Amazon RDS DB instance\. Following, you can find out how to do this using either a command line tool or the AWS SDK for Java\.

For more information, see [Use IAM authentication to connect with SQL Workbench/J to Amazon Aurora MySQL or Amazon RDS for MySQL](https://aws.amazon.com/blogs/database/use-iam-authentication-to-connect-with-sql-workbenchj-to-amazon-aurora-mysql-or-amazon-rds-for-mysql/)\.

**Topics**
+ [Connecting to Your DB Instance from the Command Line: AWS CLI and mysql Client](UsingWithRDS.IAMDBAuth.Connecting.AWSCLI.md)
+ [Connecting to Your DB Instance from the Command Line: AWS CLI and psql Client](UsingWithRDS.IAMDBAuth.Connecting.AWSCLI.PostgreSQL.md)
+ [Connecting to Your DB Instance Using the AWS SDK for Java](UsingWithRDS.IAMDBAuth.Connecting.Java.md)