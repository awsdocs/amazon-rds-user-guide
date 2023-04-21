# Setting up your environment for Amazon RDS Custom for Oracle<a name="custom-setup-orcl"></a>

Before you create an Amazon RDS Custom for Oracle DB instance, perform the following tasks\.

**Topics**
+ [Prerequisites for creating an RDS Custom for Oracle DB instance](#custom-setup-orcl.review)
+ [Step 1: Create or reuse a symmetric encryption AWS KMS key](#custom-setup-orcl.cmk)
+ [Step 2: Download and install the AWS CLI](#custom-setup-orcl.cli)
+ [Step 3: Configure IAM and your VPC](#custom-setup-orcl.iam-vpc)
+ [Step 4: Grant required permissions to your IAM principal](#custom-setup-orcl.iam-user)

## Prerequisites for creating an RDS Custom for Oracle DB instance<a name="custom-setup-orcl.review"></a>

Before creating an RDS Custom for Oracle DB instance, make sure that you meet the following prerequisites:
+ You have access to [My Oracle Support](https://support.oracle.com/portal/) and [Oracle Software Delivery Cloud](https://edelivery.oracle.com/osdc/faces/Home.jspx) to download the supported list of installation files and patches for the Enterprise Edition of any of the following Oracle Database releases:
  + Oracle Database 19c
  + Oracle Database 18c
  + Oracle Database 12c Release 2 \(12\.2\)
  + Oracle Database 12c Release 1 \(12\.1\)

  If you use an unknown patch, custom engine version \(CEV\) creation fails\. In this case, contact the RDS Custom support team and ask it to add the missing patch\.

  For more information, see [Step 2: Download your database installation files and patches from Oracle Software Delivery Cloud](custom-cev.preparing.md#custom-cev.preparing.download)\.
+ You have access to Amazon S3\. This service is required for the following reasons:
  + You upload your Oracle installation files to S3 buckets\. You use the uploaded installation files to create your RDS Custom CEV\.
  + RDS Custom for Oracle uses scripts downloaded from internally defined S3 buckets to perform actions on your DB instances\. These scripts are necessary for onboarding and RDS Custom automation\.
  + RDS Custom for Oracle uploads certain files to S3 buckets located in your customer account\. These buckets use the following naming format: `do-not-delete-rds-custom-`*account\_id*\-*region*\-*six\_character\_alphanumeric\_string*\. For example, you might have a bucket named `do-not-delete-rds-custom-123456789012-us-east-1-12a3b4`\.

  For more information, see [Step 3: Upload your installation files to Amazon S3](custom-cev.preparing.md#custom-cev.preparing.s3) and [Creating a CEV](custom-cev.create.md)\.
+ You supply your own virtual private cloud \(VPC\) and security group configuration\. For more information, see [Step 3: Configure IAM and your VPC](#custom-setup-orcl.iam-vpc)\.
+ The AWS Identity and Access Management \(IAM\) user that creates a CEV or RDS Custom DB instance has the required permissions for IAM, CloudTrail, and Amazon S3\.

  For more information, see [Step 4: Grant required permissions to your IAM principal](#custom-setup-orcl.iam-user)\.

For each task, the following sections describe the requirements and limitations specific to the task\. For example, when you create your RDS Custom DB for Oracle instance, use either the db\.m5 or db\.r5 instance classes running Oracle Linux 7 Update 9\. For general requirements that apply to RDS Custom, see [Availability and requirements for Amazon RDS Custom for Oracle](custom-reqs-limits.md)\.

## Step 1: Create or reuse a symmetric encryption AWS KMS key<a name="custom-setup-orcl.cmk"></a>

*Customer managed keys* are AWS KMS keys in your AWS account that you create, own, and manage\. A customer managed symmetric encryption KMS key is required for RDS Custom\. When you create an RDS Custom for Oracle DB instance, you supply the KMS key identifier\. For more information, see [Configuring a DB instance for Amazon RDS Custom for Oracle](custom-creating.md)\.

You have the following options:
+ If you have an existing customer managed KMS key in your AWS account, you can use it with RDS Custom\. No further action is necessary\.
+ If you already created a customer managed symmetric encryption KMS key for a different RDS Custom engine, you can reuse the same KMS key\. No further action is necessary\.
+ If you don't have an existing customer managed symmetric encryption KMS key in your account, create a KMS key by following the instructions in [Creating keys](https://docs.aws.amazon.com/kms/latest/developerguide/create-keys.html#create-symmetric-cmk) in the *AWS Key Management Service Developer Guide*\.
+ If you're creating a CEV or RDS Custom DB instance, and your KMS key is in a different AWS account, make sure to use the AWS CLI\. You can't use the AWS console with cross\-account KMS keys\.

**Important**  
RDS Custom doesn't support AWS managed KMS keys\.

Make sure that your symmetric encryption key grants access to the `kms:Decrypt` and `kms:GenerateDataKey` operations to the AWS Identity and Access Management \(IAM\) role in your IAM instance profile\. If you have a new symmetric encryption key in your account, no changes are required\. Otherwise, make sure that your symmetric encryption key's policy grants access to these operations\.

For more information, see [Step 3: Configure IAM and your VPC](#custom-setup-orcl.iam-vpc)\.

For more information about configuring IAM for RDS Custom for Oracle, see [Step 3: Configure IAM and your VPC](#custom-setup-orcl.iam-vpc)\.

## Step 2: Download and install the AWS CLI<a name="custom-setup-orcl.cli"></a>

AWS provides you with a command\-line interface to use RDS Custom features\. You can use either version 1 or version 2 of the AWS CLI\.

For information about downloading and installing the AWS CLI, see [Installing or updating the latest version of the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)\.

Skip this step if either of the following is true:
+ You plan to access RDS Custom only from the AWS Management Console\.
+ You have already downloaded the AWS CLI for Amazon RDS or a different RDS Custom DB engine\.

## Step 3: Configure IAM and your VPC<a name="custom-setup-orcl.iam-vpc"></a>

Your RDS Custom DB instance is in a virtual private cloud \(VPC\) based on the Amazon VPC service, just like an Amazon EC2 instance or Amazon RDS instance\. You provide and configure your own VPC\. Thus, you have full control over your instance networking setup\.

You can configure your IAM role and virtual private cloud \(VPC\) using either of the following techniques:
+ [Configure IAM and your VPC using AWS CloudFormation](#custom-setup-orcl.cf) \(recommended\)
+ Follow the procedures in [Create your IAM role and instance profile manually](#custom-setup-orcl.iam) and [Configure your VPC manually](#custom-setup-orcl.vpc)

### Configure IAM and your VPC using AWS CloudFormation<a name="custom-setup-orcl.cf"></a>

To simplify setup, you can use the AWS CloudFormation template files to create CloudFormation stacks\. To learn how to create stacks, see [Creating a stack on the AWS CloudFormation console](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-console-create-stack.html) in *AWS CloudFormation User Guide*\.

**Topics**
+ [Step 1: Download the CloudFormation template files](#custom-setup-orcl.cf.dl-templates)
+ [Step 2: Configure IAM using CloudFormation](#custom-setup-orcl.cf.config-iam)
+ [Step 3: Configure your VPC using CloudFormation](#custom-setup-orcl.cf.config-vpc)

#### Step 1: Download the CloudFormation template files<a name="custom-setup-orcl.cf.dl-templates"></a>

**To download the template files**

1. Open the context \(right\-click\) menu for the link [ custom\-oracle\-iam\.zip](samples/custom-oracle-iam.zip) and choose **Save Link As**\.

1. Save the file to your computer\.

1. Repeat the previous steps for the link [custom\-vpc\.zip](samples/custom-vpc.zip)\.

   If you already configured your VPC for RDS Custom, skip this step\.

#### Step 2: Configure IAM using CloudFormation<a name="custom-setup-orcl.cf.config-iam"></a>

**To configure IAM using CloudFormation**

1. Open the CloudFormation console at [https://console\.aws\.amazon\.com/cloudformation](https://console.aws.amazon.com/cloudformation/)\.

1. Start the Create Stack wizard, and choose **Create Stack**\.

1. On the **Create stack** page, do the following:

   1. For **Prepare template**, choose **Template is ready**\.

   1. For **Template source**, choose **Upload a template file**\.

   1. For **Choose file**, navigate to, then choose `custom-oracle-iam.json`\.

   1. Choose **Next**\.

1. On the **Specify stack details** page, do the following:

   1. For **Stack name**, enter **custom\-oracle\-iam**\.

   1. Choose **Next**\.

1. On the **Configure stack options page**, choose **Next**\.

1. On the **Review custom\-oracle\-iam** page, do the following:

   1. Select the ****I acknowledge that AWS CloudFormation might create IAM resources with custom names**** check box\.

   1. Choose **Submit**\.

   CloudFormation creates the IAM roles that RDS Custom for Oracle requires\. In the left panel, when **custom\-oracle\-iam** shows **CREATE\_COMPLETE**, proceed to the next step\.

1. In the left panel, choose **custom\-oracle\-iam**\. In the right panel, do the following:

   1. Choose **Stack info**\. Your stack has an ID in the format **arn:aws:cloudformation:*region*:*account\-no*:stack/custom\-oracle\-iam/*identifier***\.

   1. Choose **Resources**\. You should see an instance profile named **AWSRDSCustomInstanceProfile\-*region*** and a service role named **AWSRDSCustomInstanceRole\-*region***\. When you create your DB instance, you need to supply the instance profile ID\.

#### Step 3: Configure your VPC using CloudFormation<a name="custom-setup-orcl.cf.config-vpc"></a>

This procedure assumes that you've already used CloudFormation to create your IAM roles\. If you've already configured your VPC for a different RDS Custom engine, and want to reuse this VPC, skip this step\.

Before beginning the following procedure, make sure that you know your route table ID\. For a subnet to be private, it must not be associated with a route table that has a default internet gateway\. For more information, see [Configure route tables](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Route_Tables.html) in the *Amazon VPC User Guide*\.

**To configure your VPC using CloudFormation**

1. Open the CloudFormation console at [https://console\.aws\.amazon\.com/cloudformation](https://console.aws.amazon.com/cloudformation/)\.

1. Start the Create Stack wizard, and choose **Create Stack** and then **With new resources \(standard\)**\.

1. On the **Create stack** page, do the following:

   1. For **Prepare template**, choose **Template is ready**\.

   1. For **Template source**, choose **Upload a template file**\.

   1. For **Choose file**, navigate to, then choose `custom-vpc.json`\.

   1. Choose **Next**\.

1. On the **Specify stack details** page, do the following:

   1. For **Stack name**, enter **custom\-vpc**\.

   1. For **Parameters**, choose the private subnets to use for RDS Custom DB instances\.

   1. Choose the private VPC ID to use for RDS Custom DB instances\.

   1. Enter the route table associated with the private subnets\.

   1. Choose **Next**\.

1. On the **Configure stack options page**, choose **Next**\.

1. On the **Review custom\-vpc** page, choose **Submit**\.

   CloudFormation configures your private VPC\. In the left panel, when **custom\-vpc** shows **CREATE\_COMPLETE**, proceed to the next step\.

1. In the left panel, choose **custom\-vpc**\. In the right panel, do the following:

   1. Choose **Stack info**\. Your stack has an ID in the format **arn:aws:cloudformation:*region*:*account\-no*:stack/custom\-vpc/*identifier***\.

   1. Choose **Resources**\. You should see a subnet group named **rds\-custom\-private** and several VPC endpoints that use the naming format **vpce\-*string***\. Each endpoint corresponds to an AWS service that RDS Custom needs to communicate with\.

   1. Choose **Parameters**\. You should see the private subnets, private VPC, and the route table that you specified when you created the stack\. When you create a DB instance, you need to supply the VPC ID and subnet group\.

### Create your IAM role and instance profile manually<a name="custom-setup-orcl.iam"></a>

For manual setup, you create an IAM instance profile named `AWSRDSCustomInstanceProfile-region`, where *region* is the AWS Region where you plan to deploy your DB instances\. You also create the IAM role `AWSRDSCustomInstanceRole-region` for the instance profile\. You then add this role to your instance profile\.

The following section explains how to perform the task without using CloudFormation\.

**To create the RDS Custom instance profile and add the necessary role to it**

1. Create the IAM role that uses the naming format `AWSRDSCustomInstanceRole-region` with a trust policy that Amazon EC2 can use to assume this role\.

1. Add an access policy to `AWSRDSCustomInstanceRole-region`\.

1. Create an IAM instance profile for RDS Custom that uses the naming format `AWSRDSCustomInstanceProfile-region`\.

1. Add the `AWSRDSCustomInstanceRole-region` IAM role to the instance profile\.

#### Step 1: Create the role AWSRDSCustomInstanceRoleForRdsCustomInstance<a name="custom-setup-orcl.iam.create-role"></a>

In this step, you create the role using the naming format `AWSRDSCustomInstanceRole-region`\. Using the trust policy, Amazon EC2 can assume the role\. The following example assumes that you have set the environment variable `$REGION` to the AWS Region in which you want to create your DB instance\.

```
aws iam create-role \
  --role-name AWSRDSCustomInstanceRole-$REGION \
  --assume-role-policy-document '{
    "Version": "2012-10-17",
      "Statement": [
        {
          "Action": "sts:AssumeRole",
          "Effect": "Allow",
          "Principal": {
              "Service": "ec2.amazonaws.com"
          }
        }
      ]
    }'
```

#### Step 2: Add an access policy to AWSRDSCustomInstanceRoleForRdsCustomInstance<a name="custom-setup-orcl.iam.add-policy"></a>

When you embed an inline policy in an IAM role, the inline policy is used as part of the role's access \(permissions\) policy\. You create the `AWSRDSCustomIamRolePolicy` policy that permits Amazon EC2 to send and receive messages and perform various actions\.

The following example creates the access policy named `AWSRDSCustomIamRolePolicy`, and adds it to the IAM role `AWSRDSCustomInstanceRole-region`\. This example assumes that you have set the following environment variables:

`$REGION`  
Set this variable to the AWS Region in which you plan to create your DB instance\.

`$ACCOUNT_ID`  
Set this variable to your AWS account number\.

`$KMS_KEY`  
Set this variable to the Amazon Resource Name \(ARN\) of the AWS KMS key that you want to use for your RDS Custom DB instances\. To specify more than one KMS key, add it to the `Resources` section of statement ID \(Sid\) 11\.

```
aws iam put-role-policy \
  --role-name AWSRDSCustomInstanceRole-$REGION \
  --policy-name AWSRDSCustomIamRolePolicy \
  --policy-document '{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "1",
            "Effect": "Allow",
            "Action": [
                "ssm:DescribeAssociation",
                "ssm:GetDeployablePatchSnapshotForInstance",
                "ssm:GetDocument",
                "ssm:DescribeDocument",
                "ssm:GetManifest",
                "ssm:GetParameter",
                "ssm:GetParameters",
                "ssm:ListAssociations",
                "ssm:ListInstanceAssociations",
                "ssm:PutInventory",
                "ssm:PutComplianceItems",
                "ssm:PutConfigurePackageResult",
                "ssm:UpdateAssociationStatus",
                "ssm:UpdateInstanceAssociationStatus",
                "ssm:UpdateInstanceInformation",
                "ssm:GetConnectionStatus",
                "ssm:DescribeInstanceInformation",
                "ssmmessages:CreateControlChannel",
                "ssmmessages:CreateDataChannel",
                "ssmmessages:OpenControlChannel",
                "ssmmessages:OpenDataChannel"
            ],
            "Resource": [
                "*"
            ]
        },
        {
            "Sid": "2",
            "Effect": "Allow",
            "Action": [
                "ec2messages:AcknowledgeMessage",
                "ec2messages:DeleteMessage",
                "ec2messages:FailMessage",
                "ec2messages:GetEndpoint",
                "ec2messages:GetMessages",
                "ec2messages:SendReply"
            ],
            "Resource": [
                "*"
            ]
        },
        {
            "Sid": "3",
            "Effect": "Allow",
            "Action": [
                "logs:PutRetentionPolicy",
                "logs:PutLogEvents",
                "logs:DescribeLogStreams",
                "logs:DescribeLogGroups",
                "logs:CreateLogStream",
                "logs:CreateLogGroup"
            ],
            "Resource": [
                "arn:aws:logs:'$REGION':*:log-group:rds-custom-instance*"
            ]
        },
        {
            "Sid": "4",
            "Effect": "Allow",
            "Action": [
                "s3:putObject",
                "s3:getObject",
                "s3:getObjectVersion"
            ],
            "Resource": [
                "arn:aws:s3:::do-not-delete-rds-custom-*/*"
            ]
        },
        {
            "Sid": "5",
            "Effect": "Allow",
            "Action": [
                "cloudwatch:PutMetricData"
            ],
            "Resource": [
                "*"
            ],
            "Condition": {
                "StringEquals": {
                    "cloudwatch:namespace": [
                        "RDSCustomForOracle/Agent"
                    ]
                }
            }
        },
        {
            "Sid": "6",
            "Effect": "Allow",
            "Action": [
                "events:PutEvents"
            ],
            "Resource": [
                "*"
            ]
        },
        {
            "Sid": "7",
            "Effect": "Allow",
            "Action": [
                "secretsmanager:GetSecretValue",
                "secretsmanager:DescribeSecret"
            ],
            "Resource": [
                "arn:aws:secretsmanager:'$REGION':'$ACCOUNT_ID':secret:do-not-delete-rds-custom-*"
            ]
        },
        {
           "Sid": "8",
           "Effect": "Allow",
           "Action": [
             "s3:ListBucketVersions"
           ],
           "Resource": [
             "arn:aws:s3:::do-not-delete-rds-custom-*"
           ]
         },
         {
            "Sid": "9",
            "Effect": "Allow",
            "Action": "ec2:CreateSnapshots",
            "Resource": [
                "arn:aws:ec2:*:*:instance/*",
                "arn:aws:ec2:*:*:volume/*"
            ],
            "Condition": {
                "StringEquals": {
                    "ec2:ResourceTag/AWSRDSCustom": "custom-oracle"
                }
            }
          },
          {
            "Sid": "10",
            "Effect": "Allow",
            "Action": "ec2:CreateSnapshots",
            "Resource": [
                "arn:aws:ec2:*::snapshot/*"
            ]
          },
          {
            "Sid": "11",
            "Effect": "Allow",
            "Action": [
              "kms:Decrypt",
              "kms:GenerateDataKey"
            ],
            "Resource": [
              "arn:aws:kms:'$REGION':'$ACCOUNT_ID':key/$KMS_KEY"
            ]
          },
          {
            "Sid": "12",
            "Effect": "Allow",
            "Action": "ec2:CreateTags",
            "Resource": "*",
            "Condition": {
                "StringLike": {
                    "ec2:CreateAction": [
                        "CreateSnapshots"
                    ]
                }
            }
        }
    ]
}'
```

#### Step 3: Create your RDS Custom instance profile<a name="custom-setup-orcl.iam.create-profile"></a>

Create your IAM instance profile as follows, naming it using the format `AWSRDSCustomInstanceProfile-region`\. The following example assumes that you have set the environment variable `$REGION` to the AWS Region in which you want to create your DB instance\.

```
aws iam create-instance-profile \
    --instance-profile-name AWSRDSCustomInstanceProfile-$REGION
```

#### Step 4: Add AWSRDSCustomInstanceRoleForRdsCustomInstance to your RDS Custom instance profile<a name="custom-setup-orcl.iam.add-profile"></a>

Add your IAM role to the instance profile that you previously created\. The following example assumes that you have set the environment variable `$REGION` to the AWS Region in which you want to create your DB instance\.

```
aws iam add-role-to-instance-profile \
    --instance-profile-name AWSRDSCustomInstanceProfile-$REGION \
    --role-name AWSRDSCustomInstanceRole-$REGION
```

### Configure your VPC manually<a name="custom-setup-orcl.vpc"></a>

If you don't want to use AWS CloudFormation, you can configure your VPC endpoints manually\.

**Topics**
+ [Create VPC endpoints for dependent AWS services](#custom-setup-orcl.vpc.endpoints)
+ [Configure the instance metadata service](#custom-setup-orcl.vpc.imds)

#### Create VPC endpoints for dependent AWS services<a name="custom-setup-orcl.vpc.endpoints"></a>

RDS Custom sends communication from your DB instance to other AWS services\. To make sure that RDS Custom can communicate, it validates network connectivity to the following AWS services:
+ Amazon CloudWatch
+ Amazon CloudWatch Logs
+ Amazon CloudWatch Events
+ Amazon EC2
+ Amazon EventBridge
+ Amazon S3
+ AWS Secrets Manager
+ AWS Systems Manager

If RDS Custom can't communicate with the necessary services, it publishes the following event:

```
Database instance in incompatible-network. SSM Agent connection not available. Amazon RDS can't connect to the dependent AWS services.
```

To avoid `incompatible-network` errors, make sure that VPC components involved in communication between your RDS Custom DB instance and AWS services satisfy the following requirements:
+ The DB instance can make outbound connections on port 443 to other AWS services\.
+ The VPC allows incoming responses to requests originating from your RDS Custom DB instance\.
+ RDS Custom can correctly resolve the domain names of endpoints for each AWS service\.

RDS Custom relies on AWS Systems Manager connectivity for its automation\. For information about how to configure VPC endpoints, see [Creating VPC endpoints for Systems Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/setup-create-vpc.html#sysman-setting-up-vpc-create)\. For the list of endpoints in each Region, see [AWS Systems Manager endpoints and quotas](https://docs.aws.amazon.com/general/latest/gr/ssm.html) in the *Amazon Web Services General Reference*\.

If you already configured a VPC for a different RDS Custom DB engine, you can reuse that VPC and skip this process\.

#### Configure the instance metadata service<a name="custom-setup-orcl.vpc.imds"></a>

Make sure that your instance can do the following:
+ Access the instance metadata service using Instance Metadata Service Version 2 \(IMDSv2\)\.
+ Allow outbound communications through port 80 \(HTTP\) to the IMDS link IP address\.
+ Request instance metadata from `http://169.254.169.254`, the IMDSv2 link\.

For more information, see [Use IMDSv2](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/configuring-instance-metadata-service.html) in the *Amazon EC2 User Guide for Linux Instances*\.

RDS Custom for Oracle automation uses IMDSv2 by default, by setting `HttpTokens=enabled` on the underlying Amazon EC2 instance\. However, you can use IMDSv1 if you want\. For more information, see [Configure the instance metadata options](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/configuring-instance-metadata-options.html) in the *Amazon EC2 User Guide for Linux Instances*\.

## Step 4: Grant required permissions to your IAM principal<a name="custom-setup-orcl.iam-user"></a>

Make sure that the IAM principal that creates the CEV or DB instance has either of the following policies:
+ The `AdministratorAccess` policy
+ The `AmazonRDSFullAccess` policy with required permissions for Amazon S3 and AWS KMS \(required for both CEV and DB creation\), CEV creation, and DB instance creation\.

**Topics**
+ [IAM permissions required for Amazon S3 and AWS KMS](#custom-setup-orcl.s3-kms)
+ [IAM permissions required for creating a CEV](#custom-setup-orcl.cev)
+ [IAM permissions required for creating a DB instance from a CEV](#custom-setup-orcl.db)

### IAM permissions required for Amazon S3 and AWS KMS<a name="custom-setup-orcl.s3-kms"></a>

To create CEVs or RDS Custom for Oracle DB instances, the IAM identity needs to access Amazon S3 and AWS KMS\. The following sample JSON policy grants the required permissions\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "CreateS3Bucket",
            "Effect": "Allow",
            "Action": [
                "s3:CreateBucket",
                "s3:PutBucketPolicy",
                "s3:PutBucketObjectLockConfiguration",
                "s3:PutBucketVersioning"
            ],
            "Resource": "arn:aws:s3:::do-not-delete-rds-custom-*"
        },
        {
            "Sid": "CreateKmsGrant",
            "Effect": "Allow",
            "Action": [
                "kms:CreateGrant",
                "kms:DescribeKey"
            ],
            "Resource": "*"
        }
    ]
}
```

For more information about the `kms:CreateGrant` permission, see [AWS KMS key management](Overview.Encryption.Keys.md)\.

### IAM permissions required for creating a CEV<a name="custom-setup-orcl.cev"></a>

To create a CEV, the IAM identity needs the following additional permissions:

```
s3:GetObjectAcl
s3:GetObject
s3:GetObjectTagging
s3:ListBucket
mediaimport:CreateDatabaseBinarySnapshot
```

The following sample JSON policy grants the additional permissions necessary to access bucket *my\-custom\-installation\-files* and its contents\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AccessToS3MediaBucket",
            "Effect": "Allow",
            "Action": [
                "s3:GetObjectAcl",
                "s3:GetObject",
                "s3:GetObjectTagging",
                "s3:ListBucket"
            ],
            "Resource": [
                "arn:aws:s3:::my-custom-installation-files",
                "arn:aws:s3:::my-custom-installation-files/*"
            ]
        },
        {
            "Sid": "PermissionForByom",
            "Effect": "Allow",
            "Action": [
                "mediaimport:CreateDatabaseBinarySnapshot"
            ],
            "Resource": "*"
        }
    ]
}
```

You can grant similar permissions for Amazon S3 to caller accounts using an S3 bucket policy\.

### IAM permissions required for creating a DB instance from a CEV<a name="custom-setup-orcl.db"></a>

To create an RDS Custom for Oracle DB instance from an existing CEV, the IAM entity needs the following additional permissions\.

```
iam:SimulatePrincipalPolicy
cloudtrail:CreateTrail
cloudtrail:StartLogging
```

The following sample JSON policy grants the permissions necessary to validate an IAM role and log information to an AWS CloudTrail\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "ValidateIamRole",
            "Effect": "Allow",
            "Action": "iam:SimulatePrincipalPolicy",
            "Resource": "*"
        },
        {
            "Sid": "CreateCloudTrail",
            "Effect": "Allow",
            "Action": [
                "cloudtrail:CreateTrail",
                "cloudtrail:StartLogging"
            ],
            "Resource": "arn:aws:cloudtrail:*:*:trail/do-not-delete-rds-custom-*"
        }
    ]
}
```