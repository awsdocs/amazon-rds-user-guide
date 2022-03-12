# Amazon RDS updates to AWS managed policies<a name="rds-manpol-updates"></a>

View details about updates to AWS managed policies for Amazon RDS since this service began tracking these changes\. For automatic alerts about changes to this page, subscribe to the RSS feed on the Amazon RDS [Document history](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/WhatsNew.html) page\.




| Change | Description | Date | 
| --- | --- | --- | 
|  [Service\-linked role permissions for Amazon RDS](UsingWithRDS.IAM.ServiceLinkedRoles.md#service-linked-role-permissions) – Update to an existing policy  |  Amazon RDS added new Amazon CloudWatch namespaces to `AWSServiceRoleForRDS` for `PutMetricData`\. These namespaces are required for Amazon DocumentDB \(with MongoDB compatibility\) and Amazon Neptune to publish CloudWatch metrics\. For more information, see [Using condition keys to limit access to CloudWatch namespaces](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/iam-cw-condition-keys-namespace.html) in the *Amazon CloudWatch User Guide*\.  |  March 4, 2022  | 
|  [Service\-linked role permissions for Amazon RDS Custom](UsingWithRDS.IAM.ServiceLinkedRoles.md#slr-permissions-custom) – New policy  |  Amazon RDS added a new service\-linked role named `AWSServiceRoleForRDSCustom` to allow RDS Custom to call AWS services on behalf of your DB instances\.  |  October 26, 2021  | 
|  Amazon RDS started tracking changes  |  Amazon RDS started tracking changes for its AWS managed policies\.  |  October 26, 2021  | 