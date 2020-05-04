# Updating Applications to Connect to Microsoft SQL Server DB Instances Using New SSL/TLS Certificates<a name="ssl-certificate-rotation-sqlserver"></a>

As of September 19, 2019, Amazon RDS has published new Certificate Authority \(CA\) certificates for connecting to your RDS DB instances using Secure Socket Layer or Transport Layer Security \(SSL/TLS\)\. Following, you can find information about updating your applications to use the new certificates\.

This topic can help you to determine whether any client applications use SSL/TLS to connect to your DB instances\. If they do, you can further check whether those applications require certificate verification to connect\. 

**Note**  
Some applications are configured to connect to SQL Server DB instances only if they can successfully verify the certificate on the server\. ****  
For such applications, you must update your client application trust stores to include the new CA certificates\. 

After you update your CA certificates in the client application trust stores, you can rotate the certificates on your DB instances\. We strongly recommend testing these procedures in a development or staging environment before implementing them in your production environments\.

For more information about certificate rotation, see [Rotating Your SSL/TLS Certificate](UsingWithRDS.SSL-certificate-rotation.md)\. For more information about downloading certificates, see [Using SSL/TLS to Encrypt a Connection to a DB Instance](UsingWithRDS.SSL.md)\. For information about using SSL/TLS with Microsoft SQL Server DB instances, see [Using SSL with a Microsoft SQL Server DB Instance](SQLServer.Concepts.General.SSL.Using.md)\.

