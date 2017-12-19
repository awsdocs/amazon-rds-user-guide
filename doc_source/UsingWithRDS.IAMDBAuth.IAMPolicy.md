# Creating and Using an IAM Policy for IAM Database Access<a name="UsingWithRDS.IAMDBAuth.IAMPolicy"></a>

To allow an IAM user or role to connect to your DB instance or DB cluster, you must create an IAM policy\. After that, you attach the policy to an IAM user or role\.

**Note**  
To learn more about IAM policies, see [Authentication and Access Control for Amazon RDS](UsingWithRDS.IAM.md)\.

The following example policy allows an IAM user to connect to a DB instance using IAM database authentication\.

```
 1. {
 2.    "Version": "2012-10-17",
 3.    "Statement": [
 4.       {
 5.          "Effect": "Allow",
 6.          "Action": [
 7.              "rds-db:connect"
 8.          ],
 9.          "Resource": [
10.              "arn:aws:rds-db:us-west-2:123456789012:dbuser:db-12ABC34DEFG5HIJ6KLMNOP78QR/jane_doe"
11.          ]
12.       }
13.    ]
14. }
```

**Important**  
Don't confuse the `rds-db:` prefix with other Amazon RDS action prefixes that begin with `rds:`\. You use the `rds-db:` prefix and the `rds-db:connect` action only for IAM database authentication\. They aren't valid in any other context\. 

The example policy includes a single statement with the following elements:

+ `Effect`—Specify `Allow` to grant access to the DB instance\. If you don't explicitly allow access, then access is denied by default\.

+ `Action`—Specify `rds-db:connect` to allow connection to the DB instance\.

+ `Resource`—Specify an Amazon Resource Name \(ARN\) that describes one database account in one DB instance\. The ARN format is as follows\.

  ```
  arn:aws:rds-db:region:account-id:dbuser:dbi-resource-id/database-user-name
  ```

  In this format, the following are so:

  + `region` is the AWS Region for the Amazon RDS DB instance\. In the example policy, the AWS Region is `us-west-2`\.

  + `account-id` is the AWS account number for the DB instance\. In the example policy, the account number is `123456789012`\.

  + `dbi-resource-id` is the identifier for the DB instance\. This identifier is unique to an AWS Region and never changes\. In the example policy, the identifier is `db-12ABC34DEFG5HIJ6KLMNOP78QR`\.

    To find a DB instance resource ID in the AWS Management Console for Amazon RDS, choose the DB instance you want, and then choose **Instance Actions**, **See Details**\. The **Resource ID** is shown in the **Configuration Details** section\.

    Alternatively, you can use the AWS CLI command to list the identifiers and resource IDs for all of your DB instances in the current AWS Region, as shown following\.

    ```
    aws rds describe-db-instances \
        --query "DBInstances[*].[DBInstanceIdentifier,DbiResourceId]"
    ```

  + `db-user-name` is the name of the MySQL database account to associate with IAM authentication\. In the example policy, the database account is `jane_doe`\.

You can construct other ARNs to support various access patterns\. The following policy allows access to two different database accounts in a DB instance:

```
 1. {
 2.    "Version": "2012-10-17",
 3.    "Statement": [
 4.       {
 5.          "Effect": "Allow",
 6.          "Action": [
 7.              "rds-db:connect"
 8.          ],
 9.          "Resource": [
10.              "arn:aws:rds-db:us-west-2:123456789012:dbuser:db-12ABC34DEFG5HIJ6KLMNOP78QR/jane_doe",
11.              "arn:aws:rds-db:us-west-2:123456789012:dbuser:db-12ABC34DEFG5HIJ6KLMNOP78QR/mary_roe"
12.          ]
13.       }
14.    ]
15. }
```

The following IAM policy allows access to a DB cluster, rather than a DB instance\. The cluster identifier is `cluster-CO4FHMOYDKJ7CVBEJS2UWDQX7I`\.

```
 1. {
 2.    "Version": "2012-10-17",
 3.    "Statement": [
 4.       {
 5.          "Effect": "Allow",
 6.          "Action": [
 7.              "rds-db:connect"
 8.          ],
 9.          "Resource": [
10.              "arn:aws:rds-db:us-west-2:123456789012:dbuser:cluster-CO4FHMOYDKJ7CVBEJS2UWDQX7I/jane_doe"
11.          ]
12.       }
13.    ]
14. }
```

To find a DB cluster resource ID in the AWS Management Console for Amazon RDS, choose the DB cluster you want and expand the selection, and then choose **Instance Actions**, **See Details**\. The **Resource ID** is shown in the **DB Cluster Details** section\.

Alternatively, you can use the AWS CLI command to list the identifiers and resource IDs for all of your DB clusters in the current AWS Region, as shown following\.

```
aws rds describe-db-clusters \
    --query "DBClusters[*].[DBClusterIdentifier,DbClusterResourceId]"
```

The following policy uses the "\*" character to match all of the DB instances and DB clusters for a particular AWS account and AWS Region\. However, the policy only grants access to DB instances or DB clusters that have a `jane_doe` database account\.

```
 1. {
 2.    "Version": "2012-10-17",
 3.    "Statement": [
 4.       {
 5.          "Effect": "Allow",
 6.          "Action": [
 7.              "rds-db:connect"
 8.          ],
 9.          "Resource": [
10.              "arn:aws:rds-db:us-west-2:123456789012:dbuser:*/jane_doe"
11.          ]
12.       }
13.    ]
14. }
```

The IAM user or role has access to only those databases that the database user does\. For example, suppose that your DB instance has a database named *dev*, and another database named *test*\. If the database user `jane_doe` has access only to *dev*, any IAM users or roles that access that DB instance with the `jane_doe` user also have access only to *dev*\. This access restriction is also true for other database objects, such as tables, views, and so on\.

## Attaching an IAM Policy to an IAM User or Role<a name="UsingWithRDS.IAMDBAuth.IAMPolicy.Attaching"></a>

After you create an IAM policy to allow database authentication, you need to attach the policy to an IAM user or role\. For a tutorial on this topic, see [ Create and Attach Your First Customer Managed Policy](http://docs.aws.amazon.com/IAM/latest/UserGuide/tutorial_managed-policies.html) in the *IAM User Guide*\.

As you work through the tutorial, you can use one of the policy examples shown in this section as a starting point and tailor it to your needs\. At the end of the tutorial, you have an IAM user with an attached policy that can make use of the `rds-db:connect` action\.

**Note**  
You can map multiple IAM users or roles to the same database user account\. For example, suppose that your IAM policy specified the following resource ARN\.  

```
arn:aws:rds-db:us-west-2:123456789012:dbuser:db-12ABC34DEFG5HIJ6KLMNOP78QR/jane_doe
```
If you attach the policy to IAM users *Jane*, *Bob*, and *Diego*, then each of those users can connect to the specified DB instance using the `jane_doe` database account\.