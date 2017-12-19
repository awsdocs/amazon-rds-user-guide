# Creating a Database Account<a name="UsingWithRDS.IAMDBAuth.DBAccounts"></a>

With IAM database authentication, you don't need to assign database passwords to the MySQL user accounts you create\. Instead, authentication is handled by `AWSAuthenticationPlugin`—an AWS\-provided plugin that works seamlessly with IAM to authenticate your IAM users\.

To create a database account for MySQL, connect to the DB instance or DB cluster and issue the `CREATE USER` statement, as shown in the following example\.

```
CREATE USER jane_doe IDENTIFIED WITH AWSAuthenticationPlugin AS 'RDS'; 
```

The `IDENTIFIED WITH` clause allows MySQL to use the `AWSAuthenticationPlugin` to authenticate the database account \(`jane_doe`\)\. The `AS 'RDS'` clause maps the `jane_doe` database account to the corresponding IAM user or role\.

**Note**  
If you see the following message, it means that the AWS\-provided plugin is not available for the current DB instance or DB cluster\.  
`ERROR 1524 (HY000): Plugin 'AWSAuthenticationPlugin' is not loaded`  
To troubleshoot this error, verify that you are using a supported configuration and that you have enabled IAM database authentication on your DB instance or DB cluster\. For more information, see [Availability for IAM Database Authentication](UsingWithRDS.IAMDBAuth.md#UsingWithRDS.IAMDBAuth.Availability) and [Enabling and Disabling IAM Database Authentication](UsingWithRDS.IAMDBAuth.Enabling.md)\.

After you create an account using `AWSAuthenticationPlugin`, you manage it in the same way as other database accounts\. For example, you can modify account privileges with `GRANT` and `REVOKE` statements, or modify various account attributes with the `ALTER USER` statement\.

 If you remove an IAM user that is mapped to a database account, you should also remove the database account with the `DROP USER` statement\.