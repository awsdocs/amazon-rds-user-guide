# Importing using Oracle Export/Import<a name="Oracle.Procedural.Importing.ExportImport"></a>

You might consider Oracle Export/Import utilities for migrations in the following conditions:
+ Your data size is small\.
+ Data types such as binary float and double aren't required\.

The import process creates the necessary schema objects\. Thus, you don't need to run a script to create the objects beforehand\. 

The easiest way to install the Oracle the export and import utilities is to install the Oracle Instant Client\. To download the software, go to [https://www\.oracle\.com/database/technologies/instant\-client\.html](https://www.oracle.com/database/technologies/instant-client.html)\. For documentation, see [Instant Client for SQL\*Loader, Export, and Import](https://docs.oracle.com/en/database/oracle/oracle-database/21/sutil/instant-client-sql-loader-export-import.html#GUID-FF1B6F75-09F5-4911-9317-9776FAD15965) in the *Oracle Database Utilities* manual\.

**To export tables and then import them**

1. Export the tables from the source database using the `exp` command\.

   The following command exports the tables named `tab1`, `tab2`, and `tab3`\. The dump file is `exp_file.dmp`\.

   ```
   exp cust_dba@ORCL FILE=exp_file.dmp TABLES=(tab1,tab2,tab3) LOG=exp_file.log
   ```

   The export creates a binary dump file that contains both the schema and data for the specified tables\. 

1. Import the schema and data into a target database using the `imp` command\.

   The following command imports the tables `tab1`, `tab2`, and `tab3` from dump file `exp_file.dmp`\.

   ```
   imp cust_dba@targetdb FROMUSER=cust_schema TOUSER=cust_schema \  
   TABLES=(tab1,tab2,tab3) FILE=exp_file.dmp LOG=imp_file.log
   ```

Export and Import have other variations that might be better suited to your requirements\. See the Oracle Database documentation for full details\.