# Oracle on Amazon RDS<a name="CHAP_Oracle"></a>

Amazon RDS supports DB instances that run the following versions and editions of Oracle Database: 
+ Oracle 19c, Version 19\.0\.0\.0
+ Oracle 18c, Version 18\.0\.0\.0
+ Oracle 12c, Version 12\.2\.0\.1
+ Oracle 12c, Version 12\.1\.0\.2

**Note**  
Amazon RDS also currently supports Oracle 11g, Version 11\.2\.0\.4\. This version is on a deprecation path because Oracle will no longer provide patches for 11\.2\.0\.4 after the end\-of\-support date\. For more information, see [Deprecation of Oracle 11\.2\.0\.4](#Oracle.Concepts.Deprecate.11204)\.

You can create DB instances and DB snapshots, point\-in\-time restores, and automated or manual backups\. You can use DB instances running Oracle inside a VPC\. You can also add features to your Oracle DB instance by enabling various options\. Amazon RDS supports Multi\-AZ deployments for Oracle as a high\-availability, failover solution\. 

To deliver a managed service experience, Amazon RDS doesn't provide shell access to DB instances\. It also restricts access to certain system procedures and tables that require advanced privileges\. You can access databases on a DB instance using any standard SQL client application such as Oracle SQL\*Plus\. However, you can't access the host directly by using Telnet or Secure Shell \(SSH\)\. 

When you create a DB instance using your master account, the account gets DBA privileges, with some limitations\. Use this account for administrative tasks such as creating additional database accounts\. You can't use SYS, SYSTEM, or other Oracle\-supplied administrative accounts\. 

Before creating a DB instance, complete the steps in the [Setting Up for Amazon RDS](CHAP_SettingUp.md) section of this guide\. 

## Common Management Tasks for Oracle on Amazon RDS<a name="Oracle.Concepts.General"></a>

Following are the common management tasks you perform with an Amazon RDS Oracle DB instance, with links to relevant documentation for each task\. 


****  

| Task Area | Relevant Documentation | 
| --- | --- | 
|  **Instance Classes, Storage, and PIOPS** If you are creating a production instance, learn how instance classes, storage types, and Provisioned IOPS work in Amazon RDS\.   |  [DB Instance Class Support for Oracle](#Oracle.Concepts.InstanceClasses) [Amazon RDS Storage Types](CHAP_Storage.md#Concepts.Storage)   | 
|  **Multi\-AZ Deployments** A production DB instance should use Multi\-AZ deployments\. Multi\-AZ deployments provide increased availability, data durability, and fault tolerance for DB instances\.   |  [High Availability \(Multi\-AZ\) for Amazon RDS](Concepts.MultiAZ.md)  | 
|  **Amazon Virtual Private Cloud \(VPC\)** If your AWS account has a default VPC, then your DB instance is automatically created inside the default VPC\. If your account doesn't have a default VPC, and you want the DB instance in a VPC, create the VPC and subnet groups before you create the instance\.   |  [Determining Whether You Are Using the EC2\-VPC or EC2\-Classic Platform](USER_VPC.FindDefaultVPC.md) [Working with a DB Instance in a VPC](USER_VPC.WorkingWithRDSInstanceinaVPC.md)  | 
|  **Security Groups** By default, DB instances use a firewall that prevents access\. Make sure you create a security group with the correct IP addresses and network configuration to access the DB instance\. The security group you create depends on which Amazon EC2 platform your DB instance is on, and whether you will access your DB instance from an Amazon EC2 instance\.  In general, if your DB instance is on the EC2\-Classic platform, you should create a DB security group\. Also, if your instance is on the EC2\-VPC platform, you should create a VPC security group\.   |  [Determining Whether You Are Using the EC2\-VPC or EC2\-Classic Platform](USER_VPC.FindDefaultVPC.md) [Controlling Access with Security Groups](Overview.RDSSecurityGroups.md)   | 
|  **Parameter Groups** If your DB instance is going to require specific database parameters, you should create a parameter group before you create the DB instance\.   |  [Working with DB Parameter Groups](USER_WorkingWithParamGroups.md)  | 
|  **Option Groups** If your DB instance will require specific database options, you should create an option group before you create the DB instance\.   |  [Options for Oracle DB Instances](Appendix.Oracle.Options.md)  | 
|  **Connecting to Your DB Instance** After creating a security group and associating it to a DB instance, you can connect to the DB instance using any standard SQL client application such as Oracle SQL\*Plus\.   |  [Connecting to a DB Instance Running the Oracle Database Engine](USER_ConnectToOracleInstance.md)  | 
|  **Backup and Restore** You can configure your DB instance to take automated backups, or take manual snapshots, and then restore instances from the backups or snapshots\.   |  [Backing Up and Restoring an Amazon RDS DB Instance](CHAP_CommonTasks.BackupRestore.md)  | 
|  **Monitoring** You can monitor an Oracle DB instance by using CloudWatch Amazon RDS metrics, events, and enhanced monitoring\.   |  [Viewing DB Instance Metrics](MonitoringOverview.md#USER_Monitoring) [Viewing Amazon RDS Events](USER_ListEvents.md)  | 
|  **Log Files** You can access the log files for your Oracle DB instance\.   |  [Amazon RDS Database Log Files](USER_LogAccess.md)  | 

There are also advanced tasks and optional features for working with Oracle DB instances\. For more information, see the following documentation\.
+ For information on common DBA tasks for Oracle on Amazon RDS, see [Common DBA Tasks for Oracle DB Instances](Appendix.Oracle.CommonDBATasks.md)\.
+ For information on Oracle GoldenGate support, see [Using Oracle GoldenGate with Amazon RDS](Appendix.OracleGoldenGate.md)\. 
+ For information on Siebel Customer Relationship Management \(CRM\) support, see [Installing a Siebel Database on Oracle on Amazon RDS](Oracle.Resources.Siebel.md)\. 

## Oracle Licensing<a name="Oracle.Concepts.Licensing"></a>

Amazon RDS for Oracle has two licensing options: License Included \(LI\) and Bring Your Own License \(BYOL\)\. After you create an Oracle DB instance on Amazon RDS, you can change the licensing model by modifying the DB instance\. For more information, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md)\. 

### License Included<a name="Oracle.Concepts.Licensing.LicenseIncluded"></a>

In the License Included model, you don't need to purchase Oracle licenses separately\. AWS holds the license for the Oracle database software\. In this model, if you have an AWS Support account with case support, contact AWS Support for both Amazon RDS and Oracle Database service requests\. 

The License Included model is supported on Amazon RDS for the following Oracle database editions: 
+ Oracle Database Standard Edition One \(SE1\)
+ Oracle Database Standard Edition Two \(SE2\)

**Note**  
The Oracle Database SE1 License Included model isn't supported in the following opt\-in AWS Regions:  
Africa \(Cape Town\)
Asia Pacific \(Hong Kong\)
Europe \(Milan\)
Middle East \(Bahrain\)

### Bring Your Own License \(BYOL\)<a name="Oracle.Concepts.Licensing.BYOL"></a>

In the BYOL model, you can use your existing Oracle Database licenses to run Oracle deployments on Amazon RDS\. You must have the appropriate Oracle Database license \(with Software Update License and Support\) for the DB instance class and Oracle Database edition you wish to run\. You must also follow Oracle's policies for licensing Oracle Database software in the cloud computing environment\. For more information on Oracle's licensing policy for Amazon EC2, see [ Licensing Oracle Software in the Cloud Computing Environment](http://www.oracle.com/us/corporate/pricing/cloud-licensing-070579.pdf)\. 

In this model, you continue to use your active Oracle support account, and you contact Oracle directly for Oracle Database service requests\. If you have an AWS Support account with case support, you can contact AWS Support for Amazon RDS issues\. Amazon Web Services and Oracle have a multi\-vendor support process for cases that require assistance from both organizations\. 

The Bring Your Own License model is supported on Amazon RDS for the following Oracle database editions:
+ Oracle Database Enterprise Edition \(EE\)
+ Oracle Database Standard Edition \(SE\)
+ Oracle Database Standard Edition One \(SE1\)
+ Oracle Database Standard Edition Two \(SE2\)

#### Integrating with AWS License Manager<a name="oracle-lms-integration"></a>

To make it easier to monitor Oracle license usage in the BYOL model, [ AWS License Manager](https://aws.amazon.com/license-manager/) integrates with Amazon RDS for Oracle\. License Manager supports tracking of RDS for Oracle engine editions and licensing packs based on virtual cores \(vCPUs\)\. You can also use License Manager with AWS Organizations to manage all of your organizational accounts centrally\.

**Note**  
RDS for Oracle integration with License Manager isn't supported in the Asia Pacific \(Osaka\-Local\) Region\.

The following table shows the product information filters for RDS for Oracle\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Oracle.html)

To track license usage of your Oracle DB instances, you can create a license configuration\. In this case, RDS for Oracle resources that match the product information filter are automatically associated with the license configuration\. Discovery of Oracle DB instances can take up to 24 hours\.

##### Console<a name="oracle-lms-integration.console"></a>

**To create a license configuration to track the license usage of your Oracle DB instances**

1. Go to [https://console\.aws\.amazon\.com/license\-manager/](https://console.aws.amazon.com/license-manager/)\.

1. Create a license configuration\.

   For instructions, see [Create a License Configuration](https://docs.aws.amazon.com/license-manager/latest/userguide/create-license-configuration.html) in the *AWS License Manager User Guide*\.

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

For more information about product information, see [Automated Discovery of Resource Inventory](https://docs.aws.amazon.com/license-manager/latest/userguide/automated-discovery.html) in the *AWS License Manager User Guide*\.

For more information about the `--cli-input` parameter, see [Generating AWS CLI Skeleton and Input Parameters from a JSON or YAML Input File](https://docs.aws.amazon.com/cli/latest/userguide/cli-usage-skeleton.html) in the *AWS CLI User Guide*\.

### Licensing Oracle Multi\-AZ Deployments<a name="Oracle.Concepts.Licensing.MAZ"></a>

Amazon RDS supports Multi\-AZ deployments for Oracle as a high\-availability, failover solution\. We recommend Multi\-AZ for production workloads\. For more information, see [High Availability \(Multi\-AZ\) for Amazon RDS](Concepts.MultiAZ.md)\. 

If you use the Bring Your Own License model, you must have a license for both the primary DB instance and the standby DB instance in a Multi\-AZ deployment\. 

## Migrating Between Oracle Editions<a name="Oracle.Concepts.EditionsMigrating"></a>

For the BYOL model, you can migrate from any Standard Edition \(SE, SE1, or SE2\) to Enterprise Edition \(EE\), assuming you have an unused Oracle license appropriate for the edition and class of DB instance that you plan to run\. You can't migrate from Enterprise Edition to other editions\.

**To change the edition and retain your data**

1. Create a snapshot of the DB instance\.

   For more information, see [Creating a DB Snapshot](USER_CreateSnapshot.md)\.

1. Restore the snapshot to a new DB instance, and select the Oracle database edition you want to use\.

   For more information, see [Restoring from a DB Snapshot](USER_RestoreFromSnapshot.md)\.

1. \(Optional\) Delete the old DB instance, unless you want to keep it running and have the appropriate Oracle Database licenses for it\.

   For more information, see [Deleting a DB Instance](USER_DeleteInstance.md)\.

## DB Instance Class Support for Oracle<a name="Oracle.Concepts.InstanceClasses"></a>

The computation and memory capacity of a DB instance is determined by its DB instance class\. The DB instance class you need depends on your processing power and memory requirements\. For more information, see [DB Instance Classes](Concepts.DBInstanceClass.md)\. 

The following are the DB instance classes supported for Oracle\. 


****  

| Oracle Edition | 19c, 18c, and 12c Version 12\.2\.0\.1 Support | 12c Version 12\.1\.0\.2 Support | 11g Version 11\.2\.0\.4 Support | 
| --- | --- | --- | --- | 
|  Enterprise Edition \(EE\) Bring Your Own License \(BYOL\)  |  db\.m5\.large–db\.m5\.24xlarge db\.m4\.large–db\.m4\.16xlarge db\.z1d\.large–db\.z1d\.12xlarge db\.x1e\.xlarge–db\.x1e\.32xlarge db\.x1\.16xlarge–db\.x1\.32xlarge db\.r5\.large–db\.r5\.24xlarge db\.r4\.large–db\.r4\.16xlarge db\.t3\.small–db\.t3\.2xlarge  |  db\.m5\.large–db\.m5\.24xlarge db\.m4\.large–db\.m4\.16xlarge db\.z1d\.large–db\.z1d\.12xlarge db\.x1e\.xlarge–db\.x1e\.32xlarge db\.x1\.16xlarge–db\.x1\.32xlarge db\.r5\.large–db\.r5\.24xlarge db\.r4\.large–db\.r4\.16xlarge db\.t3\.micro–db\.t3\.2xlarge  |  db\.m5\.large–db\.m5\.24xlarge db\.m4\.large–db\.m4\.16xlarge db\.z1d\.large–db\.z1d\.12xlarge db\.x1e\.xlarge–db\.x1e\.32xlarge db\.x1\.16xlarge–db\.x1\.32xlarge db\.r5\.large–db\.r5\.24xlarge db\.r4\.large–db\.r4\.16xlarge db\.t3\.micro–db\.t3\.2xlarge  | 
|  Standard Edition 2 \(SE2\) Bring Your Own License \(BYOL\)  |  db\.m5\.large–db\.m5\.4xlarge db\.m4\.large–db\.m4\.4xlarge db\.z1d\.large–db\.z1d\.3xlarge db\.x1e\.xlarge–db\.x1e\.4xlarge db\.r5\.large–db\.r5\.4xlarge db\.r4\.large–db\.r4\.4xlarge db\.t3\.small–db\.t3\.2xlarge  |  db\.m5\.large–db\.m5\.4xlarge db\.m4\.large–db\.m4\.4xlarge db\.z1d\.large–db\.z1d\.3xlarge db\.x1e\.xlarge–db\.x1e\.4xlarge db\.r5\.large–db\.r5\.4xlarge db\.r4\.large–db\.r4\.4xlarge db\.t3\.micro–db\.t3\.2xlarge  |  —  | 
|  Standard Edition 2 \(SE2\) License Included  |  db\.m5\.large–db\.m5\.4xlarge db\.m4\.large–db\.m4\.4xlarge db\.r5\.large–db\.r5\.4xlarge db\.r4\.large–db\.r4\.4xlarge db\.t3\.small–db\.t3\.2xlarge  |  db\.m5\.large–db\.m5\.4xlarge db\.m4\.large–db\.m4\.4xlarge db\.r5\.large–db\.r5\.4xlarge db\.r4\.large–db\.r4\.4xlarge db\.t3\.micro–db\.t3\.2xlarge  |  —  | 
|  Standard Edition 1 \(SE1\) Bring Your Own License \(BYOL\)  |  —  |  —  |  db\.m5\.large–db\.m5\.4xlarge db\.m4\.large–db\.m4\.4xlarge db\.z1d\.large–db\.z1d\.3xlarge db\.x1e\.xlarge–db\.x1e\.4xlarge db\.r5\.large–db\.r5\.4xlarge db\.r4\.large–db\.r4\.4xlarge db\.t3\.micro–db\.t3\.2xlarge  | 
|  Standard Edition 1 \(SE1\) License Included  |  —  |  —  |  db\.m5\.large–db\.m5\.4xlarge db\.m4\.large–db\.m4\.4xlarge db\.r5\.large–db\.r5\.4xlarge db\.t3\.micro–db\.t3\.2xlarge  | 
|  Standard Edition \(SE\) Bring Your Own License \(BYOL\)  |  —  |  —  |  db\.m5\.large–db\.m5\.8xlarge db\.m4\.large–db\.m4\.4xlarge db\.z1d\.large–db\.z1d\.6xlarge db\.x1e\.xlarge–db\.x1e\.8xlarge db\.r5\.large–db\.r5\.8xlarge db\.r4\.large–db\.r4\.8xlarge db\.t3\.micro–db\.t3\.2xlarge  | 

**Note**  
We encourage all bring\-your\-own\-license customers to consult their licensing agreement to assess the impact of Amazon RDS for Oracle deprecations\. For more information on the compute capacity of DB instance classes supported by Amazon RDS for Oracle, see [DB Instance Classes](Concepts.DBInstanceClass.md) and [Configuring the Processor for a DB Instance Class](Concepts.DBInstanceClass.md#USER_ConfigureProcessor)\.

### Deprecated db\.t2 DB Instance Classes for Oracle<a name="Oracle.Concepts.InstanceClasses.DeprecatedT2"></a>

The db\.t2 DB instance classes are deprecated for Amazon RDS for Oracle\. The db\.t2 DB instance classes have been replaced by the better performing db\.t3 DB instance classes that are generally available at a lower cost\. Starting on January 15, 2020, Amazon RDS for Oracle will automatically scale db\.t2 DB instances to comparable db\.t3 DB instance classes\.

If you have DB instances that use db\.t2 DB instance classes, Amazon RDS will modify each one automatically to use a comparable DB instance class that is not deprecated\. You can change the DB instance class for a DB instance yourself by modifying the DB instance\. For more information, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md)\.

If you have DB snapshots of DB instances that were using db\.t2 DB instance classes, you can choose a DB instance class that is not deprecated when you restore the DB snapshots\. For more information, see [Restoring from a DB Snapshot](USER_RestoreFromSnapshot.md)\.

**Note**  
The db\.t3 DB instance classes have hyper\-threading enabled by default\. When DB instances running the db\.t2 DB instance class are migrated, the number of vCPUs is set automatically to the default number of the comparable db\.t3 DB instance class\. To learn more about the vCPU management features available on Amazon RDS for Oracle, and the default settings for each db\.t3 DB instance class, see [Configuring the Processor for a DB Instance Class](Concepts.DBInstanceClass.md#USER_ConfigureProcessor)\.

### Deprecated db\.m3 and db\.r3 DB Instance Classes for Oracle<a name="Oracle.Concepts.InstanceClasses.DeprecatedM3R3"></a>

 The db\.m3 and db\.r3 DB instance classes are deprecated for Amazon RDS for Oracle\. These DB instance classes have been replaced by better performing DB instance classes that are generally available at a lower cost\. Starting on September 30, 2019, Amazon RDS for Oracle will automatically scale DB instances to DB instance classes that are not deprecated\. 

If you have DB instances that use db\.m3 and db\.r3 DB instance classes, Amazon RDS will modify each one automatically to use a comparable DB instance class that is not deprecated\. You can change the DB instance class for a DB instance yourself by modifying the DB instance\. For more information, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md)\. 

If you have DB snapshots of DB instances that were using db\.m3 or db\.r3 DB instance classes, you can choose a DB instance class that is not deprecated when you restore the DB snapshots\. For more information, see [Restoring from a DB Snapshot](USER_RestoreFromSnapshot.md)\. 

### Deprecated db\.m1 and db\.m2 DB Instance Classes for Oracle<a name="Oracle.Concepts.InstanceClasses.Deprecated"></a>

 The db\.m1 and db\.m2 DB instance classes are deprecated for Amazon RDS for Oracle\. These DB instance classes have been replaced by better performing DB instance classes that are generally available at a lower cost\. Starting on September 12, 2018, Amazon RDS for Oracle will automatically scale DB instances to DB instance classes that are not deprecated\. 

If you have DB instances that use db\.m1 and db\.m2 DB instance classes, Amazon RDS will modify each one automatically to use a comparable DB instance class that is not deprecated\. You can change the DB instance class for a DB instance yourself by modifying the DB instance\. For more information, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md)\. 

If you have DB snapshots of DB instances that were using db\.m1 or db\.m2 DB instance classes, you can choose a DB instance class that is not deprecated when you restore the DB snapshots\. For more information, see [Restoring from a DB Snapshot](USER_RestoreFromSnapshot.md)\. 

## Oracle File Size Limits in Amazon RDS<a name="Oracle.Concepts.file-size-limits"></a>

The maximum file size on Amazon RDS Oracle DB instances is 16 TiB\. If you try to resize a data file in a bigfile tablespace to a value over the limit, you receive an error such as the following\.

```
ORA-01237: cannot extend datafile 6
ORA-01110: data file 6: '/rdsdbdata/db/mydir/datafile/myfile.dbf'
ORA-27059: could not reduce file size
Linux-x86_64 Error: 27: File too large
Additional information: 2
```

## Oracle Security<a name="Oracle.Concepts.RestrictedDBAPrivileges"></a>

The Oracle database engine uses role\-based security\. A role is a collection of privileges that can be granted to or revoked from a user\. A predefined role, named `DBA`, normally allows all administrative privileges on an Oracle database\. The following privileges are not available for the DBA role on an Amazon RDS DB instance using the Oracle engine: 
+ `ALTER DATABASE`
+ `ALTER SYSTEM`
+ `CREATE ANY DIRECTORY`
+ `DROP ANY DIRECTORY`
+ `GRANT ANY PRIVILEGE`
+ `GRANT ANY ROLE`

When you create a DB instance, the master user account that you use to create the instance gets DBA privileges \(with some limitations\)\. Use the master user account for any administrative tasks such as creating additional user accounts in the database\. You can't use the `SYS` user, `SYSTEM` user, and other Oracle\-supplied administrative accounts\. 

Amazon RDS Oracle supports SSL/TLS encrypted connections and also the Oracle Native Network Encryption \(NNE\) option to encrypt connections between your application and your Oracle DB instance\. For more information about using SSL with Oracle on Amazon RDS, see [Using SSL with an Oracle DB Instance](#Oracle.Concepts.SSL)\. For more information about the Oracle Native Network Encryption option, see [Oracle Native Network Encryption](Appendix.Oracle.Options.NetworkEncryption.md)\. 

## Using SSL with an Oracle DB Instance<a name="Oracle.Concepts.SSL"></a>

Secure Sockets Layer \(SSL\) is an industry\-standard protocol for securing network connections between client and server\. After SSL version 3\.0, the name was changed to Transport Layer Security \(TLS\), but it is still often referred to as SSL and we refer to the protocol as SSL\. Amazon RDS supports SSL encryption for Oracle DB instances\. Using SSL, you can encrypt a connection between your application client and your Oracle DB instance\. SSL support is available in all AWS regions for Oracle\. 

You enable SSL encryption for an Oracle DB instance by adding the Oracle SSL option to the option group associated with the DB instance\. Amazon RDS uses a second port, as required by Oracle, for SSL connections\. Doing this allows both clear text and SSL\-encrypted communication to occur at the same time between a DB instance and an Oracle client\. For example, you can use the port with clear text communication to communicate with other resources inside a VPC while using the port with SSL\-encrypted communication to communicate with resources outside the VPC\. 

For more information, see [Oracle Secure Sockets Layer](Appendix.Oracle.Options.SSL.md)\. 

**Note**  
You can't use both SSL and Oracle native network encryption \(NNE\) on the same DB instance\. Before you can use SSL encryption, you must disable any other connection encryption\. 

## Oracle 19c with Amazon RDS<a name="Oracle.Concepts.FeatureSupport.19c"></a>

Amazon RDS supports Oracle version 19c, which includes Oracle Enterprise Edition and Oracle Standard Edition Two\.

Oracle 19c version 19\.0\.0\.0 includes many new features and updates from the previous version\. In this section, you can find the features and changes important to using Oracle 19c version 19\.0\.0\.0 on Amazon RDS\. For a complete list of the changes, see the [Oracle Database 19c](https://docs.oracle.com/en/database/oracle/oracle-database/19/index.html) documentation\. For a complete list of features supported by each Oracle 19c edition, see [ Permitted Features, Options, and Management Packs by Oracle Database Offering](https://docs.oracle.com/en/database/oracle/oracle-database/19/dblic/Licensing-Information.html#GUID-0F9EB85D-4610-4EDF-89C2-4916A0E7AC87) in the Oracle documentation\. 

### Amazon RDS Parameter Changes for Oracle 19c Version 19\.0\.0\.0<a name="Oracle.Concepts.FeatureSupport.19c.Parameters"></a>

Oracle 19c version 19\.0\.0\.0 includes several new parameters and parameters with new ranges and new default values\.

The following table shows the new Amazon RDS parameters for Oracle 19c version 19\.0\.0\.0\.


****  

| Name | Values | Modifiable | Description | 
| --- | --- | --- | --- | 
|  [ lob\_signature\_enable](https://docs.oracle.com/en/database/oracle/oracle-database/19/refrn/lob_signature_enable.html#GUID-62997AB5-1084-4C9A-8258-8CB695C7A1D6)  | TRUE, FALSE \(default\) | Y | Enables or disables the LOB locator signature feature\. | 
|  [ max\_datapump\_parallel\_per\_job](https://docs.oracle.com/en/database/oracle/oracle-database/19/refrn/MAX_DATAPUMP_PARALLEL_PER_JOB.html#GUID-33B1F962-B8C3-4DCE-BE68-66FC5D34ECA3)  | 1 to 1024, or AUTO | Y | Specifies the maximum number of parallel processes allowed for each Oracle Data Pump job\. | 

The `compatible` parameter has a new maximum value for Oracle 19c version 19\.0\.0\.0 on Amazon RDS\. The following table shows the new default value\. 


****  

| Parameter Name | Oracle 19c Version 19\.0\.0\.0 Maximum Value | Oracle 18c Version 18\.0\.0\.0 Maximum Value | 
| --- | --- | --- | 
| [ compatible](https://docs.oracle.com/en/database/oracle/oracle-database/19/refrn/COMPATIBLE.html#GUID-6C57EE11-BD06-4BB8-A0F7-D6CDDD086FA9) | 19\.0\.0 | 18\.0\.0 | 

The following parameters were removed in Oracle 19c Version 19\.0\.0\.0:
+ exafusion\_enabled
+ max\_connections
+ o7\_dictionary\_access

## Oracle 18c with Amazon RDS<a name="Oracle.Concepts.FeatureSupport.18c"></a>

Amazon RDS supports Oracle version 18c, which includes Oracle Enterprise Edition and Oracle Standard Edition Two\.

Oracle 18c version 18\.0\.0\.0 includes many new features and updates from the previous version\. In this section, you can find the features and changes important to using Oracle 18c version 18\.0\.0\.0 on Amazon RDS\. For a complete list of the changes, see the [Oracle Database 18c](https://docs.oracle.com/en/database/oracle/oracle-database/18/index.html)\. For a complete list of features supported by each Oracle 18c edition, see [ Permitted Features, Options, and Management Packs by Oracle Database Offering](https://docs.oracle.com/en/database/oracle/oracle-database/18/dblic/Licensing-Information.html#GUID-0F9EB85D-4610-4EDF-89C2-4916A0E7AC87) in the Oracle documentation\. 

### Amazon RDS Parameter Changes for Oracle 18c Version 18\.0\.0\.0<a name="Oracle.Concepts.FeatureSupport.18c.Parameters"></a>

Oracle 18c version 18\.0\.0\.0 includes several new parameters and parameters with new ranges and new default values\.

The following table shows the new Amazon RDS parameters for Oracle 18c version 18\.0\.0\.0\.


****  

| Name | Values | Modifiable | Description | 
| --- | --- | --- | --- | 
|  [ adg\_account\_info\_tracking](https://docs.oracle.com/en/database/oracle/oracle-database/18/refrn/ADG_ACCOUNT_INFO_TRACKING.html#GUID-D8CBA7A5-A027-4366-8146-1F141FC7B111)  | LOCAL \(default\), GLOBAL | N | Controls login attempts of users on Active Data Guard Standby databases\. It extends the control of user account security information\. | 
|  [ inmemory\_optimized\_arithmetic](https://docs.oracle.com/en/database/oracle/oracle-database/18/refrn/INMEMORY_OPTIMIZED_ARITHMETIC.html#GUID-7321D23C-A75B-4830-9375-99E5B06E06EB)  | ENABLE, DISABLE \(default\) | Y | Encodes the `NUMBER` data type in in\-memory tables compressed with `QUERY LOW` as a fixed\-width native integer scaled by a common exponent\. | 
|  [ optimizer\_ignore\_hints](https://docs.oracle.com/en/database/oracle/oracle-database/18/refrn/OPTIMIZER_IGNORE_HINTS.html#GUID-D62CA6D8-D0D8-4A20-93EA-EEB4B3144347)  | TRUE, FALSE \(default\) | Y | Specifies whether embedded hints are ignored\. | 
|  [ optimizer\_ignore\_parallel\_hints](https://docs.oracle.com/en/database/oracle/oracle-database/18/refrn/OPTIMIZER_IGNORE_PARALLEL_HINTS.html#GUID-C590B465-AE71-4B47-92BB-D9DD51326DAC)  | TRUE, FALSE \(default\) | Y | Specifies that embedded parallel hints are ignored\. | 
|  [ parallel\_min\_degree](https://docs.oracle.com/en/database/oracle/oracle-database/18/refrn/PARALLEL_MIN_DEGREE.html#GUID-C18B401F-9989-486B-AB58-4B4E01394585)  | 1 \(default\) and higher or CPU | Y | Controls the minimum degree of parallelism computed by automatic degree of parallelism\. | 

The `compatible` parameter has a new default value for Oracle 18c version 18\.0\.0\.0 on Amazon RDS\. The following table shows the new default value\. 


****  

| Parameter Name | Oracle 18c Version 18\.0\.0\.0 Default Value | Oracle 12c Version 12\.2\.0\.1 Default Value | 
| --- | --- | --- | 
| [ compatible](https://docs.oracle.com/en/database/oracle/oracle-database/18/refrn/COMPATIBLE.html#GUID-6C57EE11-BD06-4BB8-A0F7-D6CDDD086FA9) | 18\.0\.0 | 12\.2\.0 | 

The following parameters were removed in Oracle 18c Version 18\.0\.0\.0:
+ standby\_archive\_dest
+ utl\_file\_dir

## Oracle 12c with Amazon RDS<a name="Oracle.Concepts.FeatureSupport.12c"></a>

Amazon RDS supports Oracle version 12c, which includes Oracle Enterprise Edition and Oracle Standard Edition Two\. Oracle version 12c includes two major versions:
+ [Oracle 12c Version 12\.2\.0\.1 with Amazon RDS](#Oracle.Concepts.FeatureSupport.12cV2Overview)
+ [Oracle 12c Version 12\.1\.0\.2 with Amazon RDS](#Oracle.Concepts.FeatureSupport.12cV1Overview)

### Oracle 12c Version 12\.2\.0\.1 with Amazon RDS<a name="Oracle.Concepts.FeatureSupport.12cV2Overview"></a>

Oracle 12c version 12\.2\.0\.1 includes many new features and updates from the previous version\. In this section, you can find the features and changes important to using Oracle 12c version 12\.2\.0\.1 on Amazon RDS\. For a complete list of the changes, see the [Oracle 12c version 12\.2 documentation](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/index.html)\. For a complete list of features supported by each Oracle 12c edition, see [ Permitted Features, Options, and Management Packs by Oracle Database Offering](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dblic/Licensing-Information.html#GUID-0F9EB85D-4610-4EDF-89C2-4916A0E7AC87) in the Oracle documentation\. 

#### Amazon RDS Parameter Changes for Oracle 12c Version 12\.2\.0\.1<a name="Oracle.Concepts.FeatureSupport.12cV2.Parameters"></a>

Oracle 12c version 12\.2\.0\.1 includes 20 new parameters in addition to several parameters with new ranges and new default values\.

The following table shows the new Amazon RDS parameters for Oracle 12c version 12\.2\.0\.1\.


****  

| Name | Values | Modifiable | Description | 
| --- | --- | --- | --- | 
|  [ allow\_global\_dblinks](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/refrn/ALLOW_GLOBAL_DBLINKS.html#GUID-854693F5-87F6-46FB-A656-F6DAC3524BA2)  | TRUE, FALSE \(default\) | Y | Specifies whether LDAP lookup for database links is allowed for the database\. | 
|  [ approx\_for\_aggregation](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/refrn/APPROX_FOR_AGGREGATION.html#GUID-46853DF9-7688-46C7-A5BA-308B9B2DAF67)  | TRUE, FALSE \(default\) | Y | Replaces exact query processing for aggregation queries with approximate query processing\. | 
|  [ approx\_for\_count\_distinct](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/refrn/APPROX_FOR_COUNT_DISTINCT.html#GUID-D2A8A53F-113A-4E6F-AC2E-37139460EF8D)  | TRUE, FALSE \(default\) | Y | Automatically replaces `COUNT (DISTINCT expr)` queries with `APPROX_COUNT_DISTINCT` queries\. | 
|  [ approx\_for\_percentile](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/refrn/APPROX_FOR_PERCENTILE.html#GUID-3872A78C-9B3F-457C-AD28-4E86F71AE74D)  | NONE \(default\), PERCENTILE\_CONT, PERCENTILE\_CONT DETERMINISTIC, PERCENTILE\_DISC, PERCENTILE\_DISC DETERMINISTIC, ALL, ALL DETERMINISTIC | Y | Converts exact percentile functions to their approximate percentile function counterparts\. | 
|  [ cursor\_invalidation](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/refrn/CURSOR_INVALIDATION.html#GUID-6BAB61E7-FABA-41D3-B73A-EB828A6767E5)  | DEFERRED, IMMEDIATE \(default\) | Y | Controls whether deferred cursor invalidation or immediate cursor invalidation is used for DDL statements by default\. | 
|  [ data\_guard\_sync\_latency](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/refrn/DATA_GUARD_SYNC_LATENCY.html#GUID-A0965F0B-608C-4B68-85D1-9F14EFC191CC)  | 0 \(default\) to the number of seconds specified by the NET\_TIMEOUT attribute for the LOG\_ARCHIVE\_DEST\_n parameter | Y | Controls how many seconds the Log Writer \(LGWR\) process waits beyond the response of the first in a series of Oracle Data Guard SYNC redo transport mode connections\. | 
|  [ data\_transfer\_cache\_size](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/refrn/DATA_TRANSFER_CACHE_SIZE.html#GUID-322093B7-1673-490D-8A1A-5F461D7897DD)  | 0 – 512M, rounded up to the next granule size | Y | Sets the size of the data transfer cache \(in bytes\) used to receive data blocks \(typically from a primary database in an Oracle Data Guard environment\) for consumption by an instance when an RMAN RECOVER \.\.\. NONLOGGED BLOCK command is running\. | 
|  [ inmemory\_adg\_enabled](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/refrn/INMEMORY_ADG_ENABLED.html#GUID-74F7324A-6067-4B7A-9D72-3046DDE70D0A)  | TRUE \(default\), FALSE | Y | Indicates whether in\-memory for Active Data Guard is enabled in addition to the in\-memory cache size\. | 
|  [ inmemory\_expressions\_usage](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/refrn/INMEMORY_EXPRESSIONS_USAGE.html#GUID-9B926459-CA8A-4843-B9A1-4DE6F630EB45)  | STATIC\_ONLY, DYNAMIC\_ONLY,ENABLE \(default\), DISABLE | Y | Controls which In\-Memory Expressions \(IM expressions\) are populated into the In\-Memory Column Store \(IM column store\) and are available for queries\. | 
|  [ inmemory\_virtual\_columns](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/refrn/INMEMORY_VIRTUAL_COLUMNS.html#GUID-1B066B16-4F22-4D9D-9D82-AF7B786DB97E)  | ENABLE, MANUAL \(default\), DISABLE | Y | Controls which In\-Memory Expressions \(IM expressions\) are populated into the In\-Memory Column Store \(IM column store\) and are available for queries\. | 
|  [ instance\_abort\_delay\_time](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/refrn/INSTANCE_ABORT_DELAY_TIME.html#GUID-F15A6EC9-1F4F-4DB7-9981-FFC1F181559F)  | 0 \(default\) and higher | Y | Specifies how much time to delay an internally initiated instance shutdown \(in seconds\), such as when a fatal process dies or an unrecoverable instance error occurs\. | 
|  [ instance\_mode](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/refrn/INSTANCE_MODE.html#GUID-77461EFC-0925-4511-AD4C-426CF842B4FD)  | READ\-WRITE \(default\), READ\-ONLY, READ\-MOSTLY | N | Indicates whether the instance is read\-write, read\-only, or read\-mostly\. | 
|  [ long\_module\_action](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/refrn/LONG_MODULE_ACTION.html#GUID-DA943655-E516-44A3-8D11-A86E5EF5F585)  | TRUE \(default\), FALSE | Y | Enables the use of longer lengths for modules and actions\. | 
|  [ max\_idle\_time](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/refrn/MAX_IDLE_TIME.html#GUID-9E26A81D-D99E-4EA8-88DE-77AF68482A20)  | 0 \(default\) to the maximum integer\. The value of 0 indicates that there is no limit\. | Y | Specifies the maximum number of minutes that a session can be idle\. After that point, the session is automatically terminated\.  | 
|  [ optimizer\_adaptive\_plans](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/refrn/OPTIMIZER_ADAPTIVE_PLANS.html#GUID-58C3E867-36BA-449A-B452-4E90FE6DCF05)  | TRUE \(default\), FALSE | Y | Controls adaptive plans\. Adaptive plans are execution plans built with alternative choices that are decided at run time based on statistics collected as the query executes\. | 
|  [ optimizer\_adaptive\_statistics](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/refrn/OPTIMIZER_ADAPTIVE_STATISTICS.html#GUID-D52B4342-3887-4054-A65C-5AEA83F69E35)  | TRUE, FALSE \(default\) | Y | Controls adaptive statistics\. Some query shapes are too complex to rely on base table statistics alone, so the optimizer augments these statistics with adaptive statistics\. | 
|  [ outbound\_dblink\_protocols](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/refrn/OUTBOUND_DBLINK_PROTOCOLS.html#GUID-4E760A19-5791-4E2D-8792-016B7B6D10D6)  | ALL \(default\), NONE, TCP, TCPS, IPC | N | Specifies the network protocols allowed for communicating for outbound database links in the database\. | 
|  [ resource\_manage\_goldengate](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/refrn/RESOURCE_MANAGE_GOLDENGATE.html#GUID-389AB551-F4EB-4294-BD52-5E5E41AFDA80)  | TRUE, FALSE \(default\) | Y | Determines whether Oracle GoldenGate apply processes in the database are resource managed\. | 
|  [ standby\_db\_preserve\_states](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/refrn/STANDBY_DB_PRESERVE_STATES.html#GUID-8D332556-30B7-4C45-8557-50988DC2219E)  | NONE \(default\), SESSION, ALL | N | Controls whether user sessions and other internal states of the instance are retained when a readable physical standby database is converted to a primary database\.  | 
|  [ uniform\_log\_timestamp\_format](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/refrn/UNIFORM_LOG_TIMESTAMP_FORMAT.html#GUID-041BC204-EA0E-4260-9726-D25C2C86A2F5)  | TRUE \(default\), FALSE | Y | Specifies that a uniform timestamp format be used in Oracle Database trace \(\.trc\) files and log files \(such as the alert log\)\. | 

The `compatible` parameter has a new default value for Oracle 12c version 12\.2\.0\.1 on Amazon RDS\. The following table shows the new default value\. 


****  

| Parameter Name | Oracle 12c Version 12\.2\.0\.1 Default Value | Oracle 12c Version 12\.1\.0\.2 Default Value | 
| --- | --- | --- | 
| [ compatible](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/refrn/COMPATIBLE.html#GUID-6C57EE11-BD06-4BB8-A0F7-D6CDDD086FA9) | 12\.2\.0 | 12\.0\.0 | 

The `optimizer_features_enable` parameter has a new value range for Oracle 12c version 12\.2\.0\.1 on Amazon RDS\. For the old and new value ranges, see the following table\.


****  

| Parameter Name | 12c Version 12\.2\.0\.1 Range | 12c Version 12\.1\.0\.2 Range | 
| --- | --- | --- | 
|  [optimizer\_features\_enable](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/refrn/OPTIMIZER_FEATURES_ENABLE.html#GUID-E193EC9E-B642-4C01-99EC-24E04AEA1A2C)  |  8\.0\.0 to 12\.2\.0\.1  |  8\.0\.0 to 12\.1\.0\.2  | 

The following parameters were removed in Oracle 12c Version 12\.2\.0\.1:
+ global\_context\_pool\_size
+ max\_enabled\_roles
+ optimizer\_adaptive\_features
+ parallel\_automatic\_tuning
+ parallel\_degree\_level
+ use\_indirect\_data\_buffers

The following parameter is not supported in Oracle 12c Version 12\.2\.0\.1 and later:
+ sec\_case\_sensitive\_logon

#### Amazon RDS Security Changes for Oracle 12c Version 12\.2\.0\.1<a name="Oracle.Concepts.FeatureSupport.12cV2.Security"></a>

In Oracle 12c version 12\.2\.0\.1, direct grant of the privilege `ADMINISTER DATABASE TRIGGER` is required for the owners of database\-level triggers\. During a major version upgrade to Oracle 12c version 12\.2\.0\.1, Amazon RDS grants this privilege to any user that owns a trigger so that the trigger owner has the required privileges\. For more information, see the My Oracle Support document [2275535\.1](https://support.oracle.com/epmos/faces/DocContentDisplay?id=2275535.1)\.

### Oracle 12c Version 12\.1\.0\.2 with Amazon RDS<a name="Oracle.Concepts.FeatureSupport.12cV1Overview"></a>

Oracle 12c version 12\.1\.0\.2 brings over 500 new features and updates from the previous version\. In this section, you can find the features and changes important to using Oracle 12c version 12\.1\.0\.2 on Amazon RDS\. For a complete list of the changes, see the [Oracle 12c version 12\.1 documentation](http://docs.oracle.com/database/121/index.htm)\. For a complete list of features supported by each Oracle 12c edition, see [Permitted Features, Options, and Management Packs by Oracle Database Edition](https://docs.oracle.com/database/121/DBLIC/editions.htm#DBLIC116) in the Oracle documentation\. 

Oracle 12c version 12\.1\.0\.2 includes 16 new parameters that impact your Amazon RDS DB instance, and also 18 new system privileges, several no longer supported packages, and several new option group settings\. For more information on these changes, see the following sections\. 

#### Amazon RDS Parameter Changes for Oracle 12c Version 12\.1\.0\.2<a name="Oracle.Concepts.FeatureSupport.12c.Parameters"></a>

Oracle 12c version 12\.1\.0\.2 includes 16 new parameters in addition to several parameters with new ranges and new default values\.

The following table shows the new Amazon RDS parameters for Oracle 12c version 12\.1\.0\.2\.


****  

| Name | Values | Modifiable | Description | 
| --- | --- | --- | --- | 
|  [connection\_brokers](http://docs.oracle.com/database/121/REFRN/GUID-C6C3A860-D74C-4D3D-9185-E59794360ACA.htm#REFRN10333)  | CONNECTION\_BROKERS = broker\_description\[,\.\.\.\] | N | Specifies connection broker types, the number of connection brokers of each type, and the maximum number of connections per broker\.  | 
|  [db\_index\_compression\_inheritance](https://docs.oracle.com/database/121/REFRN/GUID-5238D586-B068-46F7-9D8F-E4C174E5D5B2.htm#REFRN10336)  | TABLESPACE, TABL, ALL, NONE | Y | Displays the options that are set for table or tablespace level compression inheritance\.  | 
|  [db\_big\_table\_cache\_percent\_target](https://docs.oracle.com/database/121/REFRN/GUID-122865EE-4589-434D-8DD5-4E201C6CC739.htm#REFRN10340)  | 0\-90 | Y | Specifies the cache section target size for automatic big table caching, as a percentage of the buffer cache\.  | 
|  [heat\_map](http://docs.oracle.com/database/121/REFRN/GUID-F0F60587-56A5-4727-B9D4-FA44ADD7774E.htm#REFRN10342)  |  ON, OFF  | Y | Enables the database to track read and write access of all segments and modification of database blocks that are due to data manipulation language \(DML\) and data definition language \(DDL\) statements\.  | 
|  [inmemory\_clause\_default](http://docs.oracle.com/database/121/REFRN/GUID-5772F775-2A3E-4BC8-AA03-B8FF383BEE52.htm#REFRN10350)  | INMEMORY, NO INMEMORY | Y | INMEMORY\_CLAUSE\_DEFAULT enables you to specify a default In\-Memory Column Store \(IM column store\) clause for new tables and materialized views\. | 
|  [inmemory\_clause\_default\_memcompress](http://docs.oracle.com/database/121/REFRN/GUID-5772F775-2A3E-4BC8-AA03-B8FF383BEE52.htm#REFRN10350)  | NO MEMCOMPRESS, MEMCOMPRESS FOR DML, MEMCOMPRESS FOR QUERY, MEMCOMPRESS FOR QUERY LOW, MEMCOMPRESS FOR QUERY HIGH, MEMCOMPRESS FOR CAPACITY, MEMCOMPRESS FOR CAPACITY LOW, MEMCOMPRESS FOR CAPACITY HIGH | Y | See INMEMORY\_CLAUSE\_DEFAULT\. | 
|  [inmemory\_clause\_default\_priority](http://docs.oracle.com/database/121/REFRN/GUID-5772F775-2A3E-4BC8-AA03-B8FF383BEE52.htm#REFRN10350)  | PRIORITY LOW, PRIORITY MEDIUM, PRIORITY HIGH, PRIORITY CRITICAL, PRIORITY NONE | Y | See INMEMORY\_CLAUSE\_DEFAULT\. | 
|  [inmemory\_force](http://docs.oracle.com/database/121/REFRN/GUID-1CAEDEBC-AE38-428D-B07E-6718A7225548.htm#REFRN10353)  | DEFAULT, OFF | Y | INMEMORY\_FORCE allows you to specify whether tables and materialized view that are specified as INMEMORY are populated into the In\-Memory Column Store \(IM column store\) or not\. | 
|  [inmemory\_max\_populate\_servers](http://docs.oracle.com/database/121/REFRN/GUID-3428C0FF-5AA9-491C-908E-F0BD88095A5A.htm#REFRN10355)  | Null | N | INMEMORY\_MAX\_POPULATE\_SERVERS specifies the maximum number of background populate servers to use for In\-Memory Column Store \(IM column store\) population, so that these servers don't overload the rest of the system\. | 
|  [inmemory\_query](http://docs.oracle.com/database/121/REFRN/GUID-5B9BDBE2-6835-4598-9717-64D2C9D622AB.htm#REFRN10351)  |  ENABLE \(default\), DISABLE | Y | INMEMORY\_QUERY is used to enable or disable in\-memory queries for the entire database at the session or system level\. | 
|  [inmemory\_size](http://docs.oracle.com/database/121/REFRN/GUID-B5BEB6BF-5308-485F-920D-CBB584DDDE8F.htm#REFRN10348)  | 0, 104857600\-274877906944  | Y | INMEMORY\_SIZE sets the size of the In\-Memory Column Store \(IM column store\) on a database instance\. | 
|  [inmemory\_trickle\_repopulate\_servers\_percent](http://docs.oracle.com/database/121/REFRN/GUID-499850D5-6418-4AD3-BEB5-865C4A165C39.htm#REFRN10358)  | 0 to 50 | Y | INMEMORY\_TRICKLE\_REPOPULATE\_SERVERS\_PERCENT limits the maximum number of background populate servers used for In\-Memory Column Store \(IM column store\) repopulation\. This limit is applied because trickle repopulation is designed to use only a small percentage of the populate servers\. | 
|  [max\_string\_size](http://docs.oracle.com/database/121/REFRN/GUID-D424D23B-0933-425F-BC69-9C0E6724693C.htm#REFRN10321)  |  STANDARD \(default\), EXTENDED  | N | Controls the maximum size of VARCHAR2, NVARCHAR2, and RAW\. For more information, see [Using Extended Data Types](#Oracle.Concepts.ExtendedDataTypes)\. | 
|  [optimizer\_adaptive\_features](http://docs.oracle.com/database/121/REFRN/GUID-F5E53EFA-B395-4336-B046-1EE7AF12353B.htm#REFRN10344)  |  TRUE \(default\), FALSE  | Y | Enables or disables all of the adaptive optimizer features\. | 
|  [optimizer\_adaptive\_reporting\_only](http://docs.oracle.com/database/121/REFRN/GUID-8DD128F9-4891-4061-9B2D-9D45315D44FB.htm#REFRN10327)  |  TRUE, FALSE \(default\)  | Y | Controls reporting\-only mode for adaptive optimizations\.  | 
|  [pdb\_file\_name\_convert](http://docs.oracle.com/database/121/REFRN/GUID-074F8896-D565-4139-BCDB-C81C9D741941.htm#REFRN10322)  | There is no default value\. | N | Maps names of existing files to new file names\. | 
|  [pga\_aggregate\_limit](http://docs.oracle.com/database/121/REFRN/GUID-E364D0E5-19F2-4081-B55E-131DF09CFDB3.htm#REFRN10328)  | 0\-max of memory | Y | Specifies a limit on the aggregate PGA memory consumed by the instance\.  | 
|  [processor\_group\_name](http://docs.oracle.com/database/121/REFRN/GUID-4CE6E299-4D81-45D8-9EAC-48E76E4911BA.htm#REFRN10323)  | There is no default value\. | N | Instructs the database instance to run itself within the specified operating system processor group\.  | 
|  [spatial\_vector\_acceleration](http://docs.oracle.com/database/121/REFRN/GUID-1B7E8737-E176-46AD-BC68-1FCBBD4D05B6.htm#REFRN10337)  | TRUE, FALSE  | N | Enables or disables the spatial vector acceleration, part of spatial option\.  | 
|  [temp\_undo\_enabled](http://docs.oracle.com/database/121/REFRN/GUID-E2A01A84-2D63-401F-B64E-C96B18C5DCA6.htm#REFRN10326)  | TRUE, FALSE \(default\) | Y | Determines whether transactions within a particular session can have a temporary undo log\.  | 
|  [threaded\_execution](http://docs.oracle.com/database/121/REFRN/GUID-7A668A49-9FC5-4245-AD27-10D90E5AE8A8.htm#REFRN10335)  | TRUE, FALSE | N | Enables the multithreaded Oracle model, but prevents OS authentication\.  | 
|  [unified\_audit\_sga\_queue\_size](http://docs.oracle.com/database/121/REFRN/GUID-060707DF-8431-4866-8B9F-4F450472D95E.htm#REFRN10343)  | 1 MB \- 30 MB | Y | Specifies the size of the system global area \(SGA\) queue for unified auditing\.  | 
|  [use\_dedicated\_broker](http://docs.oracle.com/database/121/REFRN/GUID-643239D0-FABF-43C0-9791-BED46CB8FE07.htm#REFRN10341)  |  TRUE, FALSE  | N | Determines how dedicated servers are spawned\.  | 

Several parameters have new value ranges for Oracle 12c version 12\.1\.0\.2 on Amazon RDS\. For the old and new value ranges, see the following table\.


****  

| Parameter Name | 12c Version 12\.1\.0\.2 Range | 11g Range | 
| --- | --- | --- | 
|  [audit\_trail](http://docs.oracle.com/database/121/REFRN/GUID-BD86F593-B606-4367-9FB6-8DAB2E47E7FA.htm#REFRN10006)  |  os \| db \[, extended\] \| xml \[, extended\]  |  os \| db \[, extended\] \| xml \[, extended\] \| true \| false  | 
|  [compatible](http://docs.oracle.com/database/121/REFRN/GUID-6C57EE11-BD06-4BB8-A0F7-D6CDDD086FA9.htm#REFRN10019)  |  For DB instances upgraded from Oracle 11g, automatically set to 12\.0\.0 on Amazon RDS unless a lower value is explicitly provided during the upgrade \(as low as 11\.2\.0\)  For new Oracle 12c version 12\.1\.0\.2 DB instances, starts with 12\.0\.0 on Amazon RDS |  Starts with 11\.2\.0 on Amazon RDS | 
|  [db\_securefile](http://docs.oracle.com/database/121/REFRN/GUID-6F7C5E21-3929-4AB1-9C72-1BB9BDDB011F.htm#REFRN10290)  |  PERMITTED \| PREFERRED \| ALWAYS \| IGNORE \| FORCE  |  PERMITTED \| ALWAYS \| IGNORE \| FORCE  | 
|  [db\_writer\_processes](http://docs.oracle.com/database/121/REFRN/GUID-75774634-3B5E-49F8-A5C5-65923F596845.htm#REFRN10043)  |  1\-100  |  1\-36  | 
|  [optimizer\_features\_enable](http://docs.oracle.com/database/121/REFRN/GUID-E193EC9E-B642-4C01-99EC-24E04AEA1A2C.htm#REFRN10141)  |  8\.0\.0 to 12\.1\.0\.2  |  8\.0\.0 to 11\.2\.0\.4  | 
|  [parallel\_degree\_policy](http://docs.oracle.com/database/121/REFRN/GUID-BF09265F-8545-40D4-BD29-E58D5F02B0E5.htm#REFRN10310)  |  MANUAL,LIMITED,AUTO,ADAPTIVE  |  MANUAL,LIMITED,AUTO  | 
|  [parallel\_min\_server](http://docs.oracle.com/database/121/REFRN/GUID-1D7EC131-7B5B-40E5-A0F8-ABC7B4C5B0E8.htm#REFRN10160)  |  0 to parallel\_max\_servers  |  CPU\_COUNT \* PARALLEL\_THREADS\_PER\_CPU \* 2 to parallel\_max\_servers  | 

One parameter has a new default value for Oracle 12c on Amazon RDS\. The following table shows the new default value\. 


****  

| Parameter Name | Oracle 12c Default Value | Oracle 11g Default Value | 
| --- | --- | --- | 
|  job\_queue\_processes  |  50  |  1000  | 

#### Amazon RDS System Privileges for Oracle 12c Version 12\.1\.0\.2<a name="Oracle.Concepts.FeatureSupport.12c.Privileges"></a>

Several new system privileges have been granted to the system account for Oracle 12c version 12\.1\.0\.2\. These new system privileges include the following:
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

#### Amazon RDS Options for Oracle 12c Version 12\.1\.0\.2<a name="Oracle.Concepts.FeatureSupport.12c.Options"></a>

Several Oracle options changed between Oracle 11g and Oracle 12c version 12\.1\.0\.2, though most of the options remain the same between the two versions\. The Oracle 12c version 12\.1\.0\.2 changes include the following: 
+ Oracle Enterprise Manager Database Express 12c replaced Oracle Enterprise Manager 11g Database Control\. For more information, see [Oracle Enterprise Manager Database Express](Appendix.Oracle.Options.OEM_DBControl.md)\. 
+ The option XMLDB is installed by default in Oracle 12c version 12\.1\.0\.2\. You no longer need to install this option yourself\. 

#### Amazon RDS PL/SQL Packages for Oracle 12c Version 12\.1\.0\.2<a name="Oracle.Concepts.FeatureSupport.12c.Packages"></a>

Oracle 12c version 12\.1\.0\.2 includes a number of new built\-in PL/SQL packages\. The packages included with Amazon RDS Oracle 12c version 12\.1\.0\.2 include the following\. 


****  

| Package Name | Description | 
| --- | --- | 
|  [CTX\_ANL](http://docs.oracle.com/cd/E16655_01/appdev.121/e17602/c_anl.htm)  | The CTX\_ANL package is used with AUTO\_LEXER and provides procedures for adding and dropping a custom dictionary from the lexer\.  | 
|  [DBMS\_APP\_CONT](http://docs.oracle.com/cd/E16655_01/appdev.121/e17602/d_app_cont.htm)  | The DBMS\_APP\_CONT package provides an interface to determine if the in\-flight transaction on a now unavailable session committed or not, and if the last call on that session completed or not\.  | 
|  [DBMS\_AUTO\_REPORT](http://docs.oracle.com/cd/E16655_01/appdev.121/e17602/d_auto_report.htm#sthref1284)  | The DBMS\_AUTO\_REPORT package provides an interface to view SQL Monitoring and Real\-time Automatic Database Diagnostic Monitor \(ADDM\) data that has been captured into Automatic Workload Repository \(AWR\)\.  | 
|  [DBMS\_GOLDENGATE\_AUTH](http://docs.oracle.com/cd/E16655_01/appdev.121/e17602/d_goldengate_auth.htm)  | The DBMS\_GOLDENGATE\_AUTH package provides subprograms for granting privileges to and revoking privileges from GoldenGate administrators\.  | 
|  [DBMS\_HEAT\_MAP](http://docs.oracle.com/cd/E16655_01/appdev.121/e17602/d_heat_map.htm)  | The DBMS\_HEAT\_MAP package provides an interface to externalize heatmaps at various levels of storage including block, extent, segment, object, and tablespace\.  | 
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
|  [DBMS\_TSDP\_MANAGE](http://docs.oracle.com/cd/E16655_01/appdev.121/e17602/d_tsdp_manage.htm)  | The DBMS\_TSDP\_MANAGE package provides an interface to import and manage sensitive columns and sensitive column types in the database\. DBMS\_TSDP\_MANAGE is used with the [DBMS\_TSDP\_PROTECT](http://docs.oracle.com/database/121/ARPLS/d_tsdp_protect.htm#BHAFHBHI) package for transparent sensitive data protection \(TSDP\) policies\. DBMS\_TSDP\_MANAGE is available with the Enterprise Edition only\.  | 
|  [DBMS\_TSDP\_PROTECT](http://docs.oracle.com/cd/E16655_01/appdev.121/e17602/d_tsdp_protect.htm)  | The DBMS\_TSDP\_PROTECT package provides an interface to configure transparent sensitive data protection \(TSDP\) policies in conjunction with the [DBMS\_TSDP\_MANAGE](http://docs.oracle.com/database/121/ARPLS/d_tsdp_manage.htm#CACCBHBC) package\. DBMS\_TSDP\_PROTECT is available with the Enterprise Edition only\.  | 
|  [DBMS\_XDB\_CONFIG](http://docs.oracle.com/database/121/ARPLS/d_xdb_config.htm#ARPLS73564)  | The DBMS\_XDB\_CONFIG package provides an interface for configuring Oracle XML DB and its repository\.  | 
| [DBMS\_XDB\_CONSTANTS](http://docs.oracle.com/database/121/ARPLS/d_xdb_constants.htm#ARPLS73572) | The DBMS\_XDB\_CONSTANTS package provides an interface to commonly used constants\. Oracle recommends using constants instead of dynamic strings to avoid typographical errors\.  | 
| [DBMS\_XDB\_REPOS](http://docs.oracle.com/database/121/ARPLS/d_xdb_repos.htm#ARPLS74188) | The DBMS\_XDB\_REPOS package provides an interface to operate on the Oracle XML database Repository\.  | 
|  [DBMS\_XMLSCHEMA\_ANNOTATE](http://docs.oracle.com/database/121/ARPLS/d_xmlschema_annotate.htm#ARPLS73580)  | The DBMS\_XMLSCHEMA\_ANNOTATE package provides an interface to manage and configure the structured storage model, mainly through the use of pre\-registration schema annotations\.  | 
|  [DBMS\_XMLSTORAGE\_MANAGE](http://docs.oracle.com/database/121/ARPLS/d_xmlstorage_man.htm#ARPLS73588)  | The DBMS\_XMLSTORAGE\_MANAGE package provides an interface to manage and modify XML storage after schema registration has been completed\.  | 
|  [DBMS\_XSTREAM\_ADM](http://docs.oracle.com/cd/E16655_01/appdev.121/e17602/d_xstrm_adm.htm)  | The DBMS\_XSTREAM\_ADM package provides interfaces for streaming database changes between an Oracle database and other systems\. XStream enables applications to stream out or stream in database changes\.  | 
|  [DBMS\_XSTREAM\_AUTH](http://docs.oracle.com/cd/E16655_01/appdev.121/e17602/d_xstrm_auth.htm)  | The DBMS\_XSTREAM\_AUTH package provides subprograms for granting privileges to and revoking privileges from XStream administrators\.  | 
|  [UTL\_CALL\_STACK](http://docs.oracle.com/cd/E16655_01/appdev.121/e17602/u_call_stack.htm)  | The UTL\_CALL\_STACK package provides an interface to provide information about currently executing subprograms\.  | 

#### Oracle 12c Version 12\.1\.0\.2 Packages Not Supported<a name="Oracle.Concepts.FeatureSupport.12c.NotSupported"></a>

Several Oracle 11g PL/SQL packages are not supported in Oracle 12c version 12\.1\.0\.2\. These packages include the following:
+ DBMS\_AUTO\_TASK\_IMMEDIATE
+ DBMS\_CDC\_PUBLISH
+ DBMS\_CDC\_SUBSCRIBE
+ DBMS\_EXPFIL
+ DBMS\_OBFUSCATION\_TOOLKIT
+ DBMS\_RLMGR
+ SDO\_NET\_MEM

## Oracle Database Feature Support<a name="Oracle.Concepts.FeatureSupport"></a>

Amazon RDS Oracle supports most of the features and capabilities of Oracle Database\. Some features might have limited support or restricted privileges\. Some features are only available in Enterprise Edition, and some require additional licenses\. For more information about Oracle Database features for specific Oracle Database versions, see the *Oracle Database Licensing Information User Manual* for the version you're using\. 

**Note**  
These lists are not exhaustive\.

Amazon RDS Oracle supports the following Oracle Database features:
+ Advanced Compression
+ Application Express \(APEX\)

  For more information, see [Oracle Application Express](Appendix.Oracle.Options.APEX.md)\.
+ Automatic Memory Management
+ Automatic Undo Management
+ Automatic Workload Repository \(AWR\)

  For more information, see [Generating Performance Reports with Automatic Workload Repository \(AWR\)](Appendix.Oracle.CommonDBATasks.Database.md#Appendix.Oracle.CommonDBATasks.AWR)\.
+ Active Data Guard with Maximum Performance in the same AWS Region or across AWS Regions

  For more information, see [Working with Oracle Replicas for Amazon RDS](oracle-read-replicas.md)\.
+ Continuous Query Notification \(version 12\.1\.0\.2\.v7 and later\)

  For more information, see [ Using Continuous Query Notification \(CQN\)](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/adfns/cqn.html#GUID-373BAF72-3E63-42FE-8BEA-8A2AEFBF1C35) in the Oracle documentation\.
+ Data Redaction
+ Database Change Notification \(version 11\.2\.0\.4\.v11 and later 11g versions\)

  For more information, see [ Database Change Notification](https://docs.oracle.com/cd/E11882_01/java.112/e16548/dbchgnf.htm#JJDBC28815) in the Oracle documentation\.
**Note**  
This feature changes to Continuous Query Notification in version 12\.1 and later\.
+ Database In\-Memory \(version 12\.1 and later\)
+ Distributed Queries and Transactions
+ Edition\-Based Redefinition

  For more information, see [Setting the Default Edition for a DB Instance](Appendix.Oracle.CommonDBATasks.Database.md#Appendix.Oracle.CommonDBATasks.DefaultEdition)\.
+ Enterprise Manager Database Control \(11g\) and EM Express \(12c\)

  For more information, see [Oracle Enterprise Manager](Oracle.Options.OEM.md)\.
+ Fine\-Grained Auditing
+ Flashback Table, Flashback Query, Flashback Transaction Query
+ Import/export \(legacy and Data Pump\) and SQL\*Loader

  For more information, see [Importing Data into Oracle on Amazon RDS](Oracle.Procedural.Importing.md)\.
+ Java Virtual Machine \(JVM\)

  For more information, see [Oracle Java Virtual Machine](oracle-options-java.md)\.
+ Label Security \(version 12\.1 and later\)

  For more information, see [Oracle Label Security](Oracle.Options.OLS.md)\.
+ Locator

  For more information, see [Oracle Locator](Oracle.Options.Locator.md)\.
+ Materialized Views
+ Multimedia

  For more information, see [Oracle Multimedia](Oracle.Options.Multimedia.md)\.
+ Network encryption

  For more information, see [Oracle Native Network Encryption](Appendix.Oracle.Options.NetworkEncryption.md) and [Oracle Secure Sockets Layer](Appendix.Oracle.Options.SSL.md)\.
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
+ Unified Auditing, Mixed Mode \(version 12\.1 and later\)

  For more information, see [ Mixed Mode Auditing](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dbseg/introduction-to-auditing.html#GUID-4A3AEFC3-5422-4320-A048-8219EC96EAC1) in the Oracle documentation\.
+ XML DB \(without the XML DB Protocol Server\)

  For more information, see [Oracle XML DB](Appendix.Oracle.Options.XMLDB.md)\.
+ Virtual Private Database

Amazon RDS Oracle doesn't support the following Oracle Database features:
+ Automatic Storage Management \(ASM\)
+ Database Vault
+ Flashback Database
+ Multitenant
+ Oracle Enterprise Manager Cloud Control Management Repository
+ Real Application Clusters \(Oracle RAC\)
+ Real Application Testing
+ Unified Auditing, Pure Mode
+ Workspace Manager \(WMSYS\) schema

**Warning**  
In general, Amazon RDS doesn't prevent you from creating schemas for unsupported features\. However, if you create schemas for Oracle features and components that require SYS privileges, you can damage the data dictionary and affect the availability of your instance\. Use only supported features and schemas that are available in [Options for Oracle DB Instances](Appendix.Oracle.Options.md)\.

## Oracle Database Parameter Support<a name="Oracle.Concepts.FeatureSupport.Parameters"></a>

In Amazon RDS, you manage parameters using parameter groups\. For more information, see [Working with DB Parameter Groups](USER_WorkingWithParamGroups.md)\. To view the supported parameters for a specific Oracle edition and version, run the AWS CLI [https://docs.aws.amazon.com/cli/latest/reference/rds/describe-engine-default-parameters.html](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-engine-default-parameters.html) command\.

For example, to view the supported parameters for Oracle Enterprise Edition version 12\.2, run the following command\.

```
aws rds describe-engine-default-parameters --db-parameter-group-family oracle-ee-12.2
```

## Oracle Engine Version Management<a name="Oracle.Concepts.Patching"></a>

With DB engine version management, you can control when and how the database engine software running your DB instances is patched and upgraded\. With this feature, you get the flexibility to maintain compatibility with database engine patch versions\. You can also test new patch versions to ensure they work effectively with your application before deploying them in production\. In addition, you can perform version upgrades on your own terms and timelines\.

**Note**  
Amazon RDS periodically aggregates official Oracle database patches using an Amazon RDS\-specific DB engine version\. To see a list of which Oracle patches are contained in an Amazon RDS Oracle\-specific engine version, go to [Oracle Database Engine Release Notes](Appendix.Oracle.PatchComposition.md)\. 

If you enable auto minor version upgrades on your DB instance, they occur automatically\. You must perform major upgrades manually, with the exception of the automatic upgrade of 11\.2\.0\.4\. For more information about upgrading an Oracle DB instance, see [Upgrading the Oracle DB Engine](USER_UpgradeDBInstance.Oracle.md)\.

### Deprecation of Oracle 11\.2\.0\.4<a name="Oracle.Concepts.Deprecate.11204"></a>

Oracle Corporation intends to deprecate support for Oracle Database version 11\.2\.0\.4 on December 31, 2020\. On October 31, 2020, Amazon RDS plans to deprecate support for Oracle version 11\.2\.0\.4 SE1 using the License Included model\. On December 31, 2020, Amazon RDS plans to deprecate support for 11\.2\.0\.4 on all editions that use the Bring Your Own License model \(BYOL\)\.  

Amazon RDS plans to deprecate support for Oracle version 11\.2\.0\.4 by the following schedule, which includes upgrade recommendations\. For more information, see [Preparing for the Automatic Upgrade of Oracle 11g SE1](USER_UpgradeDBInstance.Oracle.md#USER_UpgradeDBInstance.Oracle.auto-upgrade-of-11g)\.


| Action or Recommendation | 11\.2\.0\.4 on SE1 with License Included | 11\.2\.0\.4 on EE, SE, and SE1 with BYOL | 
| --- | --- | --- | 
|  We recommend that you upgrade 11\.2\.0\.4 DB instances manually to the version of your choice\.   |  Now–October 31, 2020  |  Now–December 31, 2020  | 
|  We recommend that you upgrade 11\.2\.0\.4 snapshots manually to the version of your choice\.  |  September 1, 2020  |  October 1, 2020  | 
|  You can no longer create new instances with Amazon RDS for Oracle with the listed version\.  |  September 1, 2020  |  October 1, 2020  | 
|  Amazon RDS for Oracle starts automatic upgrades of your DB instances to version 19c\.  |  November 1, 2020  |  January 1, 2021  | 
|  Amazon RDS for Oracle starts automatic upgrades to version 19c for any DB instances restored from snapshots\.  |  November 1, 2020  |  January 1, 2021  | 

## Using Huge Pages with an Oracle DB Instance<a name="Oracle.Concepts.HugePages"></a>

Amazon RDS for Oracle supports Linux kernel huge pages for increased database scalability\. The use of huge pages results in smaller page tables and less CPU time spent on memory management, increasing the performance of large database instances\. For more information, see [Overview of HugePages](https://docs.oracle.com/database/121/UNXAR/appi_vlm.htm#UNXAR400) in the Oracle documentation\. 

You can use huge pages with the following versions and editions of Oracle: 
+ 19\.0\.0\.0, all editions
+ 18\.0\.0\.0, all editions
+ 12\.2\.0\.1, all editions
+ 12\.1\.0\.2, all editions
+ 11\.2\.0\.4, all editions

 The `use_large_pages` parameter controls whether huge pages are enabled for a DB instance\. The possible settings for this parameter are `ONLY`, `FALSE`, and `{DBInstanceClassHugePagesDefault}`\. The `use_large_pages` parameter is set to `{DBInstanceClassHugePagesDefault}` in the default DB parameter group for Oracle\. 

To control whether huge pages are enabled for a DB instance automatically, you can use the `DBInstanceClassHugePagesDefault` formula variable in parameter groups\. The value is determined as follows:
+ For the DB instance classes mentioned in the table following, `DBInstanceClassHugePagesDefault` always evaluates to `FALSE` by default, and `use_large_pages` evaluates to `FALSE`\. You can enable huge pages manually for these DB instance classes if the DB instance class has at least 14 GiB of memory\.
+ For DB instance classes not mentioned in the table following, if the DB instance class has less than 14 GiB of memory, `DBInstanceClassHugePagesDefault` always evaluates to `FALSE`\. Also, `use_large_pages` evaluates to `FALSE`\.
+ For DB instance classes not mentioned in the table following, if the instance class has at least 14 GiB of memory and less than 100 GiB of memory, `DBInstanceClassHugePagesDefault` evaluates to `TRUE` by default\. Also, `use_large_pages` evaluates to `ONLY`\. You can disable huge pages manually by setting `use_large_pages` to `FALSE`\.
+ For DB instance classes not mentioned in the table following, if the instance class has at least 100 GiB of memory, `DBInstanceClassHugePagesDefault` always evaluates to `TRUE`\. Also, `use_large_pages` evaluates to `ONLY` and huge pages can't be disabled\.

Huge pages are not enabled by default for the following DB instance classes\. 


****  

| DB Instance Class Family | DB Instance Classes with Huge Pages Not Enabled by Default | 
| --- | --- | 
|  db\.m5  |  db\.m5\.large  | 
|  db\.m4  |  db\.m4\.large, db\.m4\.xlarge, db\.m4\.2xlarge, db\.m4\.4xlarge, db\.m4\.10xlarge  | 
|  db\.m3  |  db\.m3\.medium, db\.m3\.large, db\.m3\.xlarge, db\.m3\.2xlarge  | 
|  db\.r3  |  db\.r3\.large, db\.r3\.xlarge, db\.r3\.2xlarge, db\.r3\.4xlarge, db\.r3\.8xlarge  | 
|  db\.t3  |  db\.t3\.micro, db\.t3\.small, db\.t3\.medium, db\.t3\.large  | 
|  db\.t2  |  db\.t2\.micro, db\.t2\.small, db\.t2\.medium, db\.t2\.large  | 

For more information about DB instance classes, see [Hardware Specifications for DB Instance Classes ](Concepts.DBInstanceClass.md#Concepts.DBInstanceClass.Summary)\. 

To enable huge pages for new or existing DB instances manually, set the `use_large_pages` parameter to `ONLY`\. You can't use huge pages with Oracle Automatic Memory Management \(AMM\)\. If you set the parameter `use_large_pages` to `ONLY`, then you must also set both `memory_target` and `memory_max_target` to `0`\. For more information about setting DB parameters for your DB instance, see [Working with DB Parameter Groups](USER_WorkingWithParamGroups.md)\. 

You can also set the `sga_target`, `sga_max_size`, and `pga_aggregate_target` parameters\. When you set system global area \(SGA\) and program global area \(PGA\) memory parameters, add the values together\. Subtract this total from your available instance memory \(`DBInstanceClassMemory`\) to determine the free memory beyond the huge pages allocation\. You must leave free memory of at least 2 GiB, or 10 percent of the total available instance memory, whichever is smaller\. 

After you configure your parameters, you must reboot your DB instance for the changes to take effect\. For more information, see [Rebooting a DB Instance](USER_RebootInstance.md)\. 

**Note**  
The Oracle DB instance defers changes to SGA\-related initialization parameters until you reboot the instance without failover\. In the Amazon RDS console, choose **Reboot** but *do not* choose **Reboot with failover**\. In the AWS CLI, call the `reboot-db-instance` command with the `--no-force-failover` parameter\. The DB instance does not process the SGA\-related parameters during failover or during other maintenance operations that cause the instance to restart\.

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

The parameter group is used by a db\.r4 DB instance class with less than 100 GiB of memory and a db\.r3 instance with more than 100 GiB memory\. With these parameter settings and `use_large_pages` set to `{DBInstanceClassHugePagesDefault}`, huge pages are enabled on the db\.r4 instance, but disabled on the db\.r3 instance\.

Consider another example with following parameters values set in a parameter group\.

```
1. memory_target           = IF({DBInstanceClassHugePagesDefault}, 0, {DBInstanceClassMemory*3/4})
2. memory_max_target       = IF({DBInstanceClassHugePagesDefault}, 0, {DBInstanceClassMemory*3/4})
3. pga_aggregate_target    = IF({DBInstanceClassHugePagesDefault}, {DBInstanceClassMemory*1/8}, 0)
4. sga_target              = IF({DBInstanceClassHugePagesDefault}, {DBInstanceClassMemory*3/4}, 0)
5. sga_max_size            = IF({DBInstanceClassHugePagesDefault}, {DBInstanceClassMemory*3/4}, 0)
6. use_large_pages         = FALSE
```

The parameter group is used by a db\.r4 DB instance class and a db\.r5 DB instance class, both with less than 100 GiB of memory\. The parameter group is also used by a db\.r3 instance with more than 100 GiB memory\. With these parameter settings, huge pages are disabled on the db\.r4 instance, the db\.r5 instance, and the db\.r3 instance\.

**Note**  
If this parameter group is used by a db\.r4 DB instance class or db\.r5 DB instance class with at least 100 GiB of memory, the `FALSE` setting for `use_large_pages` is overridden and set to `ONLY`\. In this case, a customer notification regarding the override is sent\.

After huge pages are active on your DB instance, you can view huge pages information by enabling enhanced monitoring\. For more information, see [Enhanced Monitoring](USER_Monitoring.OS.md)\. 

## Using utl\_http, utl\_tcp, and utl\_smtp with an Oracle DB Instance<a name="Oracle.Concepts.ONA"></a>

Amazon RDS supports outbound network access on your DB instances running Oracle\. You can use `utl_http`, `utl_tcp`, and `utl_smtp` to connect from your DB instance to the network\. 

Note the following about working with outbound network access:
+ To use `utl_http` on DB instances running Oracle 11g, you must install the XMLDB option\. For more information, see [Oracle XML DB](Appendix.Oracle.Options.XMLDB.md)\. 
+ Outbound network access with `utl_http`, `utl_tcp`, and `utl_smtp` is supported only for Oracle DB instances in a VPC\. To determine whether or not your DB instance is in a VPC, see [Determining Whether You Are Using the EC2\-VPC or EC2\-Classic Platform](USER_VPC.FindDefaultVPC.md)\. To move a DB instance not in a VPC into a VPC, see [Moving a DB Instance Not in a VPC into a VPC](USER_VPC.md#USER_VPC.Non-VPC2VPC)\. 
+ To use SMTP with the UTL\_MAIL option, see [Oracle UTL\_MAIL](Oracle.Options.UTLMAIL.md)\.
+ The Domain Name Server \(DNS\) name of the remote host can be any of the following: 
  + Publicly resolvable\.
  + The endpoint of an Amazon RDS DB instance\.
  + Resolvable through a custom DNS server\. For more information, see [Setting Up a Custom DNS Server](Appendix.Oracle.CommonDBATasks.System.md#Appendix.Oracle.CommonDBATasks.CustomDNS)\. 
  + The private DNS name of an Amazon EC2 instance in the same VPC or a peered VPC\. In this case, make sure that the name is resolvable through a custom DNS server\. Alternatively, to use the DNS provided by Amazon, you can enable the `enableDnsSupport` attribute in the VPC settings and enable DNS resolution support for the VPC peering connection\. For more information, see [DNS Support in Your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-dns.html#vpc-dns-support) and [Modifying Your VPC Peering Connection](https://docs.aws.amazon.com/vpc/latest/peering/working-with-vpc-peering.html#modify-peering-connections)\. 

To connect securely to remote SSL/TLS resources, you can create and upload customized Oracle wallets\. By using the Amazon S3 integration with Amazon RDS for Oracle feature, you can download a wallet from Amazon S3 into Oracle DB instances\. For information about Amazon S3 integration for Oracle, see [Amazon S3 Integration](oracle-s3-integration.md)\.

The following example creates a wallet for accessing [ https://status\.aws\.amazon\.com/robots\.txt](https://status.aws.amazon.com/robots.txt) over `utl_http`\.

1. Obtain the certificate for `Amazon Root CA 1` from the [ Amazon Trust Services Repository](https://www.amazontrust.com/repository/)\.

1. Create a new wallet and add the following certificate:

   ```
   orapki wallet create -wallet . -auto_login_only 
   orapki wallet add -wallet . -trusted_cert -cert AmazonRootCA1.pem -auto_login_only 
   orapki wallet display -wallet .
   ```

1. Upload the wallet to your Amazon S3 bucket\.

1. Complete the prerequisites for Amazon S3 integration with Oracle, and add the `S3_INTEGRATION` option to your Oracle DB instance\. Ensure that the IAM role for the option has access to the Amazon S3 bucket you are using\.

   For more information, see [Amazon S3 Integration](oracle-s3-integration.md)\.

1. Connect to the DB instance, and create a directory in the database to hold the wallet\. The following example creates a directory called `SSL_WALLET`:

   ```
   exec rdsadmin.rdsadmin_util.create_directory('SSL_WALLET');                    
   ```

1. Download the wallet from your Amazon S3 bucket to the Oracle DB instance\.

   The following example downloads a wallet to the DB instance directory `SSL_WALLET`:

   ```
   SELECT rdsadmin.rdsadmin_s3_tasks.download_from_s3(
         p_bucket_name    =>  'bucket_name', 
         p_s3_prefix      =>  'wallet_name', 
         p_directory_name =>  'SSL_WALLET') 
      AS TASK_ID FROM DUAL;
   ```

   Replace *bucket\_name* with the name of the bucket you are using, and replace *wallet\_name* with the name of the wallet\.

1. Set this wallet for `utl_http` transactions by running the following procedure:

   ```
   DECLARE
   	l_wallet_path all_directories.directory_path%type;
   BEGIN
   	select directory_path into l_wallet_path from all_directories
   	where upper(directory_name)='SSL_WALLET';
    	
    	utl_http.set_wallet('file:/' || l_wallet_path);
   END;
   ```

1. Access the URL from above over SSL/TLS\.

   ```
   SELECT utl_http.request('https://status.aws.amazon.com/robots.txt') AS ROBOTS_TXT FROM DUAL;
   
   ROBOTS_TXT
   --------------------------------------------------------------------------------
   User-agent: *
   Allow: /
   ```

**Note**  
The specific certificates that are required for your wallet vary by service\. For AWS services, the certificates can typically be found in the [Amazon Trust Services Repository](https://www.amazontrust.com/repository/)\.

You can use a similar setup to send emails through UTL\_SMTP over SSL/TLS \(including [ Amazon Simple Email Service](https://aws.amazon.com/ses/)\)\.

You can establish database links between Oracle DB instances over an SSL/TLS endpoint if the Oracle SSL option is configured for each instance\. No further configuration is required\. For more information, see [Oracle Secure Sockets Layer](Appendix.Oracle.Options.SSL.md)\. 

## Using OEM, APEX, TDE, and Other Options<a name="Oracle.Concepts.General.Options"></a>

Most Amazon RDS DB engines support option groups that allow you to select additional features for your DB instance\. Oracle DB instances support several options, including Oracle Enterprise Manager \(OEM\), Transparent Data Encryption \(TDE\), Application Express \(APEX\), and Native Network Encryption\. For a complete list of supported Oracle options, see [Options for Oracle DB Instances](Appendix.Oracle.Options.md)\. For more information about working with option groups, see [Working with Option Groups](USER_WorkingWithOptionGroups.md)\. 

## Using Extended Data Types<a name="Oracle.Concepts.ExtendedDataTypes"></a>

Amazon RDS Oracle version 12c supports extended data types\. With extended data types, the maximum size is 32,767 bytes for the VARCHAR2, NVARCHAR2, and RAW data types\. To use extended data types, set the `MAX_STRING_SIZE` parameter to `EXTENDED`\. For more information, see [Extended Data Types](https://docs.oracle.com/database/121/SQLRF/sql_elements001.htm#SQLRF55623) in the Oracle documentation\. 

If you don't want to use extended data types, keep the `MAX_STRING_SIZE` parameter set to `STANDARD` \(the default\)\. When this parameter is set to `STANDARD`, the size limits are 4,000 bytes for the VARCHAR2 and NVARCHAR2 data types, and 2,000 bytes for the RAW data type\.

You can enable extended data types on a new or existing DB instance\. For new DB instances, DB instance creation time is typically longer when you enable extended data types\. For existing DB instances, the DB instance is unavailable during the conversion process\.

The following are considerations for a DB instance with extended data types enabled:
+ When you enable extended data types for a DB instance, you can't change the DB instance back to use the standard size for data types\. After a DB instance is converted to use extended data types, if you set the `MAX_STRING_SIZE` parameter back to `STANDARD` it results in the `incompatible-parameters` status\.
+ When you restore a DB instance that uses extended data types, you must specify a parameter group with the `MAX_STRING_SIZE` parameter set to `EXTENDED`\. During restore, if you specify the default parameter group or any other parameter group with `MAX_STRING_SIZE` set to `STANDARD` it results in the `incompatible-parameters` status\.
+ We recommend that you don't enable extended data types for Oracle DB instances running on the t2\.micro DB instance class\.

When the DB instance status is `incompatible-parameters` because of the `MAX_STRING_SIZE` setting, the DB instance remains unavailable until you set the `MAX_STRING_SIZE` parameter to `EXTENDED` and reboot the DB instance\.

### Enabling Extended Data Types for a New DB Instance<a name="Oracle.Concepts.ExtendedDataTypes.CreateDBInstance"></a>

**To enable extended data types for a new DB instance**

1. Set the `MAX_STRING_SIZE` parameter to `EXTENDED` in a parameter group\.

   To set the parameter, you can either create a new parameter group or modify an existing parameter group\.

   For more information, see [Working with DB Parameter Groups](USER_WorkingWithParamGroups.md)\.

1. Create a new Amazon RDS Oracle DB instance, and associate the parameter group with `MAX_STRING_SIZE` set to `EXTENDED` with the DB instance\.

   For more information, see [Creating an Amazon RDS DB Instance](USER_CreateDBInstance.md)\.

### Enabling Extended Data Types for an Existing DB Instance<a name="Oracle.Concepts.ExtendedDataTypes.ModifyDBInstance"></a>

When you modify a DB instance to enable extended data types, the data in the database is converted to use the extended sizes\. The DB instance is unavailable during the conversion\. The amount of time it takes to convert the data depends on the DB instance class used by the DB instance and the size of the database\.

**Note**  
After you enable extended data types, you can't perform a point\-in\-time restore to a time during the conversion\. You can restore to the time immediately before the conversion or after the conversion\.

**To enable extended data types for an existing DB instance**

1. Take a snapshot of the database\.

   If there are invalid objects in the database, Amazon RDS tries to recompile them\. The conversion to extended data types can fail if Amazon RDS can't recompile an invalid object\. The snapshot enables you to restore the database if there is a problem with the conversion\. Always check for invalid objects before conversion and fix or drop those invalid objects\. For production databases, we recommend testing the conversion process on a copy of your DB instance first\.

   For more information, see [Creating a DB Snapshot](USER_CreateSnapshot.md)\.

1. Set the `MAX_STRING_SIZE` parameter to `EXTENDED` in a parameter group\.

   To set the parameter, you can either create a new parameter group or modify an existing parameter group\.

   For more information, see [Working with DB Parameter Groups](USER_WorkingWithParamGroups.md)\.

1. Modify the DB instance to associate it with the parameter group with `MAX_STRING_SIZE` set to `EXTENDED`\.

   For more information, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md)\.

1. Reboot the DB instance for the parameter change to take effect\.

   For more information, see [Rebooting a DB Instance](USER_RebootInstance.md)\.

## Public Synonyms<a name="Oracle.Concepts.PublicSynonyms"></a>

You can create public synonyms referencing objects in your own schemas\. Don't create or modify public synonyms for Oracle\-maintained schemas, including `SYS`, `SYSTEM`, and `RDSADMIN`\. Doing so might result in invalidation of core database components and affect the availability of your DB instance\.