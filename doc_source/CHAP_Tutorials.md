# Amazon RDS tutorials and sample code<a name="CHAP_Tutorials"></a>

The AWS documentation includes several tutorials that guide you through common Amazon RDS use cases\. Many of these tutorials show you how to use Amazon RDS with other AWS services\. In addition, you can access sample code in GitHub\. 

**Note**  
You can find more tutorials at the [AWS Database Blog](http://aws.amazon.com/blogs/database/)\. For information about training, see [AWS Training and Certification](https://www.aws.training/)\.

**Topics**
+ [Tutorials in this guide](#CHAP_Tutorials.ThisGuide)
+ [Tutorials in other AWS guides](#CHAP_Tutorials.OtherGuides)
+ [Tutorials and sample code in GitHub](#CHAP_Tutorials.GitHub)

## Tutorials in this guide<a name="CHAP_Tutorials.ThisGuide"></a>

The following tutorials in this guide show you how to perform common tasks with Amazon RDS:
+ [Tutorial: Create a VPC for use with a DB instance \(IPv4 only\)](CHAP_Tutorials.WebServerDB.CreateVPC.md)

  Learn how to include a DB instance in a virtual private cloud \(VPC\) based on the Amazon VPC service\. In this case, the VPC shares data with a web server that is running on an Amazon EC2 instance in the same VPC\.
+ [Tutorial: Create a VPC for use with a DB instance \(dual\-stack mode\)](CHAP_Tutorials.CreateVPCDualStack.md)

  Learn how to include a DB instance in a virtual private cloud \(VPC\) based on the Amazon VPC service\. In this case, the VPC shares data with an Amazon EC2 instance in the same VPC\. In this tutorial, you create the VPC for this scenario that works with a database running in dual\-stack mode\. 
+ [Tutorial: Create a web server and an Amazon RDS DB instance](TUT_WebAppWithRDS.md)

  Learn how to install an Apache web server with PHP and create a MySQL database\. The web server runs on an Amazon EC2 instance using Amazon Linux, and the MySQL database is a MySQL DB instance\. Both the Amazon EC2 instance and the DB instance run in an Amazon VPC\.
+ [Tutorial: Restore an Amazon RDS DB instance from a DB snapshot](CHAP_Tutorials.RestoringFromSnapshot.md)

  Learn how to restore a DB instance from a DB snapshot\.
+ [Tutorial: Use tags to specify which DB instances to stop](USER_Tagging.md#Tagging.RDS.Autostop)

  Learn how to use tags to specify which DB instances to stop\.
+ [Tutorial: Log DB instance state changes using Amazon EventBridge](rds-cloud-watch-events.md#log-rds-instance-state)

  Learn how to log a DB instance state change using Amazon EventBridge and AWS Lambda\.
+ [Tutorial: Creating an Amazon CloudWatch alarm for Multi\-AZ DB cluster replica lag](multi-az-db-cluster-cloudwatch-alarm.md)

  Learn how to create a CloudWatch alarm that sends an Amazon SNS message when replica lag for a Multi\-AZ DB cluster has exceeded a threshold\. An alarm watches the `ReplicaLag` metric over a time period that you specify\. The action is a notification sent to an Amazon SNS topic or Amazon EC2 Auto Scaling policy\.

## Tutorials in other AWS guides<a name="CHAP_Tutorials.OtherGuides"></a>

The following tutorials in other AWS guides show you how to perform common tasks with Amazon RDS:
+ [ Tutorial: Rotating a Secret for an AWS Database](https://docs.aws.amazon.com/secretsmanager/latest/userguide/tutorials_db-rotate.html) in the *AWS Secrets Manager User Guide*

  Learn how to create a secret for an AWS database and configure the secret to rotate on a schedule\. You trigger one rotation manually, and then confirm that the new version of the secret continues to provide access\.
+ [ Tutorial: Configuring a Lambda function to access Amazon RDS in an Amazon VPC](https://docs.aws.amazon.com/lambda/latest/dg/services-rds-tutorial.html) in the *AWS Lambda Developer Guide*

  Learn how to create a Lambda function to access a database, create a table, add a few records, and retrieve the records from the table\. You also learn how to invoke the Lambda function and verify the query results\.
+ [ Tutorials and samples](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/tutorials.html) in the *AWS Elastic Beanstalk Developer Guide*

  Learn how to deploy applications that use Amazon RDS databases with AWS Elastic Beanstalk\.
+ [ Using Data from an Amazon RDS Database to Create an Amazon ML Datasource](https://docs.aws.amazon.com/machine-learning/latest/dg/using-amazon-rds-with-amazon-ml.html) in the *Amazon Machine Learning Developer Guide*

  Learn how to create an Amazon Machine Learning \(Amazon ML\) datasource object from data stored in a MySQL DB instance\.
+ [ Manually Enabling Access to an Amazon RDS Instance in a VPC](https://docs.aws.amazon.com/quicksight/latest/user/rds-vpc-access.html) in the *Amazon QuickSight User Guide*

  Learn how to enable Amazon QuickSight access to an Amazon RDS DB instance in a VPC\.

## Tutorials and sample code in GitHub<a name="CHAP_Tutorials.GitHub"></a>

The following tutorials and sample code in GitHub show you how to perform common tasks with Amazon RDS:
+ [ Creating the Amazon Relational Database Service item tracker](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/javav2/usecases/Creating_rds_item_tracker)

  Learn how to create an application that tracks and reports on work items\. This application uses Amazon RDS, Amazon Simple Email Service, Elastic Beanstalk, and SDK for Java 2\.x\.
+ [ SDK for Go code examples for Amazon RDS](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/go/rds)

  View a collection of SDK for Go code examples for Amazon RDS and Aurora\.
+ [ SDK for Java 2\.x code examples for Amazon RDS](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/javav2/example_code/rds/src/main/java/com/example/rds)

  View a collection of SDK for Java 2\.x code examples for Amazon RDS and Aurora\.
+ [ SDK for PHP code examples for Amazon RDS](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/php/example_code/rds)

  View a collection of SDK for PHP code examples for Amazon RDS and Aurora\.
+ [AWS SDK for Python \(Boto3\) code examples for Amazon RDS](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/python/example_code/rds)

  View a collection of AWS SDK for Python \(Boto3\) code examples for Amazon RDS\.
+ [ SDK for Ruby code examples for Amazon RDS](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/ruby/example_code/rds)

  View a collection of SDK for Ruby code examples for Amazon RDS and Aurora\.