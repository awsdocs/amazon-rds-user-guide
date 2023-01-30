# Tagging Amazon RDS resources<a name="USER_Tagging"></a>

You can use Amazon RDS tags to add metadata to your Amazon RDS resources\. You can use the tags to add your own notations about database instances, snapshots, Aurora clusters, and so on\. Doing so can help you to document your Amazon RDS resources\. You can also use the tags with automated maintenance procedures\. 

 In particular, you can use these tags with IAM policies\. You can use them to manage access to RDS resources and to control what actions can be applied to the RDS resources\. You can also use these tags to track costs by grouping expenses for similarly tagged resources\. 

You can tag the following Amazon RDS resources:
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
+ DB subnet groups
+ RDS Proxies
+ RDS Proxy endpoints
+ Blue/green deployments

**Note**  
Currently, you can't tag RDS Proxies and RDS Proxy endpoints by using the AWS Management Console\.

**Topics**
+ [Overview of Amazon RDS resource tags](#Overview.Tagging)
+ [Using tags for access control with IAM](#Tagging.IAM)
+ [Using tags to produce detailed billing reports](#Tagging.Billing)
+ [Adding, listing, and removing tags](#Tagging.HowTo)
+ [Using the AWS Tag Editor](#Tagging.TagEditor)
+ [Copying tags to DB instance snapshots](#USER_Tagging.CopyTags)
+ [Tutorial: Use tags to specify which DB instances to stop](#Tagging.RDS.Autostop)
+ [Using tags to enable backups in AWS Backup](#Tagging.RDS.AWSBackup)

## Overview of Amazon RDS resource tags<a name="Overview.Tagging"></a>

An Amazon RDS tag is a name\-value pair that you define and associate with an Amazon RDS resource\. The name is referred to as the key\. Supplying a value for the key is optional\. You can use tags to assign arbitrary information to an Amazon RDS resource\. You can use a tag key, for example, to define a category, and the tag value might be an item in that category\. For example, you might define a tag key of "project" and a tag value of "Salix"\. In this case, these indicate that the Amazon RDS resource is assigned to the Salix project\. You can also use tags to designate Amazon RDS resources as being used for test or production by using a key such as `environment=test` or `environment=production`\. We recommend that you use a consistent set of tag keys to make it easier to track metadata associated with Amazon RDS resources\. 

In addition, you can use conditions in your IAM policies to control access to AWS resources based on the tags on that resource\. You can do this by using the global `aws:ResourceTag/tag-key` condition key\. For more information, see [Controlling access to AWS resources](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_tags.html#access_tags_control-resources) in the *AWS Identity and Access Management User Guide*\.

Each Amazon RDS resource has a tag set, which contains all the tags that are assigned to that Amazon RDS resource\. A tag set can contain as many as 50 tags, or it can be empty\. If you add a tag to an RDS resource with the same key as an existing resource tag, the new value overwrites the old\. 

AWS doesn't apply any semantic meaning to your tags; tags are interpreted strictly as character strings\. RDS can set tags on a DB instance or other RDS resources\. Tag setting depends on the options that you use when you create the resource\. For example, Amazon RDS might add a tag indicating that a DB instance is for production or for testing\.
+ The tag key is the required name of the tag\. The string value can be from 1 to 128 Unicode characters in length and cannot be prefixed with `aws:` or `rds:`\. The string can contain only the set of Unicode letters, digits, white space, '\_', '\.', ':', '/', '=', '\+', '\-', '@' \(Java regex: "^\(\[\\\\p\{L\}\\\\p\{Z\}\\\\p\{N\}\_\.:/=\+\\\\\-@\]\*\)$"\)\.
+ The tag value is an optional string value of the tag\. The string value can be from 1 to 256 Unicode characters in length\. The string can contain only the set of Unicode letters, digits, white space, '\_', '\.', ':', '/', '=', '\+', '\-', '@' \(Java regex: "^\(\[\\\\p\{L\}\\\\p\{Z\}\\\\p\{N\}\_\.:/=\+\\\\\-@\]\*\)$"\)\.

  Values do not have to be unique in a tag set and can be null\. For example, you can have a key\-value pair in a tag set of `project=Trinity` and `cost-center=Trinity`\. 

You can use the AWS Management Console, the AWS CLI, or the Amazon RDS API to add, list, and delete tags on Amazon RDS resources\. When using the CLI or API, make sure to provide the Amazon Resource Name \(ARN\) for the RDS resource to work with\. For more information about constructing an ARN, see [Constructing an ARN for Amazon RDS](USER_Tagging.ARN.md#USER_Tagging.ARN.Constructing)\.

Tags are cached for authorization purposes\. Because of this, additions and updates to tags on Amazon RDS resources can take several minutes before they are available\. 

## Using tags for access control with IAM<a name="Tagging.IAM"></a>

 You can use tags with IAM policies to manage access to Amazon RDS resources\. You can also use tags to control what actions can be applied to the Amazon RDS resources\. 

For information on managing access to tagged resources with IAM policies, see [Identity and access management for Amazon RDS](UsingWithRDS.IAM.md)\. 

## Using tags to produce detailed billing reports<a name="Tagging.Billing"></a>

 You can also use tags to track costs by grouping expenses for similarly tagged resources\. 

Use tags to organize your AWS bill to reflect your own cost structure\. To do this, sign up to get your AWS account bill with tag key values included\. Then, to see the cost of combined resources, organize your billing information according to resources with the same tag key values\. For example, you can tag several resources with a specific application name, and then organize your billing information to see the total cost of that application across several services\. For more information, see [Using Cost Allocation Tags](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/cost-alloc-tags.html) in the *AWS Billing User Guide*\.

**Note**  
You can add a tag to a snapshot, however, your bill will not reflect this grouping\.

## Adding, listing, and removing tags<a name="Tagging.HowTo"></a>

  The following procedures show how to perform typical tagging operations on resources related to DB instances\. 

### Console<a name="USER_Tagging.CON"></a>

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

### AWS CLI<a name="USER_Tagging.CLI"></a>

You can add, list, or remove tags for a DB instance using the AWS CLI\.
+ To add one or more tags to an Amazon RDS resource, use the AWS CLI command [https://docs.aws.amazon.com/cli/latest/reference/rds/add-tags-to-resource.html](https://docs.aws.amazon.com/cli/latest/reference/rds/add-tags-to-resource.html)\.
+ To list the tags on an Amazon RDS resource, use the AWS CLI command [https://docs.aws.amazon.com/cli/latest/reference/rds/list-tags-for-resource.html](https://docs.aws.amazon.com/cli/latest/reference/rds/list-tags-for-resource.html)\.
+ To remove one or more tags from an Amazon RDS resource, use the AWS CLI command [https://docs.aws.amazon.com/cli/latest/reference/rds/remove-tags-from-resource.html](https://docs.aws.amazon.com/cli/latest/reference/rds/remove-tags-from-resource.html)\.

To learn more about how to construct the required ARN, see [Constructing an ARN for Amazon RDS](USER_Tagging.ARN.md#USER_Tagging.ARN.Constructing)\.

### RDS API<a name="USER_Tagging.API"></a>

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

## Using the AWS Tag Editor<a name="Tagging.TagEditor"></a>

 You can browse and edit the tags on your RDS resources in the AWS Management Console by using the AWS Tag editor\. For more information, see [Tag Editor](https://docs.aws.amazon.com/ARG/latest/userguide/tag-editor.html) in the *AWS Resource Groups User Guide*\. 

## Copying tags to DB instance snapshots<a name="USER_Tagging.CopyTags"></a>

When you create or restore a DB instance, you can specify that the tags from the DB instance are copied to snapshots of the DB instance\. Copying tags ensures that the metadata for the DB snapshots matches that of the source DB instance\. It also ensures that any access policies for the DB snapshots also match those of the source DB instance\.

You can specify that tags are copied to DB snapshots for the following actions: 
+ Creating a DB instance\.
+ Restoring a DB instance\.
+ Creating a read replica\.
+ Copying a DB snapshot\.

In most cases, tags aren't copied by default\. However, when you restore a DB instance from a DB snapshot, RDS checks whether you specify new tags\. If yes, the new tags are added to the restored DB instance\. If there are no new tags, RDS adds the tags from the source DB instance at the time of snapshot creation to the restored DB instance\.

To prevent tags from source DB instances from being added to restored DB instances, we recommend that you specify new tags when restoring a DB instance\.

**Note**  
In some cases, you might include a value for the `--tag-key` parameter of the [create\-db\-snapshot](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-snapshot.html) AWS CLI command\. Or you might supply at least one tag to the [CreateDBSnapshot](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBSnapshot.html) API operation\. In these cases, RDS doesn't copy tags from the source DB instance to the new DB snapshot\. This functionality applies even if the source DB instance has the `--copy-tags-to-snapshot` \(`CopyTagsToSnapshot`\) option turned on\.   
If you take this approach, you can create a copy of a DB instance from a DB snapshot\. This approach avoids adding tags that don't apply to the new DB instance\. You create your DB snapshot using the AWS CLI `create-db-snapshot` command \(or the `CreateDBSnapshot` RDS API operation\)\. After you create your DB snapshot, you can add tags as described later in this topic\.

## Tutorial: Use tags to specify which DB instances to stop<a name="Tagging.RDS.Autostop"></a>

 Suppose that you're creating a number of DB instances in a development or test environment\. You need to keep all of these DB instances for several days\. Some of the DB instances run tests overnight\. Other DB instances can be stopped overnight and started again the next day\. The following example shows how to assign a tag to those DB instances that are suitable to stop overnight\. Then the example shows how a script can detect which DB instances have that tag and then stop those DB instances\. In this example, the value portion of the key\-value pair doesn't matter\. The presence of the `stoppable` tag signifies that the DB instance has this user\-defined property\. 

**To specify which DB instances to stop**

1. Determine the ARN of a DB instance that you want to designate as stoppable\.

   The commands and APIs for tagging work with ARNs\. That way, they can work seamlessly across AWS Regions, AWS accounts, and different types of resources that might have identical short names\. You can specify the ARN instead of the DB instance ID in CLI commands that operate on DB instances\. Substitute the name of your own DB instances for *dev\-test\-db\-instance*\. In subsequent commands that use ARN parameters, substitute the ARN of your own DB instance\. The ARN includes your own AWS account ID and the name of the AWS Region where your DB instance is located\. 

   ```
   $ aws rds describe-db-instances --db-instance-identifier dev-test-db-instance \
     --query "*[].{DBInstance:DBInstanceArn}" --output text
   arn:aws:rds:us-east-1:123456789102:db:dev-test-db-instance
   ```

1. Add the tag `stoppable` to this DB instance\.

   You choose the name for this tag\. This approach means that you can avoid devising a naming convention that encodes all relevant information in names\. In such a convention, you might encode information in the DB instance name or names of other resources\. Because this example treats the tag as an attribute that is either present or absent, it omits the `Value=` part of the `--tags` parameter\. 

   ```
   $ aws rds add-tags-to-resource \
     --resource-name arn:aws:rds:us-east-1:123456789102:db:dev-test-db-instance \
     --tags Key=stoppable
   ```

1. Confirm that the tag is present in the DB instance\.

   These commands retrieve the tag information for the DB instance in JSON format and in plain tab\-separated text\. 

   ```
   $ aws rds list-tags-for-resource \
     --resource-name arn:aws:rds:us-east-1:123456789102:db:dev-test-db-instance 
   {
       "TagList": [
           {
               "Key": "stoppable",
               "Value": ""
   
           }
       ]
   }
   aws rds list-tags-for-resource \
     --resource-name arn:aws:rds:us-east-1:123456789102:db:dev-test-db-instance --output text
   TAGLIST stoppable
   ```

1. To stop all the DB instances that are designated as `stoppable`, prepare a list of all your DB instances\. Loop through the list and check if each DB instance is tagged with the relevant attribute\.

   This Linux example uses shell scripting\. This scripting saves the list of DB instance ARNs to a temporary file and then performs CLI commands for each DB instance\. 

   ```
   $ aws rds describe-db-instances --query "*[].[DBInstanceArn]" --output text >/tmp/db_instance_arns.lst
   $ for arn in $(cat /tmp/db_instance_arns.lst)
   do
     match="$(aws rds list-tags-for-resource --resource-name $arn --output text | grep stoppable)"
     if [[ ! -z "$match" ]]
     then
         echo "DB instance $arn is tagged as stoppable. Stopping it now."
   # Note that you need to get the DB instance identifier from the ARN.
         dbid=$(echo $arn | sed -e 's/.*://')
         aws rds stop-db-instance --db-instance-identifier $dbid
     fi
   done
   
   DB instance arn:arn:aws:rds:us-east-1:123456789102:db:dev-test-db-instance is tagged as stoppable. Stopping it now.
   {
       "DBInstance": {
           "DBInstanceIdentifier": "dev-test-db-instance",
           "DBInstanceClass": "db.t3.medium",
           ...
   ```

 You can run a script like this at the end of each day to make sure that nonessential DB instances are stopped\. You might also schedule a job using a utility such as `cron` to perform such a check each night\. For example, you might do this in case some DB instances were left running by mistake\. Here, you might fine\-tune the command that prepares the list of DB instances to check\. 

The following command produces a list of your DB instances, but only the ones in `available` state\. The script can ignore DB instances that are already stopped, because they will have different status values such as `stopped` or `stopping`\. 

```
$ aws rds describe-db-instances \
  --query '*[].{DBInstanceArn:DBInstanceArn,DBInstanceStatus:DBInstanceStatus}|[?DBInstanceStatus == `available`]|[].{DBInstanceArn:DBInstanceArn}' \
  --output text
arn:aws:rds:us-east-1:123456789102:db:db-instance-2447
arn:aws:rds:us-east-1:123456789102:db:db-instance-3395
arn:aws:rds:us-east-1:123456789102:db:dev-test-db-instance
arn:aws:rds:us-east-1:123456789102:db:pg2-db-instance
```

**Tip**  
You can use assigning tags and finding DB instances with those tags to reduce costs in other ways\. For example, take this scenario with DB instances used for development and testing\. In this case, you might designate some DB instances to be deleted at the end of each day\. Or you might designate them to have their DB instances changed to small DB instance classes during times of expected low usage\. 

## Using tags to enable backups in AWS Backup<a name="Tagging.RDS.AWSBackup"></a>

AWS Backup is a fully managed backup service that makes it easy to centralize and automate the backup of data across AWS services in the cloud and on premises\. You can manage backups of your Amazon RDS DB instances in AWS Backup\.

To enable backups in AWS Backup, you use resource tagging to associate your DB instance with a backup plan\.

This example assumes that you have already created a backup plan in AWS Backup\. You use exactly the same tag for your DB instance that is in your backup plan, as shown in the following figure\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/backup-plan-tag.png)

For more information about AWS Backup, see the [https://docs.aws.amazon.com/aws-backup/latest/devguide](https://docs.aws.amazon.com/aws-backup/latest/devguide)\.

You can assign a tag to a DB instance using the AWS Management Console, the AWS CLI, or the RDS API\. The following examples are for the console and CLI\.

### Console<a name="Tagging.RDS.AWSBackup.Console"></a>

**To assign a tag to a DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose the link for the DB instance to which you want to assign a tag\.

1. On the database details page, choose the **Tags** tab\.

1. Under **Tags**, choose **Add tags**\.

1. Under **Add tags**:

   1. For **Tag key**, enter **BackupPlan**\.

   1. For **Value**, enter **Test**\.

   1. Choose **Add**\.

The result is shown under **Tags**\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/tagged-instance.png)

### CLI<a name="Tagging.RDS.AWSBackup.CLI"></a>

**To assign a tag to a DB instance**
+ Use the following CLI command:

  For Linux, macOS, or Unix:

  ```
  aws rds add-tags-to-resource \
    --resource-name arn:aws:rds:us-east-1:123456789012:db:new-orcl-db \
    --tags Key=BackupPlan,Value=Test
  ```

  For Windows:

  ```
  aws rds add-tags-to-resource ^
    --resource-name arn:aws:rds:us-east-1:123456789012:db:new-orcl-db ^
    --tags Key=BackupPlan,Value=Test
  ```

The `add-tags-to-resource` CLI command returns no output\.

**To confirm that the DB instance is tagged**
+ Use the following CLI command:

  For Linux, macOS, or Unix:

  ```
  aws rds list-tags-for-resource \
    --resource-name arn:aws:rds:us-east-1:123456789012:db:new-orcl-db
  ```

  For Windows:

  ```
  aws rds list-tags-for-resource ^
    --resource-name arn:aws:rds:us-east-1:123456789012:db:new-orcl-db
  ```

The `list-tags-for-resource` CLI command returns the following output:

```
{
    "TagList": [
        {
            "Key": "BackupPlan",
            "Value": "Test"
        }
    ]
}
```