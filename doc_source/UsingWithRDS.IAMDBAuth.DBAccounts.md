# Creating a Database Account Using IAM Authentication<a name="UsingWithRDS.IAMDBAuth.DBAccounts"></a>

With IAM database authentication, you don't need to assign database passwords to the user accounts you create\. If you remove an IAM user that is mapped to a database account, you should also remove the database account with the `DROP USER` statement\.

## Using IAM Authentication with PostgreSQL<a name="UsingWithRDS.IAMDBAuth.DBAccounts.PostgreSQL"></a>

To use IAM authentication with PostgreSQL, connect to the DB instance, create database users, and then grant them the `rds_iam` role as shown in the following example\.

```
CREATE USER db_userx; 
GRANT rds_iam TO db_userx;
```

## Using IAM Authentication with MySQL<a name="UsingWithRDS.IAMDBAuth.DBAccounts.MySQL"></a>

With MySQL, authentication is handled by `AWSAuthenticationPlugin`—an AWS\-provided plugin that works seamlessly with IAM to authenticate your IAM users\. Connect to the DB instance and issue the `CREATE USER` statement, as shown in the following example\.

```
CREATE USER jane_doe IDENTIFIED WITH AWSAuthenticationPlugin AS 'RDS'; 
```

The `IDENTIFIED WITH` clause allows MySQL to use the `AWSAuthenticationPlugin` to authenticate the database account \(`jane_doe`\)\. The `AS 'RDS'` clause refers to the authentication method, and the specified database account should have the same name as the IAM user or role\. In this example, both the database account and the IAM user or role are named `jane_doe`\. 

**Note**  
If you see the following message, it means that the AWS\-provided plugin is not available for the current DB instance\.  
`ERROR 1524 (HY000): Plugin 'AWSAuthenticationPlugin' is not loaded`  
To troubleshoot this error, verify that you are using a supported configuration and that you have enabled IAM database authentication on your DB instance\. For more information, see [Availability for IAM Database Authentication](UsingWithRDS.IAMDBAuth.md#UsingWithRDS.IAMDBAuth.Availability) and [Enabling and Disabling IAM Database Authentication](UsingWithRDS.IAMDBAuth.Enabling.md)\.

After you create an account using `AWSAuthenticationPlugin`, you manage it in the same way as other database accounts\. For example, you can modify account privileges with `GRANT` and `REVOKE` statements, or modify various account attributes with the `ALTER USER` statement\. 