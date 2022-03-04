# Customer\-owned IP addresses for RDS on Outposts<a name="rds-on-outposts.coip"></a>

AWS Outposts uses information that you provide about your on\-premises network to create an address pool, known as a *customer\-owned IP address pool* \(CoIP pool\)\. *Customer\-owned IP addresses* \(CoIPs\) provide local or external connectivity to resources in your Outpost subnets through your on\-premises network\. For more information about CoIPs, see [Customer\-owned IP addresses](https://docs.aws.amazon.com/outposts/latest/userguide/outposts-networking-components.html#ip-addressing) in the *AWS Outposts User Guide*\.

Each RDS on Outposts DB instance has a private IP address for traffic inside its virtual private cloud \(VPC\)\. This private IP address isn't publicly accessible\. You can use the **Public** option to designate whether the DB instance also has a public IP address in addition to the private IP address\. Using the public IP address for connections routes them through the internet and can result in high latencies in some cases\.

Instead of using these private and public IP addresses, RDS on Outposts supports enabling a CoIP for DB instances through their subnets\. When you enable a CoIP for an RDS on Outposts DB instance, you connect to the DB instance with the DB instance endpoint\. RDS on Outposts automatically uses the CoIP for all connections from both inside and outside of the VPC\.

CoIPs can provide the following benefits for RDS on Outposts DB instances:
+ Lower connection latency
+ Enhanced security

You can enable or disable a CoIP for an RDS on Outposts DB instance using the AWS Management Console, the AWS CLI, or the RDS API:
+ With the AWS Management Console, use the **Customer\-owned IP address \(CoIP\)** setting in **Access type** to enable a CoIP\. Use one of the other settings to disable it\.  
![\[The Customer-owned IP address (CoIPs) setting in the AWS Management Console.\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/outpost-coip.png)
+ With the AWS CLI, use the `--enable-customer-owned-ip | --no-enable-customer-owned-ip` option\.
+ With the RDS API, use the `EnableCustomerOwnedIp` parameter\.

You can enable or disable a CoIP when you perform any of the following actions:
+ Create a DB instance

  For more information, see [Creating DB instances for Amazon RDS on AWS Outposts](rds-on-outposts.creating.md)\.
+ Modify a DB instance

  For more information, see [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)\.
+ Restore a DB instance from a snapshot

  For more information, see [Restoring from a DB snapshot](USER_RestoreFromSnapshot.md)\.
+ Restore a DB instance to a specified time

  For more information, see [Restoring a DB instance to a specified time](USER_PIT.md)\.

**Note**  
If you enable a CoIP for a DB instance, but Amazon RDS is unable to allocate a CoIP for the DB instance, the DB instance status is changed to **incompatible\-network**\. For more information about the DB instance status, see [Viewing Amazon RDS DB instance status](accessing-monitoring.md#Overview.DBInstance.Status)\.

The following limitations apply to CoIP support for RDS on Outposts DB instances:
+ When a CoIP is enabled for a DB instance, make sure that public accessibility is disabled for the DB instance\.
+ You can't assign a CoIP from a CoIP pool to a DB instance\. When you enable a CoIP for a DB instance, Amazon RDS automatically assigns a CoIP from a CoIP pool to the DB instance\.
+ You must use the AWS account that owns the Outpost resources \(owner\) or share the following resources with other AWS accounts \(consumers\) in the same organization\.
  + The Outpost
  + The local gateway \(LGW\) route table for the DB instance's VPC
  + The CoIP pool or pools for the LGW route table

  For more information, see [ Working with shared AWS Outposts resources](https://docs.aws.amazon.com/outposts/latest/userguide/sharing-outposts.html) in the *AWS Outposts User Guide*\.