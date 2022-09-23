# Working with Multi\-AZ deployments for Amazon RDS on AWS Outposts<a name="rds-on-outposts.maz"></a>

For Multi\-AZ deployments, Amazon RDS creates a primary DB instance on one AWS Outpost\. RDS synchronously replicates the data to a standby DB instance on a different Outpost\. 

Multi\-AZ deployments on AWS Outposts operate like Multi\-AZ deployments in AWS Regions, but with the following differences:
+ They require a local connection between two or more Outposts\.
+ They require customer\-owned IP \(CoIP\) pools\. For more information, see [Customer\-owned IP addresses for Amazon RDS on AWS Outposts](rds-on-outposts.coip.md)\.
+ Replication runs on your local network\.

Multi\-AZ on AWS Outposts is available for all supported versions of MySQL and PostgreSQL on RDS on Outposts\. Local backups aren't supported for Multi\-AZ deployments\. For more information, see [Creating DB instances for Amazon RDS on AWS Outposts](rds-on-outposts.creating.md)\.

## Working with the shared responsibility model<a name="rds-on-outposts.maz.shared"></a>

Although AWS uses commercially reasonable efforts to provide DB instances configured for high availability, the availability uses a shared responsibility model\. The ability of RDS on Outposts to fail over and repair DB instances requires each of your Outposts to be connected to its AWS Region\.

RDS on Outposts also requires connectivity between the Outpost that is hosting the primary DB instance and the Outpost that is hosting the standby DB instance for synchronous replication\. Any impact to this connection can prevent RDS on Outposts from performing a failover\.

