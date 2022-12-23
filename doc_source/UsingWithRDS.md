# Security in Amazon RDS<a name="UsingWithRDS"></a>

Cloud security at AWS is the highest priority\. As an AWS customer, you benefit from a data center and network architecture that are built to meet the requirements of the most security\-sensitive organizations\.

Security is a shared responsibility between AWS and you\. The [shared responsibility model](https://aws.amazon.com/compliance/shared-responsibility-model/) describes this as security *of* the cloud and security *in* the cloud:
+ **Security of the cloud** – AWS is responsible for protecting the infrastructure that runs AWS services in the AWS Cloud\. AWS also provides you with services that you can use securely\. Third\-party auditors regularly test and verify the effectiveness of our security as part of the [AWS compliance programs](https://aws.amazon.com/compliance/programs/)\. To learn about the compliance programs that apply to Amazon RDS, see [AWS services in scope by compliance program](https://aws.amazon.com/compliance/services-in-scope/)\.
+ **Security in the cloud** – Your responsibility is determined by the AWS service that you use\. You are also responsible for other factors including the sensitivity of your data, your organization's requirements, and applicable laws and regulations\. 

This documentation helps you understand how to apply the shared responsibility model when using Amazon RDS\. The following topics show you how to configure Amazon RDS to meet your security and compliance objectives\. You also learn how to use other AWS services that help you monitor and secure your Amazon RDS resources\. 

You can manage access to your Amazon RDS resources and your databases on a DB instance\. The method you use to manage access depends on what type of task the user needs to perform with Amazon RDS: 
+ Run your DB instance in a virtual private cloud \(VPC\) based on the Amazon VPC service for the greatest possible network access control\. For more information about creating a DB instance in a VPC, see [Amazon VPC VPCs and Amazon RDS](USER_VPC.md)\.
+ Use AWS Identity and Access Management \(IAM\) policies to assign permissions that determine who is allowed to manage Amazon RDS resources\. For example, you can use IAM to determine who is allowed to create, describe, modify, and delete DB instances, tag resources, or modify security groups\.
+ Use security groups to control what IP addresses or Amazon EC2 instances can connect to your databases on a DB instance\. When you first create a DB instance, its firewall prevents any database access except through rules specified by an associated security group\. 
+ Use Secure Socket Layer \(SSL\) or Transport Layer Security \(TLS\) connections with DB instances running the MySQL, MariaDB, PostgreSQL, Oracle, or Microsoft SQL Server database engines\. For more information on using SSL/TLS with a DB instance, see [Using SSL/TLS to encrypt a connection to a DB instance](UsingWithRDS.SSL.md)\.
+ Use Amazon RDS encryption to secure your DB instances and snapshots at rest\. Amazon RDS encryption uses the industry standard AES\-256 encryption algorithm to encrypt your data on the server that hosts your DB instance\. For more information, see [Encrypting Amazon RDS resources](Overview.Encryption.md)\.
+ Use network encryption and transparent data encryption with Oracle DB instances; for more information, see [Oracle native network encryption](Appendix.Oracle.Options.NetworkEncryption.md) and [Oracle Transparent Data Encryption](Appendix.Oracle.Options.AdvSecurity.md)
+ Use the security features of your DB engine to control who can log in to the databases on a DB instance\. These features work just as if the database was on your local network\. 

**Note**  
You have to configure security only for your use cases\. You don't have to configure security access for processes that Amazon RDS manages\. These include creating backups, replicating data between a primary DB instance and a read replica, and other processes\.

For more information on managing access to Amazon RDS resources and your databases on a DB instance, see the following topics\.

**Topics**
+ [Database authentication with Amazon RDS](database-authentication.md)
+ [Password management with Amazon RDS and AWS Secrets Manager](rds-secrets-manager.md)
+ [Data protection in Amazon RDS](DataDurability.md)
+ [Identity and access management for Amazon RDS](UsingWithRDS.IAM.md)
+ [Logging and monitoring in Amazon RDS](Overview.LoggingAndMonitoring.md)
+ [Compliance validation for Amazon RDS](RDS-compliance.md)
+ [Resilience in Amazon RDS](disaster-recovery-resiliency.md)
+ [Infrastructure security in Amazon RDS](infrastructure-security.md)
+ [Amazon RDS API and interface VPC endpoints \(AWS PrivateLink\)](vpc-interface-endpoints.md)
+ [Security best practices for Amazon RDS](CHAP_BestPractices.Security.md)
+ [Controlling access with security groups](Overview.RDSSecurityGroups.md)
+ [Master user account privileges](UsingWithRDS.MasterAccounts.md)
+ [Using service\-linked roles for Amazon RDS](UsingWithRDS.IAM.ServiceLinkedRoles.md)
+ [Amazon VPC VPCs and Amazon RDS](USER_VPC.md)