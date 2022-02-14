# RDS for Oracle features<a name="Oracle.Concepts.FeatureSupport"></a>

Amazon RDS for Oracle supports most of the features and capabilities of Oracle Database\. Some features might have limited support or restricted privileges\. Some features are only available in Enterprise Edition, and some require additional licenses\. For more information about Oracle Database features for specific Oracle Database versions, see the *Oracle Database Licensing Information User Manual* for the version you're using\.

You can filter new Amazon RDS features on the [What's New with Database?](http://aws.amazon.com/about-aws/whats-new/database/) page\. For **Products**, choose **Amazon RDS**\. Then search using keywords such as **Oracle 2021**\.

**Note**  
These lists are not exhaustive\.

**Topics**
+ [New features in RDS for Oracle](#Oracle.Concepts.FeatureSupport.new)
+ [Supported features in RDS for Oracle](#Oracle.Concepts.FeatureSupport.supported)
+ [Unsupported features in RDS for Oracle](#Oracle.Concepts.FeatureSupport.unsupported)

## New features in RDS for Oracle<a name="Oracle.Concepts.FeatureSupport.new"></a>

To see new Oracle features, use the following techniques:
+ Search [Document history](WhatsNew.md) for the keyword **Oracle**\.
+ You can filter new Amazon RDS features on the [What's New with Database?](http://aws.amazon.com/about-aws/whats-new/database/) page\. For **Products**, choose **Amazon RDS**\. Then search for **Oracle *YYYY***, where *YYYY* is a year such as **2021**\.

The following video shows a recent video from re:Invent about RDS for Oracle new features\.

[![AWS Videos](http://img.youtube.com/vi/mLXiTfntbsc/0.jpg)](http://www.youtube.com/watch?v=mLXiTfntbsc)

## Supported features in RDS for Oracle<a name="Oracle.Concepts.FeatureSupport.supported"></a>

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
+ HugePages

  For more information, see [Enabling HugePages for an Oracle DB instance](Appendix.Oracle.CommonDBATasks.Misc.md#Oracle.Concepts.HugePages)\.
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

  This feature is available only in Oracle Database 19c\. For more information, see [RDS for Oracle architecture](Oracle.Concepts.single-tenant.md) and [Limitations of a single\-tenant CDB](Oracle.Concepts.limitations.md#Oracle.Concepts.single-tenant-limitations)\.
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

## Unsupported features in RDS for Oracle<a name="Oracle.Concepts.FeatureSupport.unsupported"></a>

Amazon RDS Oracle doesn't support the following Oracle Database features:
+ Automatic Storage Management \(ASM\)
+ Database Vault
+ Flashback Database
+ FTP and SFTP
+ Messaging Gateway
+ Oracle Enterprise Manager Cloud Control Management Repository
+ Real Application Clusters \(Oracle RAC\)
+ Real Application Testing
+ Unified Auditing, Pure Mode
+ Workspace Manager \(WMSYS\) schema

**Note**  
The preceding list is not exhausive\.

**Warning**  
In general, Amazon RDS doesn't prevent you from creating schemas for unsupported features\. However, if you create schemas for Oracle features and components that require SYS privileges, you can damage the data dictionary and affect the availability of your instance\. Use only supported features and schemas that are available in [Adding options to Oracle DB instances](Appendix.Oracle.Options.md)\.