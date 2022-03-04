# Comparing DB parameter groups<a name="USER_WorkingWithParamGroups.Comparing"></a>

You can use the AWS Management Console to view the differences between two parameter groups for the same DB engine and version\.

**To compare two parameter groups**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Parameter groups**\.

1. In the list, choose the two parameter groups that you want to compare\.

1. For **Parameter group actions**, choose **Compare**\.
**Note**  
If the items you selected aren't equivalent, you can't choose **Compare**\. For example, you can't compare a MySQL 5\.6 and a MySQL 5\.7 parameter group\. You can't compare a DB parameter group and an Aurora DB cluster parameter group\.