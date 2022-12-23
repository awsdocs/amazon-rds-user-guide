# Working with Amazon RDS on AWS Outposts<a name="rds-on-outposts"></a>

Amazon RDS on AWS Outposts extends RDS for SQL Server, RDS for MySQL, and RDS for PostgreSQL databases to AWS Outposts environments\. AWS Outposts uses the same hardware as in public AWS Regions to bring AWS services, infrastructure, and operation models on\-premises\. With RDS on Outposts, you can provision managed DB instances close to the business applications that must run on\-premises\. For more information about AWS Outposts, see [AWS Outposts](http://aws.amazon.com/outposts/)\.

You use the same AWS Management Console, AWS CLI, and RDS API to provision and manage on\-premises RDS on Outposts DB instances as you do for RDS DB instances running in the AWS Cloud\. RDS on Outposts automates tasks, such as database provisioning, operating system and database patching, backup, and long\-term archival in Amazon S3\.

RDS on Outposts supports automated backups of DB instances\. Network connectivity between your Outpost and your AWS Region is required to back up and restore DB instances\. All DB snapshots and transaction logs from an Outpost are stored in your AWS Region\. From your AWS Region, you can restore a DB instance from a DB snapshot to a different Outpost\. For more information, see [Working with backups](USER_WorkingWithAutomatedBackups.md)\.

RDS on Outposts supports automated maintenance and upgrades of DB instances\. For more information, see [Maintaining a DB instance](USER_UpgradeDBInstance.Maintenance.md)\.

RDS on Outposts uses encryption at rest for DB instances and DB snapshots using your AWS KMS key\. For more information about encryption at rest, see [Encrypting Amazon RDS resources](Overview.Encryption.md)\.

By default, EC2 instances in Outposts subnets can use the Amazon Route 53 DNS Service to resolve domain names to IP addresses\. You might encounter longer DNS resolution times with Route 53, depending on the path latency between your Outpost and the AWS Region\. In such cases, you can use the DNS servers installed locally in your on\-premises environment\. For more information, see [DNS](https://docs.aws.amazon.com/outposts/latest/userguide/outposts-networking-components.html#dns) in the *AWS Outposts User Guide*\.

When network connectivity to the AWS Region isn't available, your DB instance continues to run locally\. You can continue to access DB instances using DNS name resolution by configuring a local DNS server as a secondary server\. However, you can't create new DB instances or take new actions on existing DB instances\. Automatic backups don't occur when there is no connectivity\. If there is a DB instance failure, the DB instance isn't automatically replaced until connectivity is restored\. We recommend restoring network connectivity as soon as possible\.

**Topics**
+ [Prerequisites for Amazon RDS on AWS Outposts](#rds-on-outposts.prerequisites)
+ [Amazon RDS on AWS Outposts support for Amazon RDS features](rds-on-outposts.features.md)
+ [Supported DB instance classes for Amazon RDS on AWS Outposts](rds-on-outposts.db-instance-classes.md)
+ [Customer\-owned IP addresses for Amazon RDS on AWS Outposts](rds-on-outposts.coip.md)
+ [Working with Multi\-AZ deployments for Amazon RDS on AWS Outposts](rds-on-outposts.maz.md)
+ [Creating DB instances for Amazon RDS on AWS Outposts](rds-on-outposts.creating.md)
+ [Creating read replicas for Amazon RDS on AWS Outposts](rds-on-outposts.rr.md)
+ [Considerations for restoring DB instances on Amazon RDS on AWS Outposts](rds-on-outposts.restoring.md)

## Prerequisites for Amazon RDS on AWS Outposts<a name="rds-on-outposts.prerequisites"></a>

The following are prerequisites for using Amazon RDS on AWS Outposts:
+ Install AWS Outposts in your on\-premises data center\. For more information about AWS Outposts, see [AWS Outposts](http://aws.amazon.com/outposts/)\.
+ Make sure that you have at least one subnet available for RDS on Outposts\. You can use the same subnet for other workloads\.
+ Make sure that you have a reliable network connection between your Outpost and an AWS Region\.