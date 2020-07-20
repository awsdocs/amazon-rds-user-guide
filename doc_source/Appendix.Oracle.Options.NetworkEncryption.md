# Oracle Native Network Encryption<a name="Appendix.Oracle.Options.NetworkEncryption"></a>

Amazon RDS supports Oracle native network encryption \(NNE\)\. With native network encryption, you can encrypt data as it moves to and from a DB instance\. Amazon RDS supports NNE for all editions of Oracle\. 

A detailed discussion of Oracle native network encryption is beyond the scope of this guide, but you should understand the strengths and weaknesses of each algorithm and key before you decide on a solution for your deployment\. For information about the algorithms and keys that are available through Oracle native network encryption, see [Configuring Network Data Encryption](http://www.oracle.com/webfolder/technetwork/tutorials/obe/db/11g/r2/prod/security/network_encrypt/ntwrkencrypt.htm) in the Oracle documentation\. For more information about AWS security, see the [AWS Security Center](http://aws.amazon.com/security)\. 

**Note**  
You can use Native Network Encryption or Secure Sockets Layer, but not both\. For more information, see [Oracle Secure Sockets Layer](Appendix.Oracle.Options.SSL.md)\. 

## NNE Option Settings<a name="Oracle.Options.NNE.Options"></a>

Amazon RDS supports the following settings for the NNE option\. 

**Note**  
When you use commas to separate values for an option setting, don't put a space after the comma\.


****  

| Option Setting | Valid Values | Default Value | Description | 
| --- | --- | --- | --- | 
| **SQLNET\.ENCRYPTION\_SERVER** |  `Accepted`, `Rejected`, `Requested`, `Required`   | `Requested` |  The encryption behavior when a client, or a server acting as a client, connects to the DB instance\.  `Requested` indicates that the DB instance does not require traffic from the client to be encrypted\.  | 
| **SQLNET\.CRYPTO\_CHECKSUM\_SERVER** |  `Accepted`, `Rejected`, `Requested`, `Required`   | `Requested` |  The data integrity behavior when a client, or a server acting as a client, connects to the DB instance\.  `Requested` indicates that the DB instance does not require the client to perform a checksum\.  | 
| **SQLNET\.ENCRYPTION\_TYPES\_SERVER** |  `RC4_256`, `AES256`, `AES192`, `3DES168`, `RC4_128`, `AES128`, `3DES112`, `RC4_56`, `DES`, `RC4_40`, `DES40`  |  `RC4_256`, `AES256`, `AES192`, `3DES168`, `RC4_128`, `AES128`, `3DES112`, `RC4_56`, `DES`, `RC4_40`, `DES40`  |  A list of encryption algorithms used by the DB instance\. The DB instance uses each algorithm, in order, to attempt to decrypt the client input until an algorithm succeeds or until the end of the list is reached\.  Amazon RDS uses the following default list from Oracle\. You can change the order or limit the algorithms that the DB instance will accept\.  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Appendix.Oracle.Options.NetworkEncryption.html) You can specify either one value or a comma\-separated list of values\. If you a comma, don't insert a space after the comma; otherwise, you receive an `InvalidParameterValue` error\.  | 
| **SQLNET\.CRYPTO\_CHECKSUM\_TYPES\_SERVER** |  `SHA256`, `SHA384`, `SHA512`, `SHA1`, `MD5`  |  `SHA256`, `SHA384`, `SHA512`, `SHA1`, `MD5`  |  A list of checksum algorithms\. You can specify either one value or a comma\-separated list of values\. If you use a comma, don't insert a space after the comma; otherwise, you receive an `InvalidParameterValue` error\.  | 

## Adding the NNE Option<a name="Oracle.Options.NNE.Add"></a>

The general process for adding the NNE option to a DB instance is the following: 

1. Create a new option group, or copy or modify an existing option group\.

1. Add the option to the option group\.

1. Associate the option group with the DB instance\.

