# Integrating Amazon Aurora MySQL with Other AWS Services<a name="AuroraMySQL.Integrating"></a>

Amazon Aurora MySQL integrates with other AWS services so that you can extend your Aurora MySQL DB cluster to use additional capabilities in the AWS Cloud\. Your Aurora MySQL DB cluster can use AWS services to do the following:

+ Synchronously or asynchronously invoke an AWS Lambda function using the native functions `lambda_sync` or `lambda_async`\. For more information, see [Invoking a Lambda Function with an Aurora MySQL Native Function](AuroraMySQL.Integrating.Lambda.md#AuroraMySQL.Integrating.NativeLambda)\.

+ Load data from text or XML files stored in an Amazon Simple Storage Service \(Amazon S3\) bucket into your DB cluster using the `LOAD DATA FROM S3` or `LOAD XML FROM S3` command\. For more information, see [Loading Data into an Amazon Aurora MySQL DB Cluster from Text Files in an Amazon S3 Bucket](AuroraMySQL.Integrating.LoadFromS3.md)\.

+ Save data to text files stored in an Amazon S3 bucket from your DB cluster using the `SELECT INTO OUTFILE S3` command\. For more information, see [Saving Data from an Amazon Aurora MySQL DB Cluster into Text Files in an Amazon S3 Bucket](AuroraMySQL.Integrating.SaveIntoS3.md)\. 

+ Automatically add or remove Aurora Replicas with Application Auto Scaling\. For more information, see [Using Amazon Aurora Auto Scaling with Aurora Replicas](Aurora.Integrating.AutoScaling.md)\.

Aurora secures the ability to access other AWS services by using AWS Identity and Access Management \(IAM\)\. You grant permission to access other AWS services by creating an IAM role with the necessary permissions, and then associating the role with your DB cluster\. For details and instructions on how to permit your Aurora MySQL DB cluster to access other AWS services on your behalf, see [Authorizing Amazon Aurora MySQL to Access Other AWS Services on Your Behalf](AuroraMySQL.Integrating.Authorizing.md)\.

## Related Topics<a name="AuroraMySQL.Integrating.RelatedTopics"></a>

+ [Amazon Aurora on Amazon RDS](CHAP_Aurora.md)