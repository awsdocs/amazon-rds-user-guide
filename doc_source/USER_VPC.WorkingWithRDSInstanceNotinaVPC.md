# Working with a DB instance that is not in a VPC<a name="USER_VPC.WorkingWithRDSInstanceNotinaVPC"></a>

The legacy *EC2\-Classic* platform is the original platform used by Amazon RDS\. If you are on the *EC2\-Classic* platform, your DB instances are not in a virtual private cloud \(VPC\)\.

**Important**  
If you are a new Amazon RDS customer, if you have never created a DB instance before, or if you are creating a DB instance in an AWS Region you have not used before, in almost all cases you are on the *EC2\-VPC* platform and have a default VPC\. Also, if your DB instance was created after 2013, it is probably in a VPC\. For information about working with DB instances in a VPC, see [Working with a DB instance in a VPC](USER_VPC.WorkingWithRDSInstanceinaVPC.md)\.

**Topics**
+ [Determining whether you are using the EC2\-VPC or EC2\-Classic platform](USER_VPC.FindDefaultVPC.md)
+ [Scenarios for accessing a DB instance not in a VPC](USER_VPC.Scenarios.NotInVPC.md)
+ [Moving a DB instance not in a VPC into a VPC](USER_VPC.Non-VPC2VPC.md)