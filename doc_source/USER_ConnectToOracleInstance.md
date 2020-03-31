# Connecting to a DB Instance Running the Oracle Database Engine<a name="USER_ConnectToOracleInstance"></a>

After Amazon RDS provisions your Oracle DB instance, you can use any standard SQL client application to connect to the DB instance\. In this topic, you connect to a DB instance that is running the Oracle database engine by using Oracle SQL Developer or SQL\*Plus\. 

For an example that walks you through the process of creating and connecting to a sample DB instance, see [Creating an Oracle DB Instance and Connecting to a Database on an Oracle DB Instance](CHAP_GettingStarted.CreatingConnecting.Oracle.md)\. 

## Finding the Endpoint of Your DB Instance<a name="USER_Endpoint"></a>

Each Amazon RDS DB instance has an endpoint, and each endpoint has the DNS name and port number for the DB instance\. To connect to your DB instance using a SQL client application, you need the DNS name and port number for your DB instance\. 

You can find the endpoint for a DB instance using the Amazon RDS console or the AWS CLI\.

**Note**  
If you are using Kerberos authentication, see [Connecting to Oracle with Kerberos Authentication](oracle-kerberos-connecting.md)\.

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

## Connecting to Your DB Instance Using Oracle SQL Developer<a name="USER_ConnectToOracleInstance.SQLDeveloper"></a>

In this procedure, you connect to your DB instance by using Oracle SQL Developer\. To download a standalone version of this utility, see the [ Oracle SQL Developer Downloads page](http://www.oracle.com/technetwork/developer-tools/sql-developer/downloads/index.html)\.

To connect to your DB instance, you need its DNS name and port number\. For information about finding the DNS name and port number for a DB instance, see [Finding the Endpoint of Your DB Instance](#USER_Endpoint)\.

**To connect to a DB instance using SQL Developer**

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

## Connecting to Your DB Instance Using SQL\*Plus<a name="USER_ConnectToOracleInstance.SQLPlus"></a>

You can use a utility like SQL\*Plus to connect to an Amazon RDS DB instance running Oracle\. To download a standalone version of SQL\*Plus, see [SQL\*Plus User's Guide and Reference](http://docs.oracle.com/database/121/SQPUG/apd.htm#SQPUG157)\. 

To connect to your DB instance, you need its DNS name and port number\. For information about finding the DNS name and port number for a DB instance, see [Finding the Endpoint of Your DB Instance](#USER_Endpoint)\.

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

## Security Group Considerations<a name="USER_ConnectToOracleInstance.Security"></a>

For you to connect to your DB instance, it must be associated with a security group that contains the IP addresses and network configuration that you use to access the DB instance\. You might have associated your DB instance with an appropriate security group when you created it\. If you assigned a default, nonconfigured security group when you created the DB instance, the DB instance firewall prevents connections\.

If you need to create a new security group to enable access, the type of security group that you create depends on which Amazon EC2 platform your DB instance is on\. To determine your platform, see [Determining Whether You Are Using the EC2\-VPC or EC2\-Classic Platform](USER_VPC.FindDefaultVPC.md)\. In general, if your DB instance is on the *EC2\-Classic* platform, you create a DB security group; if your DB instance is on the *VPC* platform, you create a VPC security group\. For information about creating a new security group, see [Controlling Access with Security Groups](Overview.RDSSecurityGroups.md)\. 

After you create the new security group, you modify your DB instance to associate it with the security group\. For more information, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md)\. 

You can enhance security by using SSL to encrypt connections to your DB instance\. For more information, see [Oracle Secure Sockets Layer](Appendix.Oracle.Options.SSL.md)\. 

## Dedicated and Shared Server Processes<a name="USER_ConnectToOracleInstance.SharedServer"></a>

Server processes handle user connections to an Oracle DB instance\. By default, the Oracle DB instance uses dedicated server processes\. With dedicated server processes, each server process services only one user process\. You can optionally configure shared server processes\. With shared server processes, each server process can service multiple user processes\.

You might consider using shared server processes when a high number of user sessions are using too much memory on the server\. You might also consider shared server processes when sessions connect and disconnect very often, resulting in performance issues\. There are also disadvantages to using shared server processes\. For example, they can strain CPU resources, and they are more complicated to configure and administer\.

For more information about dedicated and shared server processes, see [About Dedicated and Shared Server Processes](https://docs.oracle.com/database/121/ADMIN/manproc.htm#ADMIN11166) in the Oracle documentation\. For more information about configuring shared server processes on an Amazon RDS Oracle DB instance, see [How do I configure Amazon RDS for Oracle Database to work with shared servers?](https://aws.amazon.com/premiumsupport/knowledge-center/oracle-db-shared/) in the Knowledge Center\.

## Troubleshooting Connections to Your Oracle DB Instance<a name="USER_ConnectToOracleInstance.Troubleshooting"></a>

The following are issues you might encounter when you try to connect to your Oracle DB instance\. 


****  

| Issue | Troubleshooting Suggestions | 
| --- | --- | 
|  Unable to connect to your DB instance\.   |  For a newly created DB instance, the DB instance has a status of **creating** until it is ready to use\. When the state changes to **available**, you can connect to the DB instance\. Depending on the DB instance class and the amount of storage, it can take up to 20 minutes before the new DB instance is available\.   | 
|  Unable to connect to your DB instance\.   |  If you can't send or receive communications over the port that you specified when you created the DB instance, you can't connect to the DB instance\. Check with your network administrator to verify that the port you specified for your DB instance allows inbound and outbound communication\.   | 
|  Unable to connect to your DB instance\.   |  The access rules enforced by your local firewall and the IP addresses you authorized to access your DB instance in the security group for the DB instance might not match\. The problem is most likely the inbound or outbound rules on your firewall\. You can add or edit an inbound rule in the security group\. For **Source**, choose **My IP**\. This allows access to the DB instance from the IP address detected in your browser\. For more information, see [Amazon Virtual Private Cloud VPCs and Amazon RDS](USER_VPC.md)\. For more information about security groups, see [Controlling Access with Security Groups](Overview.RDSSecurityGroups.md)\.  To walk through the process of setting up rules for your security group, see [Tutorial: Create an Amazon VPC for Use with a DB Instance](CHAP_Tutorials.WebServerDB.CreateVPC.md)\.   | 
|  **Connect failed because target host or object does not exist – Oracle, Error: ORA\-12545 **   |  Make sure that you specified the server name and port number correctly\. For **Server name**, enter the DNS name from the console\.  For information about finding the DNS name and port number for a DB instance, see [Finding the Endpoint of Your DB Instance](#USER_Endpoint)\.  | 
|  **Invalid username/password; logon denied – Oracle, Error: ORA\-01017**   |  You were able to reach the DB instance, but the connection was refused\. This is usually caused by providing an incorrect user name or password\. Verify the user name and password, and then retry\.   | 

For more information on connection issues, see [Can't Connect to Amazon RDS DB Instance](CHAP_Troubleshooting.md#CHAP_Troubleshooting.Connecting)\.