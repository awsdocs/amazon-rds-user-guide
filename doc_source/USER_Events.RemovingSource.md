# Removing a source identifier from an Amazon RDS event notification subscription<a name="USER_Events.RemovingSource"></a>

You can remove a source identifier \(the Amazon RDS source generating the event\) from a subscription if you no longer want to be notified of events for that source\. 

## Console<a name="USER_Events.RemovingSource.Console"></a>

You can easily add or remove source identifiers using the Amazon RDS console by selecting or deselecting them when modifying a subscription\. For more information, see [Modifying an Amazon RDS event notification subscription](USER_Events.Modifying.md)\.

## AWS CLI<a name="USER_Events.RemovingSource.CLI"></a>

To remove a source identifier from an Amazon RDS event notification subscription, use the AWS CLI [https://docs.aws.amazon.com/cli/latest/reference/rds/remove-source-identifier-from-subscription.html](https://docs.aws.amazon.com/cli/latest/reference/rds/remove-source-identifier-from-subscription.html) command\. Include the following required parameters:
+ `--subscription-name`
+ `--source-identifier`

**Example**  
The following example removes the source identifier `mysqldb` from the `myrdseventsubscription` subscription\.  
For Linux, macOS, or Unix:  

```
aws rds remove-source-identifier-from-subscription \
    --subscription-name myrdseventsubscription \
    --source-identifier mysqldb
```
For Windows:  

```
aws rds remove-source-identifier-from-subscription ^
    --subscription-name myrdseventsubscription ^
    --source-identifier mysqldb
```

## API<a name="USER_Events.RemovingSource.API"></a>

To remove a source identifier from an Amazon RDS event notification subscription, use the Amazon RDS API [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RemoveSourceIdentifierFromSubscription.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RemoveSourceIdentifierFromSubscription.html) command\. Include the following required parameters:
+ `SubscriptionName`
+ `SourceIdentifier`