# AWS managed policies for Amazon RDS<a name="rds-security-iam-awsmanpol"></a>

To add permissions to permission sets and roles, it's easier to use AWS managed policies than to write policies yourself\. It takes time and expertise to [create IAM customer managed policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_create-console.html) that provide your team with only the permissions they need\. To get started quickly, you can use our AWS managed policies\. These policies cover common use cases and are available in your AWS account\. For more information about AWS managed policies, see [AWS managed policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_managed-vs-inline.html#aws-managed-policies) in the *IAM User Guide*\.

AWS services maintain and update AWS managed policies\. You can't change the permissions in AWS managed policies\. Services occasionally add additional permissions to an AWS managed policy to support new features\. This type of update affects all identities \(permission sets and roles\) where the policy is attached\. Services are most likely to update an AWS managed policy when a new feature is launched or when new operations become available\. Services don't remove permissions from an AWS managed policy, so policy updates don't break your existing permissions\.

Additionally, AWS supports managed policies for job functions that span multiple services\. For example, the `ReadOnlyAccess` AWS managed policy provides read\-only access to all AWS services and resources\. When a service launches a new feature, AWS adds read\-only permissions for new operations and resources\. For a list and descriptions of job function policies, see [AWS managed policies for job functions](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_job-functions.html) in the *IAM User Guide*\.

