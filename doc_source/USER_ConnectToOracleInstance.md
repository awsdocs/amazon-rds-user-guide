# Connecting to your Oracle DB instance<a name="USER_ConnectToOracleInstance"></a>

After Amazon RDS provisions your Oracle DB instance, you can use any standard SQL client application to connect to the DB instance\. In this topic, you connect to a DB instance that is running the Oracle database engine by using Oracle SQL Developer or SQL\*Plus\. 

For an example that walks you through the process of creating and connecting to a sample DB instance, see [Creating an Oracle DB instance and connecting to a database on an Oracle DB instance](CHAP_GettingStarted.CreatingConnecting.Oracle.md)\. 

**Topics**
+ [Finding the endpoint of your Oracle DB instance](#USER_Endpoint)
+ [Connecting to your DB instance using Oracle SQL developer](#USER_ConnectToOracleInstance.SQLDeveloper)
+ [Connecting to your DB instance using SQL\*Plus](#USER_ConnectToOracleInstance.SQLPlus)
+ [Considerations for security groups](#USER_ConnectToOracleInstance.Security)
+ [Considerations for process architecture](#USER_ConnectToOracleInstance.SharedServer)
+ [Troubleshooting connections to your Oracle DB instance](#USER_ConnectToOracleInstance.Troubleshooting)
+ [Modifying connection properties using sqlnet\.ora parameters](#USER_ModifyInstance.Oracle.sqlnet)

## Finding the endpoint of your Oracle DB instance<a name="USER_Endpoint"></a>

Each Amazon RDS DB instance has an endpoint, and each endpoint has the DNS name and port number for the DB instance\. To connect to your DB instance using a SQL client application, you need the DNS name and port number for your DB instance\. 

You can find the endpoint for a DB instance using the Amazon RDS console or the AWS CLI\.

**Note**  
If you are using Kerberos authentication, see [Connecting to Oracle with Kerberos authentication](oracle-kerberos-connecting.md)\.

### Console<a name="USER_Endpoint.Console"></a>

**To find the endpoint using the console**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the upper\-right corner of the console, choose the AWS Region of your DB instance\. 

1. Find the DNS name and port number for your DB Instance\. 

   1. Choose **Databases** to display a list of your DB instances\. 

   1. Choose the Oracle DB instance name to display the instance details\. 

   1. On the **Connectivity & security** tab, copy the endpoint\. Also, note the port number\. You need both the endpoint and the port number to connect to the DB instance\.  
![\[Locate DB instance endpoint and port\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/OracleConnect1.png)

### AWS CLI<a name="USER_Endpoint.CLI"></a>

To find the endpoint of an Oracle DB instance by using the AWS CLI, call the [describe\-db\-instances](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-instances.html) command\. 

**Example To find the endpoint using the AWS CLI**  

```
1. aws rds describe-db-instances                                
```
Search for `Endpoint` in the output to find the DNS name and port number for your DB instance\. The `Address` line in the output contains the DNS name\. The following is an example of the JSON endpoint output\.  

```
"Endpoint": {
    "HostedZoneId": "Z1PVIF0B656C1W",
    "Port": 3306,
    "Address": "myinstance.123456789012.us-west-2.rds.amazonaws.com"
},
```

**Note**  
The output might contain information for multiple DB instances\.

## Connecting to your DB instance using Oracle SQL developer<a name="USER_ConnectToOracleInstance.SQLDeveloper"></a>

In this procedure, you connect to your DB instance by using Oracle SQL Developer\. To download a standalone version of this utility, see the [ Oracle SQL developer downloads page](https://www.oracle.com/tools/downloads/sqldev-downloads.html)\.

To connect to your DB instance, you need its DNS name and port number\. For information about finding the DNS name and port number for a DB instance, see [Finding the endpoint of your Oracle DB instance](#USER_Endpoint)\.

**To connect to a DB instance using SQL developer**

1. Start Oracle SQL Developer\.

1. On the **Connections** tab, choose the **add \(\+\)** icon\.  
![\[Oracle SQL Developer with add icon highlighted\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/oracle-sqldev-plus.png)

1. In the **New/Select Database Connection** dialog box, provide the information for your DB instance:
   + For **Connection Name**, enter a name that describes the connection, such as `Oracle-RDS`\.
   + For **Username**, enter the name of the database administrator for the DB instance\.
   + For **Password**, enter the password for the database administrator\.
   + For **Hostname**, enter the DNS name of the DB instance\.
   + For **Port**, enter the port number\.
   + For **SID**, enter the Oracle database SID\.

   The completed dialog box should look similar to the following\.  
![\[Creating a new connection in Oracle SQL Developer\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/oracle-sqldev-newcon.png)

1. Choose **Connect**\.

1. You can now start creating your own databases and running queries against your DB instance and databases as usual\. To run a test query against your DB instance, do the following:

   1. In the **Worksheet** tab for your connection, enter the following SQL query\.

      ```
      SELECT NAME FROM V$DATABASE;                           
      ```

   1. Choose the **execute** icon to run the query\.  
![\[Running a query in Oracle SQL Developer using the execute icon\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/oracle-sqldev-run.png)

      SQL Developer returns the database name\.  
![\[Query results in Oracle SQL Developer\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/oracle-sqldev-results.png)

## Connecting to your DB instance using SQL\*Plus<a name="USER_ConnectToOracleInstance.SQLPlus"></a>

You can use a utility like SQL\*Plus to connect to an Amazon RDS DB instance running Oracle\. To download Oracle Instant Client, which includes a standalone version of SQL\*Plus, see [ Oracle Instant Client Downloads](https://www.oracle.com/database/technologies/instant-client/downloads.html)\. 

To connect to your DB instance, you need its DNS name and port number\. For information about finding the DNS name and port number for a DB instance, see [Finding the endpoint of your Oracle DB instance](#USER_Endpoint)\.

**Example To connect to an Oracle DB instance using SQL\*Plus**  
In the following examples, substitute the user name of your DB instance administrator\. Also, substitute the DNS name for your DB instance, and then include the port number and the Oracle SID\. The SID value is the name of the DB instance's database that you specified when you created the DB instance, and not the name of the DB instance\.   
For Linux, macOS, or Unix:  

```
1. sqlplus 'user_name@(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=dns_name)(PORT=port))(CONNECT_DATA=(SID=database_name)))'                  
```
For Windows:  

```
1. sqlplus user_name@(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=dns_name)(PORT=port))(CONNECT_DATA=(SID=database_name)))             
```
You should see output similar to the following\.  

```
SQL*Plus: Release 12.1.0.2.0 Production on Mon Aug 21 09:42:20 2017                
```
After you enter the password for the user, the SQL prompt appears\.  

```
 SQL>               
```

**Note**  
The shorter format connection string \(Easy connect or EZCONNECT\), such as `sqlplus USER/PASSWORD@LONGER-THAN-63-CHARS-RDS-ENDPOINT-HERE:1521/DATABASE_IDENTIFIER`, might encounter a maximum character limit and should not be used to connect\. 

## Considerations for security groups<a name="USER_ConnectToOracleInstance.Security"></a>

For you to connect to your DB instance, it must be associated with a security group that contains the necessary IP addresses and network configuration\. Your DB instance might use the default security group\. If you assigned a default, nonconfigured security group when you created the DB instance, the firewall prevents connections\. For information about creating a new security group, see [Controlling access with security groups](Overview.RDSSecurityGroups.md)\. 

After you create the new security group, you modify your DB instance to associate it with the security group\. For more information, see [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)\. 

You can enhance security by using SSL to encrypt connections to your DB instance\. For more information, see [Oracle Secure Sockets Layer](Appendix.Oracle.Options.SSL.md)\. 

## Considerations for process architecture<a name="USER_ConnectToOracleInstance.SharedServer"></a>

Server processes handle user connections to an Oracle DB instance\. By default, the Oracle DB instance uses dedicated server processes\. With dedicated server processes, each server process services only one user process\. You can optionally configure shared server processes\. With shared server processes, each server process can service multiple user processes\.

You might consider using shared server processes when a high number of user sessions are using too much memory on the server\. You might also consider shared server processes when sessions connect and disconnect very often, resulting in performance issues\. There are also disadvantages to using shared server processes\. For example, they can strain CPU resources, and they are more complicated to configure and administer\.

For more information about dedicated and shared server processes, see [About dedicated and shared server processes](https://docs.oracle.com/database/121/ADMIN/manproc.htm#ADMIN11166) in the Oracle documentation\. For more information about configuring shared server processes on an RDS for Oracle DB instance, see [How do I configure Amazon RDS for Oracle database to work with shared servers?](http://aws.amazon.com/premiumsupport/knowledge-center/oracle-db-shared/) in the Knowledge Center\.

## Troubleshooting connections to your Oracle DB instance<a name="USER_ConnectToOracleInstance.Troubleshooting"></a>

The following are issues you might encounter when you try to connect to your Oracle DB instance\. 


****  

| Issue | Troubleshooting suggestions | 
| --- | --- | 
|  Unable to connect to your DB instance\.   |  For a newly created DB instance, the DB instance has a status of **creating** until it is ready to use\. When the state changes to **available**, you can connect to the DB instance\. Depending on the DB instance class and the amount of storage, it can take up to 20 minutes before the new DB instance is available\.   | 
|  Unable to connect to your DB instance\.   |  If you can't send or receive communications over the port that you specified when you created the DB instance, you can't connect to the DB instance\. Check with your network administrator to verify that the port you specified for your DB instance allows inbound and outbound communication\.   | 
|  Unable to connect to your DB instance\.   |  The access rules enforced by your local firewall and the IP addresses you authorized to access your DB instance in the security group for the DB instance might not match\. The problem is most likely the inbound or outbound rules on your firewall\. You can add or edit an inbound rule in the security group\. For **Source**, choose **My IP**\. This allows access to the DB instance from the IP address detected in your browser\. For more information, see [Amazon Virtual Private Cloud VPCs and Amazon RDS](USER_VPC.md)\. For more information about security groups, see [Controlling access with security groups](Overview.RDSSecurityGroups.md)\.  To walk through the process of setting up rules for your security group, see [Tutorial: Create an Amazon VPC for use with a DB instance \(IPv4 only\)](CHAP_Tutorials.WebServerDB.CreateVPC.md)\.   | 
|  **Connect failed because target host or object does not exist – Oracle, Error: ORA\-12545 **   |  Make sure that you specified the server name and port number correctly\. For **Server name**, enter the DNS name from the console\.  For information about finding the DNS name and port number for a DB instance, see [Finding the endpoint of your Oracle DB instance](#USER_Endpoint)\.  | 
|  **Invalid username/password; logon denied – Oracle, Error: ORA\-01017**   |  You were able to reach the DB instance, but the connection was refused\. This is usually caused by providing an incorrect user name or password\. Verify the user name and password, and then retry\.   | 
|  **TNS:listener does not currently know of SID given in connect descriptor \- Oracle, ERROR: ORA\-12505**   |  Ensure the correct SID is entered\. The SID is the same as your DB name\. Find the DB name in the Configuration tab of the Databases page for your instance\. You can also find the DB name using the AWS CLI: aws rds describe\-db\-instances \-\-query 'DBInstances\[\*\]\.\[DBInstanceIdentifier,DBName\]' \-\-output text  | 

For more information on connection issues, see [Can't connect to Amazon RDS DB instance](CHAP_Troubleshooting.md#CHAP_Troubleshooting.Connecting)\.

## Modifying connection properties using sqlnet\.ora parameters<a name="USER_ModifyInstance.Oracle.sqlnet"></a>

The sqlnet\.ora file includes parameters that configure Oracle Net features on Oracle database servers and clients\. Using the parameters in the sqlnet\.ora file, you can modify properties for connections in and out of the database\. 

For more information about why you might set sqlnet\.ora parameters, see [Configuring profile parameters](https://docs.oracle.com/database/121/NETAG/profile.htm#NETAG009) in the Oracle documentation\.

### Setting sqlnet\.ora parameters<a name="USER_ModifyInstance.Oracle.sqlnet.Setting"></a>

Amazon RDS for Oracle parameter groups include a subset of sqlnet\.ora parameters\. You set them in the same way that you set other Oracle parameters\. The `sqlnetora.` prefix identifies which parameters are sqlnet\.ora parameters\. For example, in an Oracle parameter group in Amazon RDS, the `default_sdu_size` sqlnet\.ora parameter is `sqlnetora.default_sdu_size`\.

For information about managing parameter groups and setting parameter values, see [Working with parameter groups](USER_WorkingWithParamGroups.md)\.

### Supported sqlnet\.ora parameters<a name="USER_ModifyInstance.Oracle.sqlnet.Supported"></a>

Amazon RDS supports the following sqlnet\.ora parameters\. Changes to dynamic sqlnet\.ora parameters take effect immediately\.


****  

| Parameter | Valid values | Static/Dynamic | Description | 
| --- | --- | --- | --- | 
|  `sqlnetora.default_sdu_size`  |  Oracle 12c – `512` to `2097152`   |  Dynamic  |  The session data unit \(SDU\) size, in bytes\.  The SDU is the amount of data that is put in a buffer and sent across the network at one time\.  | 
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
|  `sqlnetora.trace_level_server`  |  `0`, `4`, `10`, `16`, `OFF`, `USER`, `ADMIN`, `SUPPORT`  |  Dynamic  | For non\-ADR tracing, turns server tracing on at a specified level or turns it off\. | 

The default value for each supported sqlnet\.ora parameter is the Oracle default for the release\. For information about default values for Oracle Database 12c, see [Parameters for the sqlnet\.ora file](https://docs.oracle.com/database/121/NETRF/sqlnet.htm#NETRF006) in the Oracle Database 12c documentation\. 

### Viewing sqlnet\.ora parameters<a name="USER_ModifyInstance.Oracle.sqlnet.Viewing"></a>

You can view sqlnet\.ora parameters and their settings using the AWS Management Console, the AWS CLI, or a SQL client\.

#### Viewing sqlnet\.ora parameters using the console<a name="USER_ModifyInstance.Oracle.sqlnet.Viewing.Console"></a>

For information about viewing parameters in a parameter group, see [Working with parameter groups](USER_WorkingWithParamGroups.md)\.

In Oracle parameter groups, the `sqlnetora.` prefix identifies which parameters are sqlnet\.ora parameters\.

#### Viewing sqlnet\.ora parameters using the AWS CLI<a name="USER_ModifyInstance.Oracle.sqlnet.Viewing.CLI"></a>

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

#### Viewing sqlnet\.ora parameters using a SQL client<a name="USER_ModifyInstance.Oracle.sqlnet.Viewing.SQL"></a>

After you connect to the Oracle DB instance in a SQL client, the following query lists the sqlnet\.ora parameters\.

```
1. SELECT * FROM TABLE
2.    (rdsadmin.rds_file_util.read_text_file(
3.         p_directory => 'BDUMP',
4.         p_filename  => 'sqlnet-parameters'));
```

For information about connecting to an Oracle DB instance in a SQL client, see [Connecting to your Oracle DB instance](#USER_ConnectToOracleInstance)\.