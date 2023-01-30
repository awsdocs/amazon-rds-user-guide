# Connecting to your DB instance using SQL\*Plus<a name="USER_ConnectToOracleInstance.SQLPlus"></a>

You can use a utility like SQL\*Plus to connect to an Amazon RDS DB instance running Oracle\. To download Oracle Instant Client, which includes a standalone version of SQL\*Plus, see [ Oracle Instant Client Downloads](https://www.oracle.com/database/technologies/instant-client/downloads.html)\. 

To connect to your DB instance, you need its DNS name and port number\. For information about finding the DNS name and port number for a DB instance, see [Finding the endpoint of your Oracle DB instance](USER_Endpoint.md)\.

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
The shorter format connection string \(EZ Connect\), such as `sqlplus USER/PASSWORD@longer-than-63-chars-rds-endpoint-here:1521/database-identifier`, might encounter a maximum character limit, so you we recommend that you don't use it to connect\.