# Licensing Microsoft SQL Server on Amazon RDS<a name="SQLServer.Concepts.General.Licensing"></a>

There are two licensing options available for Amazon RDS for Microsoft SQL Server: License Included and Bring Your Own License \(BYOL\)\. After you create a Microsoft SQL Server DB instance on Amazon RDS, you can change the licensing model by using the [AWS Management Console](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_ModifyInstance.SQLServer.html), the Amazon RDS API [ModifyDBInstance](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html) action, or the AWS CLI [modify\-db\-instance](http://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) command\. 

In accordance with Microsoft’s usage rights, SQL Server Web Edition can be used only to support public and Internet\-accessible webpages, websites, web applications, and web services\. For more information, see [AWS Service Terms](http://aws.amazon.com/serviceterms)\. 

## License Included<a name="SQLServer.Concepts.General.Licensing.LicenseIncluded"></a>

In the License Included model, you don't need to purchase SQL Server licenses separately\. AWS holds the license for the SQL Server database software\. License Included pricing includes the software license, underlying hardware resources, and Amazon RDS management capabilities\. 

The License Included model is supported on Amazon RDS for the following Microsoft SQL Server database editions: 

+ Microsoft SQL Server Enterprise Edition \(2008 R2, 2012, 2014, 2016, 2017\) 

+ Microsoft SQL Server Standard Edition \(2008 R2, 2012, 2014, 2016, 2017\) 

+ Microsoft SQL Server Web Edition \(2008 R2, 2012, 2014, 2016, 2017\) 

+ Microsoft SQL Server Express Edition \(2008 R2, 2012, 2014, 2016, 2017\) 

## Bring Your Own License \(BYOL\)<a name="SQLServer.Concepts.General.Licensing.BYOL"></a>

If you participate in Microsoft’s License Mobility program, you can bring your own license to Amazon RDS\. Microsoft’s License Mobility program allows Microsoft customers to easily move current on\-premises Microsoft Server application workloads to Amazon Web Services \(AWS\), without any additional Microsoft software license fees\. This benefit is available to Microsoft Volume Licensing customers with eligible server applications covered by active Microsoft Software Assurance contracts\. For the latest licensing terms, see Microsoft’s Product Use Rights\. 

The Bring Your Own License model is supported on Amazon RDS for the following Microsoft SQL Server database editions: 

+ Microsoft SQL Server Enterprise Edition \(2008 R2, 2012, 2014, 2016, 2017\) 

+ Microsoft SQL Server Standard Edition \(2008 R2, 2012, 2014, 2016, 2017\) 

For more information about License Mobility, see [SQL License Mobility](http://aws.amazon.com/windows/mslicensemobility/sql/)\. 

## Licensing Microsoft SQL Server Multi\-AZ Deployments<a name="SQLServer.Concepts.General.Licensing.MAZ"></a>

Amazon RDS supports Multi\-AZ deployments for DB instances running Microsoft SQL Server by using SQL Server Database Mirroring\. We recommend Multi\-AZ for production workloads\. For more information, see [Multi\-AZ Deployments for Microsoft SQL Server with Database Mirroring](USER_SQLServerMultiAZ.md)\. 

There are no additional licensing requirements for Multi\-AZ deployments\. 

+ License Included — Multi\-AZ deployments are already included in the software license\. 

+ Bring Your Own License — The secondary instance of a SQL Server Multi\-AZ deployment is passive\. The secondary instance does not take writes or provide reads until a failover occurs\. Therefore, you don't need a license for this secondary instance\. 

## Providing External License Information<a name="SQLServer.Concepts.General.Licensing.Validation"></a>

To use the Bring Your Own License model, you must provide your Microsoft License Mobility Agreement information in the **External Licenses** section of the Amazon RDS console\. Fill out the form once for each License Mobility Agreement you have with Microsoft\. 

**Note**  
In some cases, you provide your License Mobility Agreement information on the AWS website instead of the Amazon RDS console\. Use the [AWS website form](https://aws.amazon.com/rds/sqlserver/license-mobility-form/) instead of the console form if your DB instance is in the US East \(Ohio\), AWS GovCloud \(US\), or Asia Pacific \(Mumbai\) region\. 

The following image shows the License Mobility Agreement verification form on the Amazon RDS console\. 

![\[License Mobility Agreement Verification Form\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/SQLServer-Licensing.png)

## Restoring License\-Terminated DB Instances<a name="SQLServer.Concepts.General.Licensing.Restoring"></a>

Microsoft has requested that some Amazon RDS customers who did not report their Microsoft License Mobility information terminate their DB instance\. Amazon RDS takes snapshots of these DB instances, and you can restore from the snapshot to a new DB instance that has the License Included model\. 

For more information, see [Restoring License\-Terminated DB Instances](Appendix.SQLServer.CommonDBATasks.RestoreLTI.md)\. 

## Related Topics<a name="SQLServer.Concepts.General.Licensing.Related"></a>

+ [Microsoft SQL Server on Amazon RDS](CHAP_SQLServer.md)

+ [Creating a DB Instance Running the Microsoft SQL Server Database Engine](USER_CreateMicrosoftSQLServerInstance.md)