# Overview of events for Amazon RDS<a name="rds-cloudwatch-events.sample"></a>

An *RDS event* indicates a change in the Amazon RDS environment\. For example, Amazon RDS generates an event when the state of a DB instance changes from pending to running\. Amazon RDS delivers events to CloudWatch Events and EventBridge in near\-real time\.

**Note**  
Amazon RDS emits events on a best effort basis\. We recommend that you avoid writing programs that depend on the order or existence of notification events, because they might be out of sequence or missing\. 

Amazon RDS records events that relate to your DB instances, DB snapshots, and DB parameter groups\. This information includes the following: 
+ The date and time of the event
+ The source name and source type of the event
+ A message associated with the event

The following examples illustrate different types of Amazon RDS events in JSON format\. For a tutorial that shows you how to capture and view events in JSON format, see [Tutorial: log the state of an Amazon RDS instance using EventBridge](rds-cloud-watch-events.md#log-rds-instance-state)\.

**Topics**
+ [Example of a DB instance event](#rds-cloudwatch-events.db-instances)
+ [Example of a DB parameter group event](#rds-cloudwatch-events.db-parameter-groups)
+ [Example of a DB snapshot event](#rds-cloudwatch-events.db-snapshots)

## Example of a DB instance event<a name="rds-cloudwatch-events.db-instances"></a>

The following is an example of a DB instance event in JSON format\. The event shows that RDS performed a multi\-AZ failover for the instance named `my-db-instance`\. The event ID is RDS\-EVENT\-0049\.

```
{
  "version": "0",
  "id": "68f6e973-1a0c-d37b-f2f2-94a7f62ffd4e",
  "detail-type": "RDS DB Instance Event",
  "source": "aws.rds",
  "account": "123456789012",
  "time": "2018-09-27T22:36:43Z",
  "region": "us-east-1",
  "resources": [
    "arn:aws:rds:us-east-1:123456789012:db:my-db-instance"
  ],
  "detail": {
    "EventCategories": [
      "failover"
    ],
    "SourceType": "DB_INSTANCE",
    "SourceArn": "arn:aws:rds:us-east-1:123456789012:db:my-db-instance",
    "Date": "2018-09-27T22:36:43.292Z",
    "Message": "A Multi-AZ failover has completed.",
    "SourceIdentifier": "rds:my-db-instance",
    "EventID": "RDS-EVENT-0049"
  }
}
```

## Example of a DB parameter group event<a name="rds-cloudwatch-events.db-parameter-groups"></a>

The following is an example of a DB parameter group event in JSON format\. The event shows that the parameter `time_zone` was updated in parameter group `my-db-param-group`\. The event ID is RDS\-EVENT\-0037\.

```
{
  "version": "0",
  "id": "844e2571-85d4-695f-b930-0153b71dcb42",
  "detail-type": "RDS DB Parameter Group Event",
  "source": "aws.rds",
  "account": "123456789012",
  "time": "2018-10-06T12:26:13Z",
  "region": "us-east-1",
  "resources": [
    "arn:aws:rds:us-east-1:123456789012:pg:my-db-param-group"
  ],
  "detail": {
    "EventCategories": [
      "configuration change"
    ],
    "SourceType": "DB_PARAM",
    "SourceArn": "arn:aws:rds:us-east-1:123456789012:pg:my-db-param-group",
    "Date": "2018-10-06T12:26:13.882Z",
    "Message": "Updated parameter time_zone to UTC with apply method immediate",
    "SourceIdentifier": "rds:my-db-param-group",
    "EventID": "RDS-EVENT-0037"
  }
}
```

## Example of a DB snapshot event<a name="rds-cloudwatch-events.db-snapshots"></a>

The following is an example of a DB snapshot event in JSON format\. The event shows the deletion of the snapshot named `my-db-snapshot`\. The event ID is RDS\-EVENT\-0041\.

```
{
  "version": "0",
  "id": "844e2571-85d4-695f-b930-0153b71dcb42",
  "detail-type": "RDS DB Snapshot Event",
  "source": "aws.rds",
  "account": "123456789012",
  "time": "2018-10-06T12:26:13Z",
  "region": "us-east-1",
  "resources": [
    "arn:aws:rds:us-east-1:123456789012:snapshot:rds:my-db-snapshot"
  ],
  "detail": {
    "EventCategories": [
      "deletion"
    ],
    "SourceType": "SNAPSHOT",
    "SourceArn": "arn:aws:rds:us-east-1:123456789012:snapshot:rds:my-db-snapshot",
    "Date": "2018-10-06T12:26:13.882Z",
    "Message": "Deleted manual snapshot",
    "SourceIdentifier": "rds:my-db-snapshot",
    "EventID": "RDS-EVENT-0041"
  }
}
```