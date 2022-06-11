# Setting up and enabling Enhanced Monitoring<a name="USER_Monitoring.OS.Enabling"></a>

To use Enhanced Monitoring, you must create an IAM role, and then enable Enhanced Monitoring\.

**Topics**
+ [Creating an IAM role for Enhanced Monitoring](#USER_Monitoring.OS.Enabling.Prerequisites)
+ [Turning Enhanced Monitoring on and off](#USER_Monitoring.OS.Enabling.Procedure)
+ [Protecting against the confused deputy problem](#USER_Monitoring.OS.confused-deputy)

## Creating an IAM role for Enhanced Monitoring<a name="USER_Monitoring.OS.Enabling.Prerequisites"></a>

Enhanced Monitoring requires permission to act on your behalf to send OS metric information to CloudWatch Logs\. You grant Enhanced Monitoring permissions using an AWS Identity and Access Management \(IAM\) role\. You can either create this role when you enable Enhanced Monitoring or create it beforehand\.

**Topics**
+ [Creating the IAM role when you enable Enhanced Monitoring](#USER_Monitoring.OS.Enabling.Prerequisites.creating-role-automatically)
+ [Creating the IAM role before you enable Enhanced Monitoring](#USER_Monitoring.OS.Enabling.Prerequisites.creating-role-manually)

### Creating the IAM role when you enable Enhanced Monitoring<a name="USER_Monitoring.OS.Enabling.Prerequisites.creating-role-automatically"></a>

When you enable Enhanced Monitoring in the RDS console, Amazon RDS can create the required IAM role for you\. The role is named `rds-monitoring-role`\. RDS uses this role for the specified DB instance, read replica, or Multi\-AZ DB cluster\.

**To create the IAM role when enabling Enhanced Monitoring**

1. Follow the steps in [Turning Enhanced Monitoring on and off](#USER_Monitoring.OS.Enabling.Procedure)\.

1. Set **Monitoring Role** to **Default** in the step where you choose a role\.

### Creating the IAM role before you enable Enhanced Monitoring<a name="USER_Monitoring.OS.Enabling.Prerequisites.creating-role-manually"></a>

You can create the required role before you enable Enhanced Monitoring\. When you enable Enhanced Monitoring, specify your new role's name\. You must create this required role if you enable Enhanced Monitoring using the AWS CLI or the RDS API\.

The user that enables Enhanced Monitoring must be granted the `PassRole` permission\. For more information, see Example 2 in [Granting a user permissions to pass a role to an AWS service](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_use_passrole.html) in the *IAM User Guide*\.<a name="USER_Monitoring.OS.IAMRole"></a>

**To create an IAM role for Amazon RDS enhanced monitoring**

1. Open the [IAM console](https://console.aws.amazon.com/iam/home?#home) at [https://console\.aws\.amazon\.com](https://console.aws.amazon.com/)\.

1. In the navigation pane, choose **Roles**\.

1. Choose **Create role**\.

1. Choose the **AWS service** tab, and then choose **RDS** from the list of services\.

1. Choose **RDS \- Enhanced Monitoring**, and then choose **Next**\.

1. Ensure that the **Permissions policies** shows **AmazonRDSEnhancedMonitoringRole**, and then choose **Next**\.

1. For **Role name**, enter a name for your role\. For example, enter **emaccess**\.

   The trusted entity for your role is the AWS service **monitoring\.rds\.amazonaws\.com**\.

1. Choose **Create role**\.

## Turning Enhanced Monitoring on and off<a name="USER_Monitoring.OS.Enabling.Procedure"></a>

You can turn Enhanced Monitoring on and off using the AWS Management Console, AWS CLI, or RDS API\. You choose the RDS DB instances on which you want to turn on Enhanced Monitoring\. You can set different granularities for metric collection on each DB instance\.

### Console<a name="USER_Monitoring.OS.Enabling.Procedure.Console"></a>

You can turn on Enhanced Monitoring when you create a DB instance, Multi\-AZ DB cluster, or read replica, or when you modify a DB instance or Multi\-AZ DB cluster\. If you modify a DB instance to turn on Enhanced Monitoring, you don't need to reboot your DB instance for the change to take effect\. 

You can turn on Enhanced Monitoring in the RDS console when you do one of the following actions in the **Databases** page: 
+ **Create a DB instance or Multi\-AZ DB cluster** – Choose **Create database**\.
+ **Create a read replica** – Choose **Actions**, then **Create read replica**\.
+ **Modify a DB instance or Multi\-AZ DB cluster** – Choose **Modify**\.

**To turn Enhanced Monitoring on or off in the RDS console**

1. Scroll to **Additional configuration**\.

1. In **Monitoring**, choose **Enable Enhanced Monitoring** for your DB instance or read replica\. To turn Enhanced Monitoring off, choose **Disable Enhanced Monitoring**\.

1. Set the **Monitoring Role** property to the IAM role that you created to permit Amazon RDS to communicate with Amazon CloudWatch Logs for you, or choose **Default** to have RDS create a role for you named `rds-monitoring-role`\.

1. Set the **Granularity** property to the interval, in seconds, between points when metrics are collected for your DB instance or read replica\. The **Granularity** property can be set to one of the following values: `1`, `5`, `10`, `15`, `30`, or `60`\.

   The fastest that the RDS console refreshes is every 5 seconds\. If you set the granularity to 1 second in the RDS console, you still see updated metrics only every 5 seconds\. You can retrieve 1\-second metric updates by using CloudWatch Logs\.

### AWS CLI<a name="USER_Monitoring.OS.Enabling.Procedure.CLI"></a>

To turn on Enhanced Monitoring using the AWS CLI, in the following commands, set the `--monitoring-interval` option to a value other than `0` and set the `--monitoring-role-arn` option to the role you created in [Creating an IAM role for Enhanced Monitoring](#USER_Monitoring.OS.Enabling.Prerequisites)\.
+ [create\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html)
+ [create\-db\-instance\-read\-replica](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance-read-replica.html)
+ [modify\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html)
+ [create\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html) \(Multi\-AZ DB cluster\)
+ [modify\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html) \(Multi\-AZ DB cluster\)

The `--monitoring-interval` option specifies the interval, in seconds, between points when Enhanced Monitoring metrics are collected\. Valid values for the option are `0`, `1`, `5`, `10`, `15`, `30`, and `60`\.

To turn off Enhanced Monitoring using the AWS CLI, set the `--monitoring-interval` option to `0` in these commands\.

**Example**  
The following example turns on Enhanced Monitoring for a DB instance:  
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

**Example**  
The following example turns on Enhanced Monitoring for a Multi\-AZ DB cluster:  
For Linux, macOS, or Unix:  

```
aws rds modify-db-cluster \
    --db-cluster-identifier mydbcluster \
    --monitoring-interval 30 \
    --monitoring-role-arn arn:aws:iam::123456789012:role/emaccess
```
For Windows:  

```
aws rds modify-db-cluster ^
    --db-cluster-identifier mydbcluster ^
    --monitoring-interval 30 ^
    --monitoring-role-arn arn:aws:iam::123456789012:role/emaccess
```

### RDS API<a name="USER_Monitoring.OS.Enabling.Procedure.API"></a>

To turn on Enhanced Monitoring using the RDS API, set the `MonitoringInterval` parameter to a value other than `0` and set the `MonitoringRoleArn` parameter to the role you created in [Creating an IAM role for Enhanced Monitoring](#USER_Monitoring.OS.Enabling.Prerequisites)\. Set these parameters in the following actions:
+ [CreateDBInstance](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html)
+ [CreateDBInstanceReadReplica](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstanceReadReplica.html)
+ [ModifyDBInstance](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html)
+ [CreateDBCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html) \(Multi\-AZ DB cluster\)
+ [ModifyDBCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html) \(Multi\-AZ DB cluster\)

The `MonitoringInterval` parameter specifies the interval, in seconds, between points when Enhanced Monitoring metrics are collected\. Valid values are `0`, `1`, `5`, `10`, `15`, `30`, and `60`\.

To turn off Enhanced Monitoring using the RDS API, set `MonitoringInterval` to `0`\.

## Protecting against the confused deputy problem<a name="USER_Monitoring.OS.confused-deputy"></a>

The confused deputy problem is a security issue where an entity that doesn't have permission to perform an action can coerce a more\-privileged entity to perform the action\. In AWS, cross\-service impersonation can result in the confused deputy problem\. Cross\-service impersonation can occur when one service \(the *calling service*\) calls another service \(the *called service*\)\. The calling service can be manipulated to use its permissions to act on another customer's resources in a way it should not otherwise have permission to access\. To prevent this, AWS provides tools that help you protect your data for all services with service principals that have been given access to resources in your account\. For more information, see [The confused deputy problem](https://docs.aws.amazon.com/IAM/latest/UserGuide/confused-deputy.html)\.

To limit the permissions to the resource that Amazon RDS can give another service, we recommend using the `aws:SourceArn` and `aws:SourceAccount` global condition context keys in a trust policy for your Enhanced Monitoring role\. If you use both global condition context keys, they must use the same account ID\.

The most effective way to protect against the confused deputy problem is to use the `aws:SourceArn` global condition context key with the full ARN of the resource\. For Amazon RDS, set `aws:SourceArn` to `arn:aws:rds:Region:my-account-id:db:dbname`\.

The following example uses the `aws:SourceArn` and `aws:SourceAccount` global condition context keys in a trust policy to prevent the confused deputy problem\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": "rds.amazonaws.com"
      },
      "Action": "sts:AssumeRole",
      "Condition": {
        "StringLike": {
          "aws:SourceArn": "arn:aws:rds:Region:my-account-id:db:dbname"
        },
        "StringEquals": {
          "aws:SourceAccount": "my-account-id"
        }
      }
    }
  ]
}
```