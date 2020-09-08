# Oracle UTL\_MAIL<a name="Oracle.Options.UTLMAIL"></a>

Amazon RDS supports Oracle UTL\_MAIL through the use of the UTL\_MAIL option and SMTP servers\. You can send email directly from your database by using the UTL\_MAIL package\. Amazon RDS supports UTL\_MAIL for the following versions of Oracle: 
+ Oracle version 19\.0\.0\.0, all versions
+ Oracle version 18\.0\.0\.0, all versions
+ Oracle version 12\.2\.0\.1, all versions
+ Oracle version 12\.1\.0\.2\.v5 and later
+ Oracle version 11\.2\.0\.4\.v9 and later

The following are some limitations to using UTL\_MAIL: 
+ UTL\_MAIL does not support Transport Layer Security \(TLS\) and therefore emails are not encrypted\. 

  To connect securely to remote SSL/TLS resources by creating and uploading custom Oracle wallets, follow the instructions in [Using utl\_http, utl\_tcp, and utl\_smtp with an Oracle DB Instance](CHAP_Oracle.md#Oracle.Concepts.ONA)\.

  The specific certificates that are required for your wallet vary by service\. For AWS services, these can typically be found in the [Amazon Trust Services Repository](https://www.amazontrust.com/repository/)\.
+ UTL\_MAIL does not support authentication with SMTP servers\. 
+ You can only send a single attachment in an email\. 
+ You can't send attachments larger than 32 K\. 
+ You can only use ASCII and Extended Binary Coded Decimal Interchange Code \(EBCDIC\) character encodings\. 
+ SMTP port \(25\) is throttled based on the elastic network interface owner's policies\. 

When you enable UTL\_MAIL, only the master user for your DB instance is granted the execute privilege\. If necessary, the master user can grant the execute privilege to other users so that they can use UTL\_MAIL\. 

**Important**  
We recommend that you enable Oracle's built\-in auditing feature to track the use of UTL\_MAIL procedures\. 

## Prerequisites for Oracle UTL\_MAIL<a name="Oracle.Options.UTLMAIL.PreReqs"></a>

The following are prerequisites for using Oracle UTL\_MAIL: 
+ One or more SMTP servers, and the corresponding IP addresses or public or private Domain Name Server \(DNS\) names\. For more information about private DNS names resolved through a custom DNS server, see [Setting Up a Custom DNS Server](Appendix.Oracle.CommonDBATasks.System.md#Appendix.Oracle.CommonDBATasks.CustomDNS)\. 
+ For Oracle versions prior to 12c, your DB instance must also use the XML DB option\. For more information, see [Oracle XML DB](Appendix.Oracle.Options.XMLDB.md)\. 

## Adding the Oracle UTL\_MAIL Option<a name="Oracle.Options.UTLMAIL.Add"></a>

The general process for adding the Oracle UTL\_MAIL option to a DB instance is the following: 

1. Create a new option group, or copy or modify an existing option group\.

1. Add the option to the option group\.

1. Associate the option group with the DB instance\.

After you add the UTL\_MAIL option, as soon as the option group is active, UTL\_MAIL is active\. 

**To add the UTL\_MAIL option to a DB instance**

1. Determine the option group you want to use\. You can create a new option group or use an existing option group\. If you want to use an existing option group, skip to the next step\. Otherwise, create a custom DB option group with the following settings: 

   1. For **Engine**, choose the edition of Oracle you want to use\. 

   1. For **Major engine version**, choose the version of your DB instance\. 

   For more information, see [Creating an Option Group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.Create)\. 

1. Add the **UTL\_MAIL** option to the option group\. For more information about adding options, see [Adding an Option to an Option Group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.AddOption)\.  

1. Apply the option group to a new or existing DB instance: 
   + For a new DB instance, you apply the option group when you launch the instance\. For more information, see [Creating an Amazon RDS DB Instance](USER_CreateDBInstance.md)\. 
   + For an existing DB instance, you apply the option group by modifying the instance and attaching the new option group\. For more information, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md)\. 

## Using Oracle UTL\_MAIL<a name="Oracle.Options.UTLMAIL.Using"></a>

After you enable the UTL\_MAIL option, you must configure the SMTP server before you can begin using it\. 

You configure the SMTP server by setting the SMTP\_OUT\_SERVER parameter to a valid IP address or public DNS name\. For the SMTP\_OUT\_SERVER parameter, you can specify a comma\-separated list of the addresses of multiple servers\. If the first server is unavailable, UTL\_MAIL tries the next server, and so on\. 

You can set the default SMTP\_OUT\_SERVER for a DB instance by using a [DB parameter group](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_WorkingWithParamGroups.html)\. You can set the SMTP\_OUT\_SERVER parameter for a session by running the following code on your database on your DB instance\. 

```
1. ALTER SESSION SET smtp_out_server = mailserver.domain.com:25;
```

After the UTL\_MAIL option is enabled, and your SMTP\_OUT\_SERVER is configured, you can send mail by using the `SEND` procedure\. For more information, see [UTL\_MAIL](http://docs.oracle.com/cd/B19306_01/appdev.102/b14258/u_mail.htm#BABFJJBD) in the Oracle documentation\. 

## Removing the Oracle UTL\_MAIL Option<a name="Oracle.Options.UTLMAIL.Remove"></a>

You can remove Oracle UTL\_MAIL from a DB instance\. 

To remove UTL\_MAIL from a DB instance, do one of the following: 
+ To remove UTL\_MAIL from multiple DB instances, remove the UTL\_MAIL option from the option group they belong to\. This change affects all DB instances that use the option group\. For more information, see [Removing an Option from an Option Group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.RemoveOption)\. 
+ To remove UTL\_MAIL from a single DB instance, modify the DB instance and specify a different option group that doesn't include the UTL\_MAIL option\. You can specify the default \(empty\) option group, or a different custom option group\. For more information, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md)\. 

## Troubleshooting<a name="Oracle.Options.UTLMAIL.Troubleshooting"></a>

The following are issues you might encounter when you use UTL\_MAIL with Amazon RDS\. 
+ Throttling\. SMTP port \(25\) is throttled based on the elastic network interface owner's policies\. If you can successfully send email by using UTL\_MAIL, and you see the error `ORA-29278: SMTP transient error: 421 Service not available`, you are possibly being throttled\. If you experience throttling with email delivery, we recommend that you implement a backoff algorithm\. For more information about backoff algorithms, see [Error Retries and Exponential Backoff in AWS](https://docs.aws.amazon.com/general/latest/gr/api-retries.html) and [How to handle a "Throttling – Maximum sending rate exceeded" error](http://aws.amazon.com/blogs/ses/how-to-handle-a-throttling-maximum-sending-rate-exceeded-error/)\. 

  You can request that this throttle be removed\. For more information, see [How do I remove the throttle on port 25 from my EC2 instance?](http://aws.amazon.com/premiumsupport/knowledge-center/ec2-port-25-throttle/)\.

## Related Topics<a name="Oracle.Options.UTLMAIL.Related"></a>
+ [Working with Option Groups](USER_WorkingWithOptionGroups.md)
+ [Options for Oracle DB Instances](Appendix.Oracle.Options.md)