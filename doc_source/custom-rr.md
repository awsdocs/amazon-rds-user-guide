# Working with read replicas for RDS Custom for Oracle<a name="custom-rr"></a>

You can create read replicas for RDS Custom for Oracle DB instances\. Read replica creation is similar to that in Amazon RDS, but with some important differences\. For general information on creating and managing read replicas, see [Working with read replicas](USER_ReadRepl.md) and [Working with Oracle replicas for Amazon RDS](oracle-read-replicas.md)\.

Not all options are supported when you create RDS Custom read replicas\. For more information, see the documentation for the [create\-db\-instance\-read\-replica](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance-read-replica.html) AWS CLI command\.

As with RDS for Oracle, you can have up to six managed read replicas\. You can create your own manually configured \(external\) RDS Custom for Oracle read replicas, which don't count toward the limit, but they're outside the support perimeter\. For more information on the support perimeter, see [Responding to an unsupported configuration](custom-troubleshooting.md#custom-troubleshooting.support-perimeter)\.

Read replicas are named after the database unique name, with letters appended sequentially: `DB_UNIQUE_NAME_X`, for example `ORCL_A`\. The first six letters, A–F, are reserved for RDS Custom\. Database parameters are copied from the primary DB instance to the replicas\. For more information, see [DB\_UNIQUE\_NAME](https://docs.oracle.com/database/121/REFRN/GUID-3547C937-5DDA-49FF-A9F9-14FF306545D8.htm#REFRN10242) in the Oracle documentation\.

RDS Custom read replicas use the same backup retention period as the primary DB instance by default\. You can modify the backup retention period\. Backing up, restoring, and point\-in\-time recovery \(PITR\) are supported\. For more information on backing up and restoring RDS Custom DB instances, see [Backing up and restoring an Amazon RDS Custom DB instance](custom-backup.md)\.

## Network considerations<a name="custom-rr.network"></a>

Make sure that your network configuration supports read replicas\. Be aware of the following considerations:
+ Make sure to enable port 1140 for both inbound and outbound communication within your virtual private cloud \(VPC\) for the primary DB instance and all of the replicas\. This is required for Oracle Data Guard communication between the read replicas\.
+ There is a network validation step during read replica creation\. If end\-to\-end network connectivity between the primary DB instance and the new replica isn't possible, read replica creation fails\. The replica is put in the `INCOMPATIBLE_NETWORK` state\.
+ For external read replicas, such as those you create on Amazon EC2 or on\-premises, use another port and listener for Oracle Data Guard replication\. Trying to use port 1140 could cause conflicts with RDS Custom automation\.

## Considerations for the tnsnames\.ora file<a name="custom-rr.tnsnames"></a>

The `tnsnames.ora` file contains network service names mapped to listener protocol addresses\. On RDS Custom for Oracle, this file is in the `/rdsdbdata/config` directory\. Be aware of the following considerations:
+ Entries in `tnsnames.ora` prefixed with `rds_custom_` are reserved for RDS Custom when handling read replica operations\.

  When creating manual entries in `tnsnames.ora`, don't use this prefix\.
+ In some cases, you might want to switch over or fail over manually, or use failover technologies such as Fast\-Start Failover \(FSFO\)\. If so, make sure to manually synchronize `tnsnames.ora` entries from the primary DB instance to all of the standby instances\. This recommendation applies to both read replicas managed by RDS Custom and to external read replicas\.

  RDS Custom automation updates `tnsnames.ora` entries only on the primary DB instance\. Make sure also to synchronize when you add or remove a read replica\.

  If you don't synchronize the `tnsnames.ora` files and switch over or fail over manually, Oracle Data Guard on the primary DB instance might not be able to communicate with the read replicas\.

## Limitations<a name="custom-rr.limitations"></a>

RDS Custom for Oracle read replicas have the following limitations:
+ Cross\-Region read replicas aren't supported\.
+ Deleting the primary DB instance for a read replica isn't supported\. Delete the read replicas first, then delete the primary\.
+ Promoting a read replica using the [promote\-read\-replica](https://docs.aws.amazon.com/cli/latest/reference/rds/promote-read-replica.html) AWS CLI command isn't supported, but you can promote a read replica manually\.

  For information on promoting read replicas manually, see the white paper [Enabling high availability with Data Guard on Amazon RDS Custom for Oracle](https://d1.awsstatic.com/whitepapers/enabling-high-availability-with-data-guard-on-amazon-rds-custom-for-oracle.pdf)\.
+ RDS Custom uses the `RDS_DATAGUARD` database user to administer the Oracle Data Guard configuration for the DB instance\. This user is reserved for RDS Custom automation\.

  Don't modify the `RDS_DATAGUARD` user\. Doing so can result in undesired outcomes, such as not being able to create read replicas for your RDS Custom DB instance\.
+ The Oracle Data Guard `CommunicationTimeout` parameter is set to 15 seconds for RDS Custom DB instances and can't be changed\.
+ The replication user password is stored in AWS Secrets Manager, tagged with the DB resource ID\. Each read replica has its own secret in Secrets Manager\. This password is required to administer the Oracle Data Guard configuration on the host\. The format for the secret is the following\.

  ```
  do-not-delete-rds-custom-db-DB_resource_id-6-digit_UUID-dg
  ```

  Don't change the password, because this might put your read replica outside the support perimeter\. For more information, see [Responding to an unsupported configuration](custom-troubleshooting.md#custom-troubleshooting.support-perimeter)\.
+ While RDS Custom is creating a read replica, RDS Custom automation temporarily pauses the cleanup of redo logs\. It does this so that these logs can be applied to the new read replica when it's available\.
+ RDS Custom detects instance role changes upon manual failover, such as FSFO, for read replicas managed by RDS Custom, but not for external read replicas\.

  The role change is noted in the event log\. You can also see the new state by using the [describe\-db\-instances](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-instances.html) AWS CLI command\.
+ RDS Custom detects high replication lag for RDS Custom–managed read replicas, but not for external read replicas\.

  High replication lag produces the `Replication has stopped` event\. You can also see the replication status by using the [describe\-db\-instances](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-instances.html) AWS CLI command, but there might be a delay for it to be updated\.