# Using Amazon Aurora Auto Scaling with Aurora Replicas<a name="Aurora.Integrating.AutoScaling"></a>

Amazon Aurora with MySQL compatibility supports Aurora Auto Scaling\. To meet your connectivity and workload requirements, Aurora Auto Scaling dynamically adjusts the number of Aurora Replicas provisioned for an Aurora DB cluster\. Aurora Auto Scaling enables your Aurora DB cluster to handle sudden increases in connectivity or workload\. When the connectivity or workload decreases, Aurora Auto Scaling removes unnecessary Aurora Replicas so that you don't pay for unused provisioned DB instances\.

You define and apply a scaling policy to an Aurora DB cluster\. The scaling policy defines the minimum and maximum number of Aurora Replicas that Aurora Auto Scaling can manage\. Based on the policy, Aurora Auto Scaling adjusts the number of Aurora Replicas up or down in response to actual workloads, determined by using Amazon CloudWatch metrics and target values\.

You can use the AWS Management Console to apply a scaling policy based on a predefined metric\. Alternatively, you can use either the AWS CLI or Aurora Auto Scaling API to apply a scaling policy based on a predefined or custom metric\.


+ [Aurora Auto Scaling Policies](#Aurora.Integrating.AutoScaling.Concepts)
+ [Before You Begin](#Aurora.Integrating.AutoScaling.BYB)
+ [Adding a Scaling Policy](#Aurora.Integrating.AutoScaling.Add)
+ [Editing a Scaling Policy](#Aurora.Integrating.AutoScaling.Edit)
+ [Deleting a Scaling Policy](#Aurora.Integrating.AutoScaling.Delete)
+ [Related Topics](#Aurora.Integrating.AutoScaling.RelatedItems)

## Aurora Auto Scaling Policies<a name="Aurora.Integrating.AutoScaling.Concepts"></a>

Aurora Auto Scaling uses a scaling policy to adjust the number of Aurora Replicas in an Aurora DB cluster\. Aurora Auto Scaling has the following components:

+ A service\-linked role

+ A target metric

+ Minimum and maximum capacity

+ A cooldown period

### Service Linked Role<a name="Aurora.Integrating.AutoScaling.Concepts.SLR"></a>

Aurora Auto Scaling uses the `AWSServiceRoleForApplicationAutoScaling_RDSCluster` service\-linked role\. For more information, see [Service\-Linked Roles for Application Auto Scaling](http://docs.aws.amazon.com/ApplicationAutoScaling/latest/APIReference/application-autoscaling-service-linked-roles.html) in the *Application Auto Scaling API Reference*\.

### Target Metric<a name="Aurora.Integrating.AutoScaling.Concepts.TargetMetric"></a>

Aurora MySQL supports target\-tracking scaling policies\. In this type of policy, a predefined or custom metric and a target value for the metric is specified in a target\-tracking scaling policy configuration\. Aurora Auto Scaling creates and manages CloudWatch alarms that trigger the scaling policy and calculates the scaling adjustment based on the metric and target value\. The scaling policy adds or removes Aurora Replicas as required to keep the metric at, or close to, the specified target value\. In addition to keeping the metric close to the target value, a target\-tracking scaling policy also adjusts to fluctuations in the metric due to a changing workload\. Such a policy also minimizes rapid fluctuations in the number of available Aurora Replicas for your DB cluster\.

For example, take a scaling policy that uses the predefined average CPU utilization metric\. Such a policy can keep CPU utilization at, or close to, a specified percentage of utilization, such as 40 percent\.

Aurora Auto Scaling only scales a DB cluster if all Aurora Replicas in a DB cluster are in the available state\. If any of the Aurora Replicas are in a state other than available, Aurora Auto Scaling waits until the whole DB cluster becomes available for scaling\. Also, Aurora Auto Scaling only removes Aurora Replicas that it created\.

**Note**  
 For each Aurora DB cluster, you can create only one Auto Scaling policy for each target metric\. 

### Minimum and Maximum Capacity<a name="Aurora.Integrating.AutoScaling.Concepts.Capacity"></a>

You can specify the maximum number of Aurora Replicas to be managed by Application Auto Scaling\. This value must be set to 0–15, and must be equal to or greater than the value specified for the minimum number of Aurora Replicas\.

You can also specify the minimum number of Aurora Replicas to be managed by Application Auto Scaling\. This value must be set to 0–15, and must be equal to or less than the value specified for the maximum number of Aurora Replicas\.

**Note**  
The minimum and maximum capacity are set for an Aurora DB cluster\. The specified values apply to all of the policies associated with that Aurora DB cluster\. 

### Cooldown Period<a name="Aurora.Integrating.AutoScaling.Concepts.Cooldown"></a>

You can tune the responsiveness of a target\-tracking scaling policy by adding cooldown periods that affect scaling your Aurora DB cluster in and out\. A cooldown period blocks subsequent scale\-in or scale\-out requests until the period expires\. These blocks slow the deletions of Aurora Replicas in your Aurora DB cluster for scale\-in requests, and the creation of Aurora Replicas for scale\-out requests\.

You can specify the following cooldown periods:

+ A scale\-in activity reduces the number of Aurora Replicas in your Aurora DB cluster\. A scale\-in cooldown period specifies the amount of time, in seconds, after a scale\-in activity completes before another scale\-in activity can start\. 

+ A scale\-out activity increases the number of Aurora Replicas in your Aurora DB cluster\. A scale\-out cooldown period specifies the amount of time, in seconds, after a scale\-out activity completes before another scale\-out activity can start\. 

When a scale\-in or a scale\-out cooldown period is not specified, the default for each is 300 seconds\.

### Enable or Disable Scale\-In Activities<a name="Aurora.Integrating.AutoScaling.Concepts.ScaleIn"></a>

You can enable or disable scale\-in activities for a policy\. Enabling scale\-in activities allows the scaling policy to delete Aurora Replicas\. When scale\-in activities are enabled, the scale\-in cooldown period in the scaling policy applies to scale\-in activities\. Disabling scale\-in activities prevents the scaling policy from deleting Aurora Replicas\.

**Note**  
Scale\-out activities are always enabled so that the scaling policy can create Aurora Replicas as needed\.

## Before You Begin<a name="Aurora.Integrating.AutoScaling.BYB"></a>

Before you can use Aurora Auto Scaling with an Aurora DB cluster, you must first create an Aurora DB cluster with a primary instance and at least one Aurora Replica\. Although Aurora Auto Scaling manages Aurora Replicas, the Aurora DB cluster must start with at least one Aurora Replica\. For more information about creating an Aurora DB cluster, see [Creating an Amazon Aurora DB Cluster](Aurora.CreateInstance.md)\.

When Aurora Auto Scaling adds a new Aurora Replica, the new Aurora Replica is the same DB instance class as the one used by the primary instance\. For more information about DB instance classes, see [DB Instance Class](Concepts.DBInstanceClass.md)\.

To benefit from Aurora Auto Scaling, your applications must support connections to new Aurora Replicas\. To do so, we recommend using the Aurora reader endpoint or a driver such as the MariaDB Connector/J utility\. For more information, see [Connecting to an Amazon Aurora DB Cluster](Aurora.Connecting.md)\.

## Adding a Scaling Policy<a name="Aurora.Integrating.AutoScaling.Add"></a>

You can add a scaling policy using the AWS Management Console, the AWS CLI, or the Application Auto Scaling API\.


+ [Adding a Scaling Policy Using the AWS Management Console](#Aurora.Integrating.AutoScaling.AddConsole)
+ [Adding a Scaling Policy Using the AWS CLI or the Application Auto Scaling API](#Aurora.Integrating.AutoScaling.AddCode)

### Adding a Scaling Policy Using the AWS Management Console<a name="Aurora.Integrating.AutoScaling.AddConsole"></a>

You can add a scaling policy to an Aurora DB cluster by using the AWS Management Console\.

**To add an Auto Scaling policy to an Aurora DB cluster**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Clusters**\. 

1. Choose the Aurora DB cluster that you want to add a policy for\.

1. Choose **Cluster actions**, and then choose **Add Auto Scaling policy**\.

   The **Add Auto Scaling policy** dialog box appears\.

1. Type the policy name in the **Policy Name** box\.

1. For the target metric, choose one of the following:

   + **Average CPU utilization of Aurora Replicas** to create a policy based on the average CPU utilization\.

   + **Average connections of Aurora Replicas** to create a policy based on the average number of connections to Aurora Replicas\.

1. For the target value, type one of the following:

   + If you chose **Average CPU utilization of Aurora Replicas** in the previous step, type the percentage of CPU utilization that you want to maintain on Aurora Replicas\.

   + If you chose **Average connections of Aurora Replicas** in the previous step, type the number of connections that you want to maintain\.

   Aurora Replicas are added or removed to keep the metric close to the specified value\.

1. \(Optional\) Open **Additional Configuration** to create a scale\-in or scale\-out cooldown period\.

1. For **Minimum capacity**, type the minimum number of Aurora Replicas that the Aurora Auto Scaling policy is required to maintain\.

1. For **Maximum capacity**, type the maximum number of Aurora Replicas the Aurora Auto Scaling policy is required to maintain\.

1. Choose **Add policy**\.

The following dialog box creates an Auto Scaling policy based an average CPU utilization of 40 percent\. The policy specifies a minimum of 5 Aurora Replicas and a maximum of 15 Aurora Replicas\.

![\[Creating an Auto Scaling policy based on average CPU utilization\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/aurora-autoscaling-cpu.png)

The following dialog box creates an Auto Scaling policy based an average number of connections of 100\. The policy specifies a minimum of two Aurora Replicas and a maximum of eight Aurora Replicas\.

![\[Creating an Auto Scaling policy based on average connections\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/aurora-autoscaling-connections.png)

### Adding a Scaling Policy Using the AWS CLI or the Application Auto Scaling API<a name="Aurora.Integrating.AutoScaling.AddCode"></a>

You can apply a scaling policy based on either a predefined or custom metric\. To do so, you can use the AWS CLI or the Application Auto Scaling API\. The first step is to register your Aurora DB cluster with Application Auto Scaling\.

#### Registering an Aurora DB Cluster<a name="Aurora.Integrating.AutoScaling.AddCode.Register"></a>

Before you can use Aurora Auto Scaling with an Aurora DB cluster, you register your Aurora DB cluster with Application Auto Scaling\. You do so to define the scaling dimension and limits to be applied to that cluster\. Application Auto Scaling dynamically scales the Aurora DB cluster along the `rds:cluster:ReadReplicaCount` scalable dimension, which represents the number of Aurora Replicas\. 

To register your Aurora DB cluster, you can use either the AWS CLI or the Application Auto Scaling API\. 

##### CLI<a name="Aurora.Integrating.AutoScaling.AddCode.Register.CLI"></a>

To register your Aurora DB cluster, use the [http://docs.aws.amazon.com/cli/latest/reference/application-autoscaling/register-scalable-target.html](http://docs.aws.amazon.com/cli/latest/reference/application-autoscaling/register-scalable-target.html) AWS CLI command with the following parameters:

+ `--service-namespace` – Set this value to `rds`\.

+ `--resource-id` – The resource identifier for the Aurora DB cluster\. For this parameter, the resource type is `cluster` and the unique identifier is the name of the Aurora DB cluster, for example `cluster:myscalablecluster`\.

+ `--scalable-dimension` – Set this value to `rds:cluster:ReadReplicaCount`\.

+ `--min-capacity` – The minimum number of Aurora Replicas to be managed by Application Auto Scaling\. This value must be set to 0–15, and must be equal to or less than the value specified for `max-capacity`\.

+ `--max-capacity` – The maximum number of Aurora Replicas to be managed by Application Auto Scaling\. This value must be set to 0–15, and must be equal to or greater than the value specified for `min-capacity`\.

**Example**  
In the following example, you register an Aurora DB cluster named `myscalablecluster`\. The registration indicates that the DB cluster should be dynamically scaled to have from one to eight Aurora Replicas\.  
For Linux, OS X, or Unix:  

```
aws application-autoscaling register-scalable-target \
    --service-namespace rds \
    --resource-id cluster:myscalablecluster \
    --scalable-dimension rds:cluster:ReadReplicaCount \
    --min-capacity 1 \
    --max-capacity 8 \
```
For Windows:  

```
aws application-autoscaling register-scalable-target ^
    --service-namespace rds ^
    --resource-id cluster:myscalablecluster ^
    --scalable-dimension rds:cluster:ReadReplicaCount ^
    --min-capacity 1 ^
    --max-capacity 8 ^
```

##### API<a name="Aurora.Integrating.AutoScaling.AddCode.Register.API"></a>

To register your Aurora DB cluster with Application Auto Scaling, use the [http://docs.aws.amazon.com/ApplicationAutoScaling/latest/APIReference/API_RegisterScalableTarget.html](http://docs.aws.amazon.com/ApplicationAutoScaling/latest/APIReference/API_RegisterScalableTarget.html) Application Auto Scaling API action with the following parameters:

+ `ServiceNamespace` – Set this value to `rds`\.

+ `ResourceID` – The resource identifier for the Aurora DB cluster\. For this parameter, the resource type is `cluster` and the unique identifier is the name of the Aurora DB cluster, for example `cluster:myscalablecluster`\.

+ `ScalableDimension` – Set this value to `rds:cluster:ReadReplicaCount`\.

+ `MinCapacity` – The minimum number of Aurora Replicas to be managed by Application Auto Scaling\. This value must be set to 0–15, and must be equal to or less than the value specified for `MaxCapacity`\.

+ `MaxCapacity` – The maximum number of Aurora Replicas to be managed by Application Auto Scaling\. This value must be set to 0–15, and must be equal to or greater than the value specified for `MinCapacity`\.

**Example**  
In the following example, you register an Aurora DB cluster named `myscalablecluster` with the Application Auto Scaling API\. This registration indicates that the DB cluster should be dynamically scaled to have from one to eight Aurora Replicas\.  

```
POST / HTTP/1.1
Host: autoscaling.us-east-2.amazonaws.com
Accept-Encoding: identity
Content-Length: 219
X-Amz-Target: AnyScaleFrontendService.RegisterScalableTarget
X-Amz-Date: 20160506T182145Z
User-Agent: aws-cli/1.10.23 Python/2.7.11 Darwin/15.4.0 botocore/1.4.8
Content-Type: application/x-amz-json-1.1
Authorization: AUTHPARAMS

{
    "ServiceNamespace": "rds",
    "ResourceId": "cluster:myscalablecluster",
    "ScalableDimension": "rds:cluster:ReadReplicaCount",
    "MinCapacity": 1,
    "MaxCapacity": 8
}
```

#### Defining a Scaling Policy for an Aurora DB Cluster<a name="Aurora.Integrating.AutoScaling.AddCode.DefineScalingPolicy"></a>

A target\-tracking scaling policy configuration is represented by a JSON block that the metrics and target values are defined in\. You can save a scaling policy configuration as a JSON block in a text file\. You use that text file when invoking the AWS CLI or the Application Auto Scaling API\. For more information about policy configuration syntax, see [http://docs.aws.amazon.com/ApplicationAutoScaling/latest/APIReference/API_TargetTrackingScalingPolicyConfiguration.html](http://docs.aws.amazon.com/ApplicationAutoScaling/latest/APIReference/API_TargetTrackingScalingPolicyConfiguration.html) in the *Application Auto Scaling API Reference*\.

 The following options are available for defining a target\-tracking scaling policy configuration\.


+ [Using a Predefined Metric](#Aurora.Integrating.AutoScaling.AddCode.DefineScalingPolicy.Predefined)
+ [Using a Custom Metric](#Aurora.Integrating.AutoScaling.AddCode.DefineScalingPolicy.Custom)
+ [Using Cooldown Periods](#Aurora.Integrating.AutoScaling.AddCode.DefineScalingPolicy.Cooldown)
+ [Disabling Scale\-in Activity](#Aurora.Integrating.AutoScaling.AddCode.DefineScalingPolicy.ScaleIn)

##### Using a Predefined Metric<a name="Aurora.Integrating.AutoScaling.AddCode.DefineScalingPolicy.Predefined"></a>

By using predefined metrics, you can quickly define a target\-tracking scaling policy for an Aurora DB cluster that works well with both target tracking and dynamic scaling in Aurora Auto Scaling\. 

Currently, Aurora MySQL supports the following predefined metrics in Aurora Auto Scaling:

+ **RDSReaderAverageCPUUtilization** – The average value of the `CPUUtilization` Aurora MySQL metric in CloudWatch across all Aurora Replicas in the Aurora DB cluster\.

+ **RDSReaderAverageDatabaseConnections** – The average value of the `DatabaseConnections` Aurora MySQL metric in CloudWatch across all Aurora Replicas in the Aurora DB cluster\.

For more information about the `CPUUtilization` and `DatabaseConnections` metrics for Aurora MySQL, see [Amazon Aurora MySQL Metrics](Aurora.Monitoring.md#Aurora.AuroraMySQL.Monitoring.Metrics)\.

To use a predefined metric in your scaling policy, you create a target tracking configuration for your scaling policy\. This configuration must include a `PredefinedMetricSpecification` for the predefined metric and a `TargetValue` for the target value of that metric\.

**Example**  
The following example describes a typical policy configuration for target\-tracking scaling for an Aurora DB cluster\. In this configuration, the `RDSReaderAverageCPUUtilization` predefined metric is used to adjust the Aurora DB cluster based on an average CPU utilization of 40 percent across all Aurora Replicas\.   

```
{
    "TargetValue": 40.0,
    "PredefinedMetricSpecification":
    {
        "PredefinedMetricType": "RDSReaderAverageCPUUtilization"
    }
}
```

##### Using a Custom Metric<a name="Aurora.Integrating.AutoScaling.AddCode.DefineScalingPolicy.Custom"></a>

By using custom metrics, you can define a target\-tracking scaling policy that meets your custom requirements\. You can define a custom metric based on any Aurora MySQL metric that changes in proportion to scaling\. 

Not all Aurora MySQL metrics work for target tracking\. The metric must be a valid utilization metric and describe how busy an instance is\. The value of the metric must increase or decrease in proportion to the number of Aurora Replicas in the Aurora DB cluster\. This proportional increase or decrease is necessary to use the metric data to proportionally scale out or in the number of Aurora Replicas\.

**Example**  
The following example describes a target\-tracking configuration for a scaling policy\. In this configuration, a custom metric adjusts an Aurora DB cluster based on an average CPU utilization of 50 percent across all Aurora Replicas in an Aurora DB cluster named `my-db-cluster`\.  

```
{
    "TargetValue": 50,
    "CustomizedMetricSpecification":
    {
        "MetricName": "CPUUtilization",
        "Namespace": "AWS/RDS",
        "Dimensions": [
            {"Name": "DBClusterIdentifier","Value": "my-db-cluster"},
            {"Name": "Role","Value": "READER"}
        ],
        "Statistic": "Average",
        "Unit": "Percent"
    }
}
```

##### Using Cooldown Periods<a name="Aurora.Integrating.AutoScaling.AddCode.DefineScalingPolicy.Cooldown"></a>

You can specify a value, in seconds, for `ScaleOutCooldown` to add a cooldown period for scaling out your Aurora DB cluster\. Similarly, you can add a value, in seconds, for `ScaleInCooldown` to add a cooldown period for scaling in your Aurora DB cluster\. For more information about `ScaleInCooldown` and `ScaleOutCooldown`, see [http://docs.aws.amazon.com/ApplicationAutoScaling/latest/APIReference/API_TargetTrackingScalingPolicyConfiguration.html](http://docs.aws.amazon.com/ApplicationAutoScaling/latest/APIReference/API_TargetTrackingScalingPolicyConfiguration.html) in the *Application Auto Scaling API Reference*\. 

**Example**  
The following example describes a target\-tracking configuration for a scaling policy\. In this configuration, the `RDSReaderAverageCPUUtilization` predefined metric is used to adjust an Aurora DB cluster based on an average CPU utilization of 40 percent across all Aurora Replicas in that Aurora DB cluster\. The configuration provides a scale\-in cooldown period of 10 minutes and a scale\-out cooldown period of 5 minutes\.   

```
{
    "TargetValue": 40.0,
    "PredefinedMetricSpecification":
    {
        "PredefinedMetricType": "RDSReaderAverageCPUUtilization"
    },
    "ScaleInCooldown": 600,
    "ScaleOutCooldown": 300
}
```

##### Disabling Scale\-in Activity<a name="Aurora.Integrating.AutoScaling.AddCode.DefineScalingPolicy.ScaleIn"></a>

You can prevent the target\-tracking scaling policy configuration from scaling in your Aurora DB cluster by disabling scale\-in activity\. Disabling scale\-in activity prevents the scaling policy from deleting Aurora Replicas, while still allowing the scaling policy to create them as needed\.

You can specify a Boolean value for `DisableScaleIn` to enable or disable scale in activity for your Aurora DB cluster\. For more information about `DisableScaleIn`, see [http://docs.aws.amazon.com/ApplicationAutoScaling/latest/APIReference/API_TargetTrackingScalingPolicyConfiguration.html](http://docs.aws.amazon.com/ApplicationAutoScaling/latest/APIReference/API_TargetTrackingScalingPolicyConfiguration.html) in the *Application Auto Scaling API Reference*\. 

**Example**  
The following example describes a target\-tracking configuration for a scaling policy\. In this configuration, the `RDSReaderAverageCPUUtilization` predefined metric adjusts an Aurora DB cluster based on an average CPU utilization of 40 percent across all Aurora Replicas in that Aurora DB cluster\. The configuration disables scale\-in activity for the scaling policy\.  

```
{
    "TargetValue": 40.0,
    "PredefinedMetricSpecification":
    {
        "PredefinedMetricType": "RDSReaderAverageCPUUtilization"
    },
    "DisableScaleIn": true
}
```

#### Applying a Scaling Policy to an Aurora DB Cluster<a name="Aurora.Integrating.AutoScaling.AddCode.ApplyScalingPolicy"></a>

After registering your Aurora DB cluster with Application Auto Scaling and defining a scaling policy, you apply the scaling policy to the registered Aurora DB cluster\. To apply a scaling policy to an Aurora DB cluster, you can use the AWS CLI or the Application Auto Scaling API\. 

##### CLI<a name="Aurora.Integrating.AutoScaling.AddCode.ApplyScalingPolicy.CLI"></a>

To apply a scaling policy to your Aurora DB cluster, use the [http://docs.aws.amazon.com/cli/latest/reference/application-autoscaling/put-scaling-policy.html](http://docs.aws.amazon.com/cli/latest/reference/application-autoscaling/put-scaling-policy.html) AWS CLI command with the following parameters:

+ `--policy-name` – The name of the scaling policy\.

+ `--policy-type` – Set this value to `TargetTrackingScaling`\.

+ `--resource-id` – The resource identifier for the Aurora DB cluster\. For this parameter, the resource type is `cluster` and the unique identifier is the name of the Aurora DB cluster, for example `cluster:myscalablecluster`\.

+ `--service-namespace` – Set this value to `rds`\.

+ `--scalable-dimension` – Set this value to `rds:cluster:ReadReplicaCount`\.

+ `--target-tracking-scaling-policy-configuration` – The target\-tracking scaling policy configuration to use for the Aurora DB cluster\.

**Example**  
In the following example, you apply a target\-tracking scaling policy named `myscalablepolicy` to an Aurora DB cluster named `myscalablecluster` with Application Auto Scaling\. To do so, you use a policy configuration saved in a file named `config.json`\.  
For Linux, OS X, or Unix:  

```
aws application-autoscaling put-scaling-policy \
    --policy-name myscalablepolicy \
    --policy-type TargetTrackingScaling \
    --resource-id cluster:myscalablecluster \
    --service-namespace rds \
    --scalable-dimension rds:cluster:ReadReplicaCount \
    --target-tracking-scaling-policy-configuration file://config.json
```
For Windows:  

```
aws application-autoscaling put-scaling-policy ^
    --policy-name myscalablepolicy ^
    --policy-type TargetTrackingScaling ^
    --resource-id cluster:myscalablecluster ^
    --service-namespace rds ^
    --scalable-dimension rds:cluster:ReadReplicaCount ^
    --target-tracking-scaling-policy-configuration file://config.json
```

##### API<a name="Aurora.Integrating.AutoScaling.AddCode.ApplyScalingPolicy.API"></a>

To apply a scaling policy to your Aurora DB cluster with the Application Auto Scaling API, use the [http://docs.aws.amazon.com/ApplicationAutoScaling/latest/APIReference/API_PutScalingPolicy.html](http://docs.aws.amazon.com/ApplicationAutoScaling/latest/APIReference/API_PutScalingPolicy.html) Application Auto Scaling API action with the following parameters:

+ `PolicyName` – The name of the scaling policy\.

+ `ServiceNamespace` – Set this value to `rds`\.

+ `ResourceID` – The resource identifier for the Aurora DB cluster\. For this parameter, the resource type is `cluster` and the unique identifier is the name of the Aurora DB cluster, for example `cluster:myscalablecluster`\.

+ `ScalableDimension` – Set this value to `rds:cluster:ReadReplicaCount`\.

+ `PolicyType` – Set this value to `TargetTrackingScaling`\.

+ `TargetTrackingScalingPolicyConfiguration` – The target\-tracking scaling policy configuration to use for the Aurora DB cluster\.

**Example**  
In the following example, you apply a target\-tracking scaling policy named `myscalablepolicy` to an Aurora DB cluster named `myscalablecluster` with Application Auto Scaling\. You use a policy configuration based on the `RDSReaderAverageCPUUtilization` predefined metric\.  

```
POST / HTTP/1.1
Host: autoscaling.us-east-2.amazonaws.com
Accept-Encoding: identity
Content-Length: 219
X-Amz-Target: AnyScaleFrontendService.PutScalingPolicy
X-Amz-Date: 20160506T182145Z
User-Agent: aws-cli/1.10.23 Python/2.7.11 Darwin/15.4.0 botocore/1.4.8
Content-Type: application/x-amz-json-1.1
Authorization: AUTHPARAMS

{
    "PolicyName": "myscalablepolicy",
    "ServiceNamespace": "rds",
    "ResourceId": "cluster:myscalablecluster",
    "ScalableDimension": "rds:cluster:ReadReplicaCount",
    "PolicyType": "TargetTrackingScaling",
    "TargetTrackingScalingPolicyConfiguration": {
        "TargetValue": 40.0,
        "PredefinedMetricSpecification":
        {
            "PredefinedMetricType": "RDSReaderAverageCPUUtilization"
        }
    }
}
```

## Editing a Scaling Policy<a name="Aurora.Integrating.AutoScaling.Edit"></a>

You can edit a scaling policy using the AWS Management Console, the AWS CLI, or the Application Auto Scaling API\.

### Editing a Scaling Policy Using the AWS Management Console<a name="Aurora.Integrating.AutoScaling.EditConsole"></a>

You can edit a scaling policy by using the AWS Management Console\.

**To edit an Auto Scaling policy for an Aurora DB cluster**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Clusters**\. 

1. Click the Aurora DB cluster whose Auto Scaling policy you want to edit to show the DB cluster's details\.

1. In the **Auto Scaling Policies** section, choose the Auto Scaling policy, and then choose **Edit**\.

1. Make changes to the policy\.

1. Choose **Save**\.

The following is a sample **Edit Auto Scaling policy** dialog box\.

![\[Editing an Auto Scaling policy based on average CPU utilization\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/aurora-autoscaling-edit-cpu.png)

### Editing a Scaling Policy Using the AWS CLI or the Application Auto Scaling API<a name="Aurora.Integrating.AutoScaling.EditCode"></a>

You can use the AWS CLI or the Application Auto Scaling API to edit a scaling policy in the same way that you apply a scaling policy:

+ When using the AWS CLI, specify the name of the policy you want to edit in the `--policy-name` parameter\. Specify new values for the parameters you want to change\.

+ When using the Application Auto Scaling API, specify the name of the policy you want to edit in the `PolicyName` parameter\. Specify new values for the parameters you want to change\.

For more information, see [Applying a Scaling Policy to an Aurora DB Cluster](#Aurora.Integrating.AutoScaling.AddCode.ApplyScalingPolicy)\.

## Deleting a Scaling Policy<a name="Aurora.Integrating.AutoScaling.Delete"></a>

You can delete a scaling policy using the AWS Management Console, the AWS CLI, or the Application Auto Scaling API\.

### Deleting a Scaling Policy Using the AWS Management Console<a name="Aurora.Integrating.AutoScaling.DeleteConsole"></a>

You can delete a scaling policy by using the AWS Management Console\.

**To delete an Auto Scaling policy for an Aurora DB cluster**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Clusters**\. 

1. Choose the Aurora DB cluster with the scaling policy that you want to delete\.

1. In the **Auto Scaling Policies** section, choose the Auto Scaling policy, and then choose **Delete**\.

### Deleting a Scaling Policy Using the AWS CLI or the Application Auto Scaling API<a name="Aurora.Integrating.AutoScaling.DeleteCode"></a>

You can use the AWS CLI or the Application Auto Scaling API to delete a scaling policy from an Aurora DB cluster\.

#### CLI<a name="Aurora.Integrating.AutoScaling.DeleteCode.CLI"></a>

To delete a scaling policy from your Aurora DB cluster, use the [http://docs.aws.amazon.com/cli/latest/reference/application-autoscaling/delete-scaling-policy.html](http://docs.aws.amazon.com/cli/latest/reference/application-autoscaling/delete-scaling-policy.html) AWS CLI command with the following parameters:

+ `--policy-name` – The name of the scaling policy\.

+ `--resource-id` – The resource identifier for the Aurora DB cluster\. For this parameter, the resource type is `cluster` and the unique identifier is the name of the Aurora DB cluster, for example `cluster:myscalablecluster`\.

+ `--service-namespace` – Set this value to `rds`\.

+ `--scalable-dimension` – Set this value to `rds:cluster:ReadReplicaCount`\.

**Example**  
In the following example, you delete a target\-tracking scaling policy named `myscalablepolicy` from an Aurora DB cluster named `myscalablecluster`\.  
For Linux, OS X, or Unix:  

```
aws application-autoscaling delete-scaling-policy \
    --policy-name myscalablepolicy \
    --resource-id cluster:myscalablecluster \
    --service-namespace rds \
    --scalable-dimension rds:cluster:ReadReplicaCount \
```
For Windows:  

```
aws application-autoscaling delete-scaling-policy ^
    --policy-name myscalablepolicy ^
    --resource-id cluster:myscalablecluster ^
    --service-namespace rds ^
    --scalable-dimension rds:cluster:ReadReplicaCount ^
```

#### API<a name="Aurora.Integrating.AutoScaling.DeleteCode.API"></a>

To delete a scaling policy from your Aurora DB cluster, use the [http://docs.aws.amazon.com/ApplicationAutoScaling/latest/APIReference/API_DeleteScalingPolicy.html](http://docs.aws.amazon.com/ApplicationAutoScaling/latest/APIReference/API_DeleteScalingPolicy.html) the Application Auto Scaling API action with the following parameters:

+ `PolicyName` – The name of the scaling policy\.

+ `ServiceNamespace` – Set this value to `rds`\.

+ `ResourceID` – The resource identifier for the Aurora DB cluster\. For this parameter, the resource type is `cluster` and the unique identifier is the name of the Aurora DB cluster, for example `cluster:myscalablecluster`\.

+ `ScalableDimension` – Set this value to `rds:cluster:ReadReplicaCount`\.

**Example**  
In the following example, you delete a target\-tracking scaling policy named `myscalablepolicy` from an Aurora DB cluster named `myscalablecluster` with the Application Auto Scaling API\.  

```
POST / HTTP/1.1
Host: autoscaling.us-east-2.amazonaws.com
Accept-Encoding: identity
Content-Length: 219
X-Amz-Target: AnyScaleFrontendService.DeleteScalingPolicy
X-Amz-Date: 20160506T182145Z
User-Agent: aws-cli/1.10.23 Python/2.7.11 Darwin/15.4.0 botocore/1.4.8
Content-Type: application/x-amz-json-1.1
Authorization: AUTHPARAMS

{
    "PolicyName": "myscalablepolicy",
    "ServiceNamespace": "rds",
    "ResourceId": "cluster:myscalablecluster",
    "ScalableDimension": "rds:cluster:ReadReplicaCount"
}
```

## Related Topics<a name="Aurora.Integrating.AutoScaling.RelatedItems"></a>

+ [Integrating Aurora with Other AWS Services](Aurora.Integrating.md)

+ [Amazon Aurora on Amazon RDS](CHAP_Aurora.md)