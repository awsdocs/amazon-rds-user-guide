# Parameters for MariaDB<a name="Appendix.MariaDB.Parameters"></a>

By default, a MariaDB DB instance uses a DB parameter group that is specific to a MariaDB database\. This parameter group contains some but not all of the parameters contained in the Amazon RDS DB parameter groups for the MySQL database engine\. It also contains a number of new, MariaDB\-specific parameters\. For information about working with parameter groups and setting parameters, see [Working with parameter groups](USER_WorkingWithParamGroups.md)\.

## Viewing MariaDB parameters<a name="Appendix.MariaDB.Parameters.Viewing"></a>

RDS for MariaDB parameters are set to the default values of the storage engine that you have selected\. For more information about MariaDB parameters, see the [MariaDB documentation](http://mariadb.com/kb/en/mariadb/documentation/)\. For more information about MariaDB storage engines, see [Supported storage engines for MariaDB on Amazon RDS](MariaDB.Concepts.FeatureSupport.md#MariaDB.Concepts.Storage)\.

You can view the parameters available for a specific RDS for MariaDB version using the RDS console or the AWS CLI\. For information about viewing the parameters in a MariaDB parameter group in the RDS console, see [Viewing parameter values for a DB parameter group](USER_WorkingWithDBInstanceParamGroups.md#USER_WorkingWithParamGroups.Viewing)\.

Using the AWS CLI, you can view the parameters for an RDS for MariaDB version by running the [https://docs.aws.amazon.com/cli/latest/reference/rds/describe-engine-default-parameters.html](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-engine-default-parameters.html) command\. Specify one of the following values for the `--db-parameter-group-family` option:
+ `mariadb10.6`
+ `mariadb10.5`
+ `mariadb10.4`
+ `mariadb10.3`

For example, to view the parameters for RDS for MariaDB version 10\.6, run the following command\.

```
aws rds describe-engine-default-parameters --db-parameter-group-family mariadb10.6
```

Your output looks similar to the following\.

```
{
    "EngineDefaults": {
        "Parameters": [
            {
                "ParameterName": "alter_algorithm",
                "Description": "Specify the alter table algorithm.",
                "Source": "engine-default",
                "ApplyType": "dynamic",
                "DataType": "string",
                "AllowedValues": "DEFAULT,COPY,INPLACE,NOCOPY,INSTANT",
                "IsModifiable": true
            },
            {
                "ParameterName": "analyze_sample_percentage",
                "Description": "Percentage of rows from the table ANALYZE TABLE will sample to collect table statistics.",
                "Source": "engine-default",
                "ApplyType": "dynamic",
                "DataType": "float",
                "AllowedValues": "0-100",
                "IsModifiable": true
            },
            {
                "ParameterName": "aria_block_size",
                "Description": "Block size to be used for Aria index pages.",
                "Source": "engine-default",
                "ApplyType": "static",
                "DataType": "integer",
                "AllowedValues": "1024-32768",
                "IsModifiable": false
            },
            {
                "ParameterName": "aria_checkpoint_interval",
                "Description": "Interval in seconds between automatic checkpoints.",
                "Source": "engine-default",
                "ApplyType": "dynamic",
                "DataType": "integer",
                "AllowedValues": "0-4294967295",
                "IsModifiable": true
            },
        ...
```

To list only the modifiable parameters for RDS for MariaDB version 10\.6, run the following command\.

For Linux, macOS, or Unix:

```
aws rds describe-engine-default-parameters --db-parameter-group-family mariadb10.6 \
   --query 'EngineDefaults.Parameters[?IsModifiable==`true`]'
```

For Windows:

```
aws rds describe-engine-default-parameters --db-parameter-group-family mariadb10.6 ^
   --query "EngineDefaults.Parameters[?IsModifiable==`true`]"
```

## MySQL parameters that aren't available<a name="Appendix.MariaDB.Parameters.MySQLNotAvailable"></a>

The following MySQL parameters are not available in MariaDB\-specific DB parameter groups:
+ bind\_address
+ binlog\_error\_action
+ binlog\_gtid\_simple\_recovery
+ binlog\_max\_flush\_queue\_time
+ binlog\_order\_commits
+ binlog\_row\_image
+ binlog\_rows\_query\_log\_events
+ binlogging\_impossible\_mode
+ block\_encryption\_mode
+ core\_file
+ default\_tmp\_storage\_engine
+ div\_precision\_increment
+ end\_markers\_in\_json
+ enforce\_gtid\_consistency
+ eq\_range\_index\_dive\_limit
+ explicit\_defaults\_for\_timestamp
+ gtid\_executed
+ gtid\-mode
+ gtid\_next
+ gtid\_owned
+ gtid\_purged
+ log\_bin\_basename
+ log\_bin\_index
+ log\_bin\_use\_v1\_row\_events
+ log\_slow\_admin\_statements
+ log\_slow\_slave\_statements
+ log\_throttle\_queries\_not\_using\_indexes
+ master\-info\-repository
+ optimizer\_trace
+ optimizer\_trace\_features
+ optimizer\_trace\_limit
+ optimizer\_trace\_max\_mem\_size
+ optimizer\_trace\_offset
+ relay\_log\_info\_repository
+ rpl\_stop\_slave\_timeout
+ slave\_parallel\_workers
+ slave\_pending\_jobs\_size\_max
+ slave\_rows\_search\_algorithms
+ storage\_engine
+ table\_open\_cache\_instances
+ timed\_mutexes
+ transaction\_allow\_batching
+ validate\-password
+ validate\_password\_dictionary\_file
+ validate\_password\_length
+ validate\_password\_mixed\_case\_count
+ validate\_password\_number\_count
+ validate\_password\_policy
+ validate\_password\_special\_char\_count

For more information on MySQL parameters, see the [MySQL documentation](https://dev.mysql.com/doc/refman/8.0/en/)\.