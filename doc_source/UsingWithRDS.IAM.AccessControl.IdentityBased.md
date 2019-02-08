# Using Identity\-Based Policies \(IAM Policies\) for Amazon RDS<a name="UsingWithRDS.IAM.AccessControl.IdentityBased"></a>

This topic provides examples of identity\-based policies in which an account administrator can attach permissions policies to IAM identities \(that is, users, groups, and roles\)\. 

**Important**  
We recommend that you first review the introductory topics that explain the basic concepts and options available for you to manage access to your Amazon RDS resources\. For more information, see [Overview of Managing Access Permissions to Your Amazon RDS Resources](UsingWithRDS.IAM.AccessControl.Overview.md)\.

The sections in this topic cover the following:
+ [Permissions Required to Use the Amazon RDS Console](#UsingWithRDS.IAM.RequiredPermissions.Console)
+ [AWS Managed \(Predefined\) Policies for Amazon RDS](#UsingWithRDS.IAM.AccessControl.ManagedPolicies)
+ [Customer Managed Policy Examples](#IAMPolicyExamples-RDS)

The following is an example of an IAM policy:

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowCreateDBInstanceOnly",
            "Effect": "Allow",
            "Action": [
                "rds:CreateDBInstance"
            ],
            "Resource": [
                "arn:aws:rds:*:123456789012:db:test*",
                "arn:aws:rds:*:123456789012:og:default*",
                "arn:aws:rds:*:123456789012:pg:default*",
                "arn:aws:rds:*:123456789012:subgrp:default"
            ],
            "Condition": {
                "StringEquals": {
                    "rds:DatabaseEngine": "mysql",
                    "rds:DatabaseClass": "db.t2.micro"
                }
            }
        }
    ]
}
```

The policy includes a single statement that specifies the following permissions for the IAM user:
+ The policy allows the IAM user to create a DB instance using the [CreateDBInstance](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html) API action \(this also applies to the [create\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html) AWS CLI command and the AWS Management Console\)\.
+ The `Resource` element specifies that the user can perform actions on or with resources\. You specify resources using an Amazon Resources Name \(ARN\)\. This ARN includes the name of the service that the resource belongs to \(`rds`\), the AWS Region \(`*` indicates any region in this example\), the user account number \(`123456789012` is the user ID in this example\), and the type of resource\. For more information about creating ARNs, see [Working with Amazon Resource Names \(ARNs\) in Amazon RDS](USER_Tagging.ARN.md)\.

  The `Resource` element in the example specifies the following policy constraints on resources for the user:
  + The DB instance identifier for the new DB instance must begin with `test` \(for example, `testCustomerData1`, `test-region2-data`\)\.
  + The option group for the new DB instance must begin with `default`\.
  + The DB parameter group for the new DB instance must begin with `default`\.
  + The subnet group for the new DB instance must be the `default` subnet group\.
+ The `Condition` element specifies that the DB engine must be MySQL and the DB instance class must be `db.t2.micro`\. The `Condition` element specifies the conditions when a policy should take effect\. You can add additional permissions or restrictions by using the `Condition` element\. For more information about specifying conditions, see [Using IAM Policy Conditions for Fine\-Grained Access Control](UsingWithRDS.IAM.Conditions.md)\.

The policy doesn't specify the `Principal` element because in an identity\-based policy you don't specify the principal who gets the permission\. When you attach policy to a user, the user is the implicit principal\. When you attach a permission policy to an IAM role, the principal identified in the role's trust policy gets the permissions\.

 For a table showing all of the Amazon RDS API actions and the resources that they apply to, see [Amazon RDS API Permissions: Actions, Resources, and Conditions Reference](UsingWithRDS.IAM.ResourcePermissions.md)\. 

## Permissions Required to Use the Amazon RDS Console<a name="UsingWithRDS.IAM.RequiredPermissions.Console"></a>

For a user to work with the Amazon RDS console, that user must have a minimum set of permissions\. These permissions allow the user to describe the Amazon RDS resources for their AWS account and to provide other related information, including Amazon EC2 security and network information\.

If you create an IAM policy that is more restrictive than the minimum required permissions, the console won't function as intended for users with that IAM policy\. To ensure that those users can still use the Amazon RDS console, also attach the `AmazonRDSReadOnlyAccess` managed policy to the user, as described in [AWS Managed \(Predefined\) Policies for Amazon RDS](#UsingWithRDS.IAM.AccessControl.ManagedPolicies)\.

You don't need to allow minimum console permissions for users that are making calls only to the AWS CLI or the Amazon RDS API\. 

## AWS Managed \(Predefined\) Policies for Amazon RDS<a name="UsingWithRDS.IAM.AccessControl.ManagedPolicies"></a>

AWS addresses many common use cases by providing standalone IAM policies that are created and administered by AWS\. Managed policies grant necessary permissions for common use cases so you can avoid having to investigate what permissions are needed\. For more information, see [AWS Managed Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_managed-vs-inline.html#aws-managed-policies) in the *IAM User Guide*\.

The following AWS managed policies, which you can attach to users in your account, are specific to Amazon RDS:
+ **AmazonRDSReadOnlyAccess** – Grants read\-only access to all Amazon RDS resources for the root AWS account\.
+ **AmazonRDSFullAccess** – Grants full access to all Amazon RDS resources for the root AWS account\.

You can also create custom IAM policies that allow users to access the required Amazon RDS API actions and resources\. You can attach these custom policies to the IAM users or groups that require those permissions\. 

## Customer Managed Policy Examples<a name="IAMPolicyExamples-RDS"></a>

In this section, you can find example user policies that grant permissions for various Amazon RDS actions\. These policies work when you are using RDS API actions, AWS SDKs, or the AWS CLI\. When you are using the console, you need to grant additional permissions specific to the console, which is discussed in [Permissions Required to Use the Amazon RDS Console](#UsingWithRDS.IAM.RequiredPermissions.Console)\.

**Note**  
All examples use the US West \(Oregon\) Region \(`us-west-2`\) and contain fictitious account IDs\.

**Topics**
+ [Example 1: Allow a User to Perform Any Describe Action on Any RDS Resource](#IAMPolicyExamples-RDS-perform-describe-action)
+ [Example 2: Allow a User to Create a DB Instance That Uses the Specified DB Parameter and Security Groups](#IAMPolicyExamples-RDS-create-db-instance)
+ [Example 3: Prevent a User from Deleting a DB Instance](#IAMPolicyExamples-RDS-prevent-db-deletion)

### Example 1: Allow a User to Perform Any Describe Action on Any RDS Resource<a name="IAMPolicyExamples-RDS-perform-describe-action"></a>

The following permissions policy grants permissions to a user to run all of the actions that begin with `Describe`\. These actions show information about an RDS resource, such as a DB instance\. The wildcard character \(\*\) in the `Resource` element indicates that the actions are allowed for all Amazon RDS resources owned by the account\. 

```
{
   "Version":"2012-10-17",
   "Statement":[
      {
         "Sid":"AllowRDSDescribe",
         "Effect":"Allow",
         "Action":"rds:Describe*",
         "Resource":"*"
      }
   ]
}
```

### Example 2: Allow a User to Create a DB Instance That Uses the Specified DB Parameter and Security Groups<a name="IAMPolicyExamples-RDS-create-db-instance"></a>

The following permissions policy grants permissions to allow a user to only create a DB instance that must use the `mysql-production` DB parameter group and the `db-production` DB security group\. 

```
{
   "Version":"2012-10-17",
   "Statement":[
      {
         "Sid":"AllowMySQLProductionCreate",
         "Effect":"Allow",
         "Action":"rds:CreateDBInstance",
         "Resource":[
            "arn:aws:rds:us-west-2:123456789012:pg:mysql-production",
            "arn:aws:rds:us-west-2:123456789012:secgrp:db-production"
         ]
      }
   ]
}
```

### Example 3: Prevent a User from Deleting a DB Instance<a name="IAMPolicyExamples-RDS-prevent-db-deletion"></a>

The following permissions policy grants permissions to prevent a user from deleting a specific DB instance\. For example, you might want to deny the ability to delete your production instances to any user that is not an administrator\.

```
{
   "Version":"2012-10-17",
   "Statement":[
      {
         "Sid":"DenyDelete1",
         "Effect":"Deny",
         "Action":"rds:DeleteDBInstance",
         "Resource":"arn:aws:rds:us-west-2:123456789012:db:my-mysql-instance"
      }
   ]
}
```