# Creating an IAM Policy to Access Amazon S3 Resources<a name="AuroraMySQL.Integrating.Authorizing.IAM.S3CreatePolicy"></a>

Aurora can access Amazon S3 resources to either load data to or save data from an Aurora DB cluster\. However, you must first create an IAM policy that provides the bucket and object permissions that allow Aurora to access Amazon S3\.

The following table lists the Aurora features that can access an Amazon S3 bucket on your behalf, and the minimum required bucket and object permissions required by each feature\.


| Feature | Bucket Permissions | Object Permissions | 
| --- | --- | --- | 
|  `LOAD DATA FROM S3`  |  `ListBucket`  |  `GetObject` `GetObjectVersion`  | 
| LOAD XML FROM S3 |  `ListBucket`  |  `GetObject` `GetObjectVersion`  | 
|  `SELECT INTO OUTFILE S3`  |  `ListBucket`  |  `AbortMultipartUpload` `DeleteObject` `GetObject` `ListMultipartUploadParts` `PutObject`  | 

You can use the following steps to create an IAM policy that provides the minimum required permissions for Aurora to access an Amazon S3 bucket on your behalf\. To allow Aurora to access all of your Amazon S3 buckets, you can skip these steps and use either the `AmazonS3ReadOnlyAccess` or `AmazonS3FullAccess` predefined IAM policy instead of creating your own\.

**To create an IAM policy to grant access to your Amazon S3 resources**

1. Open the [IAM Management Console](https://console.aws.amazon.com/iam/home?#home)\.

1. In the navigation pane, choose **Policies**\.

1. Choose **Create policy**\.

1. On the **Visual editor** tab, choose **Choose a service**, and then choose **S3**\.

1. Choose **Select actions** and then choose the bucket permissions and object permissions needed for the IAM policy\.

   Object permissions are permissions for object operations in Amazon S3, and need to be granted for objects in a bucket, not the bucket itself\. For more information about permissions for object operations in Amazon S3, see [Permissions for Object Operations](http://docs.aws.amazon.com/AmazonS3/latest/dev/using-with-s3-actions.html#using-with-s3-actions-related-to-objects)\.

1. Choose **Resources** and choose **Add ARN** for **bucket**\.

1. In the **Add ARN\(s\)** dialog box, provide the details about your resource, and choose **Add**\.

   Specify the Amazon S3 bucket to allow access to\. For instance, if you want to allow Aurora to access the Amazon S3 bucket named `example-bucket`, then set the ARN value to `arn:aws:s3:::example-bucket`\.

1. If the **object** resource is listed, choose **Add ARN** for **object**\.

1. In the **Add ARN\(s\)** dialog box, provide the details about your resource\.

   For the Amazon S3 bucket, specify the Amazon S3 bucket to allow access to\. For the object, you can choose **Any** to grant permissions to any object in the bucket\.
**Note**  
You can set **Amazon Resource Name \(ARN\)** to a more specific ARN value in order to allow Aurora to access only specific files or folders in an Amazon S3 bucket\. For more information about how to define an access policy for Amazon S3, see [Managing Access Permissions to Your Amazon S3 Resources](http://docs.aws.amazon.com/AmazonS3/latest/dev/s3-access-control.html)\.

1. Optionally, choose **Add additional permissions** to add another Amazon S3 bucket to the policy, and repeat the previous steps for the bucket\.
**Note**  
You can repeat this to add corresponding bucket permission statements to your policy for each Amazon S3 bucket that you want Aurora to access\. Optionally, you can also grant access to all buckets and objects in Amazon S3\.

1. Choose **Review policy**\.

1. Set **Name** to a name for your IAM policy, for example `AllowAuroraToExampleBucket`\. You use this name when you create an IAM role to associate with your Aurora DB cluster\. You can also add an optional **Description** value\.

1. Choose **Create policy**\.

1. Complete the steps in [Creating an IAM Role to Allow Amazon Aurora to Access AWS Services](AuroraMySQL.Integrating.Authorizing.IAM.CreateRole.md)\.