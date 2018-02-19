# Loading Data into an Amazon Aurora MySQL DB Cluster from Text Files in an Amazon S3 Bucket<a name="AuroraMySQL.Integrating.LoadFromS3"></a>

You can use the `LOAD DATA FROM S3` or `LOAD XML FROM S3` statement to load data from files stored in an Amazon S3 bucket\. 

**Note**  
Loading data into a table from text files in an Amazon S3 bucket is available for Amazon Aurora MySQL version 1\.8 and later\. For more information about Aurora MySQL versions, see [Amazon Aurora MySQL Database Engine Updates](AuroraMySQL.Updates.md)\.

## Giving Aurora Access to Amazon S3<a name="AuroraMySQL.Integrating.LoadFromS3.Authorize"></a>

Before you can load data from an Amazon S3 bucket, you must give your Aurora MySQL DB cluster permission to access Amazon S3\. To grant permission, create an AWS Identity and Access Management \(IAM\) role with the necessary permissions, and then associate the role with your DB cluster\. You must also configure your Aurora MySQL DB cluster to allow outbound connections to Amazon S3\. For details and instructions on how to permit your Aurora MySQL DB cluster to communicate with Amazon S3 on your behalf, see [Setting Up IAM Roles to Access AWS Services](AuroraMySQL.Integrating.Authorizing.IAM.md)\. 

