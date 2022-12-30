# Preparing to create a CEV for RDS Custom for SQL Server<a name="custom-cev-sqlserver.preparing"></a>

To create a CEV using SQL Server CU17, use the following basic steps:

1. Choose an AWS EC2 Windows Amazon Machine Image \(AMI\) with license\-included \(LI\) Microsoft Windows Server and SQL Server\.

   1. Search for **CU17** within the [Windows AMI version history](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)\.

   1. Take note of the Release number\. For SQL Server 2019 CU17, it’s `2022.09.14`\.

   1. In the left navigation panel of the Amazon EC2 console choose **Images**, then **AMIs**\.

   1. Choose **Public images** from the drop\-down menu\.

   1. Enter `2022.09.14` into the search box\. A list of AMIs will be returned\.

   1. Enter `Windows_Server-2019-English-Full-SQL_2019` into the search box to filter the results\. The following results should appear\.  
![\[Supported AMIs using SQL Server 2019 CU17.\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/rds_custom_sqlserver_cev_find_ami.png)

   1. Choose the AMI with the SQL Server edition that you want to use\.

1. Create or launch an EC2 instance from your chosen AMI\.

1. Log in to the EC2 instance and install additional software or customize the OS and database configuration to meet your requirements\.

1. Run Sysprep on the EC2 instance\. For more information prepping an AMI using Sysprep, see [Create a standardized Amazon Machine Image \(AMI\) using Sysprep](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/Creating_EBSbacked_WinAMI.html#sysprep-using-ec2launchv2)\.

1. Save the AMI as your golden image that contains your software and other customizations\.

1. Create a new CEV by providing the AMI ID of the image that you created\. For detailed steps on creating a CEV, see [Creating a CEV for RDS Custom for SQL Server](custom-cev-sqlserver.create.md)\.

1. Create a new RDS Custom for SQL Server DB instance using the CEV\. For detailed steps on creating a RDS Custom for SQL Server DB instance using a CEV, see [Create an RDS Custom for SQL Server DB instance from a CEV](custom-cev-sqlserver.create.md#custom-cev-sqlserver.create.newdbinstance)\.

**Contents**
+ [Region and version availability](#custom-cev-sqlserver.preparing.RegionVersionAvailability)
+ [Requirements](#custom-cev-sqlserver.preparing.Requirements)
+ [Limitations](#custom-cev-sqlserver.preparing.Limitations)

## Region and version availability<a name="custom-cev-sqlserver.preparing.RegionVersionAvailability"></a>

Custom engine version \(CEV\) support for RDS Custom for SQL Server is available in the following AWS Regions:
+ US East \(Ohio\)
+ US East \(N\. Virginia\)
+ US West \(Oregon\)
+ Asia Pacific \(Mumbai\)
+ Asia Pacific \(Seoul\)
+ Asia Pacific \(Singapore\)
+ Asia Pacific \(Sydney\)
+ Asia Pacific \(Tokyo\)
+ Europe \(Frankfurt\)
+ Europe \(Ireland\)
+ Europe \(London\)
+ Europe \(Stockholm\)

CEV creation for RDS Custom for SQL Server is supported for the following AWS EC2 Windows Amazon Machine Images \(AMI\):
+ AWS EC2 Windows AMIs with license\-included \(LI\) Microsoft Windows Server and SQL Server 2019

CEV for RDS Custom for SQL Server is supported for the following operating system \(OS\) and database editions:
+ SQL Server 2019 with CU17, for Enterprise, Standard, and Web editions
+ Windows Server 2019

## Requirements<a name="custom-cev-sqlserver.preparing.Requirements"></a>

The following requirements apply to creating a CEV for RDS Custom for SQL Server:
+ The AMI used to create a CEV is based on an OS and database configuration supported by RDS Custom for SQL Server\. For more information on supported configurations, see [Requirements and limitations for Amazon RDS Custom for SQL Server](custom-reqs-limits-MS.md)\.
+ The CEV must have a unique name\. You can't create a CEV with the same name as an existing CEV\.
+ You must name the CEV using a naming pattern of SQL Server *major version \+ minor version \+ customized string*\. The *major version \+ minor version* must match the SQL Server version provided with the AMI\. For example, you can name an AMI with SQL Server 2019 CU17 as **15\.00\.4249\.2\.my\_cevtest**\.
+ You must prepare an AMI using Sysprep\. For more information about prepping an AMI using Sysprep, see [Create a standardized Amazon Machine Image \(AMI\) using Sysprep](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/Creating_EBSbacked_WinAMI.html#sysprep-using-ec2launchv2)\.
+ You are responsible for maintaining the life cycle of the AMI\. An RDS Custom for SQL Server DB instance created from a CEV doesn't store a copy of the AMI\. It maintains a pointer to the AMI that you used to create the CEV\. The AMI must exist for an RDS Custom for SQL Server DB instance to remain operable\.

## Limitations<a name="custom-cev-sqlserver.preparing.Limitations"></a>

The following limitations apply to custom engine versions with RDS Custom for SQL Server
+ You can't delete a CEV if there are resources, such as DB instances or DB snapshots, associated with it\.
+ To create an RDS Custom for SQL Server DB instance, a CEV must have a status of either `pending-validation`, `available`, `failed`, or `validating`\. A RDS Custom for SQL Server DB instance cannot be created using a CEV if the CEV status is `incompatible-image-configuration`\.
+ To modify a RDS Custom for SQL Server DB instance to use a new CEV, a CEV must have a status of `available`\.
+ Creating an AMI or CEV from an existing RDS Custom for SQL Server DB instance isn't supported\.
+ After it is created, a CEV can't be modified to use a different AMI\. However, you can modify an RDS Custom for SQL Server DB instance to use a different CEV\. For more information, see [Modifying an RDS Custom for SQL Server DB instance](custom-managing-sqlserver.md#custom-managing.modify-sqlserver)\.
+ Cross\-Region copy of CEVs isn't supported\.
+ Cross\-account copy of CEVs isn't supported\.
+ SQL Server Transparent Data Encryption \(TDE\) isn't supported\.
+ After you delete a CEV, it can't be restored or recovered\. However, you can create a new CEV from the same AMI\.
+ A RDS Custom for SQL Server DB instance stores your SQL Server database files in the *D:\\*drive\. The AMI associated with a CEV should store the Microsoft SQL Server system database files in the *C:\\* drive\.
+ An RDS Custom for SQL Server DB instance retains your configuration changes made to SQL Server\. Any configuration changes to the OS on a running RDS Custom for SQL Server DB Instance created from a CEV aren't retained\. If you need to make a permanent configuration change to the OS and have it retained as your new baseline configuration, create a new CEV and modify the DB instance to use the new CEV\.
**Important**  
Modifying an RDS Custom for SQL Server DB instance to use a new CEV is an offline operation\. You can perform the modification immediately or schedule it to occur during a weekly maintenance window\.
+ Updated CEVs don't get pushed to any associated RDS Custom for SQL Server DB instances\. You must modify each RDS Custom for SQL Server DB instance to use the new or updated CEV\. For more information, see [Modifying an RDS Custom for SQL Server DB instance](custom-managing-sqlserver.md#custom-managing.modify-sqlserver)\.
+ 
**Important**  
If an AMI used by a CEV is deleted, any modifications that may require host replacement, for example, scale compute, will fail\. The RDS Custom for SQL Server DB instance will then be placed outside of the RDS support perimeter\. We recommend that you avoid deleting any AMI that's associated to a CEV\.