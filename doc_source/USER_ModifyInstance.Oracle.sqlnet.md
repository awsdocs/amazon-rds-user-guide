# Modifying Oracle sqlnet\.ora Parameters<a name="USER_ModifyInstance.Oracle.sqlnet"></a>

The sqlnet\.ora file includes parameters that configure Oracle Net features on Oracle database servers and clients\. Using the parameters in the sqlnet\.ora file, you can modify properties for connections in and out of the database\. 

For more information about why you might set sqlnet\.ora parameters, see [Configuring Profile Parameters](https://docs.oracle.com/database/121/NETAG/profile.htm#NETAG009) in the Oracle documentation\.

## Setting sqlnet\.ora Parameters<a name="USER_ModifyInstance.Oracle.sqlnet.Setting"></a>

Amazon RDS Oracle parameter groups include a subset of sqlnet\.ora parameters\. You set them in the same way that you set other Oracle parameters\. The `sqlnetora.` prefix identifies which parameters are sqlnet\.ora parameters\. For example, in an Oracle parameter group in Amazon RDS, the `default_sdu_size` sqlnet\.ora parameter is `sqlnetora.default_sdu_size`\.

For information about managing parameter groups and setting parameter values, see [Working with DB Parameter Groups](USER_WorkingWithParamGroups.md)\.

## Supported sqlnet\.ora Parameters<a name="USER_ModifyInstance.Oracle.sqlnet.Supported"></a>

Amazon RDS supports the following sqlnet\.ora parameters\. Changes to dynamic sqlnet\.ora parameters take effect immediately\.


****  

| Parameter | Valid Values | Static/Dynamic | Description | 
| --- | --- | --- | --- | 
|  `sqlnetora.default_sdu_size`  |  Oracle 11g – `512` to `65535`  Oracle 12c – `512` to `2097152`   |  Dynamic  |  The session data unit \(SDU\) size, in bytes\.  The SDU is the amount of data that is put in a buffer and sent across the network at one time\.  | 
|  `sqlnetora.diag_adr_enabled`  |  `ON`, `OFF`   |  Dynamic  |  A value that enables or disables Automatic Diagnostic Repository \(ADR\) tracing\.  `ON` specifies that ADR file tracing is used\. `OFF` specifies that non\-ADR file tracing is used\.  | 
|  `sqlnetora.recv_buf_size`  |  `8192` to `268435456`   |  Dynamic  |  The buffer space limit for receive operations of sessions, supported by the TCP/IP, TCP/IP with SSL, and SDP protocols\.   | 
|  `sqlnetora.send_buf_size`  |  `8192` to `268435456`   |  Dynamic  |  The buffer space limit for send operations of sessions, supported by the TCP/IP, TCP/IP with SSL, and SDP protocols\.   | 
|  `sqlnetora.sqlnet.allowed_logon_version_client`  |  `8`, `10`, `11`, `12`   |  Dynamic  |  Minimum authentication protocol version allowed for clients, and servers acting as clients, to establish a connection to Oracle DB instances\.  | 
|  `sqlnetora.sqlnet.allowed_logon_version_server`  |  `8`, `9`, `10`, `11`, `12`, `12a`   |  Dynamic  |  Minimum authentication protocol version allowed to establish a connection to Oracle DB instances\.  | 
|  `sqlnetora.sqlnet.expire_time`  |  `0` to `1440`   |  Dynamic  |  Time interval, in minutes, to send a check to verify that client\-server connections are active\.   | 
|  `sqlnetora.sqlnet.inbound_connect_timeout`  |  `0` or `10` to `7200`   |  Dynamic  |  Time, in seconds, for a client to connect with the database server and provide the necessary authentication information\.   | 
|  `sqlnetora.sqlnet.outbound_connect_timeout`  |  `0` or `10` to `7200`   |  Dynamic  |  Time, in seconds, for a client to establish an Oracle Net connection to the DB instance\.   | 
|  `sqlnetora.sqlnet.recv_timeout`  |  `0` or `10` to `7200`   |  Dynamic  |  Time, in seconds, for a database server to wait for client data after establishing a connection\.   | 
|  `sqlnetora.sqlnet.send_timeout`  |  `0` or `10` to `7200`   |  Dynamic  |  Time, in seconds, for a database server to complete a send operation to clients after establishing a connection\.   | 
|  `sqlnetora.tcp.connect_timeout`  |  `0` or `10` to `7200`   |  Dynamic  |  Time, in seconds, for a client to establish a TCP connection to the database server\.   | 
|  `sqlnetora.trace_level_server`  |  `0`, `4`, `10`, `16`, `OFF`, `USER`, `ADMIN`, `SUPPORT`   |  Dynamic  |  For non\-ADR tracing, turns server tracing on at a specified level or turns it off\.   | 

The default value for each supported sqlnet\.ora parameter is the Oracle default for the release\. For information about default values for Oracle 12c, see [Parameters for the sqlnet\.ora File](https://docs.oracle.com/database/121/NETRF/sqlnet.htm#NETRF006) in the 12c Oracle documentation\. For information about default values for Oracle 11g, see [Parameters for the sqlnet\.ora File](https://docs.oracle.com/cd/E11882_01/network.112/e10835/sqlnet.htm#NETRF006) in the 11g Oracle documentation\.

## Viewing sqlnet\.ora Parameters<a name="USER_ModifyInstance.Oracle.sqlnet.Viewing"></a>

You can view sqlnet\.ora parameters and their settings using the AWS Management Console, the AWS CLI, or a SQL client\.

### Viewing sqlnet\.ora Parameters Using the Console<a name="USER_ModifyInstance.Oracle.sqlnet.Viewing.Console"></a>

For information about viewing parameters in a parameter group, see [Working with DB Parameter Groups](USER_WorkingWithParamGroups.md)\.

In Oracle parameter groups, the `sqlnetora.` prefix identifies which parameters are sqlnet\.ora parameters\.

### Viewing sqlnet\.ora Parameters Using the AWS CLI<a name="USER_ModifyInstance.Oracle.sqlnet.Viewing.CLI"></a>

To view the sqlnet\.ora parameters that were configured in an Oracle parameter group, use the AWS CLI [describe\-db\-parameters](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-parameters.html) command\.

To view the all of the sqlnet\.ora parameters for an Oracle DB instance, call the AWS CLI [download\-db\-log\-file\-portion](https://docs.aws.amazon.com/cli/latest/reference/rds/download-db-log-file-portion.html) command\. Specify the DB instance identifier, the log file name, and the type of output\. 

**Example**  
The following code lists all of the sqlnet\.ora parameters for `mydbinstance`\.   
For Linux, macOS, or Unix:  

```
aws rds download-db-log-file-portion \
--db-instance-identifier mydbinstance \
--log-file-name trace/sqlnet-parameters \
--output text
```
For Windows:  

```
aws rds download-db-log-file-portion ^
--db-instance-identifier mydbinstance ^
--log-file-name trace/sqlnet-parameters ^
--output text
```

### Viewing sqlnet\.ora Parameters Using a SQL Client<a name="USER_ModifyInstance.Oracle.sqlnet.Viewing.SQL"></a>

After you connect to the Oracle DB instance in a SQL client, the following query lists the sqlnet\.ora parameters\.

```
1. SELECT * FROM TABLE
2.    (rdsadmin.rds_file_util.read_text_file(
3.         p_directory => 'BDUMP',
4.         p_filename  => 'sqlnet-parameters'));
```

For information about connecting to an Oracle DB instance in a SQL client, see [Connecting to a DB Instance Running the Oracle Database Engine](USER_ConnectToOracleInstance.md)\.