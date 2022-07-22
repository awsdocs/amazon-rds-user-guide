# Using the Oracle Repository Creation Utility on RDS for Oracle<a name="Oracle.Resources.RCU"></a>

You can use Amazon RDS to host an RDS for Oracle DB instance that holds the schemas to support your Oracle Fusion Middleware components\. Before you can use Fusion Middleware components, create and populate schemas for them in your database\. You create and populate the schemas by using the Oracle Repository Creation Utility \(RCU\)\.

## Supported versions and licensing options for RCU<a name="Oracle.Resources.RCU.Versions"></a>

Amazon RDS supports Oracle Repository Creation Utility \(RCU\) version 12c only\. You can use the RCU in the following configurations: 
+ RCU 12c with Oracle Database 21c
+ RCU 12c with Oracle Database 19c
+ RCU 12c with Oracle Database 12c Release 2 \(12\.2\)
+ RCU 12c with Oracle Database 12c Release 1 \(12\.1\) using 12\.1\.0\.2\.v4 or higher

Before you can use RCU, make sure that you do the following:
+ Obtain a license for Oracle Fusion Middleware\.
+ Follow the Oracle licensing guidelines for the Oracle database that hosts the repository\. For more information, see [ Oracle Fusion Middleware Licensing Information User Manual](https://docs.oracle.com/en/middleware/fusion-middleware/fmwlc/) in the Oracle documentation\.

Fusion MiddleWare supports repositories on Oracle Database Enterprise Edition and Standard Edition Two\. Oracle recommends Enterprise Edition for production installations that require partitioning and installations that require online index rebuild\.

Before you create your RDS for Oracle instance, confirm the Oracle database version that you need to support the components that you want to deploy\. To find the requirements for the Fusion Middleware components and versions you want to deploy, use the Certification Matrix\. For more information, see [Oracle Fusion Middleware Supported System Configurations](http://www.oracle.com/technetwork/middleware/ias/downloads/fusion-certification-100350.html) in the Oracle documentation\. 

Amazon RDS supports Oracle database version upgrades as needed\. For more information, see [Upgrading a DB instance engine version](USER_UpgradeDBInstance.Upgrading.md)\. 

## Requirements and limitations for RCU<a name="Oracle.Resources.RCU.BeforeYouBegin"></a>

To use RCU, you need an Amazon VPC\. Your Amazon RDS DB instance must be available only to your Fusion Middleware components, and not to the public Internet\. Thus, host your Amazon RDS DB instance in a private subnet, which provides greater security\. For information about how to create an Amazon VPC for use with an RDS for Oracle instance, see [Creating a VPC for use with an Oracle database](Oracle.Resources.Shared.md#Oracle.Resources.Shared.VPC)\. 

You also need an RDS for Oracle DB instance\. For information about how to create an RDS for Oracle DB instance for use with Fusion Middleware metadata, see [Creating an Oracle DB instance](Oracle.Resources.Shared.md#Oracle.Resources.Shared.Database.RDS)\. 

You can store the schemas for any Fusion Middleware components in your Amazon RDS DB instance\. The following schemas have been verified to install correctly: 
+ Analytics \(ACTIVITIES\)
+ Audit Services \(IAU\)
+ Audit Services Append \(IAU\_APPEND\)
+ Audit Services Viewer \(IAU\_VIEWER\)
+ Discussions \(DISCUSSIONS\)
+ Metadata Services \(MDS\)
+ Oracle Business Intelligence \(BIPLATFORM\)
+ Oracle Platform Security Services \(OPSS\)
+ Portal and Services \(WEBCENTER\)
+ Portlet Producers \(PORTLET\)
+ Service Table \(STB\)
+ SOA Infrastructure \(SOAINFRA\)
+ User Messaging Service \(UCSUMS\)
+ WebLogic Services \(WLS\)

## Guidelines for using RCU<a name="Oracle.Resources.RCU.Recommendations"></a>

The following are some recommendations for working with your DB instance in this scenario: 
+ We recommend that you use Multi\-AZ for production workloads\. For more information about working with multiple Availability Zones, see [Regions, Availability Zones, and Local Zones](Concepts.RegionsAndAvailabilityZones.md)\. 
+ For additional security, Oracle recommends that you use Transparent Data Encryption \(TDE\) to encrypt your data at rest\. If you have an Enterprise Edition license that includes the Advanced Security Option, you can enable encryption at rest by using the TDE option\. For more information, see [Oracle Transparent Data Encryption](Appendix.Oracle.Options.AdvSecurity.md)\. 

  Amazon RDS also provides an encryption at rest option for all database editions\. For more information, see [Encrypting Amazon RDS resources](Overview.Encryption.md)\. 
+ Configure your VPC Security Groups to allow communication between your application servers and your Amazon RDS DB instance\. The application servers that host the Fusion Middleware components can be on Amazon EC2 or on\-premises\. 

## Running RCU<a name="Oracle.Resources.RCU.Installing"></a>

To create and populate the schemas to support your Fusion Middleware components, use the Oracle Repository Creation Utility \(RCU\)\. You can run RCU in different ways\.

**Topics**
+ [Running RCU using the command line in one step](#Oracle.Resources.RCU.SilentSingle)
+ [Running RCU using the command line in multiple steps](#Oracle.Resources.RCU.SilentMulti)
+ [Running RCU in interactive mode](#Oracle.Resources.RCU.Interactive)

### Running RCU using the command line in one step<a name="Oracle.Resources.RCU.SilentSingle"></a>

If you don't need to edit any of your schemas before populating them, you can run RCU in a single step\. Otherwise, see the following section for running RCU in multiple steps\. 

You can run the RCU in silent mode by using the command\-line parameter `-silent`\. When you run RCU in silent mode, you can avoid typing passwords on the command line by creating a text file containing the passwords\. Create a text file with the password for `dbUser` on the first line, and the password for each component on subsequent lines\. You specify the name of the password file as the last parameter to the RCU command\. 

**Example**  
The following example creates and populates schemas for the SOA Infrastructure component \(and its dependencies\) in a single step\.   
For Linux, macOS, or Unix:  

```
export ORACLE_HOME=/u01/app/oracle/product/12.2.1.0/fmw
export JAVA_HOME=/usr/java/jdk1.8.0_65
${ORACLE_HOME}/oracle_common/bin/rcu \
-silent \
-createRepository \
-connectString ${dbhost}:${dbport}:${dbname} \
-dbUser ${dbuser} \
-dbRole Normal \
-honorOMF \
-schemaPrefix ${SCHEMA_PREFIX} \
-component MDS \
-component STB \
-component OPSS \
-component IAU \
-component IAU_APPEND \
-component IAU_VIEWER \
-component UCSUMS \
-component WLS \
-component SOAINFRA \
-f < /tmp/passwordfile.txt
```

For more information, see [ Running Repository Creation Utility from the command line](https://docs.oracle.com/middleware/1221/core/RCUUG/GUID-0D3A2959-7CC8-4001-997E-718ADF04C5F2.htm#RCUUG248) in the Oracle documentation\. 

### Running RCU using the command line in multiple steps<a name="Oracle.Resources.RCU.SilentMulti"></a>

To manually edit your schema scripts, run RCU in multiple steps: 

1. Run RCU in **Prepare Scripts for System Load** mode by using the `-generateScript` command\-line parameter to create the scripts for your schemas\. 

1. Manually edit and run the generated script `script_systemLoad.sql`\. 

1. Run RCU again in **Perform Product Load** mode by using the `-dataLoad` command\-line parameter to populate the schemas\. 

1. Run the generated cleanup script `script_postDataLoad.sql`\.

To run RCU in silent mode, specify the command\-line parameter `-silent`\. When you run RCU in silent mode, you can avoid typing passwords on the command line by creating a text file containing the passwords\. Create a text file with the password for `dbUser` on the first line, and the password for each component on subsequent lines\. Specify the name of the password file as the last parameter to the RCU command\. 

**Example**  
The following example creates schema scripts for the SOA Infrastructure component and its dependencies\.   
For Linux, macOS, or Unix:  

```
export ORACLE_HOME=/u01/app/oracle/product/12.2.1.0/fmw
export JAVA_HOME=/usr/java/jdk1.8.0_65
${ORACLE_HOME}/oracle_common/bin/rcu \
-silent \
-generateScript \
-connectString ${dbhost}:${dbport}:${dbname} \
-dbUser ${dbuser} \
-dbRole Normal \
-honorOMF \
[-encryptTablespace true] \
-schemaPrefix ${SCHEMA_PREFIX} \
-component MDS \
-component STB \
-component OPSS \
-component IAU \
-component IAU_APPEND \
-component IAU_VIEWER \
-component UCSUMS \
-component WLS \
-component SOAINFRA \
-scriptLocation /tmp/rcuscripts \
-f < /tmp/passwordfile.txt
```
Now you can edit the generated script, connect to your Oracle DB instance, and run the script\. The generated script is named `script_systemLoad.sql`\. For information about connecting to your Oracle DB instance, see [Connecting to your sample Oracle DB instance](CHAP_GettingStarted.CreatingConnecting.Oracle.md#CHAP_GettingStarted.Connecting.Oracle)\.   
The following example populates the schemas for the SOA Infrastructure component \(and its dependencies\)\.   
For Linux, macOS, or Unix:  

```
export JAVA_HOME=/usr/java/jdk1.8.0_65
${ORACLE_HOME}/oracle_common/bin/rcu \
-silent \
-dataLoad \
-connectString ${dbhost}:${dbport}:${dbname} \
-dbUser ${dbuser} \
-dbRole Normal \
-honorOMF \
-schemaPrefix ${SCHEMA_PREFIX} \
-component MDS \
-component STB \
-component OPSS \
-component IAU \
-component IAU_APPEND \
-component IAU_VIEWER \
-component UCSUMS \
-component WLS \
-component SOAINFRA \
-f < /tmp/passwordfile.txt
```
To finish, you connect to your Oracle DB instance, and run the clean\-up script\. The script is named `script_postDataLoad.sql`\. 

For more information, see [ Running Repository Creation Utility from the command line](https://docs.oracle.com/middleware/1221/core/RCUUG/GUID-0D3A2959-7CC8-4001-997E-718ADF04C5F2.htm#RCUUG248) in the Oracle documentation\. 

### Running RCU in interactive mode<a name="Oracle.Resources.RCU.Interactive"></a>

To use the RCU graphical user interface, run RCU in interactive mode\. Include the `-interactive` parameter and omit the `-silent` parameter\. For more information, see [ Understanding Repository Creation Utility screens](https://docs.oracle.com/middleware/1213/core/RCUUG/rcu_screens.htm#RCUUG143) in the Oracle documentation\. 

**Example**  
The following example starts RCU in interactive mode and pre\-populates the connection information\.   
For Linux, macOS, or Unix:  

```
export ORACLE_HOME=/u01/app/oracle/product/12.2.1.0/fmw
export JAVA_HOME=/usr/java/jdk1.8.0_65
${ORACLE_HOME}/oracle_common/bin/rcu \
-interactive \
-createRepository \
-connectString ${dbhost}:${dbport}:${dbname} \
-dbUser ${dbuser} \
-dbRole Normal
```

## Troubleshooting RCU<a name="Oracle.Resources.RCU.KnownIssues"></a>

Be mindful of the following issues\.

**Topics**
+ [Oracle Managed Files \(OMF\)](#Oracle.Resources.RCU.KnownIssues.OMF)
+ [Object privileges](#Oracle.Resources.RCU.KnownIssues.object-privs)
+ [Enterprise Scheduler Service](#Oracle.Resources.RCU.KnownIssues.Scheduler)

### Oracle Managed Files \(OMF\)<a name="Oracle.Resources.RCU.KnownIssues.OMF"></a>

Amazon RDS uses OMF data files to simplify storage management\. You can customize tablespace attributes, such as size and extent management\. However, if you specify a data file name when you run RCU, the tablespace code fails with `ORA-20900`\. You can use RCU with OMF in the following ways: 
+ In RCU 12\.2\.1\.0 and later, use the `-honorOMF` command\-line parameter\. 
+ In RCU 12\.1\.0\.3 and later, use multiple steps and edit the generated script\. For more information, see [Running RCU using the command line in multiple steps](#Oracle.Resources.RCU.SilentMulti)\. 

### Object privileges<a name="Oracle.Resources.RCU.KnownIssues.object-privs"></a>

Because Amazon RDS is a managed service, you don't have full `SYSDBA` access to your RDS for Oracle DB instance\. However, RCU 12c supports users with lower privileges\. In most cases, the master user privilege is sufficient to create repositories\. 

The master account can directly grant privileges that it has already been granted `WITH GRANT OPTION`\. In some cases, when you attempt to grant `SYS` object privileges, the RCU might fail with `ORA-01031`\. You can retry and run the `rdsadmin_util.grant_sys_object` stored procedure, as shown in the following example:

```
BEGIN
  rdsadmin.rdsadmin_util.grant_sys_object('GV_$SESSION','MY_DBA','SELECT');
END;
/
```

If you attempt to grant `SYS` privileges on the object `SCHEMA_VERSION_REGISTRY`, the operation might fail with `ORA-20199: Error in rdsadmin_util.grant_sys_object`\. You can qualify the table `SCHEMA_VERSION_REGISTRY$` and the view `SCHEMA_VERSION_REGISTRY` with the schema owner name, which is `SYSTEM`, and retry the operation\. Or, you can create a synonym\. Log in as the master user and run the following statements:

```
CREATE OR REPLACE VIEW SYSTEM.SCHEMA_VERSION_REGISTRY 
  AS SELECT * FROM SYSTEM.SCHEMA_VERSION_REGISTRY$;
CREATE OR REPLACE PUBLIC SYNONYM SCHEMA_VERSION_REGISTRY FOR SYSTEM.SCHEMA_VERSION_REGISTRY;
CREATE OR REPLACE PUBLIC SYNONYM SCHEMA_VERSION_REGISTRY$ FOR SCHEMA_VERSION_REGISTRY;
```

### Enterprise Scheduler Service<a name="Oracle.Resources.RCU.KnownIssues.Scheduler"></a>

When you use the RCU to drop an Enterprise Scheduler Service repository, the RCU might fail with `Error: Component drop check failed`\.