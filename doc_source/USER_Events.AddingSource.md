# Adding a source identifier to an Amazon RDS event notification subscription<a name="USER_Events.AddingSource"></a>

You can add a source identifier \(the Amazon RDS source generating the event\) to an existing subscription\.

## Console<a name="USER_Events.AddingSource.Console"></a>

You can easily add or remove source identifiers using the Amazon RDS console by selecting or deselecting them when modifying a subscription\. For more information, see [Modifying an Amazon RDS event notification subscription](USER_Events.Modifying.md)\.

## AWS CLI<a name="USER_Events.AddingSource.CLI"></a>

To add a source identifier to an Amazon RDS event notification subscription, use the AWS CLI [https://docs.aws.amazon.com/](https://docs.aws.amazon.com/) command\. Include the following required parameters:
+ `--subscription-name`
+ `--source-identifier`

**Example**  
The following example adds the source identifier `mysqldb` to the `myrdseventsubscription` subscription\.  
For Linux, macOS, or Unix:  

```
aws rds add-source-identifier-to-subscription \
    --subscription-name myrdseventsubscription \
    --source-identifier mysqldb
```
For Windows:  

```
aws rds add-source-identifier-to-subscription ^
    --subscription-name myrdseventsubscription ^
    --source-identifier mysqldb
```

## API<a name="USER_Events.AddingSource.API"></a>

To add a source identifier to an Amazon RDS event notification subscription, call the Amazon RDS API [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_AddSourceIdentifierToSubscription.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_AddSourceIdentifierToSubscription.html)\. Include the following required parameters:
+ `SubscriptionName`
+ `SourceIdentifier`