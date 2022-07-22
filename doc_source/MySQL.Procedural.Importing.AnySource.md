# Importing data from any source to a MariaDB or MySQL DB instance<a name="MySQL.Procedural.Importing.AnySource"></a>

If you have more than 1 GiB of data to load, or if your data is coming from somewhere other than a MariaDB or MySQL database, we recommend creating flat files and loading them with mysqlimport\. The mysqlimport utility is another command line utility bundled with the MySQL and MariaDB client software\. Its purpose is to load flat files into MySQL or MariaDB\. For information about mysqlimport, see [mysqlimport \- a data import program](https://dev.mysql.com/doc/refman/8.0/en/mysqlimport.html) in the MySQL documentation\.

We also recommend creating DB snapshots of the target Amazon RDS DB instance before and after the data load\. Amazon RDS DB snapshots are complete backups of your DB instance that can be used to restore your DB instance to a known state\. When you initiate a DB snapshot, I/O operations to your DB instance are momentarily suspended while your database is backed up\. 

Creating a DB snapshot immediately before the load makes it possible for you to restore the database to its state before the load, if you need to\. A DB snapshot taken immediately after the load protects you from having to load the data again in case of a mishap and can also be used to seed new database instances\. 

The following list shows the steps to take\. Each step is discussed in more detail following\.

1. Create flat files containing the data to be loaded\.

1. Stop any applications accessing the target DB instance\.

1. Create a DB snapshot\.

1. Consider turning off Amazon RDS automated backups\.

1. Load the data using mysqlimport\.

1. Enable automated backups again\.

## Step 1: Create flat files containing the data to be loaded<a name="MySQL.Procedural.Importing.AnySource.Step1"></a>

Use a common format, such as comma\-separated values \(CSV\), to store the data to be loaded\. Each table must have its own file; you can't combine data for multiple tables in the same file\. Give each file the same name as the table it corresponds to\. The file extension can be anything you like\. For example, if the table name is `sales`, the file name might be `sales.csv` or `sales.txt`, but not `sales_01.csv`\.

Whenever possible, order the data by the primary key of the table being loaded\. Doing this drastically improves load times and minimizes disk storage requirements\. 

The speed and efficiency of this procedure depends on keeping the size of the files small\. If the uncompressed size of any individual file is larger than 1 GiB, split it into multiple files and load each one separately\.

On Unix\-like systems \(including Linux\), use the `split` command\. For example, the following command splits the `sales.csv` file into multiple files of less than 1 GiB, splitting only at line breaks \(\-C 1024m\)\. The new files are named `sales.part_00`, `sales.part_01`, and so on\. 

```
split -C 1024m -d sales.csv sales.part_ 
```

Similar utilities are available for other operating systems\.

## Step 2: Stop any applications accessing the target DB instance<a name="MySQL.Procedural.Importing.AnySource.Step2"></a>

Before starting a large load, stop all application activity accessing the target DB instance that you plan to load to\. We recommend this particularly if other sessions will be modifying the tables being loaded or tables that they reference\. Doing this reduces the risk of constraint violations occurring during the load and improves load performance\. It also makes it possible to restore the DB instance to the point just before the load without losing changes made by processes not involved in the load\. 

Of course, this might not be possible or practical\. If you can't stop applications from accessing the DB instance before the load, take steps to ensure the availability and integrity of your data\. The specific steps required vary greatly depending upon specific use cases and site requirements\. 

## Step 3: Create a DB snapshot<a name="MySQL.Procedural.Importing.AnySource.Step3"></a>

If you plan to load data into a new DB instance that contains no data, you can skip this step\. Otherwise, creating a DB snapshot of your DB instance makes it possible for you to restore the DB instance to the point just before the load, if it becomes necessary\. As previously mentioned, when you initiate a DB snapshot, I/O operations to your DB instance are suspended for a few minutes while the database is backed up\. 

The example following uses the AWS CLI `create-db-snapshot` command to create a DB snapshot of the `AcmeRDS` instance and give the DB snapshot the identifier `"preload"`\.

For Linux, macOS, or Unix:

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

You can also use the restore from DB snapshot functionality to create test DB instances for dry runs or to undo changes made during the load\. 

Keep in mind that restoring a database from a DB snapshot creates a new DB instance that, like all DB instances, has a unique identifier and endpoint\. To restore the DB instance without changing the endpoint, first delete the DB instance so that you can reuse the endpoint\. 

For example, to create a DB instance for dry runs or other testing, you give the DB instance its own identifier\. In the example, `AcmeRDS-2`" is the identifier\. The example connects to the DB instance using the endpoint associated with `AcmeRDS-2`\. 

For Linux, macOS, or Unix:

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

To reuse the existing endpoint, first delete the DB instance and then give the restored database the same identifier\.

For Linux, macOS, or Unix:

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

The preceding example takes a final DB snapshot of the DB instance before deleting it\. This is optional but recommended\. 

## Step 4: Consider turning off Amazon RDS automated backups<a name="MySQL.Procedural.Importing.AnySource.Step4"></a>

**Warning**  
Do not turn off automated backups if you need to perform point\-in\-time recovery\.

Turning off automated backups erases all existing backups, so point\-in\-time recovery isn't possible after automated backups have been turned off\. Disabling automated backups is a performance optimization and isn't required for data loads\. Manual DB snapshots aren't affected by turning off automated backups\. All existing manual DB snapshots are still available for restore\.

Turning off automated backups reduces load time by about 25 percent and reduces the amount of storage space required during the load\. If you plan to load data into a new DB instance that contains no data, turning off backups is an easy way to speed up the load and avoid using the additional storage needed for backups\. However, in some cases you might plan to load into a DB instance that already contains data\. If so, weigh the benefits of turning off backups against the impact of losing the ability to perform point\-in\-time\-recovery\. 

DB instances have automated backups turned on by default \(with a one day retention period\)\. To turn off automated backups, set the backup retention period to zero\. After the load, you can turn backups back on by setting the backup retention period to a nonzero value\. To turn on or turn off backups, Amazon RDS shuts the DB instance down and restarts it to turn MariaDB or MySQL logging on or off\. 

Use the AWS CLI `modify-db-instance` command to set the backup retention to zero and apply the change immediately\. Setting the retention period to zero requires a DB instance restart, so wait until the restart has completed before proceeding\.

For Linux, macOS, or Unix:

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

You can check the status of your DB instance with the AWS CLI `describe-db-instances` command\. The following example displays the DB instance status of the `AcmeRDS` DB instance\. 

```
aws rds describe-db-instances --db-instance-identifier AcmeRDS --query "*[].{DBInstanceStatus:DBInstanceStatus}"
```

When the DB instance status is `available`, you're ready to proceed\. 

## Step 5: Load the data<a name="MySQL.Procedural.Importing.AnySource.Step5"></a>

Use the mysqlimport utility to load the flat files into Amazon RDS\. The following example tells mysqlimport to load all of the files named "sales" with an extension starting with "part\_"\. This is a convenient way to load all of the files created in the "split" example\. 

Use the \-\-compress option to minimize network traffic\. The \-\-fields\-terminated\-by=',' option is used for CSV files, and the \-\-local option specifies that the incoming data is located on the client\. Without the \-\-local option, the Amazon RDS DB instance looks for the data on the database host, so always specify the \-\-local option\. For the \-\-host option, specify the DB instance endpoint of the RDS for MySQL DB instance\.

In the following examples, replace `master_user` with the master username for your DB instance\.

Replace `hostname` with the endpoint for your DB instance\. An example of a DB instance endpoint is `my-db-instance.123456789012.us-west-2.rds.amazonaws.com`\.

For RDS for MySQL version 8\.0\.15 and higher, run the following statement before using the mysqlimport utility\.

```
GRANT SESSION_VARIABLES_ADMIN ON *.* TO master_user;
```

For Linux, macOS, or Unix:

```
mysqlimport --local \
    --compress \
    --user=master_user \
    --password \
    --host=hostname \
    --fields-terminated-by=',' Acme sales.part_*
```

For Windows:

```
mysqlimport --local ^
    --compress ^
    --user=master_user ^
    --password ^
    --host=hostname ^
    --fields-terminated-by="," Acme sales.part_*
```

For very large data loads, take additional DB snapshots periodically between loading files and note which files have been loaded\. If a problem occurs, you can easily resume from the point of the last DB snapshot, avoiding lengthy reloads\. 

## Step 6: Turn Amazon RDS automated backups back on<a name="MySQL.Procedural.Importing.AnySource.Step6"></a>

After the load is finished, turn Amazon RDS automated backups on by setting the backup retention period back to its preload value\. As noted earlier, Amazon RDS restarts the DB instance, so be prepared for a brief outage\. 

The following example uses the AWS CLI `modify-db-instance` command to turn on automated backups for the `AcmeRDS` DB instance and set the retention period to one day\.

For Linux, macOS, or Unix:

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