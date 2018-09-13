# Overview of Managing Access Permissions to Your Amazon RDS Resources<a name="UsingWithRDS.IAM.AccessControl.Overview"></a>

Every AWS resource is owned by an AWS account, and permissions to create or access the resources are governed by permissions policies\. An account administrator can attach permissions policies to IAM identities \(that is, users, groups, and roles\), and some services \(such as AWS Lambda\) also support attaching permissions policies to resources\.

**Note**  
An *account administrator* \(or administrator user\) is a user with administrator privileges\. For more information, see [IAM Best Practices](http://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html) in the *IAM User Guide*\.

When granting permissions, you decide who is getting the permissions, the resources they get permissions for, and the specific actions that you want to allow on those resources\. 

**Topics**
+ [Amazon RDS Resources and Operations](#CreatingIAMPolicies-RDS)
+ [Understanding Resource Ownership](#UsingWithRDS.IAM.AccessControl.ResourceOwner)
+ [Managing Access to Resources](#UsingWithRDS.IAM.AccessControl.ManagingAccess)
+ [Specifying Policy Elements: Actions, Effects, Resources, and Principals](#SpecifyingIAMPolicyActions-RDS)
+ [Specifying Conditions in a Policy](#SpecifyingIAMPolicyConditions-RDS)

## Amazon RDS Resources and Operations<a name="CreatingIAMPolicies-RDS"></a>

In Amazon RDS, the primary resource is a *DB instance*\. Amazon RDS supports other resources that can be used with the primary resource such as *DB snapshots*, *parameter groups*, and *event subscriptions*\. These are referred to as *subresources*\. 

These resources and subresources have unique Amazon Resource Names \(ARNs\) associated with them as shown in the following table\.


| **Resource Type**  |  **ARN Format**  | 
| --- | --- | 
| DB cluster | `arn:aws:rds:region:account-id:cluster:db-cluster-name` | 
| DB cluster parameter group | `arn:aws:rds:region:account-id:cluster-pg:cluster-parameter-group-name` | 
| DB cluster snapshot | `arn:aws:rds:region:account-id:cluster-snapshot:cluster-snapshot-name` | 
| DB instance | `arn:aws:rds:region:account-id:db:db-instance-name` | 
| DB option group | `arn:aws:rds:region:account-id:og:option-group-name` | 
| DB parameter group | `arn:aws:rds:region:account-id:pg:parameter-group-name` | 
| DB snapshot | `arn:aws:rds:region:account-id:snapshot:snapshot-name` | 
| DB security group | `arn:aws:rds:region:account-id:secgrp:security-group-name`   | 
| DB subnet group | `arn:aws:rds:region:account-id:subgrp:subnet-group-name` | 
| Event subscription | `arn:aws:rds:region:account-id:es:subscription-name` | 
| Read Replica | `arn:aws:rds:region:account-id:db:db-instance-name` | 
| Reserved DB instance | `arn:aws:rds:region:account-id:ri:reserved-db-instance-name` | 

Amazon RDS provides a set of operations to work with the Amazon RDS resources\. For a list of available operations, see [Actions](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_Operations.html)\. 

## Understanding Resource Ownership<a name="UsingWithRDS.IAM.AccessControl.ResourceOwner"></a>

A *resource owner* is the AWS account that created a resource\. That is, the resource owner is the AWS account of the *principal entity* \(the root account, an IAM user, or an IAM role\) that authenticates the request that creates the resource\. The following examples illustrate how this works:
+ If you use the root account credentials of your AWS account to create an RDS resource, such as a DB instance, your AWS account is the owner of the RDS resource\.
+ If you create an IAM user in your AWS account and grant permissions to create RDS resources to that user, the user can create RDS resources\. However, your AWS account, to which the user belongs, owns the RDS resources\.
+ If you create an IAM role in your AWS account with permissions to create RDS resources, anyone who can assume the role can create RDS resources\. Your AWS account, to which the role belongs, owns the RDS resources\. 

## Managing Access to Resources<a name="UsingWithRDS.IAM.AccessControl.ManagingAccess"></a>

A *permissions policy* describes who has access to what\. The following section explains the available options for creating permissions policies\.

**Note**  
This section discusses using IAM in the context of Amazon RDS\. It doesn't provide detailed information about the IAM service\. For complete IAM documentation, see [What Is IAM?](http://docs.aws.amazon.com/IAM/latest/UserGuide/introduction.html) in the *IAM User Guide*\. For information about IAM policy syntax and descriptions, see [AWS IAM Policy Reference](http://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies.html) in the *IAM User Guide*\.

Policies attached to an IAM identity are referred to as *identity\-based* policies \(IAM polices\) and policies attached to a resource are referred to as *resource\-based* policies\. Amazon RDS supports only identity\-based policies \(IAM policies\)\.

**Topics**
+ [Identity\-Based Policies \(IAM Policies\)](#UsingWithRDS.IAM.AccessControl.ManagingAccess.IdentityBased)
+ [Resource\-Based Policies](#UsingWithRDS.IAM.AccessControl.ManagingAccess.ResourceBased)

### Identity\-Based Policies \(IAM Policies\)<a name="UsingWithRDS.IAM.AccessControl.ManagingAccess.IdentityBased"></a>

You can attach policies to IAM identities\. For example, you can do the following: 
+ **Attach a permissions policy to a user or a group in your account** – An account administrator can use a permissions policy that is associated with a particular user to grant permissions for that user to create an Amazon RDS resource, such as a DB instance\. 
+ **Attach a permissions policy to a role \(grant cross\-account permissions\)** – You can attach an identity\-based permissions policy to an IAM role to grant cross\-account permissions\. For example, the administrator in Account A can create a role to grant cross\-account permissions to another AWS account \(for example, Account B\) or an AWS service as follows:

  1. Account A administrator creates an IAM role and attaches a permissions policy to the role that grants permissions on resources in Account A\.

  1. Account A administrator attaches a trust policy to the role identifying Account B as the principal who can assume the role\. 

  1. Account B administrator can then delegate permissions to assume the role to any users in Account B\. Doing this allows users in Account B to create or access resources in Account A\. The principal in the trust policy can also be an AWS service principal if you want to grant an AWS service permissions to assume the role\.

   For more information about using IAM to delegate permissions, see [Access Management](http://docs.aws.amazon.com/IAM/latest/UserGuide/access.html) in the *IAM User Guide*\. 

The following is an example policy that allows the user with the ID `123456789012` to create DB instances for your AWS account\. The policy requires that the name of the new DB instance begin with `test`\. The new DB instance must also use the MySQL database engine and the `db.t2.micro` DB instance class\. In addition, the new DB instance must use an option group and a DB parameter group that starts with `default`, and it must use the `default` subnet group\.

```
 1. {
 2.     "Version": "2012-10-17",
 3.     "Statement": [
 4.         {
 5.             "Sid": "AllowCreateDBInstanceOnly",
 6.             "Effect": "Allow",
 7.             "Action": [
 8.                 "rds:CreateDBInstance"
 9.             ],
10.             "Resource": [
11.                 "arn:aws:rds:*:123456789012:db:test*",
12.                 "arn:aws:rds:*:123456789012:og:default*",
13.                 "arn:aws:rds:*:123456789012:pg:default*",
14.                 "arn:aws:rds:*:123456789012:subgrp:default"
15.             ],
16.             "Condition": {
17.                 "StringEquals": {
18.                     "rds:DatabaseEngine": "mysql",
19.                     "rds:DatabaseClass": "db.t2.micro"
20.                 }
21.             }
22.         }
23.     ]
24. }
```

For more information about using identity\-based policies with Amazon RDS, see [Using Identity\-Based Policies \(IAM Policies\) for Amazon RDS](UsingWithRDS.IAM.AccessControl.IdentityBased.md)\. For more information about users, groups, roles, and permissions, see [Identities \(Users, Groups, and Roles\)](http://docs.aws.amazon.com/IAM/latest/UserGuide/id.html) in the *IAM User Guide*\. 

### Resource\-Based Policies<a name="UsingWithRDS.IAM.AccessControl.ManagingAccess.ResourceBased"></a>

Other services, such as Amazon S3, also support resource\-based permissions policies\. For example, you can attach a policy to an S3 bucket to manage access permissions to that bucket\. Amazon RDS doesn't support resource\-based policies\. 

## Specifying Policy Elements: Actions, Effects, Resources, and Principals<a name="SpecifyingIAMPolicyActions-RDS"></a>

For each Amazon RDS resource \(see [Amazon RDS Resources and Operations](#CreatingIAMPolicies-RDS)\), the service defines a set of API operations \(see [Actions](http://docs.aws.amazon.com/redshift/latest/APIReference/API_Operations.html)\)\. To grant permissions for these API operations, Amazon RDS defines a set of actions that you can specify in a policy\. Performing an API operation can require permissions for more than one action\. 

The following are the basic policy elements:
+ **Resource** – In a policy, you use an Amazon Resource Name \(ARN\) to identify the resource to which the policy applies\. For more information, see [Amazon RDS Resources and Operations](#CreatingIAMPolicies-RDS)\. 
+ **Action** – You use action keywords to identify resource operations that you want to allow or deny\. For example, the `rds:DescribeDBInstances` permission allows the user permissions to perform the Amazon RDS `DescribeDBInstances` operation\. 
+ **Effect** – You specify the effect when the user requests the specific action—this can be either allow or deny\. If you don't explicitly grant access to \(allow\) a resource, access is implicitly denied\. You can also explicitly deny access to a resource, which you might do to make sure that a user cannot access it, even if a different policy grants access\.
+ **Principal** – In identity\-based policies \(IAM policies\), the user that the policy is attached to is the implicit principal\. For resource\-based policies, you specify the user, account, service, or other entity that you want to receive permissions \(applies to resource\-based policies only\)\. Amazon RDS doesn't support resource\-based policies\.

To learn more about IAM policy syntax and descriptions, see [AWS IAM Policy Reference](http://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies.html) in the *IAM User Guide*\.

For a table showing all of the Amazon RDS API actions and the resources that they apply to, see [Amazon RDS API Permissions: Actions, Resources, and Conditions Reference](UsingWithRDS.IAM.ResourcePermissions.md)\. 

You can test IAM policies with the IAM policy simulator\. It automatically provides a list of resources and parameters required for each AWS action, including Amazon RDS actions\. The IAM policy simulator determines the permissions required for each of the actions that you specify\. For information about the IAM policy simulator, see [ Testing IAM Policies with the IAM Policy Simulator](http://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_testing-policies.html) in the *IAM User Guide*\. 

## Specifying Conditions in a Policy<a name="SpecifyingIAMPolicyConditions-RDS"></a>

When you grant permissions, you can use the access policy language to specify the conditions when a policy should take effect\. For example, you might want a policy to be applied only after a specific date\. For more information about specifying conditions in a policy language, see [Condition](http://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements.html#Condition) in the *IAM User Guide*\.

To express conditions, you use predefined condition keys\. There are AWS\-wide condition keys and RDS\-specific keys that you can use as appropriate\. For a complete list of AWS\-wide keys, see [Available Keys for Conditions](http://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements.html#AvailableKeys) in the *IAM User Guide*\. For a complete list of RDS\-specific keys, see [Using IAM Policy Conditions for Fine\-Grained Access Control](UsingWithRDS.IAM.Conditions.md)\. 