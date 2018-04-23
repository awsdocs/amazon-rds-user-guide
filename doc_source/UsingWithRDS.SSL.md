# Using SSL to Encrypt a Connection to a DB Instance<a name="UsingWithRDS.SSL"></a>

You can use SSL from your application to encrypt a connection to a DB instance running MySQL, MariaDB, Amazon Aurora, SQL Server, Oracle, or PostgreSQL\. Each DB engine has its own process for implementing SSL\. To learn how to implement SSL for your DB instance, use the link following that corresponds to your DB engine: 
+ [Using SSL with Aurora DB Clusters](Aurora.Overview.md#Aurora.Overview.Security.SSL)
+ [Using SSL with a MariaDB DB Instance](CHAP_MariaDB.md#MariaDB.Concepts.SSLSupport)
+ [Using SSL with a Microsoft SQL Server DB Instance](SQLServer.Concepts.General.SSL.Using.md)
+ [Using SSL with a MySQL DB Instance](CHAP_MySQL.md#MySQL.Concepts.SSLSupport)
+ [Using SSL with an Oracle DB Instance](CHAP_Oracle.md#Oracle.Concepts.SSL)
+ [Using SSL with a PostgreSQL DB Instance](CHAP_PostgreSQL.md#PostgreSQL.Concepts.General.SSL)

A root certificate that works for all regions can be downloaded at [ https://s3\.amazonaws\.com/rds\-downloads/rds\-ca\-2015\-root\.pem](https://s3.amazonaws.com/rds-downloads/rds-ca-2015-root.pem)\. It is the trusted root entity and should work in most cases but might fail if your application doesn't accept certificate chains\. If your application doesn't accept certificate chains, download the AWS Region–specific certificate from the list of intermediate certificates found later in this section\. 

**Note**  
All certificates are only available for download using SSL connections\.

A certificate bundle that contains both the old and new root certificates can be downloaded at [ https://s3\.amazonaws\.com/rds\-downloads/rds\-combined\-ca\-bundle\.pem ](https://s3.amazonaws.com/rds-downloads/rds-combined-ca-bundle.pem)\. 

If your application is on the Microsoft Windows platform and requires a PKCS7 file, you can download the PKCS7 certificate bundle that contains both the old and new certificates at [ https://s3\.amazonaws\.com/rds\-downloads/rds\-combined\-ca\-bundle\.p7b ](https://s3.amazonaws.com/rds-downloads/rds-combined-ca-bundle.p7b)\. 

## Intermediate Certificates<a name="UsingWithRDS.SSL.IntermediateCertificates"></a>

You might need to use an intermediate certificate to connect to your region\. For example, you must use an intermediate certificate to connect to the AWS GovCloud \(US\) region using SSL\. If you need an intermediate certificate for a particular AWS Region, download the certificate from the following list:

[Asia Pacific \(Mumbai\)](https://s3.amazonaws.com/rds-downloads/rds-ca-2015-ap-south-1.pem)

[Asia Pacific \(Tokyo\)](https://s3.amazonaws.com/rds-downloads/rds-ca-2015-ap-northeast-1.pem)

[Asia Pacific \(Seoul\)](https://s3.amazonaws.com/rds-downloads/rds-ca-2015-ap-northeast-2.pem)

[Asia Pacific \(Osaka\-Local\)](https://s3.amazonaws.com/rds-downloads/rds-ca-2015-ap-northeast-3.pem)

[Asia Pacific \(Singapore\)](https://s3.amazonaws.com/rds-downloads/rds-ca-2015-ap-southeast-1.pem)

[Asia Pacific \(Sydney\)](https://s3.amazonaws.com/rds-downloads/rds-ca-2015-ap-southeast-2.pem)

[Canada \(Central\)](https://s3.amazonaws.com/rds-downloads/rds-ca-2015-ca-central-1.pem)

[China \(Beijing\)](https://s3.amazonaws.com/rds-downloads/rds-cn-north-1-ca-certificate.pem)

[China \(Ningxia\)](https://s3.amazonaws.com/rds-downloads/rds-ca-cn-northwest-1.pem)

[EU \(Frankfurt\)](https://s3.amazonaws.com/rds-downloads/rds-ca-2015-eu-central-1.pem)

[EU \(Ireland\)](https://s3.amazonaws.com/rds-downloads/rds-ca-2015-eu-west-1.pem)

[EU \(London\)](https://s3.amazonaws.com/rds-downloads/rds-ca-2015-eu-west-2.pem)

[EU \(Paris\)](https://s3.amazonaws.com/rds-downloads/rds-ca-2015-eu-west-3.pem)

[South America \(São Paulo\)](https://s3.amazonaws.com/rds-downloads/rds-ca-2015-sa-east-1.pem)

[US East \(N\. Virginia\)](https://s3.amazonaws.com/rds-downloads/rds-ca-2015-us-east-1.pem)

[US East \(Ohio\)](https://s3.amazonaws.com/rds-downloads/rds-ca-2015-us-east-2.pem)

[US West \(N\. California\)](https://s3.amazonaws.com/rds-downloads/rds-ca-2015-us-west-1.pem)

[US West \(Oregon\)](https://s3.amazonaws.com/rds-downloads/rds-ca-2015-us-west-2.pem)

[ AWS GovCloud \(US\)](https://s3-us-gov-west-1.amazonaws.com/rds-downloads/rds-ca-2012-us-gov-west-1.pem) \(CA\-2012; for CA\-2017, see following\)