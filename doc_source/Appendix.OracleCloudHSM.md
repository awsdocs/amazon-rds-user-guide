# Using AWS CloudHSM Classic to Store Amazon RDS Oracle TDE Keys<a name="Appendix.OracleCloudHSM"></a>

You can use AWS CloudHSM Classic with an Amazon RDS DB instance running Oracle Enterprise Edition to store keys when you use Oracle Transparent Data Encryption \(TDE\)\. AWS CloudHSM Classic is a service that provides a hardware appliance called a hardware security module \(HSM\) that performs secure key storage and cryptographic operations\. You enable an Amazon RDS DB instance to use AWS CloudHSM Classic by setting up an HSM appliance, setting the proper permissions for cross\-service access, and then setting up Amazon RDS and the DB instance that will use AWS CloudHSM Classic\. 

**Important**  
Review the following availability and pricing information before you setup AWS CloudHSM Classic:   
Amazon RDS supports AWS CloudHSM Classic for Oracle DB instances in the following regions:   US East \(N\. Virginia\), US West \(Oregon\), Asia Pacific \(Seoul\), Asia Pacific \(Singapore\), Asia Pacific \(Sydney\), Asia Pacific \(Tokyo\), EU \(Frankfurt\), EU \(Ireland\)\. 
AWS CloudHSM Classic pricing:  
AWS CloudHSM Classic pricing information is available on the [AWS CloudHSM Classic pricing page](https://aws.amazon.com/cloudhsm/pricing-classic/)\. 
AWS CloudHSM Classic upfront fee refund \(API and CLI Tools\):  
You are charged an upfront fee for each new AWS CloudHSM Classic instance that you create by using the [CreateHsm](https://docs.aws.amazon.com/cloudhsm/classic/APIReference/API_CreateHsm.html) API operation or the [create\-hsm](https://docs.aws.amazon.com/cli/latest/reference/cloudhsm/create-hsm.html) AWS CLI command\. If you accidentally provision an HSM instance that you don't need, first delete the HSM instance by using the [DeleteHsm](https://docs.aws.amazon.com/cloudhsm/classic/APIReference/API_DeleteHsm.html) API operation or the [delete\-hsm](https://docs.aws.amazon.com/cli/latest/reference/cloudhsm/delete-hsm.html) AWS CLI command\. You can then request a refund of the upfront fee at the [AWS Support Center](https://console.aws.amazon.com/support/home#/), by creating a new case and choosing **Account and Billing Support**\. 

The number of Oracle databases you can support on a single AWS CloudHSM Classic partition will depend on the rotation schedule you choose for your data\. You should rotate your keys as often as your data needs require\. The [ PCI\-DSS documentation](https://www.pcisecuritystandards.org/documents/PCI_DSS_v3-2.pdf) and the [National Institute of Standards and Technology \(NIST\)](http://csrc.nist.gov/publications/nistpubs/800-57/sp800-57_part1_rev3_general.pdf) provide guidance on appropriate key rotation frequency\. You can maintain approximately 10,000 symmetric master keys per AWS CloudHSM Classic device\. Note that after key rotation the old master key remains on the partition and is still counted against the per\-partition maximum\. 

AWS CloudHSM Classic works with Amazon Virtual Private Cloud \(Amazon VPC\)\. An appliance is provisioned inside your VPC with a private IP address that you specify, providing simple and private network connectivity to your Amazon RDS DB instance\. Your HSM appliances are dedicated exclusively to you and are isolated from other AWS customers\. For more information, see [Amazon Virtual Private Cloud \(VPCs\) and Amazon RDS](USER_VPC.md) and [Creating a DB Instance in a VPC](USER_VPC.WorkingWithRDSInstanceinaVPC.md#USER_VPC.InstanceInVPC)\. 

To use AWS CloudHSM Classic with an Amazon RDS Oracle DB instance, you must complete the following tasks, which are explained in detail in the following sections: 
+ [Setting Up AWS CloudHSM Classic to Work with Amazon RDS](Appendix.OracleCloudHSM.SetupCloudHSM.md)
+ [Setting Up Amazon RDS to Work with AWS CloudHSM Classic](Appendix.OracleCloudHSM.SetupRDS.md)

When you complete the entire setup, you should have the following AWS components\. 
+ An AWS CloudHSM Classic control instance that will communicate with the HSM appliance using port 22, and the AWS CloudHSM Classic endpoint\. The AWS CloudHSM Classic control instance is an Amazon EC2 instance that is in the same VPC as the HSMs and is used to manage the HSMs\. 
+ An Amazon RDS Oracle DB instance that will communicate with the Amazon RDS service endpoint, as well as the HSM appliance using port 1792\.

![\[AWS CloudHSM Classic-RDS network\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/hsm_rds_networking-beta.png)

**Topics**
+ [Setting Up AWS CloudHSM Classic to Work with Amazon RDS](Appendix.OracleCloudHSM.SetupCloudHSM.md)
+ [Setting Up Amazon RDS to Work with AWS CloudHSM Classic](Appendix.OracleCloudHSM.SetupRDS.md)
+ [Verifying the HSM Connection, the Oracle Keys in the HSM, and the TDE Key](Appendix.OracleCloudHSM.Verify.md)
+ [Restoring Encrypted DB Instances](Appendix.OracleCloudHSM.Restoring.md)
+ [Managing a Multi\-AZ Failover](Appendix.OracleCloudHSM.Multi-AZ.md)