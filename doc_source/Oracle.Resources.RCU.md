# Using the Oracle Repository Creation Utility on Amazon RDS for Oracle<a name="Oracle.Resources.RCU"></a>

You can use Amazon RDS to host an Oracle DB instance that holds the schemas to support your Fusion Middleware components\. Before you can use Fusion Middleware components, you must create and populate schemas for them in your database\. You create and populate the schemas by using the Oracle Repository Creation Utility \(RCU\)\. 

You can store the schemas for any Fusion Middleware components in your Amazon RDS DB instance\. The following is a list of schemas that have been verified to install correctly: 
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

## Licensing and Versions<a name="Oracle.Resources.RCU.Versions"></a>

Amazon RDS supports Oracle Repository Creation Utility \(RCU\) version 12c only\. You can use the RCU in the following configurations: 
+ RCU 12c with Oracle database 12\.2\.0\.1
+ RCU 12c with Oracle database 12\.1\.0\.2\.v4 or later
+ RCU 12c with Oracle database 11\.2\.0\.4\.v8 or later

Before you can use RCU, you need a license for Oracle Fusion Middleware\. You also need to follow the Oracle licensing guidelines for the Oracle database that hosts the repository\. For more information, see [ Oracle Fusion Middleware Licensing Information User Manual ](https://docs.oracle.com/cd/E55108_01/doc.62016/e56762/toc.htm) in the Oracle documentation\. 

Fusion MiddleWare supports repositories on Oracle Database Enterprise Edition and Standard Editions \(SE, SE One, or SE Two\)\. Oracle recommends Enterprise Edition for production installations that require partitioning and installations that require online index rebuild\. 

Before you create your Oracle DB instance, confirm the Oracle database version that you need to support the components that you want to deploy\. You can use the Certification Matrix to find the requirements for the Fusion Middleware components and versions you want to deploy\. For more information, see [ Oracle Fusion Middleware Supported System Configurations](http://www.oracle.com/technetwork/middleware/ias/downloads/fusion-certification-100350.html) in the Oracle documentation\. 

Amazon RDS supports Oracle database version upgrades as needed\. For more information, see [Upgrading a DB Instance Engine Version](USER_UpgradeDBInstance.Upgrading.md)\. 

## Before You Begin<a name="Oracle.Resources.RCU.BeforeYouBegin"></a>

Before you begin, you need an Amazon VPC\. Because your Amazon RDS DB instance needs to be available only to your Fusion Middleware components, and not to the public Internet, your Amazon RDS DB instance is hosted in a private subnet, providing greater security\. For information about how to create an Amazon VPC for use with an Oracle DB instance, see [Creating a VPC for Use with an Oracle Database](Oracle.Resources.Shared.md#Oracle.Resources.Shared.VPC)\. 

Before you begin, you also need an Oracle DB instance\. For information about how to create an Oracle DB instance for use with Fusion Middleware metadata, see [Creating an Oracle DB Instance](Oracle.Resources.Shared.md#Oracle.Resources.Shared.Database.RDS)\. 

## Recommendations<a name="Oracle.Resources.RCU.Recommendations"></a>

The following are some recommendations for working with your DB instance in this scenario: 
+ We recommend that you use Multi\-AZ for production workloads\. For more information about working with multiple Availability Zones, see [ Regions, Availability Zones, and Local Zones ](Concepts.RegionsAndAvailabilityZones.md)\. 
+ For additional security, Oracle recommends that you use Transparent Data Encryption \(TDE\) to encrypt your data at rest\. If you have an Enterprise Edition license that includes the Advanced Security Option, you can enable encryption at rest by using the TDE option\. For more information, see [Oracle Transparent Data Encryption](Appendix.Oracle.Options.AdvSecurity.md)\. 

  Amazon RDS also provides an encryption at rest option for all database editions\. For more information, see [Encrypting Amazon RDS Resources](Overview.Encryption.md)\. 
+ Configure your VPC Security Groups to allow communication between your application servers and your Amazon RDS DB instance\. The application servers that host the Fusion Middleware components can be on Amazon EC2 or on\-premises\. 

## Using the Oracle Repository Creation Utility<a name="Oracle.Resources.RCU.Installing"></a>

You use the Oracle Repository Creation Utility \(RCU\) to create and populate the schemas to support your Fusion Middleware components\. 

### Running RCU Using the Command Line in One Step<a name="Oracle.Resources.RCU.SilentSingle"></a>

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

For more information, see [ Running Repository Creation Utility from the Command Line](https://docs.oracle.com/middleware/1221/core/RCUUG/GUID-0D3A2959-7CC8-4001-997E-718ADF04C5F2.htm#RCUUG248) in the Oracle documentation\. 

### Running RCU Using the Command Line in Multiple Steps<a name="Oracle.Resources.RCU.SilentMulti"></a>

If you need to manually edit your schema scripts, you can run the RCU in multiple steps: 

1. Run RCU in **Prepare Scripts for System Load** mode by using the `-generateScript` command\-line parameter to create the scripts for your schemas\. 

1. Manually edit and run the generated script `script_systemLoad.sql`\. 

1. Run RCU again in **Perform Product Load** mode by using the `-dataLoad` command\-line parameter to populate the schemas\. 

1. Run the generated clean\-up script `script_postDataLoad.sql`\.

You can run the RCU in silent mode by using the command\-line parameter `-silent`\. When you run RCU in silent mode, you can avoid typing passwords on the command line by creating a text file containing the passwords\. Create a text file with the password for `dbUser` on the first line, and the password for each component on subsequent lines\. You specify the name of the password file as the last parameter to the RCU command\. 

**Example**  
The following example creates schema scripts for the SOA Infrastructure component \(and its dependencies\)\.   
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
Now you can edit the generated script, connect to your Oracle DB instance, and run the script\. The generated script is named `script_systemLoad.sql`\. For information about connecting to your Oracle DB instance, see [Connecting to Your Sample Oracle DB Instance](CHAP_GettingStarted.CreatingConnecting.Oracle.md#CHAP_GettingStarted.Connecting.Oracle)\.   
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

For more information, see [ Running Repository Creation Utility from the Command Line](https://docs.oracle.com/middleware/1221/core/RCUUG/GUID-0D3A2959-7CC8-4001-997E-718ADF04C5F2.htm#RCUUG248) in the Oracle documentation\. 

### Running RCU in Interactive Mode<a name="Oracle.Resources.RCU.Interactive"></a>

To use the RCU graphical user interface, you can run RCU in interactive mode\. To run RCU in interactive mode, include the `-interactive` parameter and omit the `-silent` parameter\. For more information, see [ Understanding Repository Creation Utility Screens](https://docs.oracle.com/middleware/1213/core/RCUUG/rcu_screens.htm#RCUUG143) in the Oracle documentation\. 

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

## Known Issues<a name="Oracle.Resources.RCU.KnownIssues"></a>

The following are some known issues for working with RCU, with some troubleshooting suggestions: 
+ Oracle Managed Files \(OMF\) — Amazon RDS uses OMF data files to simplify storage management\. You can customize tablespace attributes, such as size and extent management\. However, specifying a data file name when you run RCU causes tablespace code to fail with `ORA-20900`\. The RCU can be used with OMF in the following ways: 
  + In RCU 12\.2\.1\.0 and later, use the `-honorOMF` command\-line parameter\. 
  + In RCU 12\.1\.0\.3 and later, use multiple steps and edit the generated script\. For more information, see [Running RCU Using the Command Line in Multiple Steps](#Oracle.Resources.RCU.SilentMulti)\. 
+ SYSDBA — Because Amazon RDS is a managed service, you don't have full SYSDBA access to your Oracle DB instance\. However, RCU 12c supports users with lower privileges\. In most cases, the master user privilege is sufficient to create repositories\. In some cases, the RCU might fail with `ORA-01031` when attempting to grant SYS object privileges\. You can retry and run the RDSADMIN\_UTIL\.GRANT\_SYS\_OBJECT\(\) stored procedure, or contact AWS Support\. 
+ Dropping Enterprise Scheduler Service — When you use the RCU to drop an Enterprise Scheduler Service repository, the RCU might fail with `Error: Component drop check failed`\. 

## Related Topics<a name="w121aac31d103c15c19"></a>
+ [Oracle Licensing](CHAP_Oracle.md#Oracle.Concepts.Licensing)