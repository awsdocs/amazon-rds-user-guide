# Launch an EC2 instance<a name="CHAP_Tutorials.WebServerDB.LaunchEC2"></a>

Create an Amazon EC2 instance in the public subnet of your VPC\.

**To launch an EC2 instance**

1. Sign in to the AWS Management Console and open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the upper\-right corner of the AWS Management Console, choose the AWS Region where you want to create the EC2 instance\.

1. Choose **EC2 Dashboard**, and then choose **Launch instance**, as shown following\.  
![\[EC2 Dashboard\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/Tutorial_WebServer_11.png)

1. Make sure you have opted into the new launch experience\.

1. Under **Name and tags**, for **Name**, enter **tutorial\-ec2\-instance\-web\-server**\.

1. Under **Application and OS Images \(Amazon Machine Image\)**, choose **Amazon Linux**, and then choose the **Amazon Linux 2 AMI**\. Keep the defaults for the other choices\.  
![\[Choose an Amazon Machine Image\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/Tutorial_WebServer_12.png)

1. Under **Instance type**, choose **t2\.micro**\.

1. Under **Key pair \(login\)**, choose a **Key pair name** to use an existing key pair\. To create a new key pair for the Amazon EC2 instance, choose **Create new key pair** and then use the **Create key pair** window to create it\.

   For more information about creating a new key pair, see [Create a key pair](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/get-set-up-for-amazon-ec2.html#create-a-key-pair) in the *Amazon EC2 User Guide for Linux Instances*\.

1. Under **Network settings**, set these values and keep the other values as their defaults:
   + For **Allow SSH traffic from**, choose the source of SSH connections to the EC2 instance\.

     You can choose **My IP** if the displayed IP address is correct for SSH connections\.

     Otherwise, you can determine the IP address to use to connect to EC2 instances in your VPC using Secure Shell \(SSH\)\. To determine your public IP address, in a different browser window or tab, you can use the service at [https://checkip\.amazonaws\.com](https://checkip.amazonaws.com)\. An example of an IP address is `203.0.113.25/32`\.

     In many cases, you might connect through an internet service provider \(ISP\) or from behind your firewall without a static IP address\. If so, make sure to determine the range of IP addresses used by client computers\.
**Warning**  
If you use `0.0.0.0/0` for SSH access, you make it possible for all IP addresses to access your public instances using SSH\. This approach is acceptable for a short time in a test environment, but it's unsafe for production environments\. In production, authorize only a specific IP address or range of addresses to access your instances using SSH\.
   + Turn on **Allow HTTPs traffic from the internet**\.
   + Turn on **Allow HTTP traffic from the internet**\.  
![\[Configure Instance Details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/Tutorial_WebServer_14.png)

1. Leave the default values for the remaining sections\.

1. Review a summary of your instance configuration in the **Summary** panel, and when you're ready, choose **Launch instance**\.

1. On the **Launch Status** page, shown following, note the identifier for your new EC2 instance, for example: `i-03a6ad47e97ba9dc5`\.  
![\[Launch Status\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/Tutorial_WebServer_19.png)

1. Choose **View all instances** to find your instance\. 

1. Wait until **Instance state** for your instance is **Running** before continuing\.

1. Complete [Create a DB instance](CHAP_Tutorials.WebServerDB.CreateDBInstance.md)\.