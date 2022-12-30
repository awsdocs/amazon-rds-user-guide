# Using native network encryption with an RDS for Oracle DB instance<a name="Oracle.Concepts.NNE"></a>

Oracle Database offers two ways to encrypt data over the network: native network encryption \(NNE\) and Transport Layer Security \(TLS\)\. NNE is a proprietary Oracle security feature, whereas TLS is an industry standard\. RDS for Oracle supports NNE for all editions of Oracle Database\.

NNE has the following advantages over TLS:
+ You can control NNE on the client and server using settings in the NNE option:
  + `SQLNET.ALLOW_WEAK_CRYPTO_CLIENTS` and `SQLNET.ALLOW_WEAK_CRYPTO`
  + `SQLNET.CRYPTO_CHECKSUM_CLIENT` and `SQLNET.CRYPTO_CHECKSUM_SERVER`
  + `SQLNET.CRYPTO_CHECKSUM_TYPES_CLIENT` and `SQLNET.CRYPTO_CHECKSUM_TYPES_SERVER`
  + `SQLNET.ENCRYPTION_CLIENT` and `SQLNET.ENCRYPTION_SERVER`
  + `SQLNET.ENCRYPTION_TYPES_CLIENT` and `SQLNET.ENCRYPTION_TYPES_SERVER`
+ In most cases, you don't need to configure your client or server\. In contrast, TSL requires you to configure both client and server\.
+ No certificates are required\. In TLS, the server requires a certificate \(which eventually expires\), and the client requires a trusted root certificate for the certificate authority that issued the server’s certificate\.

To enable NNE encryption for an Oracle DB instance, add the Oracle NNE option to the option group associated with the DB instance\. For more information, see [Oracle native network encryption](Appendix.Oracle.Options.NetworkEncryption.md)\. 

**Note**  
You can't use both NNE and TLS on the same DB instance\.