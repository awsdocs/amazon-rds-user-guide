# Connecting to Oracle with Kerberos Authentication<a name="oracle-kerberos-connecting"></a>

This section assumes that you have set up your Oracle client as described in [Step 8: Configure an Oracle Client](oracle-kerberos-setting-up.md#oracle-kerberos-setting-up.configure-oracle-client)\. To connect to the Oracle DB with Kerberos authentication, log in using the Kerberos authentication type\. For example, after launching Oracle SQL Developer, choose **Kerberos Authentication** as the authentication type, as shown following\. 

![\[\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/ora-kerberos-auth.png)

To connect to Oracle with Kerberos authentication with SQL\*Plus:

1. At a command prompt, run the following command:

   ```
   kinit username
   ```

   Replace *`username`* with the user name and, at the prompt, enter the password stored in the Microsoft Active Directory for the user\.

1. Open SQL\*Plus and connect using the DNS name and port number for the Oracle DB instance\.

   For more information about connecting to an Oracle DB instance in SQL\*Plus, see [Connecting to Your DB Instance Using SQL\*Plus](USER_ConnectToOracleInstance.md#USER_ConnectToOracleInstance.SQLPlus)\.