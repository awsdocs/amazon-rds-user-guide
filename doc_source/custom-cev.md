# Working with custom engine versions for Amazon RDS Custom for Oracle<a name="custom-cev"></a>

A *custom engine version \(CEV\)* for Amazon RDS Custom for Oracle is a binary volume snapshot of a database engine and specific Amazon Machine Image \(AMI\)\. You store your database installation files in Amazon S3\. RDS Custom uses the installation files to create your CEV for you\.

**Topics**
+ [Preparing to create a CEV](#custom-cev.preparing)
+ [Creating a CEV](#custom-cev.create)
+ [Modifying CEV status](#custom-cev.modify)
+ [Deleting a CEV](#custom-cev.delete)

## Preparing to create a CEV<a name="custom-cev.preparing"></a>

To create a CEV, access the installation files and patches for Oracle Database 12\.1 or 19c Enterprise Edition that are stored in your Amazon S3 bucket\. For example, you can use the April 2021 RU/RUR for 19c, or any valid combination of installation files and patches\.

**Topics**
+ [Downloading your database installation files and patches from Oracle](#custom-cev.preparing.download)
+ [Uploading your installation files to Amazon S3](#custom-cev.preparing.s3)
+ [Creating the CEV manifest](#custom-cev.preparing.manifest)
+ [Validating the CEV manifest](#custom-cev.preparing.validating)
+ [Adding necessary IAM permissions](#custom-cev.preparing.iam)

### Downloading your database installation files and patches from Oracle<a name="custom-cev.preparing.download"></a>

The Oracle Database installation files and patches are hosted on Oracle Software Delivery Cloud\.

**To download the database installation files for 12\.1**

1. Go to [https://edelivery.oracle.com/](https://edelivery.oracle.com/) and sign in\.

1. In the box, enter **Oracle Database Enterprise Edition** and choose **Search**\.

1. Choose **DLP: Oracle Database 12c Enterprise Edition 12\.1\.0\.2\.0 \( Oracle Database Enterprise Edition \)**\.

1. Choose **Continue**\.

1. Clear the **Download Queue** check box\.

1. Choose **Oracle Database 12\.1\.0\.2\.0**\.

1. Choose **Linux x86\-64** in **Platform/Languages**\.

1. Choose **Continue**, and then sign the waiver\.

1. Choose **V46095\-01\_1of2\.zip** and **V46095\-01\_2of2\.zip**, choose **Download**, and then save the files\.

   
**Note**  
The SHA\-256 hash for `V46095-01_1of2.zip` is `31FDC2AF41687B4E547A3A18F796424D8C1AF36406D2160F65B0AF6A9CD47355`\.  
The SHA\-256 hash for `V46095-01_2of2.zip` is `03DA14F5E875304B28F0F3BB02AF0EC33227885B99C9865DF70749D1E220ACCD`\.

1. Click the links in the following table to download the Oracle patches\. All URLs are for `updates.oracle.com` or `support.oracle.com`\.    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/custom-cev.html)

**To download the database installation files for 19c**

1. Go to [https://edelivery.oracle.com/](https://edelivery.oracle.com/) and sign in\.

1. In the box, enter **Oracle Database Enterprise Edition** and choose **Search**\.

1. Choose **DLP: Oracle Database Enterprise Edition 19\.3\.0\.0\.0 \( Oracle Database Enterprise Edition \)**\.

1. Choose **Continue**\.

1. Clear the **Download Queue** check box\.

1. Choose **Oracle Database 19\.3\.0\.0\.0 \- Long Term Release**\.

1. Choose **Linux x86\-64** in **Platform/Languages**\.

1. Choose **Continue**, and then sign the waiver\.

1. Choose **V982063\-01\.zip**, choose **Download**, and then save the file\.
**Note**  
The SHA\-256 hash is `BA8329C757133DA313ED3B6D7F86C5AC42CD9970A28BF2E6233F3235233AA8D8`\.

1. Click the links in the following table to download the Oracle patches\. All URLs are for `updates.oracle.com` or `support.oracle.com`\.    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/custom-cev.html)

### Uploading your installation files to Amazon S3<a name="custom-cev.preparing.s3"></a>

Upload your Oracle installation and patch files to Amazon S3 using the AWS CLI\. The S3 bucket that contains your installation files must be in the same AWS Region as your CEV\.

Choose either of the following options:
+ Use `aws s3 cp` to upload a single \.zip file\.

  Upload each installation \.zip file separately\. Don't combine the \.zip files into a single \.zip file\.
+ Use `aws s3 sync` to upload a directory\.

List your installation files using either the AWS Management Console or the AWS CLI\.

Examples in this section use the following placeholders:
+ `install-or-patch-file.zip` – Oracle installation media file\. For example, p32126828\_190000\_Linux\-x86\-64\.zip is a patch\.
+ `my-custom-installation-files` – Your Amazon S3 bucket designated for your uploaded installation files\.
+ `123456789012/cev1` – An optional prefix in your Amazon S3 bucket\.
+ `source-bucket` – An Amazon S3 bucket where you can optionally stage files\.

The following example uploads `install-or-patch-file.zip` to the `123456789012/cev1` folder in the RDS Custom Amazon S3 bucket\. Run a separate `aws s3` command for each \.zip that you want to upload\.

For Linux, macOS, or Unix:

```
1. aws s3 cp install-or-patch-file.zip \
2.     s3://my-custom-installation-files/123456789012/cev1/
```

For Windows:

```
1. aws s3 cp install-or-patch-file.zip ^
2.     s3://my-custom-installation-files/123456789012/cev1/
```

Verify that your S3 bucket is in the AWS Region where you plan to run the `create-custom-db-engine-version` command\.

```
aws s3 get-bucket-location --bucket my-custom-installation-files
```

List the files in your RDS Custom Amazon S3 bucket as follows\.

```
aws s3 ls \
    s3://my-custom-installation-files/123456789012/cev1/
```

The following example uploads the files in your local *cev1* folder to the *123456789012/cev1* folder in your Amazon S3 bucket\.

For Linux, macOS, or Unix:

```
aws s3 sync cev1 \
    s3://my-custom-installation-files/123456789012/cev1/
```

For Windows:

```
aws s3 sync cev1 ^
    s3://my-custom-installation-files/123456789012/cev1/
```

The following example uploads all files in `source-bucket` to the **`123456789012/cev1`** folder in your Amazon S3 bucket\.

For Linux, macOS, or Unix:

```
aws s3 sync s3://source-bucket/ \
    s3://my-custom-installation-files/123456789012/cev1/
```

For Windows:

```
aws s3 sync s3://source-bucket/ ^
    s3://my-custom-installation-files/123456789012/cev1/
```

### Creating the CEV manifest<a name="custom-cev.preparing.manifest"></a>

A CEV manifest is a JSON document that describes installation \.zip files stored in Amazon S3\.

Either save the CEV manifest as a file, or edit the template when creating the CEV using the console\.

The following CEV manifest lists the files that you uploaded to Amazon S3\. RDS Custom applies the patches in the order in which they're listed\.

In the following example for the July 2021 PSU for Oracle Database 12c Release 1 \(12\.1\), RDS Custom applies p32768233, then p32876425, then p16799735, and so on\.

```
{
    "mediaImportTemplateVersion":"2020-08-14",
    "databaseInstallationFileNames":[
        "V46095-01_1of2.zip",
        "V46095-01_2of2.zip"
    ],
    "opatchFileNames":[
        "p6880880_121010_Linux-x86-64.zip"
    ],
    "psuRuPatchFileNames":[
        "p32768233_121020_Linux-x86-64.zip"
    ],
    "otherPatchFileNames":[
        "p32876425_121020_Linux-x86-64.zip",
        "p18759211_121020_Linux-x86-64.zip",
        "p19396455_121020_Linux-x86-64.zip",
        "p20875898_121020_Linux-x86-64.zip",
        "p22037014_121020_Linux-x86-64.zip",
        "p22873635_121020_Linux-x86-64.zip",
        "p23614158_121020_Linux-x86-64.zip",
        "p24701840_121020_Linux-x86-64.zip",
        "p25881255_121020_Linux-x86-64.zip",
        "p27015449_121020_Linux-x86-64.zip",
        "p28125601_121020_Linux-x86-64.zip",
        "p28852325_121020_Linux-x86-64.zip",
        "p29997937_121020_Linux-x86-64.zip",
        "p31335037_121020_Linux-x86-64.zip",
        "p32327201_121020_Linux-x86-64.zip",
        "p32327208_121020_Generic.zip",
        "p17969866_12102210119_Linux-x86-64.zip",
        "p20394750_12102210119_Linux-x86-64.zip",
        "p24835919_121020_Linux-x86-64.zip",
        "p23262847_12102201020_Linux-x86-64.zip",
        "p21171382_12102201020_Generic.zip",
        "p21091901_12102210720_Linux-x86-64.zip",
        "p33013352_12102210720_Linux-x86-64.zip",
        "p25031502_12102210720_Linux-x86-64.zip",
        "p23711335_12102191015_Generic.zip",
        "p19504946_121020_Linux-x86-64.zip"
    ]
}
```

In the following example for Oracle Database 19c, RDS Custom applies p32126828, then p29213893, then p29782284, and so on\.

```
{
    "mediaImportTemplateVersion": "2020-08-14",
    "databaseInstallationFileNames": [
        "V982063-01.zip"
    ],
    "opatchFileNames": [
        "p6880880_190000_Linux-x86-64.zip"
    ],
    "psuRuPatchFileNames": [
        "p32126828_190000_Linux-x86-64.zip"
    ],
    "otherPatchFileNames": [
        "p29213893_1910000DBRU_Generic.zip",
        "p29782284_1910000DBRU_Generic.zip",
        "p28730253_190000_Linux-x86-64.zip",
        "p29374604_1910000DBRU_Linux-x86-64.zip",
        "p28852325_190000_Linux-x86-64.zip",
        "p29997937_190000_Linux-x86-64.zip",
        "p31335037_190000_Linux-x86-64.zip",
        "p31335142_190000_Generic.zip"
    ]
}
```

The following table describes the JSON fields in the manifest\.


| JSON field | Description | Valid values for 12\.1 | Valid values for 19c | 
| --- | --- | --- | --- | 
| MediaImportTemplateVersion |  Version of the CEV manifest\. The date is in the format `YYYY-MM-DD`\.  |  2020\-08\-14  |  2020\-08\-14  | 
| databaseInstallationFileNames |  Ordered list of installation files for the database\.  |  V46095\-01\_1of2\.zip V46095\-01\_2of2\.zip  |  V982063\-01\.zip  | 
| opatchFileNames |  Ordered list of OPatch installers used for the Oracle DB engine\. Only one value is valid\. If you include a `psuRuPatchFileNames` or `OtherPatchFileNames` section \(or both\), the `opatchFileNames` section is required\. Values for `opatchFileNames` must start with `p6880880_`\.  |  p6880880\_121010\_Linux\-x86\-64\.zip  |  p6880880\_190000\_Linux\-x86\-64\.zip  | 
| psuRuPatchFileNames |  The PSU and RU patches for this database\.  |  p32768233\_121020\_Linux\-x86\-64\.zip  |  p32126828\_190000\_Linux\-x86\-64\.zip  | 
| OtherPatchFileNames |  The patches that aren't in the list of PSU and RU patches\. RDS Custom applies these patches after applying the PSU and RU patches\.  |  p32876425\_121020\_Linux\-x86\-64\.zip p18759211\_121020\_Linux\-x86\-64\.zip p19396455\_121020\_Linux\-x86\-64\.zip p20875898\_121020\_Linux\-x86\-64\.zip p22037014\_121020\_Linux\-x86\-64\.zip p22873635\_121020\_Linux\-x86\-64\.zip p23614158\_121020\_Linux\-x86\-64\.zip p24701840\_121020\_Linux\-x86\-64\.zip p25881255\_121020\_Linux\-x86\-64\.zip p27015449\_121020\_Linux\-x86\-64\.zip p28125601\_121020\_Linux\-x86\-64\.zip p28852325\_121020\_Linux\-x86\-64\.zip p29997937\_121020\_Linux\-x86\-64\.zip p31335037\_121020\_Linux\-x86\-64\.zip p32327201\_121020\_Linux\-x86\-64\.zip p32327208\_121020\_Generic\.zip p17969866\_12102210119\_Linux\-x86\-64\.zip p20394750\_12102210119\_Linux\-x86\-64\.zip p24835919\_121020\_Linux\-x86\-64\.zip p23262847\_12102201020\_Linux\-x86\-64\.zip p21171382\_12102201020\_Generic\.zip p21091901\_12102210720\_Linux\-x86\-64\.zip p33013352\_12102210720\_Linux\-x86\-64\.zip p25031502\_12102210720\_Linux\-x86\-64\.zip p23711335\_12102191015\_Generic\.zip p19504946\_121020\_Linux\-x86\-64\.zip  |  p29213893\_1910000DBRU\_Generic\.zip p29782284\_1910000DBRU\_Generic\.zip p28730253\_190000\_Linux\-x86\-64\.zip p29374604\_1910000DBRU\_Linux\-x86\-64\.zip p28852325\_190000\_Linux\-x86\-64\.zip p29997937\_190000\_Linux\-x86\-64\.zip p31335037\_190000\_Linux\-x86\-64\.zip p31335142\_190000\_Generic\.zip  | 

If you include a JSON field, it can't be empty\. For example, the following manifest isn't valid because `otherPatchFileNames` is empty\.

```
{
    "mediaImportTemplateVersion": "2020-08-14",
    "databaseInstallationFileNames": [
        "V982063-01.zip"
    ],
    "opatchFileNames": [
        "p6880880_190000_Linux-x86-64.zip"
    ],
    "psuRuPatchFileNames": [
        "p32126828_190000_Linux-x86-64.zip"
    ],
    "otherPatchFileNames": [
    ]
}
```

### Validating the CEV manifest<a name="custom-cev.preparing.validating"></a>

Optionally, verify that manifest is a valid JSON file by running the `json.tool` Python script\.

For example, if you change into the directory containing a CEV manifest named `manifest.json`, run the following command\.

```
python -m json.tool < manifest.json
```

### Adding necessary IAM permissions<a name="custom-cev.preparing.iam"></a>

Make sure that the IAM principal that creates the CEV has the necessary policies described in [Grant required permissions to your IAM user](custom-setup-orcl.md#custom-setup-orcl.iam-user)\.

## Creating a CEV<a name="custom-cev.create"></a>

You can create a CEV using the AWS Management Console or the AWS CLI\. Typically, creating a CEV takes about two hours\.

You can then use the CEV to create an RDS Custom instance\. Make sure that the Amazon S3 bucket containing your installation files is in the same AWS Region as your CEV\. Otherwise, the process to create a CEV fails\.

For more information, see [Creating an RDS Custom for Oracle DB instance](custom-creating.md#custom-creating.create)\.

### Console<a name="custom-cev.create.console"></a>

**To create a CEV**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Custom engine versions**\.

   The **Custom engine versions** page shows all CEVs that currently exist\. If you haven't created any CEVs, the page is empty\.

1. Choose **Create custom engine version**\.

1. For **Engine type**, choose **Oracle**\.

1. For **Edition**, choose **Oracle Enterprise Edition**\. **Oracle Enterprise Edition \(Oracle RAC option\)** isn't supported\.

1. For **Major version**, choose the major engine version\.

1. In **Version details**, enter a valid name in **Custom engine version name**\.

   The name format is `major-engine-version.customized_string`\. You can use 1–50 alphanumeric characters, underscores, dashes, and periods\. For example, you might enter the name **19\.my\_cev1**\.

   Optionally, enter a description for your CEV\.

1. For **S3 location of manifest files**, enter the location of the Amazon S3 bucket that you specified in [Uploading your installation files to Amazon S3](#custom-cev.preparing.s3)\. For example, enter **s3://my\-custom\-installation\-files/806242271698/cev1/**\.

1. In the **RDS Custom encryption** section, select **Enter a key ARN** to list the available AWS KMS keys\.

   Then select your KMS key from the list\. An AWS KMS key is required for RDS Custom\. For more information, see [Make sure that you have a symmetric AWS KMS key](custom-setup-orcl.md#custom-setup-orcl.cmk)\.

1. Choose **Create custom engine version**\.

   If the CEV manifest has an invalid form, the console displays **Error validating the CEV manifest**\. Fix the problems, and try again\.

The **Custom engine versions** page appears\. Your CEV is shown with the status **Creating**\. The process to create the CEV takes approximately two hours\.

### AWS CLI<a name="custom-cev.create.CEV"></a>

To create a CEV by using the AWS CLI, run the [create\-custom\-db\-engine\-version](https://docs.aws.amazon.com/cli/latest/reference/rds/create-custom-db-engine-version.html) command\.

The following options are required:
+ `--engine custom-oracle-ee`
+ `--engine-version major-engine-version.customized_string`
+ `--kms-key-id`
+ `--manifest manifest_string`

  Newline characters aren't permitted in `manifest_string`\. Make sure to escape double quotes \("\) in the JSON code by prefixing them with a backslash \(\\\)\.

  The following example shows the `manifest_string` for 19c from [Creating the CEV manifest](#custom-cev.preparing.manifest)\. If you copy this string, remove all newline characters before pasting it into your command\.

  `"{\"mediaImportTemplateVersion\": \"2020-08-14\",\"databaseInstallationFileNames\": [\"V982063-01.zip\"],\"opatchFileNames\": [\"p6880880_190000_Linux-x86-64.zip\"],\"psuRuPatchFileNames\": [\"p32126828_190000_Linux-x86-64.zip\"],\"otherPatchFileNames\": [\"p29213893_1910000DBRU_Generic.zip\",\"p29782284_1910000DBRU_Generic.zip\",\"p28730253_190000_Linux-x86-64.zip\",\"p29374604_1910000DBRU_Linux-x86-64.zip\",\"p28852325_190000_Linux-x86-64.zip\",\"p29997937_190000_Linux-x86-64.zip\",\"p31335037_190000_Linux-x86-64.zip\",\"p31335142_190000_Generic.zip\"]}"`
+ `--database-installation-files-s3-bucket-name`` s3-bucket-name`, where `s3-bucket-name` is the bucket name that you specified in [Uploading your installation files to Amazon S3](#custom-cev.preparing.s3)\. The AWS Region in which you run `create-custom-db-engine-version` must be in the same AWS Region as the bucket\.

You can also specify the following options:
+ `--description` *`my-cev-description`*
+ `database-installation-files-s3-prefix` *`prefix`*, where *`prefix`* is the folder name that you specified in [Uploading your installation files to Amazon S3](#custom-cev.preparing.s3)\.

The following example creates a CEV named `19.my_cev1`\. Make sure that the name of your CEV starts with the major engine version number\.

**Example**  
For Linux, macOS, or Unix:  

```
1. aws rds create-custom-db-engine-version \
2.     --engine custom-oracle-ee \
3.     --engine-version 19.my_cev1 \
4.     --database-installation-files-s3-bucket-name my-custom-installation-files \
5.     --database-installation-files-s3-prefix 123456789012/cev1 \
6.     --kms-key-id my-kms-key \
7.     --description "some text" \
8.     --manifest manifest_string
```
For Windows:  

```
1. aws rds create-custom-db-engine-version ^
2.     --engine custom-oracle-ee ^
3.     --engine-version 19.my_cev1 ^
4.     --database-installation-files-s3-bucket-name s3://my-custom-installation-files ^
5.     --database-installation-files-s3-prefix 123456789012/cev1 ^
6.     --kms-key-id my-kms-key ^
7.     --description "some text" ^
8.     --manifest manifest_string
```

**Example**  
Get details about your CEV by using the `describe-db-engine-versions` command\.  

```
1. aws rds describe-db-engine-versions \
2.     --engine custom-oracle-ee \
3.     --include-all
```
The following partial output shows the engine, parameter groups, and other information\.  

```
 1. "DBEngineVersions": [
 2.     {
 3.         "Engine": "custom-oracle-ee",
 4.         "Status": "available",
 5.         "DBParameterGroupFamily": "custom-oracle-ee-19",
 6.         "DBEngineVersionArn": "arn:aws:rds:us-west-2:<my-account-id>:cev:custom-oracle-ee/19.my_cev1/0a123b45-6c78-901d-23e4-5678f901fg23",
 7.         "MajorEngineVersion": "19",
 8.         "SupportsReadReplica": false,
 9.         "SupportsLogExportsToCloudwatchLogs": false,
10.         "EngineVersion": "19.my_cev1",
11.         "DatabaseInstallationFilesS3BucketName": "my-custom-installation-files",
12.         "DBEngineDescription": "Oracle Database server EE for RDS Custom",
13.         "SupportedFeatureNames": [],
14.         "KMSKeyId": "arn:aws:kms:us-east-2:<your-account-id>:key/<my-kms-key-id>",
15.         "SupportsGlobalDatabases": false,
16.         "SupportsParallelQuery": false,
17.         "DatabaseInstallationFilesS3Prefix": "123456789012/cev1",
18.         "DBEngineVersionDescription": "some text",
19.         "ValidUpgradeTarget": [],
20.         "CreateTime": "2021-06-23T20:00:34.782Z"
21.     }
22. ]
```

### Failure to create a CEV<a name="custom-cev.create.failure"></a>

If the process to create a CEV fails, RDS Custom issues `RDS-EVENT-0196` with the message `Creation failed for custom engine version major-engine-version.cev_name`, and includes details about the failure\. For example, the event prints missing files\.

You can't modify a failed CEV\. You can only delete it, then try again to create a CEV after fixing the causes of the failure\. For information on troubleshooting the reasons for CEV creation failure, see [Troubleshooting custom engine version creation for RDS Custom for Oracle](custom-troubleshooting.md#custom-troubleshooting.cev)\.

## Modifying CEV status<a name="custom-cev.modify"></a>

You can modify a CEV using the AWS Management Console or the AWS CLI\. You can modify the CEV description or its availability status\. Your CEV has one of the following status values:
+ `available` – You can use this CEV to create a new RDS Custom DB instance or upgrade a DB instance\. This is the default status for a newly created CEV\.
+ `inactive` – You can't create or upgrade an RDS Custom instance with this CEV\. You can't restore a DB snapshot to create a new RDS Custom DB instance with this CEV\.

You can change the CEV from any supported status to any other supported status\. You might change status to prevent the accidental use of a CEV or make a discontinued CEV eligible for use again\. For example, you might change the status of your CEV from `available` to `inactive`, and from `inactive` back to `available`\.

### Console<a name="custom-cev.modify.console"></a>

**To modify a CEV**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Custom engine versions**\.

1. Choose a CEV whose description or status you want to modify\.

1. For **Actions**, choose **Modify**\.

1. Make any of the following changes:
   + For **CEV status settings**, choose a new availability status\.
   + For **Version description**, enter a new description\.

1. Choose **Modify CEV**\.

   If the CEV is in use, the console displays **You can't modify the CEV status**\. Fix the problems, and try again\.

The **Custom engine versions** page appears\.

### AWS CLI<a name="custom-cev.modify.cli"></a>

To modify a CEV by using the AWS CLI, run the [modify\-custom\-db\-engine\-version](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-custom-db-engine-version.html) command\. You can find CEVs to modify by running the [describe\-db\-engine\-versions](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-engine-versions.html) command\.

The following options are required:
+ `--engine custom-oracle-ee`
+ `--engine-version cev`, where *`cev`* is the name of the custom engine version that you want to modify
+ `--status`` status`, where *`status`* is the availability status that you want to assign to the CEV

The following example changes a CEV named `19.my_cev1` from its current status to `inactive`\.

**Example**  
For Linux, macOS, or Unix:  

```
1. aws rds modify-custom-db-engine-version \
2.     --engine custom-oracle-ee \ 
3.     --engine-version 19.my_cev1 \
4.     --status inactive
```
For Windows:  

```
1. aws rds modify-custom-db-engine-version ^
2.     --engine custom-oracle-ee ^
3.     --engine-version 19.my_cev1 ^
4.     --status inactive
```

## Deleting a CEV<a name="custom-cev.delete"></a>

You can delete a CEV using the AWS Management Console or the AWS CLI\. Typically, deletion takes a few minutes\.

To delete a CEV, it can't be in use by any of the following:
+ An RDS Custom DB instance
+ A snapshot of an RDS Custom DB instance
+ An automated backup of your RDS Custom DB instance

### Console<a name="custom-cev.create.console"></a>

**To delete a CEV**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Custom engine versions**\.

1. Choose a CEV whose description or status you want to delete\.

1. For **Actions**, choose **Delete**\.

   The **Delete *cev\_name*?** dialog box appears\.

1. Enter **delete me**, and then choose **Delete**\.

   In the **Custom engine versions** page, the banner shows that your CEV is being deleted\.

### AWS CLI<a name="custom-cev.create.console.cli"></a>

To delete a CEV by using the AWS CLI, run the [delete\-custom\-db\-engine\-version](https://docs.aws.amazon.com/cli/latest/reference/rds/delete-custom-db-engine-version.html) command\.

The following options are required:
+ `--engine custom-oracle-ee`
+ `--engine-version cev`, where *cev* is the name of the custom engine version to be deleted

The following example deletes a CEV named `19.my_cev1`\.

**Example**  
For Linux, macOS, or Unix:  

```
1. aws rds delete-custom-db-engine-version \
2.     --engine custom-oracle-ee \
3.     --engine-version 19.my_cev1
```
For Windows:  

```
1. aws rds delete-custom-db-engine-version ^
2.     --engine custom-oracle-ee ^
3.     --engine-version 19.my_cev1
```