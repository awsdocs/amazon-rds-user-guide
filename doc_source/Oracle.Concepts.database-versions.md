# Oracle versions on Amazon RDS<a name="Oracle.Concepts.database-versions"></a>

Amazon RDS for Oracle supports the following major database releases\.

**Topics**
+ [Oracle Database 19c with Amazon RDS](#Oracle.Concepts.FeatureSupport.19c)
+ [Oracle Database 12c with Amazon RDS](#Oracle.Concepts.FeatureSupport.12c)

## Oracle Database 19c with Amazon RDS<a name="Oracle.Concepts.FeatureSupport.19c"></a>

Amazon RDS supports Oracle Database 19c, which includes Oracle Enterprise Edition and Oracle Standard Edition Two\.

Oracle Database 19c \(19\.0\.0\.0\) includes many new features and updates from the previous version\. In this section, you can find the features and changes important to using Oracle Database 19c \(19\.0\.0\.0\) on Amazon RDS\. For a complete list of the changes, see the [Oracle database 19c](https://docs.oracle.com/en/database/oracle/oracle-database/19/index.html) documentation\. For a complete list of features supported by each Oracle Database 19c edition, see [ Permitted features, options, and management packs by Oracle database offering](https://docs.oracle.com/en/database/oracle/oracle-database/19/dblic/Licensing-Information.html#GUID-0F9EB85D-4610-4EDF-89C2-4916A0E7AC87) in the Oracle documentation\. 

### Amazon RDS parameter changes for Oracle Database 19c \(19\.0\.0\.0\)<a name="Oracle.Concepts.FeatureSupport.19c.Parameters"></a>

Oracle Database 19c \(19\.0\.0\.0\) includes several new parameters and parameters with new ranges and new default values\.

The following table shows the new Amazon RDS parameters for Oracle Database 19c \(19\.0\.0\.0\)\.


****  

|  Name  |  Values  |  Modifiable  |  Description  | 
| --- | --- | --- | --- | 
|   [ lob\_signature\_enable](https://docs.oracle.com/en/database/oracle/oracle-database/19/refrn/lob_signature_enable.html#GUID-62997AB5-1084-4C9A-8258-8CB695C7A1D6)   |  TRUE, FALSE \(default\)  |  Y  |  Enables or disables the LOB locator signature feature\.  | 
|   [ max\_datapump\_parallel\_per\_job](https://docs.oracle.com/en/database/oracle/oracle-database/19/refrn/MAX_DATAPUMP_PARALLEL_PER_JOB.html#GUID-33B1F962-B8C3-4DCE-BE68-66FC5D34ECA3)   |  1 to 1024, or AUTO  |  Y  |  Specifies the maximum number of parallel processes allowed for each Oracle Data Pump job\.  | 

The `compatible` parameter has a new maximum value for Oracle Database 19c \(19\.0\.0\.0\) on Amazon RDS\. The following table shows the new default value\. 


****  

|  Parameter name  |  Oracle Database 19c \(19\.0\.0\.0\) maximum value  | 
| --- | --- | 
|  [ compatible](https://docs.oracle.com/en/database/oracle/oracle-database/19/refrn/COMPATIBLE.html#GUID-6C57EE11-BD06-4BB8-A0F7-D6CDDD086FA9)  |  19\.0\.0  | 

The following parameters were removed in Oracle Database 19c \(19\.0\.0\.0\):
+ exafusion\_enabled
+ max\_connections
+ o7\_dictionary\_access

## Oracle Database 12c with Amazon RDS<a name="Oracle.Concepts.FeatureSupport.12c"></a>

Amazon RDS supports Oracle Database 12c, which includes Oracle Enterprise Edition and Oracle Standard Edition 2\. Oracle Database 12c includes the following major versions:
+ [Oracle Database 12c Release 2 \(12\.2\.0\.1\) with Amazon RDS](#Oracle.Concepts.FeatureSupport.12cV2Overview)
+ [Oracle Database 12c Release 1 \(12\.1\.0\.2\) with Amazon RDS](#Oracle.Concepts.FeatureSupport.12cV1Overview)

### Oracle Database 12c Release 2 \(12\.2\.0\.1\) with Amazon RDS<a name="Oracle.Concepts.FeatureSupport.12cV2Overview"></a>

Oracle Database 12c Release 2 \(12\.2\.0\.1\) includes many new features and updates from the previous version\. In this section, you can find the features and changes important to using Oracle Database 12c Release 2 \(12\.2\.0\.1\) on Amazon RDS\. For a complete list of the changes, see the [Oracle Database 12c Release 2 \(12\.2\) documentation](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/index.html)\. For a complete list of features supported by each Oracle Database 12c edition, see [ Permitted features, options, and management packs by Oracle database offering](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dblic/Licensing-Information.html#GUID-0F9EB85D-4610-4EDF-89C2-4916A0E7AC87) in the Oracle documentation\. 

#### Amazon RDS parameter changes for Oracle Database 12c Release 2 \(12\.2\.0\.1\)<a name="Oracle.Concepts.FeatureSupport.12cV2.Parameters"></a>

Oracle Database 12c Release 2 \(12\.2\.0\.1\) includes 20 new parameters in addition to several parameters with new ranges and new default values\.

The following table shows the new Amazon RDS parameters for Oracle Database 12c Release 2 \(12\.2\.0\.1\)\.


****  

|  Name  |  Values  |  Modifiable  |  Description  | 
| --- | --- | --- | --- | 
|   [ allow\_global\_dblinks](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/refrn/ALLOW_GLOBAL_DBLINKS.html#GUID-854693F5-87F6-46FB-A656-F6DAC3524BA2)   |  TRUE, FALSE \(default\)  |  Y  |  Specifies whether LDAP lookup for database links is allowed for the database\.  | 
|   [ approx\_for\_aggregation](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/refrn/APPROX_FOR_AGGREGATION.html#GUID-46853DF9-7688-46C7-A5BA-308B9B2DAF67)   |  TRUE, FALSE \(default\)  |  Y  |  Replaces exact query processing for aggregation queries with approximate query processing\.  | 
|   [ approx\_for\_count\_distinct](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/refrn/APPROX_FOR_COUNT_DISTINCT.html#GUID-D2A8A53F-113A-4E6F-AC2E-37139460EF8D)   |  TRUE, FALSE \(default\)  |  Y  |  Automatically replaces `COUNT (DISTINCT expr)` queries with `APPROX_COUNT_DISTINCT` queries\.  | 
|   [ approx\_for\_percentile](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/refrn/APPROX_FOR_PERCENTILE.html#GUID-3872A78C-9B3F-457C-AD28-4E86F71AE74D)   |  NONE \(default\), PERCENTILE\_CONT, PERCENTILE\_CONT DETERMINISTIC, PERCENTILE\_DISC, PERCENTILE\_DISC DETERMINISTIC, ALL, ALL DETERMINISTIC  |  Y  |  Converts exact percentile functions to their approximate percentile function counterparts\.  | 
|   [ cursor\_invalidation](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/refrn/CURSOR_INVALIDATION.html#GUID-6BAB61E7-FABA-41D3-B73A-EB828A6767E5)   |  DEFERRED, IMMEDIATE \(default\)  |  Y  |  Controls whether deferred cursor invalidation or immediate cursor invalidation is used for DDL statements by default\.  | 
|   [ data\_guard\_sync\_latency](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/refrn/DATA_GUARD_SYNC_LATENCY.html#GUID-A0965F0B-608C-4B68-85D1-9F14EFC191CC)   |  0 \(default\) to the number of seconds specified by the NET\_TIMEOUT attribute for the LOG\_ARCHIVE\_DEST\_n parameter  |  Y  |  Controls how many seconds the Log Writer \(LGWR\) process waits beyond the response of the first in a series of Oracle Data Guard SYNC redo transport mode connections\.  | 
|   [ data\_transfer\_cache\_size](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/refrn/DATA_TRANSFER_CACHE_SIZE.html#GUID-322093B7-1673-490D-8A1A-5F461D7897DD)   |  0 – 512M, rounded up to the next granule size  |  Y  |  Sets the size of the data transfer cache \(in bytes\) used to receive data blocks \(typically from a primary database in an Oracle Data Guard environment\) for consumption by an instance when an RMAN RECOVER \.\.\. NONLOGGED BLOCK command is running\.  | 
|   [ inmemory\_adg\_enabled](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/refrn/INMEMORY_ADG_ENABLED.html#GUID-74F7324A-6067-4B7A-9D72-3046DDE70D0A)   |  TRUE \(default\), FALSE  |  Y  |  Indicates whether in\-memory for Active Data Guard is enabled in addition to the in\-memory cache size\.  | 
|   [ inmemory\_expressions\_usage](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/refrn/INMEMORY_EXPRESSIONS_USAGE.html#GUID-9B926459-CA8A-4843-B9A1-4DE6F630EB45)   |  STATIC\_ONLY, DYNAMIC\_ONLY,ENABLE \(default\), DISABLE  |  Y  |  Controls which In\-Memory Expressions \(IM expressions\) are populated into the In\-Memory Column Store \(IM column store\) and are available for queries\.  | 
|   [ inmemory\_virtual\_columns](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/refrn/INMEMORY_VIRTUAL_COLUMNS.html#GUID-1B066B16-4F22-4D9D-9D82-AF7B786DB97E)   |  ENABLE, MANUAL \(default\), DISABLE  |  Y  |  Controls which In\-Memory Expressions \(IM expressions\) are populated into the In\-Memory Column Store \(IM column store\) and are available for queries\.  | 
|   [ instance\_abort\_delay\_time](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/refrn/INSTANCE_ABORT_DELAY_TIME.html#GUID-F15A6EC9-1F4F-4DB7-9981-FFC1F181559F)   |  0 \(default\) and higher  |  Y  |  Specifies how much time to delay an internally initiated instance shutdown \(in seconds\), such as when a fatal process dies or an unrecoverable instance error occurs\.  | 
|   [ instance\_mode](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/refrn/INSTANCE_MODE.html#GUID-77461EFC-0925-4511-AD4C-426CF842B4FD)   |  READ\-WRITE \(default\), READ\-ONLY, READ\-MOSTLY  |  N  |  Indicates whether the instance is read\-write, read\-only, or read\-mostly\.  | 
|   [ long\_module\_action](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/refrn/LONG_MODULE_ACTION.html#GUID-DA943655-E516-44A3-8D11-A86E5EF5F585)   |  TRUE \(default\), FALSE  |  Y  |  Enables the use of longer lengths for modules and actions\.  | 
|   [ max\_idle\_time](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/refrn/MAX_IDLE_TIME.html#GUID-9E26A81D-D99E-4EA8-88DE-77AF68482A20)   |  0 \(default\) to the maximum integer\. The value of 0 indicates that there is no limit\.  |  Y  |  Specifies the maximum number of minutes that a session can be idle\. After that point, the session is automatically terminated\.   | 
|   [ optimizer\_adaptive\_plans](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/refrn/OPTIMIZER_ADAPTIVE_PLANS.html#GUID-58C3E867-36BA-449A-B452-4E90FE6DCF05)   |  TRUE \(default\), FALSE  |  Y  |  Controls adaptive plans\. Adaptive plans are execution plans built with alternative choices that are decided at run time based on statistics collected as the query executes\.  | 
|   [ optimizer\_adaptive\_statistics](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/refrn/OPTIMIZER_ADAPTIVE_STATISTICS.html#GUID-D52B4342-3887-4054-A65C-5AEA83F69E35)   |  TRUE, FALSE \(default\)  |  Y  |  Controls adaptive statistics\. Some query shapes are too complex to rely on base table statistics alone, so the optimizer augments these statistics with adaptive statistics\.  | 
|   [ outbound\_dblink\_protocols](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/refrn/OUTBOUND_DBLINK_PROTOCOLS.html#GUID-4E760A19-5791-4E2D-8792-016B7B6D10D6)   |  ALL \(default\), NONE, TCP, TCPS, IPC  |  N  |  Specifies the network protocols allowed for communicating for outbound database links in the database\.  | 
|   [ resource\_manage\_goldengate](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/refrn/RESOURCE_MANAGE_GOLDENGATE.html#GUID-389AB551-F4EB-4294-BD52-5E5E41AFDA80)   |  TRUE, FALSE \(default\)  |  Y  |  Determines whether Oracle GoldenGate apply processes in the database are resource managed\.  | 
|   [ standby\_db\_preserve\_states](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/refrn/STANDBY_DB_PRESERVE_STATES.html#GUID-8D332556-30B7-4C45-8557-50988DC2219E)   |  NONE \(default\), SESSION, ALL  |  N  |  Controls whether user sessions and other internal states of the instance are retained when a readable physical standby database is converted to a primary database\.   | 
|   [ uniform\_log\_timestamp\_format](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/refrn/UNIFORM_LOG_TIMESTAMP_FORMAT.html#GUID-041BC204-EA0E-4260-9726-D25C2C86A2F5)   |  TRUE \(default\), FALSE  |  Y  |  Specifies that a uniform timestamp format be used in Oracle Database trace \(\.trc\) files and log files \(such as the alert log\)\.  | 

The `compatible` parameter has a new default value for Oracle Database 12c Release 2 \(12\.2\.0\.1\) on Amazon RDS\. The following table shows the new default value\. 


****  

|  Parameter name  |  Oracle Database 12c Release 2 \(12\.2\.0\.1\) default value  |  Oracle Database 12c Release 1 \(12\.1\.0\.2\) default value  | 
| --- | --- | --- | 
|  [ compatible](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/refrn/COMPATIBLE.html#GUID-6C57EE11-BD06-4BB8-A0F7-D6CDDD086FA9)  |  12\.2\.0  |  12\.0\.0  | 

The `optimizer_features_enable` parameter has a new value range for Oracle Database 12c Release 2 \(12\.2\.0\.1\) on Amazon RDS\. For the old and new value ranges, see the following table\.


****  

|  Parameter name  |  Oracle Database 12c Release 2 \(12\.2\.0\.1\) range  |  Oracle Database 12c Release 1 \(12\.1\.0\.2\) range  | 
| --- | --- | --- | 
|   [optimizer\_features\_enable](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/refrn/OPTIMIZER_FEATURES_ENABLE.html#GUID-E193EC9E-B642-4C01-99EC-24E04AEA1A2C)   |   8\.0\.0 to 12\.2\.0\.1   |   8\.0\.0 to 12\.1\.0\.2   | 

The following parameters were removed in Oracle Database 12c Release 2 \(12\.2\.0\.1\):
+ global\_context\_pool\_size
+ max\_enabled\_roles
+ optimizer\_adaptive\_features
+ parallel\_automatic\_tuning
+ parallel\_degree\_level
+ use\_indirect\_data\_buffers

The following parameter is not supported in Oracle Database 12c Release 2 \(12\.2\.0\.1\) and later:
+ sec\_case\_sensitive\_logon

#### Amazon RDS security changes for Oracle Database 12c Release 2 \(12\.2\.0\.1\)<a name="Oracle.Concepts.FeatureSupport.12cV2.Security"></a>

In Oracle Database 12c Release 2 \(12\.2\.0\.1\), direct grant of the privilege `ADMINISTER DATABASE TRIGGER` is required for the owners of database\-level triggers\. During a major version upgrade to Oracle Database 12c Release 2 \(12\.2\.0\.1\), Amazon RDS grants this privilege to any user that owns a trigger so that the trigger owner has the required privileges\. For more information, see the My Oracle Support document [2275535\.1](https://support.oracle.com/epmos/faces/DocContentDisplay?id=2275535.1)\.

### Oracle Database 12c Release 1 \(12\.1\.0\.2\) with Amazon RDS<a name="Oracle.Concepts.FeatureSupport.12cV1Overview"></a>

Oracle Database 12c Release 1 \(12\.1\.0\.2\) brings over 500 new features and updates from the previous version\. In this section, you can find the features and changes important to using Oracle Database 12c Release 1 \(12\.1\.0\.2\) on Amazon RDS\. For a complete list of the changes, see the [Oracle Database 12c Release 1 \(12\.1\) documentation](http://docs.oracle.com/database/121/index.htm)\. For a complete list of features supported by each Oracle Database 12c edition, see [Permitted features, options, and management packs by Oracle database edition](https://docs.oracle.com/database/121/DBLIC/editions.htm#DBLIC116) in the Oracle documentation\. 

Oracle Database 12c Release 1 \(12\.1\.0\.2\) includes 16 new parameters that impact your Amazon RDS DB instance, and also 18 new system privileges, several no longer supported packages, and several new option group settings\. For more information on these changes, see the following sections\. 

#### Amazon RDS parameter changes for Oracle Database 12c Release 1 \(12\.1\.0\.2\)<a name="Oracle.Concepts.FeatureSupport.12c.Parameters"></a>

Oracle Database 12c Release 1 \(12\.1\.0\.2\) includes 16 new parameters in addition to several parameters with new ranges and new default values\.

The following table shows the new Amazon RDS parameters for Oracle Database 12c Release 1 \(12\.1\.0\.2\)\.


****  

|  Name  |  Values  |  Modifiable  |  Description  | 
| --- | --- | --- | --- | 
|   [connection\_brokers](http://docs.oracle.com/database/121/REFRN/GUID-C6C3A860-D74C-4D3D-9185-E59794360ACA.htm#REFRN10333)   |  CONNECTION\_BROKERS = broker\_description\[,\.\.\.\]  |  N  |  Specifies connection broker types, the number of connection brokers of each type, and the maximum number of connections per broker\.   | 
|   [db\_index\_compression\_inheritance](https://docs.oracle.com/database/121/REFRN/GUID-5238D586-B068-46F7-9D8F-E4C174E5D5B2.htm#REFRN10336)   |  TABLESPACE, TABL, ALL, NONE  |  Y  |  Displays the options that are set for table or tablespace level compression inheritance\.   | 
|   [db\_big\_table\_cache\_percent\_target](https://docs.oracle.com/database/121/REFRN/GUID-122865EE-4589-434D-8DD5-4E201C6CC739.htm#REFRN10340)   |  0\-90  |  Y  |  Specifies the cache section target size for automatic big table caching, as a percentage of the buffer cache\.   | 
|   [heat\_map](http://docs.oracle.com/database/121/REFRN/GUID-F0F60587-56A5-4727-B9D4-FA44ADD7774E.htm#REFRN10342)   |   ON, OFF   |  Y  |  Enables the database to track read and write access of all segments and modification of database blocks that are due to data manipulation language \(DML\) and data definition language \(DDL\) statements\.   | 
|   [inmemory\_clause\_default](http://docs.oracle.com/database/121/REFRN/GUID-5772F775-2A3E-4BC8-AA03-B8FF383BEE52.htm#REFRN10350)   |  INMEMORY, NO INMEMORY  |  Y  |  INMEMORY\_CLAUSE\_DEFAULT enables you to specify a default In\-Memory Column Store \(IM column store\) clause for new tables and materialized views\.  | 
|   [inmemory\_clause\_default\_memcompress](http://docs.oracle.com/database/121/REFRN/GUID-5772F775-2A3E-4BC8-AA03-B8FF383BEE52.htm#REFRN10350)   |  NO MEMCOMPRESS, MEMCOMPRESS FOR DML, MEMCOMPRESS FOR QUERY, MEMCOMPRESS FOR QUERY LOW, MEMCOMPRESS FOR QUERY HIGH, MEMCOMPRESS FOR CAPACITY, MEMCOMPRESS FOR CAPACITY LOW, MEMCOMPRESS FOR CAPACITY HIGH  |  Y  |  See INMEMORY\_CLAUSE\_DEFAULT\.  | 
|   [inmemory\_clause\_default\_priority](http://docs.oracle.com/database/121/REFRN/GUID-5772F775-2A3E-4BC8-AA03-B8FF383BEE52.htm#REFRN10350)   |  PRIORITY LOW, PRIORITY MEDIUM, PRIORITY HIGH, PRIORITY CRITICAL, PRIORITY NONE  |  Y  |  See INMEMORY\_CLAUSE\_DEFAULT\.  | 
|   [inmemory\_force](http://docs.oracle.com/database/121/REFRN/GUID-1CAEDEBC-AE38-428D-B07E-6718A7225548.htm#REFRN10353)   |  DEFAULT, OFF  |  Y  |  INMEMORY\_FORCE allows you to specify whether tables and materialized view that are specified as INMEMORY are populated into the In\-Memory Column Store \(IM column store\) or not\.  | 
|   [inmemory\_max\_populate\_servers](http://docs.oracle.com/database/121/REFRN/GUID-3428C0FF-5AA9-491C-908E-F0BD88095A5A.htm#REFRN10355)   |  Null  |  N  |  INMEMORY\_MAX\_POPULATE\_SERVERS specifies the maximum number of background populate servers to use for In\-Memory Column Store \(IM column store\) population, so that these servers don't overload the rest of the system\.  | 
|   [inmemory\_query](http://docs.oracle.com/database/121/REFRN/GUID-5B9BDBE2-6835-4598-9717-64D2C9D622AB.htm#REFRN10351)   |   ENABLE \(default\), DISABLE  |  Y  |  INMEMORY\_QUERY is used to enable or disable in\-memory queries for the entire database at the session or system level\.  | 
|   [inmemory\_size](http://docs.oracle.com/database/121/REFRN/GUID-B5BEB6BF-5308-485F-920D-CBB584DDDE8F.htm#REFRN10348)   |  0, 104857600\-274877906944   |  Y  |  INMEMORY\_SIZE sets the size of the In\-Memory Column Store \(IM column store\) on a database instance\.  | 
|   [inmemory\_trickle\_repopulate\_servers\_percent](http://docs.oracle.com/database/121/REFRN/GUID-499850D5-6418-4AD3-BEB5-865C4A165C39.htm#REFRN10358)   |  0 to 50  |  Y  |  INMEMORY\_TRICKLE\_REPOPULATE\_SERVERS\_PERCENT limits the maximum number of background populate servers used for In\-Memory Column Store \(IM column store\) repopulation\. This limit is applied because trickle repopulation is designed to use only a small percentage of the populate servers\.  | 
|   [max\_string\_size](http://docs.oracle.com/database/121/REFRN/GUID-D424D23B-0933-425F-BC69-9C0E6724693C.htm#REFRN10321)   |   STANDARD \(default\), EXTENDED   |  N  |  Controls the maximum size of VARCHAR2, NVARCHAR2, and RAW\. For more information, see [Enabling extended data types](Appendix.Oracle.CommonDBATasks.Misc.md#Oracle.Concepts.ExtendedDataTypes)\.  | 
|   [optimizer\_adaptive\_features](http://docs.oracle.com/database/121/REFRN/GUID-F5E53EFA-B395-4336-B046-1EE7AF12353B.htm#REFRN10344)   |   TRUE \(default\), FALSE   |  Y  |  Enables or disables all of the adaptive optimizer features\.  | 
|   [optimizer\_adaptive\_reporting\_only](http://docs.oracle.com/database/121/REFRN/GUID-8DD128F9-4891-4061-9B2D-9D45315D44FB.htm#REFRN10327)   |   TRUE, FALSE \(default\)   |  Y  |  Controls reporting\-only mode for adaptive optimizations\.   | 
|   [pdb\_file\_name\_convert](http://docs.oracle.com/database/121/REFRN/GUID-074F8896-D565-4139-BCDB-C81C9D741941.htm#REFRN10322)   |  There is no default value\.  |  N  |  Maps names of existing files to new file names\.  | 
|   [pga\_aggregate\_limit](http://docs.oracle.com/database/121/REFRN/GUID-E364D0E5-19F2-4081-B55E-131DF09CFDB3.htm#REFRN10328)   |  0\-max of memory  |  Y  |  Specifies a limit on the aggregate PGA memory consumed by the instance\.   | 
|   [processor\_group\_name](http://docs.oracle.com/database/121/REFRN/GUID-4CE6E299-4D81-45D8-9EAC-48E76E4911BA.htm#REFRN10323)   |  There is no default value\.  |  N  |  Instructs the database instance to run itself within the specified operating system processor group\.   | 
|   [spatial\_vector\_acceleration](http://docs.oracle.com/database/121/REFRN/GUID-1B7E8737-E176-46AD-BC68-1FCBBD4D05B6.htm#REFRN10337)   |  TRUE, FALSE   |  N  |  Enables or disables the spatial vector acceleration, part of spatial option\.   | 
|   [temp\_undo\_enabled](http://docs.oracle.com/database/121/REFRN/GUID-E2A01A84-2D63-401F-B64E-C96B18C5DCA6.htm#REFRN10326)   |  TRUE, FALSE \(default\)  |  Y  |  Determines whether transactions within a particular session can have a temporary undo log\.   | 
|   [threaded\_execution](http://docs.oracle.com/database/121/REFRN/GUID-7A668A49-9FC5-4245-AD27-10D90E5AE8A8.htm#REFRN10335)   |  TRUE, FALSE  |  N  |  Enables the multithreaded Oracle model, but prevents OS authentication\.   | 
|   [unified\_audit\_sga\_queue\_size](http://docs.oracle.com/database/121/REFRN/GUID-060707DF-8431-4866-8B9F-4F450472D95E.htm#REFRN10343)   |  1 MB \- 30 MB  |  Y  |  Specifies the size of the system global area \(SGA\) queue for unified auditing\.   | 
|   [use\_dedicated\_broker](http://docs.oracle.com/database/121/REFRN/GUID-643239D0-FABF-43C0-9791-BED46CB8FE07.htm#REFRN10341)   |   TRUE, FALSE   |  N  |  Determines how dedicated servers are spawned\.   | 

Several parameters have new value ranges for Oracle Database 12c Release 1 \(12\.1\.0\.2\) on Amazon RDS\. For the old and new value ranges, see the following table\.


****  

|  Parameter name  |  Oracle Database 12c Release 1 \(12\.1\.0\.2\) range  | 
| --- | --- | 
|   [audit\_trail](http://docs.oracle.com/database/121/REFRN/GUID-BD86F593-B606-4367-9FB6-8DAB2E47E7FA.htm#REFRN10006)   |   os \| db \[, extended\] \| xml \[, extended\]   | 
|   [compatible](http://docs.oracle.com/database/121/REFRN/GUID-6C57EE11-BD06-4BB8-A0F7-D6CDDD086FA9.htm#REFRN10019)   |  If you upgrade to Oracle Database 12c Release 2 \(12\.2\.0\.1\) or Oracle Database 19c, `COMPATIBLE` must be `11.2.0` or higher\. We recommend that you use the default settings for `COMPATIBLE` for your version of Oracle Database unless you have a reason to change it\. If `COMPATIBLE` is not explicitly set, Amazon RDS automatically sets this parameter to `12.0.0`\.  | 
|   [db\_securefile](http://docs.oracle.com/database/121/REFRN/GUID-6F7C5E21-3929-4AB1-9C72-1BB9BDDB011F.htm#REFRN10290)   |  PERMITTED \| PREFERRED \| ALWAYS \| IGNORE \| FORCE  | 
|   [db\_writer\_processes](http://docs.oracle.com/database/121/REFRN/GUID-75774634-3B5E-49F8-A5C5-65923F596845.htm#REFRN10043)   |  1\-100  | 
|   [optimizer\_features\_enable](http://docs.oracle.com/database/121/REFRN/GUID-E193EC9E-B642-4C01-99EC-24E04AEA1A2C.htm#REFRN10141)   |   8\.0\.0 to 12\.1\.0\.2   | 
|   [parallel\_degree\_policy](http://docs.oracle.com/database/121/REFRN/GUID-BF09265F-8545-40D4-BD29-E58D5F02B0E5.htm#REFRN10310)   |   MANUAL,LIMITED,AUTO,ADAPTIVE   | 
|   [parallel\_min\_server](http://docs.oracle.com/database/121/REFRN/GUID-1D7EC131-7B5B-40E5-A0F8-ABC7B4C5B0E8.htm#REFRN10160)   |   0 to parallel\_max\_servers   | 

One parameter has a new default value for Oracle Database 12c on Amazon RDS\. The following table shows the new default value\.


****  

|  Parameter name  |  Oracle Database 12c default value  | 
| --- | --- | 
|   job\_queue\_processes   |   50   | 

#### Amazon RDS system privileges for Oracle Database 12c Release 1 \(12\.1\.0\.2\)<a name="Oracle.Concepts.FeatureSupport.12c.Privileges"></a>

Several new system privileges have been granted to the system account for Oracle Database 12c Release 1 \(12\.1\.0\.2\)\. These new system privileges include the following:
+ ALTER ANY CUBE BUILD PROCESS
+ ALTER ANY MEASURE FOLDER
+ ALTER ANY SQL TRANSLATION PROFILE
+ CREATE ANY SQL TRANSLATION PROFILE
+ CREATE SQL TRANSLATION PROFILE
+ DROP ANY SQL TRANSLATION PROFILE
+ EM EXPRESS CONNECT
+ EXEMPT DDL REDACTION POLICY
+ EXEMPT DML REDACTION POLICY
+ EXEMPT REDACTION POLICY
+ LOGMINING
+ REDEFINE ANY TABLE
+ SELECT ANY CUBE BUILD PROCESS
+ SELECT ANY MEASURE FOLDER
+ USE ANY SQL TRANSLATION PROFILE

#### Amazon RDS options for Oracle Database 12c Release 1 \(12\.1\.0\.2\)<a name="Oracle.Concepts.FeatureSupport.12c.Options"></a>

Several Oracle options changed between Oracle Database 11g and Oracle Database 12c Release 1 \(12\.1\.0\.2\), though most of the options remain the same between the two versions\. The Oracle Database 12c Release 1 \(12\.1\.0\.2\) changes include the following: 
+ Oracle Enterprise Manager Database Express 12c replaced Oracle Enterprise Manager 11g Database Control\. For more information, see [Oracle Enterprise Manager Database Express](Appendix.Oracle.Options.OEM_DBControl.md)\. 
+ The option XMLDB is installed by default in Oracle Database 12c Release 1 \(12\.1\.0\.2\)\. You no longer need to install this option yourself\. 

#### Amazon RDS PL/SQL packages for Oracle Database 12c Release 1 \(12\.1\.0\.2\)<a name="Oracle.Concepts.FeatureSupport.12c.Packages"></a>

Oracle Database 12c Release 1 \(12\.1\.0\.2\) includes a number of new built\-in PL/SQL packages\. The packages included with Amazon RDS for Oracle Database 12c Release 1 \(12\.1\.0\.2\) include the following\. 


****  

|  Package name  |  Description  | 
| --- | --- | 
|   [CTX\_ANL](http://docs.oracle.com/cd/E16655_01/appdev.121/e17602/c_anl.htm)   |  The CTX\_ANL package is used with AUTO\_LEXER and provides procedures for adding and dropping a custom dictionary from the lexer\.   | 
|   [DBMS\_APP\_CONT](http://docs.oracle.com/cd/E16655_01/appdev.121/e17602/d_app_cont.htm)   |  The DBMS\_APP\_CONT package provides an interface to determine if the in\-flight transaction on a now unavailable session committed or not, and if the last call on that session completed or not\.   | 
|   [DBMS\_AUTO\_REPORT](http://docs.oracle.com/cd/E16655_01/appdev.121/e17602/d_auto_report.htm#sthref1284)   |  The DBMS\_AUTO\_REPORT package provides an interface to view SQL Monitoring and Real\-time Automatic Database Diagnostic Monitor \(ADDM\) data that has been captured into Automatic Workload Repository \(AWR\)\.   | 
|   [DBMS\_GOLDENGATE\_AUTH](http://docs.oracle.com/cd/E16655_01/appdev.121/e17602/d_goldengate_auth.htm)   |  The DBMS\_GOLDENGATE\_AUTH package provides subprograms for granting privileges to and revoking privileges from GoldenGate administrators\.   | 
|   [DBMS\_HEAT\_MAP](http://docs.oracle.com/cd/E16655_01/appdev.121/e17602/d_heat_map.htm)   |  The DBMS\_HEAT\_MAP package provides an interface to externalize heatmaps at various levels of storage including block, extent, segment, object, and tablespace\.   | 
|   [DBMS\_ILM](http://docs.oracle.com/cd/E16655_01/appdev.121/e17602/d_ilm.htm)   |  The DBMS\_ILM package provides an interface for implementing Information Lifecycle Management \(ILM\) strategies using Automatic Data Optimization \(ADO\) policies\.   | 
|   [DBMS\_ILM\_ADMIN](http://docs.oracle.com/cd/E16655_01/appdev.121/e17602/d_ilm_admin.htm)   |  The DBMS\_ILM\_ADMIN package provides an interface to customize Automatic Data Optimization \(ADO\) policy execution\.   | 
|   [DBMS\_PART](http://docs.oracle.com/cd/E16655_01/appdev.121/e17602/d_part.htm)   |  The DBMS\_PART package provides an interface for maintenance and management operations on partitioned objects\.   | 
|   [DBMS\_PRIVILEGE\_CAPTURE](http://docs.oracle.com/cd/E16655_01/appdev.121/e17602/d_priv_prof.htm)   |  The DBMS\_PRIVILEGE\_CAPTURE package provides an interface to database privilege analysis\.   | 
|   [DBMS\_QOPATCH](http://docs.oracle.com/cd/E16655_01/appdev.121/e17602/d_qopatch.htm)   |  The DBMS\_QOPATCH package provides an interface to view the installed database patches\.   | 
|   [DBMS\_REDACT](http://docs.oracle.com/cd/E16655_01/appdev.121/e17602/d_redact.htm)   |  The DBMS\_REDACT package provides an interface to Oracle Data Redaction, which enables you to mask \(redact\) data that is returned from queries issued by low\-privileged users or an application\.   | 
|   [DBMS\_SPD](http://docs.oracle.com/cd/E16655_01/appdev.121/e17602/d_spd.htm)   |  The DBMS\_SPD package provides subprograms for managing SQL plan directives \(SPD\)\.   | 
|   [DBMS\_SQL\_TRANSLATOR](http://docs.oracle.com/cd/E16655_01/appdev.121/e17602/d_sql_trans.htm)   |  The DBMS\_SQL\_TRANSLATOR package provides an interface for creating, configuring, and using SQL translation profiles\.   | 
|   [DBMS\_SQL\_MONITOR](http://docs.oracle.com/cd/E16655_01/appdev.121/e17602/d_sql_monitor.htm)   |  The DBMS\_SQL\_MONITOR package provides information about real\-time SQL Monitoring and real\-time Database Operation Monitoring\.   | 
|   [DBMS\_SYNC\_REFRESH](http://docs.oracle.com/cd/E16655_01/appdev.121/e17602/d_sync_refresh.htm)   |  The DBMS\_SYNC\_REFRESH package provides an interface to perform a synchronous refresh of materialized views\.   | 
|   [DBMS\_TSDP\_MANAGE](http://docs.oracle.com/cd/E16655_01/appdev.121/e17602/d_tsdp_manage.htm)   |  The DBMS\_TSDP\_MANAGE package provides an interface to import and manage sensitive columns and sensitive column types in the database\. DBMS\_TSDP\_MANAGE is used with the [DBMS\_TSDP\_PROTECT](http://docs.oracle.com/database/121/ARPLS/d_tsdp_protect.htm#BHAFHBHI) package for transparent sensitive data protection \(TSDP\) policies\. DBMS\_TSDP\_MANAGE is available with the Enterprise Edition only\.   | 
|   [DBMS\_TSDP\_PROTECT](http://docs.oracle.com/cd/E16655_01/appdev.121/e17602/d_tsdp_protect.htm)   |  The DBMS\_TSDP\_PROTECT package provides an interface to configure transparent sensitive data protection \(TSDP\) policies in conjunction with the [DBMS\_TSDP\_MANAGE](http://docs.oracle.com/database/121/ARPLS/d_tsdp_manage.htm#CACCBHBC) package\. DBMS\_TSDP\_PROTECT is available with the Enterprise Edition only\.   | 
|   [DBMS\_XDB\_CONFIG](http://docs.oracle.com/database/121/ARPLS/d_xdb_config.htm#ARPLS73564)   |  The DBMS\_XDB\_CONFIG package provides an interface for configuring Oracle XML DB and its repository\.   | 
|  [DBMS\_XDB\_CONSTANTS](http://docs.oracle.com/database/121/ARPLS/d_xdb_constants.htm#ARPLS73572)  |  The DBMS\_XDB\_CONSTANTS package provides an interface to commonly used constants\. Oracle recommends using constants instead of dynamic strings to avoid typographical errors\.   | 
|  [DBMS\_XDB\_REPOS](http://docs.oracle.com/database/121/ARPLS/d_xdb_repos.htm#ARPLS74188)  |  The DBMS\_XDB\_REPOS package provides an interface to operate on the Oracle XML database Repository\.   | 
|   [DBMS\_XMLSCHEMA\_ANNOTATE](http://docs.oracle.com/database/121/ARPLS/d_xmlschema_annotate.htm#ARPLS73580)   |  The DBMS\_XMLSCHEMA\_ANNOTATE package provides an interface to manage and configure the structured storage model, mainly through the use of pre\-registration schema annotations\.   | 
|   [DBMS\_XMLSTORAGE\_MANAGE](http://docs.oracle.com/database/121/ARPLS/d_xmlstorage_man.htm#ARPLS73588)   |  The DBMS\_XMLSTORAGE\_MANAGE package provides an interface to manage and modify XML storage after schema registration has been completed\.   | 
|   [DBMS\_XSTREAM\_ADM](http://docs.oracle.com/cd/E16655_01/appdev.121/e17602/d_xstrm_adm.htm)   |  The DBMS\_XSTREAM\_ADM package provides interfaces for streaming database changes between an Oracle database and other systems\. XStream enables applications to stream out or stream in database changes\.   | 
|   [DBMS\_XSTREAM\_AUTH](http://docs.oracle.com/cd/E16655_01/appdev.121/e17602/d_xstrm_auth.htm)   |  The DBMS\_XSTREAM\_AUTH package provides subprograms for granting privileges to and revoking privileges from XStream administrators\.   | 
|   [UTL\_CALL\_STACK](http://docs.oracle.com/cd/E16655_01/appdev.121/e17602/u_call_stack.htm)   |  The UTL\_CALL\_STACK package provides an interface to provide information about currently executing subprograms\.   | 

#### Oracle Database 12c Release 1 \(12\.1\.0\.2\) packages not supported<a name="Oracle.Concepts.FeatureSupport.12c.NotSupported"></a>

Several Oracle Database 11g PL/SQL packages are not supported in Oracle Database 12c Release 1 \(12\.1\.0\.2\)\. These packages include the following:
+ DBMS\_AUTO\_TASK\_IMMEDIATE
+ DBMS\_CDC\_PUBLISH
+ DBMS\_CDC\_SUBSCRIBE
+ DBMS\_EXPFIL
+ DBMS\_OBFUSCATION\_TOOLKIT
+ DBMS\_RLMGR
+ SDO\_NET\_MEM