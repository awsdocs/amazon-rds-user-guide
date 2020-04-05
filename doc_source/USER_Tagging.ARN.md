# Working with Amazon Resource Names \(ARNs\) in Amazon RDS<a name="USER_Tagging.ARN"></a>

Resources created in Amazon Web Services are each uniquely identified with an Amazon Resource Name \(ARN\)\. For certain Amazon RDS operations, you must uniquely identify an Amazon RDS resource by specifying its ARN\. For example, when you create an RDS DB instance read replica, you must supply the ARN for the source DB instance\. 

## Constructing an ARN for Amazon RDS<a name="USER_Tagging.ARN.Constructing"></a>

Resources created in Amazon Web Services are each uniquely identified with an Amazon Resource Name \(ARN\)\. You can construct an ARN for an Amazon RDS resource using the following syntax\. 

 `arn:aws:rds:<region>:<account number>:<resourcetype>:<name>` 

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_Tagging.ARN.html)

The following table shows the format that you should use when constructing an ARN for a particular Amazon RDS resource type\. 


****  

| Resource Type | ARN Format | 
| --- | --- | 
| DB instance  |  arn:aws:rds:*<region>*:*<account>*:db:*<name>* For example: <pre>arn:aws:rds:us-east-2:123456789012:db:my-mysql-instance-1</pre>  | 
| Event subscription  |  arn:aws:rds:*<region>*:*<account>*:es:*<name>* For example: <pre>arn:aws:rds:us-east-2:123456789012:es:my-subscription</pre>  | 
| DB option group  |  arn:aws:rds:*<region>*:*<account>*:og:*<name>* For example: <pre>arn:aws:rds:us-east-2:123456789012:og:my-og</pre>  | 
| DB parameter group  |  arn:aws:rds:*<region>*:*<account>*:pg:*<name>* For example: <pre>arn:aws:rds:us-east-2:123456789012:pg:my-param-enable-logs</pre>  | 
| Reserved DB instance  |  arn:aws:rds:*<region>*:*<account>*:ri:*<name>* For example: <pre>arn:aws:rds:us-east-2:123456789012:ri:my-reserved-postgresql</pre>  | 
| DB security group  |  arn:aws:rds:*<region>*:*<account>*:secgrp:*<name>* For example: <pre>arn:aws:rds:us-east-2:123456789012:secgrp:my-public</pre>  | 
| Automated DB snapshot |  arn:aws:rds:*<region>*:*<account>*:snapshot:rds:*<name>* For example: <pre>arn:aws:rds:us-east-2:123456789012:snapshot:rds:my-mysql-db-2019-07-22-07-23</pre>  | 
| Manual DB snapshot |  arn:aws:rds:*<region>*:*<account>*:snapshot:*<name>* For example: <pre>arn:aws:rds:us-east-2:123456789012:snapshot:my-mysql-db-snap</pre>  | 
| DB subnet group |  arn:aws:rds:*<region>*:*<account>*:subgrp:*<name>* For example: <pre>arn:aws:rds:us-east-2:123456789012:subgrp:my-subnet-10</pre>  | 

## Getting an Existing ARN<a name="USER_Tagging.ARN.Getting"></a>

You can get the ARN of an RDS resource by using the AWS Management Console, AWS Command Line Interface \(AWS CLI\), or RDS API\. 

### Console<a name="USER_Tagging.ARN.CON"></a>

To get an ARN from the AWS Management Console, navigate to the resource you want an ARN for, and view the details for that resource\. For example, you can get the ARN for a DB instance from the **Configuration** tab of the DB instance details, as shown following\. 

![\[DB instance ARN\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/DB-instance-arn.png)

### AWS CLI<a name="USER_Tagging.ARN.CLI"></a>

To get an ARN from the AWS CLI for a particular RDS resource, you use the `describe` command for that resource\. The following table shows each AWS CLI command, and the ARN property used with the command to get an ARN\. 


****  
<a name="cli-command-arn-property"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_Tagging.ARN.html)

For example, the following AWS CLI command gets the ARN for a DB instance\.

**Example**  
For Linux, macOS, or Unix:  

```
aws rds describe-db-instances \
--db-instance-identifier DBInstanceIdentifier \
--region us-west-2 \
--query "*[].{DBInstanceIdentifier:DBInstanceIdentifier,DBInstanceArn:DBInstanceArn}"
```
For Windows:  

```
aws rds describe-db-instances ^
--db-instance-identifier DBInstanceIdentifier ^
--region us-west-2 ^
--query "*[].{DBInstanceIdentifier:DBInstanceIdentifier,DBInstanceArn:DBInstanceArn}"
```
The output of that command is like the following:  

```
[
    {
        "DBInstanceArn": "arn:aws:rds:us-west-2:account_id:db:instance_id", 
        "DBInstanceIdentifier": "instance_id"
    }
]
```

### RDS API<a name="USER_Tagging.ARN.API"></a>

To get an ARN for a particular RDS resource, you can call the following RDS API operations and use the ARN properties shown following\.


****  
<a name="rds-operation-arn-property"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_Tagging.ARN.html)