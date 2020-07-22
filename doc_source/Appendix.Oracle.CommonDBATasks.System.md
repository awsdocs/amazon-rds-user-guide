# Common DBA System Tasks for Oracle DB Instances<a name="Appendix.Oracle.CommonDBATasks.System"></a>

Following, you can find how to perform certain common DBA tasks related to the system on your Amazon RDS DB instances running Oracle\. To deliver a managed service experience, Amazon RDS doesn't provide shell access to DB instances, and restricts access to certain system procedures and tables that require advanced privileges\. 

**Topics**
+ [Disconnecting a Session](#Appendix.Oracle.CommonDBATasks.DisconnectingSession)
+ [Terminating a Session](#Appendix.Oracle.CommonDBATasks.KillingSession)
+ [Canceling a SQL Statement in a Session](#Appendix.Oracle.CommonDBATasks.CancellingSQL)
+ [Enabling and Disabling Restricted Sessions](#Appendix.Oracle.CommonDBATasks.RestrictedSession)
+ [Flushing the Shared Pool](#Appendix.Oracle.CommonDBATasks.FlushingSharedPool)
+ [Flushing the Buffer Cache](#Appendix.Oracle.CommonDBATasks.FlushingBufferCache)
+ [Granting SELECT or EXECUTE Privileges to SYS Objects](#Appendix.Oracle.CommonDBATasks.TransferPrivileges)
+ [Revoking SELECT or EXECUTE Privileges on SYS Objects](#Appendix.Oracle.CommonDBATasks.RevokePrivileges)
+ [Granting Privileges to Non\-Master Users](#Appendix.Oracle.CommonDBATasks.PermissionsNonMasters)
+ [Creating Custom Functions to Verify Passwords](#Appendix.Oracle.CommonDBATasks.CustomPassword)
+ [Setting Up a Custom DNS Server](#Appendix.Oracle.CommonDBATasks.CustomDNS)

## Disconnecting a Session<a name="Appendix.Oracle.CommonDBATasks.DisconnectingSession"></a>

To disconnect the current session by ending the dedicated server process, use the Amazon RDS procedure `rdsadmin.rdsadmin_util.disconnect`\. The `disconnect` procedure has the following parameters\. 


****  

| Parameter Name | Data Type | Default | Required | Description | 
| --- | --- | --- | --- | --- | 
| `sid` | number | — | Yes | The session identifier\. | 
| `serial` | number | — | Yes | The serial number of the session\. | 
| `method` | varchar | 'IMMEDIATE' | No | Valid values are `'IMMEDIATE'` or `'POST_TRANSACTION'`\. | 

The following example disconnects a session\.

```
begin
    rdsadmin.rdsadmin_util.disconnect(
        sid    => sid, 
        serial => serial_number);
end;
/
```

To get the session identifier and the session serial number, query the `V$SESSION` view\. The following example gets all sessions for the user `AWSUSER`\.

```
select SID, SERIAL#, STATUS from V$SESSION where USERNAME = 'AWSUSER';
```

The database must be open to use this method\. For more information about disconnecting a session, see [ALTER SYSTEM](http://docs.oracle.com/cd/E11882_01/server.112/e41084/statements_2014.htm#SQLRF53166) in the Oracle documentation\. 

## Terminating a Session<a name="Appendix.Oracle.CommonDBATasks.KillingSession"></a>

To terminate a session, use the Amazon RDS procedure `rdsadmin.rdsadmin_util.kill`\. The `kill` procedure has the following parameters\.


****  

| Parameter Name | Data Type | Default | Required | Description | 
| --- | --- | --- | --- | --- | 
| `sid` | number | — | Yes | The session identifier\. | 
| `serial` | number | — | Yes | The serial number of the session\. | 
| `method` | varchar | null | No |  Valid values are `'IMMEDIATE'` or `'PROCESS'`\.   | 

The following example terminates a session\.

```
begin
    rdsadmin.rdsadmin_util.kill(
        sid    => sid, 
        serial => serial_number);
end;
/
```

To get the session identifier and the session serial number, query the `V$SESSION` view\. The following example gets all sessions for the user `AWSUSER`\.

```
SELECT SID, SERIAL#, STATUS FROM V$SESSION WHERE USERNAME = 'AWSUSER';
```

You can specify either `IMMEDIATE` or `PROCESS` as a value for the `method` parameter\. By specifying `PROCESS` as the `method` value, you can terminate the processes associated with a session\. Do this only if terminating the session using `IMMEDIATE` as the `method` value was unsuccessful\. 

## Canceling a SQL Statement in a Session<a name="Appendix.Oracle.CommonDBATasks.CancellingSQL"></a>

To cancel a SQL statement in a session, use the Amazon RDS procedure `rdsadmin.rdsadmin_util.cancel`\.

**Note**  
This procedure is supported for Oracle version 18\.0\.0\.0 and later\.

The `cancel` procedure has the following parameters\. 


****  

| Parameter Name | Data Type | Default | Required | Description | 
| --- | --- | --- | --- | --- | 
| `sid` | number | — | Yes | The session identifier\. | 
| `serial` | number | — | Yes | The serial number of the session\. | 
| `sql_id` | varchar2 | null | No |  The SQL identifier of the SQL statement\.   | 

The following example cancels a SQL statement in a session\.

```
begin
    rdsadmin.rdsadmin_util.cancel(
        sid    => sid, 
        serial => serial_number,
        sql_id => sql_id);
end;
/
```

To get the session identifier, the session serial number, and the SQL identifier of a SQL statement, query the `V$SESSION` view\. The following example gets all sessions and SQL identifiers for the user `AWSUSER`\.

```
select SID, SERIAL#, SQL_ID, STATUS from V$SESSION where USERNAME = 'AWSUSER';
```

## Enabling and Disabling Restricted Sessions<a name="Appendix.Oracle.CommonDBATasks.RestrictedSession"></a>

To enable and disable restricted sessions, use the Amazon RDS procedure `rdsadmin.rdsadmin_util.restricted_session`\. The `restricted_session` procedure has the following parameters\. 


****  

| Parameter Name | Data Type | Default | Yes | Description | 
| --- | --- | --- | --- | --- | 
| `p_enable` | boolean | true | No |  Set to `true` to enable restricted sessions, `false` to disable restricted sessions\.   | 

The following example shows how to enable and disable restricted sessions\. 

```
/* Verify that the database is currently unrestricted. */

SELECT LOGINS FROM V$INSTANCE;
 
LOGINS
-------
ALLOWED


/* Enable restricted sessions */

exec rdsadmin.rdsadmin_util.restricted_session(p_enable => true);
 

/* Verify that the database is now restricted. */

SELECT LOGINS FROM V$INSTANCE;
 
LOGINS
----------
RESTRICTED
 

/* Disable restricted sessions */

exec rdsadmin.rdsadmin_util.restricted_session(p_enable => false);
 

/* Verify that the database is now unrestricted again. */

SELECT LOGINS FROM V$INSTANCE;
 
LOGINS
-------
ALLOWED
```

## Flushing the Shared Pool<a name="Appendix.Oracle.CommonDBATasks.FlushingSharedPool"></a>

To flush the shared pool, use the Amazon RDS procedure `rdsadmin.rdsadmin_util.flush_shared_pool`\. The `flush_shared_pool` procedure has no parameters\. 

The following example flushes the shared pool\.

```
exec rdsadmin.rdsadmin_util.flush_shared_pool;
```

## Flushing the Buffer Cache<a name="Appendix.Oracle.CommonDBATasks.FlushingBufferCache"></a>

To flush the buffer cache, use the Amazon RDS procedure `rdsadmin.rdsadmin_util.flush_buffer_cache`\. The `flush_buffer_cache` procedure has no parameters\. 

The following example flushes the buffer cache\.

```
exec rdsadmin.rdsadmin_util.flush_buffer_cache;
```

## Granting SELECT or EXECUTE Privileges to SYS Objects<a name="Appendix.Oracle.CommonDBATasks.TransferPrivileges"></a>

Usually you transfer privileges by using roles, which can contain many objects\. To grant privileges to a single object, use the Amazon RDS procedure `rdsadmin.rdsadmin_util.grant_sys_object`\. The procedure grants only privileges that the master user has already been granted through a role or direct grant\. 

The `grant_sys_object` procedure has the following parameters\. 

**Important**  
For all parameter values, use uppercase unless you created the user with a case\-sensitive identifier\. For example, if you run `CREATE USER myuser` or `CREATE USER MYUSER`, the data dictionary stores `MYUSER`\. However, if you use double quotes in `CREATE USER "MyUser"`, the data dictionary stores `MyUser`\.


****  

| Parameter Name | Data Type | Default | Required | Description | 
| --- | --- | --- | --- | --- | 
| `p_obj_name` | varchar2 | — | Yes |  The name of the object to grant privileges for\. The object can be a directory, function, package, procedure, sequence, table, or view\. Object names must be spelled exactly as they appear in `DBA_OBJECTS`\. Most system objects are defined in uppercase, so we recommend that you try that first\.   | 
| `p_grantee` | varchar2 | — | Yes |  The name of the object to grant privileges to\. The object can be a schema or a role\.   | 
| `p_privilege` | varchar2 | null | Yes | — | 
| `p_grant_option` | boolean | false | No |  Set to `true` to use the with grant option\. The `p_grant_option` parameter is supported for Oracle versions 11\.2\.0\.4\.v8 and later, 12\.1\.0\.2\.v4 and later, all 12\.2\.0\.1 versions, all 18\.0\.0\.0 versions, and all 19\.0\.0 versions\.   | 

The following example grants select privileges on an object named `V_$SESSION` to a user named `USER1`\.

```
begin
    rdsadmin.rdsadmin_util.grant_sys_object(
        p_obj_name  => 'V_$SESSION',
        p_grantee   => 'USER1',
        p_privilege => 'SELECT');
end;
/
```

The following example grants select privileges on an object named `V_$SESSION` to a user named `USER1` with the grant option\.

```
begin
    rdsadmin.rdsadmin_util.grant_sys_object(
        p_obj_name     => 'V_$SESSION',
        p_grantee      => 'USER1',
        p_privilege    => 'SELECT',
        p_grant_option => true);
end;
/
```

To be able to grant privileges on an object, your account must have those privileges granted to it directly with the grant option, or via a role granted using `with admin option`\. In the most common case, you may want to grant `SELECT` on a DBA view that has been granted to the `SELECT_CATALOG_ROLE` role\. If that role isn't already directly granted to your user using `with admin option`, then you can't transfer the privilege\. If you have the DBA privilege, then you can grant the role directly to another user\. 

The following example grants the `SELECT_CATALOG_ROLE` and `EXECUTE_CATALOG_ROLE` to `USER1`\. Since the `with admin option` is used, `USER1` can now grant access to SYS objects that have been granted to `SELECT_CATALOG_ROLE`\. 

```
GRANT SELECT_CATALOG_ROLE TO USER1 WITH ADMIN OPTION; 
GRANT EXECUTE_CATALOG_ROLE to USER1 WITH ADMIN OPTION;
```

Objects already granted to `PUBLIC` do not need to be re\-granted\. If you use the `grant_sys_object` procedure to re\-grant access, the procedure call succeeds\. 

## Revoking SELECT or EXECUTE Privileges on SYS Objects<a name="Appendix.Oracle.CommonDBATasks.RevokePrivileges"></a>

To revoke privileges on a single object, use the Amazon RDS procedure `rdsadmin.rdsadmin_util.revoke_sys_object`\. The procedure only revokes privileges that the master account has already been granted through a role or direct grant\. 

The `revoke_sys_object` procedure has the following parameters\. 


****  

| Parameter Name | Data Type | Default | Required | Description | 
| --- | --- | --- | --- | --- | 
| `p_obj_name` | varchar2 | — | Yes |  The name of the object to revoke privileges for\. The object can be a directory, function, package, procedure, sequence, table, or view\. Object names must be spelled exactly as they appear in `DBA_OBJECTS`\. Most system objects are defined in upper case, so we recommend you try that first\.   | 
| `p_revokee` | varchar2 | — | Yes |  The name of the object to revoke privileges for\. The object can be a schema or a role\.   | 
| `p_privilege` | varchar2 | null | Yes | — | 

The following example revokes select privileges on an object named `V_$SESSION` from a user named `USER1`\.

```
begin
    rdsadmin.rdsadmin_util.revoke_sys_object(
        p_obj_name  => 'V_$SESSION',
        p_revokee   => 'USER1',
        p_privilege => 'SELECT');
end;
/
```

## Granting Privileges to Non\-Master Users<a name="Appendix.Oracle.CommonDBATasks.PermissionsNonMasters"></a>

You can grant select privileges for many objects in the `SYS` schema by using the `SELECT_CATALOG_ROLE` role\. The `SELECT_CATALOG_ROLE` role gives users `SELECT` privileges on data dictionary views\. The following example grants the role `SELECT_CATALOG_ROLE` to a user named `user1`\. 

```
GRANT SELECT_CATALOG_ROLE TO user1;
```

You can grant `EXECUTE` privileges for many objects in the `SYS` schema by using the `EXECUTE_CATALOG_ROLE` role\. The `EXECUTE_CATALOG_ROLE` role gives users `EXECUTE` privileges for packages and procedures in the data dictionary\. The following example grants the role `EXECUTE_CATALOG_ROLE` to a user named *user1*\. 

```
GRANT EXECUTE_CATALOG_ROLE TO user1;
```

The following example gets the permissions that the roles `SELECT_CATALOG_ROLE` and `EXECUTE_CATALOG_ROLE` allow\. 

```
  SELECT * 
    FROM ROLE_TAB_PRIVS  
   WHERE ROLE IN ('SELECT_CATALOG_ROLE','EXECUTE_CATALOG_ROLE') 
ORDER BY ROLE, TABLE_NAME ASC;
```

The following example creates a non\-master user named `user1`, grants the `CREATE SESSION` privilege, and grants the `SELECT` privilege on a database named *sh\.sales*\.

```
CREATE USER user1 IDENTIFIED BY PASSWORD;
GRANT CREATE SESSION TO user1;
GRANT SELECT ON sh.sales TO user1;
```

## Creating Custom Functions to Verify Passwords<a name="Appendix.Oracle.CommonDBATasks.CustomPassword"></a>

You can create a custom password verification function in two ways\. If you want to use standard verification logic, and to store your function in the `SYS` schema, use the `create_verify_function` procedure\. If you want to use custom verification logic, or you don't want to store your function in the `SYS` schema, use the `create_passthrough_verify_fcn` procedure\. 

### The create\_verify\_function Procedure<a name="Appendix.Oracle.CommonDBATasks.CustomPassword.Standard"></a>

The `create_verify_function` procedure is supported for Oracle version 11\.2\.0\.4\.v9 and later, Oracle version 12\.1\.0\.2\.v5 and later, all 12\.2\.0\.1 versions, all 18\.0\.0\.0 versions, and all 19\.0\.0 versions\.

You can create a custom function to verify passwords by using the Amazon RDS procedure `rdsadmin.rdsadmin_password_verify.create_verify_function`\. The `create_verify_function` procedure has the following parameters\. 


****  

| Parameter Name | Data Type | Default | Required | Description | 
| --- | --- | --- | --- | --- | 
| `p_verify_function_name` | varchar2 | — | Yes |  The name for your custom function\. This function is created for you in the SYS schema\. You assign this function to user profiles\.   | 
| `p_min_length` | number | 8 | No | The minimum number of characters required\. | 
| `p_max_length` | number | 256 | No | The maximum number of characters allowed\. | 
| `p_min_letters` | number | 1 | No | The minimum number of letters required\. | 
| `p_min_uppercase` | number | 0 | No | The minimum number of uppercase letters required\. | 
| `p_min_lowercase` | number | 0 | No | The minimum number of lowercase letters required\. | 
| `p_min_digits` | number | 1 | No | The minimum number of digits required\. | 
| `p_min_special` | number | 0 | No | The minimum number of special characters required\. | 
| `p_min_different_chars` | number | 3 | No | The minimum number of different characters required between the old and new password\. | 
| `p_disallow_username` | boolean | true | No | Set to `true` to disallow the user name in the password\. | 
| `p_disallow_reverse` | boolean | true | No | Set to `true` to disallow the reverse of the user name in the password\. | 
| `p_disallow_db_name` | boolean | true | No | Set to `true` to disallow the database or server name in the password\. | 
| `p_disallow_simple_strings` | boolean | true | No | Set to `true` to disallow simple strings as the password\. | 
| `p_disallow_whitespace` | boolean | false | No | Set to `true` to disallow white space characters in the password\. | 
| `p_disallow_at_sign` | boolean | false | No | Set to `true` to disallow the @ character in the password\. | 

You can create multiple password verification functions\.

There are restrictions on the name of your custom function\. Your custom function can't have the same name as an existing system object\. The name can be no more than 30 characters long\. Also, the name must include one of the following strings: `PASSWORD`, `VERIFY`, `COMPLEXITY`, `ENFORCE`, or `STRENGTH`\. 

The following example creates a function named `CUSTOM_PASSWORD_FUNCTION`\. The function requires that a password has at least 12 characters, 2 uppercase characters, 1 digit, and 1 special character, and that the password disallows the @ character\. 

```
begin
    rdsadmin.rdsadmin_password_verify.create_verify_function(
        p_verify_function_name => 'CUSTOM_PASSWORD_FUNCTION', 
        p_min_length           => 12, 
        p_min_uppercase        => 2, 
        p_min_digits           => 1, 
        p_min_special          => 1,
        p_disallow_at_sign     => true);
end;
/
```

To see the text of your verification function, query `DBA_SOURCE`\. The following example gets the text of a custom password function named `CUSTOM_PASSWORD_FUNCTION`\. 

```
COL TEXT FORMAT a150

  SELECT TEXT 
    FROM DBA_SOURCE 
   WHERE OWNER = 'SYS' 
     AND NAME = 'CUSTOM_PASSWORD_FUNCTION' 
ORDER BY LINE;
```

To associate your verification function with a user profile, use `alter profile`\. The following example associates a verification function with the `DEFAULT` user profile\. 

```
ALTER PROFILE DEFAULT LIMIT PASSWORD_VERIFY_FUNCTION CUSTOM_PASSWORD_FUNCTION;
```

To see what user profiles are associated with what verification functions, query `DBA_PROFILES`\. The following example gets the profiles that are associated with the custom verification function named `CUSTOM_PASSWORD_FUNCTION`\. 

```
SELECT * FROM DBA_PROFILES WHERE RESOURCE_NAME = 'PASSWORD' AND LIMIT = 'CUSTOM_PASSWORD_FUNCTION';


PROFILE                    RESOURCE_NAME                     RESOURCE  LIMIT
-------------------------  --------------------------------  --------  ------------------------
DEFAULT                    PASSWORD_VERIFY_FUNCTION          PASSWORD  CUSTOM_PASSWORD_FUNCTION
```

The following example gets all profiles and the password verification functions that they are associated with\. 

```
SELECT * FROM DBA_PROFILES WHERE RESOURCE_NAME = 'PASSWORD_VERIFY_FUNCTION';


PROFILE                    RESOURCE_NAME                     RESOURCE  LIMIT
-------------------------  --------------------------------  --------  ------------------------
DEFAULT                    PASSWORD_VERIFY_FUNCTION          PASSWORD  CUSTOM_PASSWORD_FUNCTION
RDSADMIN                   PASSWORD_VERIFY_FUNCTION          PASSWORD  NULL
```

### The create\_passthrough\_verify\_fcn Procedure<a name="Appendix.Oracle.CommonDBATasks.CustomPassword.Custom"></a>

The `create_passthrough_verify_fcn` procedure is supported for Oracle version 11\.2\.0\.4\.v11 and later, Oracle version 12\.1\.0\.2\.v7 and later, all 12\.2\.0\.1 versions, all 18\.0\.0\.0 versions, and all 19\.0\.0 versions\.

You can create a custom function to verify passwords by using the Amazon RDS procedure `rdsadmin.rdsadmin_password_verify.create_passthrough_verify_fcn`\. The `create_passthrough_verify_fcn` procedure has the following parameters\. 


****  

| Parameter Name | Data Type | Default | Required | Description | 
| --- | --- | --- | --- | --- | 
| `p_verify_function_name` | varchar2 | — | Yes |  The name for your custom verification function\. This is a wrapper function that is created for you in the SYS schema, and it doesn't contain any verification logic\. You assign this function to user profiles\.   | 
| `p_target_owner` | varchar2 | — | Yes | The schema owner for your custom verification function\. | 
| `p_target_function_name` | varchar2 | — | Yes |  The name of your existing custom function that contains the verification logic\. Your custom function must return a boolean\. Your function should return `true` if the password is valid and `false` if the password is invalid\.   | 

The following example creates a password verification function that uses the logic from the function named `PASSWORD_LOGIC_EXTRA_STRONG`\. 

```
begin
    rdsadmin.rdsadmin_password_verify.create_passthrough_verify_fcn(
        p_verify_function_name => 'CUSTOM_PASSWORD_FUNCTION', 
        p_target_owner         => 'TEST_USER',
        p_target_function_name => 'PASSWORD_LOGIC_EXTRA_STRONG');
end;
/
```

To associate the verification function with a user profile, use `alter profile`\. The following example associates the verification function with the `DEFAULT` user profile\. 

```
ALTER PROFILE DEFAULT LIMIT PASSWORD_VERIFY_FUNCTION CUSTOM_PASSWORD_FUNCTION;
```

## Setting Up a Custom DNS Server<a name="Appendix.Oracle.CommonDBATasks.CustomDNS"></a>

Amazon RDS supports outbound network access on your DB instances running Oracle\.  For more information about outbound network access, including prerequisites, see [Using utl\_http, utl\_tcp, and utl\_smtp with an Oracle DB Instance](CHAP_Oracle.md#Oracle.Concepts.ONA)\. 

Amazon RDS Oracle allows Domain Name Service \(DNS\) resolution from a custom DNS server owned by the customer\. You can resolve only fully qualified domain names from your Amazon RDS DB instance through your custom DNS server\. 

After you set up your custom DNS name server, it takes up to 30 minutes to propagate the changes to your DB instance\. After the changes are propagated to your DB instance, all outbound network traffic requiring a DNS lookup queries your DNS server over port 53\. 

To set up a custom DNS server for your Amazon RDS for Oracle DB instance, do the following: 
+ From the DHCP options set attached to your virtual private cloud \(VPC\), set the `domain-name-servers` option to the IP address of your DNS name server\. For more information, see [DHCP Options Sets](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_DHCP_Options.html)\. 
**Note**  
The `domain-name-servers` option accepts up to four values, but your Amazon RDS DB instance uses only the first value\. 
+ Ensure that your DNS server can resolve all lookup queries, including public DNS names, Amazon EC2 private DNS names, and customer\-specific DNS names\. If the outbound network traffic contains any DNS lookups that your DNS server can't handle, your DNS server must have appropriate upstream DNS providers configured\. 
+ Configure your DNS server to produce User Datagram Protocol \(UDP\) responses of 512 bytes or less\. 
+ Configure your DNS server to produce Transmission Control Protocol \(TCP\) responses of 1024 bytes or less\. 
+ Configure your DNS server to allow inbound traffic from your Amazon RDS DB instances over port 53\. If your DNS server is in an Amazon VPC, the VPC must have a security group that contains inbound rules that permit UDP and TCP traffic on port 53\. If your DNS server is not in an Amazon VPC, it must have appropriate firewall allow\-listing to permit UDP and TCP inbound traffic on port 53\.

  For more information, see [Security Groups for Your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html) and [Adding and Removing Rules](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html#AddRemoveRules)\. 
+ Configure the VPC of your Amazon RDS DB instance to allow outbound traffic over port 53\. Your VPC must have a security group that contains outbound rules that allow UDP and TCP traffic on port 53\. 

  For more information, see [Security Groups for Your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html) and [Adding and Removing Rules](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html#AddRemoveRules)\. 
+ The routing path between the Amazon RDS DB instance and the DNS server has to be configured correctly to allow DNS traffic\. 
  + If the Amazon RDS DB instance and the DNS server are not in the same VPC, a peering connection has to be set up between them\. For more information, see [What is VPC Peering?](https://docs.aws.amazon.com/vpc/latest/peering/Welcome.html) 