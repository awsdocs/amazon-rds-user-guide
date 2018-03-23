# Working with Option Groups<a name="USER_WorkingWithOptionGroups"></a>

Some DB engines offer additional features that make it easier to manage data and databases, and to provide additional security for your database\. Amazon RDS uses option groups to enable and configure these features\. An *option group* can specify features, called options, that are available for a particular Amazon RDS DB instance\. Options can have settings that specify how the option works\. When you associate a DB instance with an option group, the specified options and option settings are enabled for that DB instance\. 

 Amazon RDS supports options for the following database engines: 


****  

| Database Engine | Relevant Documentation | 
| --- | --- | 
|  `MariaDB`  |  [Appendix: Options for MariaDB Database Engine](Appendix.MariaDB.Options.md)  | 
|  `Microsoft SQL Server`  |  [Options for the Microsoft SQL Server Database Engine](Appendix.SQLServer.Options.md)  | 
|  `MySQL`  |  [Options for MySQL DB Instances](Appendix.MySQL.Options.md)  | 
|  `Oracle`  |  [Options for Oracle DB Instances](Appendix.Oracle.Options.md)  | 

## Option Groups Overview<a name="Overview.OptionGroups"></a>

Amazon RDS provides an empty default option group for each new DB instance\. You cannot modify this default option group, but any new option group that you create derives its settings from the default option group\. To apply an option to a DB instance, you must do the following: 

1. Create a new option group, or copy or modify an existing option group\.

1. Add one or more options to the option group\.

1. Associate the option group with the DB instance\.

Both DB instances and DB snapshots can be associated with an option group\. When you restore from a DB snapshot or perform a point\-in\-time restore for a DB instance, the option group associated with the DB snapshot or DB instance will, by default, be associated with the restored DB instance\. You can associate a different option group with a restored DB instance\. However, the new option group must contain any persistent or permanent options that were included in the original option group\. Persistent and permanent options are described following\.

Options require additional memory to run on a DB instance, so you might need to launch a larger instance to use them, depending on your current use of your DB instance\. For example, Oracle Enterprise Manager Database Control uses about 300 MB of RAM; if you enable this option for a small DB instance, you might encounter performance problems or out\-of\-memory errors\.

Each DB instance indicates the status of its association with an option group\. For example, a status of **active** indicates the DB instance is associated with that option group, and a status of **invalid** indicates that the option group associated with the DB instance does not contain the options the DB instance requires\. If you query a DB instance for the status of its associated option group, Amazon RDS can also return a status such as **pending** or **applying** when it is attempting to change the association from one state to another\. For example, the status of the association of a DB instance in an option group can be **creating/pending**\. 

### Persistent and Permanent Options<a name="Overview.OptionGroups.Permanent"></a>

Two types of options, persistent and permanent, require special consideration when you add them to an option group\. 

Persistent options, such as the TDE option for Microsoft SQL Server transparent data encryption \(TDE\), cannot be removed from an option group while DB instances are associated with the option group\. You must disassociate all DB instances from the option group before a persistent option can be removed from the option group\. When you restore or perform a point\-in\-time restore from a DB snapshot, if the option group associated with that DB snapshot contains a persistent option, you can only associate the restored DB instance with that option group\. 

Permanent options, such as the TDE option for Oracle Advanced Security TDE, can never be removed from an option group, and the option group cannot be disassociated from the DB instance\. When you restore or perform a point\-in\-time restore from a DB snapshot, if the option group associated with that DB snapshot contains a permanent option, you can only associate the restored DB instance with an option group with that permanent option\. 

### VPC and Platform Considerations<a name="Overview.OptionGroups.Platform"></a>

When an option group is assigned to a DB instance, it is linked to the platform that the DB instance is on\. That platform can either be a VPC supported by the Amazon Virtual Private Cloud \(Amazon VPC\) service, or EC2\-Classic \(non\-VPC\) supported by the Amazon Elastic Compute Cloud \(Amazon EC2\) service\. For details on these two platforms, see [Amazon EC2 and Amazon Virtual Private Cloud](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-vpc.html)\. 

If a DB instance is in a VPC, the option group associated with the instance is linked to that VPC\. This means that you cannot use the option group assigned to a DB instance if you attempt to restore the instance into a different VPC or onto a different platform\. If you restore a DB instance into a different VPC or onto a different platform, you must either assign the default option group to the DB instance, assign an option group that is linked to that VPC or platform, or create a new option group and assign it to the DB instance\. Note that with persistent or permanent options, such as Oracle TDE, you must create a new option group that includes the persistent or permanent option when restoring a DB instance into a different VPC\.

Option settings control the behavior of an option\. For example, the Oracle Advanced Security option `NATIVE_NETWORK_ENCRYPTION` has a setting that you can use to specify the encryption algorithm for network traffic to and from the DB instance\. Some options settings are optimized for use with Amazon RDS and cannot be changed\.

### Mutually Exclusive Options<a name="Overview.OptionGroups.Exclusive"></a>

