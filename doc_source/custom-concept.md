# Amazon RDS Custom architecture<a name="custom-concept"></a>

Amazon RDS Custom architecture is based on Amazon RDS, with important differences\. The following diagram shows the key components of the RDS Custom architecture\.

![\[RDS Custom architecture components\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/./images/RDS_Custom_gen_architecture.png)

**Topics**
+ [VPC](#custom-concept.components.VPC)
+ [Amazon S3](#custom-concept.components.S3)
+ [AWS CloudTrail](#custom-concept.components.CloudTrail)
+ [RDS Custom automation and monitoring](#custom-concept.workflow.automation)

## VPC<a name="custom-concept.components.VPC"></a>

As in Amazon RDS, your RDS Custom DB instance resides in a virtual private cloud \(VPC\)\. 

![\[RDS Custom DB instance components\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/./images/RDS_Custom_instance.png)

The DB instance consists of the following main components:
+ Amazon EC2 instance
+ Instance endpoint
+ Operating system installed on the Amazon EC2 instance
+ Amazon EBS storage, which contains any additional file systems

## Amazon S3<a name="custom-concept.components.S3"></a>

If you use RDS Custom for Oracle, you upload installation media to a user\-created Amazon S3 bucket\. RDS Custom for Oracle uses the media in this bucket to create a custom engine version \(CEV\)\. A *CEV* is a binary volume snapshot of a database version and Amazon Machine Image \(AMI\)\. From the CEV, you can create an RDS Custom DB instance\. For more information, see [Working with custom engine versions for Amazon RDS Custom for Oracle](custom-cev.md)\.

For both RDS Custom for Oracle and RDS Custom for SQL Server, RDS Custom automatically creates an Amazon S3 bucket prefixed with the string `do-not-delete-rds-custom-`\. RDS Custom uses the `do-not-delete-rds-custom-` S3 bucket to store the following types of files:
+ AWS CloudTrail logs for the trail created by RDS Custom
+ Support perimeter artifacts \(see [Support perimeter](#custom-concept.workflow.automation.support-perimeter)\)
+ Database redo log files \(RDS Custom for Oracle only\)
+ Transaction logs \(RDS Custom for SQL Server only\)
+ Custom engine version artifacts \(RDS Custom for Oracle only\)

RDS Custom creates the `do-not-delete-rds-custom-` S3 bucket when you create either of the following resources:
+ Your first CEV for RDS Custom for Oracle
+ Your first DB instance for RDS Custom for SQL Server

RDS Custom creates one bucket for each combination of the following:
+ AWS account ID
+ Engine type \(either RDS Custom for Oracle or RDS Custom for SQL Server\)
+ AWS Region

For example, if you create RDS Custom for Oracle CEVs in a single AWS Region, one `do-not-delete-rds-custom-` bucket exists\. If you create multiple RDS Custom for SQL Server instances, and they reside in different AWS Regions, one `do-not-delete-rds-custom-` bucket exists in each AWS Region\. If you create one RDS Custom for Oracle instance and two RDS Custom for SQL Server instances in a single AWS Region, two `do-not-delete-rds-custom-` buckets exist\. 

## AWS CloudTrail<a name="custom-concept.components.CloudTrail"></a>

RDS Custom automatically creates an AWS CloudTrail trail whose name begins with `do-not-delete-rds-custom-`\. The RDS Custom support perimeter relies on the events from CloudTrail to determine whether your actions affect RDS Custom automation\. For more information, see [Support perimeter](#custom-concept.workflow.automation.support-perimeter)\.

RDS Custom creates the trail when you create your first DB instance\. RDS Custom creates one trail for each combination of the following:
+ AWS account ID
+ Engine type \(either RDS Custom for Oracle or RDS Custom for SQL Server\)
+ AWS Region

## RDS Custom automation and monitoring<a name="custom-concept.workflow.automation"></a>

RDS Custom has automation software that runs outside of the DB instance\. This software communicates with agents on the DB instance and with other components within the overall RDS Custom environment\.

### Monitoring and recovery<a name="custom-concept.workflow.automation.recovery"></a>

The RDS Custom monitoring and recovery features offer similar functionality to Amazon RDS\. By default, RDS Custom is in full automation mode\. The automation software has the following primary responsibilities:
+ Collect metrics and send notifications
+ Perform automatic instance recovery

An important responsibility of RDS Custom automation is responding to problems with your Amazon EC2 instance\. For various reasons, the host might become impaired or unreachable\. RDS Custom resolves these problems by either rebooting or replacing the Amazon EC2 instance\.

The automated replacement preserves all database data\. On RDS Custom for SQL Server, host replacement doesn't preserve operating system customizations or data on the C: drive\.

The only customer\-visible change when a host is replaced is a new public IP address\. For more information, see [How Amazon RDS Custom replaces an impaired host](custom-troubleshooting.md#custom-troubleshooting.host-problems)\.

### Support perimeter<a name="custom-concept.workflow.automation.support-perimeter"></a>

RDS Custom provides additional monitoring capability called the *support perimeter*\. This additional monitoring ensures that your RDS Custom instance uses a supported AWS infrastructure, operating system, and database\. For more information about the support perimeter, see [RDS Custom support perimeter and unsupported configurations](custom-troubleshooting.md#custom-troubleshooting.support-perimeter)\.