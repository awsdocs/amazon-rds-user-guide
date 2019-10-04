# Managing a DB Instance in a Domain<a name="oracle-kerberos-managing"></a>

You can use the console, the CLI, or the RDS API to manage your DB instance and its relationship with your Microsoft Active Directory\. For example, you can associate a Microsoft Active Directory to enable Kerberos authentication\. You can also disassociate a Microsoft Active Directory to disable Kerberos authentication\. You can also move a DB instance to be externally authenticated by one Microsoft Active Directory to another\.

For example, using the CLI, you can do the following: 
+ To reattempt enabling Kerberos authentication for a failed membership, use the [modify\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) CLI command and specify the current membership's directory ID for the `--domain` option\.
+ To disable Kerberos authentication on a DB instance, use the [modify\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) CLI command and specify `none` for the `--domain` option\.
+ To move a DB instance from one domain to another, use the [modify\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) CLI command and specify the domain identifier of the new domain for the `--domain` option\.

## Understanding Domain Membership<a name="oracle-kerberos-managing.understanding"></a>

After you create or modify your DB instance, the DB instance becomes a member of the domain\. You can view the status of the domain membership for the DB instance in the console or by running the [describe\-db\-instances](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-instances.html) CLI command\. The status of the DB instance can be one of the following: 
+ `kerberos-enabled` – The DB instance has Kerberos authentication enabled\.
+ `enabling-kerberos` – AWS is in the process of enabling Kerberos authentication on this DB instance\.
+ `pending-enable-kerberos` – Enabling Kerberos authentication is pending on this DB instance\.
+ `pending-maintenance-enable-kerberos` – AWS will attempt to enable Kerberos authentication on the DB instance during the next scheduled maintenance window\.
+ `pending-disable-kerberos` – Disabling Kerberos authentication is pending on this DB instance\.
+ `pending-maintenance-disable-kerberos` – AWS will attempt to disable Kerberos authentication on the DB instance during the next scheduled maintenance window\.
+ `enable-kerberos-failed` – A configuration problem has prevented AWS from enabling Kerberos authentication on the DB instance\. Correct the configuration problem before reissuing the command to modify the DB instance\.
+ `disabling-kerberos` – AWS is in the process of disabling Kerberos authentication on this DB instance\.

A request to enable Kerberos authentication can fail because of a network connectivity issue or an incorrect IAM role\. If the attempt to enable Kerberos authentication fails when you create or modify a DB instance, first make sure that you are using the correct IAM role\. Then modify the DB instance to join the domain\.

**Note**  
Only Kerberos authentication with Amazon RDS for Oracle sends traffic to the domain's DNS servers\. All other DNS requests are treated as outbound network access on your DB instances running Oracle\. For more information about outbound network access with Amazon RDS for Oracle, see [Setting Up a Custom DNS Server](Appendix.Oracle.CommonDBATasks.System.md#Appendix.Oracle.CommonDBATasks.CustomDNS)\.