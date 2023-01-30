# Using SSL/TLS to encrypt a connection to a DB instance<a name="UsingWithRDS.SSL"></a>

You can use Secure Socket Layer \(SSL\) or Transport Layer Security \(TLS\) from your application to encrypt a connection to a DB instance running MariaDB, Microsoft SQL Server, MySQL, Oracle, or PostgreSQL\.

SSL/TLS connections provide one layer of security by encrypting data that moves between your client and a DB instance\. Using a server certificate provides an extra layer of security by validating that the connection is being made to an Amazon RDS DB instance\. It does so by checking the server certificate that is automatically installed on all DB instances that you provision\.

Each DB engine has its own process for implementing SSL/TLS\. To learn how to implement SSL/TLS for your DB instance, use the link following that corresponds to your DB engine: 
+ [Using SSL/TLS with a MariaDB DB instance](mariadb-ssl-connections.md#MariaDB.Concepts.SSLSupport)
+ [Using SSL with a Microsoft SQL Server DB instance](SQLServer.Concepts.General.SSL.Using.md)
+ [Using SSL/TLS with a MySQL DB instance](mysql-ssl-connections.md#MySQL.Concepts.SSLSupport)
+ [Using SSL with an RDS for Oracle DB instance](Oracle.Concepts.SSL.md)
+ [Using SSL with a PostgreSQL DB instance](PostgreSQL.Concepts.General.SSL.md)

**Note**  
All certificates are only available for download using SSL/TLS connections\.

## Certificate authorities<a name="UsingWithRDS.SSL.RegionCertificateAuthorities"></a>

The certificate authority \(CA\) is the certificate that identifies the root CA at the top of the certificate chain\. The CA signs the DB instance certificate, which is the server certificate that is installed on each DB instance\. The server certificate identifies the DB instance as a trusted server\.

Amazon RDS provides the following CAs to sign the server certificate for a DB instance\.


****  

| Certificate authority \(CA\) | Description | 
| --- | --- | 
|  rds\-ca\-2019  |  Uses a certificate authority with RSA 2048 private key algorithm and SHA256 signing algorithm for your DB instance server certificate\. This CA expires in 2024 and doesn't support automatic server certificate rotation\. If you are using this CA and want to keep the same standard, we recommend that you switch to the rds\-ca\-rsa2048\-g1 CA\.  | 
|  rds\-ca\-rsa2048\-g1  |  Uses a certificate authority with RSA 2048 private key algorithm and SHA256 signing algorithm for your DB instance server certificate in most AWS Regions\. In the AWS GovCloud \(US\) Regions, this CA uses a certificate authority with RSA 2048 private key algorithm and SHA384 signing algorithm for your DB instance server certificate\. This CA remains valid for longer than the rds\-ca\-2019 CA\. This CA supports automatic server certificate rotation\.  | 
|  rds\-ca\-rsa4096\-g1  |  Uses a certificate authority with RSA 4096 private key algorithm and SHA384 signing algorithm for your DB instance server certificate\. This CA supports automatic server certificate rotation\.  | 
|  rds\-ca\-ecc384\-g1  |  Uses a certificate authority with ECC 384 private key algorithm and SHA384 signing algorithm for your DB instance server certificate\. This CA supports automatic server certificate rotation\.  | 

When you use the rds\-ca\-rsa2048\-g1, rds\-ca\-rsa4096\-g1, or rds\-ca\-ecc384\-g1 CA with a DB instance, RDS manages the server certificate on the DB instance\. RDS rotates it automatically before it expires\. These CA certificates are included in the regional and global certificate bundle\.

You can set the CA for a DB instance when you perform the following tasks:
+ Create a DB instance – You can set the CA when you create a DB instance\. For instructions, see [Creating an Amazon RDS DB instance](USER_CreateDBInstance.md)\.
+ Modify a DB instance – You can set the CA for a DB instance by modifying it\. For instructions, see [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)\.

The available CAs depend on the DB engine and DB engine version\. When you use the AWS Management Console, you can choose the CA using the **Certificate authority** setting, as shown in the following image\.

![\[Certificate authority option\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/certificate-authority.png)

The console only shows the CAs that are available for the DB engine and DB engine version\. If you are using the AWS CLI, you can set the CA for a DB instance using the [create\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html) or [modify\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) command\.

If you are using the AWS CLI, you can see the available CAs for your account by using the [describe\-certificates](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-certificates.html) command\. This command also shows the expiration date for each CA in `ValidTill` in the output\. You can find the CAs that are available for a specific DB engine and DB engine version using the [describe\-db\-engine\-versions](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-certificates.html) command\.

The following example shows the CAs available for the default RDS for PostgreSQL DB engine version\.

```
aws rds describe-db-engine-versions --default-only --engine postgres
```

Your output is similar to the following\. The available CAs are listed in `SupportedCACertificatesIdentifiers`\. The output also shows whether the DB engine version supports rotating the certificate without restart in `SupportsCertificateRotationWithoutRestart`\.

```
{
    "DBEngineVersions": [
        {
            "Engine": "postgres",
            "MajorEngineVersion": "13",
            "EngineVersion": "13.4",
            "DBParameterGroupFamily": "postgres13",
            "DBEngineDescription": "PostgreSQL",
            "DBEngineVersionDescription": "PostgreSQL 13.4-R1",
            "ValidUpgradeTarget": [],
            "SupportsLogExportsToCloudwatchLogs": false,
            "SupportsReadReplica": true,
            "SupportedFeatureNames": [
                "Lambda"
            ],
            "Status": "available",
            "SupportsParallelQuery": false,
            "SupportsGlobalDatabases": false,
            "SupportsBabelfish": false,
            "SupportsCertificateRotationWithoutRestart": true,
            "SupportedCACertificatesIdentifiers": [
                "rds-ca-2019",
                "rds-ca-rsa2048-g1",
                "rds-ca-ecc384-g1",
                "rds-ca-rsa4096-g1"
            ]
        }
    ]
}
```

You can view the details about the CA for a DB instance by viewing the **Connectivity & security** tab in the console, as in the following image\.

![\[Certificate authority details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/certificate-authority-details.png)

If you are using the AWS CLI, you can view the details about the CA for a DB instance by using the [describe\-db\-instances](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-instances.html) command\.

## Certificate bundles for all AWS Regions<a name="UsingWithRDS.SSL.CertificatesAllRegions"></a>

To get a certificate bundle that contains both the intermediate and root certificates for all AWS Regions, download it from [ https://truststore\.pki\.rds\.amazonaws\.com/global/global\-bundle\.pem](https://truststore.pki.rds.amazonaws.com/global/global-bundle.pem)\. 

If your application is on Microsoft Windows and requires a PKCS7 file, you can download the PKCS7 certificate bundle\. This bundle contains both the intermediate and root certificates at [ https://truststore\.pki\.rds\.amazonaws\.com/global/global\-bundle\.p7b](https://truststore.pki.rds.amazonaws.com/global/global-bundle.p7b)\.

**Note**  
Amazon RDS Proxy uses certificates from the AWS Certificate Manager \(ACM\)\. If you are using RDS Proxy, you don't need to download Amazon RDS certificates or update applications that use RDS Proxy connections\. For more information about using TLS/SSL with RDS Proxy, see [Using TLS/SSL with RDS Proxy](rds-proxy.howitworks.md#rds-proxy-security.tls)\.

## Certificate bundles for specific AWS Regions<a name="UsingWithRDS.SSL.RegionCertificates"></a>

To get a certificate bundle that contains both the intermediate and root certificates for an AWS Region, download it from the link for the AWS Region in the following table\.


| **AWS Region** | **Certificate bundle \(PEM\)** | **Certificate bundle \(PKCS7\)** | 
| --- | --- | --- | 
| US East \(N\. Virginia\) | [us\-east\-1\-bundle\.pem](https://truststore.pki.rds.amazonaws.com/us-east-1/us-east-1-bundle.pem) | [us\-east\-1\-bundle\.p7b](https://truststore.pki.rds.amazonaws.com/us-east-1/us-east-1-bundle.p7b) | 
| US East \(Ohio\) | [us\-east\-2\-bundle\.pem](https://truststore.pki.rds.amazonaws.com/us-east-2/us-east-2-bundle.pem) | [us\-east\-2\-bundle\.p7b](https://truststore.pki.rds.amazonaws.com/us-east-2/us-east-2-bundle.p7b) | 
| US West \(N\. California\) | [us\-west\-1\-bundle\.pem](https://truststore.pki.rds.amazonaws.com/us-west-1/us-west-1-bundle.pem) | [us\-west\-1\-bundle\.p7b](https://truststore.pki.rds.amazonaws.com/us-west-1/us-west-1-bundle.p7b) | 
| US West \(Oregon\) | [us\-west\-2\-bundle\.pem](https://truststore.pki.rds.amazonaws.com/us-west-2/us-west-2-bundle.pem) | [us\-west\-2\-bundle\.p7b](https://truststore.pki.rds.amazonaws.com/us-west-2/us-west-2-bundle.p7b) | 
| Africa \(Cape Town\) | [af\-south\-1\-bundle\.pem](https://truststore.pki.rds.amazonaws.com/af-south-1/af-south-1-bundle.pem) | [af\-south\-1\-bundle\.p7b](https://truststore.pki.rds.amazonaws.com/af-south-1/af-south-1-bundle.p7b) | 
| Asia Pacific \(Hong Kong\) | [ap\-east\-1\-bundle\.pem](https://truststore.pki.rds.amazonaws.com/ap-east-1/ap-east-1-bundle.pem) | [ap\-east\-1\-bundle\.p7b](https://truststore.pki.rds.amazonaws.com/ap-east-1/ap-east-1-bundle.p7b) | 
| Asia Pacific \(Hyderabad\) | [ap\-south\-2\-bundle\.pem](https://truststore.pki.rds.amazonaws.com/ap-south-2/ap-south-2-bundle.pem) | [ap\-south\-2\-bundle\.p7b](https://truststore.pki.rds.amazonaws.com/ap-south-2/ap-south-2-bundle.p7b) | 
| Asia Pacific \(Jakarta\) | [ap\-southeast\-3\-bundle\.pem](https://truststore.pki.rds.amazonaws.com/ap-southeast-3/ap-southeast-3-bundle.pem) | [ap\-southeast\-3\-bundle\.p7b](https://truststore.pki.rds.amazonaws.com/ap-southeast-3/ap-southeast-3-bundle.p7b) | 
| Asia Pacific \(Melbourne\) | [ap\-southeast\-4\-bundle\.pem](https://truststore.pki.rds.amazonaws.com/ap-southeast-4/ap-southeast-4-bundle.pem) | [ap\-southeast\-4\-bundle\.p7b](https://truststore.pki.rds.amazonaws.com/ap-southeast-4/ap-southeast-4-bundle.p7b) | 
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
| Europe \(Spain\) | [eu\-south\-2\-bundle\.pem](https://truststore.pki.rds.amazonaws.com/eu-south-2/eu-south-2-bundle.pem) | [eu\-south\-2\-bundle\.p7b](https://truststore.pki.rds.amazonaws.com/eu-south-2/eu-south-2-bundle.p7b) | 
| Europe \(Stockholm\) | [eu\-north\-1\-bundle\.pem](https://truststore.pki.rds.amazonaws.com/eu-north-1/eu-north-1-bundle.pem) | [eu\-north\-1\-bundle\.p7b](https://truststore.pki.rds.amazonaws.com/eu-north-1/eu-north-1-bundle.p7b) | 
| Europe \(Zurich\) | [eu\-central\-2\-bundle\.pem](https://truststore.pki.rds.amazonaws.com/eu-central-2/eu-central-2-bundle.pem) | [eu\-central\-2\-bundle\.p7b](https://truststore.pki.rds.amazonaws.com/eu-central-2/eu-central-2-bundle.p7b) | 
| Middle East \(Bahrain\) | [me\-south\-1\-bundle\.pem](https://truststore.pki.rds.amazonaws.com/me-south-1/me-south-1-bundle.pem) | [me\-south\-1\-bundle\.p7b](https://truststore.pki.rds.amazonaws.com/me-south-1/me-south-1-bundle.p7b) | 
| Middle East \(UAE\) | [me\-central\-1\-bundle\.pem](https://truststore.pki.rds.amazonaws.com/me-central-1/me-central-1-bundle.pem) | [me\-central\-1\-bundle\.p7b](https://truststore.pki.rds.amazonaws.com/me-central-1/me-central-1-bundle.p7b) | 
| South America \(São Paulo\) | [sa\-east\-1\-bundle\.pem](https://truststore.pki.rds.amazonaws.com/sa-east-1/sa-east-1-bundle.pem) | [sa\-east\-1\-bundle\.p7b](https://truststore.pki.rds.amazonaws.com/sa-east-1/sa-east-1-bundle.p7b) | 

## AWS GovCloud \(US\) certificates<a name="UsingWithRDS.SSL.GovCloudCertificates"></a>

To get a certificate bundle that contains both the intermediate and root certificates for the AWS GovCloud \(US\) Regions, download from [ https://truststore\.pki\.us\-gov\-west\-1\.rds\.amazonaws\.com/global/global\-bundle\.pem](https://truststore.pki.us-gov-west-1.rds.amazonaws.com/global/global-bundle.pem)\. 

If your application is on Microsoft Windows and requires a PKCS7 file, you can download the PKCS7 certificate bundle\. This bundle contains both the intermediate and root certificates at [ https://truststore\.pki\.us\-gov\-west\-1\.rds\.amazonaws\.com/global/global\-bundle\.p7b](https://truststore.pki.us-gov-west-1.rds.amazonaws.com/global/global-bundle.p7b)\. 

To get a certificate bundle that contains both the intermediate and root certificates for an AWS GovCloud \(US\) Region, download from the link for the AWS GovCloud \(US\) Region in the following table\.


| **AWS GovCloud \(US\) Region** | **Certificate bundle \(PEM\)** | **Certificate bundle \(PKCS7\)** | 
| --- | --- | --- | 
| AWS GovCloud \(US\-East\) | [us\-gov\-east\-1\-bundle\.pem](https://truststore.pki.us-gov-west-1.rds.amazonaws.com/us-gov-east-1/us-gov-east-1-bundle.pem) | [us\-gov\-east\-1\-bundle\.p7b](https://truststore.pki.us-gov-west-1.rds.amazonaws.com/us-gov-east-1/us-gov-east-1-bundle.p7b) | 
| AWS GovCloud \(US\-West\) | [us\-gov\-west\-1\-bundle\.pem](https://truststore.pki.us-gov-west-1.rds.amazonaws.com/us-gov-west-1/us-gov-west-1-bundle.pem) | [us\-gov\-west\-1\-bundle\.p7b](https://truststore.pki.us-gov-west-1.rds.amazonaws.com/us-gov-west-1/us-gov-west-1-bundle.p7b) | 