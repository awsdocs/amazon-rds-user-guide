# Tagging Amazon RDS Resources<a name="USER_Tagging"></a>

You can use Amazon RDS tags to add metadata to your Amazon RDS resources\. You can also use these tags with IAM policies to manage access to Amazon RDS resources and to control what actions can be applied to the Amazon RDS resources\. Finally, you can use these tags to track costs by grouping expenses for similarly tagged resources\. 

All Amazon RDS resources can be tagged
+ DB instances
+ DB clusters
+ Read replicas
+ DB snapshots
+ DB cluster snapshots
+ Reserved DB instances
+ Event subscriptions
+ DB option groups
+ DB parameter groups
+ DB cluster parameter groups
+ DB security groups
+ DB subnet groups

For information on managing access to tagged resources with IAM policies, see [Identity and Access Management in Amazon RDS](UsingWithRDS.IAM.md)\. 

## Overview of Amazon RDS Resource Tags<a name="Overview.Tagging"></a>

An Amazon RDS tag is a name\-value pair that you define and associate with an Amazon RDS resource\. The name is referred to as the key\. Supplying a value for the key is optional\. You can use tags to assign arbitrary information to an Amazon RDS resource\. You can use a tag key, for example, to define a category, and the tag value might be an item in that category\. For example, you might define a tag key of "project" and a tag value of "Salix", indicating that the Amazon RDS resource is assigned to the Salix project\. You can also use tags to designate Amazon RDS resources as being used for test or production by using a key such as `environment=test` or `environment=production`\. We recommend that you use a consistent set of tag keys to make it easier to track metadata associated with Amazon RDS resources\. 

Use tags to organize your AWS bill to reflect your own cost structure\. To do this, sign up to get your AWS account bill with tag key values included\. Then, to see the cost of combined resources, organize your billing information according to resources with the same tag key values\. For example, you can tag several resources with a specific application name, and then organize your billing information to see the total cost of that application across several services\. For more information, see [Cost Allocation and Tagging](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/cost-alloc-tags.html) in *About AWS Billing and Cost Management*\.

Each Amazon RDS resource has a tag set, which contains all the tags that are assigned to that Amazon RDS resource\. A tag set can contain as many as 50 tags, or it can be empty\. If you add a tag to an Amazon RDS resource that has the same key as an existing tag on resource, the new value overwrites the old value\. 

AWS does not apply any semantic meaning to your tags; tags are interpreted strictly as character strings\. Amazon RDS can set tags on a DB instance or other Amazon RDS resources, depending on the settings that you use when you create the resource\. For example, Amazon RDS might add a tag indicating that a DB instance is for production or for testing\.
+ The tag key is the required name of the tag\. The string value can be from 1 to 128 Unicode characters in length and cannot be prefixed with "aws:" or "rds:"\. The string can contain only the set of Unicode letters, digits, white\-space, '\_', '\.', ':', '/', '=', '\+', '\-', '@' \(Java regex: "^\(\[\\\\p\{L\}\\\\p\{Z\}\\\\p\{N\}\_\.:/=\+\\\\\-@\]\*\)$"\)\.
+ The tag value is an optional string value of the tag\. The string value can be from 1 to 256 Unicode characters in length and cannot be prefixed with "aws:"\. The string can contain only the set of Unicode letters, digits, white\-space, '\_', '\.', ':', '/', '=', '\+', '\-', '@' \(Java regex: "^\(\[\\\\p\{L\}\\\\p\{Z\}\\\\p\{N\}\_\.:/=\+\\\\\-@\]\*\)$"\)\.

  Values do not have to be unique in a tag set and can be null\. For example, you can have a key\-value pair in a tag set of `project=Trinity` and `cost-center=Trinity`\. 

**Note**  
You can add a tag to a snapshot, however, your bill will not reflect this grouping\.

