# RDS for Oracle features<a name="Oracle.Concepts.FeatureSupport"></a>

Amazon RDS for Oracle supports most of the features and capabilities of Oracle Database\. Some features might have limited support or restricted privileges\. Some features are only available in Enterprise Edition, and some require additional licenses\. For more information about Oracle Database features for specific Oracle Database versions, see the *Oracle Database Licensing Information User Manual* for the version you're using\.

You can filter new Amazon RDS features on the [What's New with Database?](http://aws.amazon.com/about-aws/whats-new/database/) page\. For **Products**, choose **Amazon RDS**\. Then search using keywords such as **Oracle 2022**\.

**Note**  
The following lists are not exhaustive\.

**Topics**
+ [New features in RDS for Oracle](#Oracle.Concepts.FeatureSupport.new)
+ [Supported features in RDS for Oracle](#Oracle.Concepts.FeatureSupport.supported)
+ [Unsupported features in RDS for Oracle](#Oracle.Concepts.FeatureSupport.unsupported)

## New features in RDS for Oracle<a name="Oracle.Concepts.FeatureSupport.new"></a>

To see new RDS for Oracle features, use the following techniques:
+ Search [Document history](WhatsNew.md) for the keyword **Oracle**\.
+ You can filter new Amazon RDS features on the [What's New with Database?](http://aws.amazon.com/about-aws/whats-new/database/) page\. For **Products**, choose **Amazon RDS**\. Then search for **Oracle *YYYY***, where *YYYY* is a year such as **2022**\.

## Supported features in RDS for Oracle<a name="Oracle.Concepts.FeatureSupport.supported"></a>

Amazon RDS for Oracle supports the following Oracle Database features:
+ Advanced Compression
+ Application Express \(APEX\)

  For more information, see [Oracle Application Express \(APEX\)](Appendix.Oracle.Options.APEX.md)\.
+ Automatic Memory Management
+ Automatic Undo Management
+ Automatic Workload Repository \(AWR\)

  For more information, see [Generating performance reports with Automatic Workload Repository \(AWR\)](Appendix.Oracle.CommonDBATasks.Database.md#Appendix.Oracle.CommonDBATasks.AWR)\.
+ Active Data Guard with Maximum Performance in the same AWS Region or across AWS Regions

  For more information, see [Working with read replicas for Amazon RDS for Oracle](oracle-read-replicas.md)\.
+ Blockchain tables \(Oracle Database 21c and higher\)

  For more information, see [Managing Blockchain Tables ](https://docs.oracle.com/en/database/oracle/oracle-database/21/admin/managing-tables.html#GUID-43470B0C-DE4A-4640-9278-B066901C3926) in the Oracle Database documentation\.
+ Continuous Query Notification \(version 12\.1\.0\.2\.v7 and higher\)

  For more information, see [ Using Continuous Query Notification \(CQN\)](https://docs.oracle.com/en/database/oracle/oracle-database/19/adfns/cqn.html#GUID-373BAF72-3E63-42FE-8BEA-8A2AEFBF1C35) in the Oracle documentation\.
+ Data Redaction
+ Database Change Notification

  For more information, see [ Database Change Notification](https://docs.oracle.com/cd/E11882_01/java.112/e16548/dbchgnf.htm#JJDBC28815) in the Oracle documentation\.
**Note**  
This feature changes to Continuous Query Notification in Oracle Database 12c Release 1 \(12\.1\) and higher\.
+ Database In\-Memory \(Oracle Database 12c and higher\)
+ Distributed Queries and Transactions
+ Edition\-Based Redefinition

  For more information, see [Setting the default edition for a DB instance](Appendix.Oracle.CommonDBATasks.Database.md#Appendix.Oracle.CommonDBATasks.DefaultEdition)\.
+ EM Express \(12c and higher\)

  For more information, see [Oracle Enterprise Manager](Oracle.Options.OEM.md)\.
+ Fine\-Grained Auditing
+ Flashback Table, Flashback Query, Flashback Transaction Query
+ Gradual password rollover for applications \(Oracle Database 21c and higher\)

  For more information, see [Managing Gradual Database Password Rollover for Applications ](https://docs.oracle.com/en/database/oracle/oracle-database/19/dbseg/configuring-authentication.html#GUID-ACBA8DAE-C5B4-4811-A31D-53B97C50249B) in the Oracle Database documentation\.
+ HugePages

  For more information, see [Turning on HugePages for an RDS for Oracle instance](Oracle.Concepts.HugePages.md)\.
+ Import/export \(legacy and Data Pump\) and SQL\*Loader

  For more information, see [Importing data into Oracle on Amazon RDS](Oracle.Procedural.Importing.md)\.
+ Java Virtual Machine \(JVM\)

  For more information, see [Oracle Java virtual machine](oracle-options-java.md)\.
+ JavaScript \(Oracle Database 21c and higher\)

  For more information, see [DBMS\_MLE](https://docs.oracle.com/en/database/oracle/oracle-database/21/arpls/dbms_mle.html#GUID-3F5B47A5-2C73-4317-ACD7-E93AE8B8E301) in the Oracle Database documentation\.
+ Label Security \(Oracle Database 12c and higher\)

  For more information, see [Oracle Label Security](Oracle.Options.OLS.md)\.
+ Locator

  For more information, see [Oracle Locator](Oracle.Options.Locator.md)\.
+ Materialized Views
+ Multimedia

  For more information, see [Oracle Multimedia](Oracle.Options.Multimedia.md)\.
+ Multitenant \(single\-tenant architecture only\)

  This feature is available for all Oracle Database 19c and higher releases\. For more information, see [RDS for Oracle architecture](Oracle.Concepts.single-tenant.md) and [Limitations of a single\-tenant CDB](Oracle.Concepts.limitations.md#Oracle.Concepts.single-tenant-limitations)\.
+ Network encryption

  For more information, see [Oracle native network encryption](Appendix.Oracle.Options.NetworkEncryption.md) and [Oracle Secure Sockets Layer](Appendix.Oracle.Options.SSL.md)\.
+ Partitioning
+ Sharding
+ Spatial and Graph

  For more information, see [Oracle Spatial](Oracle.Options.Spatial.md)\.
+ Star Query Optimization
+ Streams and Advanced Queuing
+ Summary Management – Materialized View Query Rewrite
+ Text \(File and URL data store types are not supported\)
+ Total Recall
+ Transparent Data Encryption \(TDE\)

  For more information, see [Oracle Transparent Data Encryption](Appendix.Oracle.Options.AdvSecurity.md)\.
+ Unified Auditing, Mixed Mode

  For more information, see [ Mixed mode auditing](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dbseg/introduction-to-auditing.html#GUID-4A3AEFC3-5422-4320-A048-8219EC96EAC1) in the Oracle documentation\.
+ XML DB \(without the XML DB Protocol Server\)

  For more information, see [Oracle XML DB](Appendix.Oracle.Options.XMLDB.md)\.
+ Virtual Private Database

## Unsupported features in RDS for Oracle<a name="Oracle.Concepts.FeatureSupport.unsupported"></a>

Amazon RDS for Oracle doesn't support the following Oracle Database features:
+ Automatic Storage Management \(ASM\)
+ Database Vault
+ Flashback Database
+ FTP and SFTP
+ Hybrid partitioned tables
+ Messaging Gateway
+ Oracle Enterprise Manager Cloud Control Management Repository
+ Real Application Clusters \(Oracle RAC\)
+ Real Application Testing
+ Unified Auditing, Pure Mode
+ Workspace Manager \(WMSYS\) schema

**Note**  
The preceding list is not exhaustive\.

**Warning**  
In general, Amazon RDS doesn't prevent you from creating schemas for unsupported features\. However, if you create schemas for Oracle features and components that require SYSDBA privileges, you can damage the data dictionary and affect the availability of your DB instance\. Use only supported features and schemas that are available in [Adding options to Oracle DB instances](Appendix.Oracle.Options.md)\.