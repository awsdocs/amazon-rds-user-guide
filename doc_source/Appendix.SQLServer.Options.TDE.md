# Support for Transparent Data Encryption in SQL Server<a name="Appendix.SQLServer.Options.TDE"></a>

Amazon RDS supports using Transparent Data Encryption \(TDE\) to encrypt stored data on your DB instances running Microsoft SQL Server\. TDE automatically encrypts data before it is written to storage, and automatically decrypts data when the data is read from storage\. 

Amazon RDS supports TDE for the following SQL Server versions and editions:
+ SQL Server 2019 Standard and Enterprise Editions
+ SQL Server 2017 Enterprise Edition
+ SQL Server 2016 Enterprise Edition
+ SQL Server 2014 Enterprise Edition
+ SQL Server 2012 Enterprise Edition

To enable transparent data encryption for an RDS SQL Server DB instance, specify the TDE option in an RDS option group that is associated with that DB instance\. 

Transparent data encryption for SQL Server provides encryption key management by using a two\-tier key architecture\. A certificate, which is generated from the database master key, is used to protect the data encryption keys\. The database encryption key performs the actual encryption and decryption of data on the user database\. Amazon RDS backs up and manages the database master key and the TDE certificate\. To comply with several security standards, Amazon RDS is working to implement automatic periodic master key rotation\.

Transparent data encryption is used in scenarios where you need to encrypt sensitive data\. For example, you might want to provide data files and backups to a third party, or address security\-related regulatory compliance issues\. You can't encrypt the system databases for SQL Server, such as the `model` or `master` databases\.

A detailed discussion of transparent data encryption is beyond the scope of this guide, but you should understand the security strengths and weaknesses of each encryption algorithm and key\. For information about transparent data encryption for SQL Server, see [Transparent Data Encryption \(TDE\)](http://msdn.microsoft.com/en-us/library/bb934049.aspx) on the Microsoft website\.

Determine if your DB instance is already associated with an option group that has the TDE option\. To view the option group that a DB instance is associated with, you can use the RDS console, the [describe\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-instances.html) AWS CLI command, or the API operation [DescribeDBInstances](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBInstances.html)\.

The process for enabling transparent data encryption on a SQL Server DB instance is as follows:

1.  If the DB instance isn't associated with an option group that has TDE enabled, you have two choices\. You can create an option group and add the TDE option, or you can modify the associated option group to add it\. For information about creating or modifying an option group, see [Working with Option Groups](USER_WorkingWithOptionGroups.md)\. For information about adding an option to an option group, see [Adding an Option to an Option Group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.AddOption)\.

1.  Associate the DB instance with the option group with the TDE option\. For information about associating a DB instance with an option group, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md)\.

When the TDE option is added to an option group, Amazon RDS generates a certificate that is used in the encryption process\. You can then use the certificate to run SQL statements that encrypt data in a database on the DB instance\. The following example uses the RDS\-created certificate called `RDSTDECertificateName` to encrypt a database called `customerDatabase`\.

```
 1. ---------- Enabling TDE -------------
 2. 
 3. -- Find a RDSTDECertificate to use
 4. USE [master]
 5. GO
 6. SELECT name FROM sys.certificates WHERE name LIKE 'RDSTDECertificate%'
 7. GO
 8. 
 9. USE [customerDatabase]
10. GO
11. -- Create DEK using one of the certificates from the previous step
12. CREATE DATABASE ENCRYPTION KEY
13. WITH ALGORITHM = AES_128
14. ENCRYPTION BY SERVER CERTIFICATE [RDSTDECertificateName]
15. GO
16. 
17. -- Enable encryption on the database
18. ALTER DATABASE [customerDatabase]
19. SET ENCRYPTION ON
20. GO
21. 
22. -- Verify that the database is encrypted
23. USE [master]
24. GO
25. SELECT name FROM sys.databases WHERE is_encrypted = 1
26. GO
27. SELECT db_name(database_id) as DatabaseName, * FROM sys.dm_database_encryption_keys
28. GO
```

 The time that it takes to encrypt a SQL Server database using TDE depends on several factors\. These include the size of the DB instance, whether PIOPS is enabled for the instance, the amount of data, and other factors\.

The TDE option is a persistent option that you can't remove from an option group unless all DB instances and backups are disassociated from the option group\. After you add the TDE option to an option group, the option group can only be associated with DB instances that use TDE\. For more information about persistent options in an option group, see [Option Groups Overview](USER_WorkingWithOptionGroups.md#Overview.OptionGroups)\. 

Because the TDE option is a persistent option, you can have a conflict between the option group and an associated DB instance\. You can have a conflict between the option group and an associated DB instance in the following situations:
+ The current option group has the TDE option, and you replace it with an option group that does not have the TDE option\.
+ You restore from a DB snapshot to a new DB instance that does not have an option group that contains the TDE option\. For more information about this scenario, see [Option Group Considerations](USER_CopySnapshot.md#USER_CopySnapshot.Options)\. 

To disable TDE for a DB instance, first make sure that there are no encrypted objects left on the DB instance by either decrypting the objects or by dropping them\. If any encrypted objects exist on the DB instance, you can't disable TDE for the DB instance\. When you use the console to remove the TDE option from an option group, the console indicates that it is processing\. In addition, an error event is created if the option group is associated with an encrypted DB instance or DB snapshot\.

The following example removes the TDE encryption from a database called `customerDatabase`\. 

```
 1. ------------- Removing TDE ----------------
 2. 
 3. USE [customerDatabase]
 4. GO
 5. 
 6. -- Disable encryption on the database
 7. ALTER DATABASE [customerDatabase]
 8. SET ENCRYPTION OFF
 9. GO
10. 
11. -- Wait until the encryption state of the database becomes 1. The state is 5 (Decryption in progress) for a while
12. SELECT db_name(database_id) as DatabaseName, * FROM sys.dm_database_encryption_keys
13. GO
14. 
15. -- Drop the DEK used for encryption
16. DROP DATABASE ENCRYPTION KEY
17. GO
18. 
19. -- Alter to SIMPLE Recovery mode so that your encrypted log gets truncated
20. USE [master]
21. GO
22. ALTER DATABASE [customerDatabase] SET RECOVERY SIMPLE
23. GO
```

When all objects are decrypted, you can have two options\. You can modify the DB instance to be associated with an option group without the TDE option\. Or you can remove the TDE option from the option group\. 