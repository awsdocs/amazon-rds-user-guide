# Oracle native network encryption<a name="Appendix.Oracle.Options.NetworkEncryption"></a>

Amazon RDS supports Oracle native network encryption \(NNE\)\. With native network encryption, you can encrypt data as it moves to and from a DB instance\. Amazon RDS supports NNE for all editions of Oracle\.

A detailed discussion of Oracle native network encryption is beyond the scope of this guide, but you should understand the strengths and weaknesses of each algorithm and key before you decide on a solution for your deployment\. For information about the algorithms and keys that are available through Oracle native network encryption, see [Configuring network data encryption](http://www.oracle.com/webfolder/technetwork/tutorials/obe/db/11g/r2/prod/security/network_encrypt/ntwrkencrypt.htm) in the Oracle documentation\. For more information about AWS security, see the [AWS security center](http://aws.amazon.com/security)\.

**Note**  
You can use Native Network Encryption or Secure Sockets Layer, but not both\. For more information, see [Oracle Secure Sockets Layer](Appendix.Oracle.Options.SSL.md)\. 

## NNE option settings<a name="Oracle.Options.NNE.Options"></a>

You can specify encryption requirements on both the server and the client\. The DB instance can act as a client when, for example, it uses a database link to connect to another database\. You might want to avoid forcing encryption on the server side\. For example, you might not want to force all client communications to use encryption because the server requires it\. In this case, you can force encryption on the client side using the `SQLNET.*CLIENT` options\. 

Amazon RDS supports the following settings for the NNE option\. 

**Note**  
When you use commas to separate values for an option setting, don't put a space after the comma\.


****  

| Option setting | Valid values | Default values | Description | 
| --- | --- | --- | --- | 
|  `SQLNET.CRYPTO_CHECKSUM_CLIENT`  |  `Accepted`, `Rejected`, `Requested`, `Required`   |  `Requested`  |  The data integrity behavior when a DB instance connects to the client, or a server acting as a client\. When a DB instance uses a database link, it acts as a client\. `Requested` indicates that the client doesn't require the DB instance to perform a checksum\.  | 
|  `SQLNET.CRYPTO_CHECKSUM_SERVER`  |  `Accepted`, `Rejected`, `Requested`, `Required`   |  `Requested`  |  The data integrity behavior when a client, or a server acting as a client, connects to the DB instance\. When a DB instance uses a database link, it acts as a client\. `Requested` indicates that the DB instance doesn't require the client to perform a checksum\.  | 
|  `SQLNET.CRYPTO_CHECKSUM_TYPES_CLIENT`  |  `SHA256`, `SHA384`, `SHA512`, `SHA1`, `MD5`  |  `SHA256`, `SHA384`, `SHA512`  |  A list of checksum algorithms\. You can specify either one value or a comma\-separated list of values\. If you use a comma, don't insert a space after the comma; otherwise, you receive an `InvalidParameterValue` error\. This parameter and `SQLNET.CRYPTO_CHECKSUM_TYPES_SERVER `must have a common cipher\.  | 
|  `SQLNET.CRYPTO_CHECKSUM_TYPES_SERVER`  |  `SHA256`, `SHA384`, `SHA512`, `SHA1`, `MD5`  |  `SHA256`, `SHA384`, `SHA512`, `SHA1`, `MD5`  |  A list of checksum algorithms\. You can specify either one value or a comma\-separated list of values\. If you use a comma, don't insert a space after the comma; otherwise, you receive an `InvalidParameterValue` error\. This parameter and `SQLNET.CRYPTO_CHECKSUM_TYPES_CLIENT` must have a common cipher\.  | 
|  `SQLNET.ENCRYPTION_CLIENT`  |  `Accepted`, `Rejected`, `Requested`, `Required`   |  `Requested`  |  The encryption behavior of the client when a client, or a server acting as a client, connects to the DB instance\. When a DB instance uses a database link, it acts as a client\. `Requested` indicates that the client does not require traffic from the server to be encrypted\.  | 
|  `SQLNET.ENCRYPTION_SERVER`  |  `Accepted`, `Rejected`, `Requested`, `Required`   |  `Requested`  |  The encryption behavior of the server when a client, or a server acting as a client, connects to the DB instance\. When a DB instance uses a database link, it acts as a client\. `Requested` indicates that the DB instance does not require traffic from the client to be encrypted\.  | 
|  `SQLNET.ENCRYPTION_TYPES_CLIENT`  |  `RC4_256`, `AES256`, `AES192`, `3DES168`, `RC4_128`, `AES128`, `3DES112`, `RC4_56`, `DES`, `RC4_40`, `DES40`  |  `RC4_256`, `AES256`, `AES192`, `3DES168`, `RC4_128`, `AES128`, `3DES112`, `RC4_56`, `DES`, `RC4_40`, `DES40`  |  A list of encryption algorithms used by the client\. The client uses each algorithm, in order, to attempt to decrypt the server input until an algorithm succeeds or until the end of the list is reached\.  Amazon RDS uses the following default list from Oracle\. You can change the order or limit the algorithms that the DB instance will accept\.  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Appendix.Oracle.Options.NetworkEncryption.html) You can specify either one value or a comma\-separated list of values\. If you a comma, don't insert a space after the comma; otherwise, you receive an `InvalidParameterValue` error\. This parameter and `SQLNET.SQLNET.ENCRYPTION_TYPES_SERVER` must have a common cipher\.  | 
|  `SQLNET.ENCRYPTION_TYPES_SERVER`  |  `RC4_256`, `AES256`, `AES192`, `3DES168`, `RC4_128`, `AES128`, `3DES112`, `RC4_56`, `DES`, `RC4_40`, `DES40`  |  `RC4_256`, `AES256`, `AES192`, `3DES168`, `RC4_128`, `AES128`, `3DES112`, `RC4_56`, `DES`, `RC4_40`, `DES40`  |  A list of encryption algorithms used by the DB instance\. The DB instance uses each algorithm, in order, to attempt to decrypt the client input until an algorithm succeeds or until the end of the list is reached\.  Amazon RDS uses the following default list from Oracle\. You can change the order or limit the algorithms that the client will accept\.  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Appendix.Oracle.Options.NetworkEncryption.html) You can specify either one value or a comma\-separated list of values\. If you a comma, don't insert a space after the comma; otherwise, you receive an `InvalidParameterValue` error\. This parameter and `SQLNET.SQLNET.ENCRYPTION_TYPES_SERVER` must have a common cipher\.  | 

