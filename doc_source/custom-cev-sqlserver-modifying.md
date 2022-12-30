# Modifying a CEV for RDS Custom for SQL Server<a name="custom-cev-sqlserver-modifying"></a>

You can modify a CEV using the AWS Management Console or the AWS CLI\. You can modify the CEV description or its availability status\. Your CEV has one of the following status values:
+ `available` – You can use this CEV to create a new RDS Custom DB instance or upgrade a DB instance\. This is the default status for a newly created CEV\.
+ `inactive` – You can't create or upgrade an RDS Custom DB instance with this CEV\. You can't restore a DB snapshot to create a new RDS Custom DB instance with this CEV\.

You can change the CEV status from `available` to `inactive` or from `inactive` to `available`\. You might change the status to `INACTIVE` to prevent the accidental use of a CEV or to make a discontinued CEV eligible for use again\.

## Console<a name="custom-cev-sqlserver-modifying.console"></a>

**To modify a CEV**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Custom engine versions**\.

1. Choose a CEV whose description or status you want to modify\.

1. For **Actions**, choose **Modify**\.

1. Make any of the following changes:
   + For **CEV status settings**, choose a new availability status\.
   + For **Version description**, enter a new description\.

1. Choose **Modify CEV**\.

   If the CEV is in use, the console displays **You can't modify the CEV status**\. Fix the problems, then try again\.

The **Custom engine versions** page appears\.

## AWS CLI<a name="custom-cev-sqlserver-modifying.cli"></a>

To modify a CEV by using the AWS CLI, run the [modify\-custom\-db\-engine\-version](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-custom-db-engine-version.html) command\. You can find CEVs to modify by running the [describe\-db\-engine\-versions](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-engine-versions.html) command\.

The following options are required:
+ `--engine`
+ `--engine-version cev`, where *`cev`* is the name of the custom engine version that you want to modify
+ `--status`` status`, where *`status`* is the availability status that you want to assign to the CEV

The following example changes a CEV named `15.00.4249.2.my_cevtest` from its current status to `inactive`\.

**Example**  
For Linux, macOS, or Unix:  

```
1. aws rds modify-custom-db-engine-version \
2.     --engine custom-sqlserver-ee \ 
3.     --engine-version 15.00.4249.2.my_cevtest \
4.     --status inactive
```
For Windows:  

```
1. aws rds modify-custom-db-engine-version ^
2.     --engine custom-sqlserver-ee ^
3.     --engine-version 15.00.4249.2.my_cevtest ^
4.     --status inactive
```

## Modifying an RDS Custom for SQL Server DB instance to use a new CEV<a name="custom-cev-sqlserver-modifying-dbinstance"></a>

You can modify an existing RDS Custom for SQL Server DB instance to use a different CEV\. The changes that you can make include:
+ Changing the CEV
+ Changing the DB instance class
+ Changing the backup retention period and backup window
+ Changing the maintenance window

### Console<a name="custom-cev-sqlserver-modifying-dbinstance.CON"></a>

**To modify an RDS Custom for SQL Server DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose the DB instance that you want to modify\.

1. Choose **Modify**\.

1. Make the following changes as needed:

   1. For **DB engine version**, choose a different CEV\.

   1. Change the value for **DB instance class**\. For supported classes, see [DB instance class support for RDS Custom for SQL Server](custom-reqs-limits-MS.md#custom-reqs-limits.instancesMS)\.

   1. Change the value for **Backup retention period**\.

   1. For **Backup window**, set values for the **Start time** and **Duration**\.

   1. For **DB instance maintenance window**, set values for the **Start day**, **Start time**, and **Duration**\.

1. Choose **Continue**\.

1. Choose **Apply immediately** or **Apply during the next scheduled maintenance window**\. 

1. Choose **Modify DB instance**\.
**Note**  
When modifying a DB instance from one CEV to an another CEV, for example, when upgrading a minor version, the SQL Server system databases, including their data and configurations, are persisted from the current RDS Custom for SQL Server DB instance\.

### AWS CLI<a name="custom-cev-sqlserver-modifying-dbinstance.CLI"></a>

To modify a DB instance to use a different CEV by using the AWS CLI, run the [modify\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-custom-db-engine-version.html) command\.

The following options are required:
+ `--db-instance-identifier`
+ `--engine-version cev`, where *`cev`* is the name of the custom engine version that you want the DB instance to change to\.

The following example modifies a DB instance named `my-cev-db-instance` to use a CEV named `15.00.4249.2.my_cevtest_new` and applies the change immediately\.

**Example**  
For Linux, macOS, or Unix:  

```
1. aws rds modify-db-instance \
2.     --db-instance-identifier my-cev-db-instance \ 
3.     --engine-version 15.00.4249.2.my_cevtest_new \
4.     --apply-immediately
```
For Windows:  

```
1. aws rds modify-db-instance ^
2.     --db-instance-identifier my-cev-db-instance ^
3.     --engine-version 15.00.4249.2.my_cevtest_new ^
4.     --apply-immediately
```