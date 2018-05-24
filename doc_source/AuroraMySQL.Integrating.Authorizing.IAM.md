# Setting Up IAM Roles to Access AWS Services<a name="AuroraMySQL.Integrating.Authorizing.IAM"></a>

To permit your Aurora DB cluster to access another AWS service, do the following:

1. Create an IAM policy that grants permission to the AWS service\. For more information, see:
   + [Creating an IAM Policy to Access Amazon S3 Resources](AuroraMySQL.Integrating.Authorizing.IAM.S3CreatePolicy.md)
   + [Creating an IAM Policy to Access AWS Lambda Resources](AuroraMySQL.Integrating.Authorizing.IAM.LambdaCreatePolicy.md)
   + [Creating an IAM Policy to Access CloudWatch Logs Resources](AuroraMySQL.Integrating.Authorizing.IAM.CWCreatePolicy.md)
   + [Creating an IAM Policy to Access AWS KMS Resources](AuroraMySQL.Integrating.Authorizing.IAM.KMSCreatePolicy.md)

1. Create an IAM role and attach the policy that you created\. For more information, see [Creating an IAM Role to Allow Amazon Aurora to Access AWS Services](AuroraMySQL.Integrating.Authorizing.IAM.CreateRole.md)\.

1. Associate that IAM role with your Aurora DB cluster\. For more information, see [Associating an IAM Role with an Amazon Aurora MySQL DB Cluster](AuroraMySQL.Integrating.Authorizing.IAM.AddRoleToDBCluster.md)\.