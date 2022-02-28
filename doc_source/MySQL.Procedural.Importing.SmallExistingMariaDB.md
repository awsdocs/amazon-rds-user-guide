# Importing data from a MariaDB or MySQL database to a MariaDB or MySQL DB instance<a name="MySQL.Procedural.Importing.SmallExistingMariaDB"></a>

You can also import data from an existing MariaDB or MySQL database to a MySQL or MariaDB DB instance\. You do so by copying the database with [mysqldump](https://dev.mysql.com/doc/refman/8.0/en/mysqldump.html) and piping it directly into the MariaDB or MySQL DB instance\. The `mysqldump` command line utility is commonly used to make backups and transfer data from one MariaDB or MySQL server to another\. It's included with MySQL and MariaDB client software\.

**Note**  
If you are using a MySQL DB instance and your scenario supports it, it's easier to move data in and out of Amazon RDS by using backup files and Amazon S3\. For more information, see [Restoring a backup into a MySQL DB instance](MySQL.Procedural.Importing.md)\. 

A typical `mysqldump` command to move data from an external database to an Amazon RDS DB instance looks similar to the following\. 

```
mysqldump -u local_user \
    --databases database_name \
    --single-transaction \
    --compress \
    --order-by-primary  \
    -plocal_password | mysql -u RDS_user \
        --port=port_number \
        --host=host_name \
        -pRDS_password
```

**Important**  
Make sure not to leave a space between the `-p` option and the entered password\.

Make sure that you're aware of the following recommendations and considerations:
+ Exclude the following schemas from the dump file: `sys`, `performance_schema`, and `information_schema`\. The `mysqldump` utility excludes these schemas by default\.
+ If you need to migrate users and privileges, consider using a tool that generates the data control language \(DCL\) for recreating them, such as the [pt\-show\-grants](https://www.percona.com/doc/percona-toolkit/LATEST/pt-show-grants.html) utility\.
+ To perform the import, make sure the user doing so has access to the DB instance\. For more information, see [Controlling access with security groups](Overview.RDSSecurityGroups.md)\.

The parameters used are as follows:
+ `-u local_user` – Use to specify a user name\. In the first usage of this parameter, you specify the name of a user account on the local MariaDB or MySQL database identified by the `--databases` parameter\.
+ `--databases database_name` – Use to specify the name of the database on the local MariaDB or MySQL instance that you want to import into Amazon RDS\.
+ `--single-transaction` – Use to ensure that all of the data loaded from the local database is consistent with a single point in time\. If there are other processes changing the data while `mysqldump` is reading it, using this parameter helps maintain data integrity\. 
+ `--compress` – Use to reduce network bandwidth consumption by compressing the data from the local database before sending it to Amazon RDS\.
+ `--order-by-primary` – Use to reduce load time by sorting each table's data by its primary key\.
+ `-plocal_password` – Use to specify a password\. In the first usage of this parameter, you specify the password for the user account identified by the first `-u` parameter\.
+ `-u RDS_user` – Use to specify a user name\. In the second usage of this parameter, you specify the name of a user account on the default database for the MariaDB or MySQL DB instance identified by the `--host` parameter\.
+ `--port port_number` – Use to specify the port for your MariaDB or MySQL DB instance\. By default, this is 3306 unless you changed the value when creating the instance\.
+ `--host host_name` – Use to specify the Domain Name System \(DNS\) name from the Amazon RDS DB instance endpoint, for example, `myinstance.123456789012.us-east-1.rds.amazonaws.com`\. You can find the endpoint value in the instance details in the Amazon RDS Management Console\.
+ `-pRDS_password` – Use to specify a password\. In the second usage of this parameter, you specify the password for the user account identified by the second `-u` parameter\.

Make sure to create any stored procedures, triggers, functions, or events manually in your Amazon RDS database\. If you have any of these objects in the database that you are copying, then exclude them when you run `mysqldump`\. To do so, include the following parameters with your `mysqldump` command: `--routines=0 --triggers=0 --events=0`\.

The following example copies the `world` sample database on the local host to a MySQL DB instance\.

For Linux, macOS, or Unix:

```
sudo mysqldump -u localuser \
    --databases world \
    --single-transaction \
    --compress \
    --order-by-primary  \
    --routines=0 \
    --triggers=0 \
    --events=0 \
    -plocalpassword | mysql -u rdsuser \
        --port=3306 \
        --host=myinstance.123456789012.us-east-1.rds.amazonaws.com \
        -prdspassword
```

For Windows, run the following command in a command prompt that has been opened by right\-clicking **Command Prompt** on the Windows programs menu and choosing **Run as administrator**:

```
mysqldump -u localuser ^
    --databases world ^
    --single-transaction ^
    --compress ^
    --order-by-primary  ^
    --routines=0 ^
    --triggers=0 ^
    --events=0 ^
    -plocalpassword | mysql -u rdsuser ^
        --port=3306 ^
        --host=myinstance.123456789012.us-east-1.rds.amazonaws.com ^
        -prdspassword
```