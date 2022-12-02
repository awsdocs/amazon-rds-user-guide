# Viewing a blue/green deployment<a name="blue-green-deployments-viewing"></a>

You can view the details about a blue/green deployment using the AWS Management Console, the AWS CLI, or the RDS API\.

You can also view and subscribe to events for information about a blue/green deployment\. For more information, see [Blue/green deployment events](USER_Events.Messages.md#USER_Events.Messages.BlueGreenDeployments)\.

## Console<a name="blue-green-deployments-viewing-console"></a>

**To view the details about a blue/green deployment**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**, and then find the blue/green deployment in the list\.  
![\[Blue/green deployment in the database list\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/blue-green-deployment-view-db-list.png)

   The **Role** value for the blue/green deployment is **Blue/Green Deployment**\.

1. Choose the name of blue/green deployment that you want to view to display its details\.

   Each tab has a section for the blue deployment and a section for the green deployment\. The section for the blue deployment shows the details about DB instances in the blue environment\. The section for the green deployment shows the details about DB instances in the green environment\. You can examine the details in both environments to see differences between them\. For example, on the **Configuration** tab, the DB engine version might be different in the blue environment and in the green environment if you are upgrading the DB engine version in the green environment\. Make sure the values for the DB instances in both environments are the expected values\.

   The following image shows an example of the **Connectivity & security** tab\.  
![\[Blue/green deployment details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/blue-green-deployment-view-details.png)

   The following image shows an example of the **Configuration** tab\.  
![\[Blue/green deployment configuration details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/blue-green-deployment-view-config.png)

   The following image shows an example of the **Status** tab\.  
![\[Blue/green deployment status\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/blue-green-deployment-view-status.png)

## AWS CLI<a name="blue-green-deployments-viewing-cli"></a>

To view the details about a blue/green deployment by using the AWS CLI, use the [describe\-blue\-green\-deployments](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-blue-green-deployments.html) command\.

**Example View the details about a blue/green deployment by filtering on its name**  
When you use the [describe\-blue\-green\-deployments](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-blue-green-deployments.html) command, you can filter on the `--blue-green-deployment-name`\. The following example shows the details for a blue/green deployment named `my-blue-green-deployment`\.  

```
aws rds describe-blue-green-deployments --filters Name=blue-green-deployment-name,Values=my-blue-green-deployment
```

**Example View the details about a blue/green deployment by specifying its identifier**  
When you use the [describe\-blue\-green\-deployments](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-blue-green-deployments.html) command, you can specify the `--blue-green-deployment-identifier`\. The following example shows the details for a blue/green deployment with the identifier `bgd-1234567890abcdef`\.  

```
aws rds describe-blue-green-deployments --blue-green-deployment-identifier bgd-1234567890abcdef
```

## RDS API<a name="blue-green-deployments-viewing-api"></a>

To view the details about a blue/green deployment by using the Amazon RDS API, use the [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeBlueGreenDeployments.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeBlueGreenDeployments.html) operation and specify the `BlueGreenDeploymentIdentifier`\.