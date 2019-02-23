# Using IAM Policy Conditions for Fine\-Grained Access Control<a name="UsingWithRDS.IAM.Conditions"></a>

When you grant permissions in Amazon RDS, you can specify conditions that determine how a permissions policy takes effect\. 

## Overview<a name="UsingWithRDS.IAM.Conditions.Overview"></a>

In Amazon RDS, you have the option to specify conditions when granting permissions using an IAM policy \(see [Access Control](UsingWithRDS.IAM.md#UsingWithRDS.IAM.AccessControl)\)\. For example, you can: 
+ Allow users to create a DB instance only if they specify a particular database engine\.
+ Allow users to modify RDS resources that are tagged with a particular tag name and tag value\.

There are two ways to specify conditions in an IAM policy for Amazon RDS:
+ [Using Condition Keys](#UsingWithRDS.IAM.SpecifyingConditions)
+ [Using Custom Tags](#UsingWithRDS.IAM.SpecifyingCustomTags)

## Specifying Conditions: Using Condition Keys<a name="UsingWithRDS.IAM.SpecifyingConditions"></a>

AWS provides a set of predefined condition keys \(AWS\-wide condition keys\) for all AWS services that support IAM for access control\. For example, you can use the `aws:userid` condition key to require a specific AWS ID when requesting an action\. For more information and a list of the AWS\-wide condition keys, see [Available Keys for Conditions](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements.html#AvailableKeys) in the *IAM User Guide*\.

**Note**  
Condition key names are not case\-sensitive\. Case\-sensitivity of condition key values depends on the condition operator that you use\. For more information, see [IAM JSON Policy Elements: Condition Operators](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_condition_operators.html) in the *IAM User Guide*\.

In addition Amazon RDS also provides its own condition keys that you can include in `Condition` elements in an IAM permissions policy\. The following table shows the RDS condition keys that apply to RDS resources\. 

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/UsingWithRDS.IAM.Conditions.html)

For example, the following `Condition` element uses a condition key and specifies the MySQL database engine\. You could apply this to an IAM policy that allows permission to the `rds:CreateDBInstance` action to enable users to only create DB instances with the MySQL database engine\. For an example of an IAM policy that uses this condition, see [Example Policies: Using Condition Keys](#UsingWithRDS.IAM.Conditions.Examples)\. 

`"Condition":{"StringEquals":{"rds:DatabaseEngine": "mysql" } } `

For a list of all of the RDS condition key identifiers and the RDS actions and resources that they apply to, see [Amazon RDS API Permissions: Actions, Resources, and Conditions Reference](UsingWithRDS.IAM.ResourcePermissions.md)\.

### Example Policies: Using Condition Keys<a name="UsingWithRDS.IAM.Conditions.Examples"></a>

Following are examples of how you can use condition keys in Amazon RDS IAM permissions policies\. 

#### Example 1: Grant Permission to Create a DB Instance that Uses a Specific DB Engine and Isn't MultiAZ<a name="w4aac22c13c17b7c16b4"></a>

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

#### Example 2: Explicitly Deny Permission to Create DB Instances for Certain DB Instance Classes and Create DB Instances that Use Provisioned IOPS<a name="w4aac22c13c17b7c16b6"></a>

The following policy explicitly denies permission to create DB instances that use the DB instance classes `r3.8xlarge` and `m4.10xlarge`, which are the largest and most expensive instances\. This policy also prevents users from creating DB instances that use Provisioned IOPS, which incurs an additional cost\. 

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

#### Example 3: Limit the Set of Tag Keys and Values That Can Be Used to Tag a Resource<a name="w4aac22c13c17b7c16b8"></a>

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

RDS supports specifying conditions in an IAM policy using custom tags\.

For example, if you add a tag named `environment` to your DB instances with values such as `beta`, `staging`, `production`, and so on, you can create a policy that restricts certain users to DB instances based on the `environment` tag value\.

**Note**  
Custom tag identifiers are case\-sensitive\.

The following table lists the RDS tag identifiers that you can use in a `Condition` element\. 

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/UsingWithRDS.IAM.Conditions.html)

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

For a list of all of the condition key values, and the RDS actions and resources that they apply to, see [Amazon RDS API Permissions: Actions, Resources, and Conditions Reference](UsingWithRDS.IAM.ResourcePermissions.md)\.

### Example Policies: Using Custom Tags<a name="UsingWithRDS.IAM.Conditions.Tags.Examples"></a>

Following are examples of how you can use custom tags in Amazon RDS IAM permissions policies\. For more information about adding tags to an Amazon RDS resource, see [Working with Amazon Resource Names \(ARNs\) in Amazon RDS](USER_Tagging.ARN.md)\. 

**Note**  
All examples use the us\-west\-2 region and contain fictitious account IDs\.

#### Example 1: Grant Permission for Actions on a Resource with a Specific Tag with Two Different Values<a name="w4aac22c13c17b9c26b6"></a>

The following policy allows permission to perform the `ModifyDBInstance` and `CreateDBSnapshot` APIs on instances with either the `stage` tag set to `development` or `test`\. 

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

#### Example 2: Explicitly Deny Permission to Create a DB Instance that Uses Specified DB Parameter Groups<a name="w4aac22c13c17b9c26b8"></a>

The following policy explicitly denies permission to create a DB instance that uses DB parameter groups with specific tag values\. You might apply this policy if you require that a specific customer\-created DB parameter group always be used when creating DB instances\. Note that policies that use `Deny` are most often used to restrict access that was granted by a broader policy\.

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

#### Example 3: Grant Permission for Actions on a DB Instance with an Instance Name that is Prefixed with a User Name<a name="w4aac22c13c17b9c26c10"></a>

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