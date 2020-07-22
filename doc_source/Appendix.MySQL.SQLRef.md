# MySQL on Amazon RDS SQL Reference<a name="Appendix.MySQL.SQLRef"></a>

This appendix describes system stored procedures that are available for Amazon RDS instances running the MySQL DB engine\.

## Overview<a name="Appendix.MySQL.SQLRef.Overview"></a>

The following system stored procedures are supported for Amazon RDS DB instances running MySQL\.

**Replication** 
+ [mysql\.rds\_set\_master\_auto\_position](mysql_rds_set_master_auto_position.md)
+ [mysql\.rds\_set\_external\_master](mysql_rds_set_external_master.md)
+ [mysql\.rds\_set\_external\_master\_with\_delay](mysql_rds_set_external_master_with_delay.md)
+ [mysql\.rds\_set\_external\_master\_with\_auto\_position](mysql_rds_set_external_master_with_auto_position.md)
+ [mysql\.rds\_reset\_external\_master](mysql_rds_reset_external_master.md)
+ [mysql\.rds\_import\_binlog\_ssl\_material](mysql_rds_import_binlog_ssl_material.md)
+ [mysql\.rds\_remove\_binlog\_ssl\_material](mysql_rds_remove_binlog_ssl_material.md)
+ [mysql\.rds\_set\_source\_delay](mysql_rds_set_source_delay.md)
+ [mysql\.rds\_start\_replication](mysql_rds_start_replication.md)
+ [mysql\.rds\_start\_replication\_until](mysql_rds_start_replication_until.md)
+ [mysql\.rds\_start\_replication\_until\_gtid](mysql_rds_start_replication_until_gtid.md)
+ [mysql\.rds\_stop\_replication](mysql_rds_stop_replication.md)
+ [mysql\.rds\_skip\_transaction\_with\_gtid](mysql_rds_skip_transaction_with_gtid.md)
+ [mysql\.rds\_skip\_repl\_error](mysql_rds_skip_repl_error.md)
+ [mysql\.rds\_next\_master\_log](mysql_rds_next_master_log.md)

**InnoDB cache warming** 
+ [mysql\.rds\_innodb\_buffer\_pool\_dump\_now](mysql_rds_innodb_buffer_pool_dump_now.md)
+ [mysql\.rds\_innodb\_buffer\_pool\_load\_now](mysql_rds_innodb_buffer_pool_load_now.md)
+ [mysql\.rds\_innodb\_buffer\_pool\_load\_abort](mysql_rds_innodb_buffer_pool_load_abort.md)

**Managing additional configuration \(for example, binlog file retention\)** 
+ [mysql\.rds\_set\_configuration](mysql_rds_set_configuration.md)
+ [mysql\.rds\_show\_configuration](mysql_rds_show_configuration.md)

**Ending a session or query** 
+ [mysql\.rds\_kill](mysql_rds_kill.md)
+ [mysql\.rds\_kill\_query](mysql_rds_kill_query.md)

**Logging** 
+ [mysql\.rds\_rotate\_general\_log](mysql_rds_rotate_general_log.md)
+ [mysql\.rds\_rotate\_slow\_log](mysql_rds_rotate_slow_log.md)

**Managing the global status history** 
+ [mysql\.rds\_enable\_gsh\_collector](mysql_rds_enable_gsh_collector.md)
+ [mysql\.rds\_set\_gsh\_collector](mysql_rds_set_gsh_collector.md)
+ [mysql\.rds\_disable\_gsh\_collector](mysql_rds_disable_gsh_collector.md)
+ [mysql\.rds\_collect\_global\_status\_history](mysql_rds_collect_global_status_history.md)
+ [mysql\.rds\_enable\_gsh\_rotation](mysql_rds_enable_gsh_rotation.md)
+ [mysql\.rds\_set\_gsh\_rotation](mysql_rds_set_gsh_rotation.md)
+ [mysql\.rds\_disable\_gsh\_rotation](mysql_rds_disable_gsh_rotation.md)
+ [mysql\.rds\_rotate\_global\_status\_history](mysql_rds_rotate_global_status_history.md)

## SQL Reference Conventions<a name="RDS_SQL_reference_conventions"></a>

Following, you can find explanations for the conventions that are used to describe the syntax of the system stored procedures and tables described in the SQL reference section\. 


****  
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Appendix.MySQL.SQLRef.html)