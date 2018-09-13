# Collations and Character Sets for Microsoft SQL Server<a name="Appendix.SQLServer.CommonDBATasks.Collation"></a>

Amazon RDS creates a default server collation for character sets when a Microsoft SQL Server DB instance is created\. This default server collation is currently English \(United States\), or more precisely, SQL\_Latin1\_General\_CP1\_CI\_AS\. You can change the default collation at the database, table, or column level by overriding the collation when creating a new database or database object\. For example, you can change from the default collation SQL\_Latin1\_General\_CP1\_CI\_AS to Japanese\_CI\_AS for Japanese collation support\. Even arguments in a query can be type\-cast to use a different collation if necessary\.

For example, the following query would change the default collation for the AccountName column to Japanese\_CI\_AS:

```
CREATE TABLE [dbo].[Account]
(
    [AccountID] [nvarchar](10) NOT NULL,
    [AccountName] [nvarchar](100) COLLATE Japanese_CI_AS NOT NULL 
) ON [PRIMARY];
```

The Microsoft SQL Server DB engine supports Unicode by the built\-in NCHAR, NVARCHAR, and NTEXT data types\. For example, if you need CJK support, use these Unicode data types for character storage and override the default server collation when creating your databases and tables\. Here are several links from Microsoft covering collation and Unicode support for SQL Server:
+ [Working with Collations](http://msdn.microsoft.com/en-us/library/ms187582%28v=sql.105%29.aspx) 
+ [Collation and International Terminology](http://msdn.microsoft.com/en-us/library/ms143726%28v=sql.105%29) 
+ [Using SQL Server Collations](http://msdn.microsoft.com/en-us/library/ms144260%28v=sql.105%29.aspx) 
+ [International Considerations for Databases and Database Engine Applications](http://msdn.microsoft.com/en-us/library/ms190245%28v=sql.105%29.aspx)