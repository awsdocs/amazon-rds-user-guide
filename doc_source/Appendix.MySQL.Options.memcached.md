# MySQL memcached Support<a name="Appendix.MySQL.Options.memcached"></a>

Amazon RDS supports using the `memcached` interface to InnoDB tables that was introduced in MySQL 5\.6\. The `memcached` API enables applications to use InnoDB tables in a manner similar to NoSQL key\-value data stores\.

The `memcached` interface is a simple, key\-based cache\. Applications use `memcached` to insert, manipulate, and retrieve key\-value data pairs from the cache\. MySQL 5\.6 introduced a plugin that implements a daemon service that exposes data from InnoDB tables through the `memcached` protocol\. For more information about the MySQL `memcached` plugin, see [InnoDB Integration with memcached](https://dev.mysql.com/doc/refman/8.0/en/innodb-memcached.html)\.

**To enable memcached support for an Amazon RDS MySQL 5\.6 or later instance**

1. Determine the security group to use for controlling access to the `memcached` interface\. If the set of applications already using the SQL interface are the same set that will access the `memcached` interface, you can use the existing VPC or DB security group used by the SQL interface\. If a different set of applications will access the `memcached` interface, define a new VPC or DB security group\. For more information about managing security groups, see [Controlling Access with Security Groups](Overview.RDSSecurityGroups.md) 

1. Create a custom DB option group, selecting MySQL as the engine type and a 5\.6 or later version\. For more information about creating an option group, see [Creating an Option Group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.Create)\.

1. Add the `MEMCACHED` option to the option group\. Specify the port that the `memcached` interface will use, and the security group to use in controlling access to the interface\. For more information about adding options, see [Adding an Option to an Option Group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.AddOption)\.

1. Modify the option settings to configure the `memcached` parameters, if necessary\. For more information about how to modify option settings, see [Modifying an Option Setting](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.ModifyOption)\.

1. Apply the option group to an instance\. Amazon RDS enables `memcached` support for that instance when the option group is applied:
   + You enable `memcached` support for a new instance by specifying the custom option group when you launch the instance\. For more information about launching a MySQL instance, see [Creating an Amazon RDS DB Instance](USER_CreateDBInstance.md)\.
   + You enable `memcached` support for an existing instance by specifying the custom option group when you modify the instance\. For more information about modifying a DB instance, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md)\.

1. Specify which columns in your MySQL tables can be accessed through the `memcached` interface\. The `memcached` plug\-in creates a catalog table named `containers` in a dedicated database named `innodb_memcache`\. You insert a row into the `containers` table to map an InnoDB table for access through `memcached`\. You specify a column in the InnoDB table that is used to store the `memcached` key values, and one or more columns that are used to store the data values associated with the key\. You also specify a name that a `memcached` application uses to refer to that set of columns\. For details on inserting rows in the `containers` table, see [InnoDB memcached Plugin Internals](https://dev.mysql.com/doc/refman/8.0/en/innodb-memcached-internals.html)\. For an example of mapping an InnoDB table and accessing it through `memcached`, see [Writing Applications for the InnoDB memcached Plugin](https://dev.mysql.com/doc/refman/8.0/en/innodb-memcached-developing.html)\.

1. If the applications accessing the `memcached` interface are on different computers or EC2 instances than the applications using the SQL interface, add the connection information for those computers to the VPC or DB security group associated with the MySQL instance\. For more information about managing security groups, see [Controlling Access with Security Groups](Overview.RDSSecurityGroups.md)\.

You turn off the `memcached` support for an instance by modifying the instance and specifying the default option group for your MySQL version\. For more information about modifying a DB instance, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md)\.

## MySQL memcached Security Considerations<a name="w121aac29c71c15c11"></a>

