# Regions and Availability Zones<a name="Concepts.RegionsAndAvailabilityZones"></a>

Amazon cloud computing resources are hosted in multiple locations world\-wide\. These locations are composed of AWS Regions and Availability Zones\. Each *AWS Region* is a separate geographic area\. Each AWS Region has multiple, isolated locations known as *Availability Zones*\. Amazon RDS provides you the ability to place resources, such as instances, and data in multiple locations\. Resources aren't replicated across AWS Regions unless you do so specifically\.

Amazon operates state\-of\-the\-art, highly\-available data centers\. Although rare, failures can occur that affect the availability of instances that are in the same location\. If you host all your instances in a single location that is affected by such a failure, none of your instances would be available\.

![\[Single AZ Scenario\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/Con-AZ.png)

It is important to remember that each AWS Region is completely independent\. Any Amazon RDS activity you initiate \(for example, creating database instances or listing available database instances\) runs only in your current default AWS Region\. The default AWS Region can be changed in the console, by setting the EC2\_REGION environment variable, or it can be overridden by using the `--region` parameter with the AWS Command Line Interface\. See [Configuring the AWS Command Line Interface](http://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-started.html), specifically, the sections on Environment Variables and Command Line Options for more information\. 

Amazon RDS supports a special AWS Region called AWS GovCloud \(US\) that is designed to allow US government agencies and customers to move more sensitive workloads into the cloud\. AWS GovCloud \(US\) addresses the US government's specific regulatory and compliance requirements\. For more information about AWS GovCloud \(US\), see [What Is AWS GovCloud \(US\)?](http://docs.aws.amazon.com/govcloud-us/latest/UserGuide/whatis.html) 

To create or work with an Amazon RDS DB instance in a specific AWS Region, use the corresponding regional service endpoint\. 

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Concepts.RegionsAndAvailabilityZones.html)

If you do not explicitly specify an endpoint, the US West \(Oregon\) endpoint is the default\. 

## Related Topics<a name="Overview.Concepts.RegionsAndAvailabilityZones.Related"></a>

+  [Regions and Availability Zones](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-regions-availability-zones.html) in the *Amazon Elastic Compute Cloud User Guide*\. 

+  [Amazon RDS DB Instances](Overview.DBInstance.md) 