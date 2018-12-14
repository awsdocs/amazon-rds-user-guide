# Common DBA System Tasks for Oracle DB Instances<a name="Appendix.Oracle.CommonDBATasks.System"></a>

This section describes how you can perform common DBA tasks related to the system on your Amazon RDS DB instances running Oracle\. To deliver a managed service experience, Amazon RDS doesn't provide shell access to DB instances, and restricts access to certain system procedures and tables that require advanced privileges\. 

## Disconnecting a Session<a name="Appendix.Oracle.CommonDBATasks.DisconnectingSession"></a>

You can use the Amazon RDS procedure `rdsadmin.rdsadmin_util.disconnect` to disconnect the current session by ending the dedicated server process\. The `disconnect` procedure has the following parameters\. 


****  

| Parameter Name | Data Type | Default | Required | Description | 
| --- | --- | --- | --- | --- | 
| `sid` | number | — | required | The session identifier\. | 
| `serial` | number | — | required | The serial number of the session\. | 
| `method` | varchar | 'IMMEDIATE' | optional | Valid values are `'IMMEDIATE'` or `'POST_TRANSACTION'`\. | 

The following example disconnects a session:

```
1. begin
2.     rdsadmin.rdsadmin_util.disconnect(
3.         sid    => sid, 
4.         serial => serial_number);
5. end;
6. /
```

To get the session identifier and the session serial number, query the `V$SESSION` view\. The following example gets all sessions for the user `AWSUSER`: 

```
1. select SID, SERIAL#, STATUS from V$SESSION where USERNAME = 'AWSUSER';
```

