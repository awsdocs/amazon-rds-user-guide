# RDS for Oracle releases<a name="Oracle.Concepts.database-versions"></a>

Amazon RDS for Oracle supports multiple Oracle Database releases\.

**Note**  
For information about upgrading your releases, see [Upgrading the RDS for Oracle DB engine](USER_UpgradeDBInstance.Oracle.md)\.

**Topics**
+ [Oracle Database 21c with Amazon RDS](#Oracle.Concepts.FeatureSupport.21c)
+ [Oracle Database 19c with Amazon RDS](#Oracle.Concepts.FeatureSupport.19c)
+ [Oracle Database 12c with Amazon RDS](#Oracle.Concepts.FeatureSupport.12c)

## Oracle Database 21c with Amazon RDS<a name="Oracle.Concepts.FeatureSupport.21c"></a>

Amazon RDS supports Oracle Database 21c, which includes Oracle Enterprise Edition and Oracle Standard Edition Two\. Oracle Database 21c \(21\.0\.0\.0\) includes many new features and updates from the previous version\. A key change is that Oracle Database 21c supports only the multitenant architecture: you can no longer create a database as a traditional non\-CDB\. To learn more about the differences between CDBs and non\-CDBs, see [Limitations of a single\-tenant CDB](Oracle.Concepts.limitations.md#Oracle.Concepts.single-tenant-limitations)\.

In this section, you can find the features and changes important to using Oracle Database 21c \(21\.0\.0\.0\) on Amazon RDS\. For a complete list of the changes, see the [Oracle database 21c](https://docs.oracle.com/en/database/oracle/oracle-database/21/index.html) documentation\. For a complete list of features supported by each Oracle Database 21c edition, see [ Permitted features, options, and management packs by Oracle database offering](https://docs.oracle.com/en/database/oracle/oracle-database/21/dblic/Licensing-Information.html) in the Oracle documentation\.

### Amazon RDS parameter changes for Oracle Database 21c \(21\.0\.0\.0\)<a name="Oracle.Concepts.FeatureSupport.21c.parameters"></a>

Oracle Database 21c \(21\.0\.0\.0\) includes several new parameters and parameters with new ranges and new default values\.

**Topics**
+ [New parameters](#Oracle.Concepts.FeatureSupport.21c.parameters.new)
+ [Changes for the compatible parameter](#Oracle.Concepts.FeatureSupport.21c.parameters.compatible)
+ [Removed parameters](#Oracle.Concepts.FeatureSupport.21c.parameters.removed)

#### New parameters<a name="Oracle.Concepts.FeatureSupport.21c.parameters.new"></a>

The following table shows the new Amazon RDS parameters for Oracle Database 21c \(21\.0\.0\.0\)\.


****  

|  Name  |  Range of values  | Default value |  Modifiable  |  Description  | 
| --- | --- | --- | --- | --- | 
|    [blockchain\_table\_max\_no\_drop](https://docs.oracle.com/en/database/oracle/oracle-database/21/refrn/BLOCKCHAIN_TABLE_MAX_NO_DROP.html#GUID-26AF15B2-5621-4602-AA6E-D92842E4285C)    |  `NONE \| 0`  |  `NONE`  |  Y  |  Lets you control the maximum amount of idle time that can be specified when creating a blockchain table\.  | 
|  [dbnest\_enable](https://docs.oracle.com/en/database/oracle/oracle-database/21/refrn/DBNEST_ENABLE.html#GUID-2F30C9D3-808E-42CD-ADA6-595FAE518A60)  |  `NONE \| CDB_RESOURCE_PDB_ALL`  |  `NONE`  |  N  |  Allows you to enable or disable dbNest\. DbNest provides operating system resource isolation and management, file system isolation, and secure computing for PDBs\.   | 
|  [dbnest\_pdb\_fs\_conf](https://docs.oracle.com/en/database/oracle/oracle-database/21/refrn/DBNEST_PDB_FS_CONF.html)  |  `NONE \| pathname`  |  `NONE`  |  N  |  Specifies the dbNest file system configuration file for a PDB\.   | 
|  [diagnostics\_control](https://docs.oracle.com/en/database/oracle/oracle-database/21/refrn/DIAGNOSTICS_CONTROL.html)  |  `ERROR \| WARNING \| IGNORE`  | IGNORE |  Y  |  Allows you to control and monitor the users who perform potentially unsafe database diagnostic operations\.  | 
|  [drcp\_dedicated\_opt](https://docs.oracle.com/en/database/oracle/oracle-database/21/refrn/DRCP_DEDICATED_OPT.html)  |  `YES \| NO`  | YES |  Y  |  Enables or disables the use of dedicated optimization with Database Resident Connection Pooling \(DRCP\)\.  | 
|  [enable\_per\_pdb\_drcp](https://docs.oracle.com/en/database/oracle/oracle-database/21/refrn/ENABLE_PER_PDB_DRCP.html)  |  `true \| false`  |  `true`  |  N  |  Controls whether Database Resident Connection Pooling \(DRCP\) configures one connection pool for the entire CDB or one isolated connection pool for each PDB\.  | 
|  [inmemory\_deep\_vectorization](https://docs.oracle.com/en/database/oracle/oracle-database/21/refrn/INMEMORY_DEEP_VECTORIZATION.html#GUID-59E87FDC-1DB4-4ACD-A807-D0C1AE44210D)  |  `true \| false`  |  `true`  |  Y  |  Enables or disables the deep vectorization framework\.  | 
|  [mandatory\_user\_profile](https://docs.oracle.com/en/database/oracle/oracle-database/21/refrn/MANDATORY_USER_PROFILE.html)  |  *profile\_name*  |  N/A  |  N  |  Specifies the mandatory user profile for a CDB or PDB\.  | 
|  [optimizer\_capture\_sql\_quarantine](https://docs.oracle.com/en/database/oracle/oracle-database/21/refrn/OPTIMIZER_CAPTURE_SQL_QUARANTINE.html)  |  `true \| false`  |  `false`  |  Y  |  Enables or disables the deep vectorization framework\.  | 
|  [optimizer\_use\_sql\_quarantine](https://docs.oracle.com/en/database/oracle/oracle-database/21/refrn/OPTIMIZER_CAPTURE_SQL_QUARANTINE.html#GUID-9DBBDBD3-2AA3-4627-9D3A-5330F447BEBB)  |  `true \| false`  |  `false`  |  Y  |  Enables or disables the automatic creation of SQL Quarantine configurations\.   | 
|  [result\_cache\_execution\_threshold](https://docs.oracle.com/en/database/oracle/oracle-database/21/refrn/RESULT_CACHE_EXECUTION_THRESHOLD.html)  |  `0` to `68719476736`  |  `2`  |  Y  |  Specifies the maximum number of times a PL/SQL function can be executed before its result is stored in the result cache\.   | 
|  [result\_cache\_max\_temp\_result](https://docs.oracle.com/en/database/oracle/oracle-database/21/refrn/RESULT_CACHE_MAX_TEMP_RESULT.html)  |  `0` to `100`  |  `5`  |  Y  |  Specifies the percentage of `RESULT_CACHE_MAX_TEMP_SIZE` that any single cached query result can consume\.   | 
|  [result\_cache\_max\_temp\_size](https://docs.oracle.com/en/database/oracle/oracle-database/21/refrn/RESULT_CACHE_MAX_TEMP_SIZE.html)  |  `0` to `2199023255552`  |  `RESULT_CACHE_SIZE * 10`  |  Y  |  Specifies the maximum amount of temporary tablespace \(in bytes\) that can be consumed by the result cache\.   | 
|  [sga\_min\_size](https://docs.oracle.com/en/database/oracle/oracle-database/21/refrn/SGA_MIN_SIZE.html)  |  `0` to `2199023255552` \(maximum value is 50% of `sga_target`\)  |  `0`  |  Y  |  Indicates a possible minimum value for SGA usage of a pluggable database \(PDB\)\.  | 
|  [tablespace\_encryption\_default\_algorithm](https://docs.oracle.com/en/database/oracle/oracle-database/21/refrn/TABLESPACE_ENCRYPTION_DEFAULT_ALGORITHM.html)  |  `GOST256 \| SEED128 \| ARIA256 \| ARIA192 \| ARIA128 \| 3DES168 \| AES256 \| AES192 \| AES128`  | AES128 |  Y  |  Specifies the default algorithm the database uses when encrypting a tablespace\.   | 

#### Changes for the compatible parameter<a name="Oracle.Concepts.FeatureSupport.21c.parameters.compatible"></a>

The `compatible` parameter has a new maximum value for Oracle Database 21c \(21\.0\.0\.0\) on Amazon RDS\. The following table shows the new default value\.


****  

|  Parameter name  |  Oracle Database 21c \(21\.0\.0\.0\) maximum value  | 
| --- | --- | 
|  [ compatible](https://docs.oracle.com/en/database/oracle/oracle-database/19/refrn/COMPATIBLE.html#GUID-6C57EE11-BD06-4BB8-A0F7-D6CDDD086FA9)  |  21\.0\.0  | 

#### Removed parameters<a name="Oracle.Concepts.FeatureSupport.21c.parameters.removed"></a>

The following parameters were removed in Oracle Database 21c \(21\.0\.0\.0\):
+ `remote_os_authent`
+ `sec_case_sensitive_logon`
+ `unified_audit_sga_queue_size`

## Oracle Database 19c with Amazon RDS<a name="Oracle.Concepts.FeatureSupport.19c"></a>

Amazon RDS supports Oracle Database 19c, which includes Oracle Enterprise Edition and Oracle Standard Edition Two\.

Oracle Database 19c \(19\.0\.0\.0\) includes many new features and updates from the previous version\. In this section, you can find the features and changes important to using Oracle Database 19c \(19\.0\.0\.0\) on Amazon RDS\. For a complete list of the changes, see the [Oracle database 19c](https://docs.oracle.com/en/database/oracle/oracle-database/19/index.html) documentation\. For a complete list of features supported by each Oracle Database 19c edition, see [ Permitted features, options, and management packs by Oracle database offering](https://docs.oracle.com/en/database/oracle/oracle-database/19/dblic/Licensing-Information.html#GUID-0F9EB85D-4610-4EDF-89C2-4916A0E7AC87) in the Oracle documentation\. 

### Amazon RDS parameter changes for Oracle Database 19c \(19\.0\.0\.0\)<a name="Oracle.Concepts.FeatureSupport.19c.Parameters"></a>

Oracle Database 19c \(19\.0\.0\.0\) includes several new parameters and parameters with new ranges and new default values\.

**Topics**
+ [New parameters](#Oracle.Concepts.FeatureSupport.19c.Parameters.new)
+ [Changes to the compatible parameter](#Oracle.Concepts.FeatureSupport.19c.Parameters.compatible)
+ [Removed parameters](#Oracle.Concepts.FeatureSupport.19c.Parameters.compatible.removed-parameters)

#### New parameters<a name="Oracle.Concepts.FeatureSupport.19c.Parameters.new"></a>

The following table shows the new Amazon RDS parameters for Oracle Database 19c \(19\.0\.0\.0\)\.


****  

|  Name  |  Values  |  Modifiable  |  Description  | 
| --- | --- | --- | --- | 
|   [ lob\_signature\_enable](https://docs.oracle.com/en/database/oracle/oracle-database/19/refrn/lob_signature_enable.html#GUID-62997AB5-1084-4C9A-8258-8CB695C7A1D6)   |  TRUE, FALSE \(default\)  |  Y  |  Enables or disables the LOB locator signature feature\.  | 
|   [ max\_datapump\_parallel\_per\_job](https://docs.oracle.com/en/database/oracle/oracle-database/19/refrn/MAX_DATAPUMP_PARALLEL_PER_JOB.html#GUID-33B1F962-B8C3-4DCE-BE68-66FC5D34ECA3)   |  1 to 1024, or AUTO  |  Y  |  Specifies the maximum number of parallel processes allowed for each Oracle Data Pump job\.  | 

#### Changes to the compatible parameter<a name="Oracle.Concepts.FeatureSupport.19c.Parameters.compatible"></a>

The `compatible` parameter has a new maximum value for Oracle Database 19c \(19\.0\.0\.0\) on Amazon RDS\. The following table shows the new default value\. 


****  

|  Parameter name  |  Oracle Database 19c \(19\.0\.0\.0\) maximum value  | 
| --- | --- | 
|  [ compatible](https://docs.oracle.com/en/database/oracle/oracle-database/19/refrn/COMPATIBLE.html#GUID-6C57EE11-BD06-4BB8-A0F7-D6CDDD086FA9)  |  19\.0\.0  | 

#### Removed parameters<a name="Oracle.Concepts.FeatureSupport.19c.Parameters.compatible.removed-parameters"></a>

The following parameters were removed in Oracle Database 19c \(19\.0\.0\.0\):
+ `exafusion_enabled`
+ `max_connections`
+ `o7_dictionary_access`

## Oracle Database 12c with Amazon RDS<a name="Oracle.Concepts.FeatureSupport.12c"></a>

Amazon RDS has deprecated support for Oracle Database 12c on both Oracle Enterprise Edition and Oracle Standard Edition 2\.

**Topics**
+ [Oracle Database 12c Release 2 \(12\.2\.0\.1\) with Amazon RDS](#Oracle.Concepts.FeatureSupport.12cV2Overview)
+ [Oracle Database 12c Release 1 \(12\.1\.0\.2\) with Amazon RDS](#Oracle.Concepts.FeatureSupport.12cV1Overview)

### Oracle Database 12c Release 2 \(12\.2\.0\.1\) with Amazon RDS<a name="Oracle.Concepts.FeatureSupport.12cV2Overview"></a>

On March 31, 2022, Oracle Corporation deprecated support for Oracle Database 12c Release 2 \(12\.2\.0\.1\) for BYOL and LI\. On this date, the release moved from Oracle Extended Support to Oracle Sustaining Support, indicating the end of support for this release\. For more information, see the end of support timeline on [AWS re:Post](https://repost.aws/questions/QUESrwZfKMSSuijzLLHCQkYQ/announcement-amazon-rds-for-oracle-end-of-support-timeline-for-12-c-oracle-release-2-12-2-0-1-and-oracle-release-1-12-1-0-2-major-version) and [Oracle Database 12c upgrade considerations](USER_UpgradeDBInstance.Oracle.OGPG.md#USER_UpgradeDBInstance.Oracle.auto-upgrade)\.


| Date | Action | 
| --- | --- | 
|  April 1, 2022  |  Amazon RDS began automatic upgrades of your Oracle Database 12c Release 2 \(12\.2\.0\.1\) instances to Oracle Database 19c\.  | 
|  April 1, 2022  |  Amazon RDS began automatic upgrades to Oracle Database 19c for any Oracle Database 12c Release 2 \(12\.2\.0\.1\) DB instances restored from snapshots\. The automatic upgrade occurs during maintenance windows\. If maintenance windows aren't available when the upgrade needs to occur, Amazon RDS upgrades the engine immediately\.  | 

### Oracle Database 12c Release 1 \(12\.1\.0\.2\) with Amazon RDS<a name="Oracle.Concepts.FeatureSupport.12cV1Overview"></a>

On July 31, 2022, Amazon RDS deprecated support for Oracle Database 12c Release 1 \(12\.1\.0\.2\) for BYOL and LI\. The release moved from Oracle Extended Support to Oracle Sustaining Support, indicating that Oracle Support will no longer release critical patch updates for this release\. For more information, see the end of support timeline on [AWS re:Post](https://repost.aws/questions/QUESrwZfKMSSuijzLLHCQkYQ/announcement-amazon-rds-for-oracle-end-of-support-timeline-for-12-c-oracle-release-2-12-2-0-1-and-oracle-release-1-12-1-0-2-major-version) and [Oracle Database 12c upgrade considerations](USER_UpgradeDBInstance.Oracle.OGPG.md#USER_UpgradeDBInstance.Oracle.auto-upgrade)\.


| Date | Action | 
| --- | --- | 
|  August 1, 2022  |  Amazon RDS began automatic upgrades of your Oracle Database 12c Release 1 \(12\.1\.0\.2\) instances to the latest Release Update \(RU\) for Oracle Database 19c\. The automatic upgrade occurs during maintenance windows\. If maintenance windows aren't available when the upgrade needs to occur, Amazon RDS upgrades the engine immediately\.  | 
|  August 1, 2022  |  Amazon RDS began automatic upgrades to Oracle Database 19c for any Oracle Database 12c Release 1 \(12\.1\.0\.2\) DB instances restored from snapshots\.  | 