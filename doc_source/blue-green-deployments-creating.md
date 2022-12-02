# Creating a blue/green deployment<a name="blue-green-deployments-creating"></a>

When you create a blue/green deployment, you specify the DB instance to copy in the deployment\. The DB instance you choose is the production DB instance, and it becomes the primary DB instance in the blue environment\. This DB instance is copied to the green environment, and RDS configures replication from the DB instance in the blue environment to the DB instance in the green environment\. 

RDS copies the blue environment's topology to a staging area, along with its configured features\. When the blue DB instance has read replicas, the read replicas are copied as read replicas of the green DB instance in the deployment\. If the blue DB instance is a Multi\-AZ DB instance deployment, then the green DB instance is created as a Multi\-AZ DB instance deployment\.

**Topics**
+ [Making changes in the green environment](#blue-green-deployments-creating-changes)
+ [Handling lazy loading when you create a blue/green deployment](#blue-green-deployments-creating-lazy-loading)
+ [Creating the blue/green deployment](#blue-green-deployments-creating-create)

## Making changes in the green environment<a name="blue-green-deployments-creating-changes"></a>

You can make the following changes to the DB instance in the green environment when you create the blue/green deployment:
+ You can specify a higher engine version if you want to test a DB engine upgrade\.
+ You can specify a DB parameter group that is different from the one used by the DB instance in the blue environment\. You can test how parameter changes affect the DB instances in the green environment or specify a parameter group for a new major DB engine version in the case of an upgrade\.

  If you specify a different DB parameter group, the specified DB parameter group is associated with all of the DB instances in the green environment\. If you don't specify a different parameter group, each DB instance in the green environment is associated with the parameter group of its corresponding blue DB instance\.

You can make other modifications to the DB instance in the green environment after it is deployed\. For example, you might make schema changes to your database or change the DB instance class used by one or more DB instances in the green environment\.

For information about modifying a DB instance, see [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)\.

## Handling lazy loading when you create a blue/green deployment<a name="blue-green-deployments-creating-lazy-loading"></a>

When you create a blue/green deployment, Amazon RDS creates the primary DB instance in the green environment by restoring from a DB snapshot\. After it is created, the green DB instance continues to load data in the background, which is known as *lazy loading*\. If the DB instance has read replicas, these are also created from DB snapshots and are subject to lazy loading\.

If you access data that hasn't been loaded yet, the DB instance immediately downloads the requested data from Amazon S3, and then continues loading the rest of the data in the background\. For more information, see [Amazon EBS snapshots](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EBSSnapshots.html)\.

To help mitigate the effects of lazy loading on tables to which you require quick access, you can perform operations that involve full\-table scans, such as `SELECT *`\. This operation allows Amazon RDS to download all of the backed\-up table data from S3\.

If an application attempts to access data that isn't loaded, the application can encounter higher latency than normal while the data is loaded\. This higher latency due to lazy loading could lead to poor performance for latency\-sensitive workloads\.

**Important**  
If you switch over a blue/green deployment before data loading is complete, your application could experience performance issues due to high latency\.

## Creating the blue/green deployment<a name="blue-green-deployments-creating-create"></a>

You can create the blue/green deployment using the AWS Management Console, the AWS CLI, or the RDS API\.

### Console<a name="blue-green-deployments-creating-console"></a>

**To create a blue/green deployment;**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**, and then choose the DB instance that you want to copy to a green environment\.

1. For **Actions**, choose **Create Blue/Green Deployment**\.

   The **Create Blue/Green Deployment** page appears\.   
![\[Create blue/green deployment\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/blue-green-deployment-create.png)

1. On the **Create Blue/Green Deployment** page, review the blue database identifiers\. Make sure they match the DB instances that you expect in the blue environment\. If they don't, choose **Cancel**\.

1. For **Blue/Green Deployment identifier**, enter a name for your blue/green deployment\.

1. \(Optional\) For **Blue/Green Deployment settings**, specify the settings for the green environment:
   + Choose a DB engine version if you want to test a DB engine version upgrade\.
   + Choose a DB parameter group to associate with the DB instances in the green environment\.

   You can make other modifications to the databases in the green environment after it is deployed\.

1. Choose **Create Blue/Green Deployment**\.

### AWS CLI<a name="blue-green-deployments-creating-cli"></a>

To create a blue/green deployment by using the AWS CLI, use the [create\-blue\-green\-deployment](https://docs.aws.amazon.com/cli/latest/reference/rds/create-blue-green-deployment.html) command with the following options:
+ `--blue-green-deployment-name` – Specify the name of the blue/green deployment\.
+ `--source` – Specify the ARN of the DB instance that you want to copy\.
+ `--target-engine-version` – Specify an engine version if you want to test a DB engine version upgrade in the green environment\. This option upgrades the DB instances in the green environment to the specified DB engine version\.

  If not specified, each DB instance in the green environment is created with the same engine version as the corresponding DB instance in the blue environment\.
+ `--target-db-parameter-group-name` – Specify a DB parameter group to associate with the DB instances in the green environment\.

**Example Create a blue/green deployment**  
For Linux, macOS, or Unix:  

```
aws rds create-blue-green-deployment \
    --blue-green-deployment-name my-blue-green-deployment \
    --source arn:aws:rds:us-east-2:123456789012:db:mydb1 \
    --target-engine-version 8.0.31 \
    --target-db-parameter-group-name mydbparametergroup
```
For Windows:  

```
aws rds create-blue-green-deployment ^
    --blue-green-deployment-name my-blue-green-deployment ^
    --source arn:aws:rds:us-east-2:123456789012:db:mydb1 ^
    --target-engine-version 8.0.31 ^
    --target-db-parameter-group-name mydbparametergroup
```

### RDS API<a name="blue-green-deployments-creating-api"></a>

To create a blue/green deployment by using the Amazon RDS API, use the [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateBlueGreenDeployment.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateBlueGreenDeployment.html) operation with the following parameters:
+ `BlueGreenDeploymentName` – Specify the name of the blue/green deployment\.
+ `Source` – Specify the ARN of the DB instance that you want to copy to the green environment\.
+ `TargetEngineVersion` – Specify an engine version if you want to test a DB engine version upgrade in the green environment\. This option upgrades the DB instances in the green environment to the specified DB engine version\.

  If not specified, each DB instance in the green environment is created with the same engine version as the corresponding DB instance in the blue environment\.
+ `TargetDBParameterGroupName` – Specify a DB parameter group to associate with the DB instances in the green environment\.