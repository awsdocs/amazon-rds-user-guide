# Amazon RDS Identity\-Based Policy Examples<a name="security_iam_id-based-policy-examples"></a>

By default, IAM users and roles don't have permission to create or modify Amazon RDS resources\. They also can't perform tasks using the AWS Management Console, AWS CLI, or AWS API\. An IAM administrator must create IAM policies that grant users and roles permission to perform specific API operations on the specified resources they need\. The administrator must then attach those policies to the IAM users or groups that require those permissions\.

To learn how to create an IAM identity\-based policy using these example JSON policy documents, see [Creating Policies on the JSON Tab](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_create.html#access_policies_create-json-editor) in the *IAM User Guide*\.

**Topics**
+ [Policy Best Practices](#security_iam_service-with-iam-policy-best-practices)
+ [Using the Amazon RDS Console](#security_iam_id-based-policy-examples-console)
+ [Allow Users to View Their Own Permissions](#security_iam_id-based-policy-examples-view-own-permissions)
+ [Allow a User to Create to DB Instances in an AWS account](#security_iam_id-based-policy-examples-create-db-instance-in-account)
+ [Permissions Required to Use the Console](#UsingWithRDS.IAM.RequiredPermissions.Console)
+ [Allow a User to Perform Any Describe Action on Any RDS Resource](#IAMPolicyExamples-RDS-perform-describe-action)
+ [Allow a User to Create a DB Instance That Uses the Specified DB Parameter and Security Groups](#security_iam_id-based-policy-examples-create-db-instance-specified-groups)
+ [Grant Permission for Actions on a Resource with a Specific Tag with Two Different Values](#security_iam_id-based-policy-examples-grant-permissions-tags)
+ [Prevent a User from Deleting a DB Instance](#IAMPolicyExamples-RDS-prevent-db-deletion)
+ [Deny All Access to a Resource](#IAMPolicyExamples-RDS-deny-all-access)
+ [Example Policies: Using Condition Keys](#UsingWithRDS.IAM.Conditions.Examples)
+ [Specifying Conditions: Using Custom Tags](#UsingWithRDS.IAM.SpecifyingCustomTags)

## Policy Best Practices<a name="security_iam_service-with-iam-policy-best-practices"></a>

Identity\-based policies are very powerful\. They determine whether someone can create, access, or delete Amazon RDS resources in your account\. These actions can incur costs for your AWS account\. When you create or edit identity\-based policies, follow these guidelines and recommendations:
+ **Get Started Using AWS Managed Policies** – To start using Amazon RDS quickly, use AWS managed policies to give your employees the permissions they need\. These policies are already available in your account and are maintained and updated by AWS\. For more information, see [Get Started Using Permissions With AWS Managed Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html#bp-use-aws-defined-policies) in the *IAM User Guide*\.
+ **Grant Least Privilege** – When you create custom policies, grant only the permissions required to perform a task\. Start with a minimum set of permissions and grant additional permissions as necessary\. Doing so is more secure than starting with permissions that are too lenient and then trying to tighten them later\. For more information, see [Grant Least Privilege](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html#grant-least-privilege) in the *IAM User Guide*\.
+ **Enable MFA for Sensitive Operations** – For extra security, require IAM users to use multi\-factor authentication \(MFA\) to access sensitive resources or API operations\. For more information, see [Using Multi\-Factor Authentication \(MFA\) in AWS](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_mfa.html) in the *IAM User Guide*\.
+ **Use Policy Conditions for Extra Security** – To the extent that it's practical, define the conditions under which your identity\-based policies allow access to a resource\. For example, you can write conditions to specify a range of allowable IP addresses that a request must come from\. You can also write conditions to allow requests only within a specified date or time range, or to require the use of SSL or MFA\. For more information, see [IAM JSON Policy Elements: Condition](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_condition.html) in the *IAM User Guide*\.

## Using the Amazon RDS Console<a name="security_iam_id-based-policy-examples-console"></a>

To access the Amazon RDS console, you must have a minimum set of permissions\. These permissions must enable you to list and view details about the Amazon RDS resources in your AWS account\. You can create an identity\-based policy that is more restrictive than the minimum required permissions\. However, if you do, the console doesn't function as intended for entities \(IAM users or roles\) with that policy\.

To ensure that those entities can still use the Amazon RDS console, also attach the following AWS managed policy to the entities\. For more information, see [Adding Permissions to a User](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_change-permissions.html#users_change_permissions-add-console) in the *IAM User Guide*\.

```
AmazonRDSReadOnlyAccess
```

You don't need to allow minimum console permissions for users that are making calls only to the AWS CLI or the AWS API\. Instead, allow access to only the actions that match the API operation that you're trying to perform\.

## Allow Users to View Their Own Permissions<a name="security_iam_id-based-policy-examples-view-own-permissions"></a>

This example shows how you might create a policy that allows IAM users to view the inline and managed policies that are attached to their user identity\. This policy includes permissions to complete this action on the console or programmatically using the AWS CLI or AWS API\.

```
{
       "Version": "2012-10-17",
       "Statement": [
           {
               "Sid": "ViewOwnUserInfo",
               "Effect": "Allow",
               "Action": [
                   "iam:GetUserPolicy",
                   "iam:ListGroupsForUser",
                   "iam:ListAttachedUserPolicies",
                   "iam:ListUserPolicies",
                   "iam:GetUser"
               ],
               "Resource": [
                   "arn:aws:iam::*:user/${aws:username}"
               ]
           },
           {
               "Sid": "NavigateInConsole",
               "Effect": "Allow",
               "Action": [
                   "iam:GetGroupPolicy",
                   "iam:GetPolicyVersion",
                   "iam:GetPolicy",
                   "iam:ListAttachedGroupPolicies",
                   "iam:ListGroupPolicies",
                   "iam:ListPolicyVersions",
                   "iam:ListPolicies",
                   "iam:ListUsers"
               ],
               "Resource": "*"
           }
       ]
   }
```

## Allow a User to Create to DB Instances in an AWS account<a name="security_iam_id-based-policy-examples-create-db-instance-in-account"></a>

The following is an example policy that allows the user with the ID `123456789012` to create DB instances for your AWS account\. The policy requires that the name of the new DB instance begin with `test`\. The new DB instance must also use the MySQL database engine and the `db.t2.micro` DB instance class\. In addition, the new DB instance must use an option group and a DB parameter group that starts with `default`, and it must use the `default` subnet group\.

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
+ The policy allows the IAM user to create a DB instance using the [CreateDBInstance](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html) API operation \(this also applies to the [create\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html) AWS CLI command and the AWS Management Console\)\.
+ The `Resource` element specifies that the user can perform actions on or with resources\. You specify resources using an Amazon Resources Name \(ARN\)\. This ARN includes the name of the service that the resource belongs to \(`rds`\), the AWS Region \(`*` indicates any region in this example\), the user account number \(`123456789012` is the user ID in this example\), and the type of resource\. For more information about creating ARNs, see [Working with Amazon Resource Names \(ARNs\) in Amazon RDS](USER_Tagging.ARN.md)\.

  The `Resource` element in the example specifies the following policy constraints on resources for the user:
  + The DB instance identifier for the new DB instance must begin with `test` \(for example, `testCustomerData1`, `test-region2-data`\)\.
  + The option group for the new DB instance must begin with `default`\.
  + The DB parameter group for the new DB instance must begin with `default`\.
  + The subnet group for the new DB instance must be the `default` subnet group\.
+ The `Condition` element specifies that the DB engine must be MySQL and the DB instance class must be `db.t2.micro`\. The `Condition` element specifies the conditions when a policy should take effect\. You can add additional permissions or restrictions by using the `Condition` element\. For more information about specifying conditions, see [Condition Keys](security_iam_service-with-iam.md#UsingWithRDS.IAM.Conditions)\. This example specifies the `rds:DatabaseEngine` and `rds:DatabaseClass` conditions\. For information about the valid condition values for `rds:DatabaseEngine`, see the list under the `Engine` parameter in [CreateDBInstance](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html)\. For information about the valid condition values for `rds:DatabaseClass`, see [Supported DB Engines for DB Instance Classes](Concepts.DBInstanceClass.md#Concepts.DBInstanceClass.Support) \. 

The policy doesn't specify the `Principal` element because in an identity\-based policy you don't specify the principal who gets the permission\. When you attach policy to a user, the user is the implicit principal\. When you attach a permission policy to an IAM role, the principal identified in the role's trust policy gets the permissions\.

To see a list of Amazon RDS actions, see [Actions Defined by Amazon RDS](https://docs.aws.amazon.com/IAM/latest/UserGuide/list_amazonrds.html#amazonrds-actions-as-permissions) in the *IAM User Guide*\.

## Permissions Required to Use the Console<a name="UsingWithRDS.IAM.RequiredPermissions.Console"></a>

For a user to work with the console, that user must have a minimum set of permissions\. These permissions allow the user to describe the Amazon RDS resources for their AWS account and to provide other related information, including Amazon EC2 security and network information\.

If you create an IAM policy that is more restrictive than the minimum required permissions, the console doesn't function as intended for users with that IAM policy\. To ensure that those users can still use the console, also attach the `AmazonRDSReadOnlyAccess` managed policy to the user, as described in [Managing Access Using Policies](UsingWithRDS.IAM.md#security_iam_access-manage)\.

You don't need to allow minimum console permissions for users that are making calls only to the AWS CLI or the Amazon RDS API\. 

The following policy grants full access to all Amazon RDS resources for the root AWS account:

```
AmazonRDSFullAccess             
```

## Allow a User to Perform Any Describe Action on Any RDS Resource<a name="IAMPolicyExamples-RDS-perform-describe-action"></a>

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

## Allow a User to Create a DB Instance That Uses the Specified DB Parameter and Security Groups<a name="security_iam_id-based-policy-examples-create-db-instance-specified-groups"></a>

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

## Grant Permission for Actions on a Resource with a Specific Tag with Two Different Values<a name="security_iam_id-based-policy-examples-grant-permissions-tags"></a>

You can use conditions in your identity\-based policy to control access to Amazon RDS resources based on tags\. The following policy allows permission to perform the `ModifyDBInstance` and `CreateDBSnapshot` APIs on DB instances with either the `stage` tag set to `development` or `test`\. 

```
 1. {
 2.    "Version":"2012-10-17",
 3.    "Statement":[
 4.       {
 5.          "Sid":"AllowDevTestCreate",
 6.          "Effect":"Allow",
 7.          "Action":[
 8.             "rds:ModifyDBInstance",
 9.             "rds:CreateDBSnapshot"
10.          ],
11.          "Resource":"*",
12.          "Condition":{
13.             "StringEquals":{
14.                "rds:db-tag/stage":[
15.                   "development",
16.                   "test"
17.                ]
18.             }
19.          }
20.       }
21.    ]
22. }
```

## Prevent a User from Deleting a DB Instance<a name="IAMPolicyExamples-RDS-prevent-db-deletion"></a>

The following permissions policy grants permissions to prevent a user from deleting a specific DB instance\. For example, you might want to deny the ability to delete your production DB instances to any user that is not an administrator\.

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

## Deny All Access to a Resource<a name="IAMPolicyExamples-RDS-deny-all-access"></a>

You can explicitly deny access to a resource\. Deny policies take precedence over allow policies\. The following policy explicitly denies a user the ability to manage a resource:

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Deny",
      "Action": "rds:*",
      "Resource": "arn:aws:rds:us-east-1:123456789012:db:mydb"
    }
  ]
}
```

## Example Policies: Using Condition Keys<a name="UsingWithRDS.IAM.Conditions.Examples"></a>

Following are examples of how you can use condition keys in Amazon RDS IAM permissions policies\. 

### Example 1: Grant Permission to Create a DB Instance that Uses a Specific DB Engine and Isn't MultiAZ<a name="w121aac36c34c43c33b4"></a>

The following policy uses an RDS condition key and allows a user to create only DB instances that use the MySQL database engine and don't use MultiAZ\. The `Condition` element indicates the requirement that the database engine is MySQL\. 

```
 1. {
 2. 
 3.    "Version":"2012-10-17",
 4.    "Statement":[
 5.       {
 6.          "Sid":"AllowMySQLCreate",
 7.          "Effect":"Allow",
 8.          "Action":"rds:CreateDBInstance",
 9.          "Resource":"*",
10.          "Condition":{
11.             "StringEquals":{
12.                "rds:DatabaseEngine":"mysql"
13.             },
14.             "Bool":{
15.                     "rds:MultiAz": false
16.             }
17.          }
18.       }
19.    ]
20. }
```

### Example 2: Explicitly Deny Permission to Create DB Instances for Certain DB Instance Classes and Create DB Instances that Use Provisioned IOPS<a name="w121aac36c34c43c33b6"></a>

The following policy explicitly denies permission to create DB instances that use the DB instance classes `r3.8xlarge` and `m4.10xlarge`, which are the largest and most expensive DB instance classes\. This policy also prevents users from creating DB instances that use Provisioned IOPS, which incurs an additional cost\. 

Explicitly denying permission supersedes any other permissions granted\. This ensures that identities to not accidentally get permission that you never want to grant\.

```
 1. {
 2.    "Version":"2012-10-17",
 3.    "Statement":[
 4.       {
 5.          "Sid":"DenyLargeCreate",
 6.          "Effect":"Deny",
 7.          "Action":"rds:CreateDBInstance",
 8.          "Resource":"*",
 9.          "Condition":{
10.             "StringEquals":{
11.                "rds:DatabaseClass":[
12.                   "db.r3.8xlarge",
13.                   "db.m4.10xlarge"
14.                ]
15.             }
16.          }
17.       },
18.       {
19.          "Sid":"DenyPIOPSCreate",
20.          "Effect":"Deny",
21.          "Action":"rds:CreateDBInstance",
22.          "Resource":"*",
23.          "Condition":{
24.             "NumericNotEquals":{
25.                "rds:Piops":"0"
26.             }
27.          }
28.       }
29.    ]
30. }
```

### Example 3: Limit the Set of Tag Keys and Values That Can Be Used to Tag a Resource<a name="w121aac36c34c43c33b8"></a>

The following policy uses an RDS condition key and allows the addition of a tag with the key `stage` to be added to a resource with the values `test`, `qa`, and `production`\.

```
 1. {
 2. 
 3.  {
 4.     "Version" : "2012-10-17",
 5.     "Statement" : [{
 6.        "Effect" : "Allow",
 7.        "Action" : [ "rds:AddTagsToResource", "rds:RemoveTagsFromResource" 
 8.        ],
 9.        "Resource" : "*",
10.        "Condition" : { "streq" : { "rds:req-tag/stage" : [ "test", "qa", 
11.        "production" ] } }
12.   }
13.  ]
14. }
15. }
```

## Specifying Conditions: Using Custom Tags<a name="UsingWithRDS.IAM.SpecifyingCustomTags"></a>

Amazon RDS supports specifying conditions in an IAM policy using custom tags\.

For example, suppose that you add a tag named `environment` to your DB instances with values such as `beta`, `staging`, `production`, and so on\. If you do, you can create a policy that restricts certain users to DB instances based on the `environment` tag value\.

**Note**  
Custom tag identifiers are case\-sensitive\.

The following table lists the RDS tag identifiers that you can use in a `Condition` element\. 

<a name="rds-iam-condition-tag-reference"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/security_iam_id-based-policy-examples.html)

The syntax for a custom tag condition is as follows:

`"Condition":{"StringEquals":{"rds:rds-tag-identifier/tag-name": ["value"]} }` 

For example, the following `Condition` element applies to DB instances with a tag named `environment` and a tag value of `production`\. 

` "Condition":{"StringEquals":{"rds:db-tag/environment": ["production"]} } ` 

For information about creating tags, see [Tagging Amazon RDS Resources](USER_Tagging.md)\.

**Important**  
If you manage access to your RDS resources using tagging, we recommend that you secure access to the tags for your RDS resources\. You can manage access to tags by creating policies for the `AddTagsToResource` and `RemoveTagsFromResource` actions\. For example, the following policy denies users the ability to add or remove tags for all resources\. You can then create policies to allow specific users to add or remove tags\.   

```
 1. {
 2.    "Version":"2012-10-17",
 3.    "Statement":[
 4.       {
 5.          "Sid":"DenyTagUpdates",
 6.          "Effect":"Deny",
 7.          "Action":[
 8.             "rds:AddTagsToResource",
 9.             "rds:RemoveTagsFromResource"
10.          ],
11.          "Resource":"*"
12.       }
13.    ]
14. }
```

To see a list of Amazon RDS actions, see [Actions Defined by Amazon RDS](https://docs.aws.amazon.com/IAM/latest/UserGuide/list_amazonrds.html#amazonrds-actions-as-permissions) in the *IAM User Guide*\.

### Example Policies: Using Custom Tags<a name="UsingWithRDS.IAM.Conditions.Tags.Examples"></a>

Following are examples of how you can use custom tags in Amazon RDS IAM permissions policies\. For more information about adding tags to an Amazon RDS resource, see [Working with Amazon Resource Names \(ARNs\) in Amazon RDS](USER_Tagging.ARN.md)\. 

**Note**  
All examples use the us\-west\-2 region and contain fictitious account IDs\.

#### Example 1: Grant Permission for Actions on a Resource with a Specific Tag with Two Different Values<a name="w121aac36c34c43c35c28b6"></a>

The following policy allows permission to perform the `ModifyDBInstance` and `CreateDBSnapshot` APIs on DB instances with either the `stage` tag set to `development` or `test`\. 

```
 1. {
 2.    "Version":"2012-10-17",
 3.    "Statement":[
 4.       {
 5.          "Sid":"AllowDevTestCreate",
 6.          "Effect":"Allow",
 7.          "Action":[
 8.             "rds:ModifyDBInstance",
 9.             "rds:CreateDBSnapshot"
10.          ],
11.          "Resource":"*",
12.          "Condition":{
13.             "StringEquals":{
14.                "rds:db-tag/stage":[
15.                   "development",
16.                   "test"
17.                ]
18.             }
19.          }
20.       }
21.    ]
22. }
```

#### Example 2: Explicitly Deny Permission to Create a DB Instance that Uses Specified DB Parameter Groups<a name="w121aac36c34c43c35c28b8"></a>

The following policy explicitly denies permission to create a DB instance that uses DB parameter groups with specific tag values\. You might apply this policy if you require that a specific customer\-created DB parameter group always be used when creating DB instances\. Policies that use `Deny` are most often used to restrict access that was granted by a broader policy\.

Explicitly denying permission supersedes any other permissions granted\. This ensures that identities to not accidentally get permission that you never want to grant\.

```
 1. {
 2.    "Version":"2012-10-17",
 3.    "Statement":[
 4.       {
 5.          "Sid":"DenyProductionCreate",
 6.          "Effect":"Deny",
 7.          "Action":"rds:CreateDBInstance",
 8.          "Resource":"*",
 9.          "Condition":{
10.             "StringEquals":{
11.                "rds:pg-tag/usage":"prod"
12.             }
13.          }
14.       }
15.    ]
16. }
```

#### Example 3: Grant Permission for Actions on a DB Instance with an Instance Name that is Prefixed with a User Name<a name="w121aac36c34c43c35c28c10"></a>

The following policy allows permission to call any API \(except to `AddTagsToResource` or `RemoveTagsFromResource`\) on a DB instance that has a DB instance name that is prefixed with the user's name and that has a tag called `stage` equal to `devo` or that has no tag called `stage`\.

The `Resource` line in the policy identifies a resource by its Amazon Resource Name \(ARN\)\. For more information about using ARNs with Amazon RDS resources, see [Working with Amazon Resource Names \(ARNs\) in Amazon RDS](USER_Tagging.ARN.md)\. 

```
{
   "Version":"2012-10-17",
   "Statement":[
      {
         "Sid":"AllowFullDevAccessNoTags",
         "Effect":"Allow",
         "NotAction":[
            "rds:AddTagsToResource",
            "rds:RemoveTagsFromResource"
         ],
         "Resource":"arn:aws:rds:*:123456789012:db:${aws:username}*",
         "Condition":{
            "StringEqualsIfExists":{
               "rds:db-tag/stage":"devo"
            }
         }
      }
   ]
}
```