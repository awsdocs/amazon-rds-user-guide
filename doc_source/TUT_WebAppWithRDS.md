# Tutorial: Create a web server and an Amazon RDS DB instance<a name="TUT_WebAppWithRDS"></a>

This tutorial helps you install an Apache web server with PHP and create a MySQL database\. The web server runs on an Amazon EC2 instance using Amazon Linux, and the MySQL database is a MySQL DB instance\. Both the Amazon EC2 instance and the DB instance run in a virtual private cloud \(VPC\) based on the Amazon VPC service\. 

**Important**  
There's no charge for creating an AWS account\. However, by completing this tutorial, you might incur costs for the AWS resources you use\. You can delete these resources after you complete the tutorial if they are no longer needed\.

**Note**  
This tutorial works with Amazon Linux and might not work for other versions of Linux such as Ubuntu\.

In the tutorial that follows, you create an EC2 instance that uses the default VPC, subnets, and security group for your AWS account\. This tutorial shows you how to create the DB instance and automatically set up connectivity with the EC2 instance that you created\. The tutorial then shows you how to install the web server on the EC2 instance\. You connect your web server to your DB instance in the VPC using the DB instance endpoint\.

1. [Launch an EC2 instance](CHAP_Tutorials.WebServerDB.LaunchEC2.md)

1. [Create a DB instance](CHAP_Tutorials.WebServerDB.CreateDBInstance.md)

1. [Install a web server on your EC2 instance](CHAP_Tutorials.WebServerDB.CreateWebServer.md)

The following diagram shows the configuration when the tutorial is complete\.

![\[Single VPC Scenario\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/con-VPC-sec-grp.png)

**Note**  
This tutorial uses the default VPC for your AWS account and automatically sets up connectivity between your EC2 instance and DB instance\. If you would rather configure a new VPC for this scenario instead, complete the tasks in [Tutorial: Create a VPC for use with a DB instance \(IPv4 only\)](CHAP_Tutorials.WebServerDB.CreateVPC.md)\.