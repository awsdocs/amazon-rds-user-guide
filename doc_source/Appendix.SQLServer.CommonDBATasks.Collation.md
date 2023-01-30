# Collations and character sets for Microsoft SQL Server<a name="Appendix.SQLServer.CommonDBATasks.Collation"></a>

SQL Server supports collations at multiple levels\. You set the default server collation when you create the DB instance\. You can override the collation in the database, table, or column level\.

**Topics**
+ [Server\-level collation for Microsoft SQL Server](#Appendix.SQLServer.CommonDBATasks.Collation.Server)
+ [Database\-level collation for Microsoft SQL Server](#Appendix.SQLServer.CommonDBATasks.Collation.Database-Table-Column)

## Server\-level collation for Microsoft SQL Server<a name="Appendix.SQLServer.CommonDBATasks.Collation.Server"></a>

When you create a Microsoft SQL Server DB instance, you can set the server collation that you want to use\. If you don't choose a different collation, the server\-level collation defaults to SQL\_Latin1\_General\_CP1\_CI\_AS\. The server collation is applied by default to all databases and database objects\.

**Note**  
You can't change the collation when you restore from a DB snapshot\.

Currently, Amazon RDS supports the following server collations:


| Collation | Description | 
| --- | --- | 
|  Chinese\_PRC\_BIN2  |  Chinese\-PRC, binary code point sort order  | 
|  Chinese\_PRC\_CI\_AS  |  Chinese\-PRC, case\-insensitive, accent\-sensitive, kanatype\-insensitive, width\-insensitive  | 
|       Chinese\_Taiwan\_Stroke\_CI\_AS  |       Chinese\-Taiwan\-Stroke, case\-insensitive, accent\-sensitive, kanatype\-insensitive, width\-insensitive  | 
|  Danish\_Norwegian\_CI\_AS  |  Danish\-Norwegian, case\-insensitive, accent\-sensitive, kanatype\-insensitive, width\-insensitive  | 
|  Finnish\_Swedish\_CI\_AS  |  Finnish, Swedish, and Swedish \(Finland\), case\-insensitive, accent\-sensitive, kanatype\-insensitive, width\-insensitive  | 
|  French\_CI\_AS  |  French, case\-insensitive, accent\-sensitive, kanatype\-insensitive, width\-insensitive  | 
|  Hebrew\_BIN  |  Hebrew, binary sort  | 
|  Hebrew\_CI\_AS  |  Hebrew, case\-insensitive, accent\-sensitive, kanatype\-insensitive, width\-insensitive  | 
|  Japanese\_BIN  | Japanese, binary sort | 
|  Japanese\_CI\_AS  |  Japanese, case\-insensitive, accent\-sensitive, kanatype\-insensitive, width\-insensitive  | 
|  Japanese\_CS\_AS  |  Japanese, case\-sensitive, accent\-sensitive, kanatype\-insensitive, width\-insensitive  | 
|  Japanese\_XJIS\_140\_CI\_AS  |  Japanese\-XJIS\-140, case\-insensitive, accent\-sensitive, kanatype\-insensitive, width\-insensitive, supplementary characters, variation selector insensitive  | 
|  Japanese\_XJIS\_140\_CI\_AS\_KS\_VSS  |  Japanese\-XJIS\-140, case\-insensitive, accent\-sensitive, kanatype\-sensitive, width\-insensitive, supplementary characters, variation selector sensitive  | 
|  Japanese\_XJIS\_140\_CI\_AS\_VSS  |  Japanese\-XJIS\-140, case\-insensitive, accent\-sensitive, kanatype\-insensitive, width\-insensitive, supplementary characters, variation selector sensitive  | 
|  Korean\_Wansung\_CI\_AS  |  Korean\-Wansung, case\-insensitive, accent\-sensitive, kanatype\-insensitive, width\-insensitive  | 
|  Latin1\_General\_100\_BIN  |  Latin1\-General\-100, binary sort  | 
|  Latin1\_General\_100\_BIN2  |  Latin1\-General\-100, binary code point sort order  | 
|  Latin1\_General\_100\_CI\_AS  |  Latin1\-General\-100, case\-insensitive, accent\-sensitive, kanatype\-insensitive, width\-insensitive  | 
|  Latin1\_General\_BIN  |  Latin1\-General, binary sort  | 
|  Latin1\_General\_BIN2  |  Latin1\-General, binary code point sort order  | 
|  Latin1\_General\_CI\_AI  |  Latin1\-General, case\-insensitive, accent\-insensitive, kanatype\-insensitive, width\-insensitive  | 
|  Latin1\_General\_CI\_AS  |  Latin1\-General, case\-insensitive, accent\-sensitive, kanatype\-insensitive, width\-insensitive  | 
|  Latin1\_General\_CI\_AS\_KS  |  Latin1\-General, case\-insensitive, accent\-sensitive, kanatype\-sensitive, width\-insensitive  | 
|  Latin1\_General\_CS\_AS  |  Latin1\-General, case\-sensitive, accent\-sensitive, kanatype\-insensitive, width\-insensitive  | 
|  Modern\_Spanish\_CI\_AS  |  Modern\-Spanish, case\-insensitive, accent\-sensitive, kanatype\-insensitive, width\-insensitive  | 
|  SQL\_1xCompat\_CP850\_CI\_AS  |  Latin1\-General, case\-insensitive, accent\-sensitive, kanatype\-insensitive, width\-insensitive for Unicode Data, SQL Server Sort Order 49 on Code Page 850 for non\-Unicode Data  | 
|  SQL\_Latin1\_General\_CP1\_CI\_AI  |  Latin1\-General, case\-insensitive, accent\-insensitive, kanatype\-insensitive, width\-insensitive for Unicode Data, SQL Server Sort Order 54 on Code Page 1252 for non\-Unicode Data  | 
|  **SQL\_Latin1\_General\_CP1\_CI\_AS \(default\)**  |  Latin1\-General, case\-insensitive, accent\-sensitive, kanatype\-insensitive, width\-insensitive for Unicode Data, SQL Server Sort Order 52 on Code Page 1252 for non\-Unicode Data  | 
|  SQL\_Latin1\_General\_CP1\_CS\_AS  |  Latin1\-General, case\-sensitive, accent\-sensitive, kanatype\-insensitive, width\-insensitive for Unicode Data, SQL Server Sort Order 51 on Code Page 1252 for non\-Unicode Data  | 
|  SQL\_Latin1\_General\_CP437\_CI\_AI  |  Latin1\-General, case\-insensitive, accent\-insensitive, kanatype\-insensitive, width\-insensitive for Unicode Data, SQL Server Sort Order 34 on Code Page 437 for non\-Unicode Data  | 
|  SQL\_Latin1\_General\_CP850\_BIN2  |  Latin1\-General, binary code point sort order for Unicode Data, SQL Server Sort Order 40 on Code Page 850 for non\-Unicode Data  | 
|  SQL\_Latin1\_General\_CP850\_CI\_AS  |  Latin1\-General, case\-insensitive, accent\-sensitive, kanatype\-insensitive, width\-insensitive for Unicode Data, SQL Server Sort Order 42 on Code Page 850 for non\-Unicode Data  | 
|  SQL\_Latin1\_General\_CP1256\_CI\_AS  |  Latin1\-General, case\-insensitive, accent\-sensitive, kanatype\-insensitive, width\-insensitive for Unicode Data, SQL Server Sort Order 146 on Code Page 1256 for non\-Unicode Data  | 
|  Thai\_CI\_AS  |  Thai, case\-insensitive, accent\-sensitive, kanatype\-insensitive, width\-insensitive  | 

To choose the collation:
+ If you're using the Amazon RDS console, when creating a new DB instance choose **Additional configuration**, then enter the collation in the **Collation** field\. For more information, see [Creating an Amazon RDS DB instance](USER_CreateDBInstance.md)\. 
+ If you're using the AWS CLI, use the `--character-set-name` option with the `create-db-instance` command\. For more information, see [create\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html)\.
+ If you're using the Amazon RDS API, use the `CharacterSetName` parameter with the `CreateDBInstance` operation\. For more information, see [CreateDBInstance](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html)\.

## Database\-level collation for Microsoft SQL Server<a name="Appendix.SQLServer.CommonDBATasks.Collation.Database-Table-Column"></a>

You can change the default collation at the database, table, or column level by overriding the collation when creating a new database or database object\. For example, if your default server collation is SQL\_Latin1\_General\_CP1\_CI\_AS, you can change it to Mohawk\_100\_CI\_AS for Mohawk collation support\. Even arguments in a query can be type\-cast to use a different collation if necessary\.

For example, the following query would change the default collation for the AccountName column to Mohawk\_100\_CI\_AS

```
CREATE TABLE [dbo].[Account]
	(
	    [AccountID] [nvarchar](10) NOT NULL,
	    [AccountName] [nvarchar](100) COLLATE Mohawk_100_CI_AS NOT NULL 
	) ON [PRIMARY];
```

The Microsoft SQL Server DB engine supports Unicode by the built\-in NCHAR, NVARCHAR, and NTEXT data types\. For example, if you need CJK support, use these Unicode data types for character storage and override the default server collation when creating your databases and tables\. Here are several links from Microsoft covering collation and Unicode support for SQL Server:
+ [Working with collations](http://msdn.microsoft.com/en-us/library/ms187582%28v=sql.105%29.aspx) 
+ [Collation and international terminology](http://msdn.microsoft.com/en-us/library/ms143726%28v=sql.105%29) 
+ [Using SQL Server collations](http://msdn.microsoft.com/en-us/library/ms144260%28v=sql.105%29.aspx) 
+ [International considerations for databases and database engine applications](http://msdn.microsoft.com/en-us/library/ms190245%28v=sql.105%29.aspx)