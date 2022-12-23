# Amazon S3 integration<a name="oracle-s3-integration"></a>

You can transfer files between your RDS for Oracle DB instance and an Amazon S3 bucket\. You can use Amazon S3 integration with Oracle Database features such as Oracle Data Pump\. For example, you can download Data Pump files from Amazon S3 to your RDS for Oracle DB instance\. For more information, see [Importing data into Oracle on Amazon RDS](Oracle.Procedural.Importing.md)\.

**Note**  
Your DB instance and your Amazon S3 bucket must be in the same AWS Region\.

**Topics**
+ [Configuring IAM permissions for RDS for Oracle integration with Amazon S3](#oracle-s3-integration.preparing)
+ [Adding the Amazon S3 integration option](#oracle-s3-integration.preparing.option-group)
+ [Transferring files between Amazon RDS for Oracle and an Amazon S3 bucket](#oracle-s3-integration.using)
+ [Removing the Amazon S3 integration option](#oracle-s3-integration.removing)

## Configuring IAM permissions for RDS for Oracle integration with Amazon S3<a name="oracle-s3-integration.preparing"></a>

For RDS for Oracle to integrate with Amazon S3, your DB instance must have access to an Amazon S3 bucket\. The Amazon VPC used by your DB instance doesn't need to provide access to the Amazon S3 endpoints\.

RDS for Oracle supports uploading files from a DB instance in one account to an Amazon S3 bucket in a different account\. Where additional steps are required, they are noted in the following sections\.

**Topics**
+ [Step 1: Create an IAM policy for your Amazon RDS role](#oracle-s3-integration.preparing.policy)
+ [Step 2: \(Optional\) Create an IAM policy for your Amazon S3 bucket](#oracle-s3-integration.preparing.policy-bucket)
+ [Step 3: Create an IAM role for your DB instance and attach your policy](#oracle-s3-integration.preparing.role)
+ [Step 4: Associate your IAM role with your RDS for Oracle DB instance](#oracle-s3-integration.preparing.instance)

### Step 1: Create an IAM policy for your Amazon RDS role<a name="oracle-s3-integration.preparing.policy"></a>

In this step, you create an AWS Identity and Access Management \(IAM\) policy with the permissions required to transfer files from your Amazon S3 bucket to your RDS DB instance\.

To create the policy, make sure you have the following:
+ The Amazon Resource Name \(ARN\) for your bucket
+ The ARN for your AWS KMS key, if your bucket uses SSE\-KMS or SSE\-S3 encryption
**Note**  
An Oracle DB instance can't access Amazon S3 buckets encrypted with SSE\-C\.

For more information, see [Protecting data using server\-side encryption](https://docs.aws.amazon.com/AmazonS3/latest/userguide/serv-side-encryption.html) in the *Amazon Simple Storage Service User Guide*\.

#### Console<a name="oracle-s3-integration.preparing.policy.console"></a>

**To create an IAM policy to allow Amazon RDS access to an Amazon S3 bucket**

1. Open the [IAM Management Console](https://console.aws.amazon.com/iam/home?#home)\.

1. Under **Access management**, choose **Policies**\.

1. Choose **Create Policy**\.

1. On the **Visual editor** tab, choose **Choose a service**, and then choose **S3**\.

1. For **Actions**, choose **Expand all**, and then choose the bucket permissions and object permissions required to transfer files from an Amazon S3 bucket to Amazon RDS\. For example, do the following:
   + Expand **List**, and then select **ListBucket**\.
   + Expand **Read**, and then select **GetObject**\.
   + Expand **Write**, and then select **PutObject** and **DeleteObject**\.
   + Expand **Permissions management**, and then select **PutObjectAcl**\. This permission is necessary if you plan to upload files to a bucket owned by a different account, and this account needs full control of the bucket contents\.

   *Object permissions* are permissions for object operations in Amazon S3\. You must grant them for objects in a bucket, not the bucket itself\. For more information, see [Permissions for object operations](https://docs.aws.amazon.com/AmazonS3/latest/dev/using-with-s3-actions.html#using-with-s3-actions-related-to-objects)\.

1. Choose **Resources**, and choose **Add ARN** for **bucket**\.

1. In the **Add ARN\(s\)** dialog box, provide the details about your resource, and choose **Add**\.

   Specify the Amazon S3 bucket to allow access to\. For instance, to allow Amazon RDS to access the Amazon S3 bucket named `example-bucket`, set the ARN value to `arn:aws:s3:::example-bucket`\.

1. If the **object** resource is listed, choose **Add ARN** for **object**\.

1. In the **Add ARN\(s\)** dialog box, provide the details about your resource\.

   For the Amazon S3 bucket, specify the Amazon S3 bucket to allow access to\. For the object, you can choose **Any** to grant permissions to any object in the bucket\.
**Note**  
You can set **Amazon Resource Name \(ARN\)** to a more specific ARN value to allow Amazon RDS to access only specific files or folders in an Amazon S3 bucket\. For more information about how to define an access policy for Amazon S3, see [Managing access permissions to your Amazon S3 resources](https://docs.aws.amazon.com/AmazonS3/latest/dev/s3-access-control.html)\.

1. \(Optional\) Choose **Add additional permissions** to add resources to the policy\. For example, do the following:
   + If your bucket is encrypted with a custom KMS key, select **KMS** for the service\. Select **Encrypt**, **ReEncrypt**, **Decrypt**, **DescribeKey**, and **GenerateDataKey** for actions\. Enter the ARN of your custom key as the resource\. For more information, see [Protecting Data Using Server\-Side Encryption with KMS keys Stored in AWS Key Management Service \(SSE\-KMS\)](https://docs.aws.amazon.com/AmazonS3/latest/userguide/UsingKMSEncryption.html) in the *Amazon Simple Storage Service User Guide*\.
   + If you want Amazon RDS to access to access other buckets, add the ARNs for these buckets\. Optionally, you can also grant access to all buckets and objects in Amazon S3\.

1. Choose **Next: Tags** and then **Next: Review**\.

1. For **Name**, enter a name for your IAM policy, for example `rds-s3-integration-policy`\. You use this name when you create an IAM role to associate with your DB instance\. You can also add an optional **Description** value\.

1. Choose **Create policy**\.

#### AWS CLI<a name="oracle-s3-integration.preparing.policy.CLI"></a>

Create an AWS Identity and Access Management \(IAM\) policy that grants Amazon RDS access to an Amazon S3 bucket\. After you create the policy, note the ARN of the policy\. You need the ARN for a subsequent step\.

Include the appropriate actions in the policy based on the type of access required:
+ `GetObject` – Required to transfer files from an Amazon S3 bucket to Amazon RDS\.
+ `ListBucket` – Required to transfer files from an Amazon S3 bucket to Amazon RDS\.
+ `PutObject` – Required to transfer files from Amazon RDS to an Amazon S3 bucket\.

The following AWS CLI command creates an IAM policy named `rds-s3-integration-policy` with these options\. It grants access to a bucket named `your-s3-bucket-arn`\.

**Example**  
For Linux, macOS, or Unix:  

```
aws iam create-policy \
   --policy-name rds-s3-integration-policy \
   --policy-document '{
     "Version": "2012-10-17",
     "Statement": [
       {
         "Sid": "s3integration",
         "Action": [
           "s3:GetObject",
           "s3:ListBucket",
           "s3:PutObject"
         ],
         "Effect": "Allow",
         "Resource": [
           "arn:aws:s3:::your-s3-bucket-arn", 
           "arn:aws:s3:::your-s3-bucket-arn/*"
         ]
       }
     ]
   }'
```
The following example includes permissions for custom KMS keys\.  

```
aws iam create-policy \
   --policy-name rds-s3-integration-policy \
   --policy-document '{
     "Version": "2012-10-17",
     "Statement": [
       {
         "Sid": "s3integration",
         "Action": [
           "s3:GetObject",
           "s3:ListBucket",
           "s3:PutObject",
           "kms:Decrypt",
           "kms:Encrypt",
           "kms:ReEncrypt",
           "kms:GenerateDataKey",
           "kms:DescribeKey",
         ],
         "Effect": "Allow",
         "Resource": [
           "arn:aws:s3:::your-s3-bucket-arn", 
           "arn:aws:s3:::your-s3-bucket-arn/*",
           "arn:aws:kms:::your-kms-arn"
         ]
       }
     ]
   }'
```
For Windows:  

```
aws iam create-policy ^
   --policy-name rds-s3-integration-policy ^
   --policy-document '{
     "Version": "2012-10-17",
     "Statement": [
       {
         "Sid": "s3integration",
         "Action": [
           "s3:GetObject",
           "s3:ListBucket",
           "s3:PutObject"
         ],
         "Effect": "Allow",
         "Resource": [
           "arn:aws:s3:::your-s3-bucket-arn", 
           "arn:aws:s3:::your-s3-bucket-arn/*"
         ]
       }
     ]
   }'
```
The following example includes permissions for custom KMS keys\.  

```
aws iam create-policy ^
   --policy-name rds-s3-integration-policy ^
   --policy-document '{
     "Version": "2012-10-17",
     "Statement": [
       {
         "Sid": "s3integration",
         "Action": [
           "s3:GetObject",
           "s3:ListBucket",
           "s3:PutObject",
           "kms:Decrypt",
           "kms:Encrypt",
           "kms:ReEncrypt",
           "kms:GenerateDataKey",
           "kms:DescribeKey",
         ],
         "Effect": "Allow",
         "Resource": [
           "arn:aws:s3:::your-s3-bucket-arn", 
           "arn:aws:s3:::your-s3-bucket-arn/*",
           "arn:aws:kms:::your-kms-arn"
         ]
       }
     ]
   }'
```

### Step 2: \(Optional\) Create an IAM policy for your Amazon S3 bucket<a name="oracle-s3-integration.preparing.policy-bucket"></a>

This step is necessary only in the following conditions:
+ You plan to upload files to an Amazon S3 bucket from one account \(account A\) and access them from a different account \(account B\)\.
+ Account B owns the bucket\.
+ Account B needs full control of objects loaded into the bucket\.

If the preceding conditions don't apply to you, skip to [Step 3: Create an IAM role for your DB instance and attach your policy](#oracle-s3-integration.preparing.role)\.

To create your bucket policy, make sure you have the following:
+ The account ID for account A
+ The user name for account A
+ The ARN value for the Amazon S3 bucket in account B

#### Console<a name="oracle-s3-integration.preparing.policy-bucket.console"></a>

**To create or edit a bucket policy**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the **Buckets** list, choose the name of the bucket that you want to create a bucket policy for or whose bucket policy you want to edit\.

1. Choose **Permissions**\.

1. Under **Bucket policy**, choose **Edit**\. This opens the Edit bucket policy page\.

1. On the **Edit bucket policy **page, explore **Policy examples** in the *Amazon S3 User Guide*, choose **Policy generator** to generate a policy automatically, or edit the JSON in the **Policy** section\. 

   If you choose **Policy generator**, the AWS Policy Generator opens in a new window:

   1. On the **AWS Policy Generator** page, in **Select Type of Policy**, choose **S3 Bucket Policy**\.

   1. Add a statement by entering the information in the provided fields, and then choose **Add Statement**\. Repeat for as many statements as you would like to add\. For more information about these fields, see the [IAM JSON policy elements reference](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements.html) in the *IAM User Guide*\. 
**Note**  
For convenience, the **Edit bucket policy** page displays the **Bucket ARN **\(Amazon Resource Name\) of the current bucket above the **Policy** text field\. You can copy this ARN for use in the statements on the **AWS Policy Generator** page\. 

   1. After you finish adding statements, choose **Generate Policy**\.

   1. Copy the generated policy text, choose **Close**, and return to the **Edit bucket policy** page in the Amazon S3 console\.

1. In the **Policy** box, edit the existing policy or paste the bucket policy from the Policy generator\. Make sure to resolve security warnings, errors, general warnings, and suggestions before you save your policy\.

   ```
   {
     "Version": "2012-10-17",
     "Statement": [
       {
         "Sid": "Example permissions",
         "Effect": "Allow",
         "Principal": {
           "AWS": "arn:aws:iam::account-A-ID:account-A-user"
         },
         "Action": [
           "s3:PutObject",
           "s3:PutObjectAcl"
         ],
         "Resource": [
           "arn:aws:s3:::account-B-bucket-arn",
           "arn:aws:s3:::account-B-bucket-arn/*"
         ]
       }
     ]
   }
   ```

1. Choose **Save changes**, which returns you to the Bucket Permissions page\.

### Step 3: Create an IAM role for your DB instance and attach your policy<a name="oracle-s3-integration.preparing.role"></a>

This step assumes that you have created the IAM policy in [Step 1: Create an IAM policy for your Amazon RDS role](#oracle-s3-integration.preparing.policy)\. In this step, you create a role for your RDS for Oracle DB instance and then attach your policy to the role\.

#### Console<a name="oracle-s3-integration.preparing.role.console"></a>

**To create an IAM role to allow Amazon RDS access to an Amazon S3 bucket**

1. Open the [IAM Management Console](https://console.aws.amazon.com/iam/home?#home)\.

1. In the navigation pane, choose **Roles**\.

1. Choose **Create role**\.

1. For **AWS service**, choose **RDS**\.

1. For **Select your use case**, choose **RDS – Add Role to Database**\.

1. Choose **Next**\.

1. For **Search** under **Permissions policies**, enter the name of the IAM policy you created, and choose the policy when it appears in the list\.

1. Choose **Next**\.

1. Set **Role name** to a name for your IAM role, for example `rds-s3-integration-role`\. You can also add an optional **Description** value\.

1. Choose **Create role**\.

#### AWS CLI<a name="integration.preparing.role.CLI"></a>

**To create a role and attach your policy to it**

1. Create an IAM role that Amazon RDS can assume on your behalf to access your Amazon S3 buckets\.

   We recommend using the [https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html#condition-keys-sourcearn](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html#condition-keys-sourcearn) and [https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html#condition-keys-sourceaccount](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html#condition-keys-sourceaccount) global condition context keys in resource\-based trust relationships to limit the service's permissions to a specific resource\. This is the most effective way to protect against the [confused deputy problem](https://docs.aws.amazon.com/IAM/latest/UserGuide/confused-deputy.html)\.

   You might use both global condition context keys and have the `aws:SourceArn` value contain the account ID\. In this case, the `aws:SourceAccount` value and the account in the `aws:SourceArn` value must use the same account ID when used in the same statement\.
   + Use `aws:SourceArn` if you want cross\-service access for a single resource\.
   + Use `aws:SourceAccount` if you want to allow any resource in that account to be associated with the cross\-service use\.

   In the trust relationship, make sure to use the `aws:SourceArn` global condition context key with the full Amazon Resource Name \(ARN\) of the resources accessing the role\.

   The following AWS CLI command creates the role named `rds-s3-integration-role` for this purpose\.  
**Example**  

   For Linux, macOS, or Unix:

   ```
   aws iam create-role \
      --role-name rds-s3-integration-role \
      --assume-role-policy-document '{
        "Version": "2012-10-17",
        "Statement": [
          {
            "Effect": "Allow",
            "Principal": {
               "Service": "rds.amazonaws.com"
             },
            "Action": "sts:AssumeRole",
            "Condition": {
                "StringEquals": {
                    "aws:SourceAccount": my_account_ID,
                    "aws:SourceArn": "arn:aws:rds:Region:my_account_ID:db:dbname"
                }
            }
          }
        ]
      }'
   ```

   For Windows:

   ```
   aws iam create-role ^
      --role-name rds-s3-integration-role ^
      --assume-role-policy-document '{
        "Version": "2012-10-17",
        "Statement": [
          {
            "Effect": "Allow",
            "Principal": {
               "Service": "rds.amazonaws.com"
             },
            "Action": "sts:AssumeRole",
            "Condition": {
                "StringEquals": {
                    "aws:SourceAccount": my_account_ID,
                    "aws:SourceArn": "arn:aws:rds:Region:my_account_ID:db:dbname"
                }
            }
          }
        ]
      }'
   ```

   For more information, see [Creating a role to delegate permissions to an IAM user](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-user.html) in the *IAM User Guide*\.

1. After the role is created, note the ARN of the role\. You need the ARN for a subsequent step\.

1. Attach the policy you created to the role you created\.

   The following AWS CLI command attaches the policy to the role named `rds-s3-integration-role`\.  
**Example**  

   For Linux, macOS, or Unix:

   ```
   aws iam attach-role-policy \
      --policy-arn your-policy-arn \
      --role-name rds-s3-integration-role
   ```

   For Windows:

   ```
   aws iam attach-role-policy ^
      --policy-arn your-policy-arn ^
      --role-name rds-s3-integration-role
   ```

   Replace `your-policy-arn` with the policy ARN that you noted in a previous step\.

### Step 4: Associate your IAM role with your RDS for Oracle DB instance<a name="oracle-s3-integration.preparing.instance"></a>

The last step in configuring permissions for Amazon S3 integration is associating your IAM role with your DB instance\. Note the following requirements:
+ You must have access to an IAM role with the required Amazon S3 permissions policy attached to it\. 
+ You can only associate one IAM role with your RDS for Oracle DB instance at a time\.
+ The status of your instance must be `available`\.

#### Console<a name="oracle-s3-integration.preparing.instance.console"></a>

**To associate your IAM role with your RDS for Oracle DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. Choose **Databases** from the navigation pane\.

1. If your database instance is unavailable, choose **Actions** and then **Start**\. When the instance status shows **Started**, go to the next step\.

1. Choose the Oracle DB instance name to display its details\.

1. On the **Connectivity & security** tab, scroll down to the **Manage IAM roles** section at the bottom of the page\.

1. Choose the role to add in the **Add IAM roles to this instance** section\.

1. For **Feature**, choose **S3\_INTEGRATION**\.  
![\[Add S3_INTEGRATION role\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/ora-s3-integration-role.png)

1. Choose **Add role**\.

#### AWS CLI<a name="oracle-s3-integration.preparing.instance.CLI"></a>

The following AWS CLI command adds the role to an Oracle DB instance named `mydbinstance`\.

**Example**  
For Linux, macOS, or Unix:  

```
aws rds add-role-to-db-instance \
   --db-instance-identifier mydbinstance \
   --feature-name S3_INTEGRATION \
   --role-arn your-role-arn
```
For Windows:  

```
aws rds add-role-to-db-instance ^
   --db-instance-identifier mydbinstance ^
   --feature-name S3_INTEGRATION ^
   --role-arn your-role-arn
```

Replace `your-role-arn` with the role ARN that you noted in a previous step\. `S3_INTEGRATION` must be specified for the `--feature-name` option\.

## Adding the Amazon S3 integration option<a name="oracle-s3-integration.preparing.option-group"></a>

To use Amazon RDS for Oracle Integration with Amazon S3, your Amazon RDS for Oracle DB instance must be associated with an option group that includes the `S3_INTEGRATION` option\.

### Console<a name="oracle-s3-integration.preparing.option-group.console"></a>

**To configure an option group for Amazon S3 integration**

1. Create a new option group or identify an existing option group to which you can add the `S3_INTEGRATION` option\.

   For information about creating an option group, see [Creating an option group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.Create)\.

1. Add the `S3_INTEGRATION` option to the option group\.

   For information about adding an option to an option group, see [Adding an option to an option group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.AddOption)\.

1. Create a new Oracle DB instance and associate the option group with it, or modify an Oracle DB instance to associate the option group with it\.

   For information about creating a DB instance, see [Creating an Amazon RDS DB instance](USER_CreateDBInstance.md)\.

   For information about modifying an Oracle DB instance, see [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)\.

### AWS CLI<a name="oracle-s3-integration.preparing.option-group.cli"></a>

**To configure an option group for Amazon S3 integration**

1. Create a new option group or identify an existing option group to which you can add the `S3_INTEGRATION` option\.

   For information about creating an option group, see [Creating an option group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.Create)\.

1. Add the `S3_INTEGRATION` option to the option group\.

   For example, the following AWS CLI command adds the `S3_INTEGRATION` option to an option group named **myoptiongroup**\.  
**Example**  

   For Linux, macOS, or Unix:

   ```
   aws rds add-option-to-option-group \
      --option-group-name myoptiongroup \
      --options OptionName=S3_INTEGRATION,OptionVersion=1.0
   ```

   For Windows:

   ```
   aws rds add-option-to-option-group ^
      --option-group-name myoptiongroup ^
      --options OptionName=S3_INTEGRATION,OptionVersion=1.0
   ```

1. Create a new Oracle DB instance and associate the option group with it, or modify an Oracle DB instance to associate the option group with it\.

   For information about creating a DB instance, see [Creating an Amazon RDS DB instance](USER_CreateDBInstance.md)\.

   For information about modifying an Oracle DB instance, see [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)\.

## Transferring files between Amazon RDS for Oracle and an Amazon S3 bucket<a name="oracle-s3-integration.using"></a>

To transfer files between an Oracle DB instance and an Amazon S3 bucket, you can use the Amazon RDS package `rdsadmin_s3_tasks`\. You can compress files with GZIP when uploading them, and decompress them when downloading\.

**Note**  
The procedures in `rdsadmin_s3_tasks` upload or download the files in a single directory\. You can't include subdirectories in these operations\.

**Topics**
+ [Uploading files from your RDS for Oracle DB instance to an Amazon S3 bucket](#oracle-s3-integration.using.upload)
+ [Downloading files from an Amazon S3 bucket to an Oracle DB instance](#oracle-s3-integration.using.download)
+ [Monitoring the status of a file transfer](#oracle-s3-integration.using.task-status)

### Uploading files from your RDS for Oracle DB instance to an Amazon S3 bucket<a name="oracle-s3-integration.using.upload"></a>

To upload files from your DB instance to an Amazon S3 bucket, use the procedure `rdsadmin.rdsadmin_s3_tasks.upload_to_s3`\. For example, you can upload Oracle Recovery Manager \(RMAN\) backup files or Oracle Data Pump files\. The maximum object size in an Amazon S3 bucket is 5 TB\. For more information about working with objects, see [Amazon Simple Storage Service User Guide](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingObjects.html)\. For more information about performing RMAN backups, see [Performing common RMAN tasks for Oracle DB instances](Appendix.Oracle.CommonDBATasks.RMAN.md)\.

The `rdsadmin.rdsadmin_s3_tasks.upload_to_s3` procedure has the following parameters\.


****  

| Parameter name | Data type | Default | Required | Description | 
| --- | --- | --- | --- | --- | 
|  `p_bucket_name`  |  VARCHAR2  |  –  |  required  |  The name of the Amazon S3 bucket to upload files to\.   | 
|  `p_directory_name`  |  VARCHAR2  |  –  |  required  |  The name of the Oracle directory object to upload files from\. The directory can be any user\-created directory object or the Data Pump directory, such as `DATA_PUMP_DIR`\.   You can only upload files from the specified directory\. You can't upload files in subdirectories in the specified directory\.   | 
|  `p_s3_prefix`  |  VARCHAR2  |  –  |  required  |  An Amazon S3 file name prefix that files are uploaded to\. An empty prefix uploads all files to the top level in the specified Amazon S3 bucket and doesn't add a prefix to the file names\.  For example, if the prefix is `folder_1/oradb`, files are uploaded to `folder_1`\. In this case, the `oradb` prefix is added to each file\.   | 
|  `p_prefix`  |  VARCHAR2  |  –  |  required  |  A file name prefix that file names must match to be uploaded\. An empty prefix uploads all files in the specified directory\.   | 
|  `p_compression_level`  |  NUMBER  |  `0`   |  optional  |  The level of GZIP compression\. Valid values range from `0` to `9`: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/oracle-s3-integration.html)  | 
|  `p_bucket_owner_full_control`  |  VARCHAR2  |  –  |  optional  |  The access control setting for the bucket\. The only valid values are null or `FULL_CONTROL`\. This setting is required only if you upload files from one account \(account A\) into a bucket owned by a different account \(account B\), and account B needs full control of the files\.  | 

The return value for the `rdsadmin.rdsadmin_s3_tasks.upload_to_s3` procedure is a task ID\.

The following example uploads all of the files in the `DATA_PUMP_DIR` directory to the Amazon S3 bucket named `mys3bucket`\. The files aren't compressed\.

```
SELECT rdsadmin.rdsadmin_s3_tasks.upload_to_s3(
      p_bucket_name    =>  'mys3bucket',
      p_prefix         =>  '', 
      p_s3_prefix      =>  '', 
      p_directory_name =>  'DATA_PUMP_DIR') 
   AS TASK_ID FROM DUAL;
```

The following example uploads all of the files with the prefix `db` in the `DATA_PUMP_DIR` directory to the Amazon S3 bucket named `mys3bucket`\. Amazon RDS applies the highest level of GZIP compression to the files\.

```
SELECT rdsadmin.rdsadmin_s3_tasks.upload_to_s3(
      p_bucket_name       =>  'mys3bucket', 
      p_prefix            =>  'db', 
      p_s3_prefix         =>  '', 
      p_directory_name    =>  'DATA_PUMP_DIR',
      p_compression_level =>  9) 
   AS TASK_ID FROM DUAL;
```

The following example uploads all of the files in the `DATA_PUMP_DIR` directory to the Amazon S3 bucket named `mys3bucket`\. The files are uploaded to a `dbfiles` folder\. In this example, the GZIP compression level is *1*, which is the fastest level of compression\.

```
SELECT rdsadmin.rdsadmin_s3_tasks.upload_to_s3(
      p_bucket_name       =>  'mys3bucket', 
      p_prefix            =>  '', 
      p_s3_prefix         =>  'dbfiles/', 
      p_directory_name    =>  'DATA_PUMP_DIR',
      p_compression_level =>  1) 
   AS TASK_ID FROM DUAL;
```

The following example uploads all of the files in the `DATA_PUMP_DIR` directory to the Amazon S3 bucket named `mys3bucket`\. The files are uploaded to a `dbfiles` folder and `ora` is added to the beginning of each file name\. No compression is applied\.

```
SELECT rdsadmin.rdsadmin_s3_tasks.upload_to_s3(
      p_bucket_name    =>  'mys3bucket', 
      p_prefix         =>  '', 
      p_s3_prefix      =>  'dbfiles/ora', 
      p_directory_name =>  'DATA_PUMP_DIR') 
   AS TASK_ID FROM DUAL;
```

The following example assumes that the command is run in account A, but account B requires full control of the bucket contents\. The command `rdsadmin_s3_tasks.upload_to_s3` transfers all files in the `DATA_PUMP_DIR` directory to the bucket named `s3bucketOwnedByAccountB`\. Access control is set to `FULL_CONTROL` so that account B can access the files in the bucket\. The GZIP compression level is *6*, which balances speed and file size\.

```
SELECT rdsadmin.rdsadmin_s3_tasks.upload_to_s3(
      p_bucket_name               =>  's3bucketOwnedByAccountB', 
      p_prefix                    =>  '', 
      p_s3_prefix                 =>  '', 
      p_directory_name            =>  'DATA_PUMP_DIR',
      p_bucket_owner_full_control =>  'FULL_CONTROL',
      p_compression_level         =>  6) 
   AS TASK_ID FROM DUAL;
```

In each example, the `SELECT` statement returns the ID of the task in a `VARCHAR2` data type\.

You can view the result by displaying the task's output file\.

```
SELECT text FROM table(rdsadmin.rds_file_util.read_text_file('BDUMP','dbtask-task-id.log'));
```

Replace *`task-id`* with the task ID returned by the procedure\.

**Note**  
Tasks are executed asynchronously\.

### Downloading files from an Amazon S3 bucket to an Oracle DB instance<a name="oracle-s3-integration.using.download"></a>

To download files from an Amazon S3 bucket to an RDS for Oracle instance, use the Amazon RDS procedure `rdsadmin.rdsadmin_s3_tasks.download_from_s3`\. 

When you download files using the procedure `download_from_s3`, consider the following:
+ The download limit is 2000 files per procedure call\. If you need to download more than 2000 files from Amazon S3, split your download into separate actions, with no more than 2000 files per procedure call\. 
+ If a file exists in your download folder, and you attempt to download a file with the same name, `download_from_s3` skips the download\. To remove a file from the download directory, use [UTL\_FILE\.FREMOVE](https://docs.oracle.com/en/database/oracle/oracle-database/19/arpls/UTL_FILE.html#GUID-09B09C2A-2C21-4F70-BF04-D0EEA7B59CAF), found on the Oracle website\.

The `download_from_s3` procedure has the following parameters\.


****  

| Parameter name | Data type | Default | Required | Description | 
| --- | --- | --- | --- | --- | 
|  `p_bucket_name`  |  VARCHAR2  |  –  |  Required  |  The name of the Amazon S3 bucket to download files from\.   | 
|  `p_directory_name`  |  VARCHAR2  |  –  |  Required  |  The name of the Oracle directory object to download files to\. The directory can be any user\-created directory object or the Data Pump directory, such as `DATA_PUMP_DIR`\.   | 
|  `p_error_on_zero_downloads`  |  VARCHAR2  | FALSE |  Optional  |  A flag that determines whether the task raises an error when no objects in the Amazon S3 bucket match the prefix\. If this parameter is not set or set to FALSE \(default\), the task prints a message that no objects were found, but doesn't raise an exception or fail\. If this parameter is TRUE, the task raises an exception and fails\.  Examples of prefix specifications that can fail match tests are spaces in prefixes, as in `' import/test9.log'`, and case mismatches, as in `test9.log` and `test9.LOG`\.  | 
|  `p_s3_prefix`  |  VARCHAR2  |  –  |  Required  |  A file name prefix that file names must match to be downloaded\. An empty prefix downloads all of the top level files in the specified Amazon S3 bucket, but not the files in folders in the bucket\.  The procedure downloads Amazon S3 objects only from the first level folder that matches the prefix\. Nested directory structures matching the specified prefix are not downloaded\. For example, suppose that an Amazon S3 bucket has the folder structure `folder_1/folder_2/folder_3`\. You specify the `'folder_1/folder_2/'` prefix\. In this case, only the files in `folder_2` are downloaded, not the files in `folder_1` or `folder_3`\. If, instead, you specify the `'folder_1/folder_2'` prefix, all files in `folder_1` that match the `'folder_2'` prefix are downloaded, and no files in `folder_2` are downloaded\.  | 
|  `p_decompression_format`  |  VARCHAR2  |  –  |  Optional  |  The decompression format\. Valid values are `NONE` for no decompression and `GZIP` for decompression\.  | 

The return value for the `rdsadmin.rdsadmin_s3_tasks.download_from_s3` procedure is a task ID\.

The following example downloads all files in the Amazon S3 bucket named `mys3bucket` to the `DATA_PUMP_DIR` directory\. The files aren't compressed, so no decompression is applied\.

```
SELECT rdsadmin.rdsadmin_s3_tasks.download_from_s3(
      p_bucket_name    =>  'mys3bucket',
      p_directory_name =>  'DATA_PUMP_DIR') 
   AS TASK_ID FROM DUAL;
```

The following example downloads all of the files with the prefix `db` in the Amazon S3 bucket named `mys3bucket` to the `DATA_PUMP_DIR` directory\. The files are compressed with GZIP, so decompression is applied\. The parameter `p_error_on_zero_downloads` turns on prefix error checking, so if the prefix doesn't match any files in the bucket, the task raises and exception and fails\.

```
SELECT rdsadmin.rdsadmin_s3_tasks.download_from_s3(
      p_bucket_name               =>  'mys3bucket', 
      p_s3_prefix                 =>  'db', 
      p_directory_name            =>  'DATA_PUMP_DIR',
      p_decompression_format      =>  'GZIP',
      p_error_on_zero_downloads   =>  'TRUE') 
   AS TASK_ID FROM DUAL;
```

The following example downloads all of the files in the folder `myfolder/` in the Amazon S3 bucket named `mys3bucket` to the `DATA_PUMP_DIR` directory\. Use the `p_s3_prefix` parameter to specify the Amazon S3 folder\. The uploaded files are compressed with GZIP, but aren't decompressed during the download\. 

```
SELECT rdsadmin.rdsadmin_s3_tasks.download_from_s3(
      p_bucket_name          =>  'mys3bucket', 
      p_s3_prefix            =>  'myfolder/', 
      p_directory_name       =>  'DATA_PUMP_DIR',
      p_decompression_format =>  'NONE')
   AS TASK_ID FROM DUAL;
```

The following example downloads the file `mydumpfile.dmp` in the Amazon S3 bucket named `mys3bucket` to the `DATA_PUMP_DIR` directory\. No decompression is applied\.

```
SELECT rdsadmin.rdsadmin_s3_tasks.download_from_s3(
      p_bucket_name    =>  'mys3bucket', 
      p_s3_prefix      =>  'mydumpfile.dmp', 
      p_directory_name =>  'DATA_PUMP_DIR') 
   AS TASK_ID FROM DUAL;
```

In each example, the `SELECT` statement returns the ID of the task in a `VARCHAR2` data type\.

You can view the result by displaying the task's output file\.

```
SELECT text FROM table(rdsadmin.rds_file_util.read_text_file('BDUMP','dbtask-task-id.log'));
```

Replace *`task-id`* with the task ID returned by the procedure\.

**Note**  
Tasks are executed asynchronously\.  
You can use the `UTL_FILE.FREMOVE` Oracle procedure to remove files from a directory\. For more information, see [FREMOVE procedure](https://docs.oracle.com/database/121/ARPLS/u_file.htm#ARPLS70924) in the Oracle documentation\.

### Monitoring the status of a file transfer<a name="oracle-s3-integration.using.task-status"></a>

File transfer tasks publish Amazon RDS events when they start and when they complete\. For information about viewing events, see [Viewing Amazon RDS events](USER_ListEvents.md)\.

You can view the status of an ongoing task in a bdump file\. The bdump files are located in the `/rdsdbdata/log/trace` directory\. Each bdump file name is in the following format\.

```
dbtask-task-id.log
```

Replace `task-id` with the ID of the task that you want to monitor\.

**Note**  
Tasks are executed asynchronously\.

You can use the `rdsadmin.rds_file_util.read_text_file` stored procedure to view the contents of bdump files\. For example, the following query returns the contents of the `dbtask-1546988886389-2444.log` bdump file\.

```
SELECT text FROM table(rdsadmin.rds_file_util.read_text_file('BDUMP','dbtask-1546988886389-2444.log'));
```

## Removing the Amazon S3 integration option<a name="oracle-s3-integration.removing"></a>

You can remove Amazon S3 integration option from a DB instance\. 

To remove the Amazon S3 integration option from a DB instance, do one of the following: 
+ To remove the Amazon S3 integration option from multiple DB instances, remove the `S3_INTEGRATION` option from the option group to which the DB instances belong\. This change affects all DB instances that use the option group\. For more information, see [Removing an option from an option group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.RemoveOption)\.

   
+ To remove the Amazon S3 integration option from a single DB instance, modify the instance and specify a different option group that doesn't include the `S3_INTEGRATION` option\. You can specify the default \(empty\) option group or a different custom option group\. For more information, see [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)\.