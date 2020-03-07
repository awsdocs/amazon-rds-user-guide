# Managing a DB Instance in a Domain<a name="postgresql-kerberos-managing"></a>

You can use the console, the CLI, or the RDS API to manage your DB instance and its relationship with your Microsoft Active Directory\. For example, you can associate an Active Directory to enable Kerberos authentication\. You can also remove the association for an Active Directory to disable Kerberos authentication\. You can also move a DB instance to be externally authenticated by one Microsoft Active Directory to another\.

For example, using the CLI, you can do the following:
+ To reattempt enabling Kerberos authentication for a failed membership, use the [modify\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) CLI command\. Specify the current membership's directory ID for the `--domain` option\.
+ To disable Kerberos authentication on a DB instance, use the [modify\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) CLI command\. Specify `none` for the `--domain` option\.
+ To move a DB instance from one domain to another, use the [modify\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) CLI command\. Specify the domain identifier of the new domain for the `--domain` option\.

## Understanding Domain Membership<a name="postgresql-kerberos-managing.understanding"></a>

After you create or modify your DB instance, it becomes a member of the domain\. You can view the status of the domain membership in the console or by running the [describe\-db\-instances](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-instances.html) CLI command\. The status of the DB instance can be one of the following: 
+ `kerberos-enabled` – The DB instance has Kerberos authentication enabled\.
+ `enabling-kerberos` – AWS is in the process of enabling Kerberos authentication on this DB instance\.
+ `pending-enable-kerberos` – Enabling Kerberos authentication is pending on this DB instance\.
+ `pending-maintenance-enable-kerberos` – AWS will attempt to enable Kerberos authentication on the DB instance during the next scheduled maintenance window\.
+ `pending-disable-kerberos` – Disabling Kerberos authentication is pending on this DB instance\.
+ `pending-maintenance-disable-kerberos` – AWS will attempt to disable Kerberos authentication on the DB instance during the next scheduled maintenance window\.
+ `enable-kerberos-failed` – A configuration problem prevented AWS from enabling Kerberos authentication on the DB instance\. Correct the configuration problem before reissuing the command to modify the DB instance\.
+ `disabling-kerberos` – AWS is in the process of disabling Kerberos authentication on this DB instance\.

A request to enable Kerberos authentication can fail because of a network connectivity issue or an incorrect IAM role\. In some cases, the attempt to enable Kerberos authentication might fail when you create or modify a DB instance\. If so, make sure that you are using the correct IAM role, then modify the DB instance to join the domain\.

**Note**  
Only Kerberos authentication with RDS for PostgreSQL sends traffic to the domain's DNS servers\. All other DNS requests are treated as outbound network access on your DB instances running PostgreSQL\. For more information about outbound network access with RDS for PostgreSQL, see [Using a Custom DNS Server for Outbound Network Access](Appendix.PostgreSQL.CommonDBATasks.md#Appendix.PostgreSQL.CommonDBATasks.CustomDNS)\.