# Setting up your environment for Amazon RDS Custom for SQL Server<a name="custom-setup-sqlserver"></a>

Before you create and manage a DB instance for Amazon RDS Custom for SQL Server DB instance, make sure to perform the following tasks\.

**Topics**
+ [Prerequisites for setting up RDS Custom for SQL Server](#custom-setup-sqlserver.review)
+ [Make sure that you have a symmetric encryption AWS KMS key](#custom-setup-sqlserver.cmk)
+ [Download and install the AWS CLI](#custom-setup-sqlserver.cli)
+ [Configuring IAM and your VPC](#custom-setup-sqlserver.iam-vpc)
+ [Grant required permissions to your IAM user](#custom-setup-sqlserver.iam-user)

## Prerequisites for setting up RDS Custom for SQL Server<a name="custom-setup-sqlserver.review"></a>

Before creating an RDS Custom for SQL Server DB instance, make sure that you have your own virtual private cloud \(VPC\) and security group configuration\. This network configuration must meet the requirements of RDS Custom\.

For each task, RDS Custom topics describe the requirements and limitations specific to that task\. For example, when you create your RDS Custom for SQL Server DB instance, use one of the SQL Server instances listed in [DB instance class support for RDS Custom for SQL Server](custom-reqs-limits-MS.md#custom-reqs-limits.instancesMS)\.

For general requirements that apply to RDS Custom for SQL Server, see [General requirements for RDS Custom for SQL Server](custom-reqs-limits-MS.md#custom-reqs-limits.reqsMS)\.

## Make sure that you have a symmetric encryption AWS KMS key<a name="custom-setup-sqlserver.cmk"></a>

A symmetric encryption AWS KMS key is required for RDS Custom\. When you create an RDS Custom for SQL Server DB instance, make sure to supply the AWS KMS key identifier\. For more information, see [Creating and connecting to a DB instance for Amazon RDS Custom for SQL Server](custom-creating-sqlserver.md)\.

You have the following options:
+ If you have an existing KMS key in your account, you can use it with RDS Custom\. No further action is necessary\.
+ If you have already created a symmetric encryption KMS key for a different RDS Custom engine, you can reuse the same KMS key\. No further action is necessary\.
+ If you don't have an existing symmetric encryption KMS key in your account, create a KMS key by following the instructions in [Creating keys](https://docs.aws.amazon.com/kms/latest/developerguide/create-keys.html#create-symmetric-cmk) in the *AWS Key Management Service Developer Guide*\.

RDS Custom doesn't support AWS\-managed KMS keys\.

The symmetric encryption key that you use must provide the AWS Identity and Access Management \(IAM\) role in your IAM instance profile with access to the `kms:Decrypt` and `kms:GenerateDataKey` operations\. If you have a new symmetric encryption key in your account, no changes are required\. Otherwise, make sure that your symmetric encryption key's policy can provide access to these operations\.

For more information on configuring IAM for RDS Custom for SQL Server, see [Configuring IAM and your VPC](#custom-setup-sqlserver.iam-vpc)\.

## Download and install the AWS CLI<a name="custom-setup-sqlserver.cli"></a>

AWS provides you with a command\-line interface to use RDS Custom features\. You can use either version 1 or version 2 of the AWS CLI\.

For information on downloading and installing the AWS CLI, see [Installing or updating the latest version of the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)\.

If you plan to access RDS Custom only from the AWS Management Console, skip this step\.

If you have already downloaded the AWS CLI for Amazon RDS or RDS Custom for Oracle, skip this step\.

## Configuring IAM and your VPC<a name="custom-setup-sqlserver.iam-vpc"></a>

You can configure your IAM role and VPC by using the procedures in the following:
+ [Configuring IAM and your VPC using AWS CloudFormation](#custom-setup-sqlserver.cf) \(recommended\)
+ Following the procedures in [Creating your IAM role and instance profile manually](#custom-setup-sqlserver.iam) and [Configuring your VPC manually](#custom-setup-sqlserver.vpc)

### Configuring IAM and your VPC using AWS CloudFormation<a name="custom-setup-sqlserver.cf"></a>

To simplify setup, you can use the AWS CloudFormation template files to create CloudFormation stacks\. To learn how to create stacks, see [Creating a stack on the AWS CloudFormation console](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-console-create-stack.html) in *AWS CloudFormation User Guide*\.

**To download the template files**

1. Open the context \(right\-click\) menu for the link [custom\-sqlserver\-iam\.json](./images/custom-sqlserver-iam.json) and choose **Save Link As**\.

1. Save the file to your computer\.

1. Repeat the previous steps for the link [custom\-vpc\.json](./images/custom-vpc.json)\.

   If you already configured your VPC for RDS Custom for Oracle, you can skip this step\.

**To configure IAM using CloudFormation**

1. Open the CloudFormation console at [https://console\.aws\.amazon\.com/cloudformation](https://console.aws.amazon.com/cloudformation/)\.

1. Start the Create Stack wizard, and choose **Create Stack**\.

1. On the **Specify template** page, do the following:

   1. For **Template source**, choose **Upload a template file**\.

   1. For **Choose file**, navigate to, then choose `custom-sqlserver-iam.json`\.

   1. Choose **Next**\.

1. On the **Specify stack details** page, do the following:

   1. For **Stack name**, enter **custom\-sqlserver\-iam**\.

   1. Choose **Next**\.

1. On the **Configure stack options page**, choose **Next\.**

1. On the **Review custom\-sqlserver\-iam** page, do the following:

   1. For **Capabilities**, select the ****I acknowledge that AWS CloudFormation might create IAM resources with custom names**** check box\.

   1. Choose **Create stack**\.

   CloudFormation creates the IAM roles that RDS Custom for SQL Server requires\.

**To configure your VPC using CloudFormation**

This procedure assumes that you've already used CloudFormation to create your IAM roles\.

If you've already configured your VPC for a different RDS Custom engine, skip this step\.

1. Open the CloudFormation console at [https://console\.aws\.amazon\.com/cloudformation](https://console.aws.amazon.com/cloudformation/)\.

1. On the **Stacks** page, for **Create stack** choose **With new resources \(standard\)**\.

1. On the **Specify template** page, do the following:

   1. For **Template source**, choose **Upload a template file**\.

   1. For **Choose file**, go to and choose `custom-vpc.json`\.

   1. Choose **Next**\.

1. On the **Specify stack details** page, do the following:

   1. For **Stack name**, enter **custom\-vpc**\.

   1. For **Parameters**, choose the private subnets to use for RDS Custom DB instances\.

   1. Choose the private VPC ID to use for RDS Custom DB instances\.

   1. Enter the route table associated with the private subnets\.

   1. Choose **Next**\.

1. On the **Configure stack options page**, choose **Next**\.

1. On the **Review custom\-vpc** page, choose **Create stack**\.

   CloudFormation configures your VPC\.

### Creating your IAM role and instance profile manually<a name="custom-setup-sqlserver.iam"></a>

To use RDS Custom for SQL Server, create an IAM instance profile and IAM role described in the following process\.

**To create the RDS Custom for SQL Server IAM instance profile and IAM roles**

1. Create the IAM role named `AWSRDSCustomSQLServerInstanceRole` with a trust policy that lets Amazon EC2 assume this role\.

1. Add an access policy to `AWSRDSCustomSQLServerInstanceRole`\.

1. Create an IAM instance profile for RDS Custom for SQL Server named `AWSRDSCustomSQLServerInstanceProfile`\.

1. Add `AWSRDSCustomSQLServerInstanceRole` to the instance profile\.

#### Create the AWSRDSCustomSQLServerInstanceRole IAM role<a name="custom-setup-sqlserver.iam.create-role"></a>

The following example creates the `AWSRDSCustomSQLServerInstanceRole` role\. The trust policy lets Amazon EC2 assume the role\.

```
aws iam create-role \
    --role-name AWSRDSCustomSQLServerInstanceRole \
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

#### Add an access policy to AWSRDSCustomSQLServerInstanceRole<a name="custom-setup-sqlserver.iam.add-policy"></a>

When you embed an inline policy in a role, the inline policy is used as part of the role's access \(permissions\) policy\. You create the `AWSRDSCustomSQLServerIamRolePolicy` policy, which permits Amazon EC2 to get and receive messages and perform various actions\.

The following example creates the access policy named `AWSRDSCustomSQLServerIamRolePolicy`, and adds it to the `AWSRDSCustomSQLServerInstanceRole` role\. This example assumes that the `'$REGION'`, `$ACCOUNT_ID`, and `'$CUSTOMER_KMS_KEY_ID'` variables have been set\.

```
aws iam put-role-policy \
    --role-name AWSRDSCustomSQLServerInstanceRole \
    --policy-name AWSRDSCustomSQLServerIamRolePolicy \
    --policy-document '{
        "Version": "2012-10-17",
        "Statement": [
            {
                "Sid": "ssmAgent1",
                "Effect": "Allow",
                "Action": [
                    "ssm:GetDeployablePatchSnapshotForInstance",
                    "ssm:ListAssociations",
                    "ssm:PutInventory",
                    "ssm:PutConfigurePackageResult",
                    "ssm:UpdateInstanceInformation",
                    "ssm:GetManifest"
                ],
                "Resource": "*"
            },
            {
                "Sid": "ssmAgent2",
                "Effect": "Allow",
                "Action": [
                    "ssm:ListInstanceAssociations",
                    "ssm:PutComplianceItems",
                    "ssm:UpdateAssociationStatus",
                    "ssm:DescribeAssociation",
                    "ssm:UpdateInstanceAssociationStatus"
                ],
                "Resource": "arn:aws:ec2:'$REGION':'$ACCOUNT_ID':instance/*",
                "Condition": {
                    "StringLike": {
                        "aws:ResourceTag/AWSRDSCustom": "custom-sqlserver"
                    }
                }
            },
            {
                "Sid": "ssmAgent3",
                "Effect": "Allow",
                "Action": [
                    "ssm:UpdateAssociationStatus",
                    "ssm:DescribeAssociation",
                    "ssm:GetDocument",
                    "ssm:DescribeDocument"
                ],
                "Resource": "arn:aws:ssm:*:*:document/*"
            },
            {
                "Sid": "ssmAgent4",
                "Effect": "Allow",
                "Action": [
                    "ssmmessages:CreateControlChannel",
                    "ssmmessages:CreateDataChannel",
                    "ssmmessages:OpenControlChannel",
                    "ssmmessages:OpenDataChannel"
                ],
                "Resource": "*"
            },
            {
                "Sid": "ssmAgent5",
                "Effect": "Allow",
                "Action": [
                    "ec2messages:AcknowledgeMessage",
                    "ec2messages:DeleteMessage",
                    "ec2messages:FailMessage",
                    "ec2messages:GetEndpoint",
                    "ec2messages:GetMessages",
                    "ec2messages:SendReply"
                ],
                "Resource": "*"
            },
            {
                "Sid": "ssmAgent6",
                "Effect": "Allow",
                "Action": [
                    "ssm:GetParameters",
                    "ssm:GetParameter"
                ],
                "Resource": "arn:aws:ssm:*:*:parameter/*"
            },
            {
                "Sid": "ssmAgent7",
                "Effect": "Allow",
                "Action": [
                    "ssm:UpdateInstanceAssociationStatus",
                    "ssm:DescribeAssociation"
                ],
                "Resource": "arn:aws:ssm:*:*:association/*"
            },
            {
                "Sid": "eccSnapshot1",
                "Effect": "Allow",
                "Action": "ec2:CreateSnapshot",
                "Resource": [
                    "arn:aws:ec2:'$REGION':'$ACCOUNT_ID':volume/*"
                ],
                "Condition": {
                    "StringLike": {
                        "aws:ResourceTag/AWSRDSCustom": "custom-sqlserver"
                    }
                }
            },
            {
                "Sid": "eccSnapshot2",
                "Effect": "Allow",
                "Action": "ec2:CreateSnapshot",
                "Resource": [
                    "arn:aws:ec2:'$REGION'::snapshot/*"
                ],
                "Condition": {
                    "StringLike": {
                        "aws:RequestTag/AWSRDSCustom": "custom-sqlserver"
                    }
                }
            },
            {
                "Sid": "eccCreateTag",
                "Effect": "Allow",
                "Action": "ec2:CreateTags",
                "Resource": "*",
                "Condition": {
                    "StringLike": {
                        "aws:RequestTag/AWSRDSCustom": "custom-sqlserver",
                        "ec2:CreateAction": [
                            "CreateSnapshot"
                        ]
                    }
                }
            },
            {
                "Sid": "s3BucketAccess",
                "Effect": "Allow",
                "Action": [
                    "s3:putObject",
                    "s3:getObject",
                    "s3:getObjectVersion",
                    "s3:AbortMultipartUpload"
                ],
                "Resource": [
                    "arn:aws:s3:::do-not-delete-rds-custom-*/*"
                ]
            },
            {
                "Sid": "customerKMSEncryption",
                "Effect": "Allow",
                "Action": [
                    "kms:Decrypt",
                    "kms:GenerateDataKey*"
                ],
                "Resource": [
                    "arn:aws:kms:'$REGION':'$ACCOUNT_ID':key/'$CUSTOMER_KMS_KEY_ID'"
                ]
            },
            {
                "Sid": "readSecretsFromCP",
                "Effect": "Allow",
                "Action": [
                    "secretsmanager:GetSecretValue",
                    "secretsmanager:DescribeSecret"
                ],
                "Resource": [
                    "arn:aws:secretsmanager:'$REGION':'$ACCOUNT_ID':secret:do-not-delete-rds-custom-*"
                ],
                "Condition": {
                    "StringLike": {
                        "aws:ResourceTag/AWSRDSCustom": "custom-sqlserver"
                    }
                }
            },
            {
                "Sid": "publishCWMetrics",
                "Effect": "Allow",
                "Action": "cloudwatch:PutMetricData",
                "Resource": "*",
                "Condition": {
                    "StringEquals": {
                        "cloudwatch:namespace": "rdscustom/rds-custom-sqlserver-agent"
                    }
                }
            },
            {
                "Sid": "putEventsToEventBus",
                "Effect": "Allow",
                "Action": "events:PutEvents",
                "Resource": "arn:aws:events:'$REGION':'$ACCOUNT_ID':event-bus/default"
            },
            {
                "Sid": "cwlOperations1",
                "Effect": "Allow",
                "Action": [
                    "logs:PutRetentionPolicy",
                    "logs:PutLogEvents",
                    "logs:DescribeLogStreams",
                    "logs:CreateLogStream",
                    "logs:CreateLogGroup"
                ],
                "Resource": "arn:aws:logs:'$REGION':'$ACCOUNT_ID':log-group:rds-custom-instance-*"
            },
            {
                "Sid": "cwlOperations2",
                "Effect": "Allow",
                "Action": "logs:DescribeLogGroups",
                "Resource": "arn:aws:logs:'$REGION':'$ACCOUNT_ID':log-group:*"
            }
        ]
    }'
```

#### Create your RDS Custom for SQL Server instance profile<a name="custom-setup-sqlserver.iam.create-profile"></a>

Create your instance profile as follows, naming it `AWSRDSCustomSQLServerInstanceProfile`\.

```
aws iam create-instance-profile \
    --instance-profile-name AWSRDSCustomSQLServerInstanceProfile
```

#### Add AWSRDSCustomSQLServerInstanceRole to your RDS Custom for SQL Server instance profile<a name="custom-setup-sqlserver.iam.add-profile"></a>

Add the `AWSRDSCustomInstanceRoleForRdsCustomInstance` role to the `AWSRDSCustomSQLServerInstanceProfile` profile\.

```
aws iam add-role-to-instance-profile \
    --instance-profile-name AWSRDSCustomSQLServerInstanceProfile \
    --role-name AWSRDSCustomSQLServerInstanceRole
```

### Configuring your VPC manually<a name="custom-setup-sqlserver.vpc"></a>

Your RDS Custom DB instance is in a VPC, just like an Amazon EC2 instance or Amazon RDS instance\. You provide and configure your own VPC\. Thus, you have full control over your instance networking setup\.

RDS Custom sends communication from your DB instance to other AWS services\. To make sure that RDS Custom can communicate, it validates network connectivity to these services\.

If you have already configured a VPC for a different RDS Custom engine, you can reuse that VPC and skip this process\.

**Topics**
+ [Configure your VPC security group](#custom-setup-sqlserver.vpc.sg)
+ [Configure endpoints for dependent AWS services](#custom-setup-sqlserver.vpc.endpoints)
+ [Configure the instance metadata service](#custom-setup-sqlserver.vpc.imds)

#### Configure your VPC security group<a name="custom-setup-sqlserver.vpc.sg"></a>

A *security group* acts as a virtual firewall for a VPC instance, controlling both inbound and outbound traffic\. An RDS Custom DB instance has a default security group that protects the instance\. Make sure that your security group permits traffic between RDS Custom and other AWS services\.

**To configure your security group for RDS Custom**

1. Sign in to the AWS Management Console and open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc](https://console.aws.amazon.com/vpc)\. 

1. Allow RDS Custom to use the default security group, or create your own security group\. 

   For detailed instructions, see [Provide access to your DB instance in your VPC by creating a security group](CHAP_SettingUp.md#CHAP_SettingUp.SecurityGroup)\.

1. If you have a private VPC and use VPC endpoints, make sure that your security group permits both inbound and outbound connections on port 443\. 

   RDS Custom needs port 443 to communicate with dependent AWS services\. If incoming connections aren't allowed, the RDS Custom instance can't connect to the AWS Systems Manager and Amazon EC2 endpoints\. For more information, see [Create a Virtual Private Cloud endpoint](https://docs.aws.amazon.com/systems-manager/latest/userguide/setup-create-vpc.html)\.

For more information about security groups, see [Security groups for your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html) in *Amazon VPC Developer Guide*\.

#### Configure endpoints for dependent AWS services<a name="custom-setup-sqlserver.vpc.endpoints"></a>

Make sure that your VPC allows outbound traffic to the following AWS services:
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

1. Choose **Create endpoint**\.

The following table explains how to find the list of endpoints that your VPC needs for outbound communications\.


| Service | Endpoint format | Notes and links | 
| --- | --- | --- | 
|  AWS Systems Manager  |  Use the following endpoint formats: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/custom-setup-sqlserver.html)  |  For the list of endpoints in each Region, see [AWS Systems Manager endpoints and quotas](https://docs.aws.amazon.com/general/latest/gr/ssm.html) in the *Amazon Web Services General Reference*\.  | 
|  AWS Secrets Manager  |  Use the endpoint format `secretsmanager.region.amazonaws.com`\.  |  For the list of endpoints in each Region, see [AWS Secrets Manager endpoints and quotas](https://docs.aws.amazon.com/general/latest/gr/asm.html) in the *Amazon Web Services General Reference*\.  | 
|  Amazon CloudWatch  |  Use the following endpoint formats: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/custom-setup-sqlserver.html)  | For the list of endpoints in every Region, see: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/custom-setup-sqlserver.html) | 
|  Amazon EC2  |  Use the following endpoint formats: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/custom-setup-sqlserver.html)  |  For the list of endpoints in each Region, see [Amazon Elastic Compute Cloud endpoints and quotas](https://docs.aws.amazon.com/general/latest/gr/ec2-service.html) in the *Amazon Web Services General Reference*\.  | 
|  Amazon S3  |  Use the endpoint format `s3.region.amazonaws.com`\.  |  For the list of endpoints in each Region, see [Amazon Simple Storage Service endpoints and quotas](https://docs.aws.amazon.com/general/latest/gr/s3.html) in the *Amazon Web Services General Reference*\.  To learn more about gateway endpoints for Amazon S3, see [Endpoints for Amazon S3](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-endpoints-s3.html) in the *Amazon VPC Developer Guide*\.  To learn how to create an access point, see [Creating access points](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/access-points-create-ap.html) in the *Amazon VPC Developer Guide*\. To learn how to create a gateway endpoints for Amazon S3, see [Gateway VPC endpoints](https://docs.aws.amazon.com/vpc/latest/privatelink/vpce-gateway.html)\.  | 

#### Configure the instance metadata service<a name="custom-setup-sqlserver.vpc.imds"></a>

Make sure that your instance can do the following:
+ Access the instance metadata service using Instance Metadata Service Version 2 \(IMDSv2\)\.
+ Allow outbound communications through port 80 \(HTTP\) to the IMDS link IP address\.
+ Request instance metadata from `http://169.254.169.254`, the IMDSv2 link\.

For more information, see [Use IMDSv2](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/configuring-instance-metadata-service.html) in the *Amazon EC2 User Guide for Linux Instances*\.

## Grant required permissions to your IAM user<a name="custom-setup-sqlserver.iam-user"></a>

The IAM principal that creates the RDS Custom for SQL Server DB instance must have either of the following policies:
+ The `AdministratorAccess` policy
+ The `AmazonRDSFullAccess` policy with the following additional permissions:

  ```
  iam:SimulatePrincipalPolicy
  cloudtrail:CreateTrail
  cloudtrail:StartLogging
  s3:CreateBucket
  s3:PutBucketPolicy
  kms:CreateGrant
  ```

  For more information on the `kms:CreateGrant` permission, see [AWS KMS key management](Overview.Encryption.Keys.md)\.