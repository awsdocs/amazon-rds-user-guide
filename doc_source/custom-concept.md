# Amazon RDS Custom architecture<a name="custom-concept"></a>

Amazon RDS Custom architecture is based on Amazon RDS, with important differences\.

**Topics**
+ [RDS Custom for Oracle components](#custom-concept.components)
+ [RDS Custom for Oracle workflow](#custom-concept.workflow)
+ [RDS Custom for SQL Server components](#custom-sqlserver.components)
+ [RDS Custom for SQL Server workflow](#custom-sqlserver.workflow)
+ [RDS Custom automation and monitoring](#custom-concept.workflow.automation)

## RDS Custom for Oracle components<a name="custom-concept.components"></a>

The following diagram shows the most important components of the RDS Custom for Oracle architecture, listed and described after the diagram\.

![\[RDS Custom for Oracle architecture components\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/./images/RDS_Custom_architecture_v2.png)

**Topics**
+ [VPC](#custom-concept.components.VPC)
+ [Amazon S3](#custom-concept.components.S3)

### VPC<a name="custom-concept.components.VPC"></a>

As in Amazon RDS, the RDS Custom DB instance resides in a virtual private cloud \(VPC\)\. The DB instance consists of the following main components:
+ Amazon EC2 instance
+ Operating system installed on the Amazon EC2 instance
+ Amazon EBS storage, which contains any additional file systems
+ Instance endpoint

### Amazon S3<a name="custom-concept.components.S3"></a>

Amazon S3 provides the storage for your installation media\. You use this media to create a custom engine version \(CEV\)\. A *CEV* is a binary volume snapshot of a database version and Amazon Machine Image \(AMI\)\. From the CEV, you create an RDS Custom instance\.

## RDS Custom for Oracle workflow<a name="custom-concept.workflow"></a>

A typical workflow is as follows:

1. Upload your database software to your Amazon S3 bucket\.

   For more information, see [Uploading your installation files to Amazon S3](custom-cev.md#custom-cev.preparing.s3)\.

1. Create an RDS Custom custom engine version \(CEV\) from your media\.

   For more information, see [Creating a CEV](custom-cev.md#custom-cev.create)\.

1. Create an RDS Custom DB instance from a CEV\.

   For more information, see [Creating an RDS Custom for Oracle DB instance](custom-creating.md#custom-creating.create)\.

1. Connect your application to the RDS Custom DB instance endpoint\.

   For more information, see [Connecting to your RDS Custom DB instance using SSH](custom-creating.md#custom-creating.ssh) and [Connecting to your RDS Custom DB instance using AWS Systems Manager](custom-creating.md#custom-creating.ssm)\.

1. \(Optional\) Access the host to customize your software\.

RDS Custom monitors the DB instance and notifies you of any problems\.

### Database installation files<a name="custom-concept.workflow.db-files"></a>

Your responsibility for media is a key difference between Amazon RDS and RDS Custom\. Amazon RDS, which is a fully managed service, supplies the Amazon Machine Image \(AMI\) and database software\. The Amazon RDS database software is preinstalled, so you need only choose a database engine and version, and create your database\.

For RDS Custom, you supply your own media\. When you create a custom engine version, RDS Custom installs the media that you provide\. RDS Custom media contains your database installation files and patches\. This service model is called Bring Your Own Media \(BYOM\)\.

### Custom engine version<a name="custom-concept.workflow.cev"></a>

An RDS Custom custom engine version \(CEV\) is a binary volume snapshot of a database version and AMI\. You store your database installation files in Amazon S3\. When you create your CEV, you specify the files in a JSON document called a CEV manifest\.

Name your CEV using a customer\-specified string\. The name format is `19.customized_string`\. You can use 1–50 alphanumeric characters, underscores, dashes, and periods\. For example, you might name your CEV `19.my_cev1`\. To learn how to create a CEV, see [Working with custom engine versions for Amazon RDS Custom for Oracle](custom-cev.md)\.

### RDS Custom for Oracle DB instance creation<a name="custom-concept.workflow.instance"></a>

After you create the CEV, it's available for use\. You can create multiple CEVs, and you can create multiple RDS Custom for Oracle instances from any CEV\. You can also change the status of a CEV to make it available or inactive\.

To create your RDS Custom for Oracle DB instance, use the `create-db-instance` command\. In this command, specify which CEV to use\. The creation procedure is similar to the creation of an Amazon RDS instance\. However, some of the parameters are different\. For more information, see [Creating and connecting to a DB instance for Amazon RDS Custom for Oracle](custom-creating.md)\.

### Database connection<a name="custom-concept.workflow.db-connection"></a>

Like an Amazon RDS DB instance, your RDS Custom DB instance resides in a VPC\. Your application connects to the RDS Custom instance using an Oracle Listener, just as with RDS for Oracle\.

### RDS Custom customization<a name="custom-concept.workflow.db-customization"></a>

You can access the RDS Custom host to install or customize software\. To avoid conflicts between your changes and the RDS Custom automation, you can pause the automation for a specified period\. During this period, RDS Custom doesn't perform monitoring or instance recovery\. At the end of the period, RDS Custom resumes full automation\. For more information, see [Pausing and resuming RDS Custom automation](custom-managing.md#custom-managing.pausing)\.

## RDS Custom for SQL Server components<a name="custom-sqlserver.components"></a>

The following diagram shows the most important components of the RDS Custom for SQL Server architecture, listed and described after the diagram\.

![\[RDS Custom for SQL Server architecture\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/./images/custom_sqlserver_architecture_v2.png)

As in Amazon RDS, the RDS Custom for SQL Server DB instance resides in a virtual private cloud \(VPC\)\. The DB instance consists of the following main components:
+ Amazon EC2 instance
+ Operating system installed on the Amazon EC2 instance
+ Amazon EBS storage, which contains any additional file systems
+ Instance endpoint

## RDS Custom for SQL Server workflow<a name="custom-sqlserver.workflow"></a>

A typical workflow is as follows:

1. You create an RDS Custom for SQL Server DB instance from an engine version offered by RDS Custom\.

   For more information, see [Creating an RDS Custom for SQL Server DB instance](custom-creating-sqlserver.md#custom-creating-sqlserver.create)\.

1. You connect your application to the RDS Custom DB instance endpoint\.

   For more information, see [Connecting to your RDS Custom DB instance using AWS Systems Manager](custom-creating-sqlserver.md#custom-creating-sqlserver.ssm) and [Connecting to your RDS Custom DB instance using RDP](custom-creating-sqlserver.md#custom-creating-sqlserver.rdp)\.

1. \(Optional\) You access the host to customize your software\.

RDS Custom monitors the DB instance and notifies you of any problems\.

### RDS Custom DB instance creation<a name="custom-sqlserver.workflow.instance"></a>

You create your RDS Custom DB instance using the `create-db-instance` command\. The creation procedure is similar to the creation of an Amazon RDS instance\. However, some of the parameters are different\. For more information, see [Creating and connecting to a DB instance for Amazon RDS Custom for SQL Server](custom-creating-sqlserver.md)\.

### Database connection<a name="custom-sqlserver.workflow.db-connection"></a>

Like an Amazon RDS DB instance, your RDS Custom for SQL Server DB instance resides in a VPC\. Your application connects to the RDS Custom instance using a client such as SQL Server Management Suite \(SSMS\), just as in RDS for SQL Server\.

### RDS Custom customization<a name="custom-sqlserver.workflow.customization"></a>

You can access the RDS Custom host to install or customize software\. To avoid conflicts between your changes and the RDS Custom automation, you can pause the automation for a specified period\. During this period, RDS Custom doesn't perform monitoring or instance recovery\. At the end of the period, RDS Custom resumes full automation\. For more information, see [Pausing and resuming RDS Custom automation](custom-managing.md#custom-managing.pausing)\.

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