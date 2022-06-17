# Parameters for MySQL<a name="Appendix.MySQL.Parameters"></a>

By default, a MySQL DB instance uses a DB parameter group that is specific to a MySQL database\. This parameter group contains parameters for the MySQL database engine\. For information about working with parameter groups and setting parameters, see [Working with parameter groups](USER_WorkingWithParamGroups.md)\.

RDS for MySQL parameters are set to the default values of the storage engine that you have selected\. For more information about MySQL parameters, see the [MySQL documentation](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html)\. For more information about MySQL storage engines, see [Supported storage engines for RDS for MySQL](MySQL.Concepts.FeatureSupport.md#MySQL.Concepts.Storage)\.

You can view the parameters available for a specific RDS for MySQL version using the RDS console or the AWS CLI\. For information about viewing the parameters in a MySQL parameter group in the RDS console, see [Viewing parameter values for a DB parameter group](USER_WorkingWithDBInstanceParamGroups.md#USER_WorkingWithParamGroups.Viewing)\.

Using the AWS CLI, you can view the parameters for an RDS for MySQL version by running the [https://docs.aws.amazon.com/cli/latest/reference/rds/describe-engine-default-parameters.html](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-engine-default-parameters.html) command\. Specify one of the following values for the `--db-parameter-group-family` option:
+ `mysql8.0`
+ `mysql5.7`

For example, to view the parameters for RDS for MySQL version 8\.0, run the following command\.

```
aws rds describe-engine-default-parameters --db-parameter-group-family mysql8.0
```

Your output looks similar to the following\.

```
{
    "EngineDefaults": {
        "Parameters": [
            {
                "ParameterName": "activate_all_roles_on_login",
                "ParameterValue": "0",
                "Description": "Automatically set all granted roles as active after the user has authenticated successfully.",
                "Source": "engine-default",
                "ApplyType": "dynamic",
                "DataType": "boolean",
                "AllowedValues": "0,1",
                "IsModifiable": true
            },
            {
                "ParameterName": "allow-suspicious-udfs",
                "Description": "Controls whether user-defined functions that have only an xxx symbol for the main function can be loaded",
                "Source": "engine-default",
                "ApplyType": "static",
                "DataType": "boolean",
                "AllowedValues": "0,1",
                "IsModifiable": false
            },
            {
                "ParameterName": "auto_generate_certs",
                "Description": "Controls whether the server autogenerates SSL key and certificate files in the data directory, if they do not already exist.",
                "Source": "engine-default",
                "ApplyType": "static",
                "DataType": "boolean",
                "AllowedValues": "0,1",
                "IsModifiable": false
            },            
        ...
```

To list only the modifiable parameters for RDS for MySQL version 8\.0, run the following command\.

For Linux, macOS, or Unix:

```
aws rds describe-engine-default-parameters --db-parameter-group-family mysql8.0 \
   --query 'EngineDefaults.Parameters[?IsModifiable==`true`]'
```

For Windows:

```
aws rds describe-engine-default-parameters --db-parameter-group-family mysql8.0 ^
   --query "EngineDefaults.Parameters[?IsModifiable==`true`]"
```