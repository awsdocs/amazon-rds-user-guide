# Saving Data from an Amazon Aurora MySQL DB Cluster into Text Files in an Amazon S3 Bucket<a name="AuroraMySQL.Integrating.SaveIntoS3"></a>

You can use the `SELECT INTO OUTFILE S3` statement to query data from an Amazon Aurora MySQL DB cluster and save it directly into text files stored in an Amazon S3 bucket\. You can use this functionality to skip bringing the data down to the client first, and then copying it from the client to Amazon S3\. The `LOAD DATA FROM S3` statement can use the files created by this statement to load data into an Aurora DB cluster\. For more information, see [Loading Data into an Amazon Aurora MySQL DB Cluster from Text Files in an Amazon S3 Bucket](AuroraMySQL.Integrating.LoadFromS3.md)\.

**Note**  
Saving data from a table into text files in an Amazon S3 bucket is available for Amazon Aurora MySQL version 1\.13 and later\. For more information about Aurora MySQL versions, see [Amazon Aurora MySQL Database Engine Updates](AuroraMySQL.Updates.md)\.

## Giving Aurora MySQL Access to Amazon S3<a name="AuroraMySQL.Integrating.SaveIntoS3.Authorize"></a>

Before you can save data into an Amazon S3 bucket, you must first give your Aurora MySQL DB cluster permission to access Amazon S3\.

**To give Aurora MySQL access to Amazon S3**

1. Create an AWS Identity and Access Management \(IAM\) policy that provides the bucket and object permissions that allow your Aurora MySQL DB cluster to access Amazon S3\. For instructions, see [Creating an IAM Policy to Access Amazon S3 Resources](AuroraMySQL.Integrating.Authorizing.IAM.S3CreatePolicy.md)\.

1. Create an IAM role, and attach the IAM policy you created in [Creating an IAM Policy to Access Amazon S3 Resources](AuroraMySQL.Integrating.Authorizing.IAM.S3CreatePolicy.md) to the new IAM role\. For instructions, see [Creating an IAM Role to Allow Amazon Aurora to Access AWS Services](AuroraMySQL.Integrating.Authorizing.IAM.CreateRole.md)\.

