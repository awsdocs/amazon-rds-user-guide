# Importing Data into PostgreSQL on Amazon RDS<a name="PostgreSQL.Procedural.Importing"></a>

Suppose that you have an existing PostgreSQL deployment that you want to move to Amazon RDS\. The complexity of your task depends on the size of your database and the types of database objects that you're transferring\. For example, consider a database that contains datasets on the order of gigabytes, along with stored procedures and triggers\. Such a database is going to be more complicated than a simple database with only a few megabytes of test data and no triggers or stored procedures\. 

We recommend that you use native PostgreSQL database migration tools under the following conditions:
+ You have a homogeneous migration, where you are migrating from a database with the same database engine as the target database\.
+ You are migrating an entire database\.
+ The native tools allow you to migrate your system with minimal downtime\.

In most other cases, performing a database migration using AWS Database Migration Service \(AWS DMS\) is the best approach\. AWS DMS can migrate databases without downtime and, for many database engines, continue ongoing replication until you are ready to switch over to the target database\. You can migrate to either the same database engine or a different database engine using AWS DMS\. If you are migrating to a different database engine than your source database, you can use the AWS Schema Conversion Tool \(AWS SCT\)\. You use AWS SCT to migrate schema objects that are not migrated by AWS DMS\. For more information about AWS DMS, see [ What Is AWS Database Migration Service?](https://docs.aws.amazon.com/dms/latest/userguide/Welcome.html)

Modify your DB parameter group to include the following settings *for your import only*\. You should test the parameter settings to find the most efficient settings for your DB instance size\. You also need to revert back to production values for these parameters after your import completes\.

Modify your DB instance settings to the following:
+ Disable DB instance backups \(set backup\_retention to 0\)\.
+ Disable Multi\-AZ\.

Modify your DB parameter group to include the following settings\. You should only use these settings when importing data\. You should test the parameter settings to find the most efficient settings for your DB instance size\. You also need to revert back to production values for these parameters after your import completes\.


| Parameter | Recommended Value When Importing | Description | 
| --- | --- | --- | 
|  `maintenance_work_mem`  |  524288, 1048576, 2097152 or 4194304 \(in KB\)\. These settings are comparable to 512 MB, 1 GB, 2 GB, and 4 GB\.  |  The value for this setting depends on the size of your host\. This parameter is used during CREATE INDEX statements and each parallel command can use this much memory\. Calculate the best value so that you don't set this value so high that you run out of memory\.  | 
|  `checkpoint_segments`  |   256  |  The value for this setting consumes more disk space, but gives you less contention on your WAL logs\. For PostgreSQL versions 9\.5\.x and 9\.6\.x, this value would be `max_wal_size`\.  | 
|  `checkpoint_timeout`  |   1800  | The value for this setting allows for less frequent WAL rotation\. | 
| `synchronous_commit` | Off | Disable this setting to speed up writes\. Turning this parameter off can increase the risk of data loss in the event of a server crash \(do not turn off FSYNC\) | 
|  `wal_buffers`  |   8192  |  This is value is in 8 KB units\. This again helps your WAL generation speed  | 
| `autovacuum` | Off | Disable the PostgreSQL auto vacuum parameter while you are loading data so that it doesn’t use resources | 

Use the `pg_dump -Fc` \(compressed\) or `pg_restore -j` \(parallel\) commands with these settings\.

**Note**  
The PostgreSQL command `pg_dumpall` requires super\_user permissions that are not granted when you create a DB instance, so it cannot be used for importing data\.

**Topics**
+ [Importing a PostgreSQL Database from an Amazon EC2 Instance](#PostgreSQL.Procedural.Importing.EC2)
+ [Using the \\copy Command to Import Data to a Table on a PostgreSQL DB Instance](#PostgreSQL.Procedural.Importing.Copy)
+ [Importing Amazon S3 Data into an RDS PostgreSQL DB Instance](USER_PostgreSQL.S3Import.md)

## Importing a PostgreSQL Database from an Amazon EC2 Instance<a name="PostgreSQL.Procedural.Importing.EC2"></a>

If you have data in a PostgreSQL server on an Amazon EC2 instance and want to move it to a PostgreSQL DB instance, you can use the following process\. The following list shows the steps to take\. Each step is discussed in more detail in the following sections\.

1. Create a file using pg\_dump that contains the data to be loaded

1. Create the target DB instance

1. Use *psql* to create the database on the DB instance and load the data

1. Create a DB snapshot of the DB instance

### Step 1: Create a File Using pg\_dump That Contains the Data to Load<a name="PostgreSQL.Procedural.Importing.EC2.Step1"></a>

The `pg_dump` utility uses the COPY command to create a schema and data dump of a PostgreSQL database\. The dump script generated by `pg_dump` loads data into a database with the same name and recreates the tables, indexes, and foreign keys\. You can use the `pg_restore` command and the `-d` parameter to restore the data to a database with a different name\.

Before you create the data dump, you should query the tables to be dumped to get a row count so you can confirm the count on the target DB instance\.

 The following command creates a dump file called mydb2dump\.sql for a database called mydb2\.

```
prompt>pg_dump dbname=mydb2 -f mydb2dump.sql 
```

### Step 2: Create the Target DB Instance<a name="PostgreSQL.Procedural.Importing.EC2.Step2"></a>

Create the target PostgreSQL DB instance using either the Amazon RDS console, AWS CLI, or API\. Create the instance with the backup retention setting set to 0 and disable Multi\-AZ\. Doing so allows faster data import\. You must create a database on the instance before you can dump the data\. The database can have the same name as the database that is contained the dumped data\. Alternatively, you can create a database with a different name\. In this case, you use the `pg_restore` command and the `-d` parameter to restore the data into the newly named database\.

For example, the following commands can be used to dump, restore, and rename a database\.

```
pg_dump -Fc -v -h [endpoint of instance] -U [master username] [database] > [database].dump
createdb [new database name]
pg_restore -v -h [endpoint of instance] -U [master username] -d [new database name] [database].dump
```

### Step 3: Use psql to Create the Database on the DB Instance and Load Data<a name="PostgreSQL.Procedural.Importing.EC2.Step3"></a>

You can use the same connection you used to execute the pg\_dump command to connect to the target DB instance and recreate the database\. Using *psql*, you can use the master user name and master password to create the database on the DB instance

The following example uses *psql* and a dump file named mydb2dump\.sql to create a database called mydb2 on a PostgreSQL DB instance called mypginstance:

For Linux, OS X, or Unix:

```
psql \
   -f mydb2dump.sql \
   --host mypginstance.c6c8mntzhgv0.us-west-2.rds.amazonaws.com \
   --port 8199 \
   --username myawsuser \
   --password password \
   --dbname mydb2
```

For Windows:

```
psql ^
   -f mydb2dump.sql ^
   --host mypginstance.c6c8mntzhgv0.us-west-2.rds.amazonaws.com ^
   --port 8199 ^
   --username myawsuser ^
   --password password ^
   --dbname mydb2
```

### Step 4: Create a DB Snapshot of the DB Instance<a name="PostgreSQL.Procedural.Importing.EC2.Step4"></a>

Once you have verified that the data was loaded into your DB instance, we recommend that you create a DB snapshot of the target PostgreSQL DB instance\. DB snapshots are complete backups of your DB instance that can be used to restore your DB instance to a known state\. A DB snapshot taken immediately after the load protects you from having to load the data again in case of a mishap\. You can also use such a snapshot to seed new DB instances\. For information about creating a DB snapshot, see [Creating a DB Snapshot](USER_CreateSnapshot.md)\.

## Using the \\copy Command to Import Data to a Table on a PostgreSQL DB Instance<a name="PostgreSQL.Procedural.Importing.Copy"></a>

You can run the `\copy` command from the *psql* prompt to import data into a table on a PostgreSQL DB instance\. The table must already exist on the DB instance\. For more information on the \\copy command, see the [PostgreSQL documentation](http://www.postgresql.org/docs/current/static/app-psql.html)\.

**Note**  
The `\copy` command doesn't provide confirmation of actions, such as a count of rows inserted\. PostgreSQL does provide error messages if the copy command fails due to an error\.

Create a \.csv file from the data in the source table, log on to the target database on the PostgreSQL instance using *psql*, and then run the following command\. This example uses *source\-table* as the source table name, *source\-table\.csv* as the \.csv file, and *target\-db* as the target database:

```
target-db=> \copy source-table from 'source-table.csv' with DELIMITER ','; 
```

You can also run the following command from your client computer command prompt\. This example uses *source\-table* as the source table name, *source\-table\.csv* as the \.csv file, and *target\-db* as the target database:

For Linux, OS X, or Unix:

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