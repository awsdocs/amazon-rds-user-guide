# Working with Amazon RDS Custom<a name="rds-custom"></a>

Amazon RDS Custom automates database administration tasks and operations, while making it possible for you as a database administrator to access and customize your database environment and operating system\. With RDS Custom, you can customize to meet the requirements of legacy, custom, and packaged applications\.

**Topics**
+ [Addressing the challenge of database customization](#custom-intro.challenge)
+ [Management model and benefits for Amazon RDS Custom](#custom-intro.solution)
+ [Amazon RDS Custom architecture](custom-concept.md)
+ [Requirements and limitations for Amazon RDS Custom](custom-reqs-limits.md)
+ [Setting up your environment for Amazon RDS Custom for Oracle](custom-setup-orcl.md)
+ [Working with custom engine versions for Amazon RDS Custom for Oracle](custom-cev.md)
+ [Creating and connecting to an Amazon RDS Custom DB instance](custom-creating.md)
+ [Managing an Amazon RDS Custom DB instance](custom-managing.md)
+ [Working with read replicas for RDS Custom for Oracle](custom-rr.md)
+ [Backing up and restoring an Amazon RDS Custom DB instance](custom-backup.md)
+ [Upgrading a DB instance for Amazon RDS Custom for Oracle](custom-upgrading.md)
+ [Troubleshooting DB issues for Amazon RDS Custom](custom-troubleshooting.md)

## Addressing the challenge of database customization<a name="custom-intro.challenge"></a>

Amazon RDS Custom brings the benefits of Amazon RDS to a market that can't easily move to a fully managed service because of customizations that are required with third\-party applications\. Amazon RDS Custom saves administrative time, is durable, and scales with your business\.

If you need the entire database and operating system to be fully managed by AWS, we recommend Amazon RDS\. If you need administrative rights to the database and underlying operating system to make dependent applications available, Amazon RDS Custom is the better choice\. If you want full management responsibility and simply need a managed compute service, the best option is self\-managing your commercial databases on Amazon EC2\.

To deliver a managed service experience, Amazon RDS doesn't let you access the underlying host\. Amazon RDS also restricts access to some procedures and objects that require high\-level privileges\. However, for some applications, you might need to perform operations as a privileged OS user\.

For example, you might need to do the following:
+ Install custom database and OS patches and packages\.
+ Configure specific database settings\.
+ Configure file systems to share files directly with their applications\.

Previously, if you needed to customize your application, you had to deploy your database on\-premises or on Amazon EC2\. In this case, you bear most or all of the responsibility for database management, as summarized in the following table\.


|  Feature  |  On\-premises responsibility  |  Amazon EC2 responsibility  |  Amazon RDS responsibility  | 
| --- | --- | --- | --- | 
|  Application optimization  |  Customer  |  Customer  |  Customer  | 
|  Scaling  |  Customer  |  Customer  |  AWS  | 
|  High availability  |  Customer  |  Customer  |  AWS  | 
|  Database backups  |  Customer  |  Customer  |  AWS  | 
|  Database software patching  |  Customer  |  Customer  |  AWS  | 
|  Database software install  |  Customer  |  Customer  |  AWS  | 
|  OS patching  |  Customer  |  Customer  |  AWS  | 
|  OS installation  |  Customer  |  Customer  |  AWS  | 
|  Server maintenance  |  Customer  |  AWS  |  AWS  | 
|  Hardware lifecycle  |  Customer  |  AWS  |  AWS  | 
|  Power, network, and cooling  |  Customer  |  AWS  |  AWS  | 

When you manage database software yourself, you gain more control, but you're also more prone to user errors\. For example, when you make changes manually, you might accidentally cause application downtime\. You might spend hours checking every change to identify and fix an issue\. Ideally, you want a managed database service that automates common DBA tasks, but also supports privileged access to the database and underlying operating system\.

## Management model and benefits for Amazon RDS Custom<a name="custom-intro.solution"></a>

Amazon RDS Custom is a managed database service for legacy, custom, and packaged applications that require access to the underlying operating system and database environment\. Amazon RDS Custom automates setup, operation, and scaling of databases in the AWS Cloud while granting you access to the database and underlying operating system\. With this access, you can configure settings, install patches, and enable native features to meet the dependent application's requirements\. With RDS Custom, you can run your database workload using the AWS Management Console or the AWS CLI\.

Currently, Amazon RDS Custom supports only the Oracle Database engine\.

### Shared responsibility model<a name="custom-intro.solution.shared"></a>

With Amazon RDS Custom, you get the automation of Amazon RDS and the flexibility of Amazon EC2\. By taking on additional database management responsibilities beyond what you do in Amazon RDS, you can benefit from Amazon RDS automation and the deeper customization that Amazon EC2 offers\.

The following table details the shared responsibility model for RDS Custom\.


|  Feature  |  Amazon EC2 responsibility  |  Amazon RDS responsibility  |  RDS Custom responsibility  | 
| --- | --- | --- | --- | 
|  Application optimization  |  Customer  |  Customer  |  Customer  | 
|  Scaling  |  Customer  |  AWS  |  Shared  | 
|  High availability  |  Customer  |  AWS  |  Shared  | 
|  Database backups  |  Customer  |  AWS  |  Shared  | 
|  Database software patching   |  Customer  |  AWS  |  Shared  | 
|  Database software install  |  Customer  |  AWS  |  Shared  | 
|  OS patching  |  Customer  |  AWS  |  Shared  | 
|  OS installation  |  Customer  |  AWS  |  Shared  | 
|  Server maintenance  |  AWS  |  AWS  |  AWS  | 
|  Hardware lifecycle  |  AWS  |  AWS  |  AWS  | 
|  Power, network, and cooling  |  AWS  |  AWS  |  AWS  | 

In the shared responsibility model of RDS Custom, you get more control than in Amazon RDS but also more responsibility\. To meet your application and business requirements, you manage the host yourself\.

You can create an RDS Custom DB instance using Oracle Database\. You do the following:
+ Manage your own media\.

  When using RDS Custom, you upload your own database installation files and patches\. You create a custom engine version \(CEV\) from these files\. Then you can create an RDS Custom DB instance by using this CEV\.
+ Manage your own licenses\.

  You bring your own Oracle Database licenses and manage licenses by yourself\.

### Key benefits of RDS Custom<a name="custom-intro.solution.benefits"></a>

With RDS Custom, you can do the following:
+ Automate many of the same administrative tasks as Amazon RDS, including the following:
  + Lifecycle management of databases
  + Automated backups and point\-in\-time recovery \(PITR\)
  + Monitoring the health of RDS Custom DB instances and observing changes to the infrastructure, operating system, and databases
  + Notification or taking action to fix issues depending on disruption to the DB instance
+ Install third\-party applications\.

  You can install software to run custom applications and agents\. Because you have privileged access to the host, you can modify file systems to support legacy applications\.
+ Install custom patches\.

  You can apply custom database patches or modify OS packages on your RDS Custom DB instances\.
+ Stage an on\-premises database before moving it to a fully managed service\.

  If you manage your own on\-premises database, you can stage the database to RDS Custom as\-is\. After you familiarize yourself with the cloud environment, you can migrate your database to a fully managed Amazon RDS DB instance\.
+ Create your own automation\.

  You can create, schedule, and execute custom automation scripts for reporting, management, or diagnostic tools\.