# Working with custom engine versions for RDS Custom for SQL Server<a name="custom-cev-sqlserver"></a>

A *custom engine version \(CEV\)* for RDS Custom for SQL Server is an Amazon Machine Image \(AMI\) with pre\-installed Microsoft SQL Server\. You choose an AWS EC2 Windows AMI to use as a base image and can install other software on the operating system \(OS\)\. You can customize the configuration of the OS and SQL Server to meet your enterprise needs\. You save the AMI as a golden image to create a CEV from and then create new RDS Custom for SQL Server DB instances by using that CEV\. Amazon RDS then manages these DB instances for you\.

A CEV allows you to maintain your preferred baseline configuration of the OS and database\. Using a CEV ensures that the host configuration, such as any third\-party agent installation or other OS customizations, are persisted on RDS Custom for SQL Server DB instances\. Additionally, a CEV lets you quickly deploy fleets of RDS Custom for SQL Server DB instances with the same configuration\.

**Topics**
+ [Preparing to create a CEV for RDS Custom for SQL Server](custom-cev-sqlserver.preparing.md)
+ [Creating a CEV for RDS Custom for SQL Server](custom-cev-sqlserver.create.md)
+ [Modifying a CEV for RDS Custom for SQL Server](custom-cev-sqlserver-modifying.md)
+ [Viewing CEV details for Amazon RDS Custom for SQL Server](custom-viewing-sqlserver.md)
+ [Deleting a CEV for RDS Custom for SQL Server](custom-cev-sqlserver-deleting.md)