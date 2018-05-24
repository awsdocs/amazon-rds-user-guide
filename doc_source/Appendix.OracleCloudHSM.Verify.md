# Verifying the HSM Connection, the Oracle Keys in the HSM, and the TDE Key<a name="Appendix.OracleCloudHSM.Verify"></a>

Once you have completed all the set up steps, you can verify the HSM is working properly for TDE key storage\. Connect to the Oracle DB instance using a SQL utility such as *sqlplus* on a client computer or from the Amazon EC2 control instance if it has *sqlplus* installed\. For more information on connecting to an Oracle DB instance, see [Connecting to a DB Instance Running the Oracle Database Engine](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_ConnectToOracleInstance.html)\.

**Note**  
Before you continue, you must verify that the option group that you created for your Oracle instance returns a status of `in-sync`\. You can verify this passing the DB instance identifier to the `describe-db-instances` command\.

## Verifying the HSM Connection<a name="w3ab1c32c83c11c29b9"></a>

You can verify the connection between an Oracle DB instance and the HSM\. Connect to the Oracle DB instance and use the following command:

```
$ select * from v$encryption_wallet;
```

If the HSM connection is working, the command should return a status of *OPEN*\. The output of the command is similar to the following example:

```
WRL_TYPE
--------------------
WRL_PARAMETER
-------------------
STATUS
------------------
HSM
OPEN

1 row selected.
```

## Verifying the Oracle Keys in the HSM<a name="w3ab1c32c83c11c29c11"></a>

Once Amazon RDS starts and Oracle is running, Oracle creates two master keys on the HSM\. Do the following steps to confirm the existence of the master keys in the HSM\. You can run these commands from the prompt on the Amazon EC2 control instance or from the Amazon RDS Oracle DB instance\.

1. Use SSH to connect to the HSM appliance\. The following command 

   ```
   $ ssh manager@10.0.203.58
   ```

1. Log in to the HSM as the HSM manager

   ```
   $ hsm login	
   ```

1. Once you have successfully logged in, the Luna Shell prompt appears \(\[hostname\]lunash:>\)\. Display the contents of the HSM partition that corresponds to the Oracle DB instance using TDE\. Look for two symmetric key objects that begin with "ORACLE\.TDE\.HSM\." 

   ```
   lunash:>part showContents -par <hapg_label> -password <partition_password>
   ```

   The following output is an example of the information returned from the command:

   ```
   Partition Name:  hapg_label
   Partition SN:    154749011
   Storage (Bytes): Total=102701, Used=348, Free=102353
   Number objects:  2
   
   Object Label:  ORACLE.TDE.HSM.MK.0699468E1DC88E4F27BF426176B94D4907
   Object Type:   Symmetric Key
   
   Object Label:  ORACLE.TSE.HSM.MK.0784B1918AB6C19483189B2296FAE261C70203
   Object Type:   Symmetric Key
   
   Command Result : 0 (Success)
   ```

## Verifying the TDE Key<a name="w3ab1c32c83c11c29c13"></a>

The final step to verifying that the TDE key is correctly stored in the HSM is to create an encrypted tablespace\. The following commands creates an encrypted tablespace and shows that it is encrypted\.

```
SQL> create tablespace encrypted_ts
datafile size 50M encryption using 'AES128'
default storage (encrypt)
/
SQL> select tablespace_Name, encrypted from dba_tablespaces where encrypted='YES'
```

The following sample output shows that the tablespace was encrypted:

```
TABLESPACE_NAME                ENC
------------------------------ ---
ENCRYPTED_TS                   YES
```