# Using RDS Proxy with AWS CloudFormation<a name="rds-proxy-cfn"></a>

 You can use RDS Proxy with AWS CloudFormation\. Doing so helps you to create groups of related resources\. Such a group can include a proxy that can connect to a newly created Amazon RDS DB instance or Aurora DB cluster\. RDS Proxy support in AWS CloudFormation involves two new registry types: `DBProxy` and `DBProxyTargetGroup`\. 

 The following listing shows a sample AWS CloudFormation template for RDS Proxy\. 

```
Resources:
 DBProxy:
   Type: AWS::RDS::DBProxy
   Properties:
     DBProxyName: CanaryProxy
     EngineFamily: MYSQL
     RoleArn:
      Fn::ImportValue: SecretReaderRoleArn
     Auth:
       - {AuthScheme: SECRETS, SecretArn: !ImportValue ProxySecret, IAMAuth: DISABLED}
     VpcSubnetIds:
       Fn::Split: [",", "Fn::ImportValue": SubnetIds]

 ProxyTargetGroup: 
   Type: AWS::RDS::DBProxyTargetGroup
   Properties:
     DBProxyName: CanaryProxy
     TargetGroupName: default
     DBInstanceIdentifiers: 
       - Fn::ImportValue: DBInstanceName
   DependsOn: DBProxy
```

 For more information about the Amazon RDS and Aurora resources that you can create using AWS CloudFormation, see [RDS resource type reference](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/AWS_RDS.html)\. 