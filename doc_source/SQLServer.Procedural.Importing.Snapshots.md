# Importing and Exporting SQL Server Data Using Other Methods<a name="SQLServer.Procedural.Importing.Snapshots"></a>

Following, you can find information about using snapshots to import your Microsoft SQL Server data to Amazon RDS\. You can also find information about using snapshots to export your data from an RDS DB instance running SQL Server\. 

If your scenario supports it, it's easier to move data in and out of Amazon RDS by using the native backup and restore functionality\. For more information, see [Importing and Exporting SQL Server Databases](SQLServer.Procedural.Importing.md)\. 

**Note**  
Amazon RDS for Microsoft SQL Server does not support importing data into the `msdb` database\. 

## Importing Data into SQL Server on Amazon RDS by Using a Snapshot<a name="SQLServer.Procedural.Importing.Procedure"></a>

**To import data into a SQL Server DB instance by using a snapshot**

1. Create a DB instance\. For more information, see [Creating an Amazon RDS DB Instance](USER_CreateDBInstance.md)\.

1. Stop applications from accessing the destination DB instance\. 

   If you prevent access to your DB instance while you are importing data, data transfer is faster\. Additionally, you don't need to worry about conflicts while data is being loaded if other applications cannot write to the DB instance at the same time\. If something goes wrong and you have to roll back to an earlier database snapshot, the only changes that you lose are the imported data\. You can import this data again after you resolve the issue\. 

   For information about controlling access to your DB instance, see [Working with DB Security Groups \(EC2\-Classic Platform\)](USER_WorkingWithSecurityGroups.md)\. 

1. Create a snapshot of the target database\. 

   If the target database is already populated with data, we recommend that you take a snapshot of the database before you import the data\. If something goes wrong with the data import or you want to discard the changes, you can restore the database to its previous state by using the snapshot\. For information about database snapshots, see [Creating a DB Snapshot](USER_CreateSnapshot.md)\. 
**Note**  
When you take a database snapshot, I/O operations to the database are suspended for a moment \(milliseconds\) while the backup is in progress\. 

1. Disable automated backups on the target database\. 

   Disabling automated backups on the target DB instance improves performance while you are importing your data because Amazon RDS doesn't log transactions when automatic backups are disabled\. However, there are some things to consider\. Automated backups are required to perform a point\-in\-time recovery\. Thus, you can't restore the database to a specific point in time while you are importing data\. Additionally, any automated backups that were created on the DB instance are erased unless you choose to retain them\. 

   Choosing to retain the automated backups can help protect you against accidental deletion of data\. Amazon RDS also saves the database instance properties along with each automated backup to make it easy to recover\. Using this option lets you can restore a deleted database instance to a specified point in time within the backup retention period even after deleting it\. Automated backups are automatically deleted at the end of the specified backup window, just as they are for an active database instance\. 

   You can also use previous snapshots to recover the database, and any snapshots that you have taken remain available\. For information about automated backups, see [Working With Backups](USER_WorkingWithAutomatedBackups.md)\. 

1. Disable foreign key constraints, if applicable\. 

    If you need to disable foreign key constraints, you can do so with the following script\. 

   ```
   --Disable foreign keys on all tables
       DECLARE @table_name SYSNAME;
       DECLARE @cmd NVARCHAR(MAX);
       DECLARE table_cursor CURSOR FOR SELECT name FROM sys.tables;
       
       OPEN table_cursor;
       FETCH NEXT FROM table_cursor INTO @table_name;
       
       WHILE @@FETCH_STATUS = 0 BEGIN
         SELECT @cmd = 'ALTER TABLE '+QUOTENAME(@table_name)+' NOCHECK CONSTRAINT ALL';
         EXEC (@cmd);
         FETCH NEXT FROM table_cursor INTO @table_name;
       END
       
       CLOSE table_cursor;
       DEALLOCATE table_cursor;
       
       GO
   ```

1. Drop indexes, if applicable\. 

