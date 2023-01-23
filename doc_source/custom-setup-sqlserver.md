# Setting up your environment for Amazon RDS Custom for SQL Server<a name="custom-setup-sqlserver"></a>

Before you create and manage a DB instance for Amazon RDS Custom for SQL Server DB instance, make sure to perform the following tasks\.

**Contents**
+ [Prerequisites for setting up RDS Custom for SQL Server](#custom-setup-sqlserver.review)
+ [Download and install the AWS CLI](#custom-setup-sqlserver.cli)
+ [Grant required permissions to your IAM principal](#custom-setup-sqlserver.iam-user)
+ [Configure networking, instance profile, and encryption](#custom-setup-sqlserver.iam-vpc)
  + [Configuring with AWS CloudFormation](#custom-setup-sqlserver.cf)
    + [Resources created by CloudFormation](#custom-setup-sqlserver.cf.list)
    + [Downloading the template file](#custom-setup-sqlserver.cf.download)
    + [Configuring resources using CloudFormation](#custom-setup-sqlserver.cf.config)
  + [Configuring manually](#custom-setup-sqlserver.manual)
    + [Make sure that you have a symmetric encryption AWS KMS key](#custom-setup-sqlserver.cmk)
    + [Creating your IAM role and instance profile manually](#custom-setup-sqlserver.iam)
      + [Create the AWSRDSCustomSQLServerInstanceRole IAM role](#custom-setup-sqlserver.iam.create-role)
      + [Add an access policy to AWSRDSCustomSQLServerInstanceRole](#custom-setup-sqlserver.iam.add-policy)
      + [Create your RDS Custom for SQL Server instance profile](#custom-setup-sqlserver.iam.create-profile)
      + [Add AWSRDSCustomSQLServerInstanceRole to your RDS Custom for SQL Server instance profile](#custom-setup-sqlserver.iam.add-profile)
    + [Configuring your VPC manually](#custom-setup-sqlserver.vpc)
      + [Configure your VPC security group](#custom-setup-sqlserver.vpc.sg)
      + [Configure endpoints for dependent AWS services](#custom-setup-sqlserver.vpc.endpoints)
      + [Configure the instance metadata service](#custom-setup-sqlserver.vpc.imds)

## Prerequisites for setting up RDS Custom for SQL Server<a name="custom-setup-sqlserver.review"></a>

Before creating an RDS Custom for SQL Server DB instance, make sure that your environment meets the requirements described in this topic\. Not all of them require action from you; if any action is necessary, the corresponding section describes it\.

As part of this setup process, make sure to configure the specified AWS Identity and Access Management \(IAM\) users and roles\. These are either used to create an RDS Custom DB instance or passed as a parameter in a creation request\. If the account that you're using is part of an AWS organization, it might have service control policies \(SCPs\) restricting account level permissions\. Make sure that the SCPs don't restrict the permissions on users and roles that you create using the following procedures\. For more information about SCPs, see [Service control policies \(SCPs\)](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_policies_scps.html) in the *AWS Organizations User Guide*\. Use the [describe\-organization](https://docs.aws.amazon.com/cli/latest/reference/organizations/describe-organization.html) AWS CLI command to check whether your account is part of an AWS organization\.

For each task, you can find descriptions following for the requirements and limitations specific to that task\. For example, when you create your RDS Custom for SQL Server DB instance, use one of the SQL Server instances listed in [DB instance class support for RDS Custom for SQL Server](custom-reqs-limits-MS.md#custom-reqs-limits.instancesMS)\.

For general requirements that apply to RDS Custom for SQL Server, see [General requirements for RDS Custom for SQL Server](custom-reqs-limits-MS.md#custom-reqs-limits.reqsMS)\.

## Download and install the AWS CLI<a name="custom-setup-sqlserver.cli"></a>

You can use the AWS Command Line Interface \(AWS CLI\) to use RDS Custom features\. You can use either version 1 or version 2 of the AWS CLI\. For information about downloading and installing the AWS CLI, see [Installing or updating the latest version of the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)\.

If you plan to access RDS Custom only from the AWS Management Console, skip this step\.

If you have already downloaded the AWS CLI for Amazon RDS or RDS Custom for Oracle, skip this step\.

## Grant required permissions to your IAM principal<a name="custom-setup-sqlserver.iam-user"></a>

You use an IAM role or IAM user \(referred to as the *IAM principal*\) for creating an RDS Custom for SQL Server DB instance using the console or CLI\. This IAM principal must have either of the following policies for successful DB instance creation:
+ The `AdministratorAccess` policy
+ The `AmazonRDSFullAccess` policy with the following additional permissions:

  ```
  iam:SimulatePrincipalPolicy
  cloudtrail:CreateTrail
  cloudtrail:StartLogging
  s3:CreateBucket
  s3:PutBucketPolicy
  s3:PutBucketObjectLockConfiguration
  s3:PutBucketVersioning 
  kms:CreateGrant
  kms:DescribeKey
  ```

  For more information about the `kms:CreateGrant` permission, see [AWS KMS key management](Overview.Encryption.Keys.md)\.

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
        },
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

Also, the IAM principal requires the `iam:PassRole` permission on the IAM role\. That must be attached to the instance profile passed in the `custom-iam-instance-profile` parameter in the request to create the RDS Custom DB instance\. The instance profile and its attached role are created later in [Configure networking, instance profile, and encryption](#custom-setup-sqlserver.iam-vpc)\.

Make sure that the previously listed permissions aren't restricted by service control policies \(SCPs\), permission boundaries, or session policies associated with the IAM principal\.

## Configure networking, instance profile, and encryption<a name="custom-setup-sqlserver.iam-vpc"></a>

You can configure your IAM instance profile role, virtual private cloud \(VPC\), and AWS KMS symmetric encryption key by using either of the following processes:
+ [Configuring with AWS CloudFormation](#custom-setup-sqlserver.cf) \(recommended\)
+ [Configuring manually](#custom-setup-sqlserver.manual)

If your account is part of an AWS organization, make sure that the permissions required by the instance profile role aren’t restricted by service control policies \(SCPs\)\.

The following networking configurations are designed to work best with DB instances that aren't publicly accessible\. That is, you can’t connect directly to the DB instance from outside the VPC\. 

### Configuring with AWS CloudFormation<a name="custom-setup-sqlserver.cf"></a>

To simplify setup, you can use an AWS CloudFormation template file to create a CloudFormation stack\. To learn how to create stacks, see [Creating a stack on the AWS CloudFormation console](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-console-create-stack.html) in the *AWS CloudFormation User Guide*\.

For a tutorial on how to lauch Amazon RDS Custom for SQL Server using an AWS CloudFormation template, see [Get started with Amazon RDS Custom for SQL Server using an AWS CloudFormation template](https://aws.amazon.com/blogs/database/get-started-with-amazon-rds-custom-for-sql-server-using-an-aws-cloudformation-template-network-setup/) in the *AWS Database Blog *\.

**Topics**
+ [Resources created by CloudFormation](#custom-setup-sqlserver.cf.list)
+ [Downloading the template file](#custom-setup-sqlserver.cf.download)
+ [Configuring resources using CloudFormation](#custom-setup-sqlserver.cf.config)

#### Resources created by CloudFormation<a name="custom-setup-sqlserver.cf.list"></a>

Successfully creating the CloudFormation stack creates the following resources in your AWS account:
+ Symmetric encryption KMS key for encryption of data managed by RDS Custom\.
+ Instance profile and associated IAM role for attaching to RDS Custom instances\.
+ VPC with the CIDR range specified as the CloudFormation parameter\. The default value is `10.0.0.0/16`\.
+ Two private subnets with the CIDR range specified in the parameters, and two different Availability Zones in the AWS Region\. The default values for the subnet CIDRs are `10.0.128.0/20` and `10.0.144.0/20`\.
+ DHCP option set for the VPC with domain name resolution to an Amazon Domain Name System \(DNS\) server\.
+ Route table to associate with two private subnets and no access to the internet\.
+ Network access control list \(ACL\) to associate with two private subnets and access restricted to HTTPS\.
+ VPC security group to be associated with the RDS Custom instance\. Access is restricted for outbound HTTPS to AWS service endpoints that are required by RDS Custom\.
+ VPC security group to be associated with VPC endpoints that are created for AWS service endpoints that are required by RDS Custom\.
+ DB subnet group in which RDS Custom instances are created\.
+ VPC endpoints for each of the AWS service endpoints that are required by RDS Custom\.

Use the following procedures to create the CloudFormation stack for RDS Custom for SQL Server\.

#### Downloading the template file<a name="custom-setup-sqlserver.cf.download"></a>

**To download the template file**

1. Open the context \(right\-click\) menu for the link [ custom\-sqlserver\-onboard\.zip](samples/custom-sqlserver-onboard.zip) and choose **Save Link As**\.

1. Save the file to your computer\.

#### Configuring resources using CloudFormation<a name="custom-setup-sqlserver.cf.config"></a>

**To configure resources using CloudFormation**

1. Open the CloudFormation console at [https://console\.aws\.amazon\.com/cloudformation](https://console.aws.amazon.com/cloudformation/)\.

1. To start the Create Stack wizard, choose **Create Stack**\.

   The **Create stack** page appears\.

1. For **Prerequisite \- Prepare template**, choose **Template is ready**\.

1. For **Specify template**, do the following:

   1. For **Template source**, choose **Upload a template file**\.

   1. For **Choose file**, navigate to and then choose `custom-sqlserver-onboard.json`\.

1. Choose **Next**\.

   The **Specify stack details** page appears\.

1. For **Stack name**, enter **rds\-custom\-sqlserver**\.

1. For **Parameters**, do the following:

   1. To keep the default options, choose **Next**\.

   1. To change options, choose the appropriate CIDR block range for the VPC and two of its subnets, and then choose **Next**\.

      Read the description of each parameter carefully before changing parameters\.

1. On the **Configure stack options page**, choose **Next\.**

1. On the **Review rds\-custom\-sqlserver** page, do the following:

   1. For **Capabilities**, select the ****I acknowledge that AWS CloudFormation might create IAM resources with custom names**** check box\.

   1. Choose **Create stack**\.

CloudFormation creates the resources that RDS Custom for SQL Server requires\. If the stack creation fails, read through the **Events** tab to see which resource creation failed and its status reason\.

The **Outputs** tab for this CloudFormation stack in the console should have information about all resources to be passed as parameters for creating an RDS Custom for SQL Server DB instance\. Make sure to use the VPC security group and DB subnet group created by CloudFormation for RDS Custom DB instances\. By default, RDS tries to attach the default VPC security group, which might not have the access that you need\.

**Note**  
When you delete a CloudFormation stack, all of the resources created by the stack are deleted except the KMS key\. The KMS key goes into a `pending-deletion` state and is deleted after 30 days\. To keep the KMS key, perform a [CancelKeyDeletion](https://docs.aws.amazon.com/s.kms/latest/APIReference/API_CancelKeyDeletion.html) operation during the 30\-day grace period\.

If you used CloudFormation to create resources, you can skip [Configuring manually](#custom-setup-sqlserver.manual)\.

### Configuring manually<a name="custom-setup-sqlserver.manual"></a>

If you choose to configure resources manually, perform the following tasks\.

**Topics**
+ [Make sure that you have a symmetric encryption AWS KMS key](#custom-setup-sqlserver.cmk)
+ [Creating your IAM role and instance profile manually](#custom-setup-sqlserver.iam)
+ [Configuring your VPC manually](#custom-setup-sqlserver.vpc)

#### Make sure that you have a symmetric encryption AWS KMS key<a name="custom-setup-sqlserver.cmk"></a>

A symmetric encryption AWS KMS key is required for RDS Custom\. When you create an RDS Custom for SQL Server DB instance, make sure to supply the KMS key identifier\. For more information, see [Creating and connecting to a DB instance for Amazon RDS Custom for SQL Server](custom-creating-sqlserver.md)\.

You have the following options:
+ If you have an existing KMS key in your AWS account, you can use it with RDS Custom\. No further action is necessary\.
+ If you already created a symmetric encryption KMS key for a different RDS Custom engine, you can reuse the same KMS key\. No further action is necessary\.
+ If you don't have an existing symmetric encryption KMS key in your account, create a KMS key by following the instructions in [Creating keys](https://docs.aws.amazon.com/kms/latest/developerguide/create-keys.html#create-symmetric-cmk) in the *AWS Key Management Service Developer Guide*\.
+ If you're creating a CEV or RDS Custom DB instance, and your KMS key is in a different AWS account, make sure to use the AWS CLI\. You can't use the AWS console with cross\-account KMS keys\.

RDS Custom doesn't support AWS\-managed KMS keys\.

Make sure that the symmetric encryption key that you use gives the AWS Identity and Access Management \(IAM\) role in your IAM instance profile access to the `kms:Decrypt` and `kms:GenerateDataKey` operations\. If you have a new symmetric encryption key in your account, no changes are required\. Otherwise, make sure that your symmetric encryption key's policy can give access to these operations\.

#### Creating your IAM role and instance profile manually<a name="custom-setup-sqlserver.iam"></a>

To use RDS Custom for SQL Server, create an IAM instance profile and IAM role as described following\.

**To create the IAM instance profile and IAM roles for RDS Custom for SQL Server**

1. Create the IAM role named `AWSRDSCustomSQLServerInstanceRole` with a trust policy that lets Amazon EC2 assume this role\.

1. Add an access policy to `AWSRDSCustomSQLServerInstanceRole`\.

1. Create an IAM instance profile for RDS Custom for SQL Server that is named `AWSRDSCustomSQLServerInstanceProfile`\.

1. Add `AWSRDSCustomSQLServerInstanceRole` to the instance profile\.

##### Create the AWSRDSCustomSQLServerInstanceRole IAM role<a name="custom-setup-sqlserver.iam.create-role"></a>

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

##### Add an access policy to AWSRDSCustomSQLServerInstanceRole<a name="custom-setup-sqlserver.iam.add-policy"></a>

When you embed an inline policy in a role, the inline policy is used as part of the role's access \(permissions\) policy\. You create the `AWSRDSCustomSQLServerIamRolePolicy` policy, which lets Amazon EC2 get and receive messages and perform various actions\.

Make sure that the permissions in the access policy aren't restricted by SCPs or permission boundaries associated with the instance profile role\.

The following example creates the access policy named `AWSRDSCustomSQLServerIamRolePolicy`, and adds it to the `AWSRDSCustomSQLServerInstanceRole` role\. This example assumes that the `'$REGION'`, `$ACCOUNT_ID`, and `'$CUSTOMER_KMS_KEY_ID'` variables have been set\. `'$CUSTOMER_KMS_KEY_ID'` is the ID, not the Amazon Resource Name \(ARN\), of the KMS key that you defined in [Make sure that you have a symmetric encryption AWS KMS key](#custom-setup-sqlserver.cmk)\.

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

##### Create your RDS Custom for SQL Server instance profile<a name="custom-setup-sqlserver.iam.create-profile"></a>

Create your instance profile as follows, naming it `AWSRDSCustomSQLServerInstanceProfile`\.

```
aws iam create-instance-profile \
    --instance-profile-name AWSRDSCustomSQLServerInstanceProfile
```

##### Add AWSRDSCustomSQLServerInstanceRole to your RDS Custom for SQL Server instance profile<a name="custom-setup-sqlserver.iam.add-profile"></a>

Add the `AWSRDSCustomInstanceRoleForRdsCustomInstance` role to the `AWSRDSCustomSQLServerInstanceProfile` profile\.

```
aws iam add-role-to-instance-profile \
    --instance-profile-name AWSRDSCustomSQLServerInstanceProfile \
    --role-name AWSRDSCustomSQLServerInstanceRole
```

#### Configuring your VPC manually<a name="custom-setup-sqlserver.vpc"></a>

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
+ [Configure your VPC security group](#custom-setup-sqlserver.vpc.sg)
+ [Configure endpoints for dependent AWS services](#custom-setup-sqlserver.vpc.endpoints)
+ [Configure the instance metadata service](#custom-setup-sqlserver.vpc.imds)

##### Configure your VPC security group<a name="custom-setup-sqlserver.vpc.sg"></a>

A *security group* acts as a virtual firewall for a VPC instance, controlling both inbound and outbound traffic\. An RDS Custom DB instance has a default security group that protects the instance\. Make sure that your security group permits traffic between RDS Custom and other AWS services\.

**To configure your security group for RDS Custom**

1. Sign in to the AWS Management Console and open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc](https://console.aws.amazon.com/vpc)\. 

1. Allow RDS Custom to use the default security group, or create your own security group\.

   For detailed instructions, see [Provide access to your DB instance in your VPC by creating a security group](CHAP_SettingUp.md#CHAP_SettingUp.SecurityGroup)\.

1. Make sure that your security group permits outbound connections on port 443\. RDS Custom needs this port to communicate with dependent AWS services\.

1. If you have a private VPC and use VPC endpoints, make sure that the security group associated with the DB instance allows outbound connections on port 443 to VPC endpoints\. Also make sure that the security group associated with the VPC endpoint allows inbound connections on port 443 from the DB instance\.

   If incoming connections aren't allowed, the RDS Custom instance can't connect to the AWS Systems Manager and Amazon EC2 endpoints\. For more information, see [Create a Virtual Private Cloud endpoint](https://docs.aws.amazon.com/systems-manager/latest/userguide/setup-create-vpc.html) in the *AWS Systems Manager User Guide*\.

For more information about security groups, see [Security groups for your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html) in the *Amazon VPC Developer Guide*\.

##### Configure endpoints for dependent AWS services<a name="custom-setup-sqlserver.vpc.endpoints"></a>

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
|  AWS Systems Manager  |  Use the following endpoint formats: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/custom-setup-sqlserver.html)  |  For the list of endpoints in each Region, see [AWS Systems Manager endpoints and quotas](https://docs.aws.amazon.com/general/latest/gr/ssm.html) in the *Amazon Web Services General Reference*\.  | 
|  AWS Secrets Manager  |  Use the endpoint format `secretsmanager.region.amazonaws.com`\.  |  For the list of endpoints in each Region, see [AWS Secrets Manager endpoints and quotas](https://docs.aws.amazon.com/general/latest/gr/asm.html) in the *Amazon Web Services General Reference*\.  | 
|  Amazon CloudWatch  |  Use the following endpoint formats: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/custom-setup-sqlserver.html)  | For the list of endpoints in every Region, see: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/custom-setup-sqlserver.html) | 
|  Amazon EC2  |  Use the following endpoint formats: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/custom-setup-sqlserver.html)  |  For the list of endpoints in each Region, see [Amazon Elastic Compute Cloud endpoints and quotas](https://docs.aws.amazon.com/general/latest/gr/ec2-service.html) in the *Amazon Web Services General Reference*\.  | 
|  Amazon S3  |  Use the endpoint format `s3.region.amazonaws.com`\.  |  For the list of endpoints in each Region, see [Amazon Simple Storage Service endpoints and quotas](https://docs.aws.amazon.com/general/latest/gr/s3.html) in the *Amazon Web Services General Reference*\.  To learn more about gateway endpoints for Amazon S3, see [Endpoints for Amazon S3](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-endpoints-s3.html) in the *Amazon VPC Developer Guide*\.  To learn how to create an access point, see [Creating access points](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/access-points-create-ap.html) in the *Amazon VPC Developer Guide*\. To learn how to create a gateway endpoints for Amazon S3, see [Gateway VPC endpoints](https://docs.aws.amazon.com/vpc/latest/privatelink/vpce-gateway.html)\.  | 

##### Configure the instance metadata service<a name="custom-setup-sqlserver.vpc.imds"></a>

Make sure that your instance can do the following:
+ Access the instance metadata service using Instance Metadata Service Version 2 \(IMDSv2\)\.
+ Allow outbound communications through port 80 \(HTTP\) to the IMDS link IP address\.
+ Request instance metadata from `http://169.254.169.254`, the IMDSv2 link\.

For more information, see [Use IMDSv2](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/configuring-instance-metadata-service.html) in the *Amazon EC2 User Guide for Linux Instances*\.