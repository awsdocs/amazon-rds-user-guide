# Modifying CEV status<a name="custom-cev.modify"></a>

You can modify a CEV using the AWS Management Console or the AWS CLI\. You can modify the CEV description or its availability status\. Your CEV has one of the following status values:
+ `available` – You can use this CEV to create a new RDS Custom DB instance or upgrade a DB instance\. This is the default status for a newly created CEV\.
+ `inactive` – You can't create or upgrade an RDS Custom instance with this CEV\. You can't restore a DB snapshot to create a new RDS Custom DB instance with this CEV\.

You can change the CEV from any supported status to any other supported status\. You might change status to prevent the accidental use of a CEV or make a discontinued CEV eligible for use again\. For example, you might change the status of your CEV from `available` to `inactive`, and from `inactive` back to `available`\.

## Console<a name="custom-cev.modify.console"></a>

**To modify a CEV**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Custom engine versions**\.

1. Choose a CEV whose description or status you want to modify\.

1. For **Actions**, choose **Modify**\.

1. Make any of the following changes:
   + For **CEV status settings**, choose a new availability status\.
   + For **Version description**, enter a new description\.

1. Choose **Modify CEV**\.

   If the CEV is in use, the console displays **You can't modify the CEV status**\. Fix the problems, and try again\.

The **Custom engine versions** page appears\.

## AWS CLI<a name="custom-cev.modify.cli"></a>

To modify a CEV by using the AWS CLI, run the [modify\-custom\-db\-engine\-version](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-custom-db-engine-version.html) command\. You can find CEVs to modify by running the [describe\-db\-engine\-versions](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-engine-versions.html) command\.

The following options are required:
+ `--engine custom-oracle-ee`
+ `--engine-version cev`, where *`cev`* is the name of the custom engine version that you want to modify
+ `--status`` status`, where *`status`* is the availability status that you want to assign to the CEV

The following example changes a CEV named `19.my_cev1` from its current status to `inactive`\.

**Example**  
For Linux, macOS, or Unix:  

```
1. aws rds modify-custom-db-engine-version \
2.     --engine custom-oracle-ee \ 
3.     --engine-version 19.my_cev1 \
4.     --status inactive
```
For Windows:  

```
1. aws rds modify-custom-db-engine-version ^
2.     --engine custom-oracle-ee ^
3.     --engine-version 19.my_cev1 ^
4.     --status inactive
```