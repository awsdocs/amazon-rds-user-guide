# Starting an Amazon RDS DB instance that was previously stopped<a name="USER_StartInstance"></a>

You can stop your Amazon RDS DB instance temporarily to save money\. After you stop your DB instance, you can restart it to begin using it again\. For more details about stopping and starting DB instances, see [Stopping an Amazon RDS DB instance temporarily](USER_StopInstance.md)\. 

When you start a DB instance that you previously stopped, the DB instance retains certain information\. This information is the ID, Domain Name Server \(DNS\) endpoint, parameter group, security group, and option group\. When you start a stopped instance, you are charged a full instance hour\. 

## Console<a name="USER_StartInstance.CON"></a>

**To start a DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**, and then choose the DB instance that you want to start\. 

1. For **Actions**, choose **Start**\. 

## AWS CLI<a name="USER_StartInstance.CLI"></a>

To start a DB instance by using the AWS CLI, call the [start\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/start-db-instance.html) command with the following option: 
+ `--db-instance-identifier` – The name of the DB instance\. 

**Example**  

```
1. aws rds start-db-instance --db-instance-identifier mydbinstance
```

## RDS API<a name="USER_StartInstance.API"></a>

To start a DB instance by using the Amazon RDS API, call the [StartDBInstance](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_StartDBInstance.html) operation with the following parameter: 
+ `DBInstanceIdentifier` – The name of the DB instance\. 