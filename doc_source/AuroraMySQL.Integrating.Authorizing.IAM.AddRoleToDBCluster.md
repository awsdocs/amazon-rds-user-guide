# Associating an IAM Role with an Amazon Aurora MySQL DB Cluster<a name="AuroraMySQL.Integrating.Authorizing.IAM.AddRoleToDBCluster"></a>

To permit database users in an Amazon Aurora DB cluster to access other AWS services, you associate the role that you created in [Creating an IAM Role to Allow Amazon Aurora to Access AWS Services](AuroraMySQL.Integrating.Authorizing.IAM.CreateRole.md) with that DB cluster\.

To associate an IAM role with a DB cluster you do two things:

+ Add the role to the list of associated roles for a DB cluster by using the RDS console, the [add\-role\-to\-db\-cluster](http://docs.aws.amazon.com/cli/latest/reference/rds/add-role-to-db-cluster.html) AWS CLI command, or the [AddRoleToDBCluster](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/AddRoleToDBCluster.html) RDS API action\.

  You can add a maximum of five IAM roles for each Aurora DB cluster\.

+ Set the cluster\-level parameter for the related AWS service to the ARN for the associated IAM role\.

  The following table describes the cluster\-level parameter names for the IAM roles used to access other AWS services\.     
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/AuroraMySQL.Integrating.Authorizing.IAM.AddRoleToDBCluster.html)

To associate an IAM role to permit your Amazon RDS cluster to communicate with other AWS services on your behalf, take the following steps\.

**To associate an IAM role with an Aurora DB cluster using the console**

1. Open the RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. Choose **Clusters**\.

1. Choose the Aurora DB cluster that you want to associate an IAM role with, and then choose **Manage IAM roles** in **Cluster actions**\.

1. In **Manage IAM roles**, choose the role to associate with your DB cluster from **Add IAM roles to this cluster**\.  
![\[Associate an IAM role with a DB cluster\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/AuroraAssociateIAMRole-02.png)

1. Choose **Add role**\.

1. \(Optional\) To stop associating an IAM role with a DB cluster and remove the related permission, choose **Delete** for the role\.

1. Choose **Done**\.

1. In the RDS console, choose **Parameter groups** in the navigation pane\.

1. If you are already using a custom DB parameter group, you can select that group to use instead of creating a new DB cluster parameter group\. If you are using the default DB cluster parameter group, create a new DB cluster parameter group, as described in the following steps: 

   1. Choose **Create Parameter Group**\.

   1. For **Parameter group family**, choose `aurora5.6` for an Aurora MySQL 5\.6\-compatible DB cluster, or choose `aurora5.7` for an Aurora MySQL 5\.7\-compatible DB cluster\.

   1. For **Type**, choose **DB Cluster Parameter Group**\. 

   1. For **Group name**, type the name of your new DB cluster parameter group\.

   1. For **Description**, type a description for your new DB cluster parameter group\.  
![\[Create a DB cluster parameter group\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/AuroraAssociateIAMRole-03.png)

   1. Choose **Create**\. 

1. On the **Parameter groups** page, select your DB cluster parameter group and choose **Edit** from **Parameter group actions**\.

1. Choose **Edit parameters**\.

1. Set the appropriate cluster\-level parameters to the related IAM role ARN values\. For example, you can set just the `aws_default_s3_role` parameter to `arn:aws:iam::123456789012:role/AllowAuroraS3Role`\.

1. Choose **Save changes**\.

1. Choose **Instances**, and then select the primary instance for your Aurora DB cluster\.

1. Choose **Instance actions** and then choose **Modify**\.

1. Scroll to **Database options** and set the **DB cluster parameter group** to the new DB cluster parameter group that you created\. Choose **Continue**\.

1. Verify your changes and then choose **Apply immediately**\.

1. Choose **Modify DB Instance**\.

1. The primary instance for your DB cluster is still selected in the list of instances\. Choose **Instance Actions**, and then choose **Reboot**\.

   When the instance has rebooted, your IAM roles is associated with your DB cluster\.

   For more information about cluster parameter groups, see [Amazon Aurora MySQL Parameters](AuroraMySQL.Reference.md#AuroraMySQL.Reference.ParameterGroups)\.

**To associate an IAM role with a DB cluster by using the AWS CLI**

1. Call the `add-role-to-db-cluster` command from the AWS CLI to add the ARNs for your IAM roles to the DB cluster, as shown following\. 

   ```
   PROMPT> aws rds add-role-to-db-cluster --db-cluster-identifier my-cluster --role-arn arn:aws:iam::123456789012:role/AllowAuroraS3Role
   PROMPT> aws rds add-role-to-db-cluster --db-cluster-identifier my-cluster --role-arn arn:aws:iam::123456789012:role/AllowAuroraLambdaRole
   ```

1. If you are using the default DB cluster parameter group, create a new DB cluster parameter group\. If you are already using a custom DB parameter group, you can use that group instead of creating a new DB cluster parameter group\.

   To create a new DB cluster parameter group, call the `create-db-cluster-parameter-group` command from the AWS CLI, as shown following\.

   ```
   PROMPT> aws rds create-db-cluster-parameter-group  --db-cluster-parameter-group-name AllowAWSAccess \
        --db-parameter-group-family aurora5.6 --description "Allow access to Amazon S3 and AWS Lambda"
   ```

   For an Aurora MySQL 5\.7\-compatible DB cluster, specify `aurora5.7` for `--db-parameter-group-family`\.

1. Set the appropriate cluster\-level parameter or parameters and the related IAM role ARN values in your DB cluster parameter group, as shown following\. 

   ```
   PROMPT> aws rds modify-db-cluster-parameter-group --db-cluster-parameter-group-name AllowAWSAccess \
       --parameters "name=aws_default_s3_role,value=arn:aws:iam::123456789012:role/AllowAuroraS3Role,method=pending-reboot" \
       --parameters "name=aws_default_lambda_role,value=arn:aws:iam::123456789012:role/AllowAuroraLambdaRole,method=pending-reboot"
   ```

1. Modify the DB cluster to use the new DB cluster parameter group and then reboot the cluster, as shown following\.

   ```
   PROMPT> aws rds modify-db-cluster --db-cluster-identifier my-cluster --db-cluster-parameter-group-name AllowAWSAccess
   PROMPT> aws rds reboot-db-instance --db-instance-identifier my-cluster-primary
   ```

   When the instance has rebooted, your IAM roles are associated with your DB cluster\.

   For more information about cluster parameter groups, see [Amazon Aurora MySQL Parameters](AuroraMySQL.Reference.md#AuroraMySQL.Reference.ParameterGroups)\.