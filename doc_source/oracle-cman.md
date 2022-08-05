# Configuring Oracle Connection Manager on an Amazon EC2 instance<a name="oracle-cman"></a>

Oracle Connection Manager \(CMAN\) is a proxy server that forwards connection requests to database servers or other proxy servers\. You can use CMAN to configure the following:

Access control  
You can create rules that filter out user\-specified client requests and accept others\.

Session multiplexing  
You can funnel multiple client sessions through a network connection to a shared server destination\.

Typically, CMAN resides on a host separate from the database server and client hosts\. For more information, see [Configuring Oracle Connection Manager](https://docs.oracle.com/en/database/oracle/oracle-database/19/netag/configuring-oracle-connection-manager.html#GUID-AF8A511E-9AE6-4F4D-8E58-F28BC53F64E4) in the Oracle Database documentation\.

**Topics**
+ [Supported versions and licensing options for CMAN](#oracle-cman.Versions)
+ [Requirements and limitations for CMAN](#oracle-cman.requirements)
+ [Configuring CMAN](#oracle-cman.configuring-cman)

## Supported versions and licensing options for CMAN<a name="oracle-cman.Versions"></a>

CMAN supports the Enterprise Edition of all versions of Oracle Database that Amazon RDS supports\. For more information, see [RDS for Oracle releases](Oracle.Concepts.database-versions.md)\.

You can install Oracle Connection Manager on a separate host from the host where Oracle Database is installed\. You don't need a separate license for the host that runs CMAN\.

## Requirements and limitations for CMAN<a name="oracle-cman.requirements"></a>

To provide a fully managed experience, Amazon RDS restricts access to the operating system\. You can't modify database parameters that require operating system access\. Thus, Amazon RDS doesn't support features of CMAN that require you to log in to the operating system\.

## Configuring CMAN<a name="oracle-cman.configuring-cman"></a>

When you configure CMAN, you perform most of the work outside of your RDS for Oracle database\.

**Topics**
+ [Step 1: Configure CMAN on an Amazon EC2 instance in the same VPC as the RDS for Oracle instance](#oracle-cman.configuring-cman.vpc)
+ [Step 2: Configure database parameters for CMAN](#oracle-cman.configuring-cman.parameters)
+ [Step 3: Associate your DB instance with the parameter group](#oracle-cman.configuring-cman.parameter-group)

### Step 1: Configure CMAN on an Amazon EC2 instance in the same VPC as the RDS for Oracle instance<a name="oracle-cman.configuring-cman.vpc"></a>

To learn how to set up CMAN, follow the detailed instructions in the blog post [Configuring and using Oracle Connection Manager on Amazon EC2 for Amazon RDS for Oracle](http://aws.amazon.com/blogs/database/configuring-and-using-oracle-connection-manager-on-amazon-ec2-for-amazon-rds-for-oracle/)\.

### Step 2: Configure database parameters for CMAN<a name="oracle-cman.configuring-cman.parameters"></a>

For CMAN features such as Traffic Director Mode and session multiplexing, set `REMOTE_LISTENER` parameter to the address of CMAN instance in a DB parameter group\. Consider the following scenario:
+ The CMAN instance resides on a host with IP address `10.0.159.100` and uses port `1521`\.
+ The databases `orcla`, `orclb`, and `orclc` reside on separate RDS for Oracle DB instances\.

The following table shows how to set the `REMOTE_LISTENER` value\. The `LOCAL_LISTENER` value is set automatically by Amazon RDS\.


| DB instance name | DB instance IP | Local listener value \(set automatically\) | Remote listener value \(set by user\) | 
| --- | --- | --- | --- | 
| orcla | 10\.0\.159\.200 |  <pre>( address=<br />  (protocol=tcp)<br />  (host=10.0.159.200)<br />  (port=1521)<br />)</pre>  | 10\.0\.159\.100:1521 | 
| orclb | 10\.0\.159\.300 |  <pre>( address=<br />  (protocol=tcp)<br />  (host=10.0.159.300)<br />  (port=1521)<br />)</pre>  | 10\.0\.159\.100:1521 | 
| orclc | 10\.0\.159\.400 |  <pre>( address=<br />  (protocol=tcp)<br />  (host=10.0.159.400)<br />  (port=1521)<br />)</pre>  | 10\.0\.159\.100:1521 | 

### Step 3: Associate your DB instance with the parameter group<a name="oracle-cman.configuring-cman.parameter-group"></a>

Create or modify your DB instance to use the parameter group that you configured in [Step 2: Configure database parameters for CMAN](#oracle-cman.configuring-cman.parameters)\. For more information, see [Associating a DB parameter group with a DB instance](USER_WorkingWithDBInstanceParamGroups.md#USER_WorkingWithParamGroups.Associating)\.