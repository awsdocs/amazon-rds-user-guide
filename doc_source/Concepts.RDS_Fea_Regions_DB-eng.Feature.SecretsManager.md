# Secrets Manager integration<a name="Concepts.RDS_Fea_Regions_DB-eng.Feature.SecretsManager"></a>

With AWS Secrets Manager, you can replace hard\-coded credentials in your code, including database passwords, with an API call to Secrets Manager to retrieve the secret programmatically\. For more information about Secrets Manager, see [AWS Secrets Manager User Guide](https://docs.aws.amazon.com/secretsmanager/latest/userguide/)\.

You can specify that Amazon RDS manages the master user password in Secrets Manager for an Amazon RDS DB instance or Multi\-AZ DB cluster\. RDS generates the password, stores it in Secrets Manager, and rotates it regularly\. For more information, see [Password management with Amazon RDS and AWS Secrets Manager](rds-secrets-manager.md)\.

Secrets Manager integration is available for all RDS DB engines and all versions\.

Secrets Manager integration is available in all AWS Regions except the following:
+ Asia Pacific \(Hyderabad\)
+ Asia Pacific \(Jakarta\)
+ Asia Pacific \(Melbourne\)
+ Asia Pacific \(Osaka\)
+ Europe \(Spain\)
+ Europe \(Zurich\)
+ Middle East \(UAE\)
+ AWS GovCloud \(US\-East\)
+ AWS GovCloud \(US\-West\)
+ China \(Beijing\)
+ China \(Ningxia\)