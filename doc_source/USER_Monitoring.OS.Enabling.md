# Setting up and enabling Enhanced Monitoring<a name="USER_Monitoring.OS.Enabling"></a>

To use Enhanced Monitoring, you must create an IAM role, and then enable Enhanced Monitoring\.

## Creating an IAM role for Enhanced Monitoring<a name="USER_Monitoring.OS.Enabling.Prerequisites"></a>

Enhanced Monitoring requires permission to act on your behalf to send OS metric information to CloudWatch Logs\. You grant Enhanced Monitoring permissions using an AWS Identity and Access Management \(IAM\) role\. 

### Creating the IAM role when you enable Enhanced Monitoring<a name="USER_Monitoring.OS.Enabling.Prerequisites.creating-role-automatically"></a>

When you enable Enhanced Monitoring in the RDS console, Amazon RDS can create the required IAM role for you\. The role is named `rds-monitoring-role`\. RDS uses this role for the specified DB instance or read replica\.

**To create the IAM role when enabling Enhanced Monitoring**

1. Follow the steps in [Enabling and disabling Enhanced Monitoring](#USER_Monitoring.OS.Enabling.Procedure)\.

1. Set **Monitoring Role** to **Default** in the step where you choose a role\.

### Creating the IAM role before you enable Enhanced Monitoring<a name="USER_Monitoring.OS.Enabling.Prerequisites.creating-role-manually"></a>

You can create the required role before you enable Enhanced Monitoring\. When you enable Enhanced Monitoring, specify your new role's name\. You must create this required role if you enable Enhanced Monitoring using the AWS CLI or the RDS API\.

The user that enables Enhanced Monitoring must be granted the `PassRole` permission\. For more information, see Example 2 in [Granting a user permissions to pass a role to an AWS service](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_use_passrole.html) in the *IAM User Guide*\.<a name="USER_Monitoring.OS.IAMRole"></a>

**To create an IAM role for Amazon RDS enhanced monitoring in the console**

1. Open the [IAM console](https://console.aws.amazon.com/iam/home?#home) at [https://console\.aws\.amazon\.com](https://console.aws.amazon.com/)\.

1. In the navigation pane, choose **Roles**\.

1. Choose **Create role**\.

1. Choose the **AWS service** tab, and then choose **RDS** from the list of services\.

1. Choose **RDS \- Enhanced Monitoring**, and then choose **Next: Permissions**\.

1. Ensure that the **Attached permissions policy** page shows **AmazonRDSEnhancedMonitoringRole**, and then choose **Next: Tags**\.

1. On the **Add tags** page, choose **Next: Review**\.

1. For **Role Name**, enter a name for your role\. For example, enter **emaccess**\.

   The trusted entity for your role is the AWS service **monitoring\.rds\.amazonaws\.com**\.

1. Choose **Create role**\.

**To create an IAM role for Amazon RDS enhanced monitoring programmatically**

To create the IAM role programmatically, the role must allow the `monitoring.rds.amazonaws.com` service principal to assume the role in the trust relationship:

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": "monitoring.rds.amazonaws.com"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```

The role should have the managed policy ARN `arn:aws:iam::aws:policy/service-role/AmazonRDSEnhancedMonitoringRole` attached to it, or permissions equivalent to those expressed in the policy. If you are using the GovCloud US or China partition, replase `arn:aws:` with `awn:aws-us-gov:` or `arn:aws-cn:`, respectively.

## Enabling and disabling Enhanced Monitoring<a name="USER_Monitoring.OS.Enabling.Procedure"></a>

You can enable and disable Enhanced Monitoring using the AWS Management Console, AWS CLI, or RDS API\.

### Console<a name="USER_Monitoring.OS.Enabling.Procedure.Console"></a>

You can enable Enhanced Monitoring when you create a DB instance or read replica, or when you modify a DB instance\. If you modify a DB instance to enable Enhanced Monitoring, you don't need to reboot your DB instance for the change to take effect\. 

You can enable Enhanced Monitoring in the RDS console when you do one of the following actions: 
+ **Create a DB instance** – You can enable Enhanced Monitoring in the **Monitoring** section under **Additional configuration**\.
+ **Create a read replica** – You can enable Enhanced Monitoring in the **Monitoring** section\.
+ **Modify a DB instance** – You can enable Enhanced Monitoring in the **Monitoring** section\.

**To enable Enhanced Monitoring by using the RDS console**

1. Scroll to the **Monitoring** section\.

1. Choose **Enable enhanced monitoring** for your DB instance or read replica\. To disable Enhanced Monitoring, choose **Disable enhanced monitoring**\.  
![\[Enable Enhanced Monitoring\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/metrics3.png)

1. Set the **Monitoring Role** property to the IAM role that you created to permit Amazon RDS to communicate with Amazon CloudWatch Logs for you, or choose **Default** to have RDS create a role for you named `rds-monitoring-role`\.

1. Set the **Granularity** property to the interval, in seconds, between points when metrics are collected for your DB instance or read replica\. The **Granularity** property can be set to one of the following values: `1`, `5`, `10`, `15`, `30`, or `60`\.

**Note**  
The fastest that the RDS console refreshes is every 5 seconds\. If you set the granularity to 1 second in the RDS console, you still see updated metrics only every 5 seconds\. You can retrieve 1\-second metric updates by using CloudWatch Logs\.

### AWS CLI<a name="USER_Monitoring.OS.Enabling.Procedure.CLI"></a>

To enable Enhanced Monitoring using the AWS CLI, in the following commands, set the `--monitoring-interval` option to a value other than `0` and set the `--monitoring-role-arn` option to the role you created in [Creating an IAM role for Enhanced Monitoring](#USER_Monitoring.OS.Enabling.Prerequisites)\.
+ [create\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html)
+ [create\-db\-instance\-read\-replica](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance-read-replica.html)
+ [modify\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html)

The `--monitoring-interval` option specifies the interval, in seconds, between points when Enhanced Monitoring metrics are collected\. Valid values for the option are `0`, `1`, `5`, `10`, `15`, `30`, and `60`\.

To disable Enhanced Monitoring using the AWS CLI, set the `--monitoring-interval` option to `0` in the these commands\.

**Example**  
The following example enables Enhanced Monitoring for a DB instance:  
For Linux, macOS, or Unix:  

```
aws rds modify-db-instance \
    --db-instance-identifier mydbinstance \
    --monitoring-interval 30 \
    --monitoring-role-arn arn:aws:iam::123456789012:role/emaccess
```
For Windows:  

```
aws rds modify-db-instance ^
    --db-instance-identifier mydbinstance ^
    --monitoring-interval 30 ^
    --monitoring-role-arn arn:aws:iam::123456789012:role/emaccess
```

### RDS API<a name="USER_Monitoring.OS.Enabling.Procedure.API"></a>

To enable Enhanced Monitoring using the RDS API, set the `MonitoringInterval` parameter to a value other than `0` and set the `MonitoringRoleArn` parameter to the role you created in [Creating an IAM role for Enhanced Monitoring](#USER_Monitoring.OS.Enabling.Prerequisites)\. Set these parameters in the following actions:
+ [CreateDBInstance](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html)
+ [CreateDBInstanceReadReplica](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstanceReadReplica.html)
+ [ModifyDBInstance](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html)

The `MonitoringInterval` parameter specifies the interval, in seconds, between points when Enhanced Monitoring metrics are collected\. Valid values are `0`, `1`, `5`, `10`, `15`, `30`, and `60`\.

To disable Enhanced Monitoring using the RDS API, set `MonitoringInterval` to `0`\.
