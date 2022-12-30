# Deleting a CEV for RDS Custom for SQL Server<a name="custom-cev-sqlserver-deleting"></a>

You can delete a CEV using the AWS Management Console or the AWS CLI\. Typically, this task takes a few minutes\.

Before deleting a CEV, make sure it isn't being used by any of the following:
+ An RDS Custom DB instance
+ A snapshot of an RDS Custom DB instance
+ An automated backup of your RDS Custom DB instance

## Console<a name="custom-cev-sqlserver-deleting.console"></a>

**To delete a CEV**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Custom engine versions**\.

1. Choose a CEV whose description or status you want to delete\.

1. For **Actions**, choose **Delete**\.

   The **Delete *cev\_name*?** dialog box appears\.

1. Enter **delete me**, and then choose **Delete**\.

   In the **Custom engine versions** page, the banner shows that your CEV is being deleted\.

## AWS CLI<a name="custom-cev-sqlserver-deleting.console.cli"></a>

To delete a CEV by using the AWS CLI, run the [delete\-custom\-db\-engine\-version](https://docs.aws.amazon.com/cli/latest/reference/rds/delete-custom-db-engine-version.html) command\.

The following options are required:
+ `--engine custom-sqlserver-ee`
+ `--engine-version cev`, where *cev* is the name of the custom engine version to be deleted

The following example deletes a CEV named `15.00.4249.2.my_cevtest`\.

**Example**  
For Linux, macOS, or Unix:  

```
1. aws rds delete-custom-db-engine-version \
2.     --engine custom-sqlserver-ee \
3.     --engine-version 15.00.4249.2.my_cevtest
```
For Windows:  

```
1. aws rds delete-custom-db-engine-version ^
2.     --engine custom-sqlserver-ee ^
3.     --engine-version 15.00.4249.2.my_cevtest
```