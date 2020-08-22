# Support for Microsoft Distributed Transaction Coordinator in SQL Server<a name="Appendix.SQLServer.Options.MSDTC"></a>

A *distributed transaction* is a database transaction in which two or more network hosts are involved\. Amazon RDS for SQL Server supports distributed transactions among hosts, where a single host can be one of the following:
+ RDS for SQL Server DB instance
+ On\-premises SQL Server host
+ Amazon EC2 host with SQL Server installed
+ Any other EC2 host or RDS DB instance with a database engine that supports distributed transactions

In RDS, starting with SQL Server 2012 \(version 11\.00\.5058\.0\.v1 and later\), all editions of SQL Server support distributed transactions\. The support is provided using Microsoft Distributed Transaction Coordinator \(MSDTC\)\. For in\-depth information about MSDTC, see [Distributed Transaction Coordinator](https://docs.microsoft.com/en-us/previous-versions/windows/desktop/ms684146(v=vs.85)) in the Microsoft documentation\.

## Limitations<a name="Appendix.SQLServer.Options.MSDTC.Limitations"></a>

The following limitations apply to using MSDTC on RDS for SQL Server:
+ MSDTC isn't supported on instances using SQL Server Database Mirroring\. For more information, see [Transactions \- availability groups and database mirroring](https://docs.microsoft.com/en-us/sql/database-engine/availability-groups/windows/transactions-always-on-availability-and-database-mirroring?view=sql-server-ver15#non-support-for-distributed-transactions)\.
+ The `in-doubt xact resolution` parameter must be set to 1 or 2\. For more information, see [Modifying the Parameter for MSDTC](#ModifyParam.MSDTC)\.
+ MSDTC requires all host names participating in distributed transactions to be resolvable using their computer names\. RDS automatically maintains this functionality for domain\-joined instances\. However, for standalone instances make sure to configure the DNS server manually\.
+ Distributed transactions that depend on client dynamic link libraries \(DLLs\) on RDS instances aren't supported\.

## Enabling MSDTC<a name="Appendix.SQLServer.Options.MSDTC.Enabling"></a>

Use the following process to enable MSDTC for your DB instance:

1. Create a new option group, or choose an existing option group\.

1. Add the `MSDTC` option to the option group\.

1. Create a new parameter group, or choose an existing parameter group\.

1. Modify the parameter group to set the `in-doubt xact resolution` parameter to 1 or 2\.

1. Associate the option group and parameter group with the DB instance\.

### Creating the Option Group for MSDTC<a name="Appendix.SQLServer.Options.MSDTC.OptionGroup"></a>

Use the AWS Management Console or the AWS CLI to create an option group that corresponds to the SQL Server engine and version of your DB instance\.

**Note**  
You can also use an existing option group if it's for the correct SQL Server engine and version\.

#### Console<a name="OptionGroup.MSDTC.Console"></a>

The following procedure creates an option group for SQL Server Standard Edition 2016\.

**To create the option group**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Option groups**\.

1. Choose **Create group**\.

1. In the **Create option group** pane, do the following:

   1. For **Name**, enter a name for the option group that is unique within your AWS account, such as **msdtc\-se\-2016**\. The name can contain only letters, digits, and hyphens\.

   1. For **Description**, enter a brief description of the option group, such as **MSDTC option group for SQL Server SE 2016**\. The description is used for display purposes\. 

   1. For **Engine**, choose **sqlserver\-se**\.

   1. For **Major engine version**, choose **13\.00**\.

1. Choose **Create**\.

#### CLI<a name="OptionGroup.MSDTC.CLI"></a>

The following example creates an option group for SQL Server Standard Edition 2016\.

**To create the option group**
+ Use one of the following commands\.  
**Example**  

  For Linux, macOS, or Unix:

  ```
  aws rds create-option-group \
      --option-group-name msdtc-se-2016 \
      --engine-name sqlserver-se \
      --major-engine-version 13.00 \
      --option-group-description "MSDTC option group for SQL Server SE 2016"
  ```

  For Windows:

  ```
  aws rds create-option-group ^
      --option-group-name msdtc-se-2016 ^
      --engine-name sqlserver-se ^
      --major-engine-version 13.00 ^
      --option-group-description "MSDTC option group for SQL Server SE 2016"
  ```

### Adding the MSDTC Option to the Option Group<a name="Appendix.SQLServer.Options.MSDTC.Add"></a>

Next, use the AWS Management Console or the AWS CLI to add the `MSDTC` option to the option group\.

The following option settings are required:
+ **Port** – The port that you use to access MSDTC\. Allowed values are 1150–49151 except for 1234, 1434, 3260, 3343, 3389, and 47001\. The default value is 5000\.

  Make sure that the port you want to use is enabled in your firewall rules\. Also, make sure as needed that this port is enabled in the inbound and outbound rules for the security group associated with your DB instance\. For more information, see [Can't Connect to Amazon RDS DB Instance](CHAP_Troubleshooting.md#CHAP_Troubleshooting.Connecting)\. 
+ **Security groups** – The VPC or DB security group memberships for your RDS DB instance\.
+ **Authentication type** – The authentication mode between hosts\. The following authentication types are supported:
  + Mutual – The RDS instances are mutually authenticated to each other using integrated authentication\. If this option is selected, all instances associated with this option group must be domain\-joined\.
  + None – No authentication is performed between hosts\. We don't recommend using this mode in production environments\.
+ **Transaction log size** – The size of the MSDTC transaction log\. Allowed values are 4–1024 MB\. The default size is 4 MB\.

The following option settings are optional:
+ **Enable inbound connections** – Whether to allow inbound MSDTC connections to instances associated with this option group\.
+ **Enable outbound connections** – Whether to allow outbound MSDTC connections from instances associated with this option group\.
+ **Enable XA** – Whether to allow XA transactions\. For more information on the XA protocol, see [XA Specification](https://publications.opengroup.org/c193)\.
**Note**  
Using custom XA dynamic link libraries isn't supported\.
+ **Enable SNA LU** – Whether to allow the SNA LU protocol to be used for distributed transactions\. For more information on SNA LU protocol support, see [Managing IBM CICS LU 6\.2 Transactions](https://docs.microsoft.com/en-us/previous-versions/windows/desktop/ms685136(v=vs.85)) in the Microsoft documentation\.

#### Console<a name="Options.MSDTC.Add.Console"></a>

**To add the MSDTC option**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Option groups**\.

1. Choose the option group that you just created\.

1. Choose **Add option**\.

1. Under **Option details**, choose **MSDTC** for **Option name**\.

1. Under **Option settings**:

   1. For **Port**, enter the port number for accessing MSDTC\. The default is **5000**\.

   1. For **Security groups**, choose the VPC or DB security group to associate with the option\.

   1. For **Authentication type**, choose **Mutual** or **None**\.

   1. For **Transaction log size**, enter a value from 4–1024\. The default is **4**\.

1. Under **Additional configuration**, do the following:

   1. For **Connections**, as needed choose **Enable inbound connections** and **Enable outbound connections**\.

   1. For **Allowed protocols**, as needed choose **Enable XA** and **Enable SNA LU**\.

1. Under **Scheduling**, choose whether to add the option immediately or at the next maintenance window\.

1. Choose **Add option**\.

   To add this option, no reboot is required\.

#### CLI<a name="Options.MSDTC.Add.CLI"></a>

**To add the MSDTC option**

1. Create a JSON file, for example `msdtc-option.json`, with the following required parameters\.

   ```
   {
   "OptionGroupName":"msdtc-se-2016",
   "OptionsToInclude": [
   	{
   	"OptionName":"MSDTC",
   	"Port":5000,
   	"VpcSecurityGroupMemberships":["sg-0abcdef123"],
   	"OptionSettings":[{"Name":"AUTHENTICATION","Value":"MUTUAL"},{"Name":"TRANSACTION_LOG_SIZE","Value":4}]
   	}],
   "ApplyImmediately": true
   }
   ```

1. Add the `MSDTC` option to the option group\.  
**Example**  

   For Linux, macOS, or Unix:

   ```
   aws rds add-option-to-option-group \
       --cli-input-json file://msdtc-option.json \
       --apply-immediately
   ```

   For Windows:

   ```
   aws rds add-option-to-option-group ^
       --cli-input-json file://msdtc-option.json ^
       --apply-immediately
   ```

   No reboot is required\.

### Creating the Parameter Group for MSDTC<a name="MSDTC.CreateParamGroup"></a>

Create or modify a parameter group for the `in-doubt xact resolution` parameter that corresponds to the SQL Server edition and version of your DB instance\.

#### Console<a name="CreateParamGroup.MSDTC.Console"></a>

The following example creates a parameter group for SQL Server Standard Edition 2016\.

**To create the parameter group**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Parameter groups**\.

1. Choose **Create parameter group**\.

1. In the **Create parameter group** pane, do the following:

   1. For **Parameter group family**, choose **sqlserver\-se\-13\.0**\.

   1. For **Group name**, enter an identifier for the parameter group, such as **msdtc\-sqlserver\-se\-13**\.

   1. For **Description**, enter **in\-doubt xact resolution**\.

1. Choose **Create**\.

#### CLI<a name="CreateParamGroup.MSDTC.CLI"></a>

The following example creates a parameter group for SQL Server Standard Edition 2016\.

**To create the parameter group**
+ Use one of the following commands\.  
**Example**  

  For Linux, macOS, or Unix:

  ```
  aws rds create-db-parameter-group \
      --db-parameter-group-name msdtc-sqlserver-se-13 \
      --db-parameter-group-family "sqlserver-se-13.0" \
      --description "in-doubt xact resolution"
  ```

  For Windows:

  ```
  aws rds create-db-parameter-group ^
      --db-parameter-group-name msdtc-sqlserver-se-13 ^
      --db-parameter-group-family "sqlserver-se-13.0" ^
      --description "in-doubt xact resolution"
  ```

### Modifying the Parameter for MSDTC<a name="ModifyParam.MSDTC"></a>

Modify the `in-doubt xact resolution` parameter in the parameter group that corresponds to the SQL Server edition and version of your DB instance\.

For MSDTC, set the `in-doubt xact resolution` parameter to one of the following:
+ `1` – `Presume commit`\. Any MSDTC in\-doubt transactions are presumed to have committed\.
+ `2` – `Presume abort`\. Any MSDTC in\-doubt transactions are presumed to have stopped\.

For more information, see [in\-doubt xact resolution Server Configuration Option](https://docs.microsoft.com/en-us/sql/database-engine/configure-windows/in-doubt-xact-resolution-server-configuration-option) in the Microsoft documentation\.

#### Console<a name="ModifyParam.MSDTC.Console"></a>

The following example modifies the parameter group that you created for SQL Server Standard Edition 2016\.

**To modify the parameter group**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Parameter groups**\.

1. Choose the parameter group, such as **msdtc\-sqlserver\-se\-13**\.

1. Under **Parameters**, filter the parameter list for **xact**\.

1. Choose **in\-doubt xact resolution**\.

1. Choose **Edit parameters**\.

1. Enter **1** or **2**\.

1. Choose **Save changes**\.

#### CLI<a name="ModifyParam.MSDTC.CLI"></a>

The following example modifies the parameter group that you created for SQL Server Standard Edition 2016\.

**To modify the parameter group**
+ Use one of the following commands\.  
**Example**  

  For Linux, macOS, or Unix:

  ```
  aws rds modify-db-parameter-group \
      --db-parameter-group-name msdtc-sqlserver-se-13 \
      --parameters "ParameterName='in-doubt xact resolution',ParameterValue=1,ApplyMethod=immediate"
  ```

  For Windows:

  ```
  aws rds modify-db-parameter-group ^
      --db-parameter-group-name msdtc-sqlserver-se-13 ^
      --parameters "ParameterName='in-doubt xact resolution',ParameterValue=1,ApplyMethod=immediate"
  ```

### Associating the Option Group and Parameter Group with the DB Instance<a name="MSDTC.Apply"></a>

You can use the AWS Management Console or the AWS CLI to associate the MSDTC option group and parameter group with the DB instance\.

#### Console<a name="MSDTC.Apply.Console"></a>

You can associate the MSDTC option group and parameter group with a new or existing DB instance\.
+ For a new DB instance, associate them when you launch the instance\. For more information, see [Creating an Amazon RDS DB Instance](USER_CreateDBInstance.md)\.
+ For an existing DB instance, associate them by modifying the instance\. For more information, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md)\.
**Note**  
If you use an domain\-joined existing DB instance, it must already have an Active Directory domain and AWS Identity and Access Management \(IAM\) role associated with it\. If you create a new domain\-joined instance, specify an existing Active Directory domain and IAM role\. For more information, see [Using Windows Authentication with an Amazon RDS for SQL Server DB Instance](USER_SQLServerWinAuth.md)\.

#### CLI<a name="MSDTC.Apply.CLI"></a>

You can associate the MSDTC option group and parameter group with a new or existing DB instance\.

**Note**  
If you use an existing domain\-joined DB instance, it must already have an Active Directory domain and IAM role associated with it\. If you create a new domain\-joined instance, specify an existing Active Directory domain and IAM role\. For more information, see [Using Windows Authentication with an Amazon RDS for SQL Server DB Instance](USER_SQLServerWinAuth.md)\.

**To create a DB instance with the MSDTC option group and parameter group**
+ Specify the same DB engine type and major version as you used when creating the option group\.  
**Example**  

  For Linux, macOS, or Unix:

  ```
  aws rds create-db-instance \
      --db-instance-identifier mydbinstance \
      --db-instance-class db.m5.2xlarge \
      --engine sqlserver-se \
      --engine-version 13.00.5426.0.v1 \
      --allocated-storage 100 \
      --master-user-password secret123 \
      --master-username admin \
      --storage-type gp2 \
      --license-model li \
      --domain-iam-role-name my-directory-iam-role \
      --domain my-domain-id \
      --option-group-name msdtc-se-2016 \
      --db-parameter-group-name msdtc-sqlserver-se-13
  ```

  For Windows:

  ```
  aws rds create-db-instance ^
      --db-instance-identifier mydbinstance ^
      --db-instance-class db.m5.2xlarge ^
      --engine sqlserver-se ^
      --engine-version 13.00.5426.0.v1 ^
      --allocated-storage 100 ^
      --master-user-password secret123 ^
      --master-username admin ^
      --storage-type gp2 ^
      --license-model li ^
      --domain-iam-role-name my-directory-iam-role ^
      --domain my-domain-id ^
      --option-group-name msdtc-se-2016 ^
      --db-parameter-group-name msdtc-sqlserver-se-13
  ```

**To modify a DB instance and associate the MSDTC option group and parameter group**
+ Use one of the following commands\.  
**Example**  

  For Linux, macOS, or Unix:

  ```
  aws rds modify-db-instance \
      --db-instance-identifier mydbinstance \
      --option-group-name msdtc-se-2016 \
      --db-parameter-group-name msdtc-sqlserver-se-13 \
      --apply-immediately
  ```

  For Windows:

  ```
  aws rds modify-db-instance ^
      --db-instance-identifier mydbinstance ^
      --option-group-name msdtc-se-2016 ^
      --db-parameter-group-name msdtc-sqlserver-se-13 ^
      --apply-immediately
  ```

## Using Distributed Transactions<a name="Appendix.SQLServer.Options.MSDTC.Using"></a>

In Amazon RDS for SQL Server, you run distributed transactions in the same way as distributed transactions running on\-premises:
+ Using \.NET framework `System.Transactions` promotable transactions, which optimizes distributed transactions by deferring their creation until they're needed\.

  In this case, promotion is automatic and doesn't require you to make any intervention\. If there's only one resource manager within the transaction, no promotion is performed\. For more information about implicit transaction scopes, see [Implementing an Implicit Transaction using Transaction Scope](https://docs.microsoft.com/en-us/dotnet/framework/data/transactions/implementing-an-implicit-transaction-using-transaction-scope) in the Microsoft documentation\.

  Promotable transactions are supported with these \.NET implementations: 
  + Starting with ADO\.NET 2\.0, `System.Data.SqlClient` supports promotable transactions with SQL Server\. For more information, see [System\.Transactions Integration with SQL Server](https://docs.microsoft.com/en-us/dotnet/framework/data/adonet/system-transactions-integration-with-sql-server) in the Microsoft documentation\.
  + ODP\.NET supports `System.Transactions`\. A local transaction is created for the first connection opened in the `TransactionsScope` scope to Oracle Database 11g release 1 \(version 11\.1\) and later\. When a second connection is opened, this transaction is automatically promoted to a distributed transaction\. For more information about distributed transaction support in ODP\.NET, see [Microsoft Distributed Transaction Coordinator Integration](https://docs.oracle.com/en/database/oracle/oracle-data-access-components/18.3/ntmts/using-mts-with-oracledb.html) in the Microsoft documentation\.
+ Using the `BEGIN DISTRIBUTED TRANSACTION` statement\. For more information, see [BEGIN DISTRIBUTED TRANSACTION \(Transact\-SQL\)](https://docs.microsoft.com/en-us/sql/t-sql/language-elements/begin-distributed-transaction-transact-sql) in the Microsoft documentation\.

## Using Transaction Tracing<a name="MSDTC.Tracing"></a>

RDS supports controlling MSDTC transaction traces and downloading them from the RDS DB instance for troubleshooting\. You can control transaction tracing sessions by running the following RDS stored procedure\.

```
exec msdb.dbo.rds_msdtc_transaction_tracing 'trace_action',
[@traceall='0|1'],
[@traceaborted='0|1'],
[@tracelong='0|1'];
```

The following parameter is required:
+ `trace_action` – The tracing action\. It can be `START`, `STOP`, or `STATUS`\.

The following parameters are optional:
+ `@traceall` – Set to 1 to trace all distributed transactions\. The default is 0\.
+ `@traceaborted` – Set to 1 to trace canceled distributed transactions\. The default is 0\.
+ `@tracelong` – Set to 1 to trace long\-running distributed transactions\. The default is 0\.

**Example of START Tracing Action**  
To start a new transaction tracing session, run the following example statement\.  

```
exec msdb.dbo.rds_msdtc_transaction_tracing 'START',
@traceall='0',
@traceaborted='1',
@tracelong='1';
```
Only one transaction tracing session can be active at one time\. If a new tracing session `START` command is issued while a tracing session is active, an error is returned and the active tracing session remains unchanged\.

**Example of STOP Tracing Action**  
To stop a transaction tracing session, run the following statement\.  

```
exec msdb.dbo.rds_msdtc_transaction_tracing 'STOP'
```
This statement stops the active transaction tracing session and saves the transaction trace data into the log directory on the RDS DB instance\. The first row of the output contains the overall result, and the following lines indicate details of the operation\.  
The following is an example of a successful tracing session stop\.  

```
OK: Trace session has been successfully stopped.
Setting log file to: D:\rdsdbdata\MSDTC\Trace\dtctrace.log
Examining D:\rdsdbdata\MSDTC\Trace\msdtctr.mof for message formats,  8 found.
Searching for TMF files on path: (null)
Logfile D:\rdsdbdata\MSDTC\Trace\dtctrace.log:
 OS version    10.0.14393  (Currently running on 6.2.9200)
 Start Time    <timestamp>
 End Time      <timestamp>
 Timezone is   @tzres.dll,-932 (Bias is 0mins)
 BufferSize            16384 B
 Maximum File Size     10 MB
 Buffers  Written      Not set (Logger may not have been stopped).
 Logger Mode Settings (11000002) ( circular paged
 ProcessorCount         1 
Processing completed   Buffers: 1, Events: 3, EventsLost: 0 :: Format Errors: 0, Unknowns: 3
Event traces dumped to d:\rdsdbdata\Log\msdtc_<timestamp>.log
```
You can use the detailed information to query the name of the generated log file\. For more information about downloading log files from the RDS DB instance, see [Amazon RDS Database Log Files](USER_LogAccess.md)\.  
The trace session logs remain on the instance for 35 days\. Any older trace session logs are automatically deleted\.

**Example of STATUS Tracing Action**  
To trace the status of a transaction tracing session, run the following statement\.  

```
exec msdb.dbo.rds_msdtc_transaction_tracing 'STATUS'
```
This statement outputs the following as separate rows of the result set\.  

```
OK
SessionStatus: <Started|Stopped>
TraceAll: <True|False>
TraceAborted: <True|False>
TraceLongLived: <True|False>
```
The first line indicates the overall result of the operation: `OK` or `ERROR` with details, if applicable\. The subsequent lines indicate details about the tracing session status:   
+ `SessionStatus` can be one of the following:
  + `Started` if a tracing session is running\.
  + `Stopped` if no tracing session is running\.
+ The tracing session flags can be `True` or `False` depending on how they were set in the `START` command\.

## Modifying the MSDTC Option<a name="Appendix.SQLServer.Options.MSDTC.Modify"></a>

After you enable the `MSDTC` option, you can modify its settings\. For information about how to modify option settings, see [Modifying an Option Setting](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.ModifyOption)\.

**Note**  
Some changes to MSDTC option settings require the MSDTC service to be restarted\. This requirement can affect running distributed transactions\.

## Disabling MSDTC<a name="Appendix.SQLServer.Options.MSDTC.Disable"></a>

To disable MSDTC, remove the `MSDTC` option from its option group\.

### Console<a name="Options.MSDTC.Disable.Console"></a>

**To remove the MSDTC option from its option group**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Option groups**\.

1. Choose the option group with the `MSDTC` option \(`msdtc-se-2016` in the previous examples\)\.

1. Choose **Delete option**\.

1. Under **Deletion options**, choose **MSDTC** for **Options to delete**\.

1. Under **Apply immediately**, choose **Yes** to delete the option immediately, or **No** to delete it at the next maintenance window\.

1. Choose **Delete**\.

### CLI<a name="Options.MSDTC.Disable.CLI"></a>

**To remove the MSDTC option from its option group**
+ Use one of the following commands\.  
**Example**  

  For Linux, macOS, or Unix:

  ```
  aws rds remove-option-from-option-group \
      --option-group-name msdtc-se-2016 \
      --options MSDTC \
      --apply-immediately
  ```

  For Windows:

  ```
  aws rds remove-option-from-option-group ^
      --option-group-name msdtc-se-2016 ^
      --options MSDTC ^
      --apply-immediately
  ```

## Troubleshooting MSDTC for RDS SQL Server<a name="Appendix.SQLServer.Options.MSDTC.Troubleshooting"></a>

In some cases, you might have trouble establishing a connection between MSDTC running on a client computer and the MSDTC service running on an RDS SQL Server DB instance\. If so, make sure of the following:
+ The inbound rules for the security group associated with the DB instance are configured correctly\. For more information, see [Can't Connect to Amazon RDS DB Instance](CHAP_Troubleshooting.md#CHAP_Troubleshooting.Connecting)\.
+ Your client computer is configured correctly\.
+ The MSDTC firewall rules on your client compter are enabled\.

**To configure the client computer**

1. Open **Component Services**\.

   Or, in **Server Manager**, choose **Tools**, and then choose **Component Services**\.

1. Expand **Component Services**, expand **Computers**, expand **My Computer**, and then expand **Distributed Transaction Coordinator**\.

1. Open the context \(right\-click\) menu for **Local DTC** and choose **Properties**\.

1. Choose the **Security** tab\.

1. Choose all of the following:
   + **Network DTC Access**
   + **Allow Inbound**
   + **Allow Outbound**

1. Make sure that the correct authentication mode is chosen:
   + **Mutual Authentication Required** – The client machine is joined to the same domain as other nodes participating in distributed transaction, or there is a trust relationship configured between domains\.
   + **No Authentication Required** – All other cases\.

1. Choose **OK** to save your changes\.

1. If prompted to restart the service, choose **Yes**\.

**To enable MSDTC firewall rules**

1. Open Windows Firewall, then choose **Advanced settings**\.

   Or, in **Server Manager**, choose **Tools**, and then choose **Windows Firewall with Advanced Security**\.
**Note**  
Depending on your operating system, Windows Firewall might be called Windows Defender Firewall\.

1. Choose **Inbound Rules** in the left pane\.

1. Enable the following firewall rules, if they are not already enabled:
   + **Distributed Transaction Coordinator \(RPC\)**
   + **Distributed Transaction Coordinator \(RPC\)\-EPMAP**
   + **Distributed Transaction Coordinator \(TCP\-In\)**

1. Close Windows Firewall\.