# Configuring outbound network access on your Oracle DB instance<a name="Oracle.Concepts.ONA"></a>

Amazon RDS supports outbound network access on your Oracle DB instances\. To connect your instance to the network, you can use the following PL/SQL packages:
+ `UTL_HTTP` – makes HTTP callouts from SQL and PL/SQL\. You can use it to access data on the Internet over HTTP\. For more information, see [UTL\_HTTP](https://docs.oracle.com/en/database/oracle/oracle-database/19/arpls/UTL_HTTP.html#GUID-A85D2D1F-90FC-45F1-967F-34368A23C9BB) in the Oracle documentation\.
+ `UTL_TCP` – provides TCP/IP client\-side access functionality in PL/SQL\. This package is useful to PL/SQL applications that use Internet protocols and email\. For more information, see [UTL\_TCP](https://docs.oracle.com/en/database/oracle/oracle-database/19/arpls/UTL_TCP.html#GUID-348AFFE8-78B2-4217-AE73-384F46A1D292) in the Oracle documentation\.
+ `UTL_SMTP` – provides interfaces to the SMTP commands that enable a client to dispatch emails to an SMTP server\. For more information, see [UTL\_SMTP](https://docs.oracle.com/en/database/oracle/oracle-database/19/arpls/UTL_SMTP.html#GUID-F0065C52-D618-4F8A-A361-7B742D44C520) in the Oracle documentation\.

Note the following about working with outbound network access:
+ Outbound network access with `UTL_HTTP`, `UTL_TCP`, and `UTL_SMTP` is supported only for Oracle DB instances in a VPC\. To determine whether or not your DB instance is in a VPC, see [Determining whether you are using the EC2\-VPC or EC2\-Classic platform](USER_VPC.FindDefaultVPC.md)\. To move a DB instance not in a VPC into a VPC, see [Moving a DB instance not in a VPC into a VPC](USER_VPC.Non-VPC2VPC.md)\. 
+ To use SMTP with the UTL\_MAIL option, see [Oracle UTL\_MAIL](Oracle.Options.UTLMAIL.md)\.
+ The Domain Name Server \(DNS\) name of the remote host can be any of the following: 
  + Publicly resolvable\.
  + The endpoint of an Amazon RDS DB instance\.
  + Resolvable through a custom DNS server\. For more information, see [Setting up a custom DNS server](Appendix.Oracle.CommonDBATasks.System.md#Appendix.Oracle.CommonDBATasks.CustomDNS)\. 
  + The private DNS name of an Amazon EC2 instance in the same VPC or a peered VPC\. In this case, make sure that the name is resolvable through a custom DNS server\. Alternatively, to use the DNS provided by Amazon, you can enable the `enableDnsSupport` attribute in the VPC settings and enable DNS resolution support for the VPC peering connection\. For more information, see [DNS support in your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-dns.html#vpc-dns-support) and [Modifying your VPC peering connection](https://docs.aws.amazon.com/vpc/latest/peering/working-with-vpc-peering.html#modify-peering-connections)\. 

To connect securely to remote SSL/TLS resources, you can create and upload customized Oracle wallets\. By using the Amazon S3 integration with Amazon RDS for Oracle feature, you can download a wallet from Amazon S3 into Oracle DB instances\. For information about Amazon S3 integration for Oracle, see [Amazon S3 integration](oracle-s3-integration.md)\.

**To create a wallet for accessing an HTTP address over UTL\_HTTP**

1. Obtain the certificate for `Amazon Root CA 1` from the [ Amazon trust services repository](https://www.amazontrust.com/repository/)\.

1. Create a new wallet and add the following certificate:

   ```
   orapki wallet create -wallet . -auto_login_only 
   orapki wallet add -wallet . -trusted_cert -cert AmazonRootCA1.pem -auto_login_only 
   orapki wallet display -wallet .
   ```

1. Upload the wallet to your Amazon S3 bucket\.

1. Complete the prerequisites for Amazon S3 integration with Oracle, and add the `S3_INTEGRATION` option to your Oracle DB instance\. Ensure that the IAM role for the option has access to the Amazon S3 bucket you are using\.

   For more information, see [Amazon S3 integration](oracle-s3-integration.md)\.

1. Connect to the DB instance, and create a directory in the database to hold the wallet\. The following example creates a directory called `SSL_WALLET`:

   ```
   EXEC rdsadmin.rdsadmin_util.create_directory('SSL_WALLET');
   ```

1. Download the wallet from your Amazon S3 bucket to the Oracle DB instance\.

   The following example downloads a wallet to the DB instance directory `SSL_WALLET`:

   ```
   SELECT rdsadmin.rdsadmin_s3_tasks.download_from_s3(
         p_bucket_name    =>  'bucket_name', 
         p_s3_prefix      =>  'wallet_name', 
         p_directory_name =>  'SSL_WALLET') 
      AS TASK_ID FROM DUAL;
   ```

   Replace *bucket\_name* with the name of the bucket you are using, and replace *wallet\_name* with the name of the wallet\.

1. Set this wallet for `utl_http` transactions by running the following procedure:

   ```
   DECLARE
   	l_wallet_path all_directories.directory_path%type;
   BEGIN
   	select directory_path into l_wallet_path from all_directories
   	where upper(directory_name)='SSL_WALLET';
    	
    	utl_http.set_wallet('file:/' || l_wallet_path);
   END;
   ```

1. Access the URL from above over SSL/TLS, as shown in the following example\.

   ```
   SELECT utl_http.request('https://status.aws.amazon.com/robots.txt') AS ROBOTS_TXT FROM DUAL;
   
   ROBOTS_TXT
   --------------------------------------------------------------------------------
   User-agent: *
   Allow: /
   ```

**Note**  
The specific certificates that are required for your wallet vary by service\. For AWS services, the certificates can typically be found in the [Amazon trust services repository](https://www.amazontrust.com/repository/)\.

You can use a similar setup to send emails through UTL\_SMTP over SSL/TLS \(including [Amazon Simple Email Service](http://aws.amazon.com/ses/)\)\.

You can establish database links between Oracle DB instances over an SSL/TLS endpoint if the Oracle SSL option is configured for each instance\. No further configuration is required\. For more information, see [Oracle Secure Sockets Layer](Appendix.Oracle.Options.SSL.md)\. 