## Adding the NNE option<a name="Oracle.Options.NNE.Add"></a>

The general process for adding the NNE option to a DB instance is the following: 

1. Create a new option group, or copy or modify an existing option group\.

1. Add the option to the option group\.

1. Associate the option group with the DB instance\.

When the option group is active, NNE is active\. 

**To add the NNE option to a DB instance**

1. For **Engine**, choose the Oracle edition that you want to use\. NNE is supported on all editions\. 

1. For **Major engine version**, choose the version of your DB instance\. 

   For more information, see [Creating an option group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.Create)\. 

1. Add the **NNE** option to the option group\. For more information about adding options, see [Adding an option to an option group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.AddOption)\.  
**Note**  
After you add the NNE option, you don't need to restart your DB instances\. As soon as the option group is active, NNE is active\. 

1. Apply the option group to a new or existing DB instance: 
   + For a new DB instance, you apply the option group when you launch the instance\. For more information, see [Creating an Amazon RDS DB instance](USER_CreateDBInstance.md)\. 

      
   + For an existing DB instance, you apply the option group by modifying the instance and attaching the new option group\. After you add the NNE option, you don't need to restart your DB instance\. As soon as the option group is active, NNE is active\. For more information, see [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)\. 

## Setting NNE values in the sqlnet\.ora<a name="Oracle.Options.NNE.Using"></a>

 With Oracle native network encryption, you can set network encryption on the server side and client side\. The client is the computer used to connect to the DB instance\. You can specify the following client settings in the slqnet\.ora: 
+ `SQLNET.CRYPTO_CHECKSUM_CLIENT`
+ `SQLNET.CRYPTO_CHECKSUM_TYPES_CLIENT`
+ `SQLNET.ENCRYPTION_CLIENT`
+ `SQLNET.ENCRYPTION_TYPES_CLIENT`

For information, see [Configuring network data encryption and integrity for Oracle servers and clients](http://docs.oracle.com/cd/E11882_01/network.112/e40393/asoconfg.htm) in the Oracle documentation\.

Sometimes, the DB instance rejects a connection request from an application, for example, if there is a mismatch between the encryption algorithms on the client and on the server\. To test Oracle native network encryption, add the following lines to the sqlnet\.ora file on the client: 

```
DIAG_ADR_ENABLED=off
TRACE_DIRECTORY_CLIENT=/tmp
TRACE_FILE_CLIENT=nettrace
TRACE_LEVEL_CLIENT=16
```

When a connection is attempted, the preceding lines generate a trace file on the client called `/tmp/nettrace*`\. The trace file contains information on the connection\. For more information about connection\-related issues when you are using Oracle Native Network Encryption, see [About negotiating encryption and integrity](http://docs.oracle.com/cd/E11882_01/network.112/e40393/asoconfg.htm#autoId12) in the Oracle documentation\. 

## Modifying NNE option settings<a name="Oracle.Options.NNE.ModifySettings"></a>

After you enable NNE, you can modify settings for the option\. For more information about how to modify option settings, see [Modifying an option setting](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.ModifyOption)\. For more information about each setting, see [NNE option settings](#Oracle.Options.NNE.Options)\. 

If you modify NNE option settings, make sure that `SQLNET.CRYPTO_CHECKSUM_TYPES_SERVER` and `SQLNET.CRYPTO_CHECKSUM_TYPES_CLIENT` have at least one common cipher\. For example, assume that `SQLNET.CRYPTO_CHECKSUM_TYPES_CLIENT` has the default values `SHA256,SHA384,SHA512`\. You modify `SQLNET.CRYPTO_CHECKSUM_TYPES_SERVER` to have values `SHA1,MD5,SHA256`\. The configuration is valid because the two parameters share `SHA256`\.

For another example, assume that you want to modify `SQLNET.CRYPTO_CHECKSUM_TYPES_SERVER` from its default setting to `SHA1,MD5`\. In this case, make sure you set `SQLNET.CRYPTO_CHECKSUM_TYPES_CLIENT` to `SHA1` or `MD5`\. These algorithms aren't included in the default values for `SQLNET.CRYPTO_CHECKSUM_TYPES_CLIENT`\.

## Removing the NNE option<a name="Oracle.Options.NNE.Remove"></a>

You can remove NNE from a DB instance\. 

To remove NNE from a DB instance, do one of the following: 
+ To remove NNE from multiple DB instances, remove the NNE option from the option group they belong to\. This change affects all DB instances that use the option group\. After you remove the NNE option, you don't need to restart your DB instances\. For more information, see [Removing an option from an option group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.RemoveOption)\. 

   
+ To remove NNE from a single DB instance, modify the DB instance and specify a different option group that doesn't include the NNE option\. You can specify the default \(empty\) option group, or a different custom option group\. After you remove the NNE option, you don't need to restart your DB instance\. For more information, see [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)\. 