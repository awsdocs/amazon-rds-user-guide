# Parameters for MariaDB<a name="Appendix.MariaDB.Parameters"></a>

By default, a MariaDB DB instance uses a DB parameter group that is specific to a MariaDB database\. This parameter group contains some but not all of the parameters contained in the Amazon RDS DB parameter groups for the MySQL database engine\. It also contains a number of new, MariaDB\-specific parameters\. The following MySQL parameters are not available in MariaDB\-specific DB parameter groups:
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

For more information on MySQL parameters, go to the [MySQL documentation](https://dev.mysql.com/doc/refman/8.0/en/)\.

The MariaDB\-specific DB parameter groups also contain the following parameters that are applicable to MariaDB only\. Acceptable ranges for the modifiable parameters are the same as specified in the MariaDB documentation except where noted\. Amazon RDS MariaDB parameters are set to the default values of the storage engine you have selected\.
+ aria\_block\_size
+ aria\_checkpoint\_interval
+ aria\_checkpoint\_log\_activity
+ aria\_force\_start\_after\_recovery\_failures
+ aria\_group\_commit
+ aria\_group\_commit\_interval
+ aria\_log\_dir\_path
+ aria\_log\_file\_size
+ aria\_log\_purge\_type
+ aria\_max\_sort\_file\_size
+ aria\_page\_checksum
+ aria\_pagecache\_age\_threshold
+ aria\_pagecache\_division\_limit
+ aria\_recover

  Amazon RDS MariaDB supports the values of NORMAL, OFF, and QUICK, but not FORCE or BACKUP\.
+ aria\_repair\_threads
+ aria\_sort\_buffer\_size
+ aria\_stats\_method
+ aria\_sync\_log\_dir
+ binlog\_annotate\_row\_events
+ binlog\_commit\_wait\_count
+ binlog\_commit\_wait\_usec
+ binlog\_row\_image \(MariaDB version 10\.1 and later\)
+ deadlock\_search\_depth\_long
+ deadlock\_search\_depth\_short
+ deadlock\_timeout\_long
+ deadlock\_timeout\_short
+ explicit\_defaults\_for\_timestamp \(MariaDB version 10\.1 and later\)
+ extra\_max\_connections
+ extra\_port
+ feedback
+ feedback\_send\_retry\_wait
+ feedback\_send\_timeout
+ feedback\_url
+ feedback\_user\_info
+ gtid\_domain\_id
+ gtid\_strict\_mode
+ histogram\_size
+ histogram\_type
+ innodb\_adaptive\_hash\_index\_partitions
+ innodb\_background\_scrub\_data\_check\_interval \(MariaDB version 10\.1 and later\)
+ innodb\_background\_scrub\_data\_compressed \(MariaDB version 10\.1 and later\)
+ innodb\_background\_scrub\_data\_interval \(MariaDB version 10\.1 and later\)
+ innodb\_background\_scrub\_data\_uncompressed \(MariaDB version 10\.1 and later\)
+ innodb\_buf\_dump\_status\_frequency \(MariaDB version 10\.1 and later\)
+ innodb\_buffer\_pool\_populate
+ innodb\_cleaner\_lsn\_age\_factor
+ innodb\_compression\_algorithm \(MariaDB version 10\.1 and later\)
+ innodb\_corrupt\_table\_action
+ innodb\_defragment \(MariaDB version 10\.1 and later\)
+ innodb\_defragment\_fill\_factor \(MariaDB version 10\.1 and later\)
+ innodb\_defragment\_fill\_factor\_n\_recs \(MariaDB version 10\.1 and later\)
+ innodb\_defragment\_frequency \(MariaDB version 10\.1 and later\)
+ innodb\_defragment\_n\_pages \(MariaDB version 10\.1 and later\)
+ innodb\_defragment\_stats\_accuracy \(MariaDB version 10\.1 and later\)
+ innodb\_empty\_free\_List\_algorithm
+ innodb\_fake\_changes
+ innodb\_fatal\_semaphore\_wait\_threshold \(MariaDB version 10\.1 and later\)
+ innodb\_foreground\_preflush
+ innodb\_idle\_flush\_pct \(MariaDB version 10\.1 and later\)
+ innodb\_immediate\_scrub\_data\_uncompressed \(MariaDB version 10\.1 and later\)
+ innodb\_instrument\_semaphores \(MariaDB version 10\.1 and later\)
+ innodb\_locking\_fake\_changes
+ innodb\_log\_arch\_dir
+ innodb\_log\_arch\_expire\_sec
+ innodb\_log\_archive
+ innodb\_log\_block\_size
+ innodb\_log\_checksum\_algorithm
+ innodb\_max\_bitmap\_file\_size
+ innodb\_max\_changed\_pages
+ innodb\_prefix\_index\_cluster\_optimization \(MariaDB version 10\.1 and later\)
+ innodb\_sched\_priority\_cleaner
+ innodb\_scrub\_log \(MariaDB version 10\.1 and later\)
+ innodb\_scrub\_log\_speed \(MariaDB version 10\.1 and later\)
+ innodb\_show\_locks\_held
+ innodb\_show\_verbose\_locks
+ innodb\_simulate\_comp\_failures
+ innodb\_stats\_modified\_counter
+ innodb\_stats\_traditional
+ innodb\_use\_atomic\_writes
+ innodb\_use\_fallocate
+ innodb\_use\_global\_flush\_log\_at\_trx\_commit
+ innodb\_use\_stacktrace
+ innodb\_use\_trim \(MariaDB version 10\.1 and later\)
+ join\_buffer\_space\_limit
+ join\_cache\_level
+ key\_cache\_file\_hash\_size
+ key\_cache\_segments
+ max\_digest\_length \(MariaDB version 10\.1 and later\)
+ max\_statement\_time \(MariaDB version 10\.1 and later\)
+ mysql56\_temporal\_format \(MariaDB version 10\.1 and later\)
+ progress\_report\_time
+ query\_cache\_strip\_comments
+ replicate\_annotate\_row\_events
+ replicate\_do\_db
+ replicate\_do\_table
+ replicate\_events\_marked\_for\_skip
+ replicate\_ignore\_db
+ replicate\_ignore\_table
+ replicate\_wild\_ignore\_table
+ slave\_domain\_parallel\_threads
+ slave\_parallel\_max\_queued
+ slave\_parallel\_mode \(MariaDB version 10\.1 and later\)
+ slave\_parallel\_threads
+ slave\_run\_triggers\_for\_rbr \(MariaDB version 10\.1 and later\)
+ sql\_error\_log\_filename
+ sql\_error\_log\_rate
+ sql\_error\_log\_rotate
+ sql\_error\_log\_rotations
+ sql\_error\_log\_size\_limit
+ thread\_handling
+ thread\_pool\_idle\_timeout
+ thread\_pool\_max\_threads
+ thread\_pool\_min\_threads
+ thread\_pool\_oversubscribe
+ thread\_pool\_size
+ thread\_pool\_stall\_limit
+ transaction\_write\_set\_extraction
+ use\_stat\_tables
+ userstat

 For more information on MariaDB parameters, go to the [MariaDB documentation](http://mariadb.com/kb/en/mariadb/documentation/)\.