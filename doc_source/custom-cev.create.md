# Creating a CEV<a name="custom-cev.create"></a>

You can create a CEV using the AWS Management Console or the AWS CLI\. Make sure that the Amazon S3 bucket containing your installation files is in the same AWS Region as your CEV\. Otherwise, the process to create a CEV fails\.

Typically, creating a CEV takes about two hours\. After the CEV is created, you can use it to create an RDS Custom DB instance\. 

For more information, see [Creating an RDS Custom for Oracle DB instance](custom-creating.md#custom-creating.create)\.

## Console<a name="custom-cev.create.console"></a>

**To create a CEV**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Custom engine versions**\.

   The **Custom engine versions** page shows all CEVs that currently exist\. If you haven't created any CEVs, the page is empty\.

1. Choose **Create custom engine version**\.

1. In **Engine options**, do the following:

   1. For **Engine type**, choose **Oracle**\.

   1. For **Architecture settings**, optionally choose **Multitenant architecture** to create a Multitenant CEV, which uses the engine `custom-oracle-ee-cdb`\. You can create an RDS Custom for Oracle CDB with a Multitenant CEV only\. If you don't choose this option, your CEV is a non\-CDB, which uses the engine `custom-oracle-ee`\.
**Note**  
The architecture is a permanent characteristic of your CEV\. You can't modify your CEV to use a different architecture later\.

   1. For **Engine version**, choose the major engine version\.

1. In **Version details**, do the following:

   1. Enter a valid name in **Custom engine version name**\.

      The name format is `major-engine-version.customized_string`\. You can use 1–50 alphanumeric characters, underscores, dashes, and periods\. For example, you might enter the name **19\.cdb\_cev1**\.

   1. \(Optional\) Enter a description for your CEV\.

1. In **Installation media**, do the following:

   1. \(Optional\) For **AMI ID**, enter an AMI that you previously used to create a CEV\. To obtain valid AMI IDs, use either of the following techniques:
      + In the console, choose **Custom engine versions** in the left navigation pane, and choose the name of a CEV\. The AMI ID used by the CEV appears in the **Configuration** tab\.
      + In the AWS CLI, use the `describe-db-engine-versions` command\. Search the output for `ImageID`\.

      If you don't enter an AMI ID, RDS Custom uses the most recent available AMI\.

   1. For **S3 location of manifest files**, enter the location of the Amazon S3 bucket that you specified in [Step 3: Upload your installation files to Amazon S3](custom-cev.preparing.md#custom-cev.preparing.s3)\. For example, enter **s3://my\-custom\-installation\-files/806242271698/cev1/**\.

   1. For **CEV manifest**, enter the JSON manifest that you created in [Creating the CEV manifest](custom-cev.preparing.md#custom-cev.preparing.manifest.creating)\.

1. In the **KMS key** section, select **Enter a key ARN** to list the available AWS KMS keys\. Then select your KMS key from the list\. 

   An AWS KMS key is required for RDS Custom\. For more information, see [Step 1: Create or reuse a symmetric encryption AWS KMS key](custom-setup-orcl.md#custom-setup-orcl.cmk)\.

1. \(Optional\) Choose **Add new tag** to create a key\-value pair for your CEV\.

1. Choose **Create custom engine version**\.

   If the CEV manifest has an invalid form, the console displays **Error validating the CEV manifest**\. Fix the problems, and try again\.

The **Custom engine versions** page appears\. Your CEV is shown with the status **Creating**\. The process to create the CEV takes approximately two hours\.

## AWS CLI<a name="custom-cev.create.CEV"></a>

To create a CEV by using the AWS CLI, run the [create\-custom\-db\-engine\-version](https://docs.aws.amazon.com/cli/latest/reference/rds/create-custom-db-engine-version.html) command\.

The following options are required:
+ `--engine engine-type`, where *engine\-type* is either `custom-oracle-ee-cdb` for a CDB or `custom-oracle-ee` for a non\-CDB\. You can create CDBs only from a CEV created with `custom-oracle-ee-cdb`\. You can create non\-CDBs only from a CEV created with `custom-oracle-ee`\.
+ `--engine-version major-engine-version.customized_string`
+ `--kms-key-id`
+ `--manifest manifest_string` or `--manifest file:file_name`

  Newline characters aren't permitted in `manifest_string`\. Make sure to escape double quotes \("\) in the JSON code by prefixing them with a backslash \(\\\)\.

  The following example shows the `manifest_string` for 19c from [Step 5: Prepare the CEV manifest](custom-cev.preparing.md#custom-cev.preparing.manifest)\. The example sets new values for the Oracle base, Oracle home, and the ID and name of the UNIX/Linux user and group\. If you copy this string, remove all newline characters before pasting it into your command\.

  `"{\"mediaImportTemplateVersion\": \"2020-08-14\",\"databaseInstallationFileNames\": [\"V982063-01.zip\"],\"opatchFileNames\": [\"p6880880_190000_Linux-x86-64.zip\"],\"psuRuPatchFileNames\": [\"p32126828_190000_Linux-x86-64.zip\"],\"otherPatchFileNames\": [\"p29213893_1910000DBRU_Generic.zip\",\"p29782284_1910000DBRU_Generic.zip\",\"p28730253_190000_Linux-x86-64.zip\",\"p29374604_1910000DBRU_Linux-x86-64.zip\",\"p28852325_190000_Linux-x86-64.zip\",\"p29997937_190000_Linux-x86-64.zip\",\"p31335037_190000_Linux-x86-64.zip\",\"p31335142_190000_Generic.zip\"]\"installationParameters\":{ \"unixGroupName\":\"dba\", \ \"unixUname\":\"oracle\", \ \"oracleHome\":\"/home/oracle/oracle.19.0.0.0.ru-2020-04.rur-2020-04.r1.EE.1\", \ \"oracleBase\":\"/home/oracle/\"}}"`
+ `--database-installation-files-s3-bucket-name`` s3-bucket-name`, where `s3-bucket-name` is the bucket name that you specified in [Step 3: Upload your installation files to Amazon S3](custom-cev.preparing.md#custom-cev.preparing.s3)\. The AWS Region in which you run `create-custom-db-engine-version` must be the same Region as your Amazon S3 bucket\.

You can also specify the following options:
+ `--description` *`my-cev-description`*
+ `--database-installation-files-s3-prefix` *`prefix`*, where *`prefix`* is the folder name that you specified in [Step 3: Upload your installation files to Amazon S3](custom-cev.preparing.md#custom-cev.preparing.s3)\.
+ `--image-id ami-id`, where *`ami-id`* is an AMI ID that want to reuse\. To find valid IDs, run the `describe-db-engine-versions` command, and then search the output for `ImageID`\. By default, RDS Custom for Oracle uses the most recent available AMI\.

The following example creates a Multitenant CEV named `19.cdb_cev1`\. The example reuses an existing AMI rather than use the latest available AMI\. Make sure that the name of your CEV starts with the major engine version number\.

**Example**  
For Linux, macOS, or Unix:  

```
1. aws rds create-custom-db-engine-version \
2.     --engine custom-oracle-ee-cdb \
3.     --engine-version 19.cdb_cev1 \
4.     --database-installation-files-s3-bucket-name us-east-1-123456789012-custom-installation-files \
5.     --database-installation-files-s3-prefix 123456789012/cev1 \
6.     --kms-key-id my-kms-key \
7.     --description "test cev" \
8.     --manifest manifest_string \
9.     --image-id ami-012a345678901bcde
```
For Windows:  

```
1. aws rds create-custom-db-engine-version ^
2.     --engine custom-oracle-ee-cdb ^
3.     --engine-version 19.cdb_cev1 ^
4.     --database-installation-files-s3-bucket-name us-east-1-123456789012-custom-installation-files ^
5.     --database-installation-files-s3-prefix 123456789012/cev1 ^
6.     --kms-key-id my-kms-key ^
7.     --description "test cev" ^
8.     --manifest manifest_string ^
9.     --image-id ami-012a345678901bcde
```

**Example**  
Get details about your CEV by using the `describe-db-engine-versions` command\.  

```
1. aws rds describe-db-engine-versions \
2.     --engine custom-oracle-ee-cdb \
3.     --include-all
```
The following partial sample output shows the engine, parameter groups, manifest, and other information\.  

```
 1. {
 2.     "DBEngineVersions": [
 3.         {
 4.             "Engine": "custom-oracle-ee-cdb",
 5.             "EngineVersion": "19.cdb_cev1",
 6.             "DBParameterGroupFamily": "custom-oracle-ee-cdb-19",
 7.             "DBEngineDescription": "Containerized Database for Oracle Custom EE",
 8.             "DBEngineVersionDescription": "test cev",
 9.             "Image": {
10.                 "ImageId": "ami-012a345678901bcde",
11.                 "Status": "active"
12.             },
13.             "ValidUpgradeTarget": [],
14.             "SupportsLogExportsToCloudwatchLogs": false,
15.             "SupportsReadReplica": true,
16.             "SupportedFeatureNames": [],
17.             "Status": "available",
18.             "SupportsParallelQuery": false,
19.             "SupportsGlobalDatabases": false,
20.             "MajorEngineVersion": "19",
21.             "DatabaseInstallationFilesS3BucketName": "us-east-1-123456789012-custom-installation-files",
22.             "DatabaseInstallationFilesS3Prefix": "123456789012/cev1",
23.             "DBEngineVersionArn": "arn:aws:rds:us-east-1:123456789012:cev:custom-oracle-ee-cdb/19.cdb_cev1/abcd12e3-4f5g-67h8-i9j0-k1234l56m789",
24.             "KMSKeyId": "arn:aws:kms:us-east-1:732027699161:key/1ab2345c-6d78-9ef0-1gh2-3456i7j89k01",
25.             "CreateTime": "2023-03-07T19:47:58.131000+00:00",
26.             "TagList": [],
27.             "SupportsBabelfish": false,
28. ...
```

## Failure to create a CEV<a name="custom-cev.create.failure"></a>

If the process to create a CEV fails, RDS Custom issues `RDS-EVENT-0198` with the message `Creation failed for custom engine version major-engine-version.cev_name`, and includes details about the failure\. For example, the event prints missing files\.

You can't modify a failed CEV\. You can only delete it, then try again to create a CEV after fixing the causes of the failure\. For information about troubleshooting the reasons for CEV creation failure, see [Troubleshooting custom engine version creation for RDS Custom for Oracle](custom-troubleshooting.md#custom-troubleshooting.cev)\.