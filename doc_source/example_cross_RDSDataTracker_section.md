# Create an Aurora Serverless work item tracker<a name="example_cross_RDSDataTracker_section"></a>

The following code examples show how to create a web application that tracks work items in an Amazon Aurora Serverless database and uses Amazon Simple Email Service \(Amazon SES\) to send reports\.

**Note**  
The source code for these examples is in the [AWS Code Examples GitHub repository](https://github.com/awsdocs/aws-doc-sdk-examples)\. Have feedback on a code example? [Create an Issue](https://github.com/awsdocs/aws-doc-sdk-examples/issues/new/choose) in the code examples repo\. 

------
#### [ \.NET ]

**AWS SDK for \.NET**  
 Shows how to use the AWS SDK for \.NET to create a web application that tracks work items in an Amazon Aurora database and emails reports by using Amazon Simple Email Service \(Amazon SES\)\. This example uses a front end built with React\.js to interact with a RESTful \.NET backend\.   
+ Integrate a React web application with AWS services\.
+ List, add, update, and delete items in an Aurora table\.
+ Send an email report of filtered work items using Amazon SES\.
+ Deploy and manage example resources with the included AWS CloudFormation script\.
 For complete source code and instructions on how to set up and run, see the full example on [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/dotnetv3/cross-service/AuroraItemTracker)\.   

**Services used in this example**
+ Aurora
+ Amazon RDS
+ Amazon RDS Data Service
+ Amazon SES

------
#### [ C\+\+ ]

**SDK for C\+\+**  
 Shows how to create a web application that tracks and reports on work items stored in an Amazon Aurora Serverless database\.   
 For complete source code and instructions on how to set up a C\+\+ REST API that queries Amazon Aurora Serverless data and for use by a React application, see the full example on [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/cpp/example_code/cross-service/serverless-aurora)\.   

**Services used in this example**
+ Aurora
+ Amazon RDS
+ Amazon RDS Data Service
+ Amazon SES

------
#### [ Java ]

**SDK for Java 2\.x**  
 Shows how to create a web application that tracks and reports on work items stored in an Amazon RDS database\.   
 For complete source code and instructions on how to set up a Spring REST API that queries Amazon Aurora Serverless data and for use by a React application, see the full example on [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/javav2/usecases/Creating_Spring_RDS_%20Rest)\.   
 For complete source code and instructions on how to set up and run an example that uses the JDBC API, see the full example on [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/javav2/usecases/Creating_rds_item_tracker)\.   

**Services used in this example**
+ Aurora
+ Amazon RDS
+ Amazon RDS Data Service
+ Amazon SES

------
#### [ JavaScript ]

**SDK for JavaScript \(v3\)**  
 Shows how to use the AWS SDK for JavaScript \(v3\) to create a web application that tracks work items in an Amazon Aurora database and emails reports by using Amazon Simple Email Service \(Amazon SES\)\. This example uses a front end built with React\.js to interact with an Express Node\.js backend\.   
+ Integrate a React\.js web application with AWS services\.
+ List, add, and update items in an Aurora table\.
+ Send an email report of filtered work items by using Amazon SES\.
+ Deploy and manage example resources with the included AWS CloudFormation script\.
 For complete source code and instructions on how to set up and run, see the full example on [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/javascriptv3/example_code/cross-services/aurora-serverless-app)\.   

**Services used in this example**
+ Aurora
+ Amazon RDS
+ Amazon RDS Data Service
+ Amazon SES

------
#### [ Kotlin ]

**SDK for Kotlin**  
This is prerelease documentation for a feature in preview release\. It is subject to change\.
 Shows how to create a web application that tracks and reports on work items stored in an Amazon RDS database\.   
 For complete source code and instructions on how to set up a Spring REST API that queries Amazon Aurora Serverless data and for use by a React application, see the full example on [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/kotlin/usecases/serverless_rds)\.   

**Services used in this example**
+ Aurora
+ Amazon RDS
+ Amazon RDS Data Service
+ Amazon SES

------
#### [ PHP ]

**SDK for PHP**  
 Shows how to use the AWS SDK for PHP to create a web application that tracks work items in an Amazon RDS database and emails reports by using Amazon Simple Email Service \(Amazon SES\)\. This example uses a front end built with React\.js to interact with a RESTful PHP backend\.   
+ Integrate a React\.js web application with AWS services\.
+ List, add, update, and delete items in an Amazon RDS table\.
+ Send an email report of filtered work items using Amazon SES\.
+ Deploy and manage example resources with the included AWS CloudFormation script\.
 For complete source code and instructions on how to set up and run, see the full example on [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/php/cross_service/aurora_item_tracker)\.   

**Services used in this example**
+ Aurora
+ Amazon RDS
+ Amazon RDS Data Service
+ Amazon SES

------
#### [ Python ]

**SDK for Python \(Boto3\)**  
 Shows how to use the AWS SDK for Python \(Boto3\) to create a REST service that tracks work items in an Amazon Aurora Serverless database and emails reports by using Amazon Simple Email Service \(Amazon SES\)\. This example uses the Flask web framework to handle HTTP routing and integrates with a React webpage to present a fully functional web application\.   
+ Build a Flask REST service that integrates with AWS services\.
+ Read, write, and update work items that are stored in an Aurora Serverless database\.
+ Create an AWS Secrets Manager secret that contains database credentials and use it to authenticate calls to the database\.
+ Use Amazon SES to send email reports of work items\.
 For complete source code and instructions on how to set up and run, see the full example on [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/python/cross_service/aurora_item_tracker)\.   

**Services used in this example**
+ Aurora
+ Amazon RDS
+ Amazon RDS Data Service
+ Amazon SES

------

For a complete list of AWS SDK developer guides and code examples, see [Using this service with an AWS SDK](CHAP_Tutorials.md#sdk-general-information-section)\. This topic also includes information about getting started and details about previous SDK versions\.