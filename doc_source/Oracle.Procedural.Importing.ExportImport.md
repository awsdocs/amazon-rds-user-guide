# Oracle Export/Import utilities<a name="Oracle.Procedural.Importing.ExportImport"></a>

The Oracle Export/Import utilities are best suited for migrations where the data size is small and data types such as binary float and double are not required\. The import process creates the schema objects so you do not need to run a script to create them beforehand, making this process well suited for databases with small tables\. The following example demonstrates how these utilities can be used to export and import specific tables\. 

To download Oracle export and import utilities, go to [http://www\.oracle\.com/technetwork/database/enterprise\-edition/downloads/index\.html](http://www.oracle.com/technetwork/database/enterprise-edition/downloads/index.html)\. 

Export the tables from the source database using the command below\. Substitute username/password as appropriate\. 

```
exp cust_dba@ORCL FILE=exp_file.dmp TABLES=(tab1,tab2,tab3) LOG=exp_file.log
```

The export process creates a binary dump file that contains both the schema and data for the specified tables\. Now this schema and data can be imported into a target database using the command: 

```
imp cust_dba@targetdb FROMUSER=cust_schema TOUSER=cust_schema \  
TABLES=(tab1,tab2,tab3) FILE=exp_file.dmp LOG=imp_file.log
```

There are other variations of the Export and Import commands that might be better suited to your needs\. See Oracle's documentation for full details\. 