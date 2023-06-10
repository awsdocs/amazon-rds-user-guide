# Preparing to create a CEV for RDS Custom for SQL Server<a name="custom-cev-sqlserver.preparing"></a>

You can create a CEV using an Amazon Machine Image \(AMI\) that contains pre\-installed, License Included \(LI\) Microsoft SQL Server, or with an AMI on which you install your own SQL Server installation media \(BYOM\)\.

**Contents**
+ [Preparing a CEV using pre\-installed SQL Server \(LI\)](#custom-cev-sqlserver.preparing.licenseincluded)
+ [Preparing a CEV using Bring Your Own Media \(BYOM\)](#custom-cev-sqlserver.preparing.byom)
+ [Region availability for RDS Custom for SQL Server CEVs](#custom-cev-sqlserver.preparing.RegionVersionAvailability)
+ [Version support for RDS Custom for SQL Server CEVs](#custom-cev-sqlserver.preparing.VersionSupport)
+ [Requirements for RDS Custom for SQL Server CEVs](#custom-cev-sqlserver.preparing.Requirements)
+ [Limitations for RDS Custom for SQL Server CEVs](#custom-cev-sqlserver.preparing.Limitations)

## Preparing a CEV using pre\-installed SQL Server \(LI\)<a name="custom-cev-sqlserver.preparing.licenseincluded"></a>

The following steps to create a CEV using pre\-installed Microsoft SQL Server \(LI\) use an AMI with **SQL Server CU20** Release number `2023.05.10` as an example\. When you create a CEV, choose an AMI with the most recent release number\. This ensures that you are using a supported version of Windows Server and SQL Server with the latest Cumulative Update \(CU\)\.

**To create a CEV using pre\-installed Microsoft SQL Server \(LI\)**

1. Choose the latest available AWS EC2 Windows Amazon Machine Image \(AMI\) with License Included \(LI\) Microsoft Windows Server and SQL Server\.

   1. Search for **CU20** within the [Windows AMI version history](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)\.

   1. Note the Release number\. For SQL Server 2019 CU20, the release number is `2023.05.10`\.  
![\[AMI version history result for SQL Server 2019 CU20.\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/rds_custom_sqlserver_cev_find_ami_history_li_cu20.png)

   1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

   1. In the left navigation panel of the Amazon EC2 console choose **Images**, then **AMIs**\.

   1. Choose **Public images**\.

   1. Enter `2023.05.10` into the search box\. A list of AMIs appears\.

   1. Enter `Windows_Server-2019-English-Full-SQL_2019` into the search box to filter the results\. The following results should appear\.  
![\[Supported AMIs using SQL Server 2019 CU20.\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/rds_custom_sqlserver_cev_find_ami_li_cu.png)

   1. Choose the AMI with the SQL Server edition that you want to use\.

1. Create or launch an EC2 instance from your chosen AMI\.

1. Log in to the EC2 instance and install additional software or customize the OS and database configuration to meet your requirements\.

1. Run Sysprep on the EC2 instance\. For more information prepping an AMI using Sysprep, see [Create a standardized Amazon Machine Image \(AMI\) using Sysprep](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/Creating_EBSbacked_WinAMI.html#sysprep-using-ec2launchv2)\.

1. Save the AMI that contains your installed SQL Server version, other software, and customizations\. This will be your golden image\.

1. Create a new CEV by providing the AMI ID of the image that you created\. For detailed steps on creating a CEV, see [Creating a CEV for RDS Custom for SQL Server](custom-cev-sqlserver.create.md)\.

1. Create a new RDS Custom for SQL Server DB instance using the CEV\. For detailed steps, see [Create an RDS Custom for SQL Server DB instance from a CEV](custom-cev-sqlserver.create.md#custom-cev-sqlserver.create.newdbinstance)\.

## Preparing a CEV using Bring Your Own Media \(BYOM\)<a name="custom-cev-sqlserver.preparing.byom"></a>

The following steps use an AMI with **Windows Server 2019** Release number `2023.05.10` as an example\. When creating a CEV, choose an AMI with the most recent release number\. This ensures that you are using the latest supported version of Windows Server\.

**To create a CEV using BYOM**

1. Choose the latest available AWS EC2 Windows Amazon Machine Image \(AMI\) with Microsoft Windows Server\.

   1. View the monthly AMI updates table within the [Windows AMI version history](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-ami-version-history.html)\.

   1. Note the latest available **Release** number\. For example, the release number for **Windows Server 2019** might be `2023.05.10`\. Although the **Changes** column may show `SQL Server CUs installed`, the release number also includes an AMI for **Windows Server 2019**, without SQL Server pre\-installed\. You can use this AMI for BYOM\.  
![\[Windows AMI version history using Windows Server for BYOM.\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/rds_custom_sqlserver_cev_find_ami_byom_windows_server_base_no_cu.png)

   1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

   1. In the left navigation panel of the Amazon EC2 console, choose **Images**, then **AMIs**\.

   1. Choose **Public images**\.

   1. Enter `Windows_Server-2019-English-Full-Base-2023.05.10` into the search box\. The following results should appear:  
![\[Supported AMIs using Windows Server for BYOM.\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/rds_custom_sqlserver_cev_find_ami_byom_windows_server_cu20.png)

   1. Choose the AMI with the supported Windows Server version that you want to use\.

1. Create or launch an EC2 instance from your chosen AMI\.

1. Log in to the EC2 instance and copy your SQL Server installation media to it\.

1. Install SQL Server\. Make sure that you do the following:

   1. Review [Requirements for BYOM for RDS Custom for SQL Server](custom-sqlserver.byom.md#custom-sqlserver.byom.requirements)\.

   1. Set the instance root directory to the default `C:\Program Files\Microsoft SQL Server\`\. Don't change this directory\.

   1. Set the SQL Server Database Engine Account Name to either `NT Service\MSSQLSERVER` or `NT AUTHORITY\NETWORK SERVICE`\.

   1. Set the SQL Server Startup mode to **Manual**\.

   1. Choose SQL Server Authentication mode as **Mixed**\.

   1. Leave the current settings for the default Data directories and TempDB locations\.

1. Grant the SQL Server sysadmin \(SA\) server role privilege to `NT AUTHORITY\SYSTEM`:

   ```
   1. USE [master]
   2. GO
   3. EXEC master..sp_addsrvrolemember @loginame = N'NT AUTHORITY\SYSTEM' , @rolename = N'sysadmin'
   4. GO
   ```

1. Install additional software or customize the OS and database configuration to meet your requirements\.

1. Run Sysprep on the EC2 instance\. For more information, see [Create a standardized Amazon Machine Image \(AMI\) using Sysprep](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/Creating_EBSbacked_WinAMI.html#sysprep-using-ec2launchv2)\.

1. Save the AMI that contains your installed SQL Server version, other software, and customizations\. This will be your golden image\.

1. Create a new CEV by providing the AMI ID of the image that you created\. For detailed steps, see [Creating a CEV for RDS Custom for SQL Server](custom-cev-sqlserver.create.md)\.

1. Create a new RDS Custom for SQL Server DB instance using the CEV\. For detailed steps, see [Create an RDS Custom for SQL Server DB instance from a CEV](custom-cev-sqlserver.create.md#custom-cev-sqlserver.create.newdbinstance)\.

## Region availability for RDS Custom for SQL Server CEVs<a name="custom-cev-sqlserver.preparing.RegionVersionAvailability"></a>

Custom engine version \(CEV\) support for RDS Custom for SQL Server is available in the following AWS Regions:
+ US East \(Ohio\)
+ US East \(N\. Virginia\)
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

## Version support for RDS Custom for SQL Server CEVs<a name="custom-cev-sqlserver.preparing.VersionSupport"></a>

CEV creation for RDS Custom for SQL Server is supported for the following AWS EC2 Windows AMIs:
+ For CEVs using pre\-installed media, AWS EC2 Windows AMIs with License Included \(LI\) Microsoft Windows Server 2019 and SQL Server 2019
+ For CEVs using bring your own media \(BYOM\), AWS EC2 Windows AMIs with Microsoft Windows Server 2019

CEV creation for RDS Custom for SQL Server is supported for the following operating system \(OS\) and database editions:
+ For CEVs using pre\-installed media, SQL Server 2019 with CU17, CU18, or CU20, for Enterprise, Standard, and Web editions
+ For CEVs using bring your own media \(BYOM\), SQL Server 2019 with CU17, CU18, or CU20, for Enterprise and Standard editions
+ For CEVs using pre\-installed media or bring your own media \(BYOM\), Windows Server 2019 is the only supported OS

## Requirements for RDS Custom for SQL Server CEVs<a name="custom-cev-sqlserver.preparing.Requirements"></a>

The following requirements apply to creating a CEV for RDS Custom for SQL Server:
+ The AMI used to create a CEV must be based on an OS and database configuration supported by RDS Custom for SQL Server\. For more information on supported configurations, see [Requirements and limitations for Amazon RDS Custom for SQL Server](custom-reqs-limits-MS.md)\.
+ The CEV must have a unique name\. You can't create a CEV with the same name as an existing CEV\.
+ You must name the CEV using a naming pattern of SQL Server *major version \+ minor version \+ customized string*\. The *major version \+ minor version* must match the SQL Server version provided with the AMI\. For example, you can name an AMI with SQL Server 2019 CU17 as **15\.00\.4249\.2\.my\_cevtest**\.
+ You must prepare an AMI using Sysprep\. For more information about prepping an AMI using Sysprep, see [Create a standardized Amazon Machine Image \(AMI\) using Sysprep](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/Creating_EBSbacked_WinAMI.html#sysprep-using-ec2launchv2)\.
+ You are responsible for maintaining the life cycle of the AMI\. An RDS Custom for SQL Server DB instance created from a CEV doesn't store a copy of the AMI\. It maintains a pointer to the AMI that you used to create the CEV\. The AMI must exist for an RDS Custom for SQL Server DB instance to remain operable\.

## Limitations for RDS Custom for SQL Server CEVs<a name="custom-cev-sqlserver.preparing.Limitations"></a>

The following limitations apply to custom engine versions with RDS Custom for SQL Server:
+ You can't delete a CEV if there are resources, such as DB instances or DB snapshots, associated with it\.
+ To create an RDS Custom for SQL Server DB instance, a CEV must have a status of `pending-validation`, `available`, `failed`, or `validating`\. You can't create an RDS Custom for SQL Server DB instance using a CEV if the CEV status is `incompatible-image-configuration`\.
+ To modify a RDS Custom for SQL Server DB instance to use a new CEV, the CEV must have a status of `available`\.
+ You can't create an AMI or CEV from an existing RDS Custom for SQL Server DB instance\.
+ You can't modify an existing CEV to use a different AMI\. However, you can modify an RDS Custom for SQL Server DB instance to use a different CEV\. For more information, see [Modifying an RDS Custom for SQL Server DB instance](custom-managing-sqlserver.md#custom-managing.modify-sqlserver)\.
+ Cross\-Region copy of CEVs isn't supported\.
+ Cross\-account copy of CEVs isn't supported\.
+ SQL Server Transparent Data Encryption \(TDE\) isn't supported\.
+ You can't restore or recover a CEV after you delete it\. However, you can create a new CEV from the same AMI\.
+ A RDS Custom for SQL Server DB instance stores your SQL Server database files in the *D:\\*drive\. The AMI associated with a CEV should store the Microsoft SQL Server system database files in the *C:\\* drive\.
+ An RDS Custom for SQL Server DB instance retains your configuration changes made to SQL Server\. Any configuration changes to the OS on a running RDS Custom for SQL Server DB instance created from a CEV aren't retained\. If you need to make a permanent configuration change to the OS and have it retained as your new baseline configuration, create a new CEV and modify the DB instance to use the new CEV\.
**Important**  
Modifying an RDS Custom for SQL Server DB instance to use a new CEV is an offline operation\. You can perform the modification immediately or schedule it to occur during a weekly maintenance window\.
+ When you modify a CEV, Amazon RDS doesn't push those modifications to any associated RDS Custom for SQL Server DB instances\. You must modify each RDS Custom for SQL Server DB instance to use the new or updated CEV\. For more information, see [Modifying an RDS Custom for SQL Server DB instance](custom-managing-sqlserver.md#custom-managing.modify-sqlserver)\.
+ 
**Important**  
If an AMI used by a CEV is deleted, any modifications that may require host replacement, for example, scale compute, will fail\. The RDS Custom for SQL Server DB instance will then be placed outside of the RDS support perimeter\. We recommend that you avoid deleting any AMI that's associated to a CEV\.