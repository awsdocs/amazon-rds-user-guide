# Importing using Oracle Export/Import<a name="Oracle.Procedural.Importing.ExportImport"></a>

You might consider Oracle Export/Import utilities for migrations in the following conditions:
+ Your data size is small\.
+ Data types such as binary float and double aren't required\.

The import process creates the necessary schema objects, so you don't need to run a script to create the objects beforehand\. To download Oracle export and import utilities, go to [http://www\.oracle\.com/technetwork/database/enterprise\-edition/downloads/index\.html](http://www.oracle.com/technetwork/database/enterprise-edition/downloads/index.html)\.

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