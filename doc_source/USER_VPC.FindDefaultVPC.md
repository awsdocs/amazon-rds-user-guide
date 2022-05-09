# Determining whether you are using the EC2\-VPC or EC2\-Classic platform<a name="USER_VPC.FindDefaultVPC"></a>

Your AWS account and the AWS Region you choose determines which of the two RDS platforms your DB instance is created on: *EC2\-VPC* or *EC2\-Classic*\. The type of platform determines if you have a default VPC, and which type of security group you use to provide access to your DB instance\.

The legacy *EC2\-Classic* platform is the original platform used by Amazon RDS\. If you are on this platform and want to use a VPC, you must create the VPC using the Amazon VPC console or Amazon VPC API\. Accounts that only support the *EC2\-VPC* platform have a default VPC where all DB instances are created, and you must use either an EC2 or VPC security group to provide access to the DB instance\.

**Important**  
If you are a new Amazon RDS customer, if you have never created a DB instance before, or if you are creating a DB instance in an AWS Region you have not used before, in almost all cases you are on the *EC2\-VPC* platform and have a default VPC\. Also, if your DB instance was created after 2013, it is probably in a VPC\. For information about working with DB instances in a VPC, see [Working with a DB instance in a VPC](USER_VPC.WorkingWithRDSInstanceinaVPC.md)\.

You can tell which platform your AWS account in a given AWS Region is using by looking at the dashboard on the [RDS console](https://console.aws.amazon.com/rds/) or [EC2 console](https://console.aws.amazon.com/ec2/)\. If you are a new Amazon RDS customer, if you have never created a DB instance before, or if you are creating a DB instance in an AWS Region you have not used before, you might be redirected to the first\-run console page and not see the home page following\.

## EC2\-VPC platform in the console<a name="USER_VPC.FindDefaultVPC.EC2-VPC-Platform"></a>

If **Supported platforms** indicates *only* `VPC`, as shown following in the RDS console, your AWS account in the current AWS Region uses the *EC2\-VPC* platform, and uses a default VPC\.

![\[EC2-VPC platform\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/RDS-GSG-VPC.png)

Similarly, if **Supported platforms** indicates *only* `VPC`, as shown following in the EC2 console, your AWS account in the current AWS Region uses the *EC2\-VPC* platform, and uses a default VPC\.

![\[EC2-VPC platform\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/EC2-GSG-VPC.png)

In both the RDS and EC2 console, the name of the default VPC is shown below the supported platform\. To provide access to a DB instance created on the *EC2\-VPC* platform, you must create a VPC security group\. For information about creating a VPC security group, see [Tutorial: Create an Amazon VPC for use with a DB instance \(IPv4 only\)](CHAP_Tutorials.WebServerDB.CreateVPC.md)\.

## EC2\-Classic platform in the console<a name="USER_VPC.FindDefaultVPC.EC2-Classic-Platform"></a>

In both the RDS and EC2 console, if **Supported platforms** indicates *both* `EC2` and `VPC`, your AWS account in the current AWS Region uses the *EC2\-Classic* platform, and you do not have a default VPC\. To provide access to a DB instance created on the* EC2\-Classic* platform, you must create a DB security group\. For information about creating a DB security group, see [Creating a DB security group](USER_WorkingWithSecurityGroups.md#USER_WorkingWithSecurityGroups.Creating)\.

**Note**  
You can create a VPC on the *EC2\-Classic* platform, but one is not created for you by default as it is on accounts that support the *EC2\-VPC* platform\. 
If you want to move an existing DB instance into a VPC, you can use the AWS Management Console to do it easily\. For more information\. see [Moving a DB instance not in a VPC into a VPC](USER_VPC.Non-VPC2VPC.md)\. 