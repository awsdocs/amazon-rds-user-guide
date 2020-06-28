# Accessing the tempdb Database on Microsoft SQL Server DB Instances on Amazon RDS<a name="SQLServer.TempDB"></a>

You can access the `tempdb` database on your Microsoft SQL Server DB instances on Amazon RDS\. You can run code on `tempdb` by using Transact\-SQL through Microsoft SQL Server Management Studio \(SSMS\), or any other standard SQL client application\. For more information about connecting to your DB instance, see [Connecting to a DB Instance Running the Microsoft SQL Server Database Engine](USER_ConnectToMicrosoftSQLServerInstance.md)\. 

The master user for your DB instance is granted `CONTROL` access to `tempdb` so that this user can modify the `tempdb` database options\. The master user isn't the database owner of the `tempdb` database\. If necessary, the master user can grant `CONTROL` access to other users so that they can also modify the `tempdb` database options\. 

**Note**  
You can't run Database Console Commands \(DBCC\) on the `tempdb` database\. 

## Modifying tempdb Database Options<a name="SQLServer.TempDB.Modifying"></a>

You can modify the database options on the `tempdb` database on your Amazon RDS DB instances\. For more information about which options can be modified, see [tempdb Database](https://msdn.microsoft.com/en-us/library/ms190768%28v=sql.120%29.aspx) in the Microsoft documentation\.

Database options such as the maximum file size options are persistent after you restart your DB instance\. You can modify the database options to optimize performance when importing data, and to prevent running out of storage\.

### Optimizing Performance when Importing Data<a name="SQLServer.TempDB.Modifying.Import"></a>

To optimize performance when importing large amounts of data into your DB instance, set the `SIZE` and `FILEGROWTH` properties of the tempdb database to large numbers\. For more information about how to optimize `tempdb`, see [Optimizing tempdb Performance](https://technet.microsoft.com/en-us/library/ms175527%28v=sql.120%29.aspx) in the Microsoft documentation\.

The following example demonstrates setting the size to 100 GB and file growth to 10 percent\. 

```
1. alter database[tempdb] modify file (NAME = N'templog', SIZE=100GB, FILEGROWTH = 10%)
```

### Preventing Storage Problems<a name="SQLServer.TempDB.Modifying.Full"></a>

To prevent the `tempdb` database from using all available disk space, set the `MAXSIZE` property\. The following example demonstrates setting the property to 2048 MB\. 

```
1. alter database [tempdb] modify file (NAME = N'templog', MAXSIZE = 2048MB)
```

## Shrinking the tempdb Database<a name="SQLServer.TempDB.Shrinking"></a>

There are two ways to shrink the `tempdb` database on your Amazon RDS DB instance\. You can use the `rds_shrink_tempdbfile` procedure, or you can set the `SIZE` property, 

### Using the rds\_shrink\_tempdbfile Procedure<a name="SQLServer.TempDB.Shrinking.Proc"></a>

You can use the Amazon RDS procedure `msdb.dbo.rds_shrink_tempdbfile` to shrink the `tempdb` database\. You can only call `rds_shrink_tempdbfile` if you have `CONTROL` access to `tempdb`\. When you call `rds_shrink_tempdbfile`, there is no downtime for your DB instance\. 

The `rds_shrink_tempdbfile` procedure has the following parameters\.


****  

| Parameter Name | Data Type | Default | Required | Description | 
| --- | --- | --- | --- | --- | 
| `@temp_filename` | SYSNAME | — | required | The logical name of the file to shrink\. | 
| `@target_size` | int | null | optional | The new size for the file, in megabytes\. | 

The following example gets the names of the files for the `tempdb` database\.

```
1. use tempdb;
2. GO
3. 
4. select name, * from sys.sysfiles;
5. GO
```

The following example shrinks a `tempdb` database file named `test_file`, and requests a new size of `10` megabytes: 

```
1. exec msdb.dbo.rds_shrink_tempdbfile @temp_filename = N'test_file', @target_size = 10;
```

### Setting the SIZE Property<a name="SQLServer.TempDB.Shrinking.Size"></a>

You can also shrink the `tempdb` database by setting the `SIZE` property and then restarting your DB instance\. For more information about restarting your DB instance, see [Rebooting a DB Instance](USER_RebootInstance.md)\. 

The following example demonstrates setting the `SIZE` property to 1024 MB\. 

```
1. alter database [tempdb] modify file (NAME = N'templog', SIZE = 1024MB)
```

## Considerations for Multi\-AZ Deployments<a name="SQLServer.TempDB.MAZ"></a>

If your Amazon RDS DB instance is in a Multi\-AZ Deployment for Microsoft SQL Server with Database Mirroring \(DBM\) or Always On Availability Groups \(AGs\), there are some things to consider\. 

The `tempdb` database can't be replicated\. No data that you store on your primary instance is replicated to your secondary instance\.

If you modify any database options on the `tempdb` database, you can capture those changes on the secondary by using one of the following methods:
+ First modify your DB instance and turn Multi\-AZ off, then modify tempdb, and finally turn Multi\-AZ back on\. This method doesn't involve any downtime\.

  For more information, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md)\. 
+ First modify `tempdb` in the original primary instance, then fail over manually, and finally modify `tempdb` in the new primary instance\. This method involves downtime\. 

  For more information, see [Rebooting a DB Instance](USER_RebootInstance.md)\.