1. Disable triggers, if applicable\. 

    If you need to disable triggers, you can do so with the following script\. 

   ```
   --Disable triggers on all tables
       DECLARE @enable BIT = 0;
       DECLARE @trigger SYSNAME;
       DECLARE @table SYSNAME;
       DECLARE @cmd NVARCHAR(MAX);
       DECLARE trigger_cursor CURSOR FOR SELECT trigger_object.name trigger_name,
        table_object.name table_name
       FROM sysobjects trigger_object
       JOIN sysobjects table_object ON trigger_object.parent_obj = table_object.id
       WHERE trigger_object.type = 'TR';
       
       OPEN trigger_cursor;
       FETCH NEXT FROM trigger_cursor INTO @trigger, @table;
       
       WHILE @@FETCH_STATUS = 0 BEGIN
         IF @enable = 1
            SET @cmd = 'ENABLE ';
         ELSE
            SET @cmd = 'DISABLE ';
       
         SET @cmd = @cmd + ' TRIGGER dbo.'+QUOTENAME(@trigger)+' ON dbo.'+QUOTENAME(@table)+' ';
         EXEC (@cmd);
         FETCH NEXT FROM trigger_cursor INTO @trigger, @table;
       END
       
       CLOSE trigger_cursor;
       DEALLOCATE trigger_cursor;
       
       GO
   ```