After you add the NNE option, as soon as the option group is active, NNE is active\. 

**To add the NNE option to a DB instance**

1. For **Engine**, choose the Oracle edition that you want to use\. NNE is supported on all editions\. 

1. For **Major engine version**, choose the version of your DB instance\. 

   For more information, see [Creating an Option Group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.Create)\. 

1. Add the **NNE** option to the option group\. For more information about adding options, see [Adding an Option to an Option Group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.AddOption)\.  
**Note**  
After you add the NNE option, you don't need to restart your DB instances\. As soon as the option group is active, NNE is active\. 

1. Apply the option group to a new or existing DB instance: 
   + For a new DB instance, you apply the option group when you launch the instance\. For more information, see [Creating an Amazon RDS DB Instance](USER_CreateDBInstance.md)\. 
   + For an existing DB instance, you apply the option group by modifying the instance and attaching the new option group\. After you add the NNE option, you don't need to restart your DB instance\. As soon as the option group is active, NNE is active\. For more information, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md)\. 

## Using NNE<a name="Oracle.Options.NNE.Using"></a>

 With Oracle native network encryption, you can also specify network encryption on the client side\. On the client \(the computer used to connect to the DB instance\), you can use the sqlnet\.ora file to specify the following client settings: SQLNET\.CRYPTO\_CHECKSUM\_CLIENT , SQLNET\.CRYPTO\_CHECKSUM\_TYPES\_CLIENT, SQLNET\.ENCRYPTION\_CLIENT,  and SQLNET\.ENCRYPTION\_TYPES\_CLIENT\. For information, see [Configuring Network Data Encryption and Integrity for Oracle Servers and Clients](http://docs.oracle.com/cd/E11882_01/network.112/e40393/asoconfg.htm) in the Oracle documentation\. 

 Sometimes, the DB instance will reject a connection request from an application, for example, if there is a mismatch between the encryption algorithms on the client and on the server\. 

 To test Oracle native network encryption , add the following lines to the sqlnet\.ora file on the client: 

```
DIAG_ADR_ENABLED=off   
TRACE_DIRECTORY_CLIENT=/tmp   
TRACE_FILE_CLIENT=nettrace   
TRACE_LEVEL_CLIENT=16
```

 These lines generate a trace file on the client called `/tmp/nettrace*` when the connection is attempted\. The trace file contains information on the connection\. For more information about connection\-related issues when you are using Oracle Native Network Encryption, see [About Negotiating Encryption and Integrity](http://docs.oracle.com/cd/E11882_01/network.112/e40393/asoconfg.htm#autoId12) in the Oracle documentation\. 

## Modifying NNE Settings<a name="Oracle.Options.NNE.ModifySettings"></a>

After you enable NNE, you can modify settings for the option\. For more information about how to modify option settings, see [Modifying an Option Setting](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.ModifyOption)\. For more information about each setting, see [NNE Option Settings](#Oracle.Options.NNE.Options)\. 

## Removing the NNE Option<a name="Oracle.Options.NNE.Remove"></a>

You can remove NNE from a DB instance\. 

To remove NNE from a DB instance, do one of the following: 
+ To remove NNE from multiple DB instances, remove the NNE option from the option group they belong to\. This change affects all DB instances that use the option group\. After you remove the NNE option, you don't need to restart your DB instances\. For more information, see [Removing an Option from an Option Group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.RemoveOption)\. 
+ To remove NNE from a single DB instance, modify the DB instance and specify a different option group that doesn't include the NNE option\. You can specify the default \(empty\) option group, or a different custom option group\. After you remove the NNE option, you don't need to restart your DB instance\. For more information, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md)\. 

## Related Topics<a name="Oracle.Options.NNE.Related"></a>
+ [Working with Option Groups](USER_WorkingWithOptionGroups.md)
+ [Options for Oracle DB Instances](Appendix.Oracle.Options.md)