# Comparing parameter groups<a name="USER_WorkingWithParamGroups.Comparing"></a>

You can use the AWS Management Console to view the differences between two parameter groups\.

**To compare two parameter groups**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Parameter groups**\.

1. In the list, choose the two parameter groups that you want to compare\.

1. For **Parameter group actions**, choose **Compare**\.
**Note**  
The specified parameter groups must both be DB parameter groups, or they both must be DB cluster parameter groups\. This is true even when the DB engine and version are the same\. For example, you can't compare an Aurora MySQL 8\.0 DB parameter group and an Aurora MySQL 8\.0 DB cluster parameter group\.  
You can compare Aurora MySQL and RDS for MySQL DB parameter groups, even for different versions, but you can't compare Aurora PostgreSQL and RDS for PostgreSQL DB parameter groups\.