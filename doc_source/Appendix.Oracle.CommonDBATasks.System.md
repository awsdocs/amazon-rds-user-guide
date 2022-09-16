# Performing common system tasks for Oracle DB instances<a name="Appendix.Oracle.CommonDBATasks.System"></a>

Following, you can find how to perform certain common DBA tasks related to the system on your Amazon RDS DB instances running Oracle\. To deliver a managed service experience, Amazon RDS doesn't provide shell access to DB instances, and restricts access to certain system procedures and tables that require advanced privileges\. 

**Topics**
+ [Disconnecting a session](#Appendix.Oracle.CommonDBATasks.DisconnectingSession)
+ [Terminating a session](#Appendix.Oracle.CommonDBATasks.KillingSession)
+ [Canceling a SQL statement in a session](#Appendix.Oracle.CommonDBATasks.CancellingSQL)
+ [Enabling and disabling restricted sessions](#Appendix.Oracle.CommonDBATasks.RestrictedSession)
+ [Flushing the shared pool](#Appendix.Oracle.CommonDBATasks.FlushingSharedPool)
+ [Flushing the buffer cache](#Appendix.Oracle.CommonDBATasks.FlushingBufferCache)
+ [Flushing the database smart flash cache](#Appendix.Oracle.CommonDBATasks.flushing-shared-pool)
+ [Granting SELECT or EXECUTE privileges to SYS objects](#Appendix.Oracle.CommonDBATasks.TransferPrivileges)
+ [Revoking SELECT or EXECUTE privileges on SYS objects](#Appendix.Oracle.CommonDBATasks.RevokePrivileges)
+ [Granting privileges to non\-master users](#Appendix.Oracle.CommonDBATasks.PermissionsNonMasters)
+ [Creating custom functions to verify passwords](#Appendix.Oracle.CommonDBATasks.CustomPassword)
+ [Setting up a custom DNS server](#Appendix.Oracle.CommonDBATasks.CustomDNS)
+ [Setting and unsetting system diagnostic events](#Appendix.Oracle.CommonDBATasks.SystemEvents)

## Disconnecting a session<a name="Appendix.Oracle.CommonDBATasks.DisconnectingSession"></a>

To disconnect the current session by ending the dedicated server process, use the Amazon RDS procedure `rdsadmin.rdsadmin_util.disconnect`\. The `disconnect` procedure has the following parameters\. 


****  

| Parameter name | Data type | Default | Required | Description | 
| --- | --- | --- | --- | --- | 
|  `sid`  |  number  |  —  |  Yes  |  The session identifier\.  | 
|  `serial`  |  number  |  —  |  Yes  |  The serial number of the session\.  | 
|  `method`  |  varchar  |  'IMMEDIATE'  |  No  |  Valid values are `'IMMEDIATE'` or `'POST_TRANSACTION'`\.  | 

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
SELECT SID, SERIAL#, STATUS FROM V$SESSION WHERE USERNAME = 'AWSUSER';
```

The database must be open to use this method\. For more information about disconnecting a session, see [ALTER SYSTEM](http://docs.oracle.com/cd/E11882_01/server.112/e41084/statements_2014.htm#SQLRF53166) in the Oracle documentation\. 

## Terminating a session<a name="Appendix.Oracle.CommonDBATasks.KillingSession"></a>

To terminate a session, use the Amazon RDS procedure `rdsadmin.rdsadmin_util.kill`\. The `kill` procedure has the following parameters\.


****  

| Parameter name | Data type | Default | Required | Description | 
| --- | --- | --- | --- | --- | 
|  `sid`  |  number  |  —  |  Yes  |  The session identifier\.  | 
|  `serial`  |  number  |  —  |  Yes  |  The serial number of the session\.  | 
|  `method`  |  varchar  |  null  |  No  |  Valid values are `'IMMEDIATE'` or `'PROCESS'`\. If you specify `IMMEDIATE`, it has the same effect as running the following statement: <pre>ALTER SYSTEM KILL SESSION 'sid,serial#' IMMEDIATE</pre> If you specify `PROCESS`, you terminate the processes associated with a session\. Only specify `PROCESS` if terminating the session using `IMMEDIATE` was unsuccessful\.  | 

To get the session identifier and the session serial number, query the `V$SESSION` view\. The following example gets all sessions for the user *AWSUSER*\.

```
SELECT SID, SERIAL#, STATUS FROM V$SESSION WHERE USERNAME = 'AWSUSER';
```

The following example terminates a session\.

```
BEGIN
    rdsadmin.rdsadmin_util.kill(
        sid    => sid, 
        serial => serial_number,
        method => 'IMMEDIATE');
END;
/
```

The following example terminates the processes associated with a session\.

```
BEGIN
    rdsadmin.rdsadmin_util.kill(
        sid    => sid, 
        serial => serial_number,
        method => 'PROCESS');
END;
/
```

## Canceling a SQL statement in a session<a name="Appendix.Oracle.CommonDBATasks.CancellingSQL"></a>

To cancel a SQL statement in a session, use the Amazon RDS procedure `rdsadmin.rdsadmin_util.cancel`\.

**Note**  
This procedure is supported for Oracle Database 19c \(19\.0\.0\) and all higher major and minor versions of RDS for Oracle\.

The `cancel` procedure has the following parameters\. 


****  

| Parameter name | Data type | Default | Required | Description | 
| --- | --- | --- | --- | --- | 
|  `sid`  |  number  |  —  |  Yes  |  The session identifier\.  | 
|  `serial`  |  number  |  —  |  Yes  |  The serial number of the session\.  | 
|  `sql_id`  |  varchar2  |  null  |  No  |  The SQL identifier of the SQL statement\.   | 

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

## Enabling and disabling restricted sessions<a name="Appendix.Oracle.CommonDBATasks.RestrictedSession"></a>

To enable and disable restricted sessions, use the Amazon RDS procedure `rdsadmin.rdsadmin_util.restricted_session`\. The `restricted_session` procedure has the following parameters\. 


****  

| Parameter name | Data type | Default | Yes | Description | 
| --- | --- | --- | --- | --- | 
|  `p_enable`  |  boolean  |  true  |  No  |  Set to `true` to enable restricted sessions, `false` to disable restricted sessions\.   | 

The following example shows how to enable and disable restricted sessions\. 

```
/* Verify that the database is currently unrestricted. */

SELECT LOGINS FROM V$INSTANCE;
 
LOGINS
-------
ALLOWED

/* Enable restricted sessions */

EXEC rdsadmin.rdsadmin_util.restricted_session(p_enable => true);
 

/* Verify that the database is now restricted. */

SELECT LOGINS FROM V$INSTANCE;
 
LOGINS
----------
RESTRICTED
 

/* Disable restricted sessions */

EXEC rdsadmin.rdsadmin_util.restricted_session(p_enable => false);
 

/* Verify that the database is now unrestricted again. */

SELECT LOGINS FROM V$INSTANCE;
 
LOGINS
-------
ALLOWED
```

## Flushing the shared pool<a name="Appendix.Oracle.CommonDBATasks.FlushingSharedPool"></a>

To flush the shared pool, use the Amazon RDS procedure `rdsadmin.rdsadmin_util.flush_shared_pool`\. The `flush_shared_pool` procedure has no parameters\. 

The following example flushes the shared pool\.

```
EXEC rdsadmin.rdsadmin_util.flush_shared_pool;
```

## Flushing the buffer cache<a name="Appendix.Oracle.CommonDBATasks.FlushingBufferCache"></a>

To flush the buffer cache, use the Amazon RDS procedure `rdsadmin.rdsadmin_util.flush_buffer_cache`\. The `flush_buffer_cache` procedure has no parameters\. 

The following example flushes the buffer cache\.

```
EXEC rdsadmin.rdsadmin_util.flush_buffer_cache;
```

## Flushing the database smart flash cache<a name="Appendix.Oracle.CommonDBATasks.flushing-shared-pool"></a>

To flush the database smart flash cache, use the Amazon RDS procedure `rdsadmin.rdsadmin_util.flush_flash_cache`\. The `flush_flash_cache` procedure has no parameters\. The following example flushes the database smart flash cache\.

```
EXEC rdsadmin.rdsadmin_util.flush_flash_cache;
```

For more information about using the database smart flash cache with RDS for Oracle, see [Storing temporary data in an RDS for Oracle instance store](CHAP_Oracle.advanced-features.instance-store.md)\.

## Granting SELECT or EXECUTE privileges to SYS objects<a name="Appendix.Oracle.CommonDBATasks.TransferPrivileges"></a>

Usually you transfer privileges by using roles, which can contain many objects\. To grant privileges to a single object, use the Amazon RDS procedure `rdsadmin.rdsadmin_util.grant_sys_object`\. The procedure grants only privileges that the master user has already been granted through a role or direct grant\. 

The `grant_sys_object` procedure has the following parameters\. 

**Important**  
For all parameter values, use uppercase unless you created the user with a case\-sensitive identifier\. For example, if you run `CREATE USER myuser` or `CREATE USER MYUSER`, the data dictionary stores `MYUSER`\. However, if you use double quotes in `CREATE USER "MyUser"`, the data dictionary stores `MyUser`\.


****  

| Parameter name | Data type | Default | Required | Description | 
| --- | --- | --- | --- | --- | 
|  `p_obj_name`  |  varchar2  |  —  |  Yes  |  The name of the object to grant privileges for\. The object can be a directory, function, package, procedure, sequence, table, or view\. Object names must be spelled exactly as they appear in `DBA_OBJECTS`\. Most system objects are defined in uppercase, so we recommend that you try that first\.   | 
|  `p_grantee`  |  varchar2  |  —  |  Yes  |  The name of the object to grant privileges to\. The object can be a schema or a role\.   | 
|  `p_privilege`  |  varchar2  |  null  |  Yes  |  —  | 
|  `p_grant_option`  |  boolean  |  false  |  No  |  Set to `true` to use the with grant option\. The `p_grant_option` parameter is supported for 12\.1\.0\.2\.v4 and later, all 12\.2\.0\.1 versions, and all 19\.0\.0 versions\.   | 

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

## Revoking SELECT or EXECUTE privileges on SYS objects<a name="Appendix.Oracle.CommonDBATasks.RevokePrivileges"></a>

To revoke privileges on a single object, use the Amazon RDS procedure `rdsadmin.rdsadmin_util.revoke_sys_object`\. The procedure only revokes privileges that the master account has already been granted through a role or direct grant\. 

The `revoke_sys_object` procedure has the following parameters\. 


****  

| Parameter name | Data type | Default | Required | Description | 
| --- | --- | --- | --- | --- | 
|  `p_obj_name`  |  varchar2  |  —  |  Yes  |  The name of the object to revoke privileges for\. The object can be a directory, function, package, procedure, sequence, table, or view\. Object names must be spelled exactly as they appear in `DBA_OBJECTS`\. Most system objects are defined in upper case, so we recommend you try that first\.   | 
|  `p_revokee`  |  varchar2  |  —  |  Yes  |  The name of the object to revoke privileges for\. The object can be a schema or a role\.   | 
|  `p_privilege`  |  varchar2  |  null  |  Yes  |  —  | 

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

## Granting privileges to non\-master users<a name="Appendix.Oracle.CommonDBATasks.PermissionsNonMasters"></a>

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

## Creating custom functions to verify passwords<a name="Appendix.Oracle.CommonDBATasks.CustomPassword"></a>

You can create a custom password verification function in the following ways:
+ To use standard verification logic, and to store your function in the `SYS` schema, use the `create_verify_function` procedure\. 
+ To use custom verification logic, or to avoid storing your function in the `SYS` schema, use the `create_passthrough_verify_fcn` procedure\. 

### The create\_verify\_function procedure<a name="Appendix.Oracle.CommonDBATasks.CustomPassword.Standard"></a>

You can create a custom function to verify passwords by using the Amazon RDS procedure `rdsadmin.rdsadmin_password_verify.create_verify_function`\. The `create_verify_function` procedure is supported for version 12\.1\.0\.2\.v5 and all higher major and minor versions of RDS for Oracle\.

The `create_verify_function` procedure has the following parameters\. 


****  

| Parameter name | Data type | Default | Required | Description | 
| --- | --- | --- | --- | --- | 
|  `p_verify_function_name`  |  varchar2  |  —  |  Yes  |  The name for your custom function\. This function is created for you in the SYS schema\. You assign this function to user profiles\.   | 
|  `p_min_length`  |  number  |  8  |  No  |  The minimum number of characters required\.  | 
|  `p_max_length`  |  number  |  256  |  No  |  The maximum number of characters allowed\.  | 
|  `p_min_letters`  |  number  |  1  |  No  |  The minimum number of letters required\.  | 
|  `p_min_uppercase`  |  number  |  0  |  No  |  The minimum number of uppercase letters required\.  | 
|  `p_min_lowercase`  |  number  |  0  |  No  |  The minimum number of lowercase letters required\.  | 
|  `p_min_digits`  |  number  |  1  |  No  |  The minimum number of digits required\.  | 
|  `p_min_special`  |  number  |  0  |  No  |  The minimum number of special characters required\.  | 
|  `p_min_different_chars`  |  number  |  3  |  No  |  The minimum number of different characters required between the old and new password\.  | 
|  `p_disallow_username`  |  boolean  |  true  |  No  |  Set to `true` to disallow the user name in the password\.  | 
|  `p_disallow_reverse`  |  boolean  |  true  |  No  |  Set to `true` to disallow the reverse of the user name in the password\.  | 
|  `p_disallow_db_name`  |  boolean  |  true  |  No  |  Set to `true` to disallow the database or server name in the password\.  | 
|  `p_disallow_simple_strings`  |  boolean  |  true  |  No  |  Set to `true` to disallow simple strings as the password\.  | 
|  `p_disallow_whitespace`  |  boolean  |  false  |  No  |  Set to `true` to disallow white space characters in the password\.  | 
|  `p_disallow_at_sign`  |  boolean  |  false  |  No  |  Set to `true` to disallow the @ character in the password\.  | 

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

### The create\_passthrough\_verify\_fcn procedure<a name="Appendix.Oracle.CommonDBATasks.CustomPassword.Custom"></a>

The `create_passthrough_verify_fcn` procedure is supported for version 12\.1\.0\.2\.v7 and all higher major and minor versions of RDS for Oracle\.

You can create a custom function to verify passwords by using the Amazon RDS procedure `rdsadmin.rdsadmin_password_verify.create_passthrough_verify_fcn`\. The `create_passthrough_verify_fcn` procedure has the following parameters\. 


****  

| Parameter name | Data type | Default | Required | Description | 
| --- | --- | --- | --- | --- | 
|  `p_verify_function_name`  |  varchar2  |  —  |  Yes  |  The name for your custom verification function\. This is a wrapper function that is created for you in the SYS schema, and it doesn't contain any verification logic\. You assign this function to user profiles\.   | 
|  `p_target_owner`  |  varchar2  |  —  |  Yes  |  The schema owner for your custom verification function\.  | 
|  `p_target_function_name`  |  varchar2  |  —  |  Yes  |  The name of your existing custom function that contains the verification logic\. Your custom function must return a boolean\. Your function should return `true` if the password is valid and `false` if the password is invalid\.   | 

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

## Setting up a custom DNS server<a name="Appendix.Oracle.CommonDBATasks.CustomDNS"></a>

Amazon RDS supports outbound network access on your DB instances running Oracle\.  For more information about outbound network access, including prerequisites, see [Configuring UTL\_HTTP access using certificates and an Oracle wallet](Oracle.Concepts.ONA.md)\. 

Amazon RDS Oracle allows Domain Name Service \(DNS\) resolution from a custom DNS server owned by the customer\. You can resolve only fully qualified domain names from your Amazon RDS DB instance through your custom DNS server\. 

After you set up your custom DNS name server, it takes up to 30 minutes to propagate the changes to your DB instance\. After the changes are propagated to your DB instance, all outbound network traffic requiring a DNS lookup queries your DNS server over port 53\. 

To set up a custom DNS server for your Amazon RDS for Oracle DB instance, do the following: 
+ From the DHCP options set attached to your virtual private cloud \(VPC\), set the `domain-name-servers` option to the IP address of your DNS name server\. For more information, see [DHCP options sets](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_DHCP_Options.html)\. 
**Note**  
The `domain-name-servers` option accepts up to four values, but your Amazon RDS DB instance uses only the first value\. 
+ Ensure that your DNS server can resolve all lookup queries, including public DNS names, Amazon EC2 private DNS names, and customer\-specific DNS names\. If the outbound network traffic contains any DNS lookups that your DNS server can't handle, your DNS server must have appropriate upstream DNS providers configured\. 
+ Configure your DNS server to produce User Datagram Protocol \(UDP\) responses of 512 bytes or less\. 
+ Configure your DNS server to produce Transmission Control Protocol \(TCP\) responses of 1024 bytes or less\. 
+ Configure your DNS server to allow inbound traffic from your Amazon RDS DB instances over port 53\. If your DNS server is in an Amazon VPC, the VPC must have a security group that contains inbound rules that permit UDP and TCP traffic on port 53\. If your DNS server is not in an Amazon VPC, it must have appropriate firewall allow\-listing to permit UDP and TCP inbound traffic on port 53\.

  For more information, see [Security groups for your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html) and [Adding and removing rules](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html#AddRemoveRules)\. 
+ Configure the VPC of your Amazon RDS DB instance to allow outbound traffic over port 53\. Your VPC must have a security group that contains outbound rules that allow UDP and TCP traffic on port 53\. 

  For more information, see [Security groups for your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html) and [Adding and removing rules](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html#AddRemoveRules)\. 
+ The routing path between the Amazon RDS DB instance and the DNS server has to be configured correctly to allow DNS traffic\. 
  + If the Amazon RDS DB instance and the DNS server are not in the same VPC, a peering connection has to be set up between them\. For more information, see [What is VPC peering?](https://docs.aws.amazon.com/vpc/latest/peering/Welcome.html) 

## Setting and unsetting system diagnostic events<a name="Appendix.Oracle.CommonDBATasks.SystemEvents"></a>

To set and unset diagnostic events at the session level, you can use the Oracle SQL statement `ALTER SESSION SET EVENTS`\. However, to set events at the system level you can't use Oracle SQL\. Instead, use the system event procedures in the `rdsadmin.rdsadmin_util` package\. The system event procedures are available in the following engine versions:
+ All Oracle Database 21c versions
+ 19\.0\.0\.0\.ru\-2020\-10\.rur\-2020\-10\.r1 and higher Oracle Database 19c versions

  For more information, see [Version 19\.0\.0\.0\.ru\-2020\-10\.rur\-2020\-10\.r1](https://docs.aws.amazon.com/AmazonRDS/latest/OracleReleaseNotes/oracle-version-19-0.html#oracle-version-RU-RUR.19.0.0.0.ru-2020-10.rur-2020-10.r1) in the *Amazon RDS for Oracle Release Notes*\.
+ 12\.2\.0\.1\.ru\-2020\-10\.rur\-2020\-10\.r1 and higher Oracle Database 12c Release 2 \(12\.2\.0\.1\) versions

  For more information, see [Version 12\.2\.0\.1\.ru\-2020\-10\.rur\-2020\-10\.r1](https://docs.aws.amazon.com/AmazonRDS/latest/OracleReleaseNotes/oracle-version-12-2.html#oracle-version-RU-RUR.12.2.0.1.ru-2020-10.rur-2020-10.r1) in the *Amazon RDS for Oracle Release Notes*\.
+ 12\.1\.0\.2\.V22 and higher Oracle Database 12c Release 1 \(12\.1\.0\.2\) versions

  For more information, see [Version 12\.1\.0\.2\.v22](https://docs.aws.amazon.com/AmazonRDS/latest/OracleReleaseNotes/oracle-version-12-1.html#oracle-version-12.1.0.2.v22) in the *Amazon RDS for Oracle Release Notes*\.

para

**Important**  
Internally, the `rdsadmin.rdsadmin_util` package sets events by using the `ALTER SYSTEM SET EVENTS` statement\. This `ALTER SYSTEM` statement isn't documented in the Oracle Database documentation\. Some system diagnostic events can generate large amounts of tracing information, cause contention, or affect database availability\. We recommend that you test specific diagnostic events in your nonproduction database, and only set events in your production database under guidance of Oracle Support\.

### Listing allowed system diagnostic events<a name="Appendix.Oracle.CommonDBATasks.SystemEvents.listing"></a>

To list the system events that you can set, use the Amazon RDS procedure `rdsadmin.rdsadmin_util.list_allowed_system_events`\. This procedure accepts no parameters\.

The following example lists all system events that you can set\.

```
SET SERVEROUTPUT ON
EXEC rdsadmin.rdsadmin_util.list_allowed_system_events;
```

The following sample output lists event numbers and their descriptions\. Use the Amazon RDS procedures `set_system_event` to set these events and `unset_system_event` to unset them\.

```
604   - error occurred at recursive SQL level
942   - table or view does not exist
1401  - inserted value too large for column
1403  - no data found
1410  - invalid ROWID
1422  - exact fetch returns more than requested number of rows
1426  - numeric overflow
1427  - single-row subquery returns more than one row
1476  - divisor is equal to zero
1483  - invalid length for DATE or NUMBER bind variable
1489  - result of string concatenation is too long
1652  - unable to extend temp segment by  in tablespace
1858  - a non-numeric character was found where a numeric was expected
4031  - unable to allocate  bytes of shared memory ("","","","")
6502  - PL/SQL: numeric or value error
10027 - Specify Deadlock Trace Information to be Dumped
10046 - enable SQL statement timing
10053 - CBO Enable optimizer trace
10173 - Dynamic Sampling time-out error
10442 - enable trace of kst for ORA-01555 diagnostics
12008 - error in materialized view refresh path
12012 - error on auto execute of job
12504 - TNS:listener was not given the SERVICE_NAME in CONNECT_DATA
14400 - inserted partition key does not map to any partition
31693 - Table data object  failed to load/unload and is being skipped due to error:
```

**Note**  
The list of the allowed system events can change over time\. To make sure that you have the most recent list of eligible events, use `rdsadmin.rdsadmin_util.list_allowed_system_events`\.

### Setting system diagnostic events<a name="Appendix.Oracle.CommonDBATasks.SystemEvents.setting"></a>

To set a system event, use the Amazon RDS procedure `rdsadmin.rdsadmin_util.set_system_event`\. You can only set events listed in the output of `rdsadmin.rdsadmin_util.list_allowed_system_events`\. The `set_system_event` procedure accepts the following parameters\.


****  

| Parameter name | Data type | Default | Required | Description | 
| --- | --- | --- | --- | --- | 
|  `p_event`  |  number  |  —  |  Yes  |  The system event number\. The value must be one of the event numbers reported by `list_allowed_system_events`\.  | 
|  `p_level`  |  number  |  —  |  Yes  |  The event level\. See the Oracle Database documentation or Oracle Support for descriptions of different level values\.  | 

The procedure `set_system_event` constructs and runs the required `ALTER SYSTEM SET EVENTS` statements according to the following principles:
+ The event type \(`context` or `errorstack`\) is determined automatically\.
+ A statement in the form `ALTER SYSTEM SET EVENTS 'event LEVEL event_level'` sets the context events\. This notation is equivalent to `ALTER SYSTEM SET EVENTS 'event TRACE NAME CONTEXT FOREVER, LEVEL event_level'`\.
+ A statement in the form `ALTER SYSTEM SET EVENTS 'event ERRORSTACK (event_level)'` sets the error stack events\. This notation is equivalent to `ALTER SYSTEM SET EVENTS 'event TRACE NAME ERRORSTACK LEVEL event_level'`\.

The following example sets event 942 at level 3, and event 10442 at level 10\. Sample output is included\.

```
SQL> SET SERVEROUTPUT ON
SQL> EXEC rdsadmin.rdsadmin_util.set_system_event(942,3);
Setting system event 942 with: alter system set events '942 errorstack (3)'

PL/SQL procedure successfully completed.

SQL> EXEC rdsadmin.rdsadmin_util.set_system_event(10442,10);
Setting system event 10442 with: alter system set events '10442 level 10'

PL/SQL procedure successfully completed.
```

### Listing system diagnostic events that are set<a name="Appendix.Oracle.CommonDBATasks.SystemEvents.listing-set"></a>

To list the system events that are currently set, use the Amazon RDS procedure `rdsadmin.rdsadmin_util.list_set_system_events`\. This procedure reports only events set at system level by `set_system_event`\.

The following example lists the active system events\.

```
SET SERVEROUTPUT ON
EXEC rdsadmin.rdsadmin_util.list_set_system_events;
```

The following sample output shows the list of events, the event type, the level at which the events are currently set, and the time when the event was set\.

```
942 errorstack (3) - set at 2020-11-03 11:42:27
10442 level 10 - set at 2020-11-03 11:42:41

PL/SQL procedure successfully completed.
```

### Unsetting system diagnostic events<a name="Appendix.Oracle.CommonDBATasks.SystemEvents.unsetting"></a>

To unset a system event, use the Amazon RDS procedure `rdsadmin.rdsadmin_util.unset_system_event`\. You can only unset events listed in the output of `rdsadmin.rdsadmin_util.list_allowed_system_events`\. The `unset_system_event` procedure accepts the following parameter\.


****  

| Parameter name | Data type | Default | Required | Description | 
| --- | --- | --- | --- | --- | 
|  `p_event`  |  number  |  —  |  Yes  |  The system event number\. The value must be one of the event numbers reported by `list_allowed_system_events`\.  | 

The following example unsets events 942 and 10442\. Sample output is included\.

```
SQL> SET SERVEROUTPUT ON
SQL> EXEC rdsadmin.rdsadmin_util.unset_system_event(942);
Unsetting system event 942 with: alter system set events '942 off'

PL/SQL procedure successfully completed.

SQL> EXEC rdsadmin.rdsadmin_util.unset_system_event(10442);
Unsetting system event 10442 with: alter system set events '10442 off'

PL/SQL procedure successfully completed.
```