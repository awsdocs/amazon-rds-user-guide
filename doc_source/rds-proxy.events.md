# Working with RDS Proxy events<a name="rds-proxy.events"></a>

An *event* indicates a change in an environment\. This can be an AWS environment or a service or application from a software as a service \(SaaS\) partner\. Or it can be one of your own custom applications or services\. For example, Amazon RDS generates an event when you create or modify an RDS Proxy\. Amazon RDS delivers events to CloudWatch Events and Amazon EventBridge in near\-real time\. Following, you can find a list of RDS Proxy events that you can subscribe to and an example of an RDS Proxy event\. 

For more information about working with events, see the following:
+ For instructions on how to view events by using the AWS Management Console, AWS CLI, or RDS API, see [Viewing Amazon RDS events](USER_ListEvents.md)\.
+ To learn how to configure Amazon RDS to send events to EventBridge, see [Creating a rule that triggers on an Amazon RDS event](rds-cloud-watch-events.md)\.

## RDS Proxy events<a name="rds-proxy.events.list"></a>

The following table shows the event category and a list of events when an RDS Proxy is the source type\.


|  Category  | RDS event ID |  Description  | 
| --- | --- | --- | 
| configuration change | RDS\-EVENT\-0204 |  RDS modified the DB proxy \(RDS Proxy\)\.  | 
|  configuration change  | RDS\-EVENT\-0207 |  RDS modified the endpoint of the DB proxy \(RDS Proxy\)\.    | 
|  configuration change  | RDS\-EVENT\-0213 | RDS detected the addition of the DB instance and automatically added it to the target group of the DB proxy \(RDS Proxy\)\.  | 
|  configuration change  | RDS\-EVENT\-0214 |  RDS detected the deletion of the DB instance and automatically removed it from the target group of the DB proxy \(RDS Proxy\)\.  | 
|  configuration change  | RDS\-EVENT\-0215 |  RDS detected the deletion of the DB cluster and automatically removed it from the target group of the DB proxy \(RDS Proxy\)\.  | 
|  creation  | RDS\-EVENT\-0203 |  RDS created the DB proxy \(RDS Proxy\)\.  | 
|  creation  | RDS\-EVENT\-0206 |  RDS created the endpoint for the DB proxy \(RDS Proxy\)\.  | 
| deletion | RDS\-EVENT\-0205 |  RDS deleted the DB proxy \(RDS Proxy\)\.  | 
|  deletion  | RDS\-EVENT\-0208 |  RDS deleted the endpoint of DB proxy \(RDS Proxy\)\.  | 
|  failure  | RDS\-EVENT\-0243 |  RDS couldn't provision capacity for the proxy because there aren't enough IP addresses available in your subnets\. To fix the issue, make sure that your subnets have the minimum number of unused IP addresses\. To determine the recommended number for your instance class, see [Planning for IP address capacity](rds-proxy-setup.md#rds-proxy-network-prereqs.plan-ip-address)\.  | 

The following is an example of an RDS Proxy event in JSON format\. The event shows that RDS modified the endpoint named `my-endpoint` of the RDS Proxy named `my-rds-proxy`\. The event ID is RDS\-EVENT\-0207\.

```
{
  "version": "0",
  "id": "68f6e973-1a0c-d37b-f2f2-94a7f62ffd4e",
  "detail-type": "RDS DB Proxy Event",
  "source": "aws.rds",
  "account": "123456789012",
  "time": "2018-09-27T22:36:43Z",
  "region": "us-east-1",
  "resources": [
     "arn:aws:rds:us-east-1:123456789012:db-proxy:my-rds-proxy"
  ],
  "detail": {
    "EventCategories": [
      "configuration change"
    ],
    "SourceType": "DB_PROXY",
    "SourceArn": "arn:aws:rds:us-east-1:123456789012:db-proxy:my-rds-proxy",
    "Date": "2018-09-27T22:36:43.292Z",
    "Message": "RDS modified endpoint my-endpoint of DB Proxy my-rds-proxy.",
    "SourceIdentifier": "my-endpoint",
    "EventID": "RDS-EVENT-0207"
  }
}
```