The `memcached` protocol does not support user authentication\. For more information about MySQL `memcached` security considerations, see [memcached Deployment](http://dev.mysql.com/doc/refman/5.6/en/ha-memcached-using-deployment.html) and [Using memcached as a MySQL Caching Layer](http://dev.mysql.com/doc/refman/5.6/en/ha-memcached-mysql-frontend.html)\.

You can take the following actions to help increase the security of the `memcached` interface:
+ Specify a different port than the default of 11211 when adding the `MEMCACHED` option to the option group\.
+ Ensure that you associate the `memcached` interface with either a VPC or DB security group that limits access to known, trusted client addresses or EC2 instances\. For more information about managing security groups, see [Controlling Access with Security Groups](Overview.RDSSecurityGroups.md)\.

## MySQL memcached Connection Information<a name="w121aac29c71c15c13"></a>

To access the `memcached` interface, an application must specify both the DNS name of the Amazon RDS instance and the `memcached` port number\. For example, if an instance has a DNS name of `my-cache-instance.cg034hpkmmjt.region.rds.amazonaws.com` and the memcached interface is using port 11212, the connection information specified in PHP would be:

```
1. <?php
2. 
3. $cache = new Memcache;
4. $cache->connect('my-cache-instance.cg034hpkmmjt.region.rds.amazonaws.com',11212);
5. ?>
```

**To find the DNS name and memcached port of an Amazon RDS MySQL instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the top right corner of the AWS Management Console, select the region that contains the DB instance\.

1. In the navigation pane, choose **Databases**\.

1. Choose the MySQL DB instance name to display its details\.

1. In the **Connect** section, note the value of the **Endpoint** field\. The DNS name is the same as the endpoint\. Also, note that the port in the **Connect** section is not used to access the `memcached` interface\.

1. In the **Details** section, note the name listed in the **Option Group** field\.

1. In the navigation pane, choose **Option groups**\.

1. Choose the name of the option group used by the MySQL DB instance to show the option group details\. In the **Options** section, note the value of the **Port** setting for the **MEMCACHED** option\.

## MySQL memcached Option Settings<a name="w121aac29c71c15c15"></a>

Amazon RDS exposes the MySQL `memcached` parameters as option settings in the Amazon RDS `MEMCACHED` option\.

### MySQL memcached Parameters<a name="w121aac29c71c15c15b4"></a>
+  `DAEMON_MEMCACHED_R_BATCH_SIZE` – an integer that specifies how many `memcached` read operations \(get\) to perform before doing a COMMIT to start a new transaction\. The allowed values are 1 to 4294967295; the default is 1\. The option does not take effect until the instance is restarted\.
+  `DAEMON_MEMCACHED_W_BATCH_SIZE` – an integer that specifies how many `memcached` write operations, such as add, set, or incr, to perform before doing a COMMIT to start a new transaction\. The allowed values are 1 to 4294967295; the default is 1\. The option does not take effect until the instance is restarted\.
+  `INNODB_API_BK_COMMIT_INTERVAL` – an integer that specifies how often to auto\-commit idle connections that use the InnoDB `memcached` interface\. The allowed values are 1 to 1073741824; the default is 5\. The option takes effect immediately, without requiring that you restart the instance\.
+  `INNODB_API_DISABLE_ROWLOCK` – a Boolean that disables \(1 \(true\)\) or enables \(0 \(false\)\) the use of row locks when using the InnoDB `memcached` interface\. The default is 0 \(false\)\. The option does not take effect until the instance is restarted\.
+  `INNODB_API_ENABLE_MDL` – a Boolean that when set to 0 \(false\) locks the table used by the InnoDB `memcached` plugin, so that it cannot be dropped or altered by DDL through the SQL interface\. The default is 0 \(false\)\. The option does not take effect until the instance is restarted\.
+  `INNODB_API_TRX_LEVEL` – an integer that specifies the transaction isolation level for queries processed by the `memcached` interface\. The allowed values are 0 to 3\. The default is 0\. The option does not take effect until the instance is restarted\.

Amazon RDS configures these MySQL `memcached` parameters, and they cannot be modified: `DAEMON_MEMCACHED_LIB_NAME`, `DAEMON_MEMCACHED_LIB_PATH`, and `INNODB_API_ENABLE_BINLOG`\. The parameters that MySQL administrators set by using `daemon_memcached_options` are available as individual `MEMCACHED` option settings in Amazon RDS\.

### MySQL daemon\_memcached\_options Parameters<a name="w121aac29c71c15c15b6"></a>
+  `BINDING_PROTOCOL` – a string that specifies the binding protocol to use\. The allowed values are `auto`, `ascii`, or `binary`\. The default is `auto`, which means the server automatically negotiates the protocol with the client\. The option does not take effect until the instance is restarted\.
+  `BACKLOG_QUEUE_LIMIT` – an integer that specifies how many network connections can be waiting to be processed by `memcached`\. Increasing this limit may reduce errors received by a client that is not able to connect to the `memcached` instance, but does not improve the performance of the server\. The allowed values are 1 to 2048; the default is 1024\. The option does not take effect until the instance is restarted\.
+  `CAS_DISABLED` – a Boolean that enables \(1 \(true\)\) or disables \(0 \(false\)\) the use of compare and swap \(CAS\), which reduces the per\-item size by 8 bytes\. The default is 0 \(false\)\. The option does not take effect until the instance is restarted\.
+  `CHUNK_SIZE` – an integer that specifies the minimum chunk size, in bytes, to allocate for the smallest item's key, value, and flags\. The allowed values are 1 to 48\. The default is 48 and you can significantly improve memory efficiency with a lower value\. The option does not take effect until the instance is restarted\.
+  `CHUNK_SIZE_GROWTH_FACTOR` – a float that controls the size of new chunks\. The size of a new chunk is the size of the previous chunk times `CHUNK_SIZE_GROWTH_FACTOR`\. The allowed values are 1 to 2; the default is 1\.25\. The option does not take effect until the instance is restarted\.
+  `ERROR_ON_MEMORY_EXHAUSTED` – a Boolean that when set to 1 \(true\) specifies that `memcached` will return an error rather than evicting items when there is no more memory to store items\. If set to 0 \(false\), `memcached` will evict items if there is no more memory\. The default is 0 \(false\)\. The option does not take effect until the instance is restarted\.
+  `MAX_SIMULTANEOUS_CONNECTIONS` – an integer that specifies the maximum number of concurrent connections\. Setting this value to anything under 10 prevents MySQL from starting\. The allowed values are 10 to 1024; the default is 1024\. The option does not take effect until the instance is restarted\.
+  `VERBOSITY` – a string that specifies the level of information logged in the MySQL error log by the `memcached` service\. The default is v\. The option does not take effect until the instance is restarted\. The allowed values are:
  +  `v` – Logs errors and warnings while running the main event loop\.
  +  `vv` – In addition to the information logged by v, also logs each client command and the response\.
  +  `vvv` – In addition to the information logged by vv, also logs internal state transitions\.

Amazon RDS configures these MySQL `DAEMON_MEMCACHED_OPTIONS` parameters, they cannot be modified: `DAEMON_PROCESS`, `LARGE_MEMORY_PAGES`, `MAXIMUM_CORE_FILE_LIMIT`, `MAX_ITEM_SIZE`, `LOCK_DOWN_PAGE_MEMORY`, `MASK`, `IDFILE`, `REQUESTS_PER_EVENT`, `SOCKET`, and `USER`\.