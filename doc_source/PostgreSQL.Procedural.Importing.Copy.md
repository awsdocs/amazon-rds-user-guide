# Using the \\copy command to import data to a table on a PostgreSQL DB instance<a name="PostgreSQL.Procedural.Importing.Copy"></a>

You can run the `\copy` command from the *psql* prompt to import data into a table on a PostgreSQL DB instance\. The table must already exist on the DB instance\. For more information on the \\copy command, see the [PostgreSQL documentation](http://www.postgresql.org/docs/current/static/app-psql.html)\.

**Note**  
The `\copy` command doesn't provide confirmation of actions, such as a count of rows inserted\. PostgreSQL does provide error messages if the copy command fails due to an error\.

Create a \.csv file from the data in the source table, log on to the target database on the PostgreSQL instance using *psql*, and then run the following command\. This example uses *source\-table* as the source table name, *source\-table\.csv* as the \.csv file, and *target\-db* as the target database:

```
target-db=> \copy source-table from 'source-table.csv' with DELIMITER ','; 
```

You can also run the following command from your client computer command prompt\. This example uses *source\-table* as the source table name, *source\-table\.csv* as the \.csv file, and *target\-db* as the target database:

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