**Note**  
You must set either the `aurora_load_from_s3_role` or `aws_default_s3_role` DB cluster parameter to the Amazon Resource Name \(ARN\) of the new IAM role\. If an IAM role isn't specified for the `aurora_load_from_s3_role`, the IAM role specified in `aws_default_s3_role` is used\.   
For more information about DB cluster parameters, see [Amazon Aurora DB Cluster and DB Instance Parameters](Aurora.Managing.md#Aurora.Managing.ParameterGroups)\.

## Granting Privileges to Load Data in Amazon Aurora MySQL<a name="AuroraMySQL.Integrating.LoadFromS3.Grant"></a>

The database user that issues the `LOAD DATA FROM S3` or `LOAD XML FROM S3` statement must be granted the `LOAD FROM S3` privilege to issue either statement\. The master user name for a DB cluster is granted the `LOAD FROM S3` privilege by default\. You can grant the privilege to another user by using the following statement\.

```
GRANT LOAD FROM S3 ON *.* TO 'user'@'domain-or-ip-address'
```

The `LOAD FROM S3` privilege is specific to Amazon Aurora and is not available for MySQL databases or RDS MySQL DB instances\. If you have set up replication between an Aurora DB cluster as the replication master and a MySQL database as the replication client, then the `GRANT LOAD FROM S3` statement causes replication to stop with an error\. You can safely skip the error to resume replication\. To skip the error on an RDS MySQL DB instance, use the [mysql\.rds\_skip\_repl\_error](mysql_rds_skip_repl_error.md) statement\. To skip the error on an external MySQL database, use the [SET GLOBAL sql\_slave\_skip\_counter ](http://dev.mysql.com/doc/refman/5.6/en/set-global-sql-slave-skip-counter.html) statement\.

## Specifying a Path to an Amazon S3 Bucket<a name="AuroraMySQL.Integrating.LoadFromS3.URI"></a>

The syntax for specifying a path to files stored on an Amazon S3 bucket is as follows\.

```
s3-region://bucket-name/file-name-or-prefix
```

The path includes the following values:

+ `region` \(optional\) – The AWS Region that contains the Amazon S3 bucket to load from\. This value is optional\. If you don't specify a `region` value, then Aurora loads your file from Amazon S3 in the same region as your DB cluster\.

+ `bucket-name` – The name of the Amazon S3 bucket that contains the data to load\. Object prefixes that identify a virtual folder path are supported\.

+ `file-name-or-prefix` – The name of the Amazon S3 text file or XML file, or a prefix that identifies one or more text or XML files to load\. You can also specify a manifest file that identifies one or more text files to load\. For more information about using a manifest file to load text files from Amazon S3, see [Using a Manifest to Specify Data Files to Load](#AuroraMySQL.Integrating.LoadFromS3.Manifest)\.

## LOAD DATA FROM S3<a name="AuroraMySQL.Integrating.LoadFromS3.Text"></a>

You can use the `LOAD DATA FROM S3` statement to load data from any text file format that is supported by the MySQL [LOAD DATA INFILE](https://dev.mysql.com/doc/refman/5.6/en/load-data.html) statement, such as text data that is comma\-delimited\. Compressed files are not supported\.

### Syntax<a name="AuroraMySQL.Integrating.LoadFromS3.Text.Syntax"></a>

```
LOAD DATA FROM S3 [FILE | PREFIX | MANIFEST] 'S3-URI'
    [REPLACE | IGNORE]
    INTO TABLE tbl_name
    [PARTITION (partition_name,...)]
    [CHARACTER SET charset_name]
    [{FIELDS | COLUMNS}
        [TERMINATED BY 'string']
        [[OPTIONALLY] ENCLOSED BY 'char']
        [ESCAPED BY 'char']
    ]
    [LINES
        [STARTING BY 'string']
        [TERMINATED BY 'string']
    ]
    [IGNORE number {LINES | ROWS}]
    [(col_name_or_user_var,...)]
    [SET col_name = expr,...]
```

### Parameters<a name="AuroraMySQL.Integrating.LoadFromS3.Text.Parameters"></a>

Following, you can find a list of the required and optional parameters used by the `LOAD DATA FROM S3` statement\. You can find more details about some of these parameters in [LOAD DATA INFILE Syntax](https://dev.mysql.com/doc/refman/5.6/en/load-data.html) in the MySQL documentation\.

+ **FILE | PREFIX | MANIFEST** – Identifies whether to load the data from a single file, from all files that match a given prefix, or from all files in a specified manifest\. `FILE` is the default\.

+ **S3\-URI** – Specifies the URI for a text or manifest file to load, or an Amazon S3 prefix to use\. Specify the URI using the syntax described in [Specifying a Path to an Amazon S3 Bucket](#AuroraMySQL.Integrating.LoadFromS3.URI)\.

+ **REPLACE | IGNORE** – Determines what action to take if an input row as the same unique key values as an existing row in the database table\.

  + Specify `REPLACE` if you want the input row to replace the existing row in the table\.

  + Specify `IGNORE` if you want to discard the input row\. `IGNORE` is the default\.

+ **INTO TABLE** – Identifies the name of the database table to load the input rows into\.

+ **PARTITION** – Requires that all input rows be inserted into the partitions identified by the specified list of comma\-separated partition names\. If an input row cannot be inserted into one of the specified partitions, then the statement fails and an error is returned\.

+ **CHARACTER SET** – Identifies the character set of the data in the input file\.

+ **FIELDS | COLUMNS** – Identifies how the fields or columns in the input file are delimited\. Fields are tab\-delimited by default\.

+ **LINES** – Identifies how the lines in the input file are delimited\. Lines are delimited by a carriage return by default\.

+ **IGNORE *number* LINES | ROWS** – Specifies to ignore a certain number of lines or rows at the start of the input file\. For example, you can use `IGNORE 1 LINES` to skip over an initial header line containing column names, or `IGNORE 2 ROWS` to skip over the first two rows of data in the input file\.

+ **col\_name\_or\_user\_var, \.\.\.** – Specifies a comma\-separated list of one or more column names or user variables that identify which columns to load by name\. The name of a user variable used for this purpose must match the name of an element from the text file, prefixed with @\. You can employ user variables to store the corresponding field values for subsequent reuse\. 

  For example, the following statement loads the first column from the input file into the first column of `table1`, and sets the value of the `table_column2` column in `table1` to the input value of the second column divided by 100\.

  ```
  LOAD DATA FROM S3 's3://mybucket/data.txt'
      INTO TABLE table1
      (column1, @var1)
      SET table_column2 = @var1/100;
  ```

+ **SET** – Specifies a comma\-separated list of assignment operations that set the values of columns in the table to values not included in the input file\.

  For example, the following statement sets the first two columns of `table1` to the values in the first two columns from the input file, and then sets the value of the `column3` in `table1` to the current time stamp\.

  ```
  LOAD DATA FROM S3  's3://mybucket/data.txt'
      INTO TABLE table1
      (column1, column2)
      SET column3 = CURRENT_TIMESTAMP;
  ```

  You can use subqueries in the right side of `SET` assignments\. For a subquery that returns a value to be assigned to a column, you can use only a scalar subquery\. Also, you cannot use a subquery to select from the table that is being loaded\. 

You cannot use the `LOCAL` keyword of the `LOAD DATA FROM S3` statement if you are loading data from an Amazon S3 bucket\.

### Using a Manifest to Specify Data Files to Load<a name="AuroraMySQL.Integrating.LoadFromS3.Manifest"></a>

You can use the `LOAD DATA FROM S3` statement with the `MANIFEST` keyword to specify a manifest file in JSON format that lists the text files to be loaded into a table in your DB cluster\. You must be using Aurora 1\.11 or greater to use the `MANIFEST` keyword with the `LOAD DATA FROM S3` statement\.

The following JSON schema describes the format and content of a manifest file\.

```
{
    "$schema": "http://json-schema.org/draft-04/schema#",
    "additionalProperties": false,
    "definitions": {},
    "id": "Aurora_LoadFromS3_Manifest",
    "properties": {
        "entries": {
            "additionalItems": false,
            "id": "/properties/entries",
            "items": {
                "additionalProperties": false,
                "id": "/properties/entries/items",
                "properties": {
                    "mandatory": {
                        "default": "false"
                        "id": "/properties/entries/items/properties/mandatory",
                        "type": "boolean"
                    },
                    "url": {
                        "id": "/properties/entries/items/properties/url",
                        "maxLength": 1024,
                        "minLength": 1,
                        "type": "string"
                    }
                },
                "required": [
                    "url"
                ],
                "type": "object"
            },
            "type": "array",
            "uniqueItems": true
        }
    },
    "required": [
        "entries"
    ],
    "type": "object"
}
```

Each `url` in the manifest must specify a URL with the bucket name and full object path for the file, not just a prefix\. You can use a manifest to load files from different buckets, different regions, or files that do not share the same prefix\. If a region is not specified in the URL, the region of the target Aurora DB cluster is used\. The following example shows a manifest file that loads four files from different buckets\.

```
{
  "entries": [
    {
      "url":"s3://aurora-bucket/2013-10-04-customerdata", 
      "mandatory":true
    },
    {
      "url":"s3-us-west-2://aurora-bucket-usw2/2013-10-05-customerdata",
      "mandatory":true
    },
    {
      "url":"s3://aurora-bucket/2013-10-04-customerdata", 
      "mandatory":false
    },
    {
      "url":"s3://aurora-bucket/2013-10-05-customerdata"
    }
  ]
}
```

The optional `mandatory` flag specifies whether `LOAD DATA FROM S3` should return an error if the file is not found\. The `mandatory` flag defaults to `false`\. Regardless of how `mandatory` is set, `LOAD DATA FROM S3` terminates if no files are found\.

Manifest files can have any extension\. The following example runs the `LOAD DATA FROM S3` statement with the manifest in the previous example, which is named **customer\.manifest**\. 

```
LOAD DATA FROM S3 MANIFEST 's3-us-west-2://aurora-bucket/customer.manifest'
    INTO TABLE CUSTOMER
    FIELDS TERMINATED BY ','
    LINES TERMINATED BY '\n'
    (ID, FIRSTNAME, LASTNAME, EMAIL);
```

After the statement completes, an entry for each successfully loaded file is written to the `aurora_s3_load_history` table\. 

#### Verifying Loaded Files Using the aurora\_s3\_load\_history Table<a name="AuroraMySQL.Integrating.LoadFromS3.Manifest.History"></a>

Every successful `LOAD DATA FROM S3` statement updates the `aurora_s3_load_history` table in the `mysql` schema with an entry for each file that was loaded\.

After you run the `LOAD DATA FROM S3` statement, you can verify which files were loaded by querying the `aurora_s3_load_history` table\. To see the files that were loaded from one execution of the statement, use the `WHERE` clause to filter the records on the Amazon S3 URI for the manifest file used in the statement\. If you have used the same manifest file before, filter the results using the `timestamp` field\.

```
select * from mysql.aurora_s3_load_history where load_prefix = 'S3_URI';
```

The following table describes the fields in the `aurora_s3_load_history` table\.


| Field | Description | 
| --- | --- | 
| `load_prefix` |  <a name="para_yt5_qkv_sy"></a>The URI that was specified in the load statement\. This URI can map to any of the following: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/AuroraMySQL.Integrating.LoadFromS3.html)  | 
|  `file_name`  |  The name of a file that was loaded into Aurora from Amazon S3 using the URI identified in the `load_prefix` field\.  | 
| `version_number` |  The version number of the file identified by the `file_name` field that was loaded, if the Amazon S3 bucket has a version number\.  | 
|  `bytes_loaded`  |  The size of the file loaded, in bytes\.  | 
| `load_timestamp`  |  The timestamp when the `LOAD DATA FROM S3` statement completed\.  | 

### Examples<a name="AuroraMySQL.Integrating.LoadFromS3.Text.Examples"></a>

The following statement loads data from an Amazon S3 bucket that is in the same region as the Aurora DB cluster\. The statement reads the comma\-delimited data in the file `customerdata.txt` that is in the `dbbucket` Amazon S3 bucket, and then loads the data into the table `store-schema.customer-table`\.

```
LOAD DATA FROM S3 's3://dbbucket/customerdata.csv' 
    INTO TABLE store-schema.customer-table
    FIELDS TERMINATED BY ','
    LINES TERMINATED BY '\n'
    (ID, FIRSTNAME, LASTNAME, ADDRESS, EMAIL, PHONE);
```

The following statement loads data from an Amazon S3 bucket that is in a different region from the Aurora DB cluster\. The statement reads the comma\-delimited data from all files that match the `employee-data` object prefix in the `my-data` Amazon S3 bucket in the `us-west-2` region, and then loads the data into the `employees` table\.

```
LOAD DATA FROM S3 PREFIX 's3-us-west-2://my-data/employee_data'
    INTO TABLE employees
    FIELDS TERMINATED BY ','
    LINES TERMINATED BY '\n'
    (ID, FIRSTNAME, LASTNAME, EMAIL, SALARY);
```

The following statement loads data from the files specified in a JSON manifest file named q1\_sales\.json into the `sales` table\. 

```
LOAD DATA FROM S3 MANIFEST 's3-us-west-2://aurora-bucket/q1_sales.json'
    INTO TABLE sales
    FIELDS TERMINATED BY ','
    LINES TERMINATED BY '\n'
    (MONTH, STORE, GROSS, NET);
```

## LOAD XML FROM S3<a name="AuroraMySQL.Integrating.LoadFromS3.XML"></a>

You can use the `LOAD XML FROM S3` statement to load data from XML files stored on an Amazon S3 bucket in one of three different XML formats:

+ Column names as attributes of a `<row>` element\. The attribute value identifies the contents of the table field\.

  ```
  <row column1="value1" column2="value2" .../>
  ```

+ Column names as child elements of a `<row>` element\. The value of the child element identifies the contents of the table field\.

  ```
  <row>
    <column1>value1</column1>
    <column2>value2</column2>
  </row>
  ```

+ Column names in the `name` attribute of `<field>` elements in a `<row>` element\. The value of the `<field>` element identifies the contents of the table field\. 

  ```
  <row>
    <field name='column1'>value1</field>
    <field name='column2'>value2</field>
  </row>
  ```

### Syntax<a name="AuroraMySQL.Integrating.LoadFromS3.XML.Syntax"></a>

```
LOAD XML FROM S3 'S3-URI'
    [REPLACE | IGNORE]
    INTO TABLE tbl_name
    [PARTITION (partition_name,...)]
    [CHARACTER SET charset_name]
    [ROWS IDENTIFIED BY '<element-name>']
    [IGNORE number {LINES | ROWS}]
    [(field_name_or_user_var,...)]
    [SET col_name = expr,...]
```

### Parameters<a name="AuroraMySQL.Integrating.LoadFromS3.XML.Parameters"></a>

Following, you can find a list of the required and optional parameters used by the `LOAD DATA FROM S3` statement\. You can find more details about some of these parameters in [LOAD XML Syntax](https://dev.mysql.com/doc/refman/5.6/en/load-xml.html) in the MySQL documentation\.

+ **FILE | PREFIX** – Identifies whether to load the data from a single file, or from all files that match a given prefix\. `FILE` is the default\.

+ **REPLACE | IGNORE** – Determines what action to take if an input row as the same unique key values as an existing row in the database table\.

  + Specify `REPLACE` if you want the input row to replace the existing row in the table\.

  + Specify `IGNORE` if you want to discard the input row\. `IGNORE` is the default\.

+ **INTO TABLE** – Identifies the name of the database table to load the input rows into\.

+ **PARTITION** – Requires that all input rows be inserted into the partitions identified by the specified list of comma\-separated partition names\. If an input row cannot be inserted into one of the specified partitions, then the statement fails and an error is returned\.

+ **CHARACTER SET** – Identifies the character set of the data in the input file\.

+ **ROWS IDENTIFIED BY** – Identifies the element name that identifies a row in the input file\. The default is `<row>`\.

+ **IGNORE *number* LINES | ROWS** – Specifies to ignore a certain number of lines or rows at the start of the input file\. For example, you can use `IGNORE 1 LINES` to skip over the first line in the text file, or `IGNORE 2 ROWS` to skip over the first two rows of data in the input XML\.

+ **field\_name\_or\_user\_var, \.\.\.** – Specifies a comma\-separated list of one or more XML element names or user variables that identify which elements to load by name\. The name of a user variable used for this purpose must match the name of an element from the XML file, prefixed with @\. You can employ user variables to store the corresponding field values for subsequent reuse\. 

  For example, the following statement loads the first column from the input file into the first column of `table1`, and sets the value of the `table_column2` column in `table1` to the input value of the second column divided by 100\.

  ```
  LOAD XML FROM S3 's3://mybucket/data.xml'
     INTO TABLE table1
     (column1, @var1)
     SET table_column2 = @var1/100;
  ```

+ **SET** – Specifies a comma\-separated list of assignment operations that set the values of columns in the table to values not included in the input file\.

  For example, the following statement sets the first two columns of `table1` to the values in the first two columns from the input file, and then sets the value of the `column3` in `table1` to the current time stamp\.

  ```
  LOAD XML FROM S3 's3://mybucket/data.xml'
     INTO TABLE table1
     (column1, column2)
     SET column3 = CURRENT_TIMESTAMP;
  ```

  You can use subqueries in the right side of `SET` assignments\. For a subquery that returns a value to be assigned to a column, you can use only a scalar subquery\. Also, you cannot use a subquery to select from the table that is being loaded\. 

## Related Topics<a name="AuroraMySQL.Integrating.LoadFromS3.RelatedTopics"></a>

+ [Integrating Amazon Aurora MySQL with Other AWS Services](AuroraMySQL.Integrating.md)

+ [Saving Data from an Amazon Aurora MySQL DB Cluster into Text Files in an Amazon S3 Bucket](AuroraMySQL.Integrating.SaveIntoS3.md)

+ [Amazon Aurora on Amazon RDS](CHAP_Aurora.md)

+ [Migrating Data to an Amazon Aurora DB Cluster](Aurora.Migrate.md)