1. Query the source SQL Server instance for any logins that you want to import to the destination DB instance\. 

   SQL Server stores logins and passwords in the `master` database\. Because Amazon RDS doesn't grant access to the `master` database, you cannot directly import logins and passwords into your destination DB instance\. Instead, you must query the `master` database on the source SQL Server instance to generate a data definition language \(DDL\) file\. This file should include all logins and passwords that you want to add to the destination DB instance\. This file also should include role memberships and permissions that you want to transfer\. 

   For information about querying the `master` database, see [ How to Transfer the Logins and the Passwords Between Instances of SQL Server 2005 and SQL Server 2008](http://support.microsoft.com/kb/918992) in the Microsoft Knowledge Base\. 

   The output of the script is another script that you can run on the destination DB instance\. The script in the Knowledge Base article has the following code: 

   ```
   p.type IN 
   ```

   Every place `p.type` appears, use the following code instead: 

   ```
   p.type = 'S' 
   ```

1. Import the data using the method in [Import the Data](#ImportData.SQLServer.Import)\. 

1. Grant applications access to the target DB instance\. 

   When your data import is complete, you can grant access to the DB instance to those applications that you blocked during the import\. For information about controlling access to your DB instance, see [Working with DB Security Groups \(EC2\-Classic Platform\)](USER_WorkingWithSecurityGroups.md)\. 

1. Enable automated backups on the target DB instance\. 

   For information about automated backups, see [Working With Backups](USER_WorkingWithAutomatedBackups.md)\. 

1. Enable foreign key constraints\. 

    If you disabled foreign key constraints earlier, you can now enable them with the following script\. 

   ```
   --Enable foreign keys on all tables
       DECLARE @table_name SYSNAME;
       DECLARE @cmd NVARCHAR(MAX);
       DECLARE table_cursor CURSOR FOR SELECT name FROM sys.tables;
       
       OPEN table_cursor;
       FETCH NEXT FROM table_cursor INTO @table_name;
       
       WHILE @@FETCH_STATUS = 0 BEGIN
         SELECT @cmd = 'ALTER TABLE '+QUOTENAME(@table_name)+' CHECK CONSTRAINT ALL';
         EXEC (@cmd);
         FETCH NEXT FROM table_cursor INTO @table_name;
       END
       
       CLOSE table_cursor;
       DEALLOCATE table_cursor;
   ```

1. Enable indexes, if applicable\.

1. Enable triggers, if applicable\.

    If you disabled triggers earlier, you can now enable them with the following script\. 

   ```
   --Enable triggers on all tables
       DECLARE @enable BIT = 1;
       DECLARE @trigger SYSNAME;
       DECLARE @table SYSNAME;
       DECLARE @cmd NVARCHAR(MAX);
       DECLARE trigger_cursor CURSOR FOR SELECT trigger_object.name trigger_name,
        table_object.name table_name
       FROM sysobjects trigger_object
       JOIN sysobjects table_object ON trigger_object.parent_obj = table_object.id
       WHERE trigger_object.type = 'TR';
       
       OPEN trigger_cursor;
       FETCH NEXT FROM trigger_cursor INTO @trigger, @table;
       
       WHILE @@FETCH_STATUS = 0 BEGIN
         IF @enable = 1
            SET @cmd = 'ENABLE ';
         ELSE
            SET @cmd = 'DISABLE ';
       
         SET @cmd = @cmd + ' TRIGGER dbo.'+QUOTENAME(@trigger)+' ON dbo.'+QUOTENAME(@table)+' ';
         EXEC (@cmd);
         FETCH NEXT FROM trigger_cursor INTO @trigger, @table;
       END
       
       CLOSE trigger_cursor;
       DEALLOCATE trigger_cursor;
   ```

### Import the Data<a name="ImportData.SQLServer.Import"></a>

Microsoft SQL Server Management Studio is a graphical SQL Server client that is included in all Microsoft SQL Server editions except the Express Edition\. SQL Server Management Studio Express is available from Microsoft as a free download\. To find this download, see [the Microsoft website](http://www.microsoft.com/en-us/download/default.aspx)\. 

**Note**  
SQL Server Management Studio is available only as a Windows\-based application\.

SQL Server Management Studio includes the following tools, which are useful in importing data to a SQL Server DB instance: 
+ Generate and Publish Scripts wizard
+ Import and Export wizard
+ Bulk copy

#### Generate and Publish Scripts Wizard<a name="ImportData.SQLServer.MgmtStudio.ScriptWizard"></a>

The Generate and Publish Scripts wizard creates a script that contains the schema of a database, the data itself, or both\. You can generate a script for a database in your local SQL Server deployment\. You can then run the script to transfer the information that it contains to an Amazon RDS DB instance\. 

**Note**  
For databases of 1 GiB or larger, it's more efficient to script only the database schema\. You then use the Import and Export wizard or the bulk copy feature of SQL Server to transfer the data\. 

For detailed information about the Generate and Publish Scripts wizard, see the [Microsoft SQL Server documentation](http://msdn.microsoft.com/en-us/library/ms178078%28v=sql.105%29.aspx)\. 

In the wizard, pay particular attention to the advanced options on the **Set Scripting Options** page to ensure that everything you want your script to include is selected\. For example, by default, database triggers are not included in the script\. 

When the script is generated and saved, you can use SQL Server Management Studio to connect to your DB instance and then run the script\. 

#### Import and Export Wizard<a name="ImportData.SQLServer.MgmtStudio.ImportExportWizard"></a>

The Import and Export wizard creates a special Integration Services package, which you can use to copy data from your local SQL Server database to the destination DB instance\. The wizard can filter which tables and even which tuples within a table are copied to the destination DB instance\. 

**Note**  
The Import and Export wizard works well for large datasets, but it might not be the fastest way to remotely export data from your local deployment\. For an even faster way, consider the SQL Server bulk copy feature\. 

For detailed information about the Import and Export wizard, see the [ Microsoft SQL Server documentation](http://msdn.microsoft.com/en-us/library/ms140052%28v=sql.105%29.aspx)\. 

In the wizard, on the **Choose a Destination** page, do the following: 
+ For **Server Name**, type the name of the endpoint for your DB instance\.
+ For the server authentication mode, choose **Use SQL Server Authentication**\.
+ For **User name** and **Password**, type the credentials for the master user that you created for the DB instance\.

#### Bulk Copy<a name="ImportData.SQLServer.MgmtStudio.BulkCopy"></a>

The SQL Server bulk copy feature is an efficient means of copying data from a source database to your DB instance\. Bulk copy writes the data that you specify to a data file, such as an ASCII file\. You can then run bulk copy again to write the contents of the file to the destination DB instance\. 

This section uses the **bcp** utility, which is included with all editions of SQL Server\. For detailed information about bulk import and export operations, see [the Microsoft SQL Server documentation](http://msdn.microsoft.com/en-us/library/ms187042%28v=sql.105%29.aspx)\. 

**Note**  
Before you use bulk copy, you must first import your database schema to the destination DB instance\. The Generate and Publish Scripts wizard, described earlier in this topic, is an excellent tool for this purpose\. 

The following command connects to the local SQL Server instance\. It generates a tab\-delimited file of a specified table in the C:\\ root directory of your existing SQL Server deployment\. The table is specified by its fully qualified name, and the text file has the same name as the table that is being copied\. 

```
bcp dbname.schema_name.table_name out C:\table_name.txt -n -S localhost -U username -P password -b 10000 
```

The preceding code includes the following options:
+ `-n` specifies that the bulk copy uses the native data types of the data to be copied\.
+ `-S` specifies the SQL Server instance that the *bcp* utility connects to\.
+ `-U` specifies the user name of the account to log in to the SQL Server instance\.
+ `-P` specifies the password for the user specified by `-U`\.
+ `-b` specifies the number of rows per batch of imported data\.

**Note**  
There might be other parameters that are important to your import situation\. For example, you might need the `-E` parameter that pertains to identity values\. For more information; see the full description of the command line syntax for the **bcp** utility in [the Microsoft SQL Server documentation](http://msdn.microsoft.com/en-us/library/ms162802%28v=sql.105%29.aspx)\. 

For example, suppose that a database named `store` that uses the default schema, `dbo`, contains a table named `customers`\. The user account `admin`, with the password `insecure`, copies 10,000 rows of the `customers` table to a file named `customers.txt`\. 

```
bcp store.dbo.customers out C:\customers.txt -n -S localhost -U admin -P insecure -b 10000 
```

After you generate the data file, you can upload the data to your DB instance by using a similar command\. Beforehand, create the database and schema on the target DB instance\. Then use the `in` argument to specify an input file instead of `out` to specify an output file\. Instead of using localhost to specify the local SQL Server instance, specify the endpoint of your DB instance\. If you use a port other than 1433, specify that too\. The user name and password are the master user and password for your DB instance\. The syntax is as follows\. 

```
bcp dbname.schema_name.table_name in C:\table_name.txt -n -S endpoint,port -U master_user_name -P master_user_password -b 10000 
```

To continue the previous example, suppose that the master user name is `admin`, and the password is `insecure`\. The endpoint for the DB instance is `rds.ckz2kqd4qsn1.us-east-1.rds.amazonaws.com`, and you use port 4080\. The command is as follows\. 

```
bcp store.dbo.customers in C:\customers.txt -n -S rds.ckz2kqd4qsn1.us-east-1.rds.amazonaws.com,4080 -U admin -P insecure -b 10000 
```

## Exporting Data from SQL Server on Amazon RDS<a name="SQLServer.Procedural.Exporting"></a>

You can choose one of the following options to export data from an RDS SQL Server DB instance: 
+ **Native database backup using a full backup file \(\.bak\)** – Using \.bak files to backup databases is heavily optimized, and is usually the fastest way to export data\. For more information, see [Importing and Exporting SQL Server Databases](SQLServer.Procedural.Importing.md)\. 
+ **SQL Server Import and Export wizard** – For more information, see [SQL Server Import and Export Wizard](#SQLServer.Procedural.Exporting.SSIEW)\. 
+ **SQL Server Generate and Publish Scripts wizard and bcp utility** – For more information, see [SQL Server Generate and Publish Scripts Wizard and bcp Utility](#SQLServer.Procedural.Exporting.SSGPSW)\. 

### SQL Server Import and Export Wizard<a name="SQLServer.Procedural.Exporting.SSIEW"></a>

You can use the SQL Server Import and Export wizard to copy one or more tables, views, or queries from your RDS SQL Server DB instance to another data store\. This choice is best if the target data store is not SQL Server\. For more information, see [ SQL Server Import and Export Wizard](http://msdn.microsoft.com/en-us/library/ms141209%28v=sql.110%29.aspx) in the SQL Server documentation\. 

The SQL Server Import and Export wizard is available as part of Microsoft SQL Server Management Studio\. This graphical SQL Server client is included in all Microsoft SQL Server editions except the Express Edition\. SQL Server Management Studio is available only as a Windows\-based application\. SQL Server Management Studio Express is available from Microsoft as a free download\. To find this download, see [the Microsoft website](http://www.microsoft.com/en-us/search/Results.aspx?q=sql%20server%20management%20studio)\. 

**To use the SQL Server Import and Export wizard to export data**

1. In SQL Server Management Studio, connect to your RDS SQL Server DB instance\. For details on how to do this, see [Connecting to a DB Instance Running the Microsoft SQL Server Database Engine](USER_ConnectToMicrosoftSQLServerInstance.md)\. 

1. In **Object Explorer**, expand **Databases**, open the context \(right\-click\) menu for the source database, choose **Tasks**, and then choose **Export Data**\. The wizard appears\. 

1. On the **Choose a Data Source** page, do the following:

   1. For **Data source**, choose **SQL Server Native Client 11\.0**\. 

   1. Verify that the **Server name** box shows the endpoint of your RDS SQL Server DB instance\.

   1. Select **Use SQL Server Authentication**\. For **User name** and **Password**, type the master user name and password of your Amazon RDS SQL DB\. 

   1. Verify that the **Database** box shows the database from which you want to export data\.

   1. Choose **Next**\.

1. On the **Choose a Destination** page, do the following:

   1. For **Destination**, choose **SQL Server Native Client 11\.0**\. 
**Note**  
Other target data sources are available\. These include \.NET Framework data providers, OLE DB providers, SQL Server Native Client providers, ADO\.NET providers, Microsoft Office Excel, Microsoft Office Access, and the Flat File source\. If you choose to target one of these data sources, skip the remainder of step 4\. For details on the connection information to provide next, see [Choose a Destination](http://msdn.microsoft.com/en-us/library/ms178430%28v=sql.110%29.aspx) in the SQL Server documentation\. 

   1. For **Server name**, type the server name of the target SQL Server DB instance\. 

   1. Choose the appropriate authentication type\. Type a user name and password if necessary\. 

   1. For **Database**, choose the name of the target database, or choose **New** to create a new database to contain the exported data\. 

      If you choose **New**, see [Create Database](http://msdn.microsoft.com/en-us/library/ms183323%28v=sql.110%29.aspx) in the SQL Server documentation for details on the database information to provide\.

   1. Choose **Next**\.

1. On the **Table Copy or Query** page, choose **Copy data from one or more tables or views** or **Write a query to specify the data to transfer**\. Choose **Next**\. 

1. If you chose **Write a query to specify the data to transfer**, you see the **Provide a Source Query** page\. Type or paste in a SQL query, and then choose **Parse** to verify it\. Once the query validates, choose **Next**\. 

1. On the **Select Source Tables and Views** page, do the following:

   1. Select the tables and views that you want to export, or verify that the query you provided is selected\.

   1. Choose **Edit Mappings** and specify database and column mapping information\. For more information, see [Column Mappings](http://msdn.microsoft.com/en-us/library/ms189660%28v=sql.110%29.aspx) in the SQL Server documentation\. 

   1. \(Optional\) To see a preview of data to be exported, select the table, view, or query, and then choose **Preview**\.

   1. Choose **Next**\.

1. On the **Run Package** page, verify that **Run immediately** is selected\. Choose **Next**\. 

1. On the **Complete the Wizard** page, verify that the data export details are as you expect\. Choose **Finish**\. 

1. On the **The execution was successful** page, choose **Close**\. 

### SQL Server Generate and Publish Scripts Wizard and bcp Utility<a name="SQLServer.Procedural.Exporting.SSGPSW"></a>

You can use the SQL Server Generate and Publish Scripts wizard to create scripts for an entire database or just selected objects\. You can run these scripts on a target SQL Server DB instance to recreate the scripted objects\. You can then use the bcp utility to bulk export the data for the selected objects to the target DB instance\. This choice is best if you want to move a whole database \(including objects other than tables\) or large quantities of data between two SQL Server DB instances\. For a full description of the bcp command\-line syntax, see [bcp Utility](http://msdn.microsoft.com/en-us/library/ms162802%28v=sql.110%29.aspx) in the Microsoft SQL Server documentation\. 

The SQL Server Generate and Publish Scripts wizard is available as part of Microsoft SQL Server Management Studio\. This graphical SQL Server client is included in all Microsoft SQL Server editions except the Express Edition\. SQL Server Management Studio is available only as a Windows\-based application\. SQL Server Management Studio Express is available from Microsoft as a [free download](http://www.microsoft.com/en-us/search/Results.aspx?q=sql%20server%20management%20studio)\. 

**To use the SQL Server Generate and Publish Scripts wizard and the bcp utility to export data**

1. In SQL Server Management Studio, connect to your RDS SQL Server DB instance\. For details on how to do this, see [Connecting to a DB Instance Running the Microsoft SQL Server Database Engine](USER_ConnectToMicrosoftSQLServerInstance.md)\. 

1. In **Object Explorer**, expand the **Databases** node and select the database you want to script\. 

1. Follow the instructions in [Generate and Publish Scripts Wizard](http://msdn.microsoft.com/en-us/library/bb895179%28v=sql.110%29.aspx) in the SQL Server documentation to create a script file\. 

1. In SQL Server Management Studio, connect to your target SQL Server DB instance\. 

1. With the target SQL Server DB instance selected in **Object Explorer**, choose **Open** on the **File** menu, choose **File**, and then open the script file\. 

1. If you have scripted the entire database, review the CREATE DATABASE statement in the script\. Make sure that the database is being created in the location and with the parameters that you want\. For more information, see [CREATE DATABASE](http://msdn.microsoft.com/en-us/library/ms176061%28v=sql.110%29.aspx) in the SQL Server documentation\. 

1. If you are creating database users in the script, check to see if server logins exist on the target DB instance for those users\. If not, create logins for those users; the scripted commands to create the database users fail otherwise\. For more information, see [Create a Login](http://msdn.microsoft.com/en-us/library/aa337562%28v=sql.110%29.aspx) in the SQL Server documentation\.

1. Choose **\!Execute** on the SQL Editor menu to run the script file and create the database objects\. When the script finishes, verify that all database objects exist as expected\.

1. Use the bcp utility to export data from the RDS SQL Server DB instance into files\. Open a command prompt and type the following command\.

   ```
   bcp database_name.schema_name.table_name out data_file -n -S aws_rds_sql_endpoint -U username -P password
   ```

   The preceding code includes the following options:
   + *table\_name* is the name of one of the tables that you've recreated in the target database and now want to populate with data\. 
   + *data\_file* is the full path and name of the data file to be created\.
   + `-n` specifies that the bulk copy uses the native data types of the data to be copied\.
   + `-S` specifies the SQL Server DB instance to export from\.
   + `-U` specifies the user name to use when connecting to the SQL Server DB instance\.
   + `-P` specifies the password for the user specified by `-U`\.

   The following shows an example command\. 

   ```
   bcp world.dbo.city out C:\Users\JohnDoe\city.dat -n -S sql-jdoe.1234abcd.us-west-2.rds.amazonaws.com,1433 -U JohnDoe -P ClearTextPassword
   ```

   Repeat this step until you have data files for all of the tables you want to export\. 

1. Prepare your target DB instance for bulk import of data by following the instructions at [Basic Guidelines for Bulk Importing Data](http://msdn.microsoft.com/en-us/library/ms189989%28v=sql.110%29.aspx) in the SQL Server documentation\. 

1. Decide on a bulk import method to use after considering performance and other concerns discussed in [About Bulk Import and Bulk Export Operations](http://msdn.microsoft.com/en-us/library/ms187042%28v=sql.105%29.aspx) in the SQL Server documentation\. 

1. Bulk import the data from the data files that you created using the bcp utility\. To do so, follow the instructions at either [Import and Export Bulk Data by Using the bcp Utility](http://msdn.microsoft.com/en-us/library/aa337544%28v=sql.110%29.aspx) or [Import Bulk Data by Using BULK INSERT or OPENROWSET\(BULK\.\.\.\)](http://msdn.microsoft.com/en-us/library/ms175915%28v=sql.110%29.aspx) in the SQL Server documentation, depending on what you decided in step 11\. 