# Using the Password Validation Plugin for RDS for MySQL<a name="MySQL.Concepts.PasswordValidationPlugin"></a>

MySQL provides the `validate_password` plugin for improved security\. The plugin enforces password policies using parameters in the DB parameter group for your MySQL DB instance\. The plugin is supported for DB instances running MySQL version 5\.7 and 8\.0\. For more information about the `validate_password` plugin, see [The Password Validation Plugin](https://dev.mysql.com/doc/refman/8.0/en/validate-password.html) in the MySQL documentation\.

**To enable the validate\_password plugin for a MySQL DB instance**

1. Connect to your MySQL DB instance and run the following command\.

   ```
   INSTALL PLUGIN validate_password SONAME 'validate_password.so';                    
   ```

1. Configure the parameters for the plugin in the DB parameter group used by the DB instance\.

   For more information about the parameters, see [Password Validation Plugin options and variables](https://dev.mysql.com/doc/refman/8.0/en/validate-password-options-variables.html) in the MySQL documentation\.

   For more information about modifying DB instance parameters, see [Modifying parameters in a DB parameter group](USER_WorkingWithDBInstanceParamGroups.md#USER_WorkingWithParamGroups.Modifying)\.

After installing and enabling the `password_validate` plugin, reset existing passwords to comply with your new validation policies\.

Amazon RDS doesn't validate passwords\. The MySQL DB instance performs password validation\. If you set a user password with the AWS Management Console, the `modify-db-instance` AWS CLI command, or the `ModifyDBInstance` RDS API operation, the change can succeed even if the new password doesn't satisfy your password policies\. However, a new password is set in the MySQL DB instance only if it satisfies the password policies\. In this case, Amazon RDS records the following event\.

```
"RDS-EVENT-0067" - An attempt to reset the master password for the DB instance has failed.            
```

For more information about Amazon RDS events, see [Working with Amazon RDS event notification](USER_Events.md)\.