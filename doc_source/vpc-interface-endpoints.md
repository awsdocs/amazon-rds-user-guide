# Amazon RDS and Interface VPC Endpoints \(AWS PrivateLink\)<a name="vpc-interface-endpoints"></a>

You can establish a private connection between your VPC and Amazon RDS API endpoints by creating an *interface VPC endpoint*\. Interface endpoints are powered by [AWS PrivateLink](http://aws.amazon.com/privatelink)\. 

AWS PrivateLink enables you to privately access Amazon RDS API operations without an internet gateway, NAT device, VPN connection, or AWS Direct Connect connection\. Instances in your VPC don't need public IP addresses to communicate with Amazon RDS API endpoints to launch, modify, or terminate DB instances\. Your instances also don't need public IP addresses to use any of the available RDS API operations\. Traffic between your VPC and Amazon RDS doesn't leave the Amazon network\. 

Each interface endpoint is represented by one or more elastic network interfaces in your subnets\. For more information on elastic network interfaces, see [Elastic network interfaces](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-eni.html) in the *Amazon EC2 User Guide\.* 

For more information, see [Interface VPC endpoints \(AWS PrivateLink\)](https://docs.aws.amazon.com/vpc/latest/userguide/vpce-interface.html) in the *Amazon VPC User Guide*\. 

## Considerations for Amazon RDS VPC Endpoints<a name="vpc-endpoint-considerations"></a>

Before you set up an interface VPC endpoint for Amazon RDS API endpoints, ensure that you review [Interface endpoint properties and limitations](https://docs.aws.amazon.com/vpc/latest/userguide/vpce-interface.html#vpce-interface-limitations) in the *Amazon VPC User Guide*\. 

Amazon RDS supports making calls to all of its API actions from your VPC\.

VPC endpoint policies are supported for Amazon RDS\. By default, full access to Amazon RDS is allowed through the endpoint\. For more information, see [Controlling access to services with VPC endpoints](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-endpoints-access.html) in the *Amazon VPC User Guide*\.

## Availability<a name="rds-and-vpc-interface-endpoints-availability"></a>

Amazon RDS currently supports VPC endpoints in the following AWS Regions:
+ US East \(Ohio\)
+ US East \(N\. Virginia\)
+ US West \(N\. California\)
+ US West \(Oregon\)
+ Asia Pacific \(Hong Kong\)
+ Asia Pacific \(Mumbai\)
+ Asia Pacific \(Seoul\)
+ Asia Pacific \(Singapore\)
+ Asia Pacific \(Sydney\)
+ Asia Pacific \(Tokyo\)
+ Canada \(Central\)
+ Europe \(Frankfurt\)
+ Europe \(Ireland\)
+ Europe \(London\)
+ Europe \(Paris\)
+ Europe \(Stockholm\)
+ Middle East \(Bahrain\)
+ South America \(São Paulo\)
+ AWS GovCloud \(US\-East\)
+ AWS GovCloud \(US\-West\)

## Creating an Interface VPC Endpoint for Amazon RDS<a name="vpc-endpoint-create"></a>

You can create a VPC endpoint for the Amazon RDS API using either the Amazon VPC console or the AWS Command Line Interface \(AWS CLI\)\. For more information, see [Creating an interface endpoint](https://docs.aws.amazon.com/vpc/latest/userguide/vpce-interface.html#create-interface-endpoint) in the *Amazon VPC User Guide*\.

Create a VPC endpoint for Amazon RDS using the service name `com.amazonaws.region.rds`\.

If you enable private DNS for the endpoint, you can make API requests to Amazon RDS using its default DNS name for the Region, for example `rds.us-east-1.amazonaws.com`\.

For more information, see [Accessing a service through an interface endpoint](https://docs.aws.amazon.com/vpc/latest/userguide/vpce-interface.html#access-service-though-endpoint) in the *Amazon VPC User Guide*\.

## Creating a VPC Endpoint Policy for Amazon RDS<a name="vpc-endpoint-policy"></a>

You can attach an endpoint policy to your VPC endpoint that controls access to Amazon RDS\. The policy specifies the following information:
+ The principal that can perform actions\.
+ The actions that can be performed\.
+ The resources on which actions can be performed\.

For more information, see [Controlling access to services with VPC endpoints](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-endpoints-access.html) in the *Amazon VPC User Guide*\. 

**Example: VPC Endpoint Policy for Amazon RDS Actions**  
The following is an example of an endpoint policy for Amazon RDS\. When attached to an endpoint, this policy grants access to the listed Amazon RDS actions for all principals on all resources\.

```
{
   "Statement":[
      {
         "Principal":"*",
         "Effect":"Allow",
         "Action":[
            "rds:CreateDBInstance",
            "rds:ModifyDBInstance",
            "rds:CreateDBSnapshot"
         ],
         "Resource":"*"
      }
   ]
}
```

**VPC Endpoint Policy That Denies All Access From a Specified AWS Account**  
The following VPC endpoint policy denies AWS account `123456789012` all access to resources using the endpoint\. The policy allows all actions from other accounts\.

```
{
  "Statement": [
    {
      "Action": "*",
      "Effect": "Allow",
      "Resource": "*",
      "Principal": "*"
    },
    {
      "Action": "*",
      "Effect": "Deny",
      "Resource": "*",
      "Principal": {
        "AWS": [
          "123456789012"
        ]
      }
   ]
}
```