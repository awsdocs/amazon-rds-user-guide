# Viewing CEV details<a name="custom-cev.view"></a>

You can view details about your CEV manifest and the command used to create your CEV by using the AWS Management Console or the AWS CLI\.

## Console<a name="custom-cev.view.console"></a>

**To view CEV details**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Custom engine versions**\.

   The **Custom engine versions** page shows all CEVs that currently exist\. If you haven't created any CEVs, the page is empty\.

1. Choose the name of the CEV that you want to view\.

1. Choose **Configuration** to view the installation parameters specified in your manifest\.  
![\[View the installation parameters for a CEV.\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/cev-configuration-tab.png)

1. Choose **Manifest** to view the installation parameters specified in the `--manifest` option of the `create-custom-db-engine-version` command\. You can copy this text, replace values as needed, and use them in a new command\.  
![\[View the command used to create the CEV.\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/cev-manifest-tab.png)

## AWS CLI<a name="custom-cev.view.CEV"></a>

To view details about a CEV by using the AWS CLI, run the [describe\-db\-engine\-versions](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-engine-versions.html) command\.

The following options are required:
+ `--engine custom-oracle-ee`
+ `--engine-version major-engine-version.customized_string`

The following example creates a CEV named `19.my_cev1`\. Make sure that the name of your CEV starts with the major engine version number\.

**Example**  
For Linux, macOS, or Unix:  

```
1. aws rds describe-db-engine-versions \
2.     --engine custom-oracle-ee \
3.     --engine-version 19.my_cev1
```
For Windows:  

```
1. aws rds describe-db-engine-versions ^
2.     --engine custom-oracle-ee ^
3.     --engine-version 19.my_cev1
```
The following partial sample output shows the engine, parameter groups, manifest, and other information\.  

```
 1. "DBEngineVersions": [
 2.     {
 3.         "Engine": "custom-oracle-ee",
 4.         "MajorEngineVersion": "19",
 5.         "EngineVersion": "19.my_cev1",
 6.         "DatabaseInstallationFilesS3BucketName": "us-east-1-123456789012-cev-customer-installation-files",
 7.         "DatabaseInstallationFilesS3Prefix": "123456789012/cev1",
 8.         "CustomDBEngineVersionManifest": "{\n\"mediaImportTemplateVersion\": \"2020-08-14\",\n\"databaseInstallationFileNames\": [\n\"V982063-01.zip\"\n],\n\"installationParameters\": {\n\"oracleBase\":\"/tmp\",\n\"oracleHome\":\"/tmp/Oracle\"\n},\n\"opatchFileNames\": [\n\"p6880880_190000_Linux-x86-64.zip\"\n],\n\"psuRuPatchFileNames\": [\n\"p32126828_190000_Linux-x86-64.zip\"\n],\n\"otherPatchFileNames\": [\n\"p29213893_1910000DBRU_Generic.zip\",\n\"p29782284_1910000DBRU_Generic.zip\",\n\"p28730253_190000_Linux-x86-64.zip\",\n\"p29374604_1910000DBRU_Linux-x86-64.zip\",\n\"p28852325_190000_Linux-x86-64.zip\",\n\"p29997937_190000_Linux-x86-64.zip\",\n\"p31335037_190000_Linux-x86-64.zip\",\n\"p31335142_190000_Generic.zip\"\n]\n}\n",
 9.         "DBParameterGroupFamily": "custom-oracle-ee-19",
10.         "DBEngineDescription": "Oracle Database server EE for RDS Custom",
11.         "DBEngineVersionArn": "arn:aws:rds:us-west-2:123456789012:cev:custom-oracle-ee/19.my_cev1/0a123b45-6c78-901d-23e4-5678f901fg23",
12.         "DBEngineVersionDescription": "test",
13.         "KMSKeyId": "arn:aws:kms:us-east-1:123456789012:key/ab1c2de3-f4g5-6789-h012-h3ijk4567l89",
14.         "CreateTime": "2022-11-18T09:17:07.693000+00:00",
15.         "ValidUpgradeTarget": [
16.         {
17.             "Engine": "custom-oracle-ee",
18.             "EngineVersion": "19.cev.2021-01.09",
19.             "Description": "test",
20.             "AutoUpgrade": false,
21.             "IsMajorVersionUpgrade": false
22.         }
23. ]
```