You can use the AWS Management Console, the command line interface, or the Amazon RDS API to add, list, and delete tags on Amazon RDS resources\. When using the command line interface or the Amazon RDS API, you must provide the Amazon Resource Name \(ARN\) for the Amazon RDS resource you want to work with\. For more information about constructing an ARN, see [Constructing an ARN for Amazon RDS](USER_Tagging.ARN.md#USER_Tagging.ARN.Constructing)\.

Tags are cached for authorization purposes\. Because of this, additions and updates to tags on Amazon RDS resources can take several minutes before they are available\. 

### Copying Tags<a name="USER_Tagging.CopyTags"></a>

When you create or restore a DB instance, you can specify that the tags from the DB instance are copied to snapshots of the DB instance\. Copying tags ensures that the metadata for the DB snapshots matches that of the source DB instance and any access policies for the DB snapshot also match those of the source DB instance\. Tags are not copied by default\. 

You can specify that tags are copied to DB snapshots for the following actions: 
+ Creating a DB instance\.
+ Restoring a DB instance\.
+ Creating a read replica\.
+ Copying a DB snapshot\.

**Note**  
If you include a value for the `--tag-key` parameter of the [create\-db\-snapshot](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-snapshot.html) AWS CLI command \(or supply at least one tag to the [CreateDBSnapshot](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBSnapshot.html) API operation\) then RDS doesn't copy tags from the source DB instance to the new DB snapshot\. This functionality applies even if the source DB instance has the `--copy-tags-to-snapshot` \(`CopyTagsToSnapshot`\) option enabled\. If you take this approach, you can create a copy of a DB instance from a DB snapshot and avoid adding tags that don't apply to the new DB instance\. Once you have created your DB snapshot using the AWS CLI `create-db-snapshot` command \(or the `CreateDBSnapshot` Amazon RDS API operation\) you can then add tags as described later in this topic\.

## Console<a name="USER_Tagging.CON"></a>

The process to tag an Amazon RDS resource is similar for all resources\. The following procedure shows how to tag an Amazon RDS DB instance\. 

**To add a tag to a DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.
**Note**  
To filter the list of DB instances in the **Databases** pane, enter a text string for **Filter databases**\. Only DB instances that contain the string appear\.

1. Choose the name of the DB instance that you want to tag to show its details\. 

1. In the details section, scroll down to the **Tags** section\. 

1. Choose **Add**\. The **Add tags** window appears\.   
![\[Add tags window\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/RDSConsoleTagging5.png)

1. Enter a value for **Tag key** and **Value**\.

1. To add another tag, you can choose **Add another Tag** and enter a value for its **Tag key** and **Value**\. 

   Repeat this step as many times as necessary\.

1. Choose **Add**\. 

**To delete a tag from a DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.
**Note**  
To filter the list of DB instances in the **Databases** pane, enter a text string in the **Filter databases** box\. Only DB instances that contain the string appear\.

1. Choose the name of the DB instance to show its details\. 

1. In the details section, scroll down to the **Tags** section\. 

1. Choose the tag you want to delete\.  
![\[Tags section\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/RDSConsoleTagging6.png)

1. Choose **Delete**, and then choose **Delete** in the **Delete tags** window\. 

## AWS CLI<a name="USER_Tagging.CLI"></a>

You can add, list, or remove tags for a DB instance using the AWS CLI\.
+ To add one or more tags to an Amazon RDS resource, use the AWS CLI command [https://docs.aws.amazon.com/cli/latest/reference/rds/add-tags-to-resource.html](https://docs.aws.amazon.com/cli/latest/reference/rds/add-tags-to-resource.html)\.
+ To list the tags on an Amazon RDS resource, use the AWS CLI command [https://docs.aws.amazon.com/cli/latest/reference/rds/list-tags-for-resource.html](https://docs.aws.amazon.com/cli/latest/reference/rds/list-tags-for-resource.html)\.
+ To remove one or more tags from an Amazon RDS resource, use the AWS CLI command [https://docs.aws.amazon.com/cli/latest/reference/rds/remove-tags-from-resource.html](https://docs.aws.amazon.com/cli/latest/reference/rds/remove-tags-from-resource.html)\.

To learn more about how to construct the required ARN, see [Constructing an ARN for Amazon RDS](USER_Tagging.ARN.md#USER_Tagging.ARN.Constructing)\.

## RDS API<a name="USER_Tagging.API"></a>

You can add, list, or remove tags for a DB instance using the Amazon RDS API\.
+ To add a tag to an Amazon RDS resource, use the [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_AddTagsToResource.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_AddTagsToResource.html) operation\.
+ To list tags that are assigned to an Amazon RDS resource, use the [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ListTagsForResource.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ListTagsForResource.html)\.
+ To remove tags from an Amazon RDS resource, use the [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RemoveTagsFromResource.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RemoveTagsFromResource.html) operation\.

To learn more about how to construct the required ARN, see [Constructing an ARN for Amazon RDS](USER_Tagging.ARN.md#USER_Tagging.ARN.Constructing)\.

When working with XML using the Amazon RDS API, tags use the following schema:

```
 1. <Tagging>
 2.     <TagSet>
 3.         <Tag>
 4.             <Key>Project</Key>
 5.             <Value>Trinity</Value>
 6.         </Tag>
 7.         <Tag>
 8.             <Key>User</Key>
 9.             <Value>Jones</Value>
10.         </Tag>
11.     </TagSet>
12. </Tagging>
```

The following table provides a list of the allowed XML tags and their characteristics\. Values for Key and Value are case\-dependent\. For example, project=Trinity and PROJECT=Trinity are two distinct tags\. 


****  
<a name="user-tag-reference"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_Tagging.html)