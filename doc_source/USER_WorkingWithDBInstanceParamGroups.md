# Working with DB parameter groups<a name="USER_WorkingWithDBInstanceParamGroups"></a>

DB instances use DB parameter groups\. The following sections describe configuring and managing DB instance parameter groups\.

**Topics**
+ [Creating a DB parameter group](#USER_WorkingWithParamGroups.Creating)
+ [Associating a DB parameter group with a DB instance](#USER_WorkingWithParamGroups.Associating)
+ [Modifying parameters in a DB parameter group](#USER_WorkingWithParamGroups.Modifying)
+ [Resetting parameters in a DB parameter group to their default values](#USER_WorkingWithParamGroups.Resetting)
+ [Copying a DB parameter group](#USER_WorkingWithParamGroups.Copying)
+ [Listing DB parameter groups](#USER_WorkingWithParamGroups.Listing)
+ [Viewing parameter values for a DB parameter group](#USER_WorkingWithParamGroups.Viewing)

## Creating a DB parameter group<a name="USER_WorkingWithParamGroups.Creating"></a>

You can create a new DB parameter group using the AWS Management Console, the AWS CLI, or the RDS API\.

The following limitations apply to the DB parameter group name:
+ The name must be 1 to 255 letters, numbers, or hyphens\.

  Default parameter group names can include a period, such as `default.mysql8.0`\. However, custom parameter group names can't include a period\.
+ The first character must be a letter\.
+ The name can't end with a hyphen or contain two consecutive hyphens\.

### Console<a name="USER_WorkingWithParamGroups.Creating.CON"></a>

**To create a DB parameter group**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Parameter groups**\.

1. Choose **Create parameter group**\.

   The **Create parameter group** window appears\.

1. In the **Parameter group family** list, select a DB parameter group family\.

1. In the **Type** list, select **DB Parameter Group**\.

1. In the **Group name** box, enter the name of the new DB parameter group\.

1. In the **Description** box, enter a description for the new DB parameter group\. 

1. Choose **Create**\.

### AWS CLI<a name="USER_WorkingWithParamGroups.Creating.CLI"></a>

To create a DB parameter group, use the AWS CLI [https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-parameter-group.html](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-parameter-group.html) command\. The following example creates a DB parameter group named *mydbparametergroup* for MySQL version 8\.0 with a description of "*My new parameter group*\."

Include the following required parameters:
+ `--db-parameter-group-name`
+ `--db-parameter-group-family`
+ `--description`

To list all of the available parameter group families, use the following command:

```
aws rds describe-db-engine-versions --query "DBEngineVersions[].DBParameterGroupFamily"
```

**Note**  
The output contains duplicates\.

**Example**  
For Linux, macOS, or Unix:  

```
aws rds create-db-parameter-group \
    --db-parameter-group-name mydbparametergroup \
    --db-parameter-group-family MySQL8.0 \
    --description "My new parameter group"
```
For Windows:  

```
aws rds create-db-parameter-group ^
    --db-parameter-group-name mydbparametergroup ^
    --db-parameter-group-family MySQL8.0 ^
    --description "My new parameter group"
```
This command produces output similar to the following:  

```
DBPARAMETERGROUP  mydbparametergroup  mysql8.0  My new parameter group
```

### RDS API<a name="USER_WorkingWithParamGroups.Creating.API"></a>

To create a DB parameter group, use the RDS API [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBParameterGroup.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBParameterGroup.html) operation\.

Include the following required parameters:
+ `DBParameterGroupName`
+ `DBParameterGroupFamily`
+ `Description`

## Associating a DB parameter group with a DB instance<a name="USER_WorkingWithParamGroups.Associating"></a>

You can create your own DB parameter groups with customized settings\. You can associate a DB parameter group with a DB instance using the AWS Management Console, the AWS CLI, or the RDS API\. You can do so when you create or modify a DB instance\.

For information about creating a DB parameter group, see [Creating a DB parameter group](#USER_WorkingWithParamGroups.Creating)\. For information about creating a DB instance, see [Creating an Amazon RDS DB instance](USER_CreateDBInstance.md)\.  For information about modifying a DB instance, see [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)\.

**Note**  
When you associate a new DB parameter group with a DB instance, the modified static and dynamic parameters are applied only after the DB instance is rebooted\. However, if you modify dynamic parameters in the DB parameter group after you associate it with the DB instance, these changes are applied immediately without a reboot\.

### Console<a name="USER_WorkingWithParamGroups.Associating.CON"></a>

**To associate a DB parameter group with a DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**, and then choose the DB instance that you want to modify\. 

1. Choose **Modify**\. The **Modify DB Instance** page appears\.

1. Change the **DB parameter group** setting\. 

1. Choose **Continue** and check the summary of modifications\. 

1. \(Optional\) Choose **Apply immediately** to apply the changes immediately\. Choosing this option can cause an outage in some cases\. For more information, see [Using the Apply Immediately setting](Overview.DBInstance.Modifying.md#USER_ModifyInstance.ApplyImmediately)\.

1. On the confirmation page, review your changes\. If they are correct, choose **Modify DB instance** to save your changes\. 

   Or choose **Back** to edit your changes or **Cancel** to cancel your changes\. 

### AWS CLI<a name="USER_WorkingWithParamGroups.Associating.CLI"></a>

To associate a DB parameter group with a DB instance, use the AWS CLI [https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) command with the following options:
+ `--db-instance-identifier`
+ `--db-parameter-group-name`

The following example associates the `mydbpg` DB parameter group with the `database-1` DB instance\. The changes are applied immediately by using `--apply-immediately`\. Use `--no-apply-immediately` to apply the changes during the next maintenance window\. For more information, see [Using the Apply Immediately setting](Overview.DBInstance.Modifying.md#USER_ModifyInstance.ApplyImmediately)\.

**Example**  
For Linux, macOS, or Unix:  

```
aws rds modify-db-instance \
    --db-instance-identifier database-1 \
    --db-parameter-group-name mydbpg \
    --apply-immediately
```
For Windows:  

```
aws rds modify-db-instance ^
    --db-instance-identifier database-1 ^
    --db-parameter-group-name mydbpg ^
    --apply-immediately
```

### RDS API<a name="USER_WorkingWithParamGroups.Associating.API"></a>

To associate a DB parameter group with a DB instance, use the RDS API [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html) operation with the following parameters:
+ `DBInstanceName`
+ `DBParameterGroupName`

## Modifying parameters in a DB parameter group<a name="USER_WorkingWithParamGroups.Modifying"></a>

You can modify parameter values in a customer\-created DB parameter group; you can't change the parameter values in a default DB parameter group\. Changes to parameters in a customer\-created DB parameter group are applied to all DB instances that are associated with the DB parameter group\. 

Changes to some parameters are applied to the DB instance immediately without a reboot\. Changes to other parameters are applied only after the DB instance is rebooted\. The RDS console shows the status of the DB parameter group associated with a DB instance on the **Configuration** tab\. For example, suppose that the DB instance isn't using the latest changes to its associated DB parameter group\. If so, the RDS console shows the DB parameter group with a status of **pending\-reboot**\. To apply the latest parameter changes to that DB instance, manually reboot the DB instance\.

![\[Parameter change pending reboot scenario\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/param-reboot.png)

### Console<a name="USER_WorkingWithParamGroups.Modifying.CON"></a>

**To modify a DB parameter group**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Parameter groups**\.

1. In the list, choose the parameter group that you want to modify\.

1. For **Parameter group actions**, choose **Edit**\.

1. Change the values of the parameters that you want to modify\. You can scroll through the parameters using the arrow keys at the top right of the dialog box\. 

   You can't change values in a default parameter group\.

1. Choose **Save changes**\.

### AWS CLI<a name="USER_WorkingWithParamGroups.Modifying.CLI"></a>

To modify a DB parameter group, use the AWS CLI [https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-parameter-group.html](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-parameter-group.html) command with the following required options:
+ `--db-parameter-group-name`
+ `--parameters`

The following example modifies the` max_connections` and `max_allowed_packet` values in the DB parameter group named *mydbparametergroup*\.

**Example**  
For Linux, macOS, or Unix:  

```
aws rds modify-db-parameter-group \
    --db-parameter-group-name mydbparametergroup \
    --parameters "ParameterName=max_connections,ParameterValue=250,ApplyMethod=immediate" \
                 "ParameterName=max_allowed_packet,ParameterValue=1024,ApplyMethod=immediate"
```
For Windows:  

```
aws rds modify-db-parameter-group ^
    --db-parameter-group-name mydbparametergroup ^
    --parameters "ParameterName=max_connections,ParameterValue=250,ApplyMethod=immediate" ^
                 "ParameterName=max_allowed_packet,ParameterValue=1024,ApplyMethod=immediate"
```
The command produces output like the following:  

```
DBPARAMETERGROUP  mydbparametergroup
```

### RDS API<a name="USER_WorkingWithParamGroups.Modifying.API"></a>

To modify a DB parameter group, use the RDS API [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBParameterGroup.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBParameterGroup.html) operation with the following required parameters:
+ `DBParameterGroupName`
+ `Parameters`

## Resetting parameters in a DB parameter group to their default values<a name="USER_WorkingWithParamGroups.Resetting"></a>

You can reset parameter values in a customer\-created DB parameter group to their default values\. Changes to parameters in a customer\-created DB parameter group are applied to all DB instances that are associated with the DB parameter group\.

When you use the console, you can reset specific parameters to their default values\. However, you can't easily reset all of the parameters in the DB parameter group at once\. When you use the AWS CLI or RDS API, you can reset specific parameters to their default values\. You can also reset all of the parameters in the DB parameter group at once\.

Changes to some parameters are applied to the DB instance immediately without a reboot\. Changes to other parameters are applied only after the DB instance is rebooted\. The RDS console shows the status of the DB parameter group associated with a DB instance on the **Configuration** tab\. For example, suppose that the DB instance isn't using the latest changes to its associated DB parameter group\. If so, the RDS console shows the DB parameter group with a status of **pending\-reboot**\. To apply the latest parameter changes to that DB instance, manually reboot the DB instance\.

![\[Parameter change pending reboot scenario\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/param-reboot.png)

**Note**  
In a default DB parameter group, parameters are always set to their default values\.

### Console<a name="USER_WorkingWithParamGroups.Resetting.CON"></a>

**To reset parameters in a DB parameter group to their default values**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Parameter groups**\.

1. In the list, choose the parameter group\.

1. For **Parameter group actions**, choose **Edit**\.

1. Choose the parameters that you want to reset to their default values\. You can scroll through the parameters using the arrow keys at the top right of the dialog box\. 

   You can't reset values in a default parameter group\.

1. Choose **Reset** and then confirm by choosing **Reset parameters**\.

### AWS CLI<a name="USER_WorkingWithParamGroups.Resetting.CLI"></a>

To reset some or all of the parameters in a DB parameter group, use the AWS CLI [https://docs.aws.amazon.com/cli/latest/reference/rds/reset-db-parameter-group.html](https://docs.aws.amazon.com/cli/latest/reference/rds/reset-db-parameter-group.html) command with the following required option: `--db-parameter-group-name`\.

To reset all of the parameters in the DB parameter group, specify the `--reset-all-parameters` option\. To reset specific parameters, specify the `--parameters` option\.

The following example resets all of the parameters in the DB parameter group named *mydbparametergroup* to their default values\.

**Example**  
For Linux, macOS, or Unix:  

```
aws rds reset-db-parameter-group \
    --db-parameter-group-name mydbparametergroup \
    --reset-all-parameters
```
For Windows:  

```
aws rds reset-db-parameter-group ^
    --db-parameter-group-name mydbparametergroup ^
    --reset-all-parameters
```

The following example resets the `max_connections` and `max_allowed_packet` options to their default values in the DB parameter group named *mydbparametergroup*\.

**Example**  
For Linux, macOS, or Unix:  

```
aws rds reset-db-parameter-group \
    --db-parameter-group-name mydbparametergroup \
    --parameters "ParameterName=max_connections,ApplyMethod=immediate" \
                 "ParameterName=max_allowed_packet,ApplyMethod=immediate"
```
For Windows:  

```
aws rds reset-db-parameter-group ^
    --db-parameter-group-name mydbparametergroup ^
    --parameters "ParameterName=max_connections,ApplyMethod=immediate" ^
                 "ParameterName=max_allowed_packet,ApplyMethod=immediate"
```
The command produces output like the following:  

```
DBParameterGroupName  mydbparametergroup
```

### RDS API<a name="USER_WorkingWithParamGroups.Resetting.API"></a>

To reset parameters in a DB parameter group to their default values, use the RDS API [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ResetDBParameterGroup.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ResetDBParameterGroup.html) command with the following required parameter: `DBParameterGroupName`\.

To reset all of the parameters in the DB parameter group, set the `ResetAllParameters` parameter to `true`\. To reset specific parameters, specify the `Parameters` parameter\.

## Copying a DB parameter group<a name="USER_WorkingWithParamGroups.Copying"></a>

You can copy custom DB parameter groups that you create\. Copying a parameter group can be convenient solution\. An example is when you have created a DB parameter group and want to include most of its custom parameters and values in a new DB parameter group\. You can copy a DB parameter group by using the AWS Management Console\. You can also use the AWS CLI [copy\-db\-parameter\-group](https://docs.aws.amazon.com/cli/latest/reference/rds/copy-db-parameter-group.html) command or the RDS API [CopyDBParameterGroup](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CopyDBParameterGroup.html) operation\.

After you copy a DB parameter group, wait at least 5 minutes before creating your first DB instance that uses that DB parameter group as the default parameter group\. Doing this allows Amazon RDS to fully complete the copy action before the parameter group is used\. This is especially important for parameters that are critical when creating the default database for a DB instance\. An example is the character set for the default database defined by the `character_set_database` parameter\. Use the **Parameter Groups** option of the [Amazon RDS console](https://console.aws.amazon.com/rds/) or the [describe\-db\-parameters](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-parameters.html) command to verify that your DB parameter group is created\.

**Note**  
You can't copy a default parameter group\. However, you can create a new parameter group that is based on a default parameter group\.  
Currently, you can't copy a parameter group to a different AWS Region\.

### Console<a name="USER_WorkingWithParamGroups.Copying.CON"></a>

**To copy a DB parameter group**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Parameter groups**\.

1. In the list, choose the custom parameter group that you want to copy\.

1. For **Parameter group actions**, choose **Copy**\.

1. In **New DB parameter group identifier**, enter a name for the new parameter group\.

1. In **Description**, enter a description for the new parameter group\.

1. Choose **Copy**\.

### AWS CLI<a name="USER_WorkingWithParamGroups.Copying.CLI"></a>

To copy a DB parameter group, use the AWS CLI [https://docs.aws.amazon.com/cli/latest/reference/rds/copy-db-parameter-group.html](https://docs.aws.amazon.com/cli/latest/reference/rds/copy-db-parameter-group.html) command with the following required options:
+ `--source-db-parameter-group-identifier`
+ `--target-db-parameter-group-identifier`
+ `--target-db-parameter-group-description`

The following example creates a new DB parameter group named `mygroup2` that is a copy of the DB parameter group `mygroup1`\.

**Example**  
For Linux, macOS, or Unix:  

```
aws rds copy-db-parameter-group \
    --source-db-parameter-group-identifier mygroup1 \
    --target-db-parameter-group-identifier mygroup2 \
    --target-db-parameter-group-description "DB parameter group 2"
```
For Windows:  

```
aws rds copy-db-parameter-group ^
    --source-db-parameter-group-identifier mygroup1 ^
    --target-db-parameter-group-identifier mygroup2 ^
    --target-db-parameter-group-description "DB parameter group 2"
```

### RDS API<a name="USER_WorkingWithParamGroups.Copying.API"></a>

To copy a DB parameter group, use the RDS API [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CopyDBParameterGroup.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CopyDBParameterGroup.html) operation with the following required parameters:
+ `SourceDBParameterGroupIdentifier`
+ `TargetDBParameterGroupIdentifier`
+ `TargetDBParameterGroupDescription`

## Listing DB parameter groups<a name="USER_WorkingWithParamGroups.Listing"></a>

You can list the DB parameter groups you've created for your AWS account\.

**Note**  
Default parameter groups are automatically created from a default parameter template when you create a DB instance for a particular DB engine and version\. These default parameter groups contain preferred parameter settings and can't be modified\. When you create a custom parameter group, you can modify parameter settings\. 

### Console<a name="USER_WorkingWithParamGroups.Listing.CON"></a>

**To list all DB parameter groups for an AWS account**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Parameter groups**\.

   The DB parameter groups appear in a list\.

### AWS CLI<a name="USER_WorkingWithParamGroups.Listing.CLI"></a>

To list all DB parameter groups for an AWS account, use the AWS CLI [https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-parameter-groups.html](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-parameter-groups.html) command\.

**Example**  
The following example lists all available DB parameter groups for an AWS account\.  

```
aws rds describe-db-parameter-groups
```
The command returns a response like the following:  

```
DBPARAMETERGROUP  default.mysql8.0     mysql8.0  Default parameter group for MySQL8.0
DBPARAMETERGROUP  mydbparametergroup   mysql8.0  My new parameter group
```
The following example describes the *mydbparamgroup1* parameter group\.  
For Linux, macOS, or Unix:  

```
aws rds describe-db-parameter-groups \
    --db-parameter-group-name mydbparamgroup1
```
For Windows:  

```
aws rds describe-db-parameter-groups ^
    --db-parameter-group-name mydbparamgroup1
```
The command returns a response like the following:  

```
DBPARAMETERGROUP  mydbparametergroup1  mysql8.0  My new parameter group
```

### RDS API<a name="USER_WorkingWithParamGroups.Listing.API"></a>

To list all DB parameter groups for an AWS account, use the RDS API [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBParameterGroups.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBParameterGroups.html) operation\.

## Viewing parameter values for a DB parameter group<a name="USER_WorkingWithParamGroups.Viewing"></a>

You can get a list of all parameters in a DB parameter group and their values\.

### Console<a name="USER_WorkingWithParamGroups.Viewing.CON"></a>

**To view the parameter values for a DB parameter group**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Parameter groups**\.

   The DB parameter groups appear in a list\.

1. Choose the name of the parameter group to see its list of parameters\.

### AWS CLI<a name="USER_WorkingWithParamGroups.Viewing.CLI"></a>

To view the parameter values for a DB parameter group, use the AWS CLI [https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-parameters.html](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-parameters.html) command with the following required parameter\.
+ `--db-parameter-group-name`

**Example**  
The following example lists the parameters and parameter values for a DB parameter group named *mydbparametergroup\.*  

```
aws rds describe-db-parameters --db-parameter-group-name mydbparametergroup
```
The command returns a response like the following:  

```
DBPARAMETER  Parameter Name            Parameter Value  Source           Data Type  Apply Type  Is Modifiable
DBPARAMETER  allow-suspicious-udfs                      engine-default   boolean    static      false
DBPARAMETER  auto_increment_increment                   engine-default   integer    dynamic     true
DBPARAMETER  auto_increment_offset                      engine-default   integer    dynamic     true
DBPARAMETER  binlog_cache_size         32768            system           integer    dynamic     true
DBPARAMETER  socket                    /tmp/mysql.sock  system           string     static      false
```

### RDS API<a name="USER_WorkingWithParamGroups.Viewing.API"></a>

To view the parameter values for a DB parameter group, use the RDS API [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBParameters.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBParameters.html) command with the following required parameter\.
+ `DBParameterGroupName`