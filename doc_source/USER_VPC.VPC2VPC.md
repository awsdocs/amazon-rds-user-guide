# Updating the VPC for a DB instance<a name="USER_VPC.VPC2VPC"></a>

You can use the AWS Management Console to move your DB instance to a different VPC\. 

For information about modifying a DB instance, see [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)\. In the **Network & Security** section of the modify page, shown following, enter the new subnet group for **Subnet group**\. The new subnet group must be a subnet group in a new VPC\.  

![\[Modify DB Instance panel Subnet Group section\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/EC2-VPC.png)