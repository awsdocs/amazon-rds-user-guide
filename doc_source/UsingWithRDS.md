# Security in Amazon RDS<a name="UsingWithRDS"></a>

Cloud security at AWS is the highest priority\. As an AWS customer, you benefit from a data center and network architecture that are built to meet the requirements of the most security\-sensitive organizations\.

Security is a shared responsibility between AWS and you\. The [shared responsibility model](https://aws.amazon.com/compliance/shared-responsibility-model/) describes this as security *of* the cloud and security *in* the cloud:
+ **Security of the cloud** – AWS is responsible for protecting the infrastructure that runs AWS services in the AWS Cloud\. AWS also provides you with services that you can use securely\. Third\-party auditors regularly test and verify the effectiveness of our security as part of the [AWS compliance programs](https://aws.amazon.com/compliance/programs/)\. To learn about the compliance programs that apply to Amazon RDS, see [AWS Services in Scope by Compliance Program](https://aws.amazon.com/compliance/services-in-scope/)\.
+ **Security in the cloud** – Your responsibility is determined by the AWS service that you use\. You are also responsible for other factors including the sensitivity of your data, your organization's requirements, and applicable laws and regulations\. 

This documentation helps you understand how to apply the shared responsibility model when using Amazon RDS\. The following topics show you how to configure Amazon RDS to meet your security and compliance objectives\. You also learn how to use other AWS services that help you monitor and secure your Amazon RDS resources\. 

You can manage access to your Amazon RDS resources and your databases on a DB instance\. The method you use to manage access depends on what type of task the user needs to perform with Amazon RDS: 
+ Run your DB instance in a virtual private cloud \(VPC\) based on the Amazon VPC service for the greatest possible network access control\. For more information about creating a DB instance in a VPC, see [Amazon Virtual Private Cloud VPCs and Amazon RDS](USER_VPC.md)\.
+ Use AWS Identity and Access Management \(IAM\) policies to assign permissions that determine who is allowed to manage Amazon RDS resources\. For example, you can use IAM to determine who is allowed to create, describe, modify, and delete DB instances, tag resources, or modify security groups\.
+ Use security groups to control what IP addresses or Amazon EC2 instances can connect to your databases on a DB instance\. When you first create a DB instance, its firewall prevents any database access except through rules specified by an associated security group\. 
+ Use Secure Socket Layer \(SSL\) or Transport Layer Security \(TLS\) connections with DB instances running the MySQL, MariaDB, PostgreSQL, Oracle, or Microsoft SQL Server database engines\. For more information on using SSL/TLS with a DB instance, see [Using SSL/TLS to Encrypt a Connection to a DB Instance](UsingWithRDS.SSL.md)\.
+ Use Amazon RDS encryption to secure your DB instances and snapshots at rest\. Amazon RDS encryption uses the industry standard AES\-256 encryption algorithm to encrypt your data on the server that hosts your DB instance\. For more information, see [Encrypting Amazon RDS Resources](Overview.Encryption.md)\.
+ Use network encryption and transparent data encryption with Oracle DB instances; for more information, see [Oracle Native Network Encryption](Appendix.Oracle.Options.NetworkEncryption.md) and [Oracle Transparent Data Encryption](Appendix.Oracle.Options.AdvSecurity.md)
+ Use the security features of your DB engine to control who can log in to the databases on a DB instance\. These features work just as if the database was on your local network\. 

**Note**  
You only have to configure security for your use cases\. You don't have to configure security access for processes that Amazon RDS manages\. These include creating backups, replicating data between a primary DB instance and a read replica, and other processes\.

For more information on managing access to Amazon RDS resources and your databases on a DB instance, see the following topics\.

**Topics**
+ [Data Protection in Amazon RDS](DataDurability.md)
+ [Identity and Access Management in Amazon RDS](UsingWithRDS.IAM.md)
+ [Kerberos Authentication](kerberos-authentication.md)
+ [Logging and Monitoring in Amazon RDS](Overview.LoggingAndMonitoring.md)
+ [Compliance Validation for Amazon RDS](RDS-compliance.md)
+ [Resilience in Amazon RDS](disaster-recovery-resiliency.md)
+ [Infrastructure Security in Amazon RDS](infrastructure-security.md)
+ [Amazon RDS and Interface VPC Endpoints \(AWS PrivateLink\)](vpc-interface-endpoints.md)
+ [Security Best Practices for Amazon RDS](CHAP_BestPractices.Security.md)
+ [Controlling Access with Security Groups](Overview.RDSSecurityGroups.md)
+ [Master User Account Privileges](UsingWithRDS.MasterAccounts.md)
+ [Using Service\-Linked Roles for Amazon RDS](UsingWithRDS.IAM.ServiceLinkedRoles.md)
+ [Amazon Virtual Private Cloud VPCs and Amazon RDS](USER_VPC.md)