**Topics**
+ [Determining Whether Any Applications are Connecting to Your Microsoft SQL Server DB Instance Using SSL](#ssl-certificate-rotation-sqlserver.determining-server)
+ [Determining Whether a Client Requires Certificate Verification in Order to Connect](#ssl-certificate-rotation-sqlserver.determining-client)
+ [Updating Your Application Trust Store](#ssl-certificate-rotation-sqlserver.updating-trust-store)

## Determining Whether Any Applications are Connecting to Your Microsoft SQL Server DB Instance Using SSL<a name="ssl-certificate-rotation-sqlserver.determining-server"></a>

Check the DB instance configuration for the value of the `rds.force_ssl` parameter\. By default, the `rds.force_ssl` parameter is set to 0 \(off\)\. If the `rds.force_ssl` parameter is set to 1 \(on\), clients are required to use SSL/TLS for connections\. For more information about parameter groups, see [Working with DB Parameter Groups](USER_WorkingWithParamGroups.md)\. You can also find the setting for this parameter in the `sys.dm_server_registry` DMV\.

Run the following query to get the current encryption option for all the open connections to a DB instance\. The column `ENCRYPT_OPTION` returns `TRUE` if the connection is encrypted\.

```
select SESSION_ID,
    ENCRYPT_OPTION,
    NET_TRANSPORT,
    AUTH_SCHEME
    from SYS.DM_EXEC_CONNECTIONS
```

This query shows only the current connections\. It doesn't show whether applications that have connected and disconnected in the past have used SSL\.

## Determining Whether a Client Requires Certificate Verification in Order to Connect<a name="ssl-certificate-rotation-sqlserver.determining-client"></a>

You can check whether different types of clients require certificate verification to connect\.

**Note**  
If you use connectors other than the ones listed, see the specific connector's documentation for information about how it enforces encrypted connections\. For more information, see [Connection modules for Microsoft SQL databases](https://docs.microsoft.com/en-us/sql/connect/sql-connection-libraries?view=sql-server-ver15) in the Microsoft SQL Server documentation\.

### SQL Server Management Studio<a name="ssl-certificate-rotation-sqlserver.determining-client.management-studio"></a>

Check whether encryption is enforced for SQL Server Management Studio connections:

1. Launch SQL Server Management Studio\. 

1. For **Connect to server**, enter the server information, login user name, and password\. 

1. Choose **Options**\. 

1. Check if **Encrypt connection** is selected in the connect page\.

For more information about SQL Server Management Studio, see [Use SQL Server Management Studio](http://msdn.microsoft.com/en-us/library/ms174173.aspx)\.

### sqlcmd<a name="ssl-certificate-rotation-sqlserver.determining-client.sqlcmd"></a>

The following example with the `sqlcmd` client shows how to check a script's SQL Server connection to determine whether successful connections require a valid certificate\. For more information, see [Connecting with sqlcmd](https://docs.microsoft.com/en-us/sql/connect/odbc/linux-mac/connecting-with-sqlcmd?view=sql-server-ver15) in the Microsoft SQL Server documentation\.

When using `sqlcmd`, an SSL connection requires verification against the server certificate if you use the `-N` command argument to encrypt connections, as in the following example\.

```
$ sqlcmd -N -S dbinstance.rds.amazon.com -d ExampleDB                     
```

**Note**  
If `sqlcmd` is invoked with the `-C` option, it trusts the server certificate, even if that doesn't match the client\-side trust store\.

### ADO\.NET<a name="ssl-certificate-rotation-sqlserver.determining-client.adonet"></a>

In the following example, the application connects using SSL, and the server certificate must be verified\.

```
using SQLC = Microsoft.Data.SqlClient;
 
...
 
    static public void Main()  
    {  
        using (var connection = new SQLC.SqlConnection(
            "Server=tcp:dbinstance.rds.amazon.com;" +
            "Database=ExampleDB;User ID=LOGIN_NAME;" +
            "Password=YOUR_PASSWORD;" + 
            "Encrypt=True;TrustServerCertificate=False;"
            ))
        {  
            connection.Open();  
            ...
        }
```

### Java<a name="ssl-certificate-rotation-sqlserver.determining-client.java"></a>

In the following example, the application connects using SSL, and the server certificate must be verified\.

```
String connectionUrl =   
    "jdbc:sqlserver://dbinstance.rds.amazon.com;" +  
    "databaseName=ExampleDB;integratedSecurity=true;" +  
    "encrypt=true;trustServerCertificate=false";
```

To enable SSL encryption for clients that connect using JDBC, you might need to add the Amazon RDS certificate to the Java CA certificate store\. For instructions, see [Configuring the client for encryption](https://docs.microsoft.com/en-us/SQL/connect/jdbc/configuring-the-client-for-ssl-encryption?view=sql-server-2017) in the Microsoft SQL Server documentation\. You can also provide the trusted CA certificate file name directly by appending `trustStore=path-to-certificate-trust-store-file` to the connection string\.

**Note**  
If you use `TrustServerCertificate=true` \(or its equivalent\) in the connection string, the connection process skips the trust chain validation\. In this case, the application connects even if the certificate can't be verified\. Using `TrustServerCertificate=false` enforces certificate validation and is a best practice\.

## Updating Your Application Trust Store<a name="ssl-certificate-rotation-sqlserver.updating-trust-store"></a>

You can update the trust store for applications that use Microsoft SQL Server\. For instructions, see [Encrypting Specific Connections](SQLServer.Concepts.General.SSL.Using.md#SQLServer.Concepts.General.SSL.Client)\. Also, see [Configuring the client for encryption](https://docs.microsoft.com/en-us/SQL/connect/jdbc/configuring-the-client-for-ssl-encryption?view=sql-server-2017) in the Microsoft SQL Server documentation\.

If you are using an operating system other than Microsoft Windows, see the software distribution documentation for SSL/TLS implementation for information about adding a new root CA certificate\. For example, OpenSSL and GnuTLS are popular options\. Use the implementation method to add trust to the RDS root CA certificate\. Microsoft provides instructions for configuring certificates on some systems\.

**Note**  
When you update the trust store, you can retain older certificates in addition to adding the new certificates\.

### Updating Your Application Trust Store for JDBC<a name="ssl-certificate-rotation-sqlserver.updating-trust-store.jdbc"></a>

You can update the trust store for applications that use JDBC for SSL/TLS connections\.

**To update the trust store for JDBC applications**

1. Download the 2019 root certificate that works for all AWS Regions and put the file in the trust store directory\.

   For information about downloading the root certificate, see [Using SSL/TLS to Encrypt a Connection to a DB Instance](UsingWithRDS.SSL.md)\.

1.  Convert the certificate to \.der format using the following command\.

   ```
   openssl x509 -outform der -in rds-ca-2019-root.pem -out rds-ca-2019-root.der                    
   ```

   Replace the file name with the one that you downloaded\.

1.  Import the certificate into the key store using the following command\. 

   ```
   keytool -import -alias rds-root -keystore clientkeystore -file rds-ca-2019-root.der                    
   ```

1. Confirm that the key store was updated successfully\.

   ```
   keytool -list -v -keystore clientkeystore.jks                        
   ```

   Enter the key store password when you are prompted for it\.

   Your output should contain the following\.

   ```
   rds-root,date, trustedCertEntry, 
   Certificate fingerprint (SHA1): D4:0D:DB:29:E3:75:0D:FF:A6:71:C3:14:0B:BF:5F:47:8D:1C:80:96
   # This fingerprint should match the output from the below command
   openssl x509 -fingerprint -in rds-ca-2019-root.pem -noout
   ```

**Important**  
After you have determined that your database connections use SSL/TLS and have updated your application trust store, you can update your database to use the rds\-ca\-2019 certificates\. For instructions, see step 3 in [Updating Your CA Certificate by Modifying Your DB Instance](UsingWithRDS.SSL-certificate-rotation.md#UsingWithRDS.SSL-certificate-rotation-updating)\.