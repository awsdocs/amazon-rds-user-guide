# Considerations for security groups<a name="USER_ConnectToOracleInstance.Security"></a>

For you to connect to your DB instance, it must be associated with a security group that contains the necessary IP addresses and network configuration\. Your DB instance might use the default security group\. If you assigned a default, nonconfigured security group when you created the DB instance, the firewall prevents connections\. For information about creating a new security group, see [Controlling access with security groups](Overview.RDSSecurityGroups.md)\. 

After you create the new security group, you modify your DB instance to associate it with the security group\. For more information, see [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)\. 

You can enhance security by using SSL to encrypt connections to your DB instance\. For more information, see [Oracle Secure Sockets Layer](Appendix.Oracle.Options.SSL.md)\. 