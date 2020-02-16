# Oracle Transparent Data Encryption<a name="Appendix.Oracle.Options.AdvSecurity"></a>

Amazon RDS supports Oracle Transparent Data Encryption \(TDE\), a feature of the Oracle Advanced Security option available in Oracle Enterprise Edition\. This feature automatically encrypts data before it is written to storage and automatically decrypts data when the data is read from storage\. 

Oracle Transparent Data Encryption is used in scenarios where you need to encrypt sensitive data in case data files and backups are obtained by a third party or when you need to address security\-related regulatory compliance issues\. 

The TDE option is a permanent option that can't be removed from an option group\. You can't disable TDE from a DB instance once that instance is associated with an option group with the Oracle TDE option\. You can change the option group of a DB instance that is using the TDE option, but the option group associated with the DB instance must include the TDE option\. You can also modify an option group that includes the TDE option by adding or removing other options\. 

A detailed explanation about Oracle Transparent Data Encryption is beyond the scope of this guide\. For information about using Oracle Transparent Data Encryption, see [Securing Stored Data Using Transparent Data Encryption](http://docs.oracle.com/cd/E11882_01/network.112/e40393/asotrans.htm#BABFGJAG)\. For more information about Oracle Advanced Security, see [Oracle Advanced Security](http://www.oracle.com/technetwork/database/options/advanced-security/index.html) in the Oracle documentation\. For more information on AWS security, see the [AWS Security Center](http://aws.amazon.com/security)\. 

**Note**  
You can't share a DB snapshot that uses this option\. For more information about sharing DB snapshots, see [Sharing a DB Snapshot](USER_ShareSnapshot.md)\.

## TDE Encryption Modes<a name="Appendix.Oracle.Options.AdvSecurity.Modes"></a>

Oracle Transparent Data Encryption supports two encryption modes: TDE tablespace encryption and TDE column encryption\. TDE tablespace encryption is used to encrypt entire application tables\. TDE column encryption is used to encrypt individual data elements that contain sensitive data\. You can also apply a hybrid encryption solution that uses both TDE tablespace and column encryption\. 

**Note**  
Amazon RDS manages the Oracle Wallet and TDE master key for the DB instance\. You do not need to set the encryption key using the command `ALTER SYSTEM set encryption key`\. 

For information about TDE best practices, see [Oracle Advanced Security Transparent Data Encryption Best Practices](http://www.oracle.com/technetwork/database/security/twp-transparent-data-encryption-bes-130696.pdf?ssSourceSiteId=ocomen)\. 

Once the option is enabled, you can check the status of the Oracle Wallet by using the following command: 

```
SELECT * FROM v$encryption_wallet; 
```

To create an encrypted tablespace, use the following command:

```
CREATE TABLESPACE encrypt_ts ENCRYPTION DEFAULT STORAGE (ENCRYPT); 
```

To specify the encryption algorithm, use the following command:

```
CREATE TABLESPACE encrypt_ts ENCRYPTION USING 'AES256' DEFAULT STORAGE (ENCRYPT); 
```

Note that the previous commands for encrypting a tablespace are the same as the commands you would use with an Oracle installation not on Amazon RDS, and the ALTER TABLE syntax to encrypt a column is also the same as the commands you would use for an Oracle installation not on Amazon RDS\. 

 You should determine if your DB instance is associated with an option group that has the **TDE** option\. To view the option group that a DB instance is associated with, you can use the RDS console, the [describe\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-instances.html) AWS CLI command, or the API operation [DescribeDBInstances](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBInstances.html)\. 

To comply with several security standards, Amazon RDS is working to implement automatic periodic master key rotation\. 

## Adding the TDE Option<a name="Appendix.Oracle.Options.AdvSecurity.Add"></a>

The process for using Oracle Transparent Data Encryption \(TDE\) with Amazon RDS is as follows: 

1.  If the DB instance is not associated with an option group that has the **TDE** option enabled, you must either create an option group and add the **TDE** option or modify the associated option group to add the **TDE** option\. For information about creating or modifying an option group, see [Working with Option Groups](USER_WorkingWithOptionGroups.md)\. For information about adding an option to an option group, see [Adding an Option to an Option Group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.AddOption)\.

1.  Associate the DB instance with the option group with the **TDE** option\. For information about associating a DB instance with an option group, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md)\. 

## Removing the TDE Option<a name="Appendix.Oracle.Options.AdvSecurity.Remove"></a>

 If you no longer want to use the TDE option with a DB instance, you must decrypt all your data on the DB instance, copy the data to a new DB instance that is not associated with an option group with TDE enabled, and then delete the original instance\. You can rename the new instance to be the same name as the previous DB instance if you prefer\. 

## Using TDE with Data Pump<a name="Appendix.Oracle.Options.AdvSecurity.Pump"></a>

You can use Oracle Data Pump to import or export encrypted dump files\. Amazon RDS supports the password encryption mode \(ENCRYPTION\_MODE=PASSWORD\) for Oracle Data Pump\. Amazon RDS does not support transparent encryption mode \(ENCRYPTION\_MODE=TRANSPARENT\) for Oracle Data Pump\. For more information about using Oracle Data Pump with Amazon RDS, see [Importing Using Oracle Data Pump](Oracle.Procedural.Importing.md#Oracle.Procedural.Importing.DataPump)\. 

## Related Topics<a name="Appendix.Oracle.Options.AdvSecurity.Related"></a>
+ [Working with Option Groups](USER_WorkingWithOptionGroups.md)
+ [Options for Oracle DB Instances](Appendix.Oracle.Options.md)