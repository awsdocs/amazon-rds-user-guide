# Kerberos Authentication<a name="kerberos-authentication"></a>

Amazon RDS supports external authentication of database users using Kerberos and Microsoft Active Directory\. Kerberos is a network authentication protocol that uses tickets and symmetric\-key cryptography to eliminate the need to transmit passwords over the network\. Kerberos has been built into Active Directory and is designed to authenticate users to network resources, such as databases\.

Amazon RDS support for Kerberos and Active Directory provides the benefits of single sign\-on and centralized authentication of database users\. You can keep your user credentials in Active Directory\. Active Directory provides a centralized place for storing and managing credentials for multiple DB instances\.

You can enable your database users to authenticate against DB instances in two ways\. They can use credentials stored either in AWS Directory Service for Microsoft Active Directory or in your on\-premises Active Directory\.

Currently, RDS supports Kerberos authentication for Microsoft SQL Server, Oracle, and PostgreSQL DB instances\. For information about Kerberos authentication with a specific engine, see the following:
+ [Using Windows Authentication with a Microsoft SQL Server DB Instance](USER_SQLServerWinAuth.md)
+ [Using Kerberos Authentication for MySQL](mysql-kerberos.md)
+ [Using Kerberos Authentication with Amazon RDS for Oracle](oracle-kerberos.md)
+ [Using Kerberos Authentication with Amazon RDS for PostgreSQL](postgresql-kerberos.md)