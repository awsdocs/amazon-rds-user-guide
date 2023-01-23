# Creating a database account using IAM authentication<a name="UsingWithRDS.IAMDBAuth.DBAccounts"></a>

With IAM database authentication, you don't need to assign database passwords to the user accounts you create\. If you remove a user that is mapped to a database account, you should also remove the database account with the `DROP USER` statement\.

**Note**  
The user name used for IAM authentication must match the case of the user name in the database\.

**Topics**
+ [Using IAM authentication with MariaDB and MySQL](#UsingWithRDS.IAMDBAuth.DBAccounts.MySQL)
+ [Using IAM authentication with PostgreSQL](#UsingWithRDS.IAMDBAuth.DBAccounts.PostgreSQL)

## Using IAM authentication with MariaDB and MySQL<a name="UsingWithRDS.IAMDBAuth.DBAccounts.MySQL"></a>

With MariaDB and MySQL, authentication is handled by `AWSAuthenticationPlugin`—an AWS\-provided plugin that works seamlessly with IAM to authenticate your users\. Connect to the DB instance as the master user or a different user who can create users and grant privileges\. After connecting, issue the `CREATE USER` statement, as shown in the following example\.

```
CREATE USER jane_doe IDENTIFIED WITH AWSAuthenticationPlugin AS 'RDS'; 
```

The `IDENTIFIED WITH` clause allows MariaDB and MySQL to use the `AWSAuthenticationPlugin` to authenticate the database account \(`jane_doe`\)\. The `AS 'RDS'` clause refers to the authentication method\. Make sure the specified database user name is the same as a resource in the IAM policy for IAM database access\. For more information, see [Creating and using an IAM policy for IAM database access](UsingWithRDS.IAMDBAuth.IAMPolicy.md)\. 

**Note**  
If you see the following message, it means that the AWS\-provided plugin is not available for the current DB instance\.  
`ERROR 1524 (HY000): Plugin 'AWSAuthenticationPlugin' is not loaded`  
To troubleshoot this error, verify that you are using a supported configuration and that you have enabled IAM database authentication on your DB instance\. For more information, see [Region and version availability](UsingWithRDS.IAMDBAuth.md#UsingWithRDS.IAMDBAuth.Availability) and [Enabling and disabling IAM database authentication](UsingWithRDS.IAMDBAuth.Enabling.md)\.

After you create an account using `AWSAuthenticationPlugin`, you manage it in the same way as other database accounts\. For example, you can modify account privileges with `GRANT` and `REVOKE` statements, or modify various account attributes with the `ALTER USER` statement\. 

## Using IAM authentication with PostgreSQL<a name="UsingWithRDS.IAMDBAuth.DBAccounts.PostgreSQL"></a>

To use IAM authentication with PostgreSQL, connect to the DB instance as the master user or a different user who can create users and grant privileges\. After connecting, create database users and then grant them the `rds_iam` role as shown in the following example\.

```
CREATE USER db_userx; 
GRANT rds_iam TO db_userx;
```

Make sure the specified database user name is the same as a resource in the IAM policy for IAM database access\. For more information, see [Creating and using an IAM policy for IAM database access](UsingWithRDS.IAMDBAuth.IAMPolicy.md)\. 