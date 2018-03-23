# Authorizing Amazon Aurora MySQL to Access Other AWS Services on Your Behalf<a name="AuroraMySQL.Integrating.Authorizing"></a>

**Note**  
Integration with other AWS services is available for Amazon Aurora MySQL version 1\.8 and later\. Some integration features are only available for later versions of Aurora MySQL\. For more information on Aurora versions, see [Amazon Aurora MySQL Database Engine Updates](AuroraMySQL.Updates.md)\.

For your Aurora MySQL DB cluster to access other services on your behalf, create and configure an AWS Identity and Access Management \(IAM\) role\. This role authorizes database users in your DB cluster to access other AWS services\. For more information, see [Setting Up IAM Roles to Access AWS Services](AuroraMySQL.Integrating.Authorizing.IAM.md)\.

You must also configure your Aurora DB cluster to allow outbound connections to the target AWS service\. For more information, see [Enabling Network Communication from Amazon Aurora MySQL to Other AWS Services](AuroraMySQL.Integrating.Authorizing.Network.md)\.

If you do so, your database users can perform these actions using other AWS services:
+ Synchronously or asynchronously invoke an AWS Lambda function using the native functions `lambda_sync` or `lambda_async`\. Or, asynchronously invoke an AWS Lambda function using the `mysql.lambda_async` procedure\. For more information, see [Invoking a Lambda Function with an Aurora MySQL Native Function](AuroraMySQL.Integrating.Lambda.md#AuroraMySQL.Integrating.NativeLambda)\.
+ Load data from text or XML files stored in an Amazon S3 bucket into your DB cluster by using the `LOAD DATA FROM S3` or `LOAD XML FROM S3` statement\. For more information, see [Loading Data into an Amazon Aurora MySQL DB Cluster from Text Files in an Amazon S3 Bucket](AuroraMySQL.Integrating.LoadFromS3.md)\.
+ Save data from your DB cluster into text files stored in an Amazon S3 bucket by using the `SELECT INTO OUTFILE S3` statement\. For more information, see [Saving Data from an Amazon Aurora MySQL DB Cluster into Text Files in an Amazon S3 Bucket](AuroraMySQL.Integrating.SaveIntoS3.md)\.
+ Export audit log data to Amazon CloudWatch Logs MySQL\. For more information, see [Publishing Audit Log Data From Amazon Aurora to Amazon CloudWatch Logs](AuroraMySQL.Integrating.CloudWatch.md)\.
+ Automatically add or remove Aurora Replicas with Application Auto Scaling\. For more information, see [Using Amazon Aurora Auto Scaling with Aurora Replicas](Aurora.Integrating.AutoScaling.md)\.

## Related Topics<a name="AuroraMySQL.Integrating.Authorizing.RelatedTopics"></a>
+ [Integrating Aurora with Other AWS Services](Aurora.Integrating.md)
+ [Amazon Aurora on Amazon RDS](CHAP_Aurora.md)