**Topics**
+ [AWS managed policy: AmazonRDSReadOnlyAccess](#rds-security-iam-awsmanpol-AmazonRDSReadOnlyAccess)
+ [AWS managed policy: AmazonRDSFullAccess](#rds-security-iam-awsmanpol-AmazonRDSFullAccess)
+ [AWS managed policy: AmazonRDSDataFullAccess](#rds-security-iam-awsmanpol-AmazonRDSDataFullAccess)
+ [AWS managed policy: AmazonRDSEnhancedMonitoringRole](#rds-security-iam-awsmanpol-AmazonRDSEnhancedMonitoringRole)
+ [AWS managed policy: AmazonRDSPerformanceInsightsReadOnly](#rds-security-iam-awsmanpol-AmazonRDSPerformanceInsightsReadOnly)
+ [AWS managed policy: AmazonRDSDirectoryServiceAccess](#rds-security-iam-awsmanpol-AmazonRDSDirectoryServiceAccess)
+ [AWS managed policy: AmazonRDSServiceRolePolicy](#rds-security-iam-awsmanpol-AmazonRDSServiceRolePolicy)
+ [AWS managed policy: AmazonRDSCustomServiceRolePolicy](#rds-security-iam-awsmanpol-AmazonRDSCustomServiceRolePolicy)

## AWS managed policy: AmazonRDSReadOnlyAccess<a name="rds-security-iam-awsmanpol-AmazonRDSReadOnlyAccess"></a>

This policy allows read\-only access to Amazon RDS through the AWS Management Console\.

**Permissions details**

This policy includes the following permissions:
+ `rds` – Allows principals to describe Amazon RDS resources and list the tags for Amazon RDS resources\.
+ `cloudwatch` – Allows principals to get Amazon CloudWatch metric statistics\.
+ `ec2` – Allows principals to describe Availability Zones and networking resources\.
+ `logs` – Allows principals to describe CloudWatch Logs log streams of log groups, and get CloudWatch Logs log events\.
+ `devops-guru` – Allows principals to describe resources that have Amazon DevOps Guru coverage, which is specified either by CloudFormation stack names or resource tags\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Action": [
                "rds:Describe*",
                "rds:ListTagsForResource",
                "ec2:DescribeAccountAttributes",
                "ec2:DescribeAvailabilityZones",
                "ec2:DescribeInternetGateways",
                "ec2:DescribeSecurityGroups",
                "ec2:DescribeSubnets",
                "ec2:DescribeVpcAttribute",
                "ec2:DescribeVpcs"
            ],
            "Effect": "Allow",
            "Resource": "*"
        },
        {
            "Action": [
                "devops-guru:GetResourceCollection"
            ],
            "Effect": "Allow",
            "Resource": "*"
        },
        {
            "Action": [
                "cloudwatch:GetMetricStatistics",
                "logs:DescribeLogStreams",
                "logs:GetLogEvents"
            ],
            "Effect": "Allow",
            "Resource": "*"
        }
    ]
}
```

## AWS managed policy: AmazonRDSFullAccess<a name="rds-security-iam-awsmanpol-AmazonRDSFullAccess"></a>

This policy provides full access to Amazon RDS through the AWS Management Console\.

**Permissions details**

This policy includes the following permissions:
+ `rds` – Allows principals full access to Amazon RDS\.
+ `application-autoscaling` – Allows principals describe and manage Application Auto Scaling scaling targets and policies\.
+ `cloudwatch` – Allows principals get CloudWatch metric statics and manage CloudWatch alarms\.
+ `ec2` – Allows principals to describe Availability Zones and networking resources\.
+ `logs` – Allows principals to describe CloudWatch Logs log streams of log groups, and get CloudWatch Logs log events\.
+ `outposts` – Allows principals to get AWS Outposts instance types\.
+ `pi` – Allows principals to get Performance Insights metrics\.
+ `sns` – Allows principals to Amazon Simple Notification Service \(Amazon SNS\) subscriptions and topics, and to publish Amazon SNS messages\.
+ `devops-guru` – Allows principals to describe resources that have Amazon DevOps Guru coverage, which is specified either by CloudFormation stack names or resource tags\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Action": [
                "rds:*",
                "application-autoscaling:DeleteScalingPolicy",
                "application-autoscaling:DeregisterScalableTarget",
                "application-autoscaling:DescribeScalableTargets",
                "application-autoscaling:DescribeScalingActivities",
                "application-autoscaling:DescribeScalingPolicies",
                "application-autoscaling:PutScalingPolicy",
                "application-autoscaling:RegisterScalableTarget",
                "cloudwatch:DescribeAlarms",
                "cloudwatch:GetMetricStatistics",
                "cloudwatch:PutMetricAlarm",
                "cloudwatch:DeleteAlarms",
                "ec2:DescribeAccountAttributes",
                "ec2:DescribeAvailabilityZones",
                "ec2:DescribeCoipPools",
                "ec2:DescribeInternetGateways",
                "ec2:DescribeLocalGatewayRouteTablePermissions",
                "ec2:DescribeLocalGatewayRouteTables",
                "ec2:DescribeLocalGatewayRouteTableVpcAssociations",
                "ec2:DescribeLocalGateways",
                "ec2:DescribeSecurityGroups",
                "ec2:DescribeSubnets",
                "ec2:DescribeVpcAttribute",
                "ec2:DescribeVpcs",
                "ec2:GetCoipPoolUsage",
                "sns:ListSubscriptions",
                "sns:ListTopics",
                "sns:Publish",
                "logs:DescribeLogStreams",
                "logs:GetLogEvents",
                "outposts:GetOutpostInstanceTypes"
            ],
            "Effect": "Allow",
            "Resource": "*"
        },
        {
            "Action": "pi:*",
            "Effect": "Allow",
            "Resource": "arn:aws:pi:*:*:metrics/rds/*"
        },
        {
            "Action": [
                "devops-guru:GetResourceCollection"
            ],
            "Effect": "Allow",
            "Resource": "*"
        },
        {
            "Action": "iam:CreateServiceLinkedRole",
            "Effect": "Allow",
            "Resource": "*",
            "Condition": {
                "StringLike": {
                    "iam:AWSServiceName": [
                        "rds.amazonaws.com",
                        "rds.application-autoscaling.amazonaws.com"
                    ]
                }
            }
        }
    ]
}
```

## AWS managed policy: AmazonRDSDataFullAccess<a name="rds-security-iam-awsmanpol-AmazonRDSDataFullAccess"></a>

This policy allows full access to use the Data API and the query editor on Aurora Serverless clusters in a specific AWS account\. This policy allows the AWS account to get the value of a secret from AWS Secrets Manager\. 

You can attach the `AmazonRDSDataFullAccess` policy to your IAM identities\.

**Permissions details**

This policy includes the following permissions:
+ `dbqms` – Allows principals to access, create, delete, describe, and update queries\. The Database Query Metadata Service \(`dbqms`\) is an internal\-only service\. It provides your recent and saved queries for the query editor on the AWS Management Console for multiple AWS services, including Amazon RDS\.
+ `rds-data` – Allows principals to run SQL statements on Aurora Serverless databases\.
+ `secretsmanager` – Allows principals to get the value of a secret from AWS Secrets Manager\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "SecretsManagerDbCredentialsAccess",
            "Effect": "Allow",
            "Action": [
                "secretsmanager:GetSecretValue",
                "secretsmanager:PutResourcePolicy",
                "secretsmanager:PutSecretValue",
                "secretsmanager:DeleteSecret",
                "secretsmanager:DescribeSecret",
                "secretsmanager:TagResource"
            ],
            "Resource": "arn:aws:secretsmanager:*:*:secret:rds-db-credentials/*"
        },
        {
            "Sid": "RDSDataServiceAccess",
            "Effect": "Allow",
            "Action": [
                "dbqms:CreateFavoriteQuery",
                "dbqms:DescribeFavoriteQueries",
                "dbqms:UpdateFavoriteQuery",
                "dbqms:DeleteFavoriteQueries",
                "dbqms:GetQueryString",
                "dbqms:CreateQueryHistory",
                "dbqms:DescribeQueryHistory",
                "dbqms:UpdateQueryHistory",
                "dbqms:DeleteQueryHistory",
                "rds-data:ExecuteSql",
                "rds-data:ExecuteStatement",
                "rds-data:BatchExecuteStatement",
                "rds-data:BeginTransaction",
                "rds-data:CommitTransaction",
                "rds-data:RollbackTransaction",
                "secretsmanager:CreateSecret",
                "secretsmanager:ListSecrets",
                "secretsmanager:GetRandomPassword",
                "tag:GetResources"
            ],
            "Resource": "*"
        }
    ]
}
```

## AWS managed policy: AmazonRDSEnhancedMonitoringRole<a name="rds-security-iam-awsmanpol-AmazonRDSEnhancedMonitoringRole"></a>

This policy provides access to Amazon CloudWatch Logs for Amazon RDS Enhanced Monitoring\.

**Permissions details**

This policy includes the following permissions:
+ `logs` – Allows principals to create CloudWatch Logs log groups and retention policies, and to create and describe CloudWatch Logs log streams of log groups\. It also allows principals to put and get CloudWatch Logs log events\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "EnableCreationAndManagementOfRDSCloudwatchLogGroups",
            "Effect": "Allow",
            "Action": [
                "logs:CreateLogGroup",
                "logs:PutRetentionPolicy"
            ],
            "Resource": [
                "arn:aws:logs:*:*:log-group:RDS*"
            ]
        },
        {
            "Sid": "EnableCreationAndManagementOfRDSCloudwatchLogStreams",
            "Effect": "Allow",
            "Action": [
                "logs:CreateLogStream",
                "logs:PutLogEvents",
                "logs:DescribeLogStreams",
                "logs:GetLogEvents"
            ],
            "Resource": [
                "arn:aws:logs:*:*:log-group:RDS*:log-stream:*"
            ]
        }
    ]
}
```

## AWS managed policy: AmazonRDSPerformanceInsightsReadOnly<a name="rds-security-iam-awsmanpol-AmazonRDSPerformanceInsightsReadOnly"></a>

This policy provides read\-only access to Amazon RDS Performance Insights for Amazon RDS DB instances and Amazon Aurora DB clusters\.

**Permissions details**

This policy includes the following permissions:
+ `rds` – Allows principals to describe Amazon RDS DB instances and Amazon Aurora DB clusters\.
+ `pi` – Allows principals make calls to the Amazon RDS Performance Insights API and access Performance Insights metrics\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "rds:DescribeDBInstances",
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": "rds:DescribeDBClusters",
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": "pi:DescribeDimensionKeys",
            "Resource": "arn:aws:pi:*:*:metrics/rds/*"
        },
        {
            "Effect": "Allow",
            "Action": "pi:GetDimensionKeyDetails",
            "Resource": "arn:aws:pi:*:*:metrics/rds/*"
        },
        {
            "Effect": "Allow",
            "Action": "pi:GetResourceMetadata",
            "Resource": "arn:aws:pi:*:*:metrics/rds/*"
        },
        {
            "Effect": "Allow",
            "Action": "pi:GetResourceMetrics",
            "Resource": "arn:aws:pi:*:*:metrics/rds/*"
        },
        {
            "Effect": "Allow",
            "Action": "pi:ListAvailableResourceDimensions",
            "Resource": "arn:aws:pi:*:*:metrics/rds/*"
        },
        {
            "Effect": "Allow",
            "Action": "pi:ListAvailableResourceMetrics",
            "Resource": "arn:aws:pi:*:*:metrics/rds/*"
        }
    ]
}
```

## AWS managed policy: AmazonRDSDirectoryServiceAccess<a name="rds-security-iam-awsmanpol-AmazonRDSDirectoryServiceAccess"></a>

This policy allows Amazon RDS to make calls to the AWS Directory Service\.

**Permissions details**

This policy includes the following permissions:
+ `ds` – Allows principals to describe AWS Directory Service directories and control authorization to AWS Directory Service directories\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Action": [
                "ds:DescribeDirectories",
                "ds:AuthorizeApplication",
                "ds:UnauthorizeApplication",
                "ds:GetAuthorizedApplicationDetails"
            ],
            "Effect": "Allow",
            "Resource": "*"
        }
    ]
}
```

## AWS managed policy: AmazonRDSServiceRolePolicy<a name="rds-security-iam-awsmanpol-AmazonRDSServiceRolePolicy"></a>

You can't attach the `AmazonRDSServiceRolePolicy` policy to your IAM entities\. This policy is attached to a service\-linked role that allows Amazon RDS to perform actions on your behalf\. For more information, see [Service\-linked role permissions for Amazon RDS](UsingWithRDS.IAM.ServiceLinkedRoles.md#service-linked-role-permissions)\.

## AWS managed policy: AmazonRDSCustomServiceRolePolicy<a name="rds-security-iam-awsmanpol-AmazonRDSCustomServiceRolePolicy"></a>

You can't attach the `AmazonRDSCustomServiceRolePolicy` policy to your IAM entities\. This policy is attached to a service\-linked role that allows Amazon RDS to perform actions on your behalf\. For more information, see [Service\-linked role permissions for Amazon RDS Custom](UsingWithRDS.IAM.ServiceLinkedRoles.md#slr-permissions-custom)\.