Some options are mutually exclusive\. You can use one or the other, but not both at the same time\. The following options are mutually exclusive: 
+ [Oracle Enterprise Manager Database Express](Appendix.Oracle.Options.OEM_DBControl.md) and [Oracle Management Agent for Enterprise Manager Cloud Control](Oracle.Options.OEMAgent.md)\. 
+ [Oracle Native Network Encryption](Appendix.Oracle.Options.NetworkEncryption.md) and [Oracle SSL](Appendix.Oracle.Options.SSL.md)\. 
+ [Oracle Transparent Data Encryption](Appendix.Oracle.Options.AdvSecurity.md) and [Using AWS CloudHSM Classic to Store Amazon RDS Oracle TDE Keys](Appendix.OracleCloudHSM.md)\. 

## Creating an Option Group<a name="USER_WorkingWithOptionGroups.Create"></a>

You can create a new option group that derives its settings from the default option group, and then add one or more options to the new option group\. Alternatively, if you already have an existing option group, you can copy that option group with all of its options to a new option group\. For more information, see [Making a Copy of an Option Group](#USER_WorkingWithOptionGroups.Copy)\. 

After you create a new option group, it has no options\. To learn how to add options to the option group, see [Adding an Option to an Option Group](#USER_WorkingWithOptionGroups.AddOption)\. After you have added the options you want, you can then associate the option group with a DB instance so that the options become available on the DB instance\. For information about associating an option group with a DB instance, see the documentation for your specific engine listed at [Working with Option Groups](#USER_WorkingWithOptionGroups)\. 

### AWS Management Console<a name="USER_WorkingWithOptionGroups.Create.Console"></a>

 One way of creating an option group is by using the AWS Management Console\. 

**To create a new option group by using the console**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Option groups**\.

1. Choose **Create group**\.

1. In the **Create option group** window, do the following:

   1. For **Name**, type a name for the option group that is unique within your AWS account\. The name can contain only letters, digits, and hyphens\. 

   1. For **Description**, type a brief description of the option group\. The description is used for display purposes\. 

   1. For **Engine**, choose the DB engine that you want\. 

   1. For **Major engine version**, choose the major version of the DB engine that you want\. 

1. To continue, choose **Create**\. To cancel the operation instead, choose **Cancel**\. 

### CLI<a name="USER_WorkingWithOptionGroups.Create.CLI"></a>

To create an option group, use the AWS CLI [http://docs.aws.amazon.com/cli/latest/reference/rds/create-option-group.html](http://docs.aws.amazon.com/cli/latest/reference/rds/create-option-group.html) command with the following required parameters\.
+ `--option-group-name`
+ `--engine-name`
+ `--major-engine-version`
+ `--option-group-description`

**Example**  
The following example creates an option group named `TestOptionGroup`, which is associated with the Oracle Enterprise Edition DB engine\. The description is enclosed in quotation marks\.  
For Linux, OS X, or Unix:  

```
aws rds create-option-group \
    --option-group-name testoptiongroup \
    -–engine-name oracle-ee \
    -–major-engine-version 11.2 \
    --option-group-description "Test option group"
```
For Windows:  

```
aws rds create-option-group ^
    --option-group-name testoptiongroup ^
    -–engine-name oracle-ee ^
    -–major-engine-version 11.2 ^
    --option-group-description "Test option group"
```

### API<a name="USER_WorkingWithOptionGroups.Create.API"></a>

To create an option group, call the Amazon RDS API [http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateOptionGroup.html](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateOptionGroup.html) action\. Include the following parameters:
+ `OptionGroupName = testoptiongroup`
+ `EngineName = oracle-ee`
+ `MajorEngineVersion = 11.2`
+ `OptionGroupDescription = Test%20option%20group`

**Example**  

```
https://rds.us-east-1.amazonaws.com/
   ?Action=CreateOptionGroup
   &EngineName=oracle-ee
   &MajorEngineVersion=11.2
   &OptionGroupDescription=test%20option%20group
   &OptionGroupName=testoptiongroup
   &SignatureMethod=HmacSHA256
   &SignatureVersion=4
   &Version=2014-09-01
   &X-Amz-Algorithm=AWS4-HMAC-SHA256
   &X-Amz-Credential=AKIADQKE4SARGYLE/20140425/us-east-1/rds/aws4_request
   &X-Amz-Date=20140425T174519Z
   &X-Amz-SignedHeaders=content-type;host;user-agent;x-amz-content-sha256;x-amz-date
   &X-Amz-Signature=d3a89afa4511d0c4ecab046d6dc760a72bfe6bb15999cce053adeb2617b60384
```

## Making a Copy of an Option Group<a name="USER_WorkingWithOptionGroups.Copy"></a>

You can use the AWS CLI or the Amazon RDS API to make a copy of an option group\. Copying an option group is a convenient solution when you have already created an option group and you want to include most of the custom parameters and values from that group in a new option group\. You can also make a copy of an option group that you use in production and then modify the copy to test other option settings\.

### CLI<a name="USER_WorkingWithOptionGroups.Copy.CLI"></a>

To copy an option group, use the AWS CLI [copy\-option\-group](http://docs.aws.amazon.com/cli/latest/reference/rds/copy-option-group.html) command\. Include the following required parameters:
+ `--source-option-group-identifier`
+ `--target-option-group-identifier`
+ `--target-option-group-description`

**Example**  
The following example creates an option group named `new-local-option-group`, which is a local copy of the option group `my-remote-option-group`\.  
For Linux, OS X, or Unix:  

```
aws rds copy-option-group \
    --source-option-group-identifier arn:aws:rds:us-west-2:123456789012:og:my-remote-option-group \
    --target-option-group-identifier new-local-option-group \
    --target-option-group-description "Option group 2"
```
For Windows:  

```
aws rds copy-option-group ^
    --source-option-group-identifier arn:aws:rds:us-west-2:123456789012:og:my-remote-option-group ^
    --target-option-group-identifier new-local-option-group ^
    --target-option-group-description "Option group 2"
```

### API<a name="USER_WorkingWithOptionGroups.Copy.API"></a>

To copy an option group, call the Amazon RDS API [CopyOptionGroup](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CopyOptionGroup.html) action\. Include the following required parameters\.
+ `SourceOptionGroupIdentifier = arn%3Aaws%3Ards%3Aus-west-2%3A123456789012%3og%3Amy-remote-option-group`
+ `TargetOptionGroupIdentifier = new-local-option-group`
+ `TargetOptionGroupDescription = Option%20group%202`

**Example**  
The following example creates an option group named `new-local-option-group`, which is a local copy of the option group `my-remote-option-group`\.  

```
 1. https://rds.us-east-1.amazonaws.com/
 2.    ?Action=CopyOptionGroup
 3.    &SignatureMethod=HmacSHA256
 4.    &SignatureVersion=4
 5.    &SourceOptionGroupIdentifier=arn%3Aaws%3Ards%3Aus-west-2%3A123456789012%3og%3Amy-remote-option-group
 6.    &TargetOptionGroupDescription=New%20option%20group
 7.    &TargetOptionGroupIdentifier=new-local-option-group
 8.    &Version=2014-09-01
 9.    &X-Amz-Algorithm=AWS4-HMAC-SHA256
10.    &X-Amz-Credential=AKIADQKE4SARGYLE/20140429/us-east-1/rds/aws4_request
11.    &X-Amz-Date=20140429T175351Z
12.    &X-Amz-SignedHeaders=content-type;host;user-agent;x-amz-content-sha256;x-amz-date
13.    &X-Amz-Signature=9164337efa99caf850e874a1cb7ef62f3cea29d0b448b9e0e7c53b288ddffed2
```

## Adding an Option to an Option Group<a name="USER_WorkingWithOptionGroups.AddOption"></a>

You can add an option to an existing option group\. After you have added the options you want, you can then associate the option group with a DB instance so that the options become available on the DB instance\. For information about associating an option group with a DB instance, see the documentation for your specific DB engine listed at [Working with Option Groups](#USER_WorkingWithOptionGroups)\. 

Option group changes must be applied immediately in two cases: 
+ When you add an option that adds or updates a port value, such as the `OEM` option\. 
+ When you add or remove an option group with an option that includes a port value\. 

In these cases, you must select the **Apply Immediately** option in the console, or include the `Apply-Immediately` option when using the AWS CLI or set the `Apply-Immediately` parameter to `true` when using the Amazon RDS API\. Options that don't include port values can be applied immediately, or can be applied during the next maintenance window for the DB instance\. 

### AWS Management Console<a name="USER_WorkingWithOptionGroups.AddOption.Console"></a>

You can use the AWS Management Console to add an option to an option group\. 

**To add an option to an option group by using the console**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Option groups**\.

1. Select the option group that you want to modify, and then choose **Add 0ption**\.   
![\[Console option group\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/optiongroup-addoption1.png)

1. In the **Add option** window, do the following: 

   1. Choose the option that you want to add\. You might need to provide additional values, depending on the option that you select\. For example, when you choose the `OEM` option, you must also type a port value and specify a DB security group\.

   1. To enable the option on all associated DB instances as soon as you add it, for **Apply Immediately**, choose **Yes**\. If you choose **No** \(the default\), the option is enabled for each associated DB instance during its next maintenance window\.   
![\[Console option group\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/optiongroup-addoption2.png)

1. When the settings are as you want them, choose **Add Option**\. 

### CLI<a name="USER_WorkingWithOptionGroups.AddOptions.CLI"></a>

To add an option to an option group, run the AWS CLI [add\-option\-to\-option\-group](http://docs.aws.amazon.com/cli/latest/reference/rds/add-option-to-option-group.html) command with the option that you want to add\. To enable the new option immediately on all associated DB instances, include the `--apply-immediately` parameter\. By default, the option is enabled for each associated DB instance during its next maintenance window\. Include the following required parameter:
+ `--option-group-name`

**Example**  
The following example adds the Oracle Enterprise Manager Database Control \(OEM\) option to an option group named `TestOptionGroup` and immediately enables it\. Note that even if you use the default security group, you must specify that security group\.   
For Linux, OS X, or Unix:  

```
aws rds add-option-to-option-group \
--option-group-name TestOptionGroup \
-–option-name OEM \
--security-groups default \
--apply-immediately
```
For Windows:  

```
aws rds add-option-to-option-group \
--option-group-name TestOptionGroup \
-–option-name OEM \
--security-groups default \
--apply-immediately
```
Command output is similar to the following:  

```
OPTIONGROUP testoptiongroup oracle-ee 11.2 Test option group
    OPTION OEM 1158 Oracle Enterprise Manager
        SECGROUP default authorized
```

**Example**  
The following example adds the Oracle OEM option to an option group, specifies a custom port, and specifies a pair of Amazon EC2 VPC security groups to use for that port\.  
For Linux, OS X, or Unix:  

```
aws rds add-option-to-option-group \
    --option-group-name my-option-group \
    --option-name OEM \
    --port 5432 \
    --vpcsg sg-454fa22a,sg-5da54932
```
For Windows:  

```
aws rds add-option-to-option-group ^
    --option-group-name my-option-group ^
    --option-name OEM ^
    --port 5432 ^
    --vpcsg sg-454fa22a,sg-5da54932
```
Command output is similar to the following:  

```
OPTIONGROUP  my-option-group  oracle-se  11.2  My option group
OPTION  OEM  n  5432  Oracle Enterprise Manager
VPCSECGROUP  sg-454fa22a  active
VPCSECGROUP  sg-5da54932  active
```

**Example**  
The following example adds the Oracle option NATIVE\_NETWORK\_ENCRYPTION to an option group and specifies the option settings\. If no option settings are specified, default values are used\.  
For Linux, OS X, or Unix:  

```
aws rds add-option-to-option-group \
    --option-group-name my-option-group \
    --options NATIVE_NETWORK_ENCRYPTION \
    --settings "SQLNET.ENCRYPTION_SERVER=REQUIRED; SQLNET.ENCRYPTION_TYPES_SERVER=AES256,AES192,DES"
```
For Windows:  

```
aws rds add-option-to-option-group ^
    --option-group-name my-option-group ^
    --options NATIVE_NETWORK_ENCRYPTION ^
    --settings "SQLNET.ENCRYPTION_SERVER=REQUIRED; SQLNET.ENCRYPTION_TYPES_SERVER=AES256,AES192,DES"
```
Command output is similar to the following:  

```
OPTIONGROUP  Group Name       Engine     Major Engine Version  Description      VpcSpecific
OPTIONGROUP  my-option-group  oracle-ee  11.2                  My option group  n          
    OPTION  Name                        Persistent Permanent Description
    OPTION  NATIVE_NETWORK_ENCRYPTION   n          n         Oracle Advanced Security - Native Network Encryption
      OPTIONSETTING  Name                                 Description                                                         Value              Modifiable
      OPTIONSETTING  SQLNET.CRYPTO_CHECKSUM_TYPES_SERVER  Specifies list of checksumming algorithms in order of intended use  SHA1,MD5           true
      OPTIONSETTING  SQLNET.ENCRYPTION_TYPES_SERVER       Specifies list of encryption algorithms in order of intended use    AES256,AES192,DES  true
      OPTIONSETTING  SQLNET.ENCRYPTION_SERVER             Specifies the desired encryption behavior                           REQUIRED           true
      OPTIONSETTING  SQLNET.CRYPTO_CHECKSUM_SERVER        Specifies the desired data integrity behavior                       REQUESTED          true
```

### API<a name="USER_WorkingWithOptionGroups.AddOptions.API"></a>

To add an option to an option group using the Amazon RDS API, call the [ModifyOptionGroup](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyOptionGroup.html) action with the option that you want to add\. To enable the new option immediately on all associated DB instances, include the `ApplyImmediately` parameter and set it to `true`\. By default, the option is enabled for each associated DB instance during its next maintenance window\. Include the following required parameter:
+ `OptionGroupName`

**Example**  

```
https://rds.us-east-1.amazonaws.com/
    ?Action=ModifyOptionGroup
    &ApplyImmediately=true
    &OptionGroupName=myawsuser-og02
    &OptionsToInclude.member.1.DBSecurityGroupMemberships.member.1=default
    &OptionsToInclude.member.1.OptionName=MEMCACHED
    &SignatureMethod=HmacSHA256
    &SignatureVersion=4
    &Version=2014-09-01
    &X-Amz-Algorithm=AWS4-HMAC-SHA256
    &X-Amz-Credential=AKIADQKE4SARGYLE/20140501/us-east-1/rds/aws4_request
    &X-Amz-Date=20140501T230529Z
    &X-Amz-SignedHeaders=content-type;host;user-agent;x-amz-content-sha256;x-amz-date
    &X-Amz-Signature=4b278baae6294738704a9948e355af0e9bd4fa0913d5b35b0a9a3c916925aced
```

## Listing the Options and Option Settings for an Option Group<a name="USER_WorkingWithOptionGroups.ListOption"></a>

 You can list all the options and option settings for an option group\. 

### AWS Management Console<a name="USER_WorkingWithOptionGroups.ListOption.Console"></a>

You can use the AWS Management Console to list all of the options and option settings for an option group\. 

**To list the options and option settings for an option group**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Option groups**\. The **Options** column in the table shows the options and option settings in the option group\.

### CLI<a name="USER_WorkingWithOptionGroups.ListOption.CLI"></a>

To list the options and option settings for an option group, use the AWS CLI [http://docs.aws.amazon.com/cli/latest/reference/rds/describe-option-groups.html](http://docs.aws.amazon.com/cli/latest/reference/rds/describe-option-groups.html) command\. Specify the name of the option group whose options and settings you want to view\. If you don't specify an option group name, all option groups are described\. 

**Example**  
The following example lists the options and option settings for all option groups\.   

```
aws rds describe-option-groups
```

**Example**  
The following example lists the options and option settings for an option group named `TestOptionGroup`\.   

```
aws rds describe-option-groups --option-group-name TestOptionGroup
```

### API<a name="USER_WorkingWithOptionGroups.ListOption.API"></a>

To list the options and option settings for an option group, use the Amazon RDS API [http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeOptionGroups.html](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeOptionGroups.html) action\. Specify the name of the option group whose options and settings you want to view\. If you don't specify an option group name, all option groups are described\. 

**Example**  
The following example lists the options and option settings for all option groups\.   

```
https://rds.us-west-2.amazonaws.com/
    ?Action=DescribeOptionGroups
    &MaxRecords=100
    &SignatureMethod=HmacSHA256
    &SignatureVersion=4
    &Version=2014-09-01
    &X-Amz-Algorithm=AWS4-HMAC-SHA256
    &X-Amz-Credential=AKIADQKE4SARGYLE/20140613/us-west-2/rds/aws4_request
    &X-Amz-Date=20140613T223341Z
    &X-Amz-SignedHeaders=content-type;host;user-agent;x-amz-content-sha256;x-amz-date
    &X-Amz-Signature=5ae331adcd684c27d66e0b794a51933effe32a4c026eba2e994ae483ee47a0ba
```
The output from the preceding action is similar to the following:  

```
 1. <DescribeOptionGroupsResponse xmlns="http://rds.amazonaws.com/doc/2014-10-31/">
 2.   <DescribeOptionGroupsResult>
 3.     <OptionGroupsList>
 4.       <OptionGroup>
 5.         <OptionGroupName>default:mysql-5-5</OptionGroupName>
 6.         <AllowsVpcAndNonVpcInstanceMemberships>true</AllowsVpcAndNonVpcInstanceMemberships>
 7.         <MajorEngineVersion>5.5</MajorEngineVersion>
 8.         <EngineName>mysql</EngineName>
 9.         <OptionGroupDescription>Default option group for mysql 5.5</OptionGroupDescription>
10.         <Options/>
11.       </OptionGroup>
12.       
13.       <!-- some output omitted for brevity -->
14.       
15.       <OptionGroup>
16.         <OptionGroupName>default:postgres-9-3</OptionGroupName>
17.         <AllowsVpcAndNonVpcInstanceMemberships>true</AllowsVpcAndNonVpcInstanceMemberships>
18.         <MajorEngineVersion>9.3</MajorEngineVersion>
19.         <EngineName>postgres</EngineName>
20.         <OptionGroupDescription>Default option group for postgres 9.3</OptionGroupDescription>
21.         <Options/>
22.       </OptionGroup>
23.     </OptionGroupsList>
24.   </DescribeOptionGroupsResult>
25.   <ResponseMetadata>
26.     <RequestId>b2ce0772-f55a-11e3-bd0f-bb88ac05a37c</RequestId>
27.   </ResponseMetadata>
28. </DescribeOptionGroupsResponse>
```

**Example**  
The following example lists the options and option settings for an option group named `myawsuser-grp1`\.   

```
 1. https://rds.us-east-1.amazonaws.com/
 2.    ?Action=DescribeOptionGroups
 3.    &MaxRecords=100
 4.    &OptionGroupName=myawsuser-grp1
 5.    &SignatureMethod=HmacSHA256
 6.    &SignatureVersion=4
 7.    &Version=2014-09-01
 8.    &X-Amz-Algorithm=AWS4-HMAC-SHA256
 9.    &X-Amz-Credential=AKIADQKE4SARGYLE/20140421/us-east-1/rds/aws4_request
10.    &X-Amz-Date=20140421T231357Z
11.    &X-Amz-SignedHeaders=content-type;host;user-agent;x-amz-content-sha256;x-amz-date
12.    &X-Amz-Signature=fabfbeb85c44e3f151d44211790c5135a9074fdb8d85ec117788ac6cfab6c5bc
```
The output from the preceding action is similar to the following:  

```
 1. <DescribeOptionGroupsResponse xmlns="http://rds.amazonaws.com/doc/2014-10-31/">
 2.   <DescribeOptionGroupsResult>
 3.     <OptionGroupsList>
 4.       <OptionGroup>
 5.         <AllowsVpcAndNonVpcInstanceMemberships>true</AllowsVpcAndNonVpcInstanceMemberships>
 6.         <MajorEngineVersion>5.6</MajorEngineVersion>
 7.         <OptionGroupName>myawsuser-grp1</OptionGroupName>
 8.         <EngineName>mysql</EngineName>
 9.         <OptionGroupDescription>my test option group</OptionGroupDescription>
10.         <Options/>
11.       </OptionGroup>
12.     </OptionGroupsList>
13.   </DescribeOptionGroupsResult>
14.   <ResponseMetadata>
15.     <RequestId>8c6201fc-b9ff-11d3-f92b-31fa5e8dbc99</RequestId>
16.   </ResponseMetadata>
17. </DescribeOptionGroupsResponse>
```

## Modifying an Option Setting<a name="USER_WorkingWithOptionGroups.ModifyOption"></a>

After you have added an option that has modifiable option settings, you can modify the settings at any time\. If you change options or option settings in an option group, those changes are applied to all DB instances that are associated with that option group\. For more information on what settings are available for the various options, see the documentation for your specific engine listed at [Working with Option Groups](#USER_WorkingWithOptionGroups)\. 

Option group changes must be applied immediately in two cases: 
+ When you add an option that adds or updates a port value, such as the `OEM` option\. 
+ When you add or remove an option group with an option that includes a port value\. 

In these cases, you must select the **Apply Immediately** option in the console, or include the `Apply-Immediately` option when using the AWS CLI or set the `Apply-Immediately` parameter to `true` when using the Amazon RDS API\. Options that don't include port values can be applied immediately, or can be applied during the next maintenance window for the DB instance\. 

### AWS Management Console<a name="USER_WorkingWithOptionGroups.ModifyOption.Console"></a>

You can use the AWS Management Console to modify an option setting\. 

**To modify an option setting by using the console**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Option groups**\. 

1. Select the option group whose option that you want to modify, and then choose **Modify option**\. 

1. In the **Modify option** window, from **Installed Options**, choose the option whose setting you want to modify\. Make the changes that you want\.

1. To enable the option as soon as you add it, for **Apply Immediately**, choose **Yes**\. If you choose **No** \(the default\), the option is enabled for each associated DB instance during its next maintenance window\. 

1. When the settings are as you want them, choose **Modify Option**\.

### CLI<a name="USER_WorkingWithOptionGroups.ModifyOption.CLI"></a>

To modify an option setting, use the AWS CLI [http://docs.aws.amazon.com/cli/latest/reference/rds/add-option-to-option-group.html](http://docs.aws.amazon.com/cli/latest/reference/rds/add-option-to-option-group.html) command with the option group and option that you want to modify\. By default, the option is enabled for each associated DB instance during its next maintenance window\. To apply the change immediately to all associated DB instances, include the `--apply-immediately` parameter\. To modify an option setting, use the `--settings` argument\.

**Example**  
The following example modifies the port that the Oracle Enterprise Manager Database Control \(OEM\) uses in an option group named `TestOptionGroup` and immediately applies the change\.   
For Linux, OS X, or Unix:  

```
aws rds add-option-to-option-group \
    --option-group-name TestOptionGroup \
    -–option-name OEM \
    --port 5432 \
    --apply-immediately
```
For Windows:  

```
aws rds add-option-to-option-group ^
    --option-group-name TestOptionGroup ^
    -–option-name OEM ^
    --port 5432 ^
    --apply-immediately
```
Command output is similar to the following:  

```
OPTIONGROUP testoptiongroup oracle-ee 11.2 Test Option Group
    OPTION OEM 5432 Oracle Enterprise Manager
        SECGROUP default authorized
```

**Example**  
The following example modifies the Oracle option NATIVE\_NETWORK\_ENCRYPTION and changes the option settings\.   
For Linux, OS X, or Unix:  

```
aws rds add-option-to-option-group \
    --option-group-name my-option-group \
    --option-name NATIVE_NETWORK_ENCRYPTION \
    --settings "SQLNET.ENCRYPTION_SERVER=REQUIRED; SQLNET.ENCRYPTION_TYPES_SERVER=AES256,AES192,DES"
```
For Windows:  

```
aws rds add-option-to-option-group ^
    --option-group-name my-option-group ^
    --option-name NATIVE_NETWORK_ENCRYPTION ^
    --settings "SQLNET.ENCRYPTION_SERVER=REQUIRED; SQLNET.ENCRYPTION_TYPES_SERVER=AES256,AES192,DES"
```
Command output is similar to the following:  

```
OPTIONGROUP  Group Name       Engine     Major Engine Version  Description      VpcSpecific
OPTIONGROUP  my-option-group  oracle-ee  11.2                  My option group  n          
    OPTION  Name                        Persistent Permanent Description
    OPTION  NATIVE_NETWORK_ENCRYPTION   n          n         Oracle Advanced Security - Native Network Encryption
      OPTIONSETTING  Name                                 Description                                                         Value              Modifiable
      OPTIONSETTING  SQLNET.CRYPTO_CHECKSUM_TYPES_SERVER  Specifies list of checksumming algorithms in order of intended use  SHA1,MD5           true
      OPTIONSETTING  SQLNET.ENCRYPTION_TYPES_SERVER       Specifies list of encryption algorithms in order of intended use    AES256,AES192,DES  true
      OPTIONSETTING  SQLNET.ENCRYPTION_SERVER             Specifies the desired encryption behavior                           REQUIRED           true
      OPTIONSETTING  SQLNET.CRYPTO_CHECKSUM_SERVER        Specifies the desired data integrity behavior                       REQUESTED          true
```

### API<a name="USER_WorkingWithOptionGroups.ModifyOption.API"></a>

To modify an option setting, use the Amazon RDS API [http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyOptionGroup.html](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyOptionGroup.html) command with the option group and option that you want to modify\. By default, the option is enabled for each associated DB instance during its next maintenance window\. To apply the change immediately to all associated DB instances, include the `ApplyImmediately` parameter and set it to `true`\.

**Example**  

```
 1. https://rds.us-east-1.amazonaws.com/
 2.     ?Action=ModifyOptionGroup
 3.     &ApplyImmediately=true
 4.     &OptionGroupName=myawsuser-og02
 5.     &OptionsToInclude.member.1.DBSecurityGroupMemberships.member.1=default
 6.     &OptionsToInclude.member.1.OptionName=MEMCACHED
 7.     &SignatureMethod=HmacSHA256
 8.     &SignatureVersion=4
 9.     &Version=2014-09-01
10.     &X-Amz-Algorithm=AWS4-HMAC-SHA256
11.     &X-Amz-Credential=AKIADQKE4SARGYLE/20140501/us-east-1/rds/aws4_request
12.     &X-Amz-Date=20140501T230529Z
13.     &X-Amz-SignedHeaders=content-type;host;user-agent;x-amz-content-sha256;x-amz-date
14.     &X-Amz-Signature=4b278baae6294738704a9948e355af0e9bd4fa0913d5b35b0a9a3c916925aced
```
Output from the preceding action should look similar to the following:  

```
 1. <ModifyOptionGroupResponse xmlns="http://rds.amazonaws.com/doc/2014-10-31/">
 2.   <ModifyOptionGroupResult>
 3.     <OptionGroup>
 4.       <OptionGroupName>myawsuser-og02</OptionGroupName>
 5.       <MajorEngineVersion>5.6</MajorEngineVersion>
 6.       <AllowsVpcAndNonVpcInstanceMemberships>false</AllowsVpcAndNonVpcInstanceMemberships>
 7.       <EngineName>mysql</EngineName>
 8.       <OptionGroupDescription>my second og</OptionGroupDescription>
 9.       <Options>
10.         <Option>
11.           <Port>11211</Port>
12.           <OptionName>MEMCACHED</OptionName>
13.           <OptionDescription>Innodb Memcached for MySQL</OptionDescription>
14.           <Persistent>false</Persistent>
15.           <OptionSettings>
16.             <OptionSetting>
17.               <DataType>BOOLEAN</DataType>
18.               <IsModifiable>true</IsModifiable>
19.               <IsCollection>false</IsCollection>
20.               <Description>If enabled when there is no more memory to store items, memcached will return an error rather than evicting items.</Description>
21.               <Name>ERROR_ON_MEMORY_EXHAUSTED</Name>
22.               <Value>0</Value>
23.               <ApplyType>STATIC</ApplyType>
24.               <AllowedValues>0,1</AllowedValues>
25.               <DefaultValue>0</DefaultValue>
26.             </OptionSetting>
27.             <OptionSetting>
28.               <DataType>INTEGER</DataType>
29.               <IsModifiable>true</IsModifiable>
30.               <IsCollection>false</IsCollection>
31.               <Description>The backlog queue configures how many network connections can be waiting to be processed by memcached</Description>
32.               <Name>BACKLOG_QUEUE_LIMIT</Name>
33.               <Value>1024</Value>
34.               <ApplyType>STATIC</ApplyType>
35.               <AllowedValues>1-2048</AllowedValues>
36.               <DefaultValue>1024</DefaultValue>
37.             </OptionSetting>
38.           </OptionSettings>
39.           <VpcSecurityGroupMemberships/>
40.           <Permanent>false</Permanent>
41.           <DBSecurityGroupMemberships>
42.             <DBSecurityGroup>
43.               <Status>authorized</Status>
44.               <DBSecurityGroupName>default</DBSecurityGroupName>
45.             </DBSecurityGroup>
46.           </DBSecurityGroupMemberships>
47.         </Option>
48.       </Options>
49.     </OptionGroup>
50.   </ModifyOptionGroupResult>
51.   <ResponseMetadata>
52.     <RequestId>073cfb45-c184-11d3-a537-cef97546330c</RequestId>
53.   </ResponseMetadata>
54. </ModifyOptionGroupResponse>
```

## Removing an Option from an Option Group<a name="USER_WorkingWithOptionGroups.RemoveOption"></a>

Some options can be removed from an option group, and some cannot\. A persistent option cannot be removed from an option group until all DB instances associated with that option group are disassociated\. A permanent option can never be removed from an option group\. For more information about what options are removable, see the documentation for your specific engine listed at [Working with Option Groups](#USER_WorkingWithOptionGroups)\. 

If you remove all options from an option group, Amazon RDS doesn't delete the option group\. DB instances that are associated with the empty option group continue to be associated with it; they just won’t have any active options\. Alternatively, to remove all options from a DB instance, you can associate the DB instance with the default \(empty\) option group\. 

### AWS Management Console<a name="USER_WorkingWithOptionGroups.RemoveOption.Console"></a>

You can use the AWS Management Console to remove an option from an option group\. 

**To remove an option from an option group by using the console**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Option groups**\. 

1. Select the option group whose option you want to remove, and then choose **Delete option**\. 

1. In the **Delete option** window, do the following: 
   +  Select the check box for the option that you want to delete\. 
   + For the deletion to take effect as soon as you make it, for **Apply immediately**, choose **Yes**\. If you choose **No** \(the default\), the option is deleted for each associated DB instance during its next maintenance window\.   
![\[Delete option group\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/optiongroup-removeoption2.png)

1. When the settings are as you want them, choose **Yes, Delete**\. 

### CLI<a name="USER_WorkingWithOptionGroups.RemoveOption.CLI"></a>

To remove an option from an option group, use the AWS CLI [ `remove-option-from-option-group`](http://docs.aws.amazon.com/cli/latest/reference/rds/remove-option-from-option-group.html) command with the option that you want to delete\. By default, the option is removed from each associated DB instance during its next maintenance window\. To apply the change immediately, include the `--apply-immediately` parameter\. 

**Example**  
The following example removes the Oracle Enterprise Manager Database Control \(OEM\) option from an option group named `TestOptionGroup` and immediately applies the change\.   
For Linux, OS X, or Unix:  

```
aws rds remove-option-from-option-group \
    --option-group-name TestOptionGroup \
    -–options OEM \
    --apply-immediately
```
For Windows:  

```
aws rds remove-option-from-option-group ^
    --option-group-name TestOptionGroup ^
    -–options OEM ^
    --apply-immediately
```
Command output is similar to the following:  

```
OPTIONGROUP    testoptiongroup oracle-ee   11.2    Test option group
```

### API<a name="USER_WorkingWithOptionGroups.RemoveOption.API"></a>

To remove an option from an option group, use the Amazon RDS API [ `ModifyOptionGroup`](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyOptionGroup.html) action\. By default, the option is removed from each associated DB instance during its next maintenance window\. To apply the change immediately, include the `ApplyImmediately` parameter and set it to `true`\. 

Include the following parameters: 
+ `OptionGroupName = myawsuser-og02`
+ `OptionsToRemove.OptionName = OEM`

**Example**  
The following example removes the Oracle Enterprise Manager Database Control \(OEM\) option from an option group named `TestOptionGroup` and immediately applies the change\.   

```
 1. https://rds.us-east-1.amazonaws.com/
 2.     ?Action=ModifyOptionGroup
 3.     &ApplyImmediately=true
 4.     &OptionGroupName=myawsuser-og02
 5.     &OptionsToRemove.OptionName=OEM
 6.     &SignatureMethod=HmacSHA256
 7.     &SignatureVersion=4
 8.     &Version=2014-09-01
 9.     &X-Amz-Algorithm=AWS4-HMAC-SHA256
10.     &X-Amz-Credential=AKIADQKE4SARGYLE/20140501/us-east-1/rds/aws4_request
11.     &X-Amz-Date=20140501T231731Z
12.     &X-Amz-SignedHeaders=content-type;host;user-agent;x-amz-content-sha256;x-amz-date
13.     &X-Amz-Signature=fd7ee924d39f1014488eb3444a8fdfb028e958b97703f95845a5addc435c1399
```
The output from the preceding command should look something like the following:  

```
 1. <ModifyOptionGroupResponse xmlns="http://rds.amazonaws.com/doc/2014-10-31/">
 2.   <ModifyOptionGroupResult>
 3.     <OptionGroup>
 4.       <OptionGroupName>myawsuser-og02</OptionGroupName>
 5.       <AllowsVpcAndNonVpcInstanceMemberships>true</AllowsVpcAndNonVpcInstanceMemberships>
 6.       <MajorEngineVersion>5.6</MajorEngineVersion>
 7.       <EngineName>mysql</EngineName>
 8.       <OptionGroupDescription>my second og</OptionGroupDescription>
 9.       <Options/>
10.     </OptionGroup>
11.   </ModifyOptionGroupResult>
12.   <ResponseMetadata>
13.     <RequestId>b5f134f3-c185-11d3-f4c6-37db295f7674</RequestId>
14.   </ResponseMetadata>
15. </ModifyOptionGroupResponse>
```