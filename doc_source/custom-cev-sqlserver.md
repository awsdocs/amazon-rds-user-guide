# Working with custom engine versions for RDS Custom for SQL Server<a name="custom-cev-sqlserver"></a>

A *custom engine version \(CEV\)* for RDS Custom for SQL Server is an Amazon Machine Image \(AMI\) that includes Microsoft SQL Server\.

**The basic steps of the CEV workflow are as follows:**

1. Choose an AWS EC2 Windows AMI to use as a base image for a CEV\. You have the option to use pre\-installed Microsoft SQL Server, or bring your own media to install SQL Server yourself\.

1. Install other software on the operating system \(OS\) and customize the configuration of the OS and SQL Server to meet your enterprise needs\.

1. Save the AMI as a golden image

1. Create a custom engine version \(CEV\) from your golden image\.

1. Create new RDS Custom for SQL Server DB instances by using your CEV\.

Amazon RDS then manages these DB instances for you\.

A CEV allows you to maintain your preferred baseline configuration of the OS and database\. Using a CEV ensures that the host configuration, such as any third\-party agent installation or other OS customizations, are persisted on RDS Custom for SQL Server DB instances\. With a CEV, you can quickly deploy fleets of RDS Custom for SQL Server DB instances with the same configuration\.

**Topics**
+ [Preparing to create a CEV for RDS Custom for SQL Server](custom-cev-sqlserver.preparing.md)
+ [Creating a CEV for RDS Custom for SQL Server](custom-cev-sqlserver.create.md)
+ [Modifying a CEV for RDS Custom for SQL Server](custom-cev-sqlserver-modifying.md)
+ [Viewing CEV details for Amazon RDS Custom for SQL Server](custom-viewing-sqlserver.md)
+ [Deleting a CEV for RDS Custom for SQL Server](custom-cev-sqlserver-deleting.md)