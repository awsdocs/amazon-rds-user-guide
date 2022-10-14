# Configuring Kerberos authentication for Amazon RDS for Oracle<a name="oracle-kerberos"></a>

You can use Kerberos authentication to authenticate users when they connect to your Amazon RDS for Oracle DB instance\. In this configuration, your DB instance works with AWS Directory Service for Microsoft Active Directory, also called AWS Managed Microsoft AD\. When users authenticate with an RDS for Oracle DB instance joined to the trusting domain, authentication requests are forwarded to the directory that you create with AWS Directory Service\.

Keeping all of your credentials in the same directory can save you time and effort\. You have a centralized place for storing and managing credentials for multiple database instances\. A directory can also improve your overall security profile\.

## Region and version availability<a name="oracle-kerberos-setting-up.RegionVersionAvailability"></a>

Feature availability and support varies across specific versions of each database engine, and across AWS Regions\. For more information on version and Region availability of RDS for Oracle with Kerberos authentication, see [Kerberos authentication](Concepts.RDS_Fea_Regions_DB-eng.Feature.KerberosAuthentication.md)\.

**Note**  
Kerberos authentication isn't supported for DB instance classes that are deprecated for RDS for Oracle DB instances\. For more information, see [RDS for Oracle instance classes](Oracle.Concepts.InstanceClasses.md)\.

**Topics**
+ [Region and version availability](#oracle-kerberos-setting-up.RegionVersionAvailability)
+ [Setting up Kerberos authentication for Oracle DB instances](oracle-kerberos-setting-up.md)
+ [Managing a DB instance in a domain](oracle-kerberos-managing.md)
+ [Connecting to Oracle with Kerberos authentication](oracle-kerberos-connecting.md)