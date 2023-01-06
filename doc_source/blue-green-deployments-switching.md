# Switching a blue/green deployment<a name="blue-green-deployments-switching"></a>

A *switchover* promotes the green environment to be the new production environment\. When the green DB instance has read replicas, they are also promoted\. Before you switch over, production traffic is routed to the DB instance and read replicas in the blue environment\. After you switch over, production traffic is routed to the DB instance and read replicas in the green environment\.

**Topics**
+ [Switchover timeout](#blue-green-deployments-switching-timeout)
+ [Switchover guardrails](#blue-green-deployments-switching-guardrails)
+ [Switchover actions](#blue-green-deployments-switching-actions)
+ [Switchover best practices](#blue-green-deployments-switching-best-practices)
+ [Switching over a blue/green deployment](#blue-green-deployments-switching-over)
+ [After switchover](#blue-green-deployments-switching-after)

## Switchover timeout<a name="blue-green-deployments-switching-timeout"></a>

You can specify a switchover timeout period between 30 seconds and 3,600 seconds \(one hour\)\. If the switchover takes longer than the specified duration, then any changes are rolled back and no changes are made to either environment\. The default timeout period is 300 seconds \(five minutes\)\.

## Switchover guardrails<a name="blue-green-deployments-switching-guardrails"></a>

When you start a switchover, Amazon RDS runs some basic checks to test the readiness of the blue and green environments for switchover\. These checks are known as *switchover guardrails*\. These switchover guardrails prevent a switchover if the environments aren't ready for it\. Therefore, they avoid longer than expected downtime and prevent the loss of data between the blue and green environments that might result if the switchover started\.

Amazon RDS runs the following guardrail checks on the green environment:
+ Replication health – Check if green primary DB instance replication status is healthy\. The green primary DB instance is a replica of the blue primary DB instance\.
+ Replication lag – Check if the replica lag of the green primary DB instance is within allowable limits for switchover\. The allowable limits are based on the specified timeout period\. Replica lag indicates how far the green primary DB instance is lagging behind its blue primary DB instance\. Replica lag indicates how much time the green replica might require before it catches up with its blue source\. For more information, see [Diagnosing and resolving lag between read replicas](CHAP_Troubleshooting.md#CHAP_Troubleshooting.MySQL.ReplicaLag)\.
+ Active writes – Make sure there are no active writes on the green primary DB instance\.

Amazon RDS runs the following guardrail checks on the blue environment:
+ External replication – Make sure the blue primary DB instance isn't the target of external replication to prevent writes on the blue primary DB instance during switchover\.
+ Long\-running active writes – Make sure there are no long\-running active writes on the blue primary DB instance because they can increase replica lag\.
+ Long\-running DDL statements – Make sure there are no long\-running DDL statements on the blue primary DB instance because they can increase replica lag\.

## Switchover actions<a name="blue-green-deployments-switching-actions"></a>

When you switch over a blue/green deployment, RDS performs the following actions:

1. Runs guardrail checks to verify if the blue and green environments are ready for switchover\.

1. Stops new write operations on the primary DB instance in both environments\.

1. Drops connections to the DB instances in both environments and doesn't allow new connections\.

1. Waits for replication to catch up in the green environment so that the green environment is in sync with the blue environment\.

1. Renames the DB instances in the both environments\.

   RDS renames the DB instances in the green environment to match the corresponding DB instances in the blue environment\. For example, assume the name of a DB instance in the blue environment is `mydb`\. Also assume the name of the corresponding DB instance in the green environment is `mydb-green-abc123`\. During switchover, the name of the DB instance in the green environment is changed to `mydb`\.

   RDS renames the DB instances in the blue environment by appending `-oldn` to the current name, where `n` is a number\. For example, assume the name of a DB instance in the blue environment is `mydb`\. After switchover, the DB instance name might be `mydb-old1`\.

   RDS also renames the endpoints in the green environment to match the corresponding endpoints in the blue environment so that application changes aren't required\.

1. Allows connections to databases in both environments\.

1. Allows write operations on the primary DB instance in the new production environment\.

   After switchover, the previous production primary DB instance only allows read operations until it is rebooted\.

If you have tags configured in the blue environment, these tags are moved to the new production environment during switchover\. The previous production environment also retains these tags\. For more information about tags, see [Tagging Amazon RDS resources](USER_Tagging.md)\.

If the switchover starts and then stops before finishing for any reason, then any changes are rolled back, and no changes are made to either environment\.

## Switchover best practices<a name="blue-green-deployments-switching-best-practices"></a>

Before you switchover, we strongly recommend that you adhere to best practices by completing the following tasks:
+ Thoroughly test the resources in the green environment\. Make sure they function properly and efficiently\.
+ Identify a time when traﬃc is lowest on your production environment\. During the switchover, writes are cut oﬀ from the databases in both environments\. Long\-running transactions, such as active DDLs can, increase your switchover time, resulting in longer downtime for your production workloads\.
+ Make sure the DB instances in both environments are in `Available` state\.
+ Make sure the primary DB instance in the green environment is healthy and replicating\.
+ Make sure data loading is complete before switching over\. For more information, see [Handling lazy loading when you create a blue/green deployment](blue-green-deployments-creating.md#blue-green-deployments-creating-lazy-loading)\.

**Note**  
During a switchover, you can't modify any DB instances included in the switchover\.

## Switching over a blue/green deployment<a name="blue-green-deployments-switching-over"></a>

You can switch over a blue/green deployment using the AWS Management Console, the AWS CLI, or the RDS API\.

### Console<a name="blue-green-deployments-switching-console"></a>

**To switch over a blue/green deployment**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**, and then choose the blue/green deployment that you want to switch over\.

1. For **Actions**, choose **Switch over**\.

   The **Switch over** page appears\.  
![\[Switch over blue/green deployment\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/blue-green-deployment-switch-over.png)

1. On the **Switch over** page, review the switchover summary\. Make sure the resources in both environments match what you expect\. If they don't, choose **Cancel**\.

1. For **Timeout**, enter the time limit for switchover\.

1. Choose **Switch over**\.

### AWS CLI<a name="blue-green-deployments-switching-cli"></a>

To switch over a blue/green deployment by using the AWS CLI, use the [switchover\-blue\-green\-deployment](https://docs.aws.amazon.com/cli/latest/reference/rds/switchover-blue-green-deployment.html) command with the following options:
+ `--blue-green-deployment-identifier` – Specify the identifier of the blue/green deployment\.
+ `--switchover-timeout` – Specify the time limit for the switchover, in seconds\. The default is 300\.

**Example Switch over a blue/green deployment**  
For Linux, macOS, or Unix:  

```
aws rds switchover-blue-green-deployment \
    --blue-green-deployment-identifier bgd-1234567890abcdef \
    --switchover-timeout 600
```
For Windows:  

```
aws rds switchover-blue-green-deployment ^
    --blue-green-deployment-identifier bgd-1234567890abcdef ^
    --switchover-timeout 600
```

### RDS API<a name="blue-green-deployments-switching-api"></a>

To switch over a blue/green deployment by using the Amazon RDS API, use the [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_SwitchoverBlueGreenDeployment.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_SwitchoverBlueGreenDeployment.html) operation with the following parameters:
+ `BlueGreenDeploymentIdentifier` – Specify the identifier of the blue/green deployment\.
+ `SwitchoverTimeout` – Specify the time limit for the switchover, in seconds\. The default is 300\.

## After switchover<a name="blue-green-deployments-switching-after"></a>

After a switchover, the DB instances in the previous blue environment are retained\. Standard costs apply to these resources\.

RDS renames the DB instances in the blue environment by appending `-oldn` to the current resource name, where `n` is a number\. The DB instances are read\-only until they are rebooted\. 