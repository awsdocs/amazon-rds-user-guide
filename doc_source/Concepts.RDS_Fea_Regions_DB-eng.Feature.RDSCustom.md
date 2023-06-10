# RDS Custom<a name="Concepts.RDS_Fea_Regions_DB-eng.Feature.RDSCustom"></a>

Amazon RDS Custom automates database administration tasks and operations\. By using RDS Custom, as a database administrator you can access and customize your database environment and operating system\. With RDS Custom, you can customize to meet the requirements of legacy, custom, and packaged applications\. For more information, see [Working with Amazon RDS Custom](rds-custom.md)\.

RDS Custom is supported for the following DB engines only:
+ RDS for Oracle
+ RDS for SQL Server

**Topics**
+ [RDS Custom for Oracle](#Concepts.RDS_Fea_Regions_DB-eng.Feature.RDSCustom.ora)
+ [RDS Custom for SQL Server](#Concepts.RDS_Fea_Regions_DB-eng.Feature.RDSCustom.sq)

## RDS Custom for Oracle<a name="Concepts.RDS_Fea_Regions_DB-eng.Feature.RDSCustom.ora"></a>

The following Regions and engine versions are available for RDS Custom for Oracle\.


| Region | RDS for Oracle 19c | RDS for Oracle 18c | RDS for Oracle 12c | 
| --- | --- | --- | --- | 
| US East \(Ohio\) | 19c with the January 2021 or higher RU/RUR | 18c with the January 2021 or higher RU/RUR | 12\.1 and 12\.2 with the January 2021 or higher RU/RUR | 
| US East \(N\. Virginia\) | 19c with the January 2021 or higher RU/RUR | 18c with the January 2021 or higher RU/RUR | 12\.1 and 12\.2 with the January 2021 or higher RU/RUR | 
| US West \(N\. California\) | – | – | – | 
| US West \(Oregon\) | 19c with the January 2021 or higher RU/RUR | 18c with the January 2021 or higher RU/RUR | 12\.1 and 12\.2 with the January 2021 or higher RU/RUR | 
| Africa \(Cape Town\) | – | – | – | 
| Asia Pacific \(Hong Kong\) | – | – | – | 
| Asia Pacific \(Jakarta\) | – | – | – | 
| Asia Pacific \(Melbourne\) | – | – | – | 
| Asia Pacific \(Mumbai\) | 19c with the January 2021 or higher RU/RUR | 18c with the January 2021 or higher RU/RUR | 12\.1 and 12\.2 with the January 2021 or higher RU/RUR | 
| Asia Pacific \(Osaka\) | 19c with the January 2021 or higher RU/RUR | 18c with the January 2021 or higher RU/RUR | 12\.1 and 12\.2 with the January 2021 or higher RU/RUR | 
| Asia Pacific \(Seoul\) | 19c with the January 2021 or higher RU/RUR | 18c with the January 2021 or higher RU/RUR | 12\.1 and 12\.2 with the January 2021 or higher RU/RUR | 
| Asia Pacific \(Singapore\) | 19c with the January 2021 or higher RU/RUR | 18c with the January 2021 or higher RU/RUR | 12\.1 and 12\.2 with the January 2021 or higher RU/RUR | 
| Asia Pacific \(Sydney\) | 19c with the January 2021 or higher RU/RUR | 18c with the January 2021 or higher RU/RUR | 12\.1 and 12\.2 with the January 2021 or higher RU/RUR | 
| Asia Pacific \(Tokyo\) | 19c with the January 2021 or higher RU/RUR | 18c with the January 2021 or higher RU/RUR | 12\.1 and 12\.2 with the January 2021 or higher RU/RUR | 
| Canada \(Central\) | 19c with the January 2021 or higher RU/RUR | 18c with the January 2021 or higher RU/RUR | 12\.1 and 12\.2 with the January 2021 or higher RU/RUR | 
| China \(Beijing\) | – | – | – | 
| China \(Ningxia\) | – | – | – | 
| Europe \(Frankfurt\) | 19c with the January 2021 or higher RU/RUR | 18c with the January 2021 or higher RU/RUR | 12\.1 and 12\.2 with the January 2021 or higher RU/RUR | 
| Europe \(Ireland\) | 19c with the January 2021 or higher RU/RUR | 18c with the January 2021 or higher RU/RUR | 12\.1 and 12\.2 with the January 2021 or higher RU/RUR | 
| Europe \(London\) | 19c with the January 2021 or higher RU/RUR | 18c with the January 2021 or higher RU/RUR | 12\.1 and 12\.2 with the January 2021 or higher RU/RUR | 
| Europe \(Milan\) | – | – | – | 
| Europe \(Paris\) | – | – | – | 
| Europe \(Stockholm\) | 19c with the January 2021 or higher RU/RUR | 18c with the January 2021 or higher RU/RUR | 12\.1 and 12\.2 with the January 2021 or higher RU/RUR | 
| Middle East \(Bahrain\) | – | – | – | 
| Middle East \(UAE\) | – | – | – | 
| South America \(São Paulo\) | 19c with the January 2021 or higher RU/RUR | 18c with the January 2021 or higher RU/RUR | 12\.1 and 12\.2 with the January 2021 or higher RU/RUR | 
| AWS GovCloud \(US\-East\) | – | – | – | 
| AWS GovCloud \(US\-West\) | – | – | – | 

## RDS Custom for SQL Server<a name="Concepts.RDS_Fea_Regions_DB-eng.Feature.RDSCustom.sq"></a>

You can deploy RDS Custom for SQL Server by using either an RDS provided engine version \(RPEV\) or a custom engine version \(CEV\):
+ If you use an RPEV, it includes the default Amazon Machine Image \(AMI\) and SQL Server installation\. If you customize or modify the operating system \(OS\), your changes might not persist during patching, snapshot restore, or automatic recovery\.
+ If you use a CEV, you choose your own AMI with either pre\-installed Microsoft SQL Server or SQL Server that you install using your own media\. When using an AWS provided CEV, you choose the latest Amazon EC2 image \(AMI\) available by AWS, which has the cumulative update \(CU\) supported by RDS Custom for SQL Server\. With a CEV, you can customize both the OS and SQL Server configuration to meet your enterprise needs\.

The following AWS Regions and DB engine versions are available for RDS Custom for SQL Server\. The engine version support depends on whether you're using RDS Custom for SQL Server with an RPEV, AWS provided CEV, or customer\-provided CEV\.


| Region | RPEV | AWS provided CEV | Customer\-provided CEV | 
| --- | --- | --- | --- | 
| US East \(Ohio\) | Enterprise, Standard, or Web SQL Server 2019 with CU8, CU17, CU18, CU20 | Enterprise, Standard, or Web SQL Server 2019 with CU17, CU18, CU20 | Enterprise or Standard SQL Server 2019 with CU17, CU18, CU20 | 
| US East \(N\. Virginia\) | Enterprise, Standard, or Web SQL Server 2019 with CU8, CU17, CU18, CU20  | Enterprise, Standard, or Web SQL Server 2019 with CU17, CU18, CU20  | Enterprise or Standard SQL Server 2019 with CU17, CU18, CU20 | 
| US West \(N\. California\) | – | – | – | 
| US West \(Oregon\) | Enterprise, Standard, or Web SQL Server 2019 with CU8, CU17, CU18, CU20  | Enterprise, Standard, or Web SQL Server 2019 with CU17, CU18, CU20  | Enterprise or Standard SQL Server 2019 with CU17, CU18, CU20 | 
| Africa \(Cape Town\) | – | – | – | 
| Asia Pacific \(Hong Kong\) | – | – | – | 
| Asia Pacific \(Hyderabad\) | – | – | – | 
| Asia Pacific \(Jakarta\) | – | – | – | 
| Asia Pacific \(Melbourne\) | – | – | – | 
| Asia Pacific \(Mumbai\) | Enterprise, Standard, or Web SQL Server 2019 with CU8, CU17, CU18, CU20  | Enterprise, Standard, or Web SQL Server 2019 with CU17, CU18, CU20  | Enterprise or Standard SQL Server 2019 with CU17, CU18, CU20 | 
| Asia Pacific \(Osaka\) | – | – | – | 
| Asia Pacific \(Seoul\) | Enterprise, Standard, or Web SQL Server 2019 with CU8, CU17, CU18, CU20  | Enterprise, Standard, or Web SQL Server 2019 with CU17, CU18, CU20  | Enterprise or Standard SQL Server 2019 with CU17, CU18, CU20 | 
| Asia Pacific \(Singapore\) | Enterprise, Standard, or Web SQL Server 2019 with CU8, CU17, CU18, CU20  | Enterprise, Standard, or Web SQL Server 2019 with CU17, CU18, CU20  | Enterprise or Standard SQL Server 2019 with CU17, CU18, CU20 | 
| Asia Pacific \(Sydney\) | Enterprise, Standard, or Web SQL Server 2019 with CU8, CU17, CU18, CU20  | Enterprise, Standard, or Web SQL Server 2019 with CU17, CU18, CU20  | Enterprise or Standard SQL Server 2019 with CU17, CU18, CU20 | 
| Asia Pacific \(Tokyo\) | Enterprise, Standard, or Web SQL Server 2019 with CU8, CU17, CU18, CU20  | Enterprise, Standard, or Web SQL Server 2019 with CU17, CU18, CU20  | Enterprise or Standard SQL Server 2019 with CU17, CU18, CU20 | 
| Canada \(Central\) | Enterprise, Standard, or Web SQL Server 2019 with CU8, CU17, CU18, CU20  | Enterprise, Standard, or Web SQL Server 2019 with CU17, CU18, CU20  | Enterprise or Standard SQL Server 2019 with CU17, CU18, CU20 | 
| China \(Beijing\) | – | – | – | 
| China \(Ningxia\) | – | – | – | 
| Europe \(Frankfurt\) | Enterprise, Standard, or Web SQL Server 2019 with CU8, CU17, CU18, CU20  | Enterprise, Standard, or Web SQL Server 2019 with CU17, CU18, CU20  | Enterprise or Standard SQL Server 2019 with CU17, CU18, CU20 | 
| Europe \(Ireland\) | Enterprise, Standard, or Web SQL Server 2019 with CU8, CU17, CU18, CU20  | Enterprise, Standard, or Web SQL Server 2019 with CU17, CU18, CU20  | Enterprise or Standard SQL Server 2019 with CU17, CU18, CU20 | 
| Europe \(London\) | Enterprise, Standard, or Web SQL Server 2019 with CU8, CU17, CU18, CU20  | Enterprise, Standard, or Web SQL Server 2019 with CU17, CU18, CU20  | Enterprise or Standard SQL Server 2019 with CU17, CU18, CU20 | 
| Europe \(Milan\) | – | – | – | 
| Europe \(Paris\) | – | – | – | 
| Europe \(Spain\) | – | – | – | 
| Europe \(Stockholm\) | Enterprise, Standard, or Web SQL Server 2019 with CU8, CU17, CU18, CU20  | Enterprise, Standard, or Web SQL Server 2019 with CU17, CU18, CU20  | Enterprise or Standard SQL Server 2019 with CU17, CU18, CU20 | 
| Europe \(Zurich\) | – | – | – | 
| Middle East \(Bahrain\) | – | – | – | 
| Middle East \(UAE\) | – | – | – | 
| South America \(São Paulo\) | Enterprise, Standard, or Web SQL Server 2019 with CU8, CU17, CU18, CU20  | Enterprise, Standard, or Web SQL Server 2019 with CU17, CU18, CU20  | Enterprise or Standard SQL Server 2019 with CU17, CU18, CU20 | 
| AWS GovCloud \(US\-East\) | – | – | – | 
| AWS GovCloud \(US\-West\) | – | – | – | 