# Troubleshooting connections to your Oracle DB instance<a name="USER_ConnectToOracleInstance.Troubleshooting"></a>

The following are issues you might encounter when you try to connect to your Oracle DB instance\. 


****  

| Issue | Troubleshooting suggestions | 
| --- | --- | 
|  Unable to connect to your DB instance\.   |  For a newly created DB instance, the DB instance has a status of **creating** until it is ready to use\. When the state changes to **available**, you can connect to the DB instance\. Depending on the DB instance class and the amount of storage, it can take up to 20 minutes before the new DB instance is available\.   | 
|  Unable to connect to your DB instance\.   |  If you can't send or receive communications over the port that you specified when you created the DB instance, you can't connect to the DB instance\. Check with your network administrator to verify that the port you specified for your DB instance allows inbound and outbound communication\.   | 
|  Unable to connect to your DB instance\.   |  The access rules enforced by your local firewall and the IP addresses you authorized to access your DB instance in the security group for the DB instance might not match\. The problem is most likely the inbound or outbound rules on your firewall\. You can add or edit an inbound rule in the security group\. For **Source**, choose **My IP**\. This allows access to the DB instance from the IP address detected in your browser\. For more information, see [Amazon VPC VPCs and Amazon RDS](USER_VPC.md)\. For more information about security groups, see [Controlling access with security groups](Overview.RDSSecurityGroups.md)\.  To walk through the process of setting up rules for your security group, see [Tutorial: Create a VPC for use with a DB instance \(IPv4 only\)](CHAP_Tutorials.WebServerDB.CreateVPC.md)\.   | 
|  **Connect failed because target host or object does not exist – Oracle, Error: ORA\-12545 **   |  Make sure that you specified the server name and port number correctly\. For **Server name**, enter the DNS name from the console\.  For information about finding the DNS name and port number for a DB instance, see [Finding the endpoint of your Oracle DB instance](USER_Endpoint.md)\.  | 
|  **Invalid username/password; logon denied – Oracle, Error: ORA\-01017**   |  You were able to reach the DB instance, but the connection was refused\. This is usually caused by providing an incorrect user name or password\. Verify the user name and password, and then retry\.   | 
|  **TNS:listener does not currently know of SID given in connect descriptor \- Oracle, ERROR: ORA\-12505**   |  Ensure the correct SID is entered\. The SID is the same as your DB name\. Find the DB name in the **Configuration** tab of the **Databases** page for your instance\. You can also find the DB name using the AWS CLI:  <pre>aws rds describe-db-instances --query 'DBInstances[*].[DBInstanceIdentifier,DBName]' --output text</pre>  | 

For more information on connection issues, see [Can't connect to Amazon RDS DB instance](CHAP_Troubleshooting.md#CHAP_Troubleshooting.Connecting)\.