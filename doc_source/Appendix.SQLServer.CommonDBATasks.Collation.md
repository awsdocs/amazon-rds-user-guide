# Collations and Character Sets for Microsoft SQL Server<a name="Appendix.SQLServer.CommonDBATasks.Collation"></a>

SQL Server supports collations at multiple levels\. You set the default server collation when you create the DB instance\. You can override the collation in the database, table, or column level\.

**Topics**
+ [Server Collation for Microsoft SQL Server](#Appendix.SQLServer.CommonDBATasks.Collation.Server)
+ [Database, Table, and Column Collation for Microsoft SQL Server](#Appendix.SQLServer.CommonDBATasks.Collation.Database-Table-Column)

## Server Collation for Microsoft SQL Server<a name="Appendix.SQLServer.CommonDBATasks.Collation.Server"></a>

When you create a Microsoft SQL Server DB instance, you can set the default server collation that you want to use\. The server collation is applied by default to all databases and database objects\. Currently, the only supported collation is SQL\_Latin1\_General\_CP1\_CI\_AS\. 

## Database, Table, and Column Collation for Microsoft SQL Server<a name="Appendix.SQLServer.CommonDBATasks.Collation.Database-Table-Column"></a>

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
+ [Working with Collations](http://msdn.microsoft.com/en-us/library/ms187582%28v=sql.105%29.aspx) 
+ [Collation and International Terminology](http://msdn.microsoft.com/en-us/library/ms143726%28v=sql.105%29) 
+ [Using SQL Server Collations](http://msdn.microsoft.com/en-us/library/ms144260%28v=sql.105%29.aspx) 
+ [International Considerations for Databases and Database Engine Applications](http://msdn.microsoft.com/en-us/library/ms190245%28v=sql.105%29.aspx)