The database must be open to use this method\. For more information about disconnecting a session, see [ALTER SYSTEM](http://docs.oracle.com/cd/E11882_01/server.112/e41084/statements_2014.htm#SQLRF53166) in the Oracle documentation\. 

## Killing a Session<a name="Appendix.Oracle.CommonDBATasks.KillingSession"></a>

You can use the Amazon RDS procedure `rdsadmin.rdsadmin_util.kill` to kill a session\. The `kill` procedure has the following parameters\. 


****  

| Parameter Name | Data Type | Default | Required | Description | 
| --- | --- | --- | --- | --- | 
| `sid` | number | — | required | The session identifier\. | 
| `serial` | number | — | required | The serial number of the session\. | 
| `method` | varchar | null | optional |  Valid values are `'IMMEDIATE'` or `'PROCESS'`\.   | 

The following example kills a session:

```
1. begin
2.     rdsadmin.rdsadmin_util.kill(
3.         sid    => sid, 
4.         serial => serial_number);
5. end;
6. /
```

To get the session identifier and the session serial number, query the `V$SESSION` view\. The following example gets all sessions for the user `AWSUSER`: 

```
1. select SID, SERIAL#, STATUS from V$SESSION where USERNAME = 'AWSUSER';
```

You can specify either `IMMEDIATE` or `PROCESS` as a value for the `method` parameter\. Specifying `PROCESS` as the enables you to kill the processes associated with a session\. You should only do this if killing the session using `IMMEDIATE` as the `method` value was unsuccessful\. 

## Enabling and Disabling Restricted Sessions<a name="Appendix.Oracle.CommonDBATasks.RestrictedSession"></a>

You can use the Amazon RDS procedure `rdsadmin.rdsadmin_util.restricted_session` to enable and disable restricted sessions\. The `restricted_session` procedure has the following parameters\. 


****  

| Parameter Name | Data Type | Default | Required | Description | 
| --- | --- | --- | --- | --- | 
| `p_enable` | boolean | true | optional |  Set to `true` to enable restricted sessions, `false` to disable restricted sessions\.   | 

The following example shows how to enable and disable restricted sessions\. 

```
 1. /* Verify that the database is currently unrestricted. */
 2. 
 3. select LOGINS from V$INSTANCE;
 4.  
 5. LOGINS
 6. -------
 7. ALLOWED
 8. 
 9. 
10. /* Enable restricted sessions */
11. 
12. exec rdsadmin.rdsadmin_util.restricted_session(p_enable => true);
13.  
14. 
15. /* Verify that the database is now restricted. */
16. 
17. select LOGINS from V$INSTANCE;
18.  
19. LOGINS
20. ----------
21. RESTRICTED
22.  
23. 
24. /* Disable restricted sessions */
25. 
26. exec rdsadmin.rdsadmin_util.restricted_session(p_enable => false);
27.  
28. 
29. /* Verify that the database is now unrestricted again. */
30. 
31. select LOGINS from V$INSTANCE;
32.  
33. LOGINS
34. -------
35. ALLOWED
```

## Flushing the Shared Pool<a name="Appendix.Oracle.CommonDBATasks.FlushingSharedPool"></a>

You can use the Amazon RDS procedure `rdsadmin.rdsadmin_util.flush_shared_pool` to flush the shared pool\. The `flush_shared_pool` procedure has no parameters\. 

The following example flushes the shared pool\.

```
1. exec rdsadmin.rdsadmin_util.flush_shared_pool;
```

## Flushing the Buffer Cache<a name="Appendix.Oracle.CommonDBATasks.FlushingBufferCache"></a>

You can use the Amazon RDS procedure `rdsadmin.rdsadmin_util.flush_buffer_cache` flush the buffer cache\. The `flush_buffer_cache` procedure has no parameters\. 

The following example flushes the buffer cache\.

```
1. exec rdsadmin.rdsadmin_util.flush_buffer_cache;
```

## Granting SELECT or EXECUTE Privileges to SYS Objects<a name="Appendix.Oracle.CommonDBATasks.TransferPrivileges"></a>

Usually you transfer privileges by using roles, which can contain many objects\. You can grant privileges to a single object by using the Amazon RDS procedure `rdsadmin.rdsadmin_util.grant_sys_object`\. The procedure only grants privileges that the master account already has via a role or direct grant\. 

The `grant_sys_object` procedure has the following parameters\. 


****  

| Parameter Name | Data Type | Default | Required | Description | 
| --- | --- | --- | --- | --- | 
| `p_obj_name` | varchar2 | — | required |  The name of the object to grant privileges for\. The object can be a directory, function, package, procedure, sequence, table, or view\. Object names must be spelled exactly as they appear in `DBA_OBJECTS`\. Most system objects are defined in upper case, so we recommend you try that first\.   | 
| `p_grantee` | varchar2 | — | required |  The name of the object to grant privileges to\. The object can be a schema or a role\.   | 
| `p_privilege` | varchar2 | null | required | — | 
| `p_grant_option` | boolean | false | optional |  Set to `true` to use the with grant option\. The `p_grant_option` parameter is supported for Oracle versions 11\.2\.0\.4\.v8 and later, and 12\.1\.0\.2\.v4 and later\.   | 

The following example grants select privileges on an object named `V_$SESSION` to a user named `USER1`: 

```
1. begin
2.     rdsadmin.rdsadmin_util.grant_sys_object(
3.         p_obj_name  => 'V_$SESSION',
4.         p_grantee   => 'USER1',
5.         p_privilege => 'SELECT');
6. end;
7. /
```

The following example grants select privileges on an object named `V_$SESSION` to a user named `USER1` with the grant option: 

```
1. begin
2.     rdsadmin.rdsadmin_util.grant_sys_object(
3.         p_obj_name     => 'V_$SESSION',
4.         p_grantee      => 'USER1',
5.         p_privilege    => 'SELECT',
6.         p_grant_option => true);
7. end;
8. /
```

To be able to grant privileges on an object, your account must have those privileges granted to it directly with the grant option, or via a role granted using `with admin option`\. In the most common case, you may want to grant `SELECT` on a DBA view that has been granted to the `SELECT_CATALOG_ROLE` role\. If that role isn't already directly granted to your user using `with admin option`, then you won't be able to transfer the privilege\. If you have the DBA privilege, then you can grant the role directly to another user\. 

The following example grants the `SELECT_CATALOG_ROLE` and `EXECUTE_CATALOG_ROLE` to `USER1`\. Since the `with admin option` is used, `USER1` can now grant access to SYS objects that have been granted to `SELECT_CATALOG_ROLE`\. 

```
1. grant SELECT_CATALOG_ROLE to USER1 with admin option; 
2. grant EXECUTE_CATALOG_ROLE to USER1 with admin option;
```

Objects already granted to `PUBLIC` do not need to be re\-granted\. If you use the `grant_sys_object` procedure to re\-grant access, the procedure call succeeds\. 

## Revoking SELECT or EXECUTE Privileges on SYS Objects<a name="Appendix.Oracle.CommonDBATasks.RevokePrivileges"></a>

 You can revoke privileges on a single object by using the Amazon RDS procedure `rdsadmin.rdsadmin_util.revoke_sys_object`\. The procedure only revokes privileges that the master account already has via a role or direct grant\. 

The `revoke_sys_object` procedure has the following parameters\. 


****  

| Parameter Name | Data Type | Default | Required | Description | 
| --- | --- | --- | --- | --- | 
| `p_obj_name` | varchar2 | — | required |  The name of the object to revoke privileges for\. The object can be a directory, function, package, procedure, sequence, table, or view\. Object names must be spelled exactly as they appear in `DBA_OBJECTS`\. Most system objects are defined in upper case, so we recommend you try that first\.   | 
| `p_revokee` | varchar2 | — | required |  The name of the object to revoke privileges for\. The object can be a schema or a role\.   | 
| `p_privilege` | varchar2 | null | required | — | 

The following example revokes select privileges on an object named `V_$SESSION` from a user named `USER1`: 

```
1. begin
2.     rdsadmin.rdsadmin_util.revoke_sys_object(
3.         p_obj_name  => 'V_$SESSION',
4.         p_revokee   => 'USER1',
5.         p_privilege => 'SELECT');
6. end;
7. /
```

## Granting Privileges to Non\-Master Users<a name="Appendix.Oracle.CommonDBATasks.PermissionsNonMasters"></a>

You can grant select privileges for many objects in the `SYS` schema by using the `SELECT_CATALOG_ROLE` role\. The `SELECT_CATALOG_ROLE` role gives users `SELECT` privileges on data dictionary views\. The following example grants the role `SELECT_CATALOG_ROLE` to a user named `user1`\. 

```
1. grant SELECT_CATALOG_ROLE to user1;
```

You can grant execute privileges for many objects in the `SYS` schema by using the `EXECUTE_CATALOG_ROLE` role\. The `EXECUTE_CATALOG_ROLE` role gives users `EXECUTE` privileges for packages and procedures in the data dictionary\. The following example grants the role `EXECUTE_CATALOG_ROLE` to a user named *user1*: 

```
1. grant EXECUTE_CATALOG_ROLE to user1;
```

The following example gets the permissions that the roles `SELECT_CATALOG_ROLE` and `EXECUTE_CATALOG_ROLE` allow: 

```
1.   select * 
2.     from ROLE_TAB_PRIVS  
3.    where ROLE in ('SELECT_CATALOG_ROLE','EXECUTE_CATALOG_ROLE') 
4. order by ROLE, TABLE_NAME asc;
```

The following example creates a non\-master user named `user1`, grants the `CREATE SESSION` privilege, and grants the `SELECT` privilege on a database named *sh\.sales*: 

```
1. create user user1 identified by password;
2. grant CREATE SESSION to user1;
3. grant SELECT on sh.sales TO user1;
```

## Modifying DBMS\_SCHEDULER Jobs<a name="Appendix.Oracle.CommonDBATasks.ModifyScheduler"></a>

You can use the Oracle procedure `dbms_scheduler.set_attribute` to modify DBMS\_SCHEDULER jobs\. For more information, see [DBMS\_SCHEDULER](https://docs.oracle.com/database/121/ARPLS/d_sched.htm#ARPLS72235) and [SET\_ATTRIBUTE Procedure](https://docs.oracle.com/database/121/ARPLS/d_sched.htm#ARPLS72399) in the Oracle documentation\. 

When working with Amazon RDS DB instances, prepend the schema name `SYS` to the object name\. The following example sets the resource plan attribute for the Monday window object\. 

```
1. begin
2.     dbms_scheduler.set_attribute(
3.         name      => 'SYS.MONDAY_WINDOW',
4.         attribute => 'RESOURCE_PLAN',
5.         value     => 'resource_plan_1');
6. end;
7. /
```

## Creating Custom Functions to Verify Passwords<a name="Appendix.Oracle.CommonDBATasks.CustomPassword"></a>

You can create a custom password verification function in two ways\. If you want to use standard verification logic, and to store your function in the `SYS` schema, use the `create_verify_function` procedure\. If you want to use custom verification logic, or you don't want to store your function in the `SYS` schema, use the `create_passthrough_verify_fcn` procedure\. 

### The `create_verify_function` Procedure<a name="Appendix.Oracle.CommonDBATasks.CustomPassword.Standard"></a>

The `create_verify_function` procedure is supported for Oracle version 11\.2\.0\.4\.v9 and later, and 12\.1\.0\.2\.v5 and later\.

You can create a custom function to verify passwords by using the Amazon RDS procedure `rdsadmin.rdsadmin_password_verify.create_verify_function`\. The `create_verify_function` procedure has the following parameters\. 


****  

| Parameter Name | Data Type | Default | Required | Description | 
| --- | --- | --- | --- | --- | 
| `p_verify_function_name` | varchar2 | — | required |  The name for your custom function\. This function is created for you in the SYS schema\. You assign this function to user profiles\.   | 
| `p_min_length` | number | 8 | optional | The minimum number of characters required\. | 
| `p_max_length` | number | 256 | optional | The maximum number of characters allowed\. | 
| `p_min_letters` | number | 1 | optional | The minimum number of letters required\. | 
| `p_min_uppercase` | number | 0 | optional | The minimum number of uppercase letters required\. | 
| `p_min_lowercase` | number | 0 | optional | The minimum number of lowercase letters required\. | 
| `p_min_digits` | number | 1 | optional | The minimum number of digits required\. | 
| `p_min_special` | number | 0 | optional | The minimum number of special characters required\. | 
| `p_min_different_chars` | number | 3 | optional | The minimum number of distinct characters required\. | 
| `p_disallow_username` | boolean | true | optional | Set to `true` to disallow the username in the password\. | 
| `p_disallow_reverse` | boolean | true | optional | Set to `true` to disallow the reverse of the username in the password\. | 
| `p_disallow_db_name` | boolean | true | optional | Set to `true` to disallow the database or server name in the password\. | 
| `p_disallow_simple_strings` | boolean | true | optional | Set to `true` to disallow simple strings as the password\. | 
| `p_disallow_whitespace` | boolean | false | optional | Set to `true` to disallow white space characters in the password\. | 
| `p_disallow_at_sign` | boolean | false | optional | Set to `true` to disallow the @ character in the password\. | 

You can create multiple password verification functions\.

There are restrictions on the name of your custom function\. Your custom function can't have the same name as an existing system object, the name can be no more than 30 characters long, and the name must include one of the following strings: `PASSWORD`, `VERIFY`, `COMPLEXITY`, `ENFORCE`, or `STRENGTH`\. 

The following example creates a function named `CUSTOM_PASSWORD_FUNCTION`\. The function requires that a password has at least 12 characters, 2 uppercase characters, 1 digit, and 1 special character, and that the password disallows the @ character\. 

```
 1. begin
 2.     rdsadmin.rdsadmin_password_verify.create_verify_function(
 3.         p_verify_function_name => 'CUSTOM_PASSWORD_FUNCTION', 
 4.         p_min_length           => 12, 
 5.         p_min_uppercase        => 2, 
 6.         p_min_digits           => 1, 
 7.         p_min_special          => 1,
 8.         p_disallow_at_sign     => true);
 9. end;
10. /
```

To see the text of your verification function, query `DBA_SOURCE`\. The following example gets the text of a custom password function named `CUSTOM_PASSWORD_FUNCTION`\. 

```
1. col text format a150
2. 
3.   select TEXT 
4.     from DBA_SOURCE 
5.    where OWNER = 'SYS' and NAME = 'CUSTOM_PASSWORD_FUNCTION' 
6. order by LINE;
```

To associate your verification function with a user profile, use `alter profile`\. The following example associates a verification function with the `DEFAULT` user profile\. 

```
1. alter profile DEFAULT limit PASSWORD_VERIFY_FUNCTION CUSTOM_PASSWORD_FUNCTION;
```

To see what user profiles are associated with what verification functions, query `DBA_PROFILES`\. The following example gets the profiles that are associated with the custom verification function named `CUSTOM_PASSWORD_FUNCTION`\. 

```
1. select * 
2.   from DBA_PROFILES 
3.  where RESOURCE = 'PASSWORD' and LIMIT = 'CUSTOM_PASSWORD_FUNCTION';
4. 
5. 
6. PROFILE                    RESOURCE_NAME                     RESOURCE  LIMIT
7. -------------------------  --------------------------------  --------  ------------------------
8. DEFAULT                    PASSWORD_VERIFY_FUNCTION          PASSWORD  CUSTOM_PASSWORD_FUNCTION
```

The following example gets all profiles and the password verification functions that they are associated with\. 

```
1. select * 
2.   from DBA_PROFILES 
3.  where RESOURCE_NAME = 'PASSWORD_VERIFY_FUNCTION';
4. 
5. 
6. PROFILE                    RESOURCE_NAME                     RESOURCE  LIMIT
7. -------------------------  --------------------------------  --------  ------------------------
8. DEFAULT                    PASSWORD_VERIFY_FUNCTION          PASSWORD  CUSTOM_PASSWORD_FUNCTION
9. RDSADMIN                   PASSWORD_VERIFY_FUNCTION          PASSWORD  NULL
```

### The `create_passthrough_verify_fcn` Procedure<a name="Appendix.Oracle.CommonDBATasks.CustomPassword.Custom"></a>

The `create_passthrough_verify_fcn` procedure is supported for Oracle version 11\.2\.0\.4\.v11 and later, and 12\.1\.0\.2\.v7 and later\.

You can create a custom function to verify passwords by using the Amazon RDS procedure `rdsadmin.rdsadmin_password_verify.create_passthrough_verify_fcn`\. The `create_passthrough_verify_fcn` procedure has the following parameters\. 


****  

| Parameter Name | Data Type | Default | Required | Description | 
| --- | --- | --- | --- | --- | 
| `p_verify_function_name` | varchar2 | — | required |  The name for your custom verification function\. This is a wrapper function that is created for you in the SYS schema, and it doesn't contain any verification logic\. You assign this function to user profiles\.   | 
| `p_target_owner` | varchar2 | — | required | The schema owner for your custom verification function\. | 
| `p_target_function_name` | varchar2 | — | required |  The name of your existing custom function that contains the verification logic\. Your custom function must return a boolean\. Your function should return `true` if the password is valid and `false` if the password is invalid\.   | 

The following example creates a password verification function that uses the logic from the function named `PASSWORD_LOGIC_EXTRA_STRONG`\. 

```
1. begin
2.     rdsadmin.rdsadmin_password_verify.create_passthrough_verify_fcn(
3.         p_verify_function_name => 'CUSTOM_PASSWORD_FUNCTION', 
4.         p_target_owner         => 'TEST_USER',
5.         p_target_function_name => 'PASSWORD_LOGIC_EXTRA_STRONG');
6. end;
7. /
```

To associate the verification function with a user profile, use `alter profile`\. The following example associates the verification function with the `DEFAULT` user profile\. 

```
1. alter profile DEFAULT limit PASSWORD_VERIFY_FUNCTION CUSTOM_PASSWORD_FUNCTION;
```

## Setting Up a Custom DNS Server<a name="Appendix.Oracle.CommonDBATasks.CustomDNS"></a>

Amazon RDS supports outbound network access on your DB instances running Oracle\.  For more information about outbound network access, including prerequisites, see [Using utl\_http, utl\_tcp, and utl\_smtp with an Oracle DB Instance](CHAP_Oracle.md#Oracle.Concepts.ONA)\. 

Amazon RDS Oracle allows Domain Name Service \(DNS\) resolution from a custom DNS server owned by the customer\. You can resolve only fully qualified domain names from your Amazon RDS DB instance through your custom DNS server\. 

After you set up your custom DNS name server, it takes up to 30 minutes to propagate the changes to your DB instance\. After the changes are propagated to your DB instance, all outbound network traffic requiring a DNS lookup queries your DNS server over port 53\. 

To set up a custom DNS server for your Oracle Amazon RDS DB instance, do the following: 
+ From the DHCP options set attached to your VPC, set the `domain-name-servers` option to the IP address of your DNS name server\. For more information, see [DHCP Options Sets](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_DHCP_Options.html)\. 
**Note**  
The `domain-name-servers` option accepts up to four values, but your Amazon RDS DB instance uses only the first value\. 
+ Ensure that your DNS server can resolve all lookup queries, including public DNS names, Amazon EC2 private DNS names, and customer\-specific DNS names\. If the outbound network traffic contains any DNS lookups that your DNS server can't handle, your DNS server must have appropriate upstream DNS providers configured\. 
+ Configure your DNS server to produce User Datagram Protocol \(UDP\) responses of 512 bytes or less\. 
+ Configure your DNS server to produce Transmission Control Protocol \(TCP\) responses of 1024 bytes or less\. 
+ Configure your DNS server to allow inbound traffic from your Amazon RDS DB instances over port 53\. If your DNS server is in an Amazon VPC, the VPC must have a security group that contains inbound rules that allow UDP and TCP traffic on port 53\. If your DNS server is not in an Amazon VPC, it must have appropriate firewall whitelisting to allow UDP and TCP inbound traffic on port 53\. 

  For more information, see [Security Groups for Your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html) and [Adding and Removing Rules](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html#AddRemoveRules)\. 
+ Configure the VPC of your Amazon RDS DB instance to allow outbound traffic over port 53\. Your VPC must have a security group that contains outbound rules that allow UDP and TCP traffic on port 53\. 

  For more information, see [Security Groups for Your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html) and [Adding and Removing Rules](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html#AddRemoveRules)\. 
+ The routing path between the Amazon RDS DB instance and the DNS server has to be configured correctly to allow DNS traffic\. 
  + If the Amazon RDS DB instance and the DNS server are not in the same VPC, a peering connection has to be setup between them\. For more information, see [What is VPC Peering?](https://docs.aws.amazon.com/vpc/latest/peering/Welcome.html) 

## Related Topics<a name="Appendix.Oracle.CommonDBATasks.System.Related"></a>
+ [Common DBA Database Tasks for Oracle DB Instances](Appendix.Oracle.CommonDBATasks.Database.md)
+ [Common DBA Log Tasks for Oracle DB Instances](Appendix.Oracle.CommonDBATasks.Log.md)
+ [Common DBA Miscellaneous Tasks for Oracle DB Instances](Appendix.Oracle.CommonDBATasks.Misc.md)