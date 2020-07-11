# Using Kerberos Authentication with Amazon RDS for PostgreSQL<a name="postgresql-kerberos"></a>

You can use Kerberos authentication to authenticate users when they connect to your DB instance running PostgreSQL\. In this case, your DB instance works with AWS Directory Service for Microsoft Active Directory to enable Kerberos authentication\. AWS Directory Service for Microsoft Active Directory is also called AWS Managed Microsoft AD\. 

You create an AWS Managed Microsoft AD directory to store user credentials\. You then provide to your PostgreSQL DB instance the Active Directory's domain and other information\. When users authenticate with the PostgreSQL DB instance, authentication requests are forwarded to the AWS Managed Microsoft AD directory\. 

Keeping all of your credentials in the same directory can save you time and effort\. You have a centralized place for storing and managing credentials for multiple DB instances\. Using a directory can also improve your overall security profile\.

Kerberos provides a different authentication method than AWS Identity and Access Management \(IAM\)\. A database can use either Kerberos or IAM authentication but not both\. For more information about IAM authentication, see [IAM Database Authentication for MySQL and PostgreSQL](UsingWithRDS.IAMDBAuth.md)\. 

Amazon RDS supports Kerberos authentication for PostgreSQL DB instances in the following AWS Regions: 
+ US East \(Ohio\)
+ US East \(N\. Virginia\)
+ US West \(N\. California\)
+ US West \(Oregon\)
+ Asia Pacific \(Mumbai\)
+ Asia Pacific \(Seoul\)
+ Asia Pacific \(Singapore\)
+ Asia Pacific \(Sydney\)
+ Asia Pacific \(Tokyo\)
+ Canada \(Central\)
+ Europe \(Frankfurt\)
+ Europe \(Ireland\)
+ Europe \(London\)
+ Europe \(Stockholm\)
+ South America \(São Paulo\)
+ China \(Beijing\)
+ China \(Ningxia\)

**Topics**
+ [Overview of Kerberos Authentication for PostgreSQL DB Instances](#postgresql-kerberos-overview)
+ [Setting Up Kerberos Authentication for PostgreSQL DB Instances](postgresql-kerberos-setting-up.md)
+ [Managing a DB Instance in a Domain](postgresql-kerberos-managing.md)
+ [Connecting to PostgreSQL with Kerberos Authentication](postgresql-kerberos-connecting.md)

## Overview of Kerberos Authentication for PostgreSQL DB Instances<a name="postgresql-kerberos-overview"></a>

To set up Kerberos authentication for a PostgreSQL DB instance, take the following steps, described in more detail later:

1. Use AWS Managed Microsoft AD to create an AWS Managed Microsoft AD directory\. You can use the AWS Management Console, the AWS CLI, or the AWS Directory Service API to create the directory\.

1. Create a role that provides Amazon RDS access to make calls to your AWS Managed Microsoft AD directory\. To do so, create an AWS Identity and Access Management \(IAM\) role that uses the managed IAM policy `AmazonRDSDirectoryServiceAccess`\. 

   For the IAM role to allow access, the AWS Security Token Service \(AWS STS\) endpoint must be activated in the correct AWS Region for your AWS account\. AWS STS endpoints are active by default in all AWS Regions, and you can use them without any further actions\. For more information, see [Activating and Deactivating AWS STS in an AWS Region](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp_enable-regions.html#sts-regions-activate-deactivate) in the *IAM User Guide*\.

1. Create and configure users in the AWS Managed Microsoft AD directory using the Microsoft Active Directory tools\. For more information about creating users in your Active Directory, see [Manage Users and Groups in AWS Managed Microsoft AD](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/ms_ad_manage_users_groups.html) in the *AWS Directory Service Administration Guide*\.

1. If you plan to locate the directory and the DB instance in different AWS accounts or virtual private clouds \(VPCs\), configure VPC peering\. For more information, see [What Is VPC Peering?](https://docs.aws.amazon.com/vpc/latest/peering/Welcome.html) in the *Amazon VPC Peering Guide*\.

1. Create or modify a PostgreSQL DB instance either from the console, CLI, or RDS API using one of the following methods:
   +   [Creating an Amazon RDS DB Instance](USER_CreateDBInstance.md) 
   +   [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md) 
   +  [Restoring from a DB Snapshot](USER_RestoreFromSnapshot.md) 
   +  [Restoring a DB Instance to a Specified Time](USER_PIT.md) 

   When you create or modify the DB instance, provide the domain identifier \(`d-*` identifier\) that was generated when you created your directory\. Also provide the name of the IAM role that you created\. You can locate the DB instance in the same VPC as the directory or in a different AWS account or VPC\.

1. Use the RDS master user credentials to connect to the PostgreSQL DB instance\. Create the user in PostgreSQL to be identified externally\. Externally identified users can log in to the PostgreSQL DB instance using Kerberos authentication\.