You might see elevated latencies for a standard DB instance deployment as a result of the synchronous data replication\. The bandwidth and latency of the connection between the Outpost hosting the primary DB instance and the Outpost hosting the standby DB instance directly affect latencies\. For more information, see [Prerequisites](#rds-on-outposts.maz.prereqs)\.

## Improving availability<a name="rds-on-outposts.maz.tips"></a>

We recommend the following actions to improve availability:
+ Allocate enough additional capacity for your mission\-critical applications to allow recovery and failover if there is an underlying host issue\. This applies to all Outposts that contain subnets in your DB subnet group\. For more information, see [Resilience in AWS Outposts](https://docs.aws.amazon.com/outposts/latest/userguide/disaster-recovery-resiliency.html)\.
+ Create multiple connections from each of your Outposts to the AWS Region\.
+ Use more than two Outposts\. Having more than two Outposts allows Amazon RDS to recover a DB instance\. RDS does this recovery by moving the DB instance to another Outpost if the current Outpost experiences a failure\.
+ Provide dual power sources and redundant network connectivity for your Outpost\.

We recommend the following for your local networks:
+ The round trip time \(RTT\) latency between the Outpost hosting your primary DB instance and the Outpost hosting your standby DB instance directly affects write latency\. Keep the RTT latency between the AWS Outposts in the low single\-digit milliseconds\. We recommend not more than 5 milliseconds, but your requirements might vary\.

  You can find the net impact to network latency in the Amazon CloudWatch metrics for `WriteLatency`\. For more information, see [Amazon CloudWatch metrics for Amazon RDS](rds-metrics.md)\.
+ The availability of the connection between the Outposts affects the overall availability of your DB instances\. Have redundant network connectivity between the Outposts\.

## Prerequisites<a name="rds-on-outposts.maz.prereqs"></a>

Multi\-AZ deployments on RDS on Outposts have the following prerequisites:
+ Have at least two Outposts, connected over local connections and attached to different Availability Zones in an AWS Region\.
+ Make sure that your DB subnet groups contain the following:
  + At least two subnets in at least two Availability Zones in a given AWS Region\.
  + Subnets only in Outposts\.
  + At least two subnets in at least two Outposts within the same virtual private cloud \(VPC\)\.
+ Associate your DB instance's VPC with all of your local gateway route tables\. This association is necessary because replication runs over your local network using your Outposts' local gateways\. 

  For example, suppose that your VPC contains subnet\-A in Outpost\-A and subnet\-B in Outpost\-B\. Outpost\-A uses LocalGateway\-A \(LGW\-A\), and Outpost\-B uses LocalGateway\-B \(LGW\-B\)\. LGW\-A has RouteTable\-A, and LGW\-B has RouteTable\-B\. You want to use both RouteTable\-A and RouteTable\-B for replication traffic\. To do this, associate your VPC with both RouteTable\-A and RouteTable\-B\.

  For more information about how to create an association, see the Amazon EC2 [create\-local\-gateway\-route\-table\-vpc\-association](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-local-gateway-route-table-vpc-association.html) AWS CLI command\.
+ Make sure that your Outposts use customer\-owned IP \(CoIP\) routing\. Each route table must also each have at least one address pool\. Amazon RDS allocates an additional IP address each for the primary and standby DB instances for data synchronization\.
+ Make sure that the AWS account that owns the RDS DB instances owns the local gateway route tables and CoIP pools\. Or make sure it's part of a Resource Access Manager share with access to the local gateway route tables and CoIP pools\.  
+ Make sure that the IP addresses in your CoIP pools can be routed from one Outpost local gateway to the others\.
+ Make sure that the VPC's CIDR blocks \(for example, 10\.0\.0\.0/4\) and your CoIP pool CIDR blocks don't contain IP addresses from Class E \(240\.0\.0\.0/4\)\. RDS uses these IP addresses internally\.
+ Make sure that you correctly set up outbound and related inbound traffic\. 

  RDS on Outposts establishes a virtual private network \(VPN\) connection between the primary and standby DB instances\. For this to work correctly, your local network must allow outbound and related inbound traffic for Internet Security Association and Key Management Protocol \(ISAKMP\)\. It does so using User Datagram Protocol \(UDP\) port 500 and IP Security \(IPsec\) Network Address Translation Traversal \(NAT\-T\) using UDP port 4500\.

For more information on CoIPs, see [Customer\-owned IP addresses for Amazon RDS on AWS Outposts](rds-on-outposts.coip.md) in this guide, and [Customer\-owned IP addresses](https://docs.aws.amazon.com/outposts/latest/userguide/how-racks-work.html#ip-addressing) in the *AWS Outposts User Guide*\.

## Working with API operations for Amazon EC2 permissions<a name="rds-on-outposts.maz.api"></a>

Regardless of whether you use CoIPs for your DB instance on AWS Outposts, RDS requires access to your CoIP pool resources\. RDS can call the following EC2 permissions API operations for CoIPs on your behalf for Multi\-AZ deployments:
+ `CreateCoipPoolPermission` – When you create a Multi\-AZ DB instance on RDS on Outposts
+ `DeleteCoipPoolPermission` – When you delete a Multi\-AZ DB instance on RDS on Outposts

These API operations grant to, or remove from, internal RDS accounts the permission to allocate elastic IP addresses from the CoIP pool specified by the permission\. You can view these IP addresses using the `DescribeCoipPoolUsage` API operation\. For more information on CoIPs, see [Customer\-owned IP addresses for Amazon RDS on AWS Outposts](rds-on-outposts.coip.md) and [Customer\-owned IP addresses](https://docs.aws.amazon.com/outposts/latest/userguide/how-racks-work.html#ip-addressing) in the *AWS Outposts User Guide*\.

RDS can also call the following EC2 permission API operations for local gateway route tables on your behalf for Multi\-AZ deployments:
+ `CreateLocalGatewayRouteTablePermission` – When you create a Multi\-AZ DB instance on RDS on Outposts
+ `DeleteLocalGatewayRouteTablePermission` – When you delete a Multi\-AZ DB instance on RDS on Outposts

These API operations grant to, or remove from, internal RDS accounts the permission to associate internal RDS VPCs with your local gateway route tables\. You can view these route table–VPC associations using the `DescribeLocalGatewayRouteTableVpcAssociations` API operations\.