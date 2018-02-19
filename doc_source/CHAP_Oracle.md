# Oracle on Amazon RDS<a name="CHAP_Oracle"></a>

Amazon RDS supports DB instances running several versions and editions of Oracle Database\. You can use the following versions and editions: 

+ Oracle 12c, Version 12\.1\.0\.2

+ Oracle 11g, Version 11\.2\.0\.4

Amazon RDS also currently supports the following versions and editions that are on deprecation paths, because Oracle no longer provides patches for them: 

+ Oracle 12c, Version 12\.1\.0\.1 \([Deprecation of Oracle 12\.1\.0\.1](#Oracle.Concepts.Deprecate.12101)\) 

+ Oracle 11g, Version 11\.2\.0\.3 \([Deprecation of Oracle 11\.2\.0\.3](#Oracle.Concepts.Deprecate.11203)\) 

+ Oracle 11g, Version 11\.2\.0\.2 \([Deprecation of Oracle 11\.2\.0\.2](#Oracle.Concepts.Deprecate.11202)\) 

You can create DB instances and DB snapshots, point\-in\-time restores and automated or manual backups\. DB instances running Oracle can be used inside a VPC\. You can also enable various options to add additional features to your Oracle DB instance\. Amazon RDS supports Multi\-AZ deployments for Oracle as a high\-availability, failover solution\. 

In order to deliver a managed service experience, Amazon RDS does not provide shell access to DB instances, and it restricts access to certain system procedures and tables that require advanced privileges\. Amazon RDS supports access to databases on a DB instance using any standard SQL client application such as Oracle SQL Plus\. Amazon RDS does not allow direct host access to a DB instance via Telnet or Secure Shell \(SSH\)\. 

When you create a DB instance, the master account that you use to create the instance gets DBA user privileges \(with some limitations\)\. Use this account for any administrative tasks such as creating additional user accounts in the database\. The SYS user, SYSTEM user, and other administrative accounts are locked and cannot be used\. 

Before creating a DB instance, you should complete the steps in the [Setting Up for Amazon RDS](CHAP_SettingUp.md) section of this guide\. 

## Common Management Tasks for Oracle on Amazon RDS<a name="Oracle.Concepts.General"></a>

The following are the common management tasks you perform with an Amazon RDS Oracle DB instance, with links to relevant documentation for each task\. 


****  

| Task Area | Relevant Documentation | 
| --- | --- | 
|  **Instance Classes, Storage, and PIOPS** If you are creating a DB instance for production purposes, you should understand how instance classes, storage types, and Provisioned IOPS work in Amazon RDS\.   |  [DB Instance Class Support for Oracle](#Oracle.Concepts.InstanceClasses) [Amazon RDS Storage Types](CHAP_Storage.md#Concepts.Storage)   | 
|  **Multi\-AZ Deployments** A production DB instance should use Multi\-AZ deployments\. Multi\-AZ deployments provide increased availability, data durability, and fault tolerance for DB instances\.   |  [High Availability \(Multi\-AZ\)](Concepts.MultiAZ.md)  | 
|  **Amazon Virtual Private Cloud \(VPC\)** If your AWS account has a default VPC, then your DB instance is automatically created inside the default VPC\. If your account does not have a default VPC, and you want the DB instance in a VPC, you must create the VPC and subnet groups before you create the DB instance\.   |  [Determining Whether You Are Using the EC2\-VPC or EC2\-Classic Platform](USER_VPC.FindDefaultVPC.md) [Working with an Amazon RDS DB Instance in a VPC](USER_VPC.WorkingWithRDSInstanceinaVPC.md)  | 
|  **Security Groups** By default, DB instances are created with a firewall that prevents access to them\. You therefore must create a security group with the correct IP addresses and network configuration to access the DB instance\. The security group you create depends on what Amazon EC2 platform your DB instance is on, and whether you will access your DB instance from an Amazon EC2 instance\.  In general, if your DB instance is on the *EC2\-Classic* platform, you will need to create a DB security group; if your DB instance is on the *EC2\-VPC* platform, you will need to create a VPC security group\.   |  [Determining Whether You Are Using the EC2\-VPC or EC2\-Classic Platform](USER_VPC.FindDefaultVPC.md) [Amazon RDS Security Groups](Overview.RDSSecurityGroups.md)   | 
|  **Parameter Groups** If your DB instance is going to require specific database parameters, you should create a parameter group before you create the DB instance\.   |  [Working with DB Parameter Groups](USER_WorkingWithParamGroups.md)  | 
|  **Option Groups** If your DB instance is going to require specific database options, you should create an option group before you create the DB instance\.   |  [Options for Oracle DB Instances](Appendix.Oracle.Options.md)  | 
|  **Connecting to Your DB Instance** After creating a security group and associating it to a DB instance, you can connect to the DB instance using any standard SQL client application such as Oracle SQL Plus\.   |  [Connecting to a DB Instance Running the Oracle Database Engine](USER_ConnectToOracleInstance.md)  | 
|  **Backup and Restore** You can configure your DB instance to take automated backups, or take manual snapshots, and then restore instances from the backups or snapshots\.   |  [Backing Up and Restoring Amazon RDS DB Instances](CHAP_CommonTasks.BackupRestore.md)  | 
|  **Monitoring** You can monitor an Oracle DB instance by using CloudWatch Amazon RDS metrics, events, and enhanced monitoring\.   |  [Viewing DB Instance Metrics](CHAP_Monitoring.md#USER_Monitoring) [Viewing Amazon RDS Events](USER_ListEvents.md)  | 
|  **Log Files** You can access the log files for your Oracle DB instance\.   |  [Amazon RDS Database Log Files](USER_LogAccess.md)  | 

There are also advanced tasks and optional features for working with Oracle DB instances\. For more information, see the following documentation: 

+ For information on common DBA tasks for Oracle on Amazon RDS, see [Common DBA Tasks for Oracle DB Instances](Appendix.Oracle.CommonDBATasks.md)\.

+ For information on Oracle GoldenGate support, see [Using Oracle GoldenGate with Amazon RDS](Appendix.OracleGoldenGate.md)\. 

+ For information on Siebel Customer Relationship Management \(CRM\) support, see [Installing a Siebel Database on Oracle on Amazon RDS](Oracle.Resources.Siebel.md)\. 

## Oracle Licensing<a name="Oracle.Concepts.Licensing"></a>

There are two licensing options available for Amazon RDS for Oracle; License Included and Bring Your Own License \(BYOL\)\. After you create an Oracle DB instance on Amazon RDS, you can change the licensing model by using the [AWS Management Console](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_ModifyInstance.Oracle.html), the Amazon RDS API [ModifyDBInstance](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html) action, or the AWS CLI [modify\-db\-instance](http://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) command\. 

### License Included<a name="Oracle.Concepts.Licensing.LicenseIncluded"></a>

In the License Included model, you don't need to purchase Oracle licenses separately; AWS holds the license for the Oracle database software\. In this model, if you have an AWS Support account with case support, you contact AWS Support for both Amazon RDS and Oracle Database service requests\. 

The License Included model is supported on Amazon RDS for the following Oracle database editions: 

+ Oracle Database Standard Edition One \(SE1\)

+ Oracle Database Standard Edition Two \(SE2\)

### Bring Your Own License \(BYOL\)<a name="Oracle.Concepts.Licensing.BYOL"></a>

In the Bring Your Own License model, you can use your existing Oracle Database licenses to run Oracle deployments on Amazon RDS\. You must have the appropriate Oracle Database license \(with Software Update License and Support\) for the DB instance class and Oracle Database edition you wish to run\. You must also follow Oracle's policies for licensing Oracle Database software in the cloud computing environment\. For more information on Oracle's licensing policy for Amazon EC2, see [ Licensing Oracle Software in the Cloud Computing Environment](http://www.oracle.com/us/corporate/pricing/cloud-licensing-070579.pdf)\. 

In this model, you continue to use your active Oracle support account, and you contact Oracle directly for Oracle Database service requests\. If you have an AWS Support account with case support, you can contact AWS Support for Amazon RDS issues\. Amazon Web Services and Oracle have a multi\-vendor support process for cases which require assistance from both organizations\. 

The Bring Your Own License model is supported on Amazon RDS for the following Oracle database editions:

+ Oracle Database Enterprise Edition \(EE\)

+ Oracle Database Standard Edition \(SE\)

+ Oracle Database Standard Edition One \(SE1\)

+ Oracle Database Standard Edition Two \(SE2\)

### Licensing Oracle Multi\-AZ Deployments<a name="Oracle.Concepts.Licensing.MAZ"></a>

Amazon RDS supports Multi\-AZ deployments for Oracle as a high\-availability, failover solution\. We recommend Multi\-AZ for production workloads\. For more information, see [High Availability \(Multi\-AZ\)](Concepts.MultiAZ.md)\. 

If you use the Bring Your Own License model, you must have a license for both the primary DB instance and the standby DB instance in a Multi\-AZ deployment\. 

## DB Instance Class Support for Oracle<a name="Oracle.Concepts.InstanceClasses"></a>

The computation and memory capacity of a DB instance is determined by its DB instance class\. The DB instance class you need depends on your processing power and memory requirements\. For more information, see [DB Instance Class](Concepts.DBInstanceClass.md)\. 

The following are the DB instance classes supported for Oracle\. 


****  

| Oracle Edition | Version 12\.1\.0\.2 Support | Version 11\.2\.0\.4 Support | 
| --- | --- | --- | 
|  Enterprise Edition \(EE\) Bring Your Own License \(BYOL\)  |  db\.m4\.large–db\.m4\.16xlarge db\.m3\.medium–db\.m3\.2xlarge db\.m2\.xlarge–db\.m2\.4xlarge db\.m1\.small–db\.m1\.xlarge db\.r4\.large–db\.r4\.16xlarge db\.r3\.large–db\.r3\.8xlarge db\.t2\.micro–db\.t2\.2xlarge  |  db\.m4\.large–db\.m4\.16xlarge db\.m3\.medium–db\.m3\.2xlarge db\.m2\.xlarge–db\.m2\.4xlarge db\.m1\.small–db\.m1\.xlarge db\.r4\.large–db\.r4\.16xlarge db\.r3\.large–db\.r3\.8xlarge db\.t2\.micro–db\.t2\.2xlarge  | 
|  Standard Edition 2 \(SE2\) Bring Your Own License \(BYOL\)  |  db\.m4\.large–db\.m4\.4xlarge db\.m3\.medium–db\.m3\.2xlarge db\.m2\.xlarge–db\.m2\.4xlarge db\.m1\.small–db\.m1\.xlarge db\.r4\.large–db\.r4\.4xlarge db\.r3\.large–db\.r3\.4xlarge db\.t2\.micro–db\.t2\.2xlarge  |  —  | 
|  Standard Edition 2 \(SE2\) License Included  |  db\.m4\.large–db\.m4\.4xlarge db\.m3\.medium–db\.m3\.2xlarge db\.m2\.xlarge–db\.m2\.4xlarge db\.m1\.small–db\.m1\.xlarge db\.r4\.large–db\.r4\.4xlarge db\.r3\.large–db\.r3\.4xlarge db\.t2\.micro–db\.t2\.2xlarge  |  —  | 
|  Standard Edition 1 \(SE1\) Bring Your Own License \(BYOL\)  |  —  |  db\.m4\.large–db\.m4\.4xlarge db\.m3\.medium–db\.m3\.2xlarge db\.m2\.xlarge–db\.m2\.4xlarge db\.m1\.small–db\.m1\.xlarge db\.r4\.large–db\.r4\.4xlarge db\.r3\.large–db\.r3\.4xlarge db\.t2\.micro–db\.t2\.2xlarge  | 
|  Standard Edition 1 \(SE1\) License Included  |  —  |  db\.m4\.large–db\.m4\.4xlarge db\.m3\.medium–db\.m3\.2xlarge db\.m2\.xlarge–db\.m2\.4xlarge db\.m1\.small–db\.m1\.xlarge db\.r3\.large–db\.r3\.4xlarge db\.t2\.micro–db\.t2\.large  | 
|  Standard Edition \(SE\) Bring Your Own License \(BYOL\)  |  —  |  db\.m4\.large–db\.m4\.4xlarge db\.m3\.medium–db\.m3\.2xlarge db\.m2\.xlarge–db\.m2\.4xlarge db\.m1\.small–db\.m1\.xlarge db\.r4\.large–db\.r4\.8xlarge db\.r3\.large–db\.r3\.8xlarge db\.t2\.micro–db\.t2\.2xlarge  | 

## Oracle Security<a name="Oracle.Concepts.RestrictedDBAPrivileges"></a>

The Oracle database engine uses role\-based security\. A role is a collection of privileges that can be granted to or revoked from a user\. A predefined role, named *DBA*, normally allows all administrative privileges on an Oracle database engine\. The following privileges are not available for the DBA role on an Amazon RDS DB instance using the Oracle engine: 

+ Alter database

+ Alter system

+ Create any directory

+ Drop any directory

+ Grant any privilege

+ Grant any role

When you create a DB instance, the master account that you use to create the instance gets DBA user privileges \(with some limitations\)\. Use this account for any administrative tasks such as creating additional user accounts in the database\. The SYS user, SYSTEM user, and other administrative accounts are locked and cannot be used\. 

Amazon RDS Oracle supports SSL/TLS encrypted connections as well as the Oracle Native Network Encryption \(NNE\) option to encrypt connections between your application and your Oracle DB instance\. For more information about using SSL with Oracle on Amazon RDS, see [Using SSL with an Oracle DB Instance](#Oracle.Concepts.SSL)\. For more information about the Oracle Native Network Encryption option, see [Oracle Native Network Encryption](Appendix.Oracle.Options.NetworkEncryption.md)\. 

## Using SSL with an Oracle DB Instance<a name="Oracle.Concepts.SSL"></a>

Secure Sockets Layer \(SSL\) is an industry standard protocol used for securing network connections between client and server\. After SSL version 3\.0, the name was changed to Transport Layer Security \(TLS\), but it is still often referred to as SSL and we refer to the protocol as SSL\. Amazon RDS supports SSL encryption for Oracle DB instances\. Using SSL, you can encrypt a connection between your application client and your Oracle DB instance\. SSL support is available in all AWS regions for Oracle\. 

You enable SSL encryption for an Oracle DB instance by adding the Oracle SSL option to the option group associated with the DB instance\. Amazon RDS uses a second port, as required by Oracle, for SSL connections which allows both clear text and SSL\-encrypted communication to occur at the same time between a DB instance and an Oracle client\. For example, you can use the port with clear text communication to communicate with other resources inside a VPC while using the port with SSL\-encrypted communication to communicate with resources outside the VPC\. 

For more information, see [Oracle SSL](Appendix.Oracle.Options.SSL.md)\. 

**Note**  
You can't use both SSL and Oracle native network encryption \(NNE\) on the same DB instance\. Before you can use SSL encryption, you must disable any other connection encryption\. 

## Oracle 12c with Amazon RDS<a name="Oracle.Concepts.FeatureSupport.12c"></a>

Amazon RDS supports Oracle version 12c, which includes Oracle Enterprise Edition and Oracle Standard Edition Two\. Oracle version 12c brings over 500 new features and updates from the previous version\. This section covers the features and changes important to using Oracle 12c on Amazon RDS\. For a complete list of the changes, see the [Oracle 12c documentation](http://docs.oracle.com/database/121/index.htm)\. For a complete list of features supported by each Oracle 12c edition, see [Feature Availability by Edition](https://docs.oracle.com/database/121/DBLIC/editions.htm#DBLIC116)\. 

Oracle 12c includes sixteen new parameters that impact your Amazon RDS DB instance, as well as eighteen new system privileges, several no longer supported packages, and several new option group settings\. The following sections provide more information on these changes\. 

### Amazon RDS Parameter Changes for Oracle 12c<a name="Oracle.Concepts.FeatureSupport.12c.Parameters"></a>

Oracle 12c includes sixteen new parameters in addition to several parameters with new ranges and new default values\.

The following table shows the new Amazon RDS parameters for Oracle 12c: 


****  

| Name | Values | Modifiable | Description | 
| --- | --- | --- | --- | 
|  [connection\_brokers](http://docs.oracle.com/database/121/REFRN/GUID-C6C3A860-D74C-4D3D-9185-E59794360ACA.htm#REFRN10333)  |  CONNECTION\_BROKERS = broker\_description\[,\.\.\.\]  | N | Specifies connection broker types, the number of connection brokers of each type, and the maximum number of connections per broker\.  | 
|  [db\_index\_compression\_inheritance](https://docs.oracle.com/database/121/REFRN/GUID-5238D586-B068-46F7-9D8F-E4C174E5D5B2.htm#REFRN10336)  | TABLESPACE, TABL, ALL, NONE | Y | Displays the options that are set for table or tablespace level compression inheritance\.  | 
|  [db\_big\_table\_cache\_percent\_target](https://docs.oracle.com/database/121/REFRN/GUID-122865EE-4589-434D-8DD5-4E201C6CC739.htm#REFRN10340)  | 0\-90 | Y | Specifies the cache section target size for automatic big table caching, as a percentage of the buffer cache\.  | 
|  [heat\_map](http://docs.oracle.com/database/121/REFRN/GUID-F0F60587-56A5-4727-B9D4-FA44ADD7774E.htm#REFRN10342)  |  ON,OFF  | Y | Enables the database to track read and write access of all segments, as well as modification of database blocks, due to data manipulation language \(DML\) and data definition language \(DDL\) statements\.  | 
|  [inmemory\_clause\_default](http://docs.oracle.com/database/121/REFRN/GUID-5772F775-2A3E-4BC8-AA03-B8FF383BEE52.htm#REFRN10350)  | INMEMORY,NO INMEMORY | Y | INMEMORY\_CLAUSE\_DEFAULT enables you to specify a default In\-Memory Column Store \(IM column store\) clause for new tables and materialized views\. | 
|  [inmemory\_clause\_default\_memcompress](http://docs.oracle.com/database/121/REFRN/GUID-5772F775-2A3E-4BC8-AA03-B8FF383BEE52.htm#REFRN10350)  | NO MEMCOMPRESS,MEMCOMPRESS FOR DML,MEMCOMPRESS FOR QUERY, MEMCOMPRESS FOR QUERY LOW,MEMCOMPRESS FOR QUERY HIGH,MEMCOMPRESS FOR CAPACITY,MEMCOMPRESS FOR CAPACITY LOW,MEMCOMPRESS FOR CAPACITY HIGH | Y | See INMEMORY\_CLAUSE\_DEFAULT\. | 
|  [inmemory\_clause\_default\_priority](http://docs.oracle.com/database/121/REFRN/GUID-5772F775-2A3E-4BC8-AA03-B8FF383BEE52.htm#REFRN10350)  | PRIORITY LOW,PRIORITY MEDIUM,PRIORITY HIGH,PRIORITY CRITICAL,PRIORITY NONE | Y | See INMEMORY\_CLAUSE\_DEFAULT\. | 
|  [inmemory\_force](http://docs.oracle.com/database/121/REFRN/GUID-1CAEDEBC-AE38-428D-B07E-6718A7225548.htm#REFRN10353)  | DEFAULT, OFF | Y | INMEMORY\_FORCE allows you to specify whether tables and materialized view that are specified as INMEMORY are populated into the In\-Memory Column Store \(IM column store\) or not\. | 
|  [inmemory\_max\_populate\_servers](http://docs.oracle.com/database/121/REFRN/GUID-3428C0FF-5AA9-491C-908E-F0BD88095A5A.htm#REFRN10355)  | Null | N | INMEMORY\_MAX\_POPULATE\_SERVERS specifies the maximum number of background populate servers to use for In\-Memory Column Store \(IM column store\) population, so that these servers do not overload the rest of the system\. | 
|  [inmemory\_query](http://docs.oracle.com/database/121/REFRN/GUID-5B9BDBE2-6835-4598-9717-64D2C9D622AB.htm#REFRN10351)  |  ENABLE \(default\), DISABLE | Y | INMEMORY\_QUERY is used to enable or disable in\-memory queries for the entire database at the session or system level\. | 
|  [inmemory\_size](http://docs.oracle.com/database/121/REFRN/GUID-B5BEB6BF-5308-485F-920D-CBB584DDDE8F.htm#REFRN10348)  | 0,104857600\-274877906944  | Y | INMEMORY\_SIZE sets the size of the In\-Memory Column Store \(IM column store\) on a database instance\. | 
|  [inmemory\_trickle\_repopulate\_servers\_percent](http://docs.oracle.com/database/121/REFRN/GUID-499850D5-6418-4AD3-BEB5-865C4A165C39.htm#REFRN10358)  | 0 to 50 | Y | INMEMORY\_TRICKLE\_REPOPULATE\_SERVERS\_PERCENT limits the maximum number of background populate servers used for In\-Memory Column Store \(IM column store\) repopulation, as trickle repopulation is designed to use only a small percentage of the populate servers\. | 
|  [max\_string\_size](http://docs.oracle.com/database/121/REFRN/GUID-D424D23B-0933-425F-BC69-9C0E6724693C.htm#REFRN10321)  |  STANDARD \(default\), EXTENDED  | N | Controls the maximum size of VARCHAR2, NVARCHAR2, and RAW\. | 
|  [optimizer\_adaptive\_features](http://docs.oracle.com/database/121/REFRN/GUID-F5E53EFA-B395-4336-B046-1EE7AF12353B.htm#REFRN10344)  |  TRUE \(default\), FALSE  | Y | Enables or disables all of the adaptive optimizer features\. | 
|  [optimizer\_adaptive\_reporting\_only](http://docs.oracle.com/database/121/REFRN/GUID-8DD128F9-4891-4061-9B2D-9D45315D44FB.htm#REFRN10327)  |  TRUE,FALSE \(default\)  | Y | Controls reporting\-only mode for adaptive optimizations\.  | 
|  [pdb\_file\_name\_convert](http://docs.oracle.com/database/121/REFRN/GUID-074F8896-D565-4139-BCDB-C81C9D741941.htm#REFRN10322)  |  | N | Maps names of existing files to new file names\. | 
|  [pga\_aggregate\_limit](http://docs.oracle.com/database/121/REFRN/GUID-E364D0E5-19F2-4081-B55E-131DF09CFDB3.htm#REFRN10328)  |  1\-max of memory  | Y | Specifies a limit on the aggregate PGA memory consumed by the instance\.  | 
|  [processor\_group\_name](http://docs.oracle.com/database/121/REFRN/GUID-4CE6E299-4D81-45D8-9EAC-48E76E4911BA.htm#REFRN10323)  |  | N | Instructs the database instance to run itself within the specified operating system processor group\.  | 
|  [spatial\_vector\_acceleration](http://docs.oracle.com/database/121/REFRN/GUID-1B7E8737-E176-46AD-BC68-1FCBBD4D05B6.htm#REFRN10337)  |  TRUE,FALSE  | N | Enables or disables the spatial vector acceleration, part of spacial option\.  | 
|  [temp\_undo\_enabled](http://docs.oracle.com/database/121/REFRN/GUID-E2A01A84-2D63-401F-B64E-C96B18C5DCA6.htm#REFRN10326)  |  TRUE,FALSE \(default\)  | Y | Determines whether transactions within a particular session can have a temporary undo log\.  | 
|  [threaded\_execution](http://docs.oracle.com/database/121/REFRN/GUID-7A668A49-9FC5-4245-AD27-10D90E5AE8A8.htm#REFRN10335)  |  TRUE,FALSE  | N | Enables the multithreaded Oracle model, but prevents OS authentication\.  | 
|  [unified\_audit\_sga\_queue\_size](http://docs.oracle.com/database/121/REFRN/GUID-060707DF-8431-4866-8B9F-4F450472D95E.htm#REFRN10343)  |  1 MB \- 30 MB  | Y | Specifies the size of the system global area \(SGA\) queue for unified auditing\.  | 
|  [use\_dedicated\_broker](http://docs.oracle.com/database/121/REFRN/GUID-643239D0-FABF-43C0-9791-BED46CB8FE07.htm#REFRN10341)  |  TRUE,FALSE  | N | Determines how dedicated servers are spawned\.  | 

Several parameter have new value ranges for Oracle 12c on Amazon RDS\. The following table shows the old and new value ranges: 


****  

| Parameter Name | 12c Range | 11g Range | 
| --- | --- | --- | 
|  [audit\_trail](http://docs.oracle.com/database/121/REFRN/GUID-BD86F593-B606-4367-9FB6-8DAB2E47E7FA.htm#REFRN10006)  |  os | db \[, extended\] | xml \[, extended\]  |  os | db \[, extended\] | xml \[, extended\] | true | false  | 
|  [compatible](http://docs.oracle.com/database/121/REFRN/GUID-6C57EE11-BD06-4BB8-A0F7-D6CDDD086FA9.htm#REFRN10019)  |  Starts with 11\.0\.0  |  Starts with 10\.0\.0  | 
|  [db\_securefile](http://docs.oracle.com/database/121/REFRN/GUID-6F7C5E21-3929-4AB1-9C72-1BB9BDDB011F.htm#REFRN10290)  |  PERMITTED | PREFERRED | ALWAYS | IGNORE | FORCE  |  PERMITTED | ALWAYS | IGNORE | FORCE  | 
|  [db\_writer\_processes](http://docs.oracle.com/database/121/REFRN/GUID-75774634-3B5E-49F8-A5C5-65923F596845.htm#REFRN10043)  |  1\-100  |  1\-36  | 
|  [optimizer\_features\_enable](http://docs.oracle.com/database/121/REFRN/GUID-E193EC9E-B642-4C01-99EC-24E04AEA1A2C.htm#REFRN10141)  |  8\.0\.0 to 12\.1\.0\.2  |  8\.0\.0 to 11\.2\.0\.4  | 
|  [parallel\_degree\_policy](http://docs.oracle.com/database/121/REFRN/GUID-BF09265F-8545-40D4-BD29-E58D5F02B0E5.htm#REFRN10310)  |  MANUAL,LIMITED,AUTO,ADAPTIVE  |  MANUAL,LIMITED,AUTO  | 
|  [parallel\_min\_server](http://docs.oracle.com/database/121/REFRN/GUID-1D7EC131-7B5B-40E5-A0F8-ABC7B4C5B0E8.htm#REFRN10160)  |  0 to parallel\_max\_servers  |  CPU\_COUNT \* PARALLEL\_THREADS\_PER\_CPU \* 2 to parallel\_max\_servers  | 

One parameters has a new default value for Oracle 12c on Amazon RDS\. The following table shows the new default value: 


****  

| Parameter Name | Oracle 12c Default Value | Oracle 11g Default Value | 
| --- | --- | --- | 
|  job\_queue\_processes  |  50  |  1000  | 

Parameters in Amazon RDS are managed using parameter groups\. See [Working with DB Parameter Groups](USER_WorkingWithParamGroups.md) for more information\. To view the supported parameters for a specific Oracle edition and version, you can run the AWS CLI [http://docs.aws.amazon.com/cli/latest/reference/rds/describe-engine-default-parameters.html](http://docs.aws.amazon.com/cli/latest/reference/rds/describe-engine-default-parameters.html) command\.

For example, to view the supported parameters for Oracle Enterprise Edition, version 12c, run the following command:

```
aws rds describe-engine-default-parameters --db-parameter-group-family oracle-ee-12.1                        
```

### Amazon RDS System Privileges for Oracle 12c<a name="Oracle.Concepts.FeatureSupport.12c.Privileges"></a>

Several new system privileges have been granted to the system account for Oracle 12c\. These new system privileges include:

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

### Amazon RDS Options for Oracle 12c<a name="Oracle.Concepts.FeatureSupport.12c.Options"></a>

Several Oracle options changed between Oracle 11g and Oracle 12c, though most of the options remain the same between the two versions\. The Oracle 12c changes include the following: 

+ Oracle Enterprise Manager Database Express 12c replaced Oracle Enterprise Manager 11g Database Control\. For more information, see [Oracle Enterprise Manager Database Express](Appendix.Oracle.Options.OEM_DBControl.md)\. 

+ The option XMLDB is installed by default in Oracle 12c\. You no longer need to install this option yourself\. 

### Amazon RDS PL/SQL Packages for Oracle 12c<a name="Oracle.Concepts.FeatureSupport.12c.Packages"></a>

Oracle 12c includes a number of new built\-in PL/SQL packages\. The packages included with Amazon RDS Oracle 12c include the following: 


****  

| Package Name | Description | 
| --- | --- | 
|  [CTX\_ANL](http://docs.oracle.com/cd/E16655_01/appdev.121/e17602/c_anl.htm)  | The CTX\_ANL package is used with AUTO\_LEXER and provides procedures for adding and dropping a custom dictionary from the lexer\.  | 
|  [DBMS\_APP\_CONT](http://docs.oracle.com/cd/E16655_01/appdev.121/e17602/d_app_cont.htm)  | The DBMS\_APP\_CONT package provides an interface to determine if the in\-flight transaction on a now unavailable session committed or not, and if the last call on that session completed or not\.  | 
|  [DBMS\_AUTO\_REPORT](http://docs.oracle.com/cd/E16655_01/appdev.121/e17602/d_auto_report.htm#sthref1284)  | The DBMS\_AUTO\_REPORT package provides an interface to view SQL Monitoring and Real\-time Automatic Database Diagnostic Monitor \(ADDM\) data that has been captured into Automatic Workload Repository \(AWR\)\.  | 
|  [DBMS\_GOLDENGATE\_AUTH](http://docs.oracle.com/cd/E16655_01/appdev.121/e17602/d_goldengate_auth.htm)  | The DBMS\_GOLDENGATE\_AUTH package provides subprograms for granting privileges to and revoking privileges from GoldenGate administrators\.  | 
|  [DBMS\_HEAT\_MAP](http://docs.oracle.com/cd/E16655_01/appdev.121/e17602/d_heat_map.htm)  | The DBMS\_HEAT\_MAP package provides an interface to externalize heatmaps at various levels of storage including block, extent, segment, object and tablespace\.  | 
|  [DBMS\_ILM](http://docs.oracle.com/cd/E16655_01/appdev.121/e17602/d_ilm.htm)  | The DBMS\_ILM package provides an interface for implementing Information Lifecycle Management \(ILM\) strategies using Automatic Data Optimization \(ADO\) policies\.  | 
|  [DBMS\_ILM\_ADMIN](http://docs.oracle.com/cd/E16655_01/appdev.121/e17602/d_ilm_admin.htm)  | The DBMS\_ILM\_ADMIN package provides an interface to customize Automatic Data Optimization \(ADO\) policy execution\.  | 
|  [DBMS\_PART](http://docs.oracle.com/cd/E16655_01/appdev.121/e17602/d_part.htm)  | The DBMS\_PART package provides an interface for maintenance and management operations on partitioned objects\.  | 
|  [DBMS\_PRIVILEGE\_CAPTURE](http://docs.oracle.com/cd/E16655_01/appdev.121/e17602/d_priv_prof.htm)  | The DBMS\_PRIVILEGE\_CAPTURE package provides an interface to database privilege analysis\.  | 
|  [DBMS\_QOPATCH](http://docs.oracle.com/cd/E16655_01/appdev.121/e17602/d_qopatch.htm)  | The DBMS\_QOPATCH package provides an interface to view the installed database patches\.  | 
|  [DBMS\_REDACT](http://docs.oracle.com/cd/E16655_01/appdev.121/e17602/d_redact.htm)  | The DBMS\_REDACT package provides an interface to Oracle Data Redaction, which enables you to mask \(redact\) data that is returned from queries issued by low\-privileged users or an application\.  | 
|  [DBMS\_SPD](http://docs.oracle.com/cd/E16655_01/appdev.121/e17602/d_spd.htm)  | The DBMS\_SPD package provides subprograms for managing SQL plan directives \(SPD\)\.  | 
|  [DBMS\_SQL\_TRANSLATOR](http://docs.oracle.com/cd/E16655_01/appdev.121/e17602/d_sql_trans.htm)  | The DBMS\_SQL\_TRANSLATOR package provides an interface for creating, configuring, and using SQL translation profiles\.  | 
|  [DBMS\_SQL\_MONITOR](http://docs.oracle.com/cd/E16655_01/appdev.121/e17602/d_sql_monitor.htm)  | The DBMS\_SQL\_MONITOR package provides information about real\-time SQL Monitoring and real\-time Database Operation Monitoring\.  | 
|  [DBMS\_SYNC\_REFRESH](http://docs.oracle.com/cd/E16655_01/appdev.121/e17602/d_sync_refresh.htm)  | The DBMS\_SYNC\_REFRESH package provides an interface to perform a synchronous refresh of materialized views\.  | 
|  [DBMS\_TSDP\_MANAGE](http://docs.oracle.com/cd/E16655_01/appdev.121/e17602/d_tsdp_manage.htm)  | The DBMS\_TSDP\_MANAGE package provides an interface to import and manage sensitive columns and sensitive column types in the database, and is used in conjunction with the [DBMS\_TSDP\_PROTECT](http://docs.oracle.com/database/121/ARPLS/d_tsdp_protect.htm#BHAFHBHI) package with regard to transparent sensitive data protection \(TSDP\) policies\. DBMS\_TSDP\_MANAGE is available with the Enterprise Edition only\.  | 
|  [DBMS\_TSDP\_PROTECT](http://docs.oracle.com/cd/E16655_01/appdev.121/e17602/d_tsdp_protect.htm)  | The DBMS\_TSDP\_PROTECT package provides an interface to configure transparent sensitive data protection \(TSDP\) policies in conjunction with the [DBMS\_TSDP\_MANAGE](http://docs.oracle.com/database/121/ARPLS/d_tsdp_manage.htm#CACCBHBC) package\. DBMS\_TSDP\_PROTECT is available with the Enterprise Edition only\.  | 
|  [DBMS\_XDB\_CONFIG](http://docs.oracle.com/database/121/ARPLS/d_xdb_config.htm#ARPLS73564)  | The DBMS\_XDB\_CONFIG package provides an interface for configuring Oracle XML DB and its repository\.  | 
| [DBMS\_XDB\_CONSTANTS](http://docs.oracle.com/database/121/ARPLS/d_xdb_constants.htm#ARPLS73572) | The DBMS\_XDB\_CONSTANTS package provides an interface to commonly used constants\. Users should use constants instead of dynamic strings to avoid typographical errors\.  | 
| [DBMS\_XDB\_REPOS](http://docs.oracle.com/database/121/ARPLS/d_xdb_repos.htm#ARPLS74188) | The DBMS\_XDB\_REPOS package provides an interface to operate on the Oracle XML database Repository\.  | 
|  [DBMS\_XMLSCHEMA\_ANNOTATE](http://docs.oracle.com/database/121/ARPLS/d_xmlschema_annotate.htm#ARPLS73580)  | The DBMS\_XMLSCHEMA\_ANNOTATE package provides an interface to manage and configure the structured storage model, mainly through the use of pre\-registration schema annotations\.  | 
|  [DBMS\_XMLSTORAGE\_MANAGE](http://docs.oracle.com/database/121/ARPLS/d_xmlstorage_man.htm#ARPLS73588)  | The DBMS\_XMLSTORAGE\_MANAGE package provides an interface to manage and modify XML storage after schema registration has been completed\.  | 
|  [DBMS\_XSTREAM\_ADM](http://docs.oracle.com/cd/E16655_01/appdev.121/e17602/d_xstrm_adm.htm)  | The DBMS\_XSTREAM\_ADM package provides interfaces for streaming database changes between an Oracle database and other systems\. XStream enables applications to stream out or stream in database changes\.  | 
|  [DBMS\_XSTREAM\_AUTH](http://docs.oracle.com/cd/E16655_01/appdev.121/e17602/d_xstrm_auth.htm)  | The DBMS\_XSTREAM\_AUTH package provides subprograms for granting privileges to and revoking privileges from XStream administrators\.  | 
|  [UTL\_CALL\_STACK](http://docs.oracle.com/cd/E16655_01/appdev.121/e17602/u_call_stack.htm)  | The UTL\_CALL\_STACK package provides an interface to provide information about currently executing subprograms\.  | 

### Oracle 12c Features Not Supported<a name="Oracle.Concepts.FeatureSupport.12c.NotSupported"></a>

The following features are not supported for Oracle 12c on Amazon RDS:

+ Automated Storage Management

+ Data Guard / Active Data Guard

+ Database Vault

+ Java Support

+ Multitenant Database

+ Real Application Clusters \(RAC\)

+ Unified Auditing

Several Oracle 11g PL/SQL packages are not supported in Oracle 12c\. These packages include:

+ DBMS\_AUTO\_TASK\_IMMEDIATE

+ DBMS\_CDC\_PUBLISH

+ DBMS\_CDC\_SUBSCRIBE

+ DBMS\_EXPFIL

+ DBMS\_OBFUSCATION\_TOOLKIT

+ DBMS\_RLMGR

+ SDO\_NET\_MEM

## Oracle 11g with Amazon RDS<a name="Oracle.Concepts.FeatureSupport.11g"></a>

### Oracle 11g Supported Features<a name="Oracle.Concepts.FeatureSupport.11g.Supported"></a>

The following list shows the Oracle 11g features supported by Amazon RDS\. For a complete list of features supported by each Oracle 11g edition, see [Oracle Database 11g Editions](http://www.oracle.com/technetwork/community/database-11g-product-family-technic-133664.pdf)\. 

+ Total Recall

+ Flashback Table, Query and Transaction Query

+ Virtual Private Database

+ Fine\-Grained Auditing

+ Comprehensive support for Microsoft \.NET, OLE DB, and ODBC

+ Automatic Memory Management

+ Automatic Undo Management

+ Advanced Compression

+ Partitioning

+ Star Query Optimization

+ Summary Management \- Materialized View Query Rewrite

+ Oracle Data Redaction

+ Distributed Queries/Transactions

+ Text

+ Materialized Views

+ Import/Export and sqlldr Support

+ Oracle Enterprise Manager Database Control

+ Oracle XML DB \(without the XML DB Protocol Server\)

+ Oracle Application Express

+ Automatic Workload Repository for Enterprise Edition \(AWR\)\. For more information, see [Working with Automatic Workload Repository \(AWR\)](Appendix.Oracle.CommonDBATasks.Database.md#Appendix.Oracle.CommonDBATasks.AWR)

+ Datapump \(network only\)

+ Native network encryption 

+ Transparent data encryption \(Oracle TDE\), part of the Oracle Advanced Security feature 

### Oracle 11g Features Not Supported<a name="Oracle.Concepts.FeatureSupport.11g.Unsupported"></a>

The following features are not supported for Oracle 11g on Amazon RDS:

+ Real Application Clusters \(RAC\)

+ Real Application Testing

+ Data Guard / Active Data Guard

+ Oracle Enterprise Manager Grid Control

+ Automated Storage Management

+ Database Vault

+ Streams

+ Java Support

+ Oracle Label Security

+ Oracle XML DB Protocol Server

### Amazon RDS Parameters for Oracle 11g<a name="Oracle.Concepts.FeatureSupport.11g.Parameters"></a>

Parameters in Amazon RDS are managed using parameter groups\. See [Working with DB Parameter Groups](USER_WorkingWithParamGroups.md) for more information\. To view the supported parameters for a specific Oracle edition and version, you can run the AWS CLI [http://docs.aws.amazon.com/cli/latest/reference/rds/describe-engine-default-parameters.html](http://docs.aws.amazon.com/cli/latest/reference/rds/describe-engine-default-parameters.html) command\.

For example, to view the supported parameters for Oracle Enterprise Edition, version 11g, run the following command:

```
aws rds describe-engine-default-parameters --db-parameter-group-family oracle-ee-11.2                        
```

## Oracle Engine Version Management<a name="Oracle.Concepts.Patching"></a>

DB Engine Version Management is a feature of Amazon RDS that enables you to control when and how the database engine software running your DB instances is patched and upgraded\. This feature gives you the flexibility to maintain compatibility with database engine patch versions, test new patch versions to ensure they work effectively with your application before deploying in production, and perform version upgrades on your own terms and timelines\. 

**Note**  
Amazon RDS periodically aggregates official Oracle database patches using an Amazon RDS\-specific DB Engine version\. To see a list of which Oracle patches are contained in an Amazon RDS Oracle\-specific engine version, go to [Appendix: Oracle Database Engine Release Notes](Appendix.Oracle.PatchComposition.md)\. 

Currently, you perform all Oracle database upgrades manually\. For more information about upgrading an Oracle DB instance, see [Upgrading the Oracle DB Engine](USER_UpgradeDBInstance.Oracle.md)\. 

### Deprecation of Oracle 11\.2\.0\.2<a name="Oracle.Concepts.Deprecate.11202"></a>

In 2017, Amazon RDS is deprecating support for Oracle version 11\.2\.0\.2\. Oracle is no longer providing patches for this version\. Therefore, to provide the best experience for AWS customers, we are deprecating this version\. 

There are no longer any production DB instances running Oracle version 11\.2\.0\.2\. You might still have a snapshot of an 11\.2\.0\.2 DB instance\. 

Amazon RDS is deprecating support for Oracle version 11\.2\.0\.2 according to the following schedule\. 


****  

| Date | Information | 
| --- | --- | 
|  August 4, 2016  |  You can no longer create DB instances that use Oracle version 11\.2\.0\.2   | 
|  April 15, 2018  |  Any 11\.2\.0\.2 snapshots are upgraded to 11\.2\.0\.4\.  You can upgrade your snapshots yourself prior to this date\. For more information, see [Upgrading an Oracle DB Snapshot](USER_UpgradeDBSnapshot.Oracle.md)\.   | 

### Deprecation of Oracle 11\.2\.0\.3<a name="Oracle.Concepts.Deprecate.11203"></a>

In 2017, Amazon RDS is deprecating support for Oracle version 11\.2\.0\.3\. Oracle is no longer providing patches for this version\. Therefore, to provide the best experience for AWS customers, we are deprecating this version\. 

There are no longer any production DB instances running Oracle version 11\.2\.0\.3\. You might still have a snapshot of an 11\.2\.0\.3 DB instance\.   

Amazon RDS is deprecating support for Oracle version 11\.2\.0\.3 according to the following schedule\. 


****  

| Date | Information | 
| --- | --- | 
|  August 4, 2016  |  You can no longer create DB instances that use Oracle version 11\.2\.0\.3\.   | 
|  March 15, 2018  |  Any 11\.2\.0\.3 snapshots are upgraded to 11\.2\.0\.4\.  You can upgrade your snapshots yourself prior to this date\. For more information, see [Upgrading an Oracle DB Snapshot](USER_UpgradeDBSnapshot.Oracle.md)\.   | 

### Deprecation of Oracle 12\.1\.0\.1<a name="Oracle.Concepts.Deprecate.12101"></a>

In 2017, Amazon RDS is deprecating support for Oracle version 12\.1\.0\.1\. Oracle is no longer providing patches for this version\. Therefore, to provide the best experience for AWS customers, we are deprecating this version\. 

There are no longer any production DB instances running Oracle version 12\.1\.0\.1\. You might still have a snapshot of an 12\.1\.0\.1 DB instance\. 

Amazon RDS will deprecate support for Oracle version 12\.1\.0\.1 according to the following schedule\. 


****  

| Date | Information | 
| --- | --- | 
|  February 15, 2017  |  You can no longer create DB instances that use Oracle version 12\.1\.0\.1\.   | 
|  June 1, 2018  |  Any 12\.1\.0\.1 snapshots are upgraded to 12\.1\.0\.2\.  You can upgrade your snapshots yourself prior to this date\. For more information, see [Upgrading an Oracle DB Snapshot](USER_UpgradeDBSnapshot.Oracle.md)\.   | 

## Using Huge Pages with an Oracle DB Instance<a name="Oracle.Concepts.HugePages"></a>

Amazon RDS for Oracle supports Linux kernel huge pages for increased database scalability\. The use of huge pages results in smaller page tables and less CPU time spent on memory management, increasing the performance of large database instances\. For more information, see [Overview of HugePages](https://docs.oracle.com/database/121/UNXAR/appi_vlm.htm#UNXAR400) in the Oracle documentation\. 

You can use huge pages with the following versions and editions of Oracle: 

+ 12\.1\.0\.2, all editions

+ 11\.2\.0\.4, all editions

 The `use_large_pages` parameter controls whether huge pages are enabled for a DB instance\. The possible settings for this parameter are `ONLY`, `FALSE`, and `{DBInstanceClassHugePagesDefault}`\. The `use_large_pages` parameter is set to `{DBInstanceClassHugePagesDefault}` in the default DB parameter group for Oracle\. 

To control whether huge pages are enabled for a DB instance automatically, you can use the `DBInstanceClassHugePagesDefault` formula variable in parameter groups\. The value is determined as follows:

+ For the current generation DB instance classes \(db\.t2, db\.r3, and db\.m4\), `DBInstanceClassHugePagesDefault` always evaluates to `FALSE` by default\. You can enable huge pages manually if the instance class has at least 14 GiB of memory\.

+ For next generation instance classes, such as db\.r4, if the instance class has less than 100 GiB of memory, `DBInstanceClassHugePagesDefault` evaluates to `ONLY` by default\.

+ For next generation instance classes, such as db\.r4, if the instance class has at least 100 GiB of memory, `DBInstanceClassHugePagesDefault` always evaluates to `ONLY`\.

Huge pages are not enabled by default for the following DB instance classes\. 


****  

| DB Instance Class Family | DB Instance Classes with Huge Pages Not Enabled by Default | 
| --- | --- | 
|  db\.m4  |  db\.m4\.large, db\.m4\.xlarge, db\.m4\.2xlarge, db\.m4\.4xlarge, db\.m4\.10xlarge  | 
|  db\.m3  |  db\.m3\.medium, db\.m3\.large, db\.m3\.xlarge, db\.m3\.2xlarge  | 
|  db\.m2  |  db\.m2\.xlarge, db\.m2\.2xlarge, db\.m2\.4xlarge  | 
|  db\.m1  |  db\.m1\.small, db\.m1\.medium, db\.m1\.large, db\.m1\.xlarge  | 
|  db\.r3  |  db\.r3\.large, db\.r3\.xlarge, db\.r3\.2xlarge, db\.r3\.4xlarge, db\.r3\.8xlarge  | 
|  db\.t2  |  db\.t2\.micro, db\.t2\.small, db\.t2\.medium, db\.t2\.large  | 
|  db\.t1  |  db\.t1\.micro  | 

For more information about DB instance classes, see [Specifications for All Available DB Instance Classes](Concepts.DBInstanceClass.md#Concepts.DBInstanceClass.Summary)\. 

To enable huge pages for new or existing DB instances manually, set the `use_large_pages` parameter to `ONLY`\. You can't use huge pages with Oracle Automatic Memory Management \(AMM\)\. If you set the parameter `use_large_pages` to `ONLY`, then you must also set both `memory_target` and `memory_max_target` to `0`\. For more information about setting DB parameters for your DB instance, see [Working with DB Parameter Groups](USER_WorkingWithParamGroups.md)\. 

You can also set the `sga_target`, `sga_max_size`, and `pga_aggregate_target` parameters\. When you set system global area \(SGA\) and program global area \(PGA\) memory parameters, add the values together\. Subtract this total from your available instance memory \(`DBInstanceClassMemory`\) to determine the free memory beyond the huge pages allocation\. You must leave free memory of at least 2 GiB, or 10 percent of the total available instance memory, whichever is smaller\. 

The following is a sample parameter configuration for huge pages that enables huge pages manually\. You should set the values to meet your needs\. 

```
1. memory_target            = 0
2. memory_max_target        = 0
3. pga_aggregate_target     = {DBInstanceClassMemory*1/8}
4. sga_target               = {DBInstanceClassMemory*3/4}
5. sga_max_size             = {DBInstanceClassMemory*3/4}
6. use_large_pages          = ONLY
```

Assume the following parameters values are set in a parameter group\.

```
1. memory_target            = IF({DBInstanceClassHugePagesDefault}, 0, {DBInstanceClassMemory*3/4})
2. memory_max_target        = IF({DBInstanceClassHugePagesDefault}, 0, {DBInstanceClassMemory*3/4})
3. pga_aggregate_target     = IF({DBInstanceClassHugePagesDefault}, {DBInstanceClassMemory*1/8}, 0)
4. sga_target               = IF({DBInstanceClassHugePagesDefault}, {DBInstanceClassMemory*3/4}, 0)
5. sga_max_size             = IF({DBInstanceClassHugePagesDefault}, {DBInstanceClassMemory*3/4}, 0)
6. use_large_pages          = {DBInstanceClassHugePagesDefault}
```

The parameter group is used by a next generation db\.r4 instance class with less than 100 GiB of memory and a current generation db\.r3 instance with more than 100 GiB memory\. With these parameter settings and `use_large_pages` set to `{DBInstanceClassHugePagesDefault}`, huge pages are enabled on the db\.r4 instance, but disabled on the db\.r3 instance\.

Consider another example with following parameters values set in a parameter group\.

```
1. memory_target           = IF({DBInstanceClassHugePagesDefault}, 0, {DBInstanceClassMemory*3/4})
2. memory_max_target       = IF({DBInstanceClassHugePagesDefault}, 0, {DBInstanceClassMemory*3/4})
3. pga_aggregate_target    = IF({DBInstanceClassHugePagesDefault}, {DBInstanceClassMemory*1/8}, 0)
4. sga_target              = IF({DBInstanceClassHugePagesDefault}, {DBInstanceClassMemory*3/4}, 0)
5. sga_max_size            = IF({DBInstanceClassHugePagesDefault}, {DBInstanceClassMemory*3/4}, 0)
6. use_large_pages         = FALSE
```

The parameter group is used by a next generation db\.r4 instance class with less than 100 GiB of memory and a current generation db\.r3 instance with more than 100 GiB memory\. With these parameter settings, huge pages are disabled on both the db\.r4 instance and the db\.r3 instance\.

**Note**  
If this parameter group is used by a next generation db\.r4 instance class with at least 100 GiB of memory, the `FALSE` setting for `use_large_pages` is overridden and set to `ONLY`\. In this case, a customer notification regarding the override is sent\.

After you configure your parameters, you must reboot your DB instance for the changes to take effect\. For more information, see [Rebooting a DB Instance](USER_RebootInstance.md)\. 

After huge pages are active on your DB instance, you can view huge pages information by enabling enhanced monitoring\. For more information, see [Enhanced Monitoring](USER_Monitoring.OS.md)\. 

## Using utl\_http, utl\_tcp, and utl\_smtp with an Oracle DB Instance<a name="Oracle.Concepts.ONA"></a>

Amazon RDS supports outbound network access on your DB instances running Oracle\. You can use `utl_http`, `utl_tcp`, and `utl_smtp` to connect from your DB instance to the network\. 

Note the following about working with outbound network access:

+ To use `utl_http` on DB instances running Oracle 11g, you must install the XMLDB option\. For more information, see [Oracle XML DB](Appendix.Oracle.Options.XMLDB.md)\. 

+ Outbound network access with `utl_http`, `utl_tcp`, and `utl_smtp` is supported only for Oracle DB instances in a VPC\. To determine whether or not your DB instance is in a VPC, see [Determining Whether You Are Using the EC2\-VPC or EC2\-Classic Platform](USER_VPC.FindDefaultVPC.md)\. To move a DB instance not in a VPC into a VPC, see [Moving a DB Instance Not in a VPC into a VPC](USER_VPC.WorkingWithRDSInstanceinaVPC.md#USER_VPC.Non-VPC2VPC)\. 

+ To use SMTP with the UTL\_MAIL option, see [Oracle UTL\_MAIL](Oracle.Options.UTLMAIL.md)\.

+ The Domain Name Server \(DNS\) name of the remote host can be any of the following: 

  + Publicly resolvable\.

  + The endpoint of an Amazon RDS DB instance\.

  + Resolvable through a custom DNS server\. For more information, see [Setting Up a Custom DNS Server](Appendix.Oracle.CommonDBATasks.System.md#Appendix.Oracle.CommonDBATasks.CustomDNS)\. 

  + The private DNS name of an Amazon EC2 instance in the same VPC or a peered VPC\. In this case, make sure that the name is resolvable through a custom DNS server\. Alternatively, to use the DNS provided by Amazon, you can enable the `enableDnsSupport` attribute in the VPC settings and enable DNS resolution support for the VPC peering connection\. For more information, see [DNS Support in Your VPC](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/vpc-dns.html#vpc-dns-support) and [Modifying Your VPC Peering Connection](http://docs.aws.amazon.com/AmazonVPC/latest/PeeringGuide/working-with-vpc-peering.html#modify-peering-connections)\. 

## Using OEM, APEX, TDE, and Other Options<a name="Oracle.Concepts.General.Options"></a>

Most Amazon RDS DB engines support option groups that allow you to select additional features for your DB instance\. Oracle DB instances support several options, including Oracle Enterprise Manager \(OEM\), Transparent Data Encryption \(TDE\), Application Express \(APEX\), and Native Network Encryption\. For a complete list of supported Oracle options, see [Options for Oracle DB Instances](Appendix.Oracle.Options.md)\. For more information about working with option groups, see [Working with Option Groups](USER_WorkingWithOptionGroups.md)\. 