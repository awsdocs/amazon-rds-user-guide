# Regions, Availability Zones, and Local Zones<a name="Concepts.RegionsAndAvailabilityZones"></a>

Amazon cloud computing resources are hosted in multiple locations world\-wide\. These locations are composed of AWS Regions, Availability Zones, and Local Zones\. Each *AWS Region* is a separate geographic area\. Each AWS Region has multiple, isolated locations known as *Availability Zones*\.

**Note**  
For information about finding the Availability Zones for an AWS Region, see [Describing Your Regions, Availability Zones, and Local Zones](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-regions-availability-zones.html#using-regions-availability-zones-describe) in the Amazon EC2 documentation\.

By using Local Zones, you can place resources, such as compute and storage, in multiple locations closer to your users\. Amazon RDS enables you to place resources, such as DB instances, and data in multiple locations\. Resources aren't replicated across AWS Regions unless you do so specifically\.

Amazon operates state\-of\-the\-art, highly\-available data centers\. Although rare, failures can occur that affect the availability of instances that are in the same location\. If you host all your instances in a single location that is affected by such a failure, none of your instances is available\.

![\[AWS Region\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/Con-AZ-Local.png)

It is important to remember that each AWS Region is completely independent\. Any Amazon RDS activity you initiate \(for example, creating database instances or listing available database instances\) runs only in your current default AWS Region\. The default AWS Region can be changed in the console, by setting the EC2\_REGION environment variable, or it can be overridden by using the `--region` parameter with the AWS Command Line Interface \(AWS CLI\)\. For more information, see [Configuring the AWS Command Line Interface](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-started.html), specifically the sections about environment variables and command line options\. 

Amazon RDS supports a special AWS Region called AWS GovCloud \(US\-West\) that is designed to allow US government agencies and customers to move more sensitive workloads into the cloud\. AWS GovCloud \(US\-West\) addresses the US government's specific regulatory and compliance requirements\. For more information about AWS GovCloud \(US\-West\), see [What Is AWS GovCloud \(US\-West\)?](https://docs.aws.amazon.com/govcloud-us/latest/UserGuide/whatis.html) 

To create or work with an Amazon RDS DB instance in a specific AWS Region, use the corresponding regional service endpoint\. 

## AWS Regions<a name="Concepts.RegionsAndAvailabilityZones.Regions"></a>

Each AWS Region is designed to be isolated from the other AWS Regions\. This design achieves the greatest possible fault tolerance and stability\.

When you view your resources, you see only the resources that are tied to the AWS Region that you specified\. This is because AWS Regions are isolated from each other, and we don't automatically replicate resources across AWS Regions\.

## Availability Zones<a name="Concepts.RegionsAndAvailabilityZones.AvailabilityZones"></a>

When you create a DB instance, you can choose an Availability Zone or have AWS choose one for you\. An Availability Zone is represented by an AWS Region code followed by a letter identifier \(for example, `us-east-1a`\)\.

In some cases, you might distribute your DB instances across multiple Availability Zones in a Multi\-AZ deployment\. In such cases, if one DB instance fails, you can design your application so that a DB instance in another Availability Zone can handle requests\. For more information about Multi\-AZ deployments, see [High Availability \(Multi\-AZ\) for Amazon RDS](Concepts.MultiAZ.md)\.

## Local Zones<a name="Concepts.RegionsAndAvailabilityZones.LocalZones"></a>

A *Local Zone* is an extension of an AWS Region that is geographically close to your users\. You can extend any VPC from the parent AWS Region into Local Zones by creating a new subnet and assigning it to the AWS Local Zone\. When you create a subnet in a Local Zone, your VPC is extended to that Local Zone\. The subnet in the Local Zone operates the same as other subnets in your VPC\.

When you create a DB instance, you can choose a subnet in a Local Zone\. Local Zones have their own connections to the internet and support AWS Direct Connect\. Thus, resources created in a Local Zone can serve local users with very low\-latency communications\. For more information, see [AWS Local Zones](http://aws.amazon.com/about-aws/global-infrastructure/localzones/)\.

A Local Zone is represented by an AWS Region code followed by an identifier that indicates the location, for example `us-west-2-lax-1a`\.

**Note**  
A Local Zone can't be included in a Multi\-AZ deployment\.

**To use a Local Zone**

1. Enable the Local Zone in the Amazon EC2 console\.

   For more information, see [Enabling Local Zones](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-regions-availability-zones.html#enable-zone-group) in the *Amazon EC2 User Guide for Linux Instances\.*

1. Create a subnet in the Local Zone\.

   For more information, see [Creating a Subnet in Your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/working-with-vpcs.html#AddaSubnet) in the *Amazon VPC User Guide\.*

1. Create a DB subnet group in the Local Zone\.

   When you create a DB subnet group, choose the Availability Zone group for the Local Zone\.

   For more information, see [Creating a DB Instance in a VPC](USER_VPC.WorkingWithRDSInstanceinaVPC.md#USER_VPC.InstanceInVPC)\.

1. Create a DB instance that uses the DB subnet group in the Local Zone\.

   For more information, see [Creating an Amazon RDS DB Instance](USER_CreateDBInstance.md)\.

**Important**  
Currently, Local Zones are only available in the US West \(Oregon\) Region\. In this AWS Region, the Los Angeles AWS Local Zone is available\.

## Region Availability<a name="Concepts.RegionsAndAvailabilityZones.Availability"></a>

The following table shows the AWS Regions where Amazon RDS is currently available and the endpoint for each Region\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Concepts.RegionsAndAvailabilityZones.html)

If you do not explicitly specify an endpoint, the US West \(Oregon\) endpoint is the default\.

When you work with a DB instance using the AWS CLI or API operations, make sure that you specify its regional endpoint\.