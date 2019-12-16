# Infrastructure Security in Amazon RDS<a name="infrastructure-security"></a>

As a managed service, Amazon RDS is protected by the AWS global network security procedures that are described in the [Amazon Web Services: Overview of Security Processes](https://d0.awsstatic.com/whitepapers/Security/AWS_Security_Whitepaper.pdf) whitepaper\.

You use AWS published API calls to access Amazon RDS through the network\. Clients must support Transport Layer Security \(TLS\) 1\.0\. We recommend TLS 1\.2 or later\. Clients must also support cipher suites with perfect forward secrecy \(PFS\) such as Ephemeral Diffie\-Hellman \(DHE\) or Elliptic Curve Ephemeral Diffie\-Hellman \(ECDHE\)\. Most modern systems such as Java 7 and later support these modes\. 

Additionally, requests must be signed by using an access key ID and a secret access key that is associated with an IAM principal\. Or you can use the [AWS Security Token Service](https://docs.aws.amazon.com/STS/latest/APIReference/Welcome.html) \(AWS STS\) to generate temporary security credentials to sign requests\.

You can call these API operations from any network location\. However, Amazon RDS also supports resource\-based access policies, which can include restrictions based on the source IP address\. In addition, you can use Amazon RDS policies to control access from specific Amazon VPC endpoints or specific VPCs\. Effectively, this isolates network access to a given Amazon RDS resource from only the specific VPC within the AWS network\.

In addition, Amazon RDS offers features to help support infrastructure security\.

## Security Groups<a name="infrastructure-security.security-groups"></a>

Security groups control the access that traffic has in and out of a DB instance\. By default, network access is turned off to a DB instance\. You can specify rules in a security group that allow access from an IP address range, port, or Amazon EC2 security group\. After ingress rules are configured, the same rules apply to all DB instances that are associated with that security group\.

For more information, see [Controlling Access with Security Groups](Overview.RDSSecurityGroups.md)\.

## Public Accessibility<a name="infrastructure-security.publicly-accessible"></a>

When you launch a DB instance inside a virtual private cloud \(VPC\) based on the Amazon VPC service, you can turn on or off public accessibility for that instance\. To designate whether the DB instance that you create has a DNS name that resolves to a public IP address, you use the *Public accessibility* parameter\. By using this parameter, you can designate whether there is public access to the DB instance\. You can modify a DB instance to turn on or off public accessibility by modifying the *Public accessibility* parameter\.

For more information, see [Hiding a DB Instance in a VPC from the Internet](USER_VPC.WorkingWithRDSInstanceinaVPC.md#USER_VPC.Hiding)\.