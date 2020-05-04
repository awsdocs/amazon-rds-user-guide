# Using SSL/TLS to Encrypt a Connection to a DB Instance<a name="UsingWithRDS.SSL"></a>

You can use Secure Socket Layer \(SSL\) or Transport Layer Security \(TLS\) from your application to encrypt a connection to a DB instance running MySQL, MariaDB, SQL Server, Oracle, or PostgreSQL\. Each DB engine has its own process for implementing SSL/TLS\. To learn how to implement SSL/TLS for your DB instance, use the link following that corresponds to your DB engine: 
+ [Using SSL with a MariaDB DB Instance](CHAP_MariaDB.md#MariaDB.Concepts.SSLSupport)
+ [Using SSL with a Microsoft SQL Server DB Instance](SQLServer.Concepts.General.SSL.Using.md)
+ [Using SSL with a MySQL DB Instance](CHAP_MySQL.md#MySQL.Concepts.SSLSupport)
+ [Using SSL with an Oracle DB Instance](CHAP_Oracle.md#Oracle.Concepts.SSL)
+ [Using SSL with a PostgreSQL DB Instance](CHAP_PostgreSQL.md#PostgreSQL.Concepts.General.SSL)

**Important**  
For information about rotating your certificate, see [Rotating Your SSL/TLS Certificate](UsingWithRDS.SSL-certificate-rotation.md)\.

**Note**  
All certificates are only available for download using SSL/TLS connections\.

To get a root certificate that works for all AWS Regions, excluding opt\-in AWS Regions, download it from [ https://s3\.amazonaws\.com/rds\-downloads/rds\-ca\-2019\-root\.pem](https://s3.amazonaws.com/rds-downloads/rds-ca-2019-root.pem)\.

This root certificate is a trusted root entity and should work in most cases but might fail if your application doesn't accept certificate chains\. If your application doesn't accept certificate chains, download the AWS Region–specific certificate from the list of intermediate certificates found later in this section\.

To get a certificate bundle that contains both the intermediate and root certificates, download from [ https://s3\.amazonaws\.com/rds\-downloads/rds\-combined\-ca\-bundle\.pem](https://s3.amazonaws.com/rds-downloads/rds-combined-ca-bundle.pem)\. 

If your application is on Microsoft Windows and requires a PKCS7 file, you can download the PKCS7 certificate bundle\. This bundle contains both the intermediate and root certificates at [ https://s3\.amazonaws\.com/rds\-downloads/rds\-combined\-ca\-bundle\.p7b](https://s3.amazonaws.com/rds-downloads/rds-combined-ca-bundle.p7b)\. 

**Note**  
Amazon RDS Proxy uses certificates from the AWS Certificate Manager \(ACM\)\. If you are using RDS Proxy, you don't need to download Amazon RDS certificates or update applications that use RDS Proxy connections\. For more information about using TLS/SSL with RDS Proxy, see [Using TLS/SSL with RDS Proxy](rds-proxy.md#rds-proxy-security.tls)\.

## Root Certificates for Opt\-In AWS Regions<a name="UsingWithRDS.SSL.RootCertificatesOptIn"></a>

If you are using an opt\-in AWS Region, you can download the root certificate from the following table\.


| **Opt\-In AWS Region** | **Root Certificate** | 
| --- | --- | 
| Africa \(Cape Town\) | [rds\-ca\-af\-south\-1\-2019\-root\.pem](https://s3.amazonaws.com/rds-downloads/rds-ca-af-south-1-2019-root.pem) | 
| Asia Pacific \(Hong Kong\) | [rds\-ca\-ap\-east\-1\-2019\-root\.pem](https://s3.amazonaws.com/rds-downloads/rds-ca-ap-east-1-2019-root.pem) | 
| Europe \(Milan\) | [rds\-ca\-eu\-south\-1\-2019\-root\.pem](https://s3.amazonaws.com/rds-downloads/rds-ca-eu-south-1-2019-root.pem) | 
| Middle East \(Bahrain\) | [rds\-ca\-me\-south\-1\-2019\-root\.pem](https://s3.amazonaws.com/rds-downloads/rds-ca-me-south-1-2019-root.pem) | 

## Intermediate Certificates<a name="UsingWithRDS.SSL.IntermediateCertificates"></a>

You might need to use an intermediate certificate to connect to your AWS Region\. For example, you must use an intermediate certificate to connect to the AWS GovCloud \(US\-West\) Region using SSL/TLS\. If you need an intermediate certificate for a particular AWS Region, download the certificate from the following table\.


| **AWS Region** | **Intermediate Certificate** | 
| --- | --- | 
| Asia Pacific \(Mumbai\) | [rds\-ca\-2019\-ap\-south\-1\.pem](https://s3.amazonaws.com/rds-downloads/rds-ca-2019-ap-south-1.pem) | 
| Asia Pacific \(Tokyo\) | [rds\-ca\-2019\-ap\-northeast\-1\.pem](https://s3.amazonaws.com/rds-downloads/rds-ca-2019-ap-northeast-1.pem) | 
| Asia Pacific \(Seoul\) | [rds\-ca\-2019\-ap\-northeast\-2\.pem](https://s3.amazonaws.com/rds-downloads/rds-ca-2019-ap-northeast-2.pem) | 
| Asia Pacific \(Osaka\-Local\) | [rds\-ca\-2019\-ap\-northeast\-3\.pem](https://s3.amazonaws.com/rds-downloads/rds-ca-2019-ap-northeast-3.pem) | 
| Asia Pacific \(Singapore\) | [rds\-ca\-2019\-ap\-southeast\-1\.pem](https://s3.amazonaws.com/rds-downloads/rds-ca-2019-ap-southeast-1.pem) | 
| Asia Pacific \(Sydney\) | [rds\-ca\-2019\-ap\-southeast\-2\.pem](https://s3.amazonaws.com/rds-downloads/rds-ca-2019-ap-southeast-2.pem) | 
| Canada \(Central\) | [rds\-ca\-2019\-ca\-central\-1\.pem](https://s3.amazonaws.com/rds-downloads/rds-ca-2019-ca-central-1.pem) | 
| Europe \(Frankfurt\) | [rds\-ca\-2019\-eu\-central\-1\.pem](https://s3.amazonaws.com/rds-downloads/rds-ca-2019-eu-central-1.pem) | 
| Europe \(Ireland\) | [rds\-ca\-2019\-eu\-west\-1\.pem](https://s3.amazonaws.com/rds-downloads/rds-ca-2019-eu-west-1.pem) | 
| Europe \(London\) | [rds\-ca\-2019\-eu\-west\-2\.pem](https://s3.amazonaws.com/rds-downloads/rds-ca-2019-eu-west-2.pem) | 
| Europe \(Paris\) | [rds\-ca\-2019\-eu\-west\-3\.pem](https://s3.amazonaws.com/rds-downloads/rds-ca-2019-eu-west-3.pem) | 
| Europe \(Stockholm\) | [rds\-ca\-2019\-eu\-north\-1\.pem](https://s3.amazonaws.com/rds-downloads/rds-ca-2019-eu-north-1.pem) | 
| South America \(São Paulo\) | [rds\-ca\-2019\-sa\-east\-1\.pem](https://s3.amazonaws.com/rds-downloads/rds-ca-2019-sa-east-1.pem) | 
| US East \(N\. Virginia\) | [rds\-ca\-2019\-us\-east\-1\.pem](https://s3.amazonaws.com/rds-downloads/rds-ca-2019-us-east-1.pem) | 
| US East \(Ohio\) | [rds\-ca\-2019\-us\-east\-2\.pem](https://s3.amazonaws.com/rds-downloads/rds-ca-2019-us-east-2.pem) | 
| US West \(N\. California\) | [rds\-ca\-2019\-us\-west\-1\.pem](https://s3.amazonaws.com/rds-downloads/rds-ca-2019-us-west-1.pem) | 
| US West \(Oregon\) | [rds\-ca\-2019\-us\-west\-2\.pem](https://s3.amazonaws.com/rds-downloads/rds-ca-2019-us-west-2.pem) | 

## AWS GovCloud \(US\) Certificates<a name="UsingWithRDS.SSL.GovCloudCertificates"></a>

You can download the root certificate for an AWS GovCloud \(US\) Region from the following list:

[AWS GovCloud \(US\-East\)](https://s3.us-gov-west-1.amazonaws.com/rds-downloads/rds-ca-us-gov-east-1-2017-root.pem) \(Root CA\-2017\)

[AWS GovCloud \(US\-West\)](https://s3.us-gov-west-1.amazonaws.com/rds-downloads/rds-ca-us-gov-west-1-2017-root.pem) \(Root CA\-2017\)

You can download the intermediate certificate for an AWS GovCloud \(US\) Region from the following list:

[AWS GovCloud \(US\-East\)](https://s3.us-gov-west-1.amazonaws.com/rds-downloads/rds-ca-2017-us-gov-east-1-intermediate.pem) \(CA\-2017\)

[AWS GovCloud \(US\-West\)](https://s3.us-gov-west-1.amazonaws.com/rds-downloads/rds-ca-2017-us-gov-west-1.pem) \(CA\-2017\)

[AWS GovCloud \(US\-West\)](https://s3.us-gov-west-1.amazonaws.com/rds-downloads/rds-ca-2012-us-gov-west-1.pem) \(CA\-2012\)

To get a certificate bundle that contains both the intermediate and root certificates for the AWS GovCloud \(US\) Regions, download from [ https://s3\.us\-gov\-west\-1\.amazonaws\.com/rds\-downloads/rds\-combined\-ca\-us\-gov\-bundle\.pem](https://s3.us-gov-west-1.amazonaws.com/rds-downloads/rds-combined-ca-us-gov-bundle.pem)\. 