# Connecting to the DB Instance or DB Cluster<a name="UsingWithRDS.IAMDBAuth.Connecting"></a>

With IAM database authentication, you use an authentication token when you connect to your DB instance or DB cluster\. An *authentication token* is a string of characters that you use instead of a password\. Once you generate an authentication token, it's valid for 15 minutes before it expires\. If you try to connect using an expired token, the connection request is denied\.

Every authentication token must be accompanied by a valid signature, using AWS signature version 4\. \(For more information, see [Signature Version 4 Signing Process](http://docs.aws.amazon.com/general/latest/gr/signature-version-4.html) in the AWS General Reference\.\) The AWS CLI and the AWS SDK for Java can automatically sign each token you create\.

You can use an authentication token when you connect to Amazon RDS from another AWS service, such as AWS Lambda\. By using a token, you can avoid placing a password in your code\.

Alternatively, you can use the AWS SDK for Java to manually create and manually sign an authentication token\.

Once you have a signed IAM authentication token, you can connect to an Amazon RDS DB instance or Aurora DB cluster\. Following, you can find out how to do this using either the `mysql` command line tool or the AWS SDK for Java\.

**Topics**
+ [Command Line: AWS CLI and mysql Client](UsingWithRDS.IAMDBAuth.Connecting.AWSCLI.md)
+ [AWS SDK for Java](UsingWithRDS.IAMDBAuth.Connecting.Java.md)