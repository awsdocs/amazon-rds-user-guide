# Oracle on Amazon RDS<a name="CHAP_Oracle"></a>

Amazon RDS supports DB instances that run the following versions and editions of Oracle Database: 
+ Oracle Database 19c \(19\.0\.0\.0\)
+ Oracle Database 18c \(18\.0\.0\.0\)
+ Oracle Database 12c Release 2 \(12\.2\.0\.1\)
+ Oracle Database 12c Release 1 \(12\.1\.0\.2\)

**Note**  
Oracle Database 18c \(18\.0\.0\.0\) is on a deprecation path\. Oracle Corporation will no longer provide patches for Oracle Database 18c after the end\-of\-support date\. For more information, see [Preparing for the automatic upgrade of Oracle Database 18c](USER_UpgradeDBInstance.Oracle.md#USER_UpgradeDBInstance.Oracle.auto-upgrade-of-18c)\.  
RDS for Oracle Database 11g is no longer supported\.

You can create DB instances and DB snapshots, point\-in\-time restores, and automated or manual backups\. You can use DB instances running Oracle inside a VPC\. You can also add features to your Oracle DB instance by enabling various options\. Amazon RDS supports Multi\-AZ deployments for Oracle as a high\-availability, failover solution\. 

To deliver a managed service experience, Amazon RDS doesn't provide shell access to DB instances\. It also restricts access to certain system procedures and tables that require advanced privileges\. You can access databases on a DB instance using any standard SQL client application such as Oracle SQL\*Plus\. However, you can't access the host directly by using Telnet or Secure Shell \(SSH\)\. 

When you create a DB instance using your master account, the account gets DBA privileges, with some limitations\. Use this account for administrative tasks such as creating additional database accounts\. You can't use SYS, SYSTEM, or other Oracle\-supplied administrative accounts\. 

Before creating a DB instance, complete the steps in the [Setting up for Amazon RDS](CHAP_SettingUp.md) section of this guide\. 

## Oracle versions on Amazon RDS<a name="Oracle.Concepts.database-versions"></a>

Amazon RDS for Oracle supports the following major database releases\.

**Topics**
+ [Oracle Database 19c with Amazon RDS](#Oracle.Concepts.FeatureSupport.19c)
+ [Oracle Database 18c on Amazon RDS](#Oracle.Concepts.FeatureSupport.18c)
+ [Oracle Database 12c with Amazon RDS](#Oracle.Concepts.FeatureSupport.12c)

### Oracle Database 19c with Amazon RDS<a name="Oracle.Concepts.FeatureSupport.19c"></a>

Amazon RDS supports Oracle Database 19c, which includes Oracle Enterprise Edition and Oracle Standard Edition Two\.

Oracle Database 19c \(19\.0\.0\.0\) includes many new features and updates from the previous version\. In this section, you can find the features and changes important to using Oracle Database 19c \(19\.0\.0\.0\) on Amazon RDS\. For a complete list of the changes, see the [Oracle database 19c](https://docs.oracle.com/en/database/oracle/oracle-database/19/index.html) documentation\. For a complete list of features supported by each Oracle Database 19c edition, see [ Permitted features, options, and management packs by Oracle database offering](https://docs.oracle.com/en/database/oracle/oracle-database/19/dblic/Licensing-Information.html#GUID-0F9EB85D-4610-4EDF-89C2-4916A0E7AC87) in the Oracle documentation\. 

#### Amazon RDS parameter changes for Oracle Database 19c \(19\.0\.0\.0\)<a name="Oracle.Concepts.FeatureSupport.19c.Parameters"></a>

Oracle Database 19c \(19\.0\.0\.0\) includes several new parameters and parameters with new ranges and new default values\.

The following table shows the new Amazon RDS parameters for Oracle Database 19c \(19\.0\.0\.0\)\.


****  

|  Name  |  Values  |  Modifiable  |  Description  | 
| --- | --- | --- | --- | 
|   [ lob\_signature\_enable](https://docs.oracle.com/en/database/oracle/oracle-database/19/refrn/lob_signature_enable.html#GUID-62997AB5-1084-4C9A-8258-8CB695C7A1D6)   |  TRUE, FALSE \(default\)  |  Y  |  Enables or disables the LOB locator signature feature\.  | 
|   [ max\_datapump\_parallel\_per\_job](https://docs.oracle.com/en/database/oracle/oracle-database/19/refrn/MAX_DATAPUMP_PARALLEL_PER_JOB.html#GUID-33B1F962-B8C3-4DCE-BE68-66FC5D34ECA3)   |  1 to 1024, or AUTO  |  Y  |  Specifies the maximum number of parallel processes allowed for each Oracle Data Pump job\.  | 

The `compatible` parameter has a new maximum value for Oracle Database 19c \(19\.0\.0\.0\) on Amazon RDS\. The following table shows the new default value\. 


****  

|  Parameter name  |  Oracle Database 19c \(19\.0\.0\.0\) maximum value  |  Oracle Database 18c \(18\.0\.0\.0\) maximum value  | 
| --- | --- | --- | 
|  [ compatible](https://docs.oracle.com/en/database/oracle/oracle-database/19/refrn/COMPATIBLE.html#GUID-6C57EE11-BD06-4BB8-A0F7-D6CDDD086FA9)  |  19\.0\.0  |  18\.0\.0  | 

The following parameters were removed in Oracle Database 19c \(19\.0\.0\.0\):
+ exafusion\_enabled
+ max\_connections
+ o7\_dictionary\_access

### Oracle Database 18c on Amazon RDS<a name="Oracle.Concepts.FeatureSupport.18c"></a>

Oracle Corporation intends to deprecate support for Oracle Database 18c \(18\.0\.0\.0\) on July 1, 2021\. On this date, Amazon RDS plans to do the following:
+ Deprecate support for Oracle Database 18c for both BYOL and LI
+ Begin upgrading all Oracle Database 18c instances automatically

The following schedule includes upgrade recommendations\. For more information, see [Preparing for the automatic upgrade of Oracle Database 18c](USER_UpgradeDBInstance.Oracle.md#USER_UpgradeDBInstance.Oracle.auto-upgrade-of-18c)\.


| Action or recommendation | Oracle Database 18c | 
| --- | --- | 
|  We recommend that you upgrade Oracle Database 18c DB instances manually to Oracle Database 19c and validate your applications\.   |  Now–June 30, 2021  | 
|  We recommend that you upgrade Oracle Database 18c snapshots manually to Oracle Database 19c\.  |  May 1, 2021  | 
|  You can no longer create new Oracle Database 18c instances with Amazon RDS\. You can continue to restore 18c DB snapshots without being automatically upgraded until June 30, 2021\.  |  May 1, 2021  | 
|  Amazon RDS plans to start automatic upgrades of your Oracle Database 18c instances to Oracle Database 19c\.  |  July 1, 2021  | 
|  Amazon RDS plans to start automatic upgrades to Oracle Database 19c for any Oracle Database 18c DB instances restored from snapshots\.  |  July 1, 2021  | 

### Oracle Database 12c with Amazon RDS<a name="Oracle.Concepts.FeatureSupport.12c"></a>

Amazon RDS supports Oracle Database 12c, which includes Oracle Enterprise Edition and Oracle Standard Edition 2\. Oracle Database 12c includes the following major versions:
+ [Oracle Database 12c Release 2 \(12\.2\.0\.1\) with Amazon RDS](#Oracle.Concepts.FeatureSupport.12cV2Overview)
+ [Oracle Database 12c Release 1 \(12\.1\.0\.2\) with Amazon RDS](#Oracle.Concepts.FeatureSupport.12cV1Overview)

#### Oracle Database 12c Release 2 \(12\.2\.0\.1\) with Amazon RDS<a name="Oracle.Concepts.FeatureSupport.12cV2Overview"></a>

Oracle Database 12c Release 2 \(12\.2\.0\.1\) includes many new features and updates from the previous version\. In this section, you can find the features and changes important to using Oracle Database 12c Release 2 \(12\.2\.0\.1\) on Amazon RDS\. For a complete list of the changes, see the [Oracle Database 12c Release 2 \(12\.2\) documentation](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/index.html)\. For a complete list of features supported by each Oracle Database 12c edition, see [ Permitted features, options, and management packs by Oracle database offering](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dblic/Licensing-Information.html#GUID-0F9EB85D-4610-4EDF-89C2-4916A0E7AC87) in the Oracle documentation\. 

##### Amazon RDS parameter changes for Oracle Database 12c Release 2 \(12\.2\.0\.1\)<a name="Oracle.Concepts.FeatureSupport.12cV2.Parameters"></a>

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

##### Amazon RDS security changes for Oracle Database 12c Release 2 \(12\.2\.0\.1\)<a name="Oracle.Concepts.FeatureSupport.12cV2.Security"></a>

In Oracle Database 12c Release 2 \(12\.2\.0\.1\), direct grant of the privilege `ADMINISTER DATABASE TRIGGER` is required for the owners of database\-level triggers\. During a major version upgrade to Oracle Database 12c Release 2 \(12\.2\.0\.1\), Amazon RDS grants this privilege to any user that owns a trigger so that the trigger owner has the required privileges\. For more information, see the My Oracle Support document [2275535\.1](https://support.oracle.com/epmos/faces/DocContentDisplay?id=2275535.1)\.

#### Oracle Database 12c Release 1 \(12\.1\.0\.2\) with Amazon RDS<a name="Oracle.Concepts.FeatureSupport.12cV1Overview"></a>

Oracle Database 12c Release 1 \(12\.1\.0\.2\) brings over 500 new features and updates from the previous version\. In this section, you can find the features and changes important to using Oracle Database 12c Release 1 \(12\.1\.0\.2\) on Amazon RDS\. For a complete list of the changes, see the [Oracle Database 12c Release 1 \(12\.1\) documentation](http://docs.oracle.com/database/121/index.htm)\. For a complete list of features supported by each Oracle Database 12c edition, see [Permitted features, options, and management packs by Oracle database edition](https://docs.oracle.com/database/121/DBLIC/editions.htm#DBLIC116) in the Oracle documentation\. 

Oracle Database 12c Release 1 \(12\.1\.0\.2\) includes 16 new parameters that impact your Amazon RDS DB instance, and also 18 new system privileges, several no longer supported packages, and several new option group settings\. For more information on these changes, see the following sections\. 

##### Amazon RDS parameter changes for Oracle Database 12c Release 1 \(12\.1\.0\.2\)<a name="Oracle.Concepts.FeatureSupport.12c.Parameters"></a>

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
|   [compatible](http://docs.oracle.com/database/121/REFRN/GUID-6C57EE11-BD06-4BB8-A0F7-D6CDDD086FA9.htm#REFRN10019)   |  If you upgrade to Oracle Database 12c Release 2 \(12\.2\.0\.1\), Oracle Database 18c, or Oracle Database 19c, `COMPATIBLE` must be `11.2.0` or higher\. We recommend that you use the default settings for `COMPATIBLE` for your version of Oracle Database unless you have a reason to change it\. If `COMPATIBLE` is not explicitly set, Amazon RDS automatically sets this parameter to `12.0.0`\.  | 
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

##### Amazon RDS system privileges for Oracle Database 12c Release 1 \(12\.1\.0\.2\)<a name="Oracle.Concepts.FeatureSupport.12c.Privileges"></a>

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

##### Amazon RDS options for Oracle Database 12c Release 1 \(12\.1\.0\.2\)<a name="Oracle.Concepts.FeatureSupport.12c.Options"></a>

Several Oracle options changed between Oracle Database 11g and Oracle Database 12c Release 1 \(12\.1\.0\.2\), though most of the options remain the same between the two versions\. The Oracle Database 12c Release 1 \(12\.1\.0\.2\) changes include the following: 
+ Oracle Enterprise Manager Database Express 12c replaced Oracle Enterprise Manager 11g Database Control\. For more information, see [Oracle Enterprise Manager Database Express](Appendix.Oracle.Options.OEM_DBControl.md)\. 
+ The option XMLDB is installed by default in Oracle Database 12c Release 1 \(12\.1\.0\.2\)\. You no longer need to install this option yourself\. 

##### Amazon RDS PL/SQL packages for Oracle Database 12c Release 1 \(12\.1\.0\.2\)<a name="Oracle.Concepts.FeatureSupport.12c.Packages"></a>

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

##### Oracle Database 12c Release 1 \(12\.1\.0\.2\) packages not supported<a name="Oracle.Concepts.FeatureSupport.12c.NotSupported"></a>

Several Oracle Database 11g PL/SQL packages are not supported in Oracle Database 12c Release 1 \(12\.1\.0\.2\)\. These packages include the following:
+ DBMS\_AUTO\_TASK\_IMMEDIATE
+ DBMS\_CDC\_PUBLISH
+ DBMS\_CDC\_SUBSCRIBE
+ DBMS\_EXPFIL
+ DBMS\_OBFUSCATION\_TOOLKIT
+ DBMS\_RLMGR
+ SDO\_NET\_MEM

## Oracle licensing options<a name="Oracle.Concepts.Licensing"></a>

Amazon RDS for Oracle has two licensing options: License Included \(LI\) and Bring Your Own License \(BYOL\)\. After you create an Oracle DB instance on Amazon RDS, you can change the licensing model by modifying the DB instance\. For more information, see [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)\. 

### License Included<a name="Oracle.Concepts.Licensing.LicenseIncluded"></a>

In the License Included model, you don't need to purchase Oracle licenses separately\. AWS holds the license for the Oracle database software\. In this model, if you have an AWS Support account with case support, contact AWS Support for both Amazon RDS and Oracle Database service requests\. The License Included model is only supported on Amazon RDS for Oracle Database Standard Edition Two \(SE2\)\.

### Bring Your Own License \(BYOL\)<a name="Oracle.Concepts.Licensing.BYOL"></a>

In the BYOL model, you can use your existing Oracle Database licenses to run Oracle deployments on Amazon RDS\. You must have the appropriate Oracle Database license \(with Software Update License and Support\) for the DB instance class and Oracle Database edition you wish to run\. You must also follow Oracle's policies for licensing Oracle Database software in the cloud computing environment\. For more information on Oracle's licensing policy for Amazon EC2, see [ Licensing Oracle software in the cloud computing environment](http://www.oracle.com/us/corporate/pricing/cloud-licensing-070579.pdf)\. 

In this model, you continue to use your active Oracle support account, and you contact Oracle directly for Oracle Database service requests\. If you have an AWS Support account with case support, you can contact AWS Support for Amazon RDS issues\. Amazon Web Services and Oracle have a multi\-vendor support process for cases that require assistance from both organizations\. 

Amazon RDS supports the BYOL model only for Oracle Database Enterprise Edition \(EE\) and Oracle Database Standard Edition Two \(SE2\)\.

#### Integrating with AWS License Manager<a name="oracle-lms-integration"></a>

To make it easier to monitor Oracle license usage in the BYOL model, [ AWS License Manager](http://aws.amazon.com/license-manager/) integrates with Amazon RDS for Oracle\. License Manager supports tracking of RDS for Oracle engine editions and licensing packs based on virtual cores \(vCPUs\)\. You can also use License Manager with AWS Organizations to manage all of your organizational accounts centrally\.

The following table shows the product information filters for RDS for Oracle\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Oracle.html)

To track license usage of your Oracle DB instances, you can create a license configuration\. In this case, RDS for Oracle resources that match the product information filter are automatically associated with the license configuration\. Discovery of Oracle DB instances can take up to 24 hours\.

##### Console<a name="oracle-lms-integration.console"></a>

**To create a license configuration to track the license usage of your Oracle DB instances**

1. Go to [https://console\.aws\.amazon\.com/license\-manager/](https://console.aws.amazon.com/license-manager/)\.

1. Create a license configuration\.

   For instructions, see [Create a license configuration](https://docs.aws.amazon.com/license-manager/latest/userguide/create-license-configuration.html) in the *AWS License Manager User Guide*\.

   Add a rule for an **RDS Product Information Filter** in the **Product Information** panel\.

   For more information, see [ProductInformation](https://docs.aws.amazon.com/license-manager/latest/APIReference/API_ProductInformation.html) in the *AWS License Manager API Reference*\.

##### AWS CLI<a name="oracle-lms-integration.cli"></a>

To create a license configuration by using the AWS CLI, call the [create\-license\-configuration](https://docs.aws.amazon.com/cli/latest/reference/license-manager/create-license-configuration.html) command\. Use the `--cli-input-json` or `--cli-input-yaml` parameters to pass the parameters to the command\.

**Example**  
The following code creates a license configuration for Oracle Enterprise Edition\.   

```
aws license-manager create-license-configuration -cli-input-json file://rds-oracle-ee.json
```
The following is the sample `rds-oracle-ee.json` file used in the example\.  

```
{
    "Name": "rds-oracle-ee",
    "Description": "RDS Oracle Enterprise Edition",
    "LicenseCountingType": "vCPU",
    "LicenseCountHardLimit": false,
    "ProductInformationList": [
        {
            "ResourceType": "RDS",
            "ProductInformationFilterList": [
                {
                    "ProductInformationFilterName": "Engine Edition",
                    "ProductInformationFilterValue": ["oracle-ee"],
                    "ProductInformationFilterComparator": "EQUALS"
                }
            ]
        }
    ]
}
```

For more information about product information, see [Automated discovery of resource inventory](https://docs.aws.amazon.com/license-manager/latest/userguide/automated-discovery.html) in the *AWS License Manager User Guide*\.

For more information about the `--cli-input` parameter, see [Generating AWS CLI skeleton and input parameters from a JSON or YAML input file](https://docs.aws.amazon.com/cli/latest/userguide/cli-usage-skeleton.html) in the *AWS CLI User Guide*\.

#### Migrating between Oracle editions<a name="Oracle.Concepts.EditionsMigrating"></a>

If you have an unused BYOL Oracle license appropriate for the edition and class of DB instance that you plan to run, you can migrate from Standard Edition 2 \(SE2\) to Enterprise Edition \(EE\)\. You can't migrate from Enterprise Edition to other editions\.

**To change the edition and retain your data**

1. Create a snapshot of the DB instance\.

   For more information, see [Creating a DB snapshot](USER_CreateSnapshot.md)\.

1. Restore the snapshot to a new DB instance, and select the Oracle database edition you want to use\.

   For more information, see [Restoring from a DB snapshot](USER_RestoreFromSnapshot.md)\.

1. \(Optional\) Delete the old DB instance, unless you want to keep it running and have the appropriate Oracle Database licenses for it\.

   For more information, see [Deleting a DB instance](USER_DeleteInstance.md)\.

### Licensing Oracle Multi\-AZ deployments<a name="Oracle.Concepts.Licensing.MAZ"></a>

Amazon RDS supports Multi\-AZ deployments for Oracle as a high\-availability, failover solution\. We recommend Multi\-AZ for production workloads\. For more information, see [High availability \(Multi\-AZ\) for Amazon RDS](Concepts.MultiAZ.md)\. 

If you use the Bring Your Own License model, you must have a license for both the primary DB instance and the standby DB instance in a Multi\-AZ deployment\. 

## RDS for Oracle instance classes<a name="Oracle.Concepts.InstanceClasses"></a>

The computation and memory capacity of a DB instance is determined by its DB instance class\. The DB instance class you need depends on your processing power and memory requirements\. For more information, see [DB instance classes](Concepts.DBInstanceClass.md)\. 

The following are the DB instance classes supported for Oracle\.


****  

| Oracle edition | Oracle Database 19c, Oracle Database 18c, and Oracle Database 12c Release 2 \(12\.2\.0\.1\) support | Oracle Database 12c Release 1 \(12\.1\.0\.2\) support | 
| --- | --- | --- | 
|  Enterprise Edition \(EE\) Bring Your Own License \(BYOL\)  |  db\.m5\.large–db\.m5\.24xlarge db\.m4\.large–db\.m4\.16xlarge db\.z1d\.large–db\.z1d\.12xlarge db\.x1e\.xlarge–db\.x1e\.32xlarge db\.x1\.16xlarge–db\.x1\.32xlarge db\.r5\.large–db\.r5\.24xlarge db\.r5b\.large–db\.r5b\.24xlarge db\.r4\.large–db\.r4\.16xlarge db\.t3\.small–db\.t3\.2xlarge  |  db\.m5\.large–db\.m5\.24xlarge db\.m4\.large–db\.m4\.16xlarge db\.z1d\.large–db\.z1d\.12xlarge db\.x1e\.xlarge–db\.x1e\.32xlarge db\.x1\.16xlarge–db\.x1\.32xlarge db\.r5\.large–db\.r5\.24xlarge db\.r5b\.large–db\.r5b\.24xlarge db\.r4\.large–db\.r4\.16xlarge db\.t3\.micro–db\.t3\.2xlarge  | 
|  Standard Edition 2 \(SE2\) Bring Your Own License \(BYOL\)  |  db\.m5\.large–db\.m5\.4xlarge db\.m4\.large–db\.m4\.4xlarge db\.z1d\.large–db\.z1d\.3xlarge db\.r5\.large–db\.r5\.4xlarge db\.r5b\.large–db\.r5b\.4xlarge db\.r4\.large–db\.r4\.4xlarge db\.t3\.small–db\.t3\.2xlarge  |  db\.m5\.large–db\.m5\.4xlarge db\.m4\.large–db\.m4\.4xlarge db\.z1d\.large–db\.z1d\.3xlarge db\.r5\.large–db\.r5\.4xlarge db\.r5b\.large–db\.r5b\.4xlarge db\.r4\.large–db\.r4\.4xlarge db\.t3\.micro–db\.t3\.2xlarge  | 
|  Standard Edition 2 \(SE2\) License Included  |  db\.m5\.large–db\.m5\.4xlarge db\.m4\.large–db\.m4\.4xlarge db\.r5\.large–db\.r5\.4xlarge db\.r4\.large–db\.r4\.4xlarge db\.t3\.small–db\.t3\.2xlarge  |  db\.m5\.large–db\.m5\.4xlarge db\.m4\.large–db\.m4\.4xlarge db\.r5\.large–db\.r5\.4xlarge db\.r4\.large–db\.r4\.4xlarge db\.t3\.micro–db\.t3\.2xlarge  | 

**Note**  
We encourage all BYOL customers to consult their licensing agreement to assess the impact of Amazon RDS for Oracle deprecations\. For more information on the compute capacity of DB instance classes supported by Amazon RDS for Oracle, see [DB instance classes](Concepts.DBInstanceClass.md) and [Configuring the processor for a DB instance class](Concepts.DBInstanceClass.md#USER_ConfigureProcessor)\.

**Note**  
If you have DB snapshots of DB instances that were using deprecated DB instance classes, you can choose a DB instance class that is not deprecated when you restore the DB snapshots\. For more information, see [Restoring from a DB snapshot](USER_RestoreFromSnapshot.md)\.

### Deprecated DB instance classes for Oracle<a name="Oracle.Concepts.InstanceClasses.Deprecated"></a>

Following are the DB instance classes deprecated for Amazon RDS for Oracle:
+ db\.m1, db\.m2, db\.m3
+ db\.t1, db\.t2
+ db\.r1, db\.r2, db\.r3

The preceding DB instance classes have been replaced by better performing DB instance classes that are generally available at a lower cost\. Amazon RDS for Oracle automatically scales DB instances to DB instance classes that are not deprecated\. 

If you have DB instances that use deprecated DB instance classes, Amazon RDS will modify each one automatically to use a comparable DB instance class that is not deprecated\. You can change the DB instance class for a DB instance yourself by modifying the DB instance\. For more information, see [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)\. 

If you have snapshots of DB instances that were using deprecated instance classes, you can choose an that isn't deprecated when you restore the snapshots\. For more information, see [Restoring from a DB snapshot](USER_RestoreFromSnapshot.md)\.

## RDS for Oracle architecture<a name="Oracle.Concepts.single-tenant"></a>

The multitenant architecture enables an Oracle database to function as a multitenant container database \(CDB\)\. A CDB can include customer\-created pluggable databases \(PDBs\)\. A non\-CDB is an Oracle database that uses the traditional architecture, which can't contain PDBs\. For more information about the multitenant architecture, see [https://docs.oracle.com/en/database/oracle/oracle-database/19/multi/introduction-to-the-multitenant-architecture.html#GUID-267F7D12-D33F-4AC9-AA45-E9CD671B6F22](https://docs.oracle.com/en/database/oracle/oracle-database/19/multi/introduction-to-the-multitenant-architecture.html#GUID-267F7D12-D33F-4AC9-AA45-E9CD671B6F22)\.

For Oracle Database 19c, you must create a DB instance either as a CDB or a non\-CDB\. The architecture is a permanent characteristic and can't be changed later\. For RDS for Oracle versions other than Oracle Database 19c, the architecture is always non\-multitenant\. For more information, see [Creating an Amazon RDS DB instance](USER_CreateDBInstance.md)\.

Currently, RDS for Oracle supports a subset of multitenant architecture called the single\-tenant architecture\. In this case, your CDB contains only one PDB\. The single\-tenant architecture uses the same RDS APIs as the non\-CDB architecture\. Your experience with a non\-CDB is mostly identical to your experience with a PDB\. You can't access the CDB itself\. 

The following sections explain the principal differences between the non\-multitenant and single\-tenant architectures\. For more information, see [Limitations of a single\-tenant CDB](#Oracle.Concepts.single-tenant-limitations)\.

**Topics**
+ [Database creation and connections in a single\-tenant architecture](#Oracle.Concepts.single-tenant.creation)
+ [User accounts and privileges in a single\-tenant architecture](#Oracle.Concepts.single-tenant.users)
+ [Parameters in a single\-tenant architecture](#Oracle.Concepts.single-tenant.parameters)
+ [Snapshots in a single\-tenant architecture](#Oracle.Concepts.single-tenant.snapshots)

### Database creation and connections in a single\-tenant architecture<a name="Oracle.Concepts.single-tenant.creation"></a>

When you create a CDB, specify the DB instance identifier just as for a non\-CDB\. The instance identifier forms the first part of your endpoint\. The system identifier \(SID\) is the name of the CDB\. The SID of every CDB is `RDSCDB`\. You can't choose a different value\.

In the single\-tenant architecture, you always connect to the PDB rather than the CDB\. Specify the endpoint for the PDB just as for a non\-CDB\. The only difference is that you specify *pdb\_name* for the database name, where *pdb\_name* is the name you chose for your PDB\. The following example shows the format for the connection string in SQL\*Plus\.

```
sqlplus 'dbuser@(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=endpoint)(PORT=port))(CONNECT_DATA=(SID=pdb_name)))'
```

### User accounts and privileges in a single\-tenant architecture<a name="Oracle.Concepts.single-tenant.users"></a>

In the Oracle multitenant architecture, all users accounts are either common users or local users\. A CDB common user is a database user whose single identity and password are known in the CDB root and in every existing and future PDB\. In contrast, a local user exists only in a single PDB\.

The RDS master user is a local user account in the PDB\. If you create new user accounts, these users will also be local users residing in the PDB\. You can't use any user accounts to create new PDBs or modify the state of the existing PDB\.

The `rdsadmin` user is a common user account\. You can run Oracle for RDS packages that exist in this account, but you can't log in as `rdsadmin`\. For more information, see [About Common Users and Local Users](https://docs.oracle.com/en/database/oracle/oracle-database/19/dbseg/managing-security-for-oracle-database-users.html#GUID-BBBD9904-F2F3-442B-9AFC-8ACDD9A588D8) in the Oracle documentation\.

### Parameters in a single\-tenant architecture<a name="Oracle.Concepts.single-tenant.parameters"></a>

CDBs have their own parameter classes and different default parameter values\. The CDB parameter classes are as follows:
+ oracle\-ee\-cdb\-19
+ oracle\-se2\-cdb\-19

You specify parameters at the CDB level rather than the PDB level\. The PDB inherits parameter settings from the CDB\. For more information about setting parameters, see [Working with DB parameter groups](CHAP_BestPractices.md#CHAP_BestPractices.DBParameterGroup)\.

### Snapshots in a single\-tenant architecture<a name="Oracle.Concepts.single-tenant.snapshots"></a>

Snapshots work the same in a single\-tenant and non\-multitenant architecture\. The only difference is that when you restore a snapshot, you can only rename the PDB, not the CDB\. The CDB is always named `RDSCDB`\. For more information, see [Oracle Database considerations](USER_RestoreFromSnapshot.md#USER_RestoreFromSnapshot.Oracle)\.

## RDS for Oracle features<a name="Oracle.Concepts.FeatureSupport"></a>

Amazon RDS for Oracle supports most of the features and capabilities of Oracle Database\. Some features might have limited support or restricted privileges\. Some features are only available in Enterprise Edition, and some require additional licenses\. For more information about Oracle Database features for specific Oracle Database versions, see the *Oracle Database Licensing Information User Manual* for the version you're using\.

**Note**  
You can filter new RDS for Oracle features on the [ What's New with Database?](http://aws.amazon.com/about-aws/whats-new/database/) page\. For **Products** filter, choose **Amazon RDS**\. Then search using keywords such as "Oracle 2021"\.

**Note**  
These lists are not exhaustive\.

**Topics**
+ [Supported features for RDS for Oracle](#Oracle.Concepts.FeatureSupport.supported)
+ [Unsupported features for RDS for Oracle](#Oracle.Concepts.FeatureSupport.unsupported)

### Supported features for RDS for Oracle<a name="Oracle.Concepts.FeatureSupport.supported"></a>

Amazon RDS Oracle supports the following Oracle Database features:
+ Advanced Compression
+ Application Express \(APEX\)

  For more information, see [Oracle Application Express \(APEX\)](Appendix.Oracle.Options.APEX.md)\.
+ Automatic Memory Management
+ Automatic Undo Management
+ Automatic Workload Repository \(AWR\)

  For more information, see [Generating performance reports with Automatic Workload Repository \(AWR\)](Appendix.Oracle.CommonDBATasks.Database.md#Appendix.Oracle.CommonDBATasks.AWR)\.
+ Active Data Guard with Maximum Performance in the same AWS Region or across AWS Regions

  For more information, see [Working with Oracle replicas for Amazon RDS](oracle-read-replicas.md)\.
+ Continuous Query Notification \(version 12\.1\.0\.2\.v7 and later\)

  For more information, see [ Using Continuous Query Notification \(CQN\)](https://docs.oracle.com/en/database/oracle/oracle-database/19/adfns/cqn.html#GUID-373BAF72-3E63-42FE-8BEA-8A2AEFBF1C35) in the Oracle documentation\.
+ Data Redaction
+ Database Change Notification

  For more information, see [ Database Change Notification](https://docs.oracle.com/cd/E11882_01/java.112/e16548/dbchgnf.htm#JJDBC28815) in the Oracle documentation\.
**Note**  
This feature changes to Continuous Query Notification in Oracle Database 12c Release 1 \(12\.1\) and later\.
+ Database In\-Memory \(Oracle Database 12c and later\)
+ Distributed Queries and Transactions
+ Edition\-Based Redefinition

  For more information, see [Setting the default edition for a DB instance](Appendix.Oracle.CommonDBATasks.Database.md#Appendix.Oracle.CommonDBATasks.DefaultEdition)\.
+ EM Express \(12c and later\)

  For more information, see [Oracle Enterprise Manager](Oracle.Options.OEM.md)\.
+ Fine\-Grained Auditing
+ Flashback Table, Flashback Query, Flashback Transaction Query
+ Import/export \(legacy and Data Pump\) and SQL\*Loader

  For more information, see [Importing data into Oracle on Amazon RDS](Oracle.Procedural.Importing.md)\.
+ Java Virtual Machine \(JVM\)

  For more information, see [Oracle Java virtual machine](oracle-options-java.md)\.
+ Label Security \(Oracle Database 12c and later\)

  For more information, see [Oracle Label Security](Oracle.Options.OLS.md)\.
+ Locator

  For more information, see [Oracle Locator](Oracle.Options.Locator.md)\.
+ Materialized Views
+ Multimedia

  For more information, see [Oracle Multimedia](Oracle.Options.Multimedia.md)\.
+ Multitenant \(single\-tenant architecture only\)

  This feature is available only in Oracle Database 19c\. For more information, see [RDS for Oracle architecture](#Oracle.Concepts.single-tenant) and [Limitations of a single\-tenant CDB](#Oracle.Concepts.single-tenant-limitations)\.
+ Network encryption

  For more information, see [Oracle native network encryption](Appendix.Oracle.Options.NetworkEncryption.md) and [Oracle Secure Sockets Layer](Appendix.Oracle.Options.SSL.md)\.
+ Partitioning
+ Spatial and Graph

  For more information, see [Oracle Spatial](Oracle.Options.Spatial.md)\.
+ Star Query Optimization
+ Streams and Advanced Queuing
+ Summary Management – Materialized View Query Rewrite
+ Text \(File and URL data store types are not supported\)
+ Total Recall
+ Transparent Data Encryption \(TDE\)

  For more information, see [Oracle Transparent Data Encryption](Appendix.Oracle.Options.AdvSecurity.md)\.
+ Unified Auditing, Mixed Mode \(Oracle Database 12c and later\)

  For more information, see [ Mixed mode auditing](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dbseg/introduction-to-auditing.html#GUID-4A3AEFC3-5422-4320-A048-8219EC96EAC1) in the Oracle documentation\.
+ XML DB \(without the XML DB Protocol Server\)

  For more information, see [Oracle XML DB](Appendix.Oracle.Options.XMLDB.md)\.
+ Virtual Private Database

### Unsupported features for RDS for Oracle<a name="Oracle.Concepts.FeatureSupport.unsupported"></a>

Amazon RDS Oracle doesn't support the following Oracle Database features:
+ Automatic Storage Management \(ASM\)
+ Database Vault
+ Flashback Database
+ Messaging Gateway
+ Oracle Enterprise Manager Cloud Control Management Repository
+ Real Application Clusters \(Oracle RAC\)
+ Real Application Testing
+ Unified Auditing, Pure Mode
+ Workspace Manager \(WMSYS\) schema

**Warning**  
In general, Amazon RDS doesn't prevent you from creating schemas for unsupported features\. However, if you create schemas for Oracle features and components that require SYS privileges, you can damage the data dictionary and affect the availability of your instance\. Use only supported features and schemas that are available in [Adding options to Oracle DB instances](Appendix.Oracle.Options.md)\.

## RDS for Oracle parameters<a name="Oracle.Concepts.FeatureSupport.Parameters"></a>

In Amazon RDS, you manage parameters using parameter groups\. For more information, see [Working with DB parameter groups](USER_WorkingWithParamGroups.md)\. To view the supported parameters for a specific Oracle Database edition and version, run the AWS CLI [https://docs.aws.amazon.com/cli/latest/reference/rds/describe-engine-default-parameters.html](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-engine-default-parameters.html) command\.

For example, to view the supported parameters for the Enterprise Edition of Oracle Database 12c Release 2 \(12\.2\), run the following command\.

```
aws rds describe-engine-default-parameters \
    --db-parameter-group-family oracle-ee-12.2
```

## RDS for Oracle character sets<a name="Appendix.OracleCharacterSets"></a>

Amazon RDS for Oracle supports two types of character sets: the DB character set and national character set\. 

### DB character set<a name="Appendix.OracleCharacterSets.db-character-set"></a>

The Oracle database character set is used in the `CHAR`, `VARCHAR2`, and `CLOB` data types\. The database also uses this character set for metadata such as table names, column names, and SQL statements\. The Oracle database character set is typically referred to as the DB character set\. 

You set the character set when you create a DB instance\. You can't change the DB character set after you create the database\.

#### Supported DB character sets<a name="Appendix.OracleCharacterSets.db-character-set.supported"></a>

The following table lists the Oracle DB character sets that are supported in Amazon RDS\. You can use a value from this table with the `--character-set-name` parameter of the AWS CLI [create\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html) command or with the `CharacterSetName` parameter of the Amazon RDS API [CreateDBInstance](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html) operation\.

**Note**  
The character set for a CDB is always AL32UTF8\. You can set a different character set for the PDB only\.


****  

| Value | Description | 
| --- | --- | 
|  AL32UTF8  |  Unicode 5\.0 UTF\-8 Universal character set \(default\)  | 
|  AR8ISO8859P6  |  ISO 8859\-6 Latin/Arabic  | 
|  AR8MSWIN1256  |  Microsoft Windows Code Page 1256 8\-bit Latin/Arabic  | 
|  BLT8ISO8859P13  |  ISO 8859\-13 Baltic  | 
|  BLT8MSWIN1257  |  Microsoft Windows Code Page 1257 8\-bit Baltic  | 
|  CL8ISO8859P5  |  ISO 88559\-5 Latin/Cyrillic  | 
|  CL8MSWIN1251  |  Microsoft Windows Code Page 1251 8\-bit Latin/Cyrillic  | 
|  EE8ISO8859P2  |  ISO 8859\-2 East European  | 
|  EL8ISO8859P7  |  ISO 8859\-7 Latin/Greek  | 
|  EE8MSWIN1250  |  Microsoft Windows Code Page 1250 8\-bit East European  | 
|  EL8MSWIN1253  |  Microsoft Windows Code Page 1253 8\-bit Latin/Greek  | 
|  IW8ISO8859P8  |  ISO 8859\-8 Latin/Hebrew  | 
|  IW8MSWIN1255  |  Microsoft Windows Code Page 1255 8\-bit Latin/Hebrew  | 
|  JA16EUC  |  EUC 24\-bit Japanese  | 
|  JA16EUCTILDE  |  Same as JA16EUC except for mapping of wave dash and tilde to and from Unicode  | 
|  JA16SJIS  |  Shift\-JIS 16\-bit Japanese  | 
|  JA16SJISTILDE  |  Same as JA16SJIS except for mapping of wave dash and tilde to and from Unicode  | 
|  KO16MSWIN949  |  Microsoft Windows Code Page 949 Korean  | 
|  NE8ISO8859P10  |  ISO 8859\-10 North European  | 
|  NEE8ISO8859P4  |  ISO 8859\-4 North and Northeast European  | 
|  TH8TISASCII  |  Thai Industrial Standard 620\-2533\-ASCII 8\-bit  | 
|  TR8MSWIN1254  |  Microsoft Windows Code Page 1254 8\-bit Turkish  | 
|  US7ASCII  |  ASCII 7\-bit American  | 
|  UTF8  |  Unicode 3\.0 UTF\-8 Universal character set, CESU\-8 compliant  | 
|  VN8MSWIN1258  |  Microsoft Windows Code Page 1258 8\-bit Vietnamese  | 
|  WE8ISO8859P1  |  Western European 8\-bit ISO 8859 Part 1  | 
|  WE8ISO8859P15  |  ISO 8859\-15 West European  | 
|  WE8ISO8859P9  |  ISO 8859\-9 West European and Turkish  | 
|  WE8MSWIN1252  |  Microsoft Windows Code Page 1252 8\-bit West European  | 
|  ZHS16GBK  |  GBK 16\-bit Simplified Chinese  | 
|  ZHT16HKSCS  |  Microsoft Windows Code Page 950 with Hong Kong Supplementary Character Set HKSCS\-2001\. Character set conversion is based on Unicode 3\.0\.  | 
|  ZHT16MSWIN950  |  Microsoft Windows Code Page 950 Traditional Chinese  | 
|  ZHT32EUC  |  EUC 32\-bit Traditional Chinese  | 

#### NLS\_LANG environment variable<a name="Appendix.OracleCharacterSets.db-character-set.nls_lang"></a>

A locale is a set of information addressing linguistic and cultural requirements that corresponds to a given language and country\. Setting the NLS\_LANG environment variable in your client's environment is the simplest way to specify locale behavior for Oracle\. This variable sets the language and territory used by the client application and the database server\. It also indicates the client's character set, which corresponds to the character set for data entered or displayed by a client application\. For more information on NLS\_LANG and character sets, see [What is a character set or code page?](http://www.oracle.com/technetwork/database/database-technologies/globalization/nls-lang-099431.html#_Toc110410570) in the Oracle documentation\.

#### NLS initialization parameters<a name="Appendix.OracleCharacterSets.db-character-set.nls_parameters"></a>

You can also set the following National Language Support \(NLS\) initialization parameters at the instance level for an Oracle DB instance in Amazon RDS:
+ NLS\_DATE\_FORMAT
+ NLS\_LENGTH\_SEMANTICS
+ NLS\_NCHAR\_CONV\_EXCP
+ NLS\_TIME\_FORMAT
+ NLS\_TIME\_TZ\_FORMAT
+ NLS\_TIMESTAMP\_FORMAT
+ NLS\_TIMESTAMP\_TZ\_FORMAT

For information about modifying instance parameters, see [Working with DB parameter groups](USER_WorkingWithParamGroups.md)\.

You can set other NLS initialization parameters in your SQL client\. For example, the following statement sets the NLS\_LANGUAGE initialization parameter to GERMAN in a SQL client that is connected to an Oracle DB instance:

```
ALTER SESSION SET NLS_LANGUAGE=GERMAN;
```

For information about connecting to an Oracle DB instance with a SQL client, see [Connecting to your Oracle DB instance](USER_ConnectToOracleInstance.md)\.

### National character set<a name="Appendix.OracleCharacterSets.nchar-character-set"></a>

The national character set is used in the `NCHAR`, `NVARCHAR2`, and `NCLOB` data types\. The national character set is typically referred to as the NCHAR character set\. Unlike the DB character set, the NCHAR character set doesn't affect database metadata\.

The NCHAR character set supports the following character sets:
+ AL16UTF16 \(default\)
+ UTF8

You can specify either value with the `--nchar-character-set-name` parameter of the [create\-db\-instance](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/rds/create-db-instance.html) command \(AWS CLI version 2 only\)\. If you use the Amazon RDS API, specify the `NcharCharacterSetName` parameter of [CreateDBInstance](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html) operation\. You can't change the national character set after you create the database\.

For more information about Unicode in Oracle databases, see [Supporting multilingual databases with unicode](https://docs.oracle.com/en/database/oracle/oracle-database/19/nlspg/supporting-multilingual-databases-with-unicode.html) in the Oracle documentation\.

## RDS for Oracle limitations<a name="Oracle.Concepts.limitations"></a>

Following are important limitations of using Amazon RDS for Oracle\.

**Note**  
This list is not exhaustive\.

**Topics**
+ [Oracle file size limits in Amazon RDS](#Oracle.Concepts.file-size-limits)
+ [Public synonyms for Oracle\-supplied schemas](#Oracle.Concepts.PublicSynonyms)
+ [Schemas for unsupported features](#Oracle.Concepts.unsupported-features)
+ [Limitations for Oracle DBA privileges](#Oracle.Concepts.dba-limitations)
+ [Limitations of a single\-tenant CDB](#Oracle.Concepts.single-tenant-limitations)
+ [Deprecation of TLS 1\.0 and 1\.1 Transport Layer Security](#Oracle.Concepts.tls)

### Oracle file size limits in Amazon RDS<a name="Oracle.Concepts.file-size-limits"></a>

The maximum file size on Amazon RDS Oracle DB instances is 16 TiB \(tebibytes\)\. If you try to resize a data file in a bigfile tablespace to a value over the limit, you receive an error such as the following\.

```
ORA-01237: cannot extend datafile 6
ORA-01110: data file 6: '/rdsdbdata/db/mydir/datafile/myfile.dbf'
ORA-27059: could not reduce file size
Linux-x86_64 Error: 27: File too large
Additional information: 2
```

### Public synonyms for Oracle\-supplied schemas<a name="Oracle.Concepts.PublicSynonyms"></a>

Don't create or modify public synonyms for Oracle\-supplied schemas, including `SYS`, `SYSTEM`, and `RDSADMIN`\. Such actions might result in invalidation of core database components and affect the availability of your DB instance\.

You can create public synonyms referencing objects in your own schemas\.

### Schemas for unsupported features<a name="Oracle.Concepts.unsupported-features"></a>

In general, Amazon RDS doesn't prevent you from creating schemas for unsupported features\. However, if you create schemas for Oracle features and components that require SYS privileges, you can damage the data dictionary and affect your instance availability\. Use only supported features and schemas that are available in [Adding options to Oracle DB instances](Appendix.Oracle.Options.md)\.

### Limitations for Oracle DBA privileges<a name="Oracle.Concepts.dba-limitations"></a>

In the database, a role is a collection of privileges that you can grant to or revoke from a user\. An Oracle database uses roles to provide security\.

The predefined role `DBA` normally allows all administrative privileges on an Oracle database\. When you create a DB instance, your master user account gets DBA privileges \(with some limitations\)\. To deliver a managed experience, an RDS for Oracle database doesn't provide the following privileges for the `DBA` role: 
+ `ALTER DATABASE`
+ `ALTER SYSTEM`
+ `CREATE ANY DIRECTORY`
+ `DROP ANY DIRECTORY`
+ `GRANT ANY PRIVILEGE`
+ `GRANT ANY ROLE`

Use the master user account for administrative tasks such as creating additional user accounts in the database\. You can't use `SYS`, `SYSTEM`, and other Oracle\-supplied administrative accounts\. 

### Limitations of a single\-tenant CDB<a name="Oracle.Concepts.single-tenant-limitations"></a>

The following options aren't supported for the single\-tenant architecture:
+ Oracle Data Guard
+ Oracle Enterprise Manager
+ Oracle Enterprise Manager Agent
+ Oracle Label Security

The following operations work in a single\-tenant CDB, but no customer\-visible mechanism can detect the current status of the operations:
+ [Enabling and disabling block change tracking](Appendix.Oracle.CommonDBATasks.RMAN.md#Appendix.Oracle.CommonDBATasks.BlockChangeTracking)
+ [Enabling auditing for the SYS\.AUD$ table](Appendix.Oracle.CommonDBATasks.Database.md#Appendix.Oracle.CommonDBATasks.EnablingAuditing)

**Note**  
Auditing information isn't available from within the PDB\.

### Deprecation of TLS 1\.0 and 1\.1 Transport Layer Security<a name="Oracle.Concepts.tls"></a>

Transport Layer Security protocol versions 1\.0 and 1\.1 \(TLS 1\.0 and TLS 1\.1\) are deprecated\. In accordance with security best practices, Oracle has deprecated the use of TLS 1\.0 and TLS 1\.1\. To meet your security requirements, RDS for Oracle strongly recommends that you use TLS 1\.2 instead\.