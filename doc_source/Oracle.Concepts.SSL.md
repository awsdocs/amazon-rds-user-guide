# Using SSL with an RDS for Oracle DB instance<a name="Oracle.Concepts.SSL"></a>

Secure Sockets Layer \(SSL\) is an industry\-standard protocol for securing network connections between client and server\. After SSL version 3\.0, the name was changed to Transport Layer Security \(TLS\), but we still often refer to the protocol as SSL\. Amazon RDS supports SSL encryption for Oracle DB instances\. Using SSL, you can encrypt a connection between your application client and your Oracle DB instance\. SSL support is available in all AWS Regions for Oracle\.

To enable SSL encryption for an Oracle DB instance, add the Oracle SSL option to the option group associated with the DB instance\. Amazon RDS uses a second port, as required by Oracle, for SSL connections\. Doing this allows both clear text and SSL\-encrypted communication to occur at the same time between a DB instance and an Oracle client\. For example, you can use the port with clear text communication to communicate with other resources inside a VPC while using the port with SSL\-encrypted communication to communicate with resources outside the VPC\. 

For more information, see [Oracle Secure Sockets Layer](Appendix.Oracle.Options.SSL.md)\. 

**Note**  
You can't use both SSL and Oracle native network encryption \(NNE\) on the same DB instance\. Before you can use SSL encryption, you must disable any other connection encryption\. 