# Using Kerberos authentication with Amazon RDS for Oracle<a name="oracle-kerberos"></a>

You can use Kerberos authentication to authenticate users when they connect to your Amazon RDS DB instance running Oracle\. In this configuration, your DB instance works with AWS Directory Service for Microsoft Active Directory, also called AWS Managed Microsoft AD\. When users authenticate with an Oracle DB instance joined to the trusting domain, authentication requests are forwarded to the directory that you create with AWS Directory Service\.

Keeping all of your credentials in the same directory can save you time and effort\. You have a centralized place for storing and managing credentials for multiple database instances\. A directory can also improve your overall security profile\.

Amazon RDS supports Kerberos authentication for Oracle DB instances in the following AWS Regions: 
+ US East \(Ohio\)
+ US East \(N\. Virginia\)
+ US West \(N\. California\)
+ US West \(Oregon\)
+ Asia Pacific \(Mumbai\)
+ Asia Pacific \(Seoul\)
+ Asia Pacific \(Singapore\)
+ Asia Pacific \(Sydney\)
+ Asia Pacific \(Tokyo\)
+ Canada \(Central\)
+ Europe \(Frankfurt\)
+ Europe \(Ireland\)
+ Europe \(London\)
+ Europe \(Stockholm\)
+ South America \(São Paulo\)
+ AWS GovCloud \(US\-East\)
+ AWS GovCloud \(US\-West\)

**Note**  
Kerberos authentication isn't supported for DB instance classes that are deprecated for Oracle DB instances\. For more information, see [DB instance class support for Oracle](CHAP_Oracle.md#Oracle.Concepts.InstanceClasses)\.

**Topics**
+ [Setting up Kerberos authentication for Oracle DB instances](oracle-kerberos-setting-up.md)
+ [Managing a DB instance in a domain](oracle-kerberos-managing.md)
+ [Connecting to Oracle with Kerberos authentication](oracle-kerberos-connecting.md)