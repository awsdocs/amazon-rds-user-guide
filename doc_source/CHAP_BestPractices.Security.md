# Security Best Practices for Amazon RDS<a name="CHAP_BestPractices.Security"></a>

Use AWS Identity and Access Management \(IAM\) accounts to control access to Amazon RDS API operations, especially operations that create, modify, or delete Amazon RDS resources\. Such resources include DB instances, security groups, and parameter groups\. Also use IAM to control actions that perform common administrative actions such as backing up and restoring DB instances\.
+ Create an individual IAM user for each person who manages Amazon RDS resources, including yourself\. Don't use AWS root credentials to manage Amazon RDS resources\.
+ Grant each user the minimum set of permissions required to perform his or her duties\.
+ Use IAM groups to effectively manage permissions for multiple users\.
+ Rotate your IAM credentials regularly\.
+ Configure AWS Secrets Manager to automatically rotate the secrets for Amazon RDS\. For more information, see [Rotating Your AWS Secrets Manager Secrets](https://docs.aws.amazon.com/secretsmanager/latest/userguide/rotating-secrets.html) in the *AWS Secrets Manager User Guide*\. You can also retrieve the credential from AWS Secrets Manager programmatically\. For more information, see [Retrieving the Secret Value](https://docs.aws.amazon.com/secretsmanager/latest/userguide/manage_retrieve-secret.html) in the *AWS Secrets Manager User Guide*\.

For more information about IAM, see [AWS Identity and Access Management](https://docs.aws.amazon.com/IAM/latest/UserGuide/Welcome.html)\. For information on IAM best practices, see [IAM Best Practices](https://docs.aws.amazon.com/IAM/latest/UserGuide/IAMBestPractices.html)\. 

Use the AWS Management Console, the AWS CLI, or the RDS API to change the password for your master user\. If you use another tool, such as a SQL client, to change the master user password, it might result in privileges being revoked for the user unintentionally\.