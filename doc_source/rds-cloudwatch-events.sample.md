# Overview of events for Amazon RDS<a name="rds-cloudwatch-events.sample"></a>

An *RDS event* indicates a change in the Amazon RDS environment\. For example, Amazon RDS generates an event when the state of a DB instance changes from pending to running\. Amazon RDS delivers events to CloudWatch Events and EventBridge in near\-real time\.

**Note**  
Amazon RDS emits events on a best effort basis\. We recommend that you avoid writing programs that depend on the order or existence of notification events, because they might be out of sequence or missing\.

Amazon RDS records events that relate to the following resources:
+ DB instances

  For a list of DB instance events, see [DB instance events](USER_Events.Messages.md#USER_Events.Messages.instance)\.
+ DB parameter groups

  For a list of DB parameter group events, see [DB parameter group events](USER_Events.Messages.md#USER_Events.Messages.parameter-group)\.
+ DB security groups

  For a list of DB security group events, see [DB security group events](USER_Events.Messages.md#USER_Events.Messages.security-group)\.
+ DB snapshots

  For a list of DB snapshot events, see [DB snapshot events](USER_Events.Messages.md#USER_Events.Messages.snapshot)\.
+ RDS Proxy events

  For a list of RDS Proxy events, see [RDS Proxy events](USER_Events.Messages.md#USER_Events.Messages.rds-proxy)\.
+ Blue/green deployment events

  For a list of blue/green deployment events, see [Blue/green deployment events](USER_Events.Messages.md#USER_Events.Messages.BlueGreenDeployments)\.

This information includes the following: 
+ The date and time of the event
+ The source name and source type of the event
+ A message associated with the event