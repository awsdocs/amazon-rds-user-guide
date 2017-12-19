# Importing Data From Any Source to a MySQL or MariaDB DB Instance<a name="MySQL.Procedural.Importing.AnySource"></a>

If you have more than 1GB of data to load, or if your data is coming from somewhere other than a MySQL or MariaDB database, we recommend creating flat files and loading them with mysqlimport\. mysqlimport is another command line utility bundled with the MySQL and MariaDB client software whose purpose is to load flat files into MySQL or MariaDB\. For information about mysqlimport, see [mysqlimport \- A Data Import Program](http://dev.mysql.com/doc/refman/5.6/en/mysqlimport.html) in the MySQL documentation\.

We also recommend creating DB snapshots of the target Amazon RDS DB instance before and after the data load\. Amazon RDS DB snapshots are complete backups of your DB instance that can be used to restore your DB instance to a known state\. When you initiate a DB snapshot, I/O operations to your database instance are momentarily suspended while your database is backed up\. 

Creating a DB snapshot immediately before the load allows you restore the database to its state prior to the load, should the need arise\. A DB snapshot taken immediately after the load protects you from having to load the data again in case of a mishap and can also be used to seed new database instances\. 

The following list shows the steps to take\. Each step is discussed in more detail below\.

1. Create flat files containing the data to be loaded\.

1. Stop any applications accessing the target DB instance\.

1. Create a DB snapshot\.

1. Consider disabling Amazon RDS automated backups\.

1. Load the data using mysqlimport\.

1. Enable automated backups again\.

## Step 1: Create Flat Files Containing the Data to be Loaded<a name="MySQL.Procedural.Importing.AnySource.Step1"></a>

Use a common format, such as CSV \(Comma\-Separated Values\), to store the data to be loaded\. Each table must have its own file; data for multiple tables cannot be combined in the same file\. Give each file the same name as the table it corresponds to\. The file extension can be anything you like\. For example, if the table name is "sales", the file name could be "sales\.csv" or "sales\.txt", but not "sales\_01\.csv"\.

Whenever possible, order the data by the primary key of the table being loaded\. This drastically improves load times and minimizes disk storage requirements\. 

The speed and efficiency of this procedure is dependent upon keeping the size of the files small\. If the uncompressed size of any individual file is larger than 1GB, split it into multiple files and load each one separately\.

On Unix\-like systems \(including Linux\), use the 'split' command\. For example, the following command splits the sales\.csv file into multiple files of less than 1GB, splitting only at line breaks \(\-C 1024m\)\. The new files will be named sales\.part\_00, sales\.part\_01, etc\. 

```
split -C 1024m -d sales.csv sales.part_ 
```

Similar utilities are available on other operating systems\.

## Step 2: Stop Any Applications Accessing the Target DB Instance<a name="MySQL.Procedural.Importing.AnySource.Step2"></a>

Before starting a large load, stop all application activity accessing the target DB instance that you will be loading to \(particularly if other sessions will be modifying the tables being loaded or tables they reference\)\. This will reduce the risk of constraint violations occurring during the load, improve load performance, and make it possible to restore the database instance to the point just prior to the load without losing changes made by processes not involved in the load\. 

Of course, this may not be possible or practical\. If you are unable to stop applications from accessing the DB instance prior to the load, take steps to ensure the availability and integrity of your data\. The specific steps required vary greatly depending upon specific use cases and site requirements\. 

## Step 3: Create a DB Snapshot<a name="MySQL.Procedural.Importing.AnySource.Step3"></a>

If you will be loading data into a new DB instance that contains no data, you may skip this step\. Otherwise, creating a DB snapshot of your DB instance will allow you to restore the database Instance to the point just prior to the load, if it becomes necessary\. As previously mentioned, when you initiate a DB snapshot, I/O operations to your database instance are suspended for a few minutes while the database is backed up\. 

In the example below, we use the AWS CLI `create-db-snapshot` command to create a DB Snapshot of our AcmeRDS instance and give the DB snapshot the identifier "preload"\.

For Linux, OS X, or Unix:

```
aws rds create-db-snapshot \
    --db-instance-identifier AcmeRDS \
    --db-snapshot-identifier preload
```

For Windows:

```
aws rds create-db-snapshot ^
    --db-instance-identifier AcmeRDS ^
    --db-snapshot-identifier preload
```

You can also use the restore from DB snapshot functionality in order to create test database instances for dry runs or to "undo" changes made during the load\. 

It is important to keep in mind that restoring a database from a DB snapshot creates a new DB instance which, like all DB instances, has a unique identifier and endpoint\. If you need to restore the database instance without changing the endpoint, you must first delete the DB instance so that the endpoint can be reused\. 

For example, to create a DB instance for dry runs or other testing, you would give the DB instance its own identifier\. In the example, "AcmeRDS\-2" is the identifier and we would connect to the database instance using the endpoint associated with AcmeRDS\-2\. 

For Linux, OS X, or Unix:

```
aws rds restore-db-instance-from-db-snapshot \
    --db-instance-identifier AcmeRDS-2 \
    --db-snapshot-identifier preload
```

For Windows:

```
aws rds restore-db-instance-from-db-snapshot ^
    --db-instance-identifier AcmeRDS-2 ^
    --db-snapshot-identifier preload
```

To reuse the existing endpoint, we must first delete the database instance and then give the restored database the same identifier:

For Linux, OS X, or Unix:

```
aws rds delete-db-instance \
    --db-instance-identifier AcmeRDS \
    --final-db-snapshot-identifier AcmeRDS-Final

aws rds restore-db-instance-from-db-snapshot \
    --db-instance-identifier AcmeRDS \
    --db-snapshot-identifier preload
```

For Windows:

```
aws rds delete-db-instance ^
    --db-instance-identifier AcmeRDS ^
    --final-db-snapshot-identifier AcmeRDS-Final

aws rds restore-db-instance-from-db-snapshot ^
    --db-instance-identifier AcmeRDS ^
    --db-snapshot-identifier preload
```

Note that the example takes a final DB snapshot of the database instance before deleting it\. This is optional, but recommended\. 

## Step 4: Consider Disabling Amazon RDS Automated Backups<a name="MySQL.Procedural.Importing.AnySource.Step4"></a>

Warning: DO NOT DISABLE AUTOMATED BACKUPS IF YOU NEED TO RETAIN THE ABILITY TO PERFORM POINT\-IN\-TIME RECOVERY\. Disabling automated backups erases all existing backups, so point\-in\-time recovery will not be possible after automated backups have been disabled\. Disabling automated backups is a performance optimization and is not required for data loads\. Note that DB snapshots are not affected by disabling automated backups\. All existing DB snapshots are still available for restore\.

Disabling automated backups will reduce load time by about 25% and reduce the amount of storage space required during the load\. If you will be loading data into a new DB instance that contains no data, disabling backups is an easy way to speed up the load and avoid using the additional storage needed for backups\. However, if you will be loading into a DB instance that already contains data; you must weigh the benefits of disabling backups against the impact of losing the ability to perform point\-in\-time\-recovery\. 

DB instances have automated backups enabled by default \(with a one day retention period\)\. In order to disable automated backups, you must set the backup retention period to zero\. After the load, you can re\-enable backups by setting the backup retention period to a non\-zero value\. In order to enable or disable backups, Amazon RDS must shut the DB instance down and restart it in order to turn MySQL or MariaDB logging on or off\. 

Use the AWS CLI `modify-db-instance` command to set the backup retention to zero and apply the change immediately\. Setting the retention period to zero requires a DB instance restart, so wait until the restart has completed before proceeding\.

For Linux, OS X, or Unix:

```
aws rds modify-db-instance \
    --db-instance-identifier AcmeRDS \
    --apply-immediately \
    --backup-retention-period 0
```

For Windows:

```
aws rds modify-db-instance ^
    --db-instance-identifier AcmeRDS ^
    --apply-immediately ^
    --backup-retention-period 0
```

You can check the status of your DB instance with the AWS CLI `describe-db-instances` command\. The example displays the status of the AcmeRDS database instance and includes the \-\-headers option to show column headings\. 

For Linux, OS X, or Unix:

```
aws rds describe-db-instances \
    --db-instance-identifier AcmeRDS \
    --headers
```

For Windows:

```
aws rds describe-db-instances ^
    --db-instance-identifier AcmeRDS ^
    --headers
```

When the Status column shows that the database is available, you're ready to proceed\. 

## Step 5: Load the Data<a name="MySQL.Procedural.Importing.AnySource.Step5"></a>

Use the mysqlimport utility to load the flat files into Amazon RDS\. In the example we tell mysqlimport to load all of the files named "sales" with an extension starting with "part\_"\. This is a convenient way to load all of the files created in the "split" example\. Use the \-\-compress option to minimize network traffic\. The \-\-fields\-terminated\-by=',' option is used for CSV files and the \-\-local option specifies that the incoming data is located on the client\. Without the \-\-local option, the Amazon RDS DB instance will look for the data on the database host, so always specify the \-\-local option\.

For Linux, OS X, or Unix:

```
mysqlimport --local \
    --compress \
    --user=username \
    --password \
    --host=hostname \
    --fields-terminated-by=',' Acme sales.part_*
```

For Windows:

```
mysqlimport --local ^
    --compress ^
    --user=username ^
    --password ^
    --host=hostname ^
    --fields-terminated-by=',' Acme sales.part_*
```

For very large data loads, take additional DB snapshots periodically between loading files and note which files have been loaded\. If a problem occurs, you can easily resume from the point of the last DB snapshot, avoiding lengthy reloads\. 

## Step 6: Enable Amazon RDS Automated Backups<a name="MySQL.Procedural.Importing.AnySource.Step6"></a>

Once the load is finished, re\-enable Amazon RDS automated backups by setting the backup retention period back to its pre\-load value\. As noted earlier, Amazon RDS will restart the DB instance, so be prepared for a brief outage\. 

In the example, we use the AWS CLI modify\-db\-instance command to enable automated backups for the AcmeRDS DB instance and set the retention period to 1 day\.

For Linux, OS X, or Unix:

```
aws rds modify-db-instance \
    --db-instance-identifier AcmeRDS \
    --backup-retention-period 1 \
    --apply-immediately
```

For Windows:

```
aws rds modify-db-instance ^
    --db-instance-identifier AcmeRDS ^
    --backup-retention-period 1 ^
    --apply-immediately
```