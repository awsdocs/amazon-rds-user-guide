# Starting an Amazon RDS DB Instance That Was Previously Stopped<a name="USER_StartInstance"></a>

You can stop your Amazon RDS DB instance temporarily to save money\. After you stop your DB instance, you can restart it to begin using it again\. For more details about stopping and starting DB instances, see [Stopping an Amazon RDS DB Instance Temporarily](USER_StopInstance.md)\. 

When you start a DB instance that you previously stopped, the DB instance retains the ID, Domain Name Server \(DNS\) endpoint, parameter group, security group, and option group\. When you start a stopped instance, you are charged a full instance hour\. 

## Console<a name="USER_StartInstance.CON"></a>

**To start a DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**, and then choose the DB instance that you want to start\. 

1. For **Actions**, choose **Start**\. 

## AWS CLI<a name="USER_StartInstance.CLI"></a>

To start a DB instance by using the AWS CLI, call the [start\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/start-db-instance.html) command with the following parameters: 
+ `--db-instance-identifier` – the name of the DB instance\. 

**Example**  

```
1. start-db-instance --db-instance-identifier mydbinstance
```

## RDS API<a name="USER_StartInstance.API"></a>

To start a DB instance by using the Amazon RDS API, call the [StartDBInstance](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_StartDBInstance.html) operation with the following parameters: 
+ `DBInstanceIdentifier` – the name of the DB instance\. 

**Example**  

```
 1. https://rds.amazonaws.com/
 2.     ?Action=StartDBInstance
 3.     &DBInstanceIdentifier=mydbinstance
 4.     &SignatureMethod=HmacSHA256
 5.     &SignatureVersion=4
 6.     &Version=2014-10-31
 7.     &X-Amz-Algorithm=AWS4-HMAC-SHA256
 8.     &X-Amz-Credential=AKIADQKE4SARGYLE/20131016/us-west-1/rds/aws4_request
 9.     &X-Amz-Date=20131016T233051Z
10.     &X-Amz-SignedHeaders=content-type;host;user-agent;x-amz-content-sha256;x-amz-date
11.     &X-Amz-Signature=087a8eb41cb1ab5f99e81575f23e73757ffc6a1e42d7d2b30b9cc0be988cff97
```

## Related Topics<a name="USER_StartInstance.Related"></a>
+ [Deleting a DB Instance](USER_DeleteInstance.md)
+ [Rebooting a DB Instance](USER_RebootInstance.md)