# Using SSL/TLS to encrypt a connection to a DB instance<a name="UsingWithRDS.SSL"></a>

You can use Secure Socket Layer \(SSL\) or Transport Layer Security \(TLS\) from your application to encrypt a connection to a DB instance running MySQL, MariaDB, SQL Server, Oracle, or PostgreSQL\. Each DB engine has its own process for implementing SSL/TLS\. To learn how to implement SSL/TLS for your DB instance, use the link following that corresponds to your DB engine: 

SSL/TLS connections provide one layer of security by encrypting data that moves between your client and a DB instance\. Using a server certificate provides an extra layer of security by validating that the connection is being made to an Amazon RDS DB instance\. It does so by checking the server certificate that is automatically installed on all DB instances that you provision\.

Each DB engine has its own process for implementing SSL/TLS\. To learn how to implement SSL/TLS for your DB cluster, use the link following that corresponds to your DB engine: 
+ [Using SSL with a MariaDB DB instance](CHAP_MariaDB.md#MariaDB.Concepts.SSLSupport)
+ [Using SSL with a Microsoft SQL Server DB instance](SQLServer.Concepts.General.SSL.Using.md)
+ [Using SSL with a MySQL DB instance](CHAP_MySQL.md#MySQL.Concepts.SSLSupport)
+ [Encrypting client connections with SSL](Oracle.Concepts.SSL.md)
+ [Using SSL with a PostgreSQL DB instance](PostgreSQL.Concepts.General.SSL.md)

**Note**  
All certificates are only available for download using SSL/TLS connections\.

To get a certificate bundle that contains both the intermediate and root certificates for all AWS Regions, download from [ https://truststore\.pki\.rds\.amazonaws\.com/global/global\-bundle\.pem](https://truststore.pki.rds.amazonaws.com/global/global-bundle.pem)\. 

If your application is on Microsoft Windows and requires a PKCS7 file, you can download the PKCS7 certificate bundle\. This bundle contains both the intermediate and root certificates at [ https://truststore\.pki\.rds\.amazonaws\.com/global/global\-bundle\.p7b](https://truststore.pki.rds.amazonaws.com/global/global-bundle.p7b)\. 

**Note**  
Amazon RDS Proxy uses certificates from the AWS Certificate Manager \(ACM\)\. If you are using RDS Proxy, you don't need to download Amazon RDS certificates or update applications that use RDS Proxy connections\. For more information about using TLS/SSL with RDS Proxy, see [Using TLS/SSL with RDS Proxy](rds-proxy.howitworks.md#rds-proxy-security.tls)\.

## Certificate bundles for AWS Regions<a name="UsingWithRDS.SSL.RegionCertificates"></a>

To get a certificate bundle that contains both the intermediate and root certificates for an AWS Region, download from the link for the AWS Region in the following table\.


| **AWS Region** | **Certificate bundle \(PEM\)** | **Certificate bundle \(PKCS7\)** | 
| --- | --- | --- | 
| US East \(N\. Virginia\) | [us\-east\-1\-bundle\.pem](https://truststore.pki.rds.amazonaws.com/us-east-1/us-east-1-bundle.pem) | [us\-east\-1\-bundle\.p7b](https://truststore.pki.rds.amazonaws.com/us-east-1/us-east-1-bundle.p7b) | 
| US East \(Ohio\) | [us\-east\-2\-bundle\.pem](https://truststore.pki.rds.amazonaws.com/us-east-2/us-east-2-bundle.pem) | [us\-east\-2\-bundle\.p7b](https://truststore.pki.rds.amazonaws.com/us-east-2/us-east-2-bundle.p7b) | 
| US West \(N\. California\) | [us\-west\-1\-bundle\.pem](https://truststore.pki.rds.amazonaws.com/us-west-1/us-west-1-bundle.pem) | [us\-west\-1\-bundle\.p7b](https://truststore.pki.rds.amazonaws.com/us-west-1/us-west-1-bundle.p7b) | 
| US West \(Oregon\) | [us\-west\-2\-bundle\.pem](https://truststore.pki.rds.amazonaws.com/us-west-2/us-west-2-bundle.pem) | [us\-west\-2\-bundle\.p7b](https://truststore.pki.rds.amazonaws.com/us-west-2/us-west-2-bundle.p7b) | 
| Africa \(Cape Town\) | [af\-south\-1\-bundle\.pem](https://truststore.pki.rds.amazonaws.com/af-south-1/af-south-1-bundle.pem) | [af\-south\-1\-bundle\.p7b](https://truststore.pki.rds.amazonaws.com/af-south-1/af-south-1-bundle.p7b) | 
| Asia Pacific \(Hong Kong\) | [ap\-east\-1\-bundle\.pem](https://truststore.pki.rds.amazonaws.com/ap-east-1/ap-east-1-bundle.pem) | [ap\-east\-1\-bundle\.p7b](https://truststore.pki.rds.amazonaws.com/ap-east-1/ap-east-1-bundle.p7b) | 
| Asia Pacific \(Mumbai\) | [ap\-south\-1\-bundle\.pem](https://truststore.pki.rds.amazonaws.com/ap-south-1/ap-south-1-bundle.pem) | [ap\-south\-1\-bundle\.p7b](https://truststore.pki.rds.amazonaws.com/ap-south-1/ap-south-1-bundle.p7b) | 
| Asia Pacific \(Osaka\) | [ap\-northeast\-3\-bundle\.pem](https://truststore.pki.rds.amazonaws.com/ap-northeast-3/ap-northeast-3-bundle.pem) | [ap\-northeast\-3\-bundle\.p7b](https://truststore.pki.rds.amazonaws.com/ap-northeast-3/ap-northeast-3-bundle.p7b) | 
| Asia Pacific \(Tokyo\) | [ap\-northeast\-1\-bundle\.pem](https://truststore.pki.rds.amazonaws.com/ap-northeast-1/ap-northeast-1-bundle.pem) | [ap\-northeast\-1\-bundle\.p7b](https://truststore.pki.rds.amazonaws.com/ap-northeast-1/ap-northeast-1-bundle.p7b) | 
| Asia Pacific \(Seoul\) | [ap\-northeast\-2\-bundle\.pem](https://truststore.pki.rds.amazonaws.com/ap-northeast-2/ap-northeast-2-bundle.pem) | [ap\-northeast\-2\-bundle\.p7b](https://truststore.pki.rds.amazonaws.com/ap-northeast-2/ap-northeast-2-bundle.p7b) | 
| Asia Pacific \(Singapore\) | [ap\-southeast\-1\-bundle\.pem](https://truststore.pki.rds.amazonaws.com/ap-southeast-1/ap-southeast-1-bundle.pem) | [ap\-southeast\-1\-bundle\.p7b](https://truststore.pki.rds.amazonaws.com/ap-southeast-1/ap-southeast-1-bundle.p7b) | 
| Asia Pacific \(Sydney\) | [ap\-southeast\-2\-bundle\.pem](https://truststore.pki.rds.amazonaws.com/ap-southeast-2/ap-southeast-2-bundle.pem) | [ap\-southeast\-2\-bundle\.p7b](https://truststore.pki.rds.amazonaws.com/ap-southeast-2/ap-southeast-2-bundle.p7b) | 
| Canada \(Central\) | [ca\-central\-1\-bundle\.pem](https://truststore.pki.rds.amazonaws.com/ca-central-1/ca-central-1-bundle.pem) | [ca\-central\-1\-bundle\.p7b](https://truststore.pki.rds.amazonaws.com/ca-central-1/ca-central-1-bundle.p7b) | 
| Europe \(Frankfurt\) | [eu\-central\-1\-bundle\.pem](https://truststore.pki.rds.amazonaws.com/eu-central-1/eu-central-1-bundle.pem) | [eu\-central\-1\-bundle\.p7b](https://truststore.pki.rds.amazonaws.com/eu-central-1/eu-central-1-bundle.p7b) | 
| Europe \(Ireland\) | [eu\-west\-1\-bundle\.pem](https://truststore.pki.rds.amazonaws.com/eu-west-1/eu-west-1-bundle.pem) | [eu\-west\-1\-bundle\.p7b](https://truststore.pki.rds.amazonaws.com/eu-west-1/eu-west-1-bundle.p7b) | 
| Europe \(London\) | [eu\-west\-2\-bundle\.pem](https://truststore.pki.rds.amazonaws.com/eu-west-2/eu-west-2-bundle.pem) | [eu\-west\-2\-bundle\.p7b](https://truststore.pki.rds.amazonaws.com/eu-west-2/eu-west-2-bundle.p7b) | 
| Europe \(Milan\) | [eu\-south\-1\-bundle\.pem](https://truststore.pki.rds.amazonaws.com/eu-south-1/eu-south-1-bundle.pem) | [eu\-south\-1\-bundle\.p7b](https://truststore.pki.rds.amazonaws.com/eu-south-1/eu-south-1-bundle.p7b) | 
| Europe \(Paris\) | [eu\-west\-3\-bundle\.pem](https://truststore.pki.rds.amazonaws.com/eu-west-3/eu-west-3-bundle.pem) | [eu\-west\-3\-bundle\.p7b](https://truststore.pki.rds.amazonaws.com/eu-west-3/eu-west-3-bundle.p7b) | 
| Europe \(Stockholm\) | [eu\-north\-1\-bundle\.pem](https://truststore.pki.rds.amazonaws.com/eu-north-1/eu-north-1-bundle.pem) | [eu\-north\-1\-bundle\.p7b](https://truststore.pki.rds.amazonaws.com/eu-north-1/eu-north-1-bundle.p7b) | 
| Middle East \(Bahrain\) | [me\-south\-1\-bundle\.pem](https://truststore.pki.rds.amazonaws.com/me-south-1/me-south-1-bundle.pem) | [me\-south\-1\-bundle\.p7b](https://truststore.pki.rds.amazonaws.com/me-south-1/me-south-1-bundle.p7b) | 
| South America \(São Paulo\) | [sa\-east\-1\-bundle\.pem](https://truststore.pki.rds.amazonaws.com/sa-east-1/sa-east-1-bundle.pem) | [sa\-east\-1\-bundle\.p7b](https://truststore.pki.rds.amazonaws.com/sa-east-1/sa-east-1-bundle.p7b) | 

## AWS GovCloud \(US\) certificates<a name="UsingWithRDS.SSL.GovCloudCertificates"></a>

To get a certificate bundle that contains both the intermediate and root certificates for the AWS GovCloud \(US\) Regions, download from [ https://truststore\.pki\.us\-gov\-west\-1\.rds\.amazonaws\.com/global/global\-bundle\.pem](https://truststore.pki.us-gov-west-1.rds.amazonaws.com/global/global-bundle.pem)\. 

If your application is on Microsoft Windows and requires a PKCS7 file, you can download the PKCS7 certificate bundle\. This bundle contains both the intermediate and root certificates at [ https://truststore\.pki\.us\-gov\-west\-1\.rds\.amazonaws\.com/global/global\-bundle\.p7b](https://truststore.pki.us-gov-west-1.rds.amazonaws.com/global/global-bundle.p7b)\. 

To get a certificate bundle that contains both the intermediate and root certificates for an AWS GovCloud \(US\) Region, download from the link for the AWS GovCloud \(US\) Region in the following table\.


| **AWS GovCloud \(US\) Region** | **Certificate bundle \(PEM\)** | **Certificate bundle \(PKCS7\)** | 
| --- | --- | --- | 
| AWS GovCloud \(US\-East\) | [us\-gov\-east\-1\-bundle\.pem](https://truststore.pki.us-gov-west-1.rds.amazonaws.com/us-gov-east-1/us-gov-east-1-bundle.pem) | [us\-gov\-east\-1\-bundle\.p7b](https://truststore.pki.us-gov-west-1.rds.amazonaws.com/us-gov-east-1/us-gov-east-1-bundle.p7b) | 
| AWS GovCloud \(US\-West\) | [us\-gov\-west\-1\-bundle\.pem](https://truststore.pki.us-gov-west-1.rds.amazonaws.com/us-gov-west-1/us-gov-west-1-bundle.pem) | [us\-gov\-west\-1\-bundle\.p7b](https://truststore.pki.us-gov-west-1.rds.amazonaws.com/us-gov-west-1/us-gov-west-1-bundle.p7b) | 