1. Set either the `aurora_select_into_s3_role` or `aws_default_s3_role` DB cluster parameter to the Amazon Resource Name \(ARN\) of the new IAM role\. If an IAM role isn't specified for the `aurora_select_into_s3_role`, the IAM role specified in `aws_default_s3_role` is used\. 

   For more information about DB cluster parameters, see [Amazon Aurora DB Cluster and DB Instance Parameters](Aurora.Managing.md#Aurora.Managing.ParameterGroups)\.

1. To permit database users in an Aurora MySQL DB cluster to access Amazon S3, associate the role that you created in [Creating an IAM Role to Allow Amazon Aurora to Access AWS Services](AuroraMySQL.Integrating.Authorizing.IAM.CreateRole.md) with the DB cluster\. For information about associating an IAM role with a DB cluster, see [Associating an IAM Role with an Amazon Aurora MySQL DB Cluster](AuroraMySQL.Integrating.Authorizing.IAM.AddRoleToDBCluster.md)\.

1. Configure your Aurora MySQL DB cluster to allow outbound connections to Amazon S3\. For instructions, see [Enabling Network Communication from Amazon Aurora MySQL to Other AWS Services](AuroraMySQL.Integrating.Authorizing.Network.md)\.

## Granting Privileges to Save Data in Aurora MySQL<a name="AuroraMySQL.Integrating.SaveIntoS3.Grant"></a>

The database user that issues the `SELECT INTO OUTFILE S3` statement must be granted the `SELECT INTO S3` privilege to issue the statement\. The master user name for a DB cluster is granted the `SELECT INTO S3` privilege by default\. You can grant the privilege to another user by using the following statement\.

```
GRANT SELECT INTO S3 ON *.* TO 'user'@'domain-or-ip-address'
```

The `SELECT INTO S3` privilege is specific to Amazon Aurora MySQL and is not available for MySQL databases or RDS MySQL DB instances\. If you have set up replication between an Aurora MySQL DB cluster as the replication master and a MySQL database as the replication client, then the `GRANT SELECT INTO S3` statement causes replication to stop with an error\. You can safely skip the error to resume replication\. To skip the error on an RDS MySQL DB instance, use the [mysql\.rds\_skip\_repl\_error](mysql_rds_skip_repl_error.md) statement\. To skip the error on an external MySQL database, use the [SET GLOBAL sql\_slave\_skip\_counter ](http://dev.mysql.com/doc/refman/5.6/en/set-global-sql-slave-skip-counter.html) statement\.

## Specifying a Path to an Amazon S3 Bucket<a name="AuroraMySQL.Integrating.SaveIntoS3.URI"></a>

The syntax for specifying a path to store the data and manifest files on an Amazon S3 bucket is similar to that used in the `LOAD DATA FROM S3 PREFIX` statement, as shown following\.

```
s3-region://bucket-name/file-prefix
```

The path includes the following values:
+ `region` \(optional\) – The AWS Region that contains the Amazon S3 bucket to save the data into\. This value is optional\. If you don't specify a `region` value, then Aurora saves your files into Amazon S3 in the same region as your DB cluster\.
+ `bucket-name` – The name of the Amazon S3 bucket to save the data into\. Object prefixes that identify a virtual folder path are supported\.
+ `file-prefix` – The Amazon S3 object prefix that identifies the files to be saved in Amazon S3\. 

The data files created by the `SELECT INTO OUTFILE S3` statement use the following path, in which *00000* represents a 5\-digit, zero\-based integer number\.

```
s3-region://bucket-name/file-prefix.part_00000
```

For example, suppose that a `SELECT INTO OUTFILE S3` statement specifies `s3-us-west-2://bucket/prefix` as the path in which to store data files and creates three data files\. The specified Amazon S3 bucket contains the following data files\.
+ s3\-us\-west\-2://bucket/prefix\.part\_00000
+ s3\-us\-west\-2://bucket/prefix\.part\_00001
+ s3\-us\-west\-2://bucket/prefix\.part\_00002

## Creating a Manifest to List Data Files<a name="AuroraMySQL.Integrating.SaveIntoS3.Manifest"></a>

You can use the `SELECT INTO OUTFILE S3` statement with the `MANIFEST ON` option to create a manifest file in JSON format that lists the text files created by the statement\. The `LOAD DATA FROM S3` statement can use the manifest file to load the data files back into an Aurora MySQL DB cluster\. For more information about using a manifest to load data files from Amazon S3 into an Aurora MySQL DB cluster, see [Using a Manifest to Specify Data Files to Load](AuroraMySQL.Integrating.LoadFromS3.md#AuroraMySQL.Integrating.LoadFromS3.Manifest)\. 

The data files included in the manifest created by the `SELECT INTO OUTFILE S3` statement are listed in the order that they're created by the statement\. For example, suppose that a `SELECT INTO OUTFILE S3` statement specified `s3-us-west-2://bucket/prefix` as the path in which to store data files and creates three data files and a manifest file\. The specified Amazon S3 bucket contains a manifest file named `s3-us-west-2://bucket/prefix.manifest`, that contains the following information\.

```
{
  "entries": [
    {
      "url":"s3-us-west-2://bucket/prefix.part_00000"
    },
    {
      "url":"s3-us-west-2://bucket/prefix.part_00001"
    },
    {
      "url":"s3-us-west-2://bucket/prefix.part_00002"
    }
  ]
}
```

## SELECT INTO OUTFILE S3<a name="AuroraMySQL.Integrating.SaveIntoS3.Statement"></a>

You can use the `SELECT INTO OUTFILE S3` statement to query data from a DB cluster and save it directly into delimited text files stored in an Amazon S3 bucket\. Compressed or encrypted files are not supported\.

### Syntax<a name="AuroraMySQL.Integrating.SaveIntoS3.Statement.Syntax"></a>

```
SELECT
    [ALL | DISTINCT | DISTINCTROW ]
        [HIGH_PRIORITY]
        [STRAIGHT_JOIN]
        [SQL_SMALL_RESULT] [SQL_BIG_RESULT] [SQL_BUFFER_RESULT]
        [SQL_CACHE | SQL_NO_CACHE] [SQL_CALC_FOUND_ROWS]
    select_expr [, select_expr ...]
    [FROM table_references
        [PARTITION partition_list]
    [WHERE where_condition]
    [GROUP BY {col_name | expr | position}
        [ASC | DESC], ... [WITH ROLLUP]]
    [HAVING where_condition]
    [ORDER BY {col_name | expr | position}
         [ASC | DESC], ...]
    [LIMIT {[offset,] row_count | row_count OFFSET offset}]
    [PROCEDURE procedure_name(argument_list)]
INTO OUTFILE S3 's3_uri'
[CHARACTER SET charset_name]
    [export_options]
    [MANIFEST {ON | OFF}]
    [OVERWRITE {ON | OFF}]

export_options:
    [{FIELDS | COLUMNS}
        [TERMINATED BY 'string']
        [[OPTIONALLY] ENCLOSED BY 'char']
        [ESCAPED BY 'char']
    ]
    [LINES
        [STARTING BY 'string']
        [TERMINATED BY 'string']
    ]
```

### Parameters<a name="AuroraMySQL.Integrating.SaveIntoS3.Statement.Parameters"></a>

Following, you can find a list of the required and optional parameters used by the `SELECT INTO OUTFILE S3` statement that are specific to Aurora\. 
+ **s3\-uri** – Specifies the URI for an Amazon S3 prefix to use\. Specify the URI using the syntax described in [Specifying a Path to an Amazon S3 Bucket](#AuroraMySQL.Integrating.SaveIntoS3.URI)\.
+ **MANIFEST \{ON \| OFF\}** – Indicates whether a manifest file is created in Amazon S3\. The manifest file is a JavaScript Object Notation \(JSON\) file that can be used to load data into an Aurora DB cluster with the `LOAD DATA FROM S3 MANIFEST` statement\. For more information about `LOAD DATA FROM S3 MANIFEST`, see [Loading Data into an Amazon Aurora MySQL DB Cluster from Text Files in an Amazon S3 Bucket](AuroraMySQL.Integrating.LoadFromS3.md)\.

  If `MANIFEST ON` is specified in the query, the manifest file is created in Amazon S3 after all data files have been created and uploaded\. The manifest file is created using the following path: 

  ```
  s3-region://bucket-name/file-prefix.manifest
  ```

  For more information about the format of the manifest file's contents, see [Creating a Manifest to List Data Files](#AuroraMySQL.Integrating.SaveIntoS3.Manifest)\.
+ **OVERWRITE \{ON \| OFF\}** – Indicates whether existing files in the specified Amazon S3 bucket are overwritten\. If `OVERWRITE ON` is specified, existing files that match the file prefix in the URI specified in `s3-uri`are overwritten\. Otherwise, an error occurs\.

You can find more details about other parameters in [SELECT Syntax](https://dev.mysql.com/doc/refman/5.6/en/select.html) and [LOAD DATA INFILE Syntax](https://dev.mysql.com/doc/refman/5.6/en/load-data.html), in the MySQL documentation\.

### Considerations<a name="AuroraMySQL.Integrating.SaveIntoS3.Considerations"></a>

The number of files written to the Amazon S3 bucket depends on the amount of data selected by the `SELECT INTO OUTFILE S3` statement and the file size threshold for Aurora MySQL\. The default file size threshold is 6 gigabytes \(GB\)\. If the data selected by the statement is less than the file size threshold, a single file is created; otherwise, multiple files are created\. Other considerations for files created by this statement include the following:
+ Aurora MySQL guarantees that rows in data files are not split across file boundaries\. For multiple files, the size of every data file except the last is typically close to the file size threshold\. However, occasionally staying under the file size threshold results in a row being split across two data files\. In this case, Aurora MySQL creates a data file that keeps the row intact, but might be larger than the file size threshold\. 
+ Because each SELECT statement in Aurora MySQL runs as an atomic transaction, a `SELECT INTO OUTFILE S3` statement that selects a large data set might run for some time\. If the statement fails for any reason, you might need to start over and execute the statement again\. If the statement fails, however, files already uploaded to Amazon S3 remain in the specified Amazon S3 bucket\. You can use another statement to upload the remaining data instead of starting over again\.
+ If the amount of data to be selected is large \(more than 25 GB\), we recommend that you use multiple `SELECT INTO OUTFILE S3` statements to save the data to Amazon S3\. Each statement should select a different portion of the data to be saved, and also specify a different `file_prefix` in the `s3-uri` parameter to use when saving the data files\. Partitioning the data to be selected with multiple statements makes it easier to recover from execution error, because only a portion of data needs to be re\-selected and uploaded to Amazon S3 if an error occurs during the execution of a particular statement\. Using multiple statements also helps to avoid a single long\-running transaction, which can improve performance\. 
+ If multiple `SELECT INTO OUTFILE S3` statements that use the same `file_prefix` in the `s3-uri` parameter run in parallel to select data into Amazon S3, the behavior is undefined\.
+ Metadata, such as table schema or file metadata, is not uploaded by Aurora MySQL to Amazon S3\.
+ In some cases, you might re\-run a `SELECT INTO OUTFILE S3` query, such as to recover from a failure\. In these cases, you must either remove any existing data files in the Amazon S3 bucket with the same file prefix specified in `s3-uri`, or include `OVERWRITE ON` in the `SELECT INTO OUTFILE S3` query\.

The `SELECT INTO OUTFILE S3` statement returns a typical MySQL error number and response on success or failure\. If you don't have access to the MySQL error number and response, the easiest way to determine when it's done is by specifying `MANIFEST ON` in the statement\. The manifest file is the last file written by the statement\. In other words, if you have a manifest file, the statement has completed execution\.

Currently, there's no way to directly monitor the progress of the `SELECT INTO OUTFILE S3` statement during execution\. However, suppose that you're writing a large amount of data from Aurora MySQL to Amazon S3 using this statement, and you know the size of the data selected by the statement\. In this case, you can estimate progress by monitoring the creation of data files in Amazon S3\.

To do so, you can use the fact that a data file is created in the specified Amazon S3 bucket for about every 6GB of data selected by the statement\. Divide the size of the data selected by 6GB to get the estimated number of data files to create\. You can then estimate the progress of the statement by monitoring the number of files uploaded to Amazon S3 during execution\.

### Examples<a name="AuroraMySQL.Integrating.SaveIntoS3.Examples"></a>

The following statement selects all of the data in the `employees` table and saves the data into an Amazon S3 bucket that is in a different region from the Aurora MySQL DB cluster\. The statement creates data files in which each field is terminated by a comma \(`,`\) character and each row is terminated by a newline \(`\n`\) character\. The statement returns an error if files that match the `sample_employee_data` file prefix exist in the specified Amazon S3 bucket\.

```
SELECT * FROM employees INTO OUTFILE S3 's3-us-west-2://aurora-select-into-s3-pdx/sample_employee_data' 
    FIELDS TERMINATED BY ','
    LINES TERMINATED BY '\n';
```

The following statement selects all of the data in the `employees` table and saves the data into an Amazon S3 bucket that is in the same region as the Aurora MySQL DB cluster\. The statement creates data files in which each field is terminated by a comma \(`,`\) character and each row is terminated by a newline \(`\n`\) character, and also a manifest file\. The statement returns an error if files that match the `sample_employee_data` file prefix exist in the specified Amazon S3 bucket\.

```
SELECT * FROM employees INTO OUTFILE S3 's3://aurora-select-into-s3-pdx/sample_employee_data' 
    FIELDS TERMINATED BY ','
    LINES TERMINATED BY '\n'
    MANIFEST ON;
```

The following statement selects all of the data in the `employees` table and saves the data into an Amazon S3 bucket that is in a different region from the Aurora DB cluster\. The statement creates data files in which each field is terminated by a comma \(`,`\) character and each row is terminated by a newline \(`\n`\) character\. The statement overwrites any existing files that match the `sample_employee_data` file prefix in the specified Amazon S3 bucket\.

```
SELECT * FROM employees INTO OUTFILE S3 's3-us-west-2://aurora-select-into-s3-pdx/sample_employee_data' 
    FIELDS TERMINATED BY ','
    LINES TERMINATED BY '\n'
    OVERWRITE ON;
```

The following statement selects all of the data in the `employees` table and saves the data into an Amazon S3 bucket that is in the same region as the Aurora MySQL DB cluster\. The statement creates data files in which each field is terminated by a comma \(`,`\) character and each row is terminated by a newline \(`\n`\) character, and also a manifest file\. The statement overwrites any existing files that match the `sample_employee_data` file prefix in the specified Amazon S3 bucket\.

```
SELECT * FROM employees INTO OUTFILE S3 's3://aurora-select-into-s3-pdx/sample_employee_data' 
    FIELDS TERMINATED BY ','
    LINES TERMINATED BY '\n'
    MANIFEST ON
    OVERWRITE ON;
```

## Related Topics<a name="AuroraMySQL.Integrating.SaveIntoS3.RelatedTopics"></a>
+ [Integrating Aurora with Other AWS Services](Aurora.Integrating.md)
+ [Loading Data into an Amazon Aurora MySQL DB Cluster from Text Files in an Amazon S3 Bucket](AuroraMySQL.Integrating.LoadFromS3.md)
+ [Amazon Aurora on Amazon RDS](CHAP_Aurora.md)
+ [Migrating Data to an Amazon Aurora DB Cluster](Aurora.Migrate.md)