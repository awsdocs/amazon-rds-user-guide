# Multi\-AZ deployments for high availability<a name="Concepts.MultiAZ"></a>

Multi\-AZ deployments can have one standby or two standby DB instances\. When the deployment has one standby DB instance, it's called a *Multi\-AZ DB instance deployment*\. A Multi\-AZ DB instance deployment has one standby DB instance that provides failover support, but doesn't serve read traffic\. When the deployment has two standby DB instances, it's called a *Multi\-AZ DB cluster deployment*\. A Multi\-AZ DB cluster deployment has standby DB instances that provide failover support and can also serve read traffic\.

**Topics**
+ [Multi\-AZ DB instance deployments](Concepts.MultiAZSingleStandby.md)
+ [Multi\-AZ DB cluster deployments \(preview\)](multi-az-db-clusters-concepts.md)

**Note**  
Multi\-AZ DB clusters are in preview for RDS for MySQL and RDS for PostgreSQL and are subject to change\.