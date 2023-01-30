# Setting up your environment for Amazon RDS Custom for Oracle<a name="custom-setup-orcl"></a>

Before you create a DB instance based on Amazon RDS Custom for Oracle, perform the following tasks\.

**Topics**
+ [Prerequisites for creating an RDS Custom for Oracle instance](#custom-setup-orcl.review)
+ [Make sure that you have a symmetric encryption AWS KMS key](#custom-setup-orcl.cmk)
+ [Download and install the AWS CLI](#custom-setup-orcl.cli)
+ [Configuring IAM and your VPC](#custom-setup-orcl.iam-vpc)
+ [Grant required permissions to your IAM user](#custom-setup-orcl.iam-user)

## Prerequisites for creating an RDS Custom for Oracle instance<a name="custom-setup-orcl.review"></a>

Before creating an RDS Custom for Oracle DB instance, make sure that you meet the following prerequisites:
+ You have access to [My Oracle Support](https://support.oracle.com/portal/) and [Oracle Software Delivery Cloud](https://edelivery.oracle.com/osdc/faces/Home.jspx) to download the supported list of installation files and patches for the Enterprise Edition of any of the following Oracle Database releases:
  + Oracle Database 19c
  + Oracle Database 18c
  + Oracle Database 12c Release 2 \(12\.2\)
  + Oracle Database 12c Release 1 \(12\.1\)

  If you use an unknown patch, custom engine version \(CEV\) creation fails\. In this case, contact the RDS Custom support team and ask it to add the missing patch\.

  For more information, see [Downloading your database installation files and patches from Oracle Software Delivery Cloud](custom-cev.preparing.md#custom-cev.preparing.download)\.
+ You have access to Amazon S3 so that you can upload your Oracle installation files\. You use the installation files when you create your RDS Custom CEV\.

  For more information, see [Uploading your installation files to Amazon S3](custom-cev.preparing.md#custom-cev.preparing.s3) and [Creating a CEV](custom-cev.create.md)\.
+ You supply your own virtual private cloud \(VPC\) and security group configuration\. For more information, see [Configuring IAM and your VPC](#custom-setup-orcl.iam-vpc)\.
+ The AWS Identity and Access Management \(IAM\) user that creates a CEV or RDS Custom DB instance has the required permissions for IAM, CloudTrail, and Amazon S3\.

  For more information, see [Grant required permissions to your IAM user](#custom-setup-orcl.iam-user)\.

For each task, the following sections describe the requirements and limitations specific to the task\. For example, when you create your RDS Custom DB for Oracle instance, use either the db\.m5 or db\.r5 instance classes running Oracle Linux 7 Update 6\. For general requirements that apply to RDS Custom, see [Availability and requirements for Amazon RDS Custom for Oracle](custom-reqs-limits.md)\.

## Make sure that you have a symmetric encryption AWS KMS key<a name="custom-setup-orcl.cmk"></a>

A symmetric encryption AWS KMS key is required for RDS Custom\. When you create an RDS Custom for Oracle DB instance, you supply the KMS key identifier\. For more information, see [Configuring a DB instance for Amazon RDS Custom for Oracle](custom-creating.md)\.

You have the following options:
+ If you have an existing KMS key in your AWS account, you can use it with RDS Custom\. No further action is necessary\.
+ If you already created a symmetric encryption KMS key for a different RDS Custom engine, you can reuse the same KMS key\. No further action is necessary\.
+ If you don't have an existing symmetric encryption KMS key in your account, create a KMS key by following the instructions in [Creating keys](https://docs.aws.amazon.com/kms/latest/developerguide/create-keys.html#create-symmetric-cmk) in the *AWS Key Management Service Developer Guide*\.
+ If you're creating a CEV or RDS Custom DB instance, and your KMS key is in a different AWS account, make sure to use the AWS CLI\. You can't use the AWS console with cross\-account KMS keys\.

RDS Custom doesn't support AWS\-managed KMS keys\.

Make sure that the symmetric encryption key that you use gives the AWS Identity and Access Management \(IAM\) role in your IAM instance profile access to the `kms:Decrypt` and `kms:GenerateDataKey` operations\. If you have a new symmetric encryption key in your account, no changes are required\. Otherwise, make sure that your symmetric encryption key's policy can give access to these operations\.

For more information about configuring IAM for RDS Custom for Oracle, see [Configuring IAM and your VPC](#custom-setup-orcl.iam-vpc)\.

## Download and install the AWS CLI<a name="custom-setup-orcl.cli"></a>

AWS provides you with a command\-line interface to use RDS Custom features\. You can use either version 1 or version 2 of the AWS CLI\.

For information about downloading and installing the AWS CLI, see [Installing or updating the latest version of the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)\.

If you plan to access RDS Custom only from the AWS Management Console, skip this step\.

If you have already downloaded the AWS CLI for Amazon RDS or a different RDS Custom engine, skip this step\.

## Configuring IAM and your VPC<a name="custom-setup-orcl.iam-vpc"></a>

You can configure your IAM role and virtual private cloud \(VPC\) using either of the following techniques:
+ [Configuring IAM and your VPC using AWS CloudFormation](#custom-setup-orcl.cf) \(recommended\)
+ Following the procedures in [Creating your IAM role and instance profile manually](#custom-setup-orcl.iam) and [Configuring your VPC manually](#custom-setup-orcl.vpc)

### Configuring IAM and your VPC using AWS CloudFormation<a name="custom-setup-orcl.cf"></a>

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

   If you already configured your VPC for RDS Custom for SQL Server, skip this step\.

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

   CloudFormation creates the IAM roles that RDS Custom for Oracle requires\.

#### Step 3: Configure your VPC using CloudFormation<a name="custom-setup-orcl.cf.config-vpc"></a>

This procedure assumes that you've already used CloudFormation to create your IAM roles\. If you've already configured your VPC for a different RDS Custom engine, skip this step\.

Before beginning the following procedure, make sure that you know your route table ID\. 

**To configure your VPC using CloudFormation**

1. Open the CloudFormation console at [https://console\.aws\.amazon\.com/cloudformation](https://console.aws.amazon.com/cloudformation/)\.

1. Start the Create Stack wizard, and choose **Create Stack**\.

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

1. On the **Review custom\-vpc** page, do the following:

   1. Select the ****I acknowledge that AWS CloudFormation might create IAM resources with custom names**** check box\.

   1. Choose **Submit**\.

   CloudFormation configures your VPC\.

### Creating your IAM role and instance profile manually<a name="custom-setup-orcl.iam"></a>

To use RDS Custom, you create an IAM instance profile named `AWSRDSCustomInstanceProfileForRdsCustomInstance`\. You also create the IAM role `AWSRDSCustomInstanceRoleForRdsCustomInstance` for the instance profile\. You then add `AWSRDSCustomInstanceRoleForRdsCustomInstance` to your instance profile\.

The following section explains how to perform the task without using CloudFormation\.

**To create the RDS Custom instance profile and add the necessary role to it**

1. Create the IAM role named `AWSRDSCustomInstanceRoleForRdsCustomInstance` with a trust policy that Amazon EC2 can use to assume this role\.

1. Add an access policy to `AWSRDSCustomInstanceRoleForRdsCustomInstance`\.

1. Create an IAM instance profile for RDS Custom named `AWSRDSCustomInstanceProfileForRdsCustomInstance`\.

1. Add the `AWSRDSCustomInstanceRoleForRdsCustomInstance` IAM role to the instance profile\.

#### Create the role AWSRDSCustomInstanceRoleForRdsCustomInstance<a name="custom-setup-orcl.iam.create-role"></a>

The following example creates the role `AWSRDSCustomInstanceRoleForRdsCustomInstance`\. Using the trust policy, Amazon EC2 can assume the role\.

```
aws iam create-role \
  --role-name AWSRDSCustomInstanceRoleForRdsCustomInstance \
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

#### Add an access policy to AWSRDSCustomInstanceRoleForRdsCustomInstance<a name="custom-setup-orcl.iam.add-policy"></a>

When you embed an inline policy in an IAM role, the inline policy is used as part of the role's access \(permissions\) policy\. You create the `AWSRDSCustomIamRolePolicy` policy that permits Amazon EC2 to send and receive messages and perform various actions\.

The following example creates the access policy named `AWSRDSCustomIamRolePolicy`, and adds it to the IAM role `AWSRDSCustomInstanceRoleForRdsCustomInstance`\. This example assumes that you have set the `$REGION` and `$ACCOUNT_ID` variables in the AWS CLI\. This example also requires the Amazon Resource Name \(ARN\) of the AWS KMS key that you want to use for your RDS Custom DB instances\.

To specify more than one KMS key, add it to the `Resources` section of statement ID \(Sid\) 11\.

```
aws iam put-role-policy \
  --role-name AWSRDSCustomInstanceRoleForRdsCustomInstance \
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
              "arn:aws:kms:'$REGION':'$ACCOUNT_ID':key/abcd1234-5678-eeff-9012-123456abcdef"
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

#### Create your RDS Custom instance profile<a name="custom-setup-orcl.iam.create-profile"></a>

Create your IAM instance profile as follows, naming it `AWSRDSCustomInstanceProfileForRdsCustomInstance`\.

```
aws iam create-instance-profile \
    --instance-profile-name AWSRDSCustomInstanceProfileForRdsCustomInstance
```

#### Add AWSRDSCustomInstanceRoleForRdsCustomInstance to your RDS Custom instance profile<a name="custom-setup-orcl.iam.add-profile"></a>

Add the IAM role `AWSRDSCustomInstanceRoleForRdsCustomInstance` to the profile `AWSRDSCustomInstanceProfileForRdsCustomInstance`\.

```
aws iam add-role-to-instance-profile \
    --instance-profile-name AWSRDSCustomInstanceProfileForRdsCustomInstance \
    --role-name AWSRDSCustomInstanceRoleForRdsCustomInstance
```

### Configuring your VPC manually<a name="custom-setup-orcl.vpc"></a>

Your RDS Custom DB instance is in a virtual private cloud \(VPC\) based on the Amazon VPC service, just like an Amazon EC2 instance or Amazon RDS instance\. You provide and configure your own VPC\. Thus, you have full control over your instance networking setup\.

RDS Custom sends communication from your DB instance to other AWS services\. To make sure that RDS Custom can communicate, it validates network connectivity to these services\.

Your DB instance communicates with the following AWS services:
+ Amazon CloudWatch
+ Amazon CloudWatch Logs
+ Amazon CloudWatch Events
+ Amazon EC2
+ Amazon EventBridge
+ Amazon S3
+ AWS Secrets Manager
+ AWS Systems Manager

Make sure that VPC components involved in communication between the DB instance and AWS services are configured with the following requirements:
+ The DB instance can make outbound connections on port 443 to other AWS services\.
+ The VPC allows incoming responses to requests originating from your DB instance\.
+ Correctly resolve the domain names of endpoints for each AWS service\.

If you already configured a VPC for a different RDS Custom engine, you can reuse that VPC and skip this process\.

**Topics**
+ [Configure your instance security group](#custom-setup-orcl.vpc.sg)
+ [Configure endpoints for dependent AWS services](#custom-setup-orcl.vpc.endpoints)
+ [Configure the instance metadata service](#custom-setup-orcl.vpc.imds)

#### Configure your instance security group<a name="custom-setup-orcl.vpc.sg"></a>

A *security group* acts as a virtual firewall for a VPC instance, controlling both inbound and outbound traffic\. An RDS Custom DB instance has a default security group that protects the instance\. Make sure that your security group permits traffic between RDS Custom and other AWS services\.

**To configure your security group for RDS Custom**

1. Sign in to the AWS Management Console and open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc](https://console.aws.amazon.com/vpc)\. 

1. Allow RDS Custom to use the default security group, or create your own security group\.

   For detailed instructions, see [Provide access to your DB instance in your VPC by creating a security group](CHAP_SettingUp.md#CHAP_SettingUp.SecurityGroup)\.

1. Make sure that your security group permits outbound connections on port 443\. RDS Custom needs this port to communicate with dependent AWS services\.

1. If you have a private VPC and use VPC endpoints, make sure that the security group associated with the DB instance allows outbound connections on port 443 to VPC endpoints\. Also make sure that the security group associated with the VPC endpoint allows inbound connections on port 443 from the DB instance\.

   If incoming connections aren't allowed, the RDS Custom instance can't connect to the AWS Systems Manager and Amazon EC2 endpoints\. For more information, see [Create a Virtual Private Cloud endpoint](https://docs.aws.amazon.com/systems-manager/latest/userguide/setup-create-vpc.html) in the *AWS Systems Manager User Guide*\.

For more information about security groups, see [Security groups for your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html) in the *Amazon VPC Developer Guide*\.

#### Configure endpoints for dependent AWS services<a name="custom-setup-orcl.vpc.endpoints"></a>

Make sure that your VPC allows outbound traffic to the following AWS services with which the DB instance communicates:
+ Amazon CloudWatch
+ Amazon CloudWatch Logs
+ Amazon CloudWatch Events
+ Amazon EC2
+ Amazon EventBridge
+ Amazon S3
+ AWS Secrets Manager
+ AWS Systems Manager

We recommend that you add endpoints for every service to your VPC using the following instructions\. However, you can use any solution that lets your VPC communicate with AWS service endpoints\. For example, you can use Network Address Translation \(NAT\) or AWS Direct Connect\.

**To configure endpoints for AWS services with which RDS Custom works**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. On the navigation bar, use the Region selector to choose the AWS Region\.

1. In the navigation pane, choose **Endpoints**\. In the main pane, choose **Create Endpoint**\.

1. For **Service category**, choose **AWS services**\.

1. For **Service Name**, choose the endpoint shown in the table\.

1. For **VPC**, choose your VPC\.

1. For **Subnets**, choose a subnet from each Availability Zone to include\.

   The VPC endpoint can span multiple Availability Zones\. AWS creates an elastic network interface for the VPC endpoint in each subnet that you choose\. Each network interface has a Domain Name System \(DNS\) host name and a private IP address\.

1. For **Security group**, choose or create a security group\.

   You can use security groups to control access to your endpoint, much as you use a firewall\. For more information about security groups, see [Security groups for your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html) in the *Amazon VPC User Guide*\. 

1. Optionally, you can attach a policy to the VPC endpoint\. Endpoint policies can control access to the AWS service to which you are connecting\. The default policy allows all requests to pass through the endpoint\. If you're using a custom policy, make sure that requests from the DB instance are allowed in the policy\.

1. Choose **Create endpoint**\.

The following table explains how to find the list of endpoints that your VPC needs for outbound communications\.


| Service | Endpoint format | Notes and links | 
| --- | --- | --- | 
|  AWS Systems Manager  |  Use the following endpoint formats: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/custom-setup-orcl.html)  |  For the list of endpoints in each Region, see [AWS Systems Manager endpoints and quotas](https://docs.aws.amazon.com/general/latest/gr/ssm.html) in the *Amazon Web Services General Reference*\.  | 
|  AWS Secrets Manager  |  Use the endpoint format `secretsmanager.region.amazonaws.com`\.  |  For the list of endpoints in each Region, see [AWS Secrets Manager endpoints and quotas](https://docs.aws.amazon.com/general/latest/gr/asm.html) in the *Amazon Web Services General Reference*\.  | 
|  Amazon CloudWatch  |  Use the following endpoint formats: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/custom-setup-orcl.html)  | For the list of endpoints in every Region, see: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/custom-setup-orcl.html) | 
|  Amazon EC2  |  Use the following endpoint formats: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/custom-setup-orcl.html)  |  For the list of endpoints in each Region, see [Amazon Elastic Compute Cloud endpoints and quotas](https://docs.aws.amazon.com/general/latest/gr/ec2-service.html) in the *Amazon Web Services General Reference*\.  | 
|  Amazon S3  |  Use the endpoint format `s3.region.amazonaws.com`\.  |  For the list of endpoints in each Region, see [Amazon Simple Storage Service endpoints and quotas](https://docs.aws.amazon.com/general/latest/gr/s3.html) in the *Amazon Web Services General Reference*\.  To learn more about gateway endpoints for Amazon S3, see [Endpoints for Amazon S3](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-endpoints-s3.html) in the *Amazon VPC Developer Guide*\.  To learn how to create an access point, see [Creating access points](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/access-points-create-ap.html) in the *Amazon VPC Developer Guide*\. To learn how to create a gateway endpoints for Amazon S3, see [Gateway VPC endpoints](https://docs.aws.amazon.com/vpc/latest/privatelink/vpce-gateway.html)\.  | 

#### Configure the instance metadata service<a name="custom-setup-orcl.vpc.imds"></a>

Make sure that your instance can do the following:
+ Access the instance metadata service using Instance Metadata Service Version 2 \(IMDSv2\)\.
+ Allow outbound communications through port 80 \(HTTP\) to the IMDS link IP address\.
+ Request instance metadata from `http://169.254.169.254`, the IMDSv2 link\.

For more information, see [Use IMDSv2](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/configuring-instance-metadata-service.html) in the *Amazon EC2 User Guide for Linux Instances*\.

RDS Custom for Oracle automation uses IMDSv2 by default, by setting `HttpTokens=enabled` on the underlying Amazon EC2 instance\. However, you can use IMDSv1 if you want\. For more information, see [Configure the instance metadata options](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/configuring-instance-metadata-options.html) in the *Amazon EC2 User Guide for Linux Instances*\.

## Grant required permissions to your IAM user<a name="custom-setup-orcl.iam-user"></a>

Make sure that the IAM principal that creates the CEV or DB instance has either of the following policies:
+ The `AdministratorAccess` policy
+ The `AmazonRDSFullAccess` policy with required permissions for Amazon S3 and AWS KMS \(required for both CEV and DB creation\), CEV creation, and DB instance creation\.

**Topics**
+ [Permissions required for Amazon S3 and AWS KMS](#custom-setup-orcl.s3-kms)
+ [Permissions required for creating a CEV](#custom-setup-orcl.cev)
+ [Permissions required for creating a DB instance from a CEV](#custom-setup-orcl.db)

### Permissions required for Amazon S3 and AWS KMS<a name="custom-setup-orcl.s3-kms"></a>

To create CEVs or RDS Custom for Oracle DB instances, the IAM principal needs to access Amazon S3 and AWS KMS\. The following sample JSON policy grants the required permissions\.

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

### Permissions required for creating a CEV<a name="custom-setup-orcl.cev"></a>

To create a CEV, the IAM principal needs the following additional permissions:

```
s3:GetObjectAcl
s3:GetObject
s3:GetObjectTagging
s3:ListBucket
mediaimport:CreateDatabaseBinarySnapshot
```

The following sample JSON policy grants the additional permissions to bucket *my\-custom\-installation\-files* and it contents\.

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

### Permissions required for creating a DB instance from a CEV<a name="custom-setup-orcl.db"></a>

To create a DB instance from an existing CEV, the IAM principal needs the following additional permissions:

```
iam:SimulatePrincipalPolicy
cloudtrail:CreateTrail
cloudtrail:StartLogging
```

The following sample JSON policy grants the required permissions\.

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