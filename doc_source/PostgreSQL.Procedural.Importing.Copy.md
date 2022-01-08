# Using the \\copy command to import data to a table on a PostgreSQL DB instance<a name="PostgreSQL.Procedural.Importing.Copy"></a>

The PostgreSQL `\copy` command is a meta\-command available from the `psql` interactive client tool\. You can use `\copy` to import data into a table on your RDS for PostgreSQL DB instance\. To use the `\copy` command, you need to first create the table structure on the target DB instance so that `\copy` has a destination for the data being copied\.

You can use `\copy` to load data from a comma\-separated values \(CSV\) file, such as one that's been exported and saved to your client workstation\.

To import the CSV data to the target RDS for PostgreSQL DB instance, first connect to the target DB instance using `psql`\. 

```
psql --host=db-instance.111122223333.aws-region.rds.amazonaws.com --port=5432 --username=postgres --password --dbname=target-db
```

You then run `\copy` command with the following parameters to identify the target for the data and its format\.
+ `target_table` – The name of the table that should receive the data being copied from the CSV file\.
+ `column_list` – Column specifications for the table\. 
+ `'filename'` – The complete path to the CSV file on your local workstation\. 

```
 \copy target_table from '/path/to/local/filename.csv' WITH DELIMITER ',' CSV;
```

If your CSV file has column heading information, you can use this version of the command and parameters\.

```
\copy target_table (column-1, column-2, column-3, ...)
    from '/path/to/local/filename.csv' WITH DELIMITER ',' CSV HEADER;
```

 If the `\copy` command fails, PostgreSQL outputs error messages\.

You can also combine the `psql` command with the `\copy` meta\-command as shown in the following examples\. This example uses *source\-table* as the source table name, *source\-table\.csv* as the \.csv file, and *target\-db* as the target database:

For Linux, macOS, or Unix:

```
$psql target-db \
    -U <admin user> \
    -p <port> \
    -h <DB instance name> \
    -c "\copy source-table from 'source-table.csv' with DELIMITER ','"
```

For Windows:

```
$psql target-db ^
    -U <admin user> ^
    -p <port> ^
    -h <DB instance name> ^
    -c "\copy source-table from 'source-table.csv' with DELIMITER ','"
```

For complete details about the `\copy` command, see the [psql](http://www.postgresql.org/docs/current/static/app-psql.html) page in the PostgreSQL documentation, in the *Meta\-Commands* section\. 