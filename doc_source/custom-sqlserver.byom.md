# Bring Your Own Media with RDS Custom for SQL Server<a name="custom-sqlserver.byom"></a>

RDS Custom for SQL Server supports two licensing models: License Included \(LI\) and Bring Your Own Media \(BYOM\)\.

**With BYOM, you can do the following:**

1. Provide and install your own Microsoft SQL Server binaries with supported cumulative updates \(CU\) on an AWS EC2 Windows AMI\.

1. Save the AMI as a golden image, which is a template that you can use to create a custom engine version \(CEV\)\.

1. Create a CEV from your golden image\.

1. Create new RDS Custom for SQL Server DB instances by using your CEV\.

Amazon RDS then manages your DB instances for you\.

**Note**  
If you also have a License Included \(LI\) RDS Custom for SQL Server DB instance, you can't use the SQL Server software from this DB instance with BYOM\. You must bring your own SQL Server binaries to BYOM\.

## Requirements for BYOM for RDS Custom for SQL Server<a name="custom-sqlserver.byom.requirements"></a>

The same general requirements for custom engine versions with RDS Custom for SQL Server also apply to BYOM\. For more information, see [Requirements for RDS Custom for SQL Server CEVs](custom-cev-sqlserver.preparing.md#custom-cev-sqlserver.preparing.Requirements)\.

When using BYOM, make you sure that you meet the following additional requirements:
+ Use only SQL Server 2019 Enterprise and Standard edition\. These are the only supported editions\.
+ Grant the SQL Server sysadmin \(SA\) server role privilege to `NT AUTHORITY\SYSTEM`\.
+ Keep the Windows Server OS configured with `UTC` time\. 

  Amazon EC2 Windows instances are set to the UTC time zone by default\. For more information about viewing and changing the time for a Windows instance, see [Set the time for a Windows instance](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-set-time.html)\.
+ Open TCP port 1433 and UDP port 1434 to allow SSM connections\.

## Limitations of BYOM for RDS Custom for SQL Server<a name="custom-sqlserver.byom.limitations"></a>

The same general limitations for RDS Custom for SQL Server also apply to BYOM\. For more information, see [Requirements and limitations for Amazon RDS Custom for SQL Server](custom-reqs-limits-MS.md)\.

With BYOM, the following additional limitations apply:
+ Only the default SQL Server instance \(MSSQLSERVER\) is supported\. Named SQL Server instances aren't supported\. RDS Custom for SQL Server detects and monitors only the default SQL Server instance\.
+ Only a single installation of SQL Server is supported on each AMI\. Multiple installations of different SQL Server versions aren't supported\.
+ SQL Server Web edition isn't supported with BYOM\.
+ Evaluation versions of SQL Server editions aren't supported with BYOM\. When you install SQL Server, don't select the checkbox for using an evaluation version\.
+ Feature availability and support varies across specific versions of each database engine, and across AWS Regions\. For more information, see [Region availability for RDS Custom for SQL Server CEVs](custom-cev-sqlserver.preparing.md#custom-cev-sqlserver.preparing.RegionVersionAvailability) and [Version support for RDS Custom for SQL Server CEVs](custom-cev-sqlserver.preparing.md#custom-cev-sqlserver.preparing.VersionSupport)\. 

## Creating an RDS Custom for SQL Server DB instance with BYOM<a name="custom-sqlserver.byom.creating"></a>

To prepare and create an RDS Custom for SQL Server DB instance with BYOM, see [Preparing a CEV using Bring Your Own Media \(BYOM\)](custom-cev-sqlserver.preparing.md#custom-cev-sqlserver.preparing.byom)\.