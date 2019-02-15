# Working with Option Groups<a name="USER_WorkingWithOptionGroups"></a>

Some DB engines offer additional features that make it easier to manage data and databases, and to provide additional security for your database\. Amazon RDS uses option groups to enable and configure these features\. An *option group* can specify features, called options, that are available for a particular Amazon RDS DB instance\. Options can have settings that specify how the option works\. When you associate a DB instance with an option group, the specified options and option settings are enabled for that DB instance\. 

 Amazon RDS supports options for the following database engines: 


****  

| Database Engine | Relevant Documentation | 
| --- | --- | 
|  `MariaDB`  |  [Options for MariaDB Database Engine](Appendix.MariaDB.Options.md)  | 
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

### Persistent and Permanent Options<a name="Overview.OptionGroups.Permanent"></a>

Two types of options, persistent and permanent, require special consideration when you add them to an option group\. 

Persistent options, such as the TDE option for Microsoft SQL Server transparent data encryption \(TDE\), cannot be removed from an option group while DB instances are associated with the option group\. You must disassociate all DB instances from the option group before a persistent option can be removed from the option group\. When you restore or perform a point\-in\-time restore from a DB snapshot, if the option group associated with that DB snapshot contains a persistent option, you can only associate the restored DB instance with that option group\. 

Permanent options, such as the TDE option for Oracle Advanced Security TDE, can never be removed from an option group\. You can change the option group of a DB instance that is using the permanent option, but the option group associated with the DB instance must include the same permanent option\. When you restore or perform a point\-in\-time restore from a DB snapshot, if the option group associated with that DB snapshot contains a permanent option, you can only associate the restored DB instance with an option group with that permanent option\.

### VPC and Platform Considerations<a name="Overview.OptionGroups.Platform"></a>

When an option group is assigned to a DB instance, it is linked to the platform that the DB instance is on\. That platform can either be a VPC supported by the Amazon Virtual Private Cloud \(Amazon VPC\) service, or EC2\-Classic \(non\-VPC\) supported by the Amazon Elastic Compute Cloud \(Amazon EC2\) service\. For details on these two platforms, see [Amazon EC2 and Amazon Virtual Private Cloud](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-vpc.html)\. 

If a DB instance is in a VPC, the option group associated with the instance is linked to that VPC\. This means that you cannot use the option group assigned to a DB instance if you attempt to restore the instance into a different VPC or onto a different platform\. If you restore a DB instance into a different VPC or onto a different platform, you must either assign the default option group to the DB instance, assign an option group that is linked to that VPC or platform, or create a new option group and assign it to the DB instance\. Note that with persistent or permanent options, such as Oracle TDE, you must create a new option group that includes the persistent or permanent option when restoring a DB instance into a different VPC\.

Option settings control the behavior of an option\. For example, the Oracle Advanced Security option `NATIVE_NETWORK_ENCRYPTION` has a setting that you can use to specify the encryption algorithm for network traffic to and from the DB instance\. Some options settings are optimized for use with Amazon RDS and cannot be changed\.

### Mutually Exclusive Options<a name="Overview.OptionGroups.Exclusive"></a>

Some options are mutually exclusive\. You can use one or the other, but not both at the same time\. The following options are mutually exclusive: 
+ [Oracle Enterprise Manager Database Express](Appendix.Oracle.Options.OEM_DBControl.md) and [Oracle Management Agent for Enterprise Manager Cloud Control](Oracle.Options.OEMAgent.md)\. 
+ [Oracle Native Network Encryption](Appendix.Oracle.Options.NetworkEncryption.md) and [Oracle Secure Sockets Layer](Appendix.Oracle.Options.SSL.md)\. 

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

To create an option group, use the AWS CLI [https://docs.aws.amazon.com/cli/latest/reference/rds/create-option-group.html](https://docs.aws.amazon.com/cli/latest/reference/rds/create-option-group.html) command with the following required parameters\.
+ `--option-group-name`
+ `--engine-name`
+ `--major-engine-version`
+ `--option-group-description`

**Example**  
The following example creates an option group named `testoptiongroup`, which is associated with the Oracle Enterprise Edition DB engine\. The description is enclosed in quotation marks\.  
For Linux, OS X, or Unix:  

```
       
aws rds create-option-group \
    --option-group-name testoptiongroup \
    --engine-name oracle-ee \
    --major-engine-version 12.1 \
    --option-group-description "Test option group"
```
For Windows:  

```
aws rds create-option-group ^
    --option-group-name testoptiongroup ^
    --engine-name oracle-ee ^
    --major-engine-version 12.1 ^
    --option-group-description "Test option group"
```

### API<a name="USER_WorkingWithOptionGroups.Create.API"></a>

To create an option group, call the Amazon RDS API [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateOptionGroup.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateOptionGroup.html) action\. Include the following parameters:
+ `OptionGroupName`
+ `EngineName`
+ `MajorEngineVersion`
+ `OptionGroupDescription`

## Making a Copy of an Option Group<a name="USER_WorkingWithOptionGroups.Copy"></a>

You can use the AWS CLI or the Amazon RDS API to make a copy of an option group\. Copying an option group is a convenient solution when you have already created an option group and you want to include most of the custom parameters and values from that group in a new option group\. You can also make a copy of an option group that you use in production and then modify the copy to test other option settings\.

### CLI<a name="USER_WorkingWithOptionGroups.Copy.CLI"></a>

To copy an option group, use the AWS CLI [copy\-option\-group](https://docs.aws.amazon.com/cli/latest/reference/rds/copy-option-group.html) command\. Include the following required parameters:
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

To copy an option group, call the Amazon RDS API [CopyOptionGroup](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CopyOptionGroup.html) action\. Include the following required parameters\.
+ `SourceOptionGroupIdentifier`
+ `TargetOptionGroupIdentifier`
+ `TargetOptionGroupDescription`

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

To add an option to an option group, run the AWS CLI [add\-option\-to\-option\-group](https://docs.aws.amazon.com/cli/latest/reference/rds/add-option-to-option-group.html) command with the option that you want to add\. To enable the new option immediately on all associated DB instances, include the `--apply-immediately` parameter\. By default, the option is enabled for each associated DB instance during its next maintenance window\. Include the following required parameter:
+ `--option-group-name`

**Example**  
The following example adds the Oracle Enterprise Manager Database Control \(OEM\) option to an option group named `testoptiongroup` and immediately enables it\. Note that even if you use the default security group, you must specify that security group\.   
For Linux, OS X, or Unix:  

```
aws rds add-option-to-option-group \
 --option-group-name testoptiongroup \
 --options OptionName=OEM,Port=5500,DBSecurityGroupMemberships=default \
 --apply-immediately
```
For Windows:  

```
aws rds add-option-to-option-group ^
 --option-group-name testoptiongroup ^
 --options OptionName=OEM,Port=5500,DBSecurityGroupMemberships=default ^
 --apply-immediately
```
Command output is similar to the following:  

```
OPTIONGROUP   False  oracle-ee  12.1  arn:aws:rds:us-east-1:1234567890:og:testoptiongroup  Test Option Group  testoptiongroup default 
OPTIONS Oracle 12c EM Express   OEM     False   False   5500
DBSECURITYGROUPMEMBERSHIPS   default authorized
```

**Example**  
The following example adds the Oracle OEM option to an option group, specifies a custom port, and specifies a pair of Amazon EC2 VPC security groups to use for that port\.  
For Linux, OS X, or Unix:  

```
aws rds add-option-to-option-group \
 --option-group-name testoptiongroup \
 --options OptionName=OEM,Port=5500,VpcSecurityGroupMemberships="sg-test1,sg-test2" \
 --apply-immediately
```
For Windows:  

```
aws rds add-option-to-option-group ^
 --option-group-name testoptiongroup ^
 --options OptionName=OEM,Port=5500,VpcSecurityGroupMemberships="sg-test1,sg-test2" ^
 --apply-immediately
```
Command output is similar to the following:  

```
OPTIONGROUP  False  oracle-ee  12.1 arn:aws:rds:us-east-1:1234567890:og:testoptiongroup   Test Option Group  testoptiongroup vpc-test 
OPTIONS Oracle 12c EM Express   OEM     False   False   5500
VPCSECURITYGROUPMEMBERSHIPS     active  sg-test1
VPCSECURITYGROUPMEMBERSHIPS     active  sg-test2
```

**Example**  
The following example adds the Oracle option NATIVE\_NETWORK\_ENCRYPTION to an option group and specifies the option settings\. If no option settings are specified, default values are used\.  
For Linux, OS X, or Unix:  

```
aws rds add-option-to-option-group \
  --option-group-name testoptiongroup \
  --options '[{"OptionSettings":[{"Name":"SQLNET.ENCRYPTION_SERVER","Value":"REQUIRED"},{"Name":"SQLNET.ENCRYPTION_TYPES_SERVER","Value":"AES256,AES192,DES"}],"OptionName":"NATIVE_NETWORK_ENCRYPTION"}]' \
  --apply-immediately
```
For Windows:  

```
      
aws rds add-option-to-option-group ^
--option-group-name testoptiongroup ^
--options "OptionSettings"=[{"Name"="SQLNET.ENCRYPTION_SERVER","Value"="REQUIRED"},{"Name"="SQLNET.ENCRYPTION_TYPES_SERVER","Value"="AES256\,AES192\,DES"}],"OptionName"="NATIVE_NETWORK_ENCRYPTION" ^
--apply-immediately
```
Command output is similar to the following:  

```
OPTIONGROUP  False  oracle-ee  12.1  arn:aws:rds:us-east-1:1234567890:og:testoptiongroup Test Option Group   testoptiongroup
OPTIONS Oracle Advanced Security - Native Network Encryption    NATIVE_NETWORK_ENCRYPTION       False   False
OPTIONSETTINGS  RC4_256,AES256,AES192,3DES168,RC4_128,AES128,3DES112,RC4_56,DES,RC4_40,DES40    
   STATIC  STRING  RC4_256,AES256,AES192,3DES168,RC4_128,AES128,3DES112,RC4_56,DES,RC4_40,DES40    Specifies list of encryption algorithms in order of intended use       
   True     True    SQLNET.ENCRYPTION_TYPES_SERVER  AES256,AES192,DES
OPTIONSETTINGS  ACCEPTED,REJECTED,REQUESTED,REQUIRED    STATIC  STRING  REQUESTED       Specifies the desired encryption behavior  False   True  SQLNET.ENCRYPTION_SERVER  REQUIRED
OPTIONSETTINGS  SHA1,MD5   STATIC  STRING  SHA1,MD5  Specifies list of checksumming algorithms in order of intended use   True  True  SQLNET.CRYPTO_CHECKSUM_TYPES_SERVER  SHA1,MD5
```

### API<a name="USER_WorkingWithOptionGroups.AddOptions.API"></a>

To add an option to an option group using the Amazon RDS API, call the [ModifyOptionGroup](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyOptionGroup.html) action with the option that you want to add\. To enable the new option immediately on all associated DB instances, include the `ApplyImmediately` parameter and set it to `true`\. By default, the option is enabled for each associated DB instance during its next maintenance window\. Include the following required parameter:
+ `OptionGroupName`

## Listing the Options and Option Settings for an Option Group<a name="USER_WorkingWithOptionGroups.ListOption"></a>

 You can list all the options and option settings for an option group\. 

### AWS Management Console<a name="USER_WorkingWithOptionGroups.ListOption.Console"></a>

You can use the AWS Management Console to list all of the options and option settings for an option group\. 

**To list the options and option settings for an option group**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Option groups**\. The **Options** column in the table shows the options and option settings in the option group\.

### CLI<a name="USER_WorkingWithOptionGroups.ListOption.CLI"></a>

To list the options and option settings for an option group, use the AWS CLI [https://docs.aws.amazon.com/cli/latest/reference/rds/describe-option-groups.html](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-option-groups.html) command\. Specify the name of the option group whose options and settings you want to view\. If you don't specify an option group name, all option groups are described\. 

**Example**  
The following example lists the options and option settings for all option groups\.   

```
aws rds describe-option-groups
```

**Example**  
The following example lists the options and option settings for an option group named `testoptiongroup`\.   

```
aws rds describe-option-groups --option-group-name testoptiongroup
```

### API<a name="USER_WorkingWithOptionGroups.ListOption.API"></a>

To list the options and option settings for an option group, use the Amazon RDS API [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeOptionGroups.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeOptionGroups.html) action\. Specify the name of the option group whose options and settings you want to view\. If you don't specify an option group name, all option groups are described\. 

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

To modify an option setting, use the AWS CLI [https://docs.aws.amazon.com/cli/latest/reference/rds/add-option-to-option-group.html](https://docs.aws.amazon.com/cli/latest/reference/rds/add-option-to-option-group.html) command with the option group and option that you want to modify\. By default, the option is enabled for each associated DB instance during its next maintenance window\. To apply the change immediately to all associated DB instances, include the `--apply-immediately` parameter\. To modify an option setting, use the `--settings` argument\.

**Example**  
The following example modifies the port that the Oracle Enterprise Manager Database Control \(OEM\) uses in an option group named `testoptiongroup` and immediately applies the change\.   
For Linux, OS X, or Unix:  

```
aws rds add-option-to-option-group \
 --option-group-name testoptiongroup \
 --options OptionName=OEM,Port=5432,DBSecurityGroupMemberships=default \
 --apply-immediately
```
For Windows:  

```
aws rds add-option-to-option-group ^
 --option-group-name testoptiongroup ^
 --options OptionName=OEM,Port=5432,DBSecurityGroupMemberships=default ^
 --apply-immediately
```
Command output is similar to the following:  

```
OPTIONGROUP   False  oracle-ee  12.1  arn:aws:rds:us-east-1:1234567890:og:testoptiongroup   Test Option Group    testoptiongroup
OPTIONS Oracle 12c EM Express   OEM     False   False   5432
DBSECURITYGROUPMEMBERSHIPS   default  authorized
```

**Example**  
The following example modifies the Oracle option NATIVE\_NETWORK\_ENCRYPTION and changes the option settings\.   
For Linux, OS X, or Unix:  

```
aws rds add-option-to-option-group \
--option-group-name testoptiongroup \
--options '[{"OptionSettings":[{"Name":"SQLNET.ENCRYPTION_SERVER","Value":"REQUIRED"},{"Name":"SQLNET.ENCRYPTION_TYPES_SERVER","Value":"AES256,AES192,DES,RC4_256"}],"OptionName":"NATIVE_NETWORK_ENCRYPTION"}]' \
--apply-immediately
```
For Windows:  

```
aws rds add-option-to-option-group ^
--option-group-name testoptiongroup ^
--options "OptionSettings"=[{"Name"="SQLNET.ENCRYPTION_SERVER","Value"="REQUIRED"},{"Name"="SQLNET.ENCRYPTION_TYPES_SERVER","Value"="AES256\,AES192\,DES\,RC4_256"}],"OptionName"="NATIVE_NETWORK_ENCRYPTION" ^
--apply-immediately
```
Command output is similar to the following:  

```
OPTIONGROUP   False  oracle-ee  12.1  arn:aws:rds:us-east-1:1234567890:og:testoptiongroup   Test Option Group    testoptiongroup                
OPTIONS Oracle Advanced Security - Native Network Encryption    NATIVE_NETWORK_ENCRYPTION       False   False
OPTIONSETTINGS  RC4_256,AES256,AES192,3DES168,RC4_128,AES128,3DES112,RC4_56,DES,RC4_40,DES40 STATIC  STRING  
   RC4_256,AES256,AES192,3DES168,RC4_128,AES128,3DES112,RC4_56,DES,RC4_40,DES40    Specifies list of encryption algorithms in order of intended use        
   True     True    SQLNET.ENCRYPTION_TYPES_SERVER  AES256,AES192,DES,RC4_256
OPTIONSETTINGS  ACCEPTED,REJECTED,REQUESTED,REQUIRED    STATIC  STRING  REQUESTED   Specifies the desired encryption behavior  False   True  SQLNET.ENCRYPTION_SERVER    REQUIRED
OPTIONSETTINGS  SHA1,MD5   STATIC  STRING  SHA1,MD5    Specifies list of checksumming algorithms in order of intended use      True    True    SQLNET.CRYPTO_CHECKSUM_TYPES_SERVER  SHA1,MD5
OPTIONSETTINGS  ACCEPTED,REJECTED,REQUESTED,REQUIRED  STATIC  STRING  REQUESTED     Specifies the desired data integrity behavior   False   True    SQLNET.CRYPTO_CHECKSUM_SERVER  REQUESTED
```

### API<a name="USER_WorkingWithOptionGroups.ModifyOption.API"></a>

To modify an option setting, use the Amazon RDS API [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyOptionGroup.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyOptionGroup.html) command with the option group and option that you want to modify\. By default, the option is enabled for each associated DB instance during its next maintenance window\. To apply the change immediately to all associated DB instances, include the `ApplyImmediately` parameter and set it to `true`\.

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

To remove an option from an option group, use the AWS CLI [ `remove-option-from-option-group`](https://docs.aws.amazon.com/cli/latest/reference/rds/remove-option-from-option-group.html) command with the option that you want to delete\. By default, the option is removed from each associated DB instance during its next maintenance window\. To apply the change immediately, include the `--apply-immediately` parameter\. 

**Example**  
The following example removes the Oracle Enterprise Manager Database Control \(OEM\) option from an option group named `testoptiongroup` and immediately applies the change\.   
For Linux, OS X, or Unix:  

```
  
aws rds remove-option-from-option-group \
    --option-group-name testoptiongroup \
    --options OEM \
    --apply-immediately
```
For Windows:  

```
aws rds remove-option-from-option-group ^
    --option-group-name testoptiongroup ^
    --options OEM ^
    --apply-immediately
```
Command output is similar to the following:  

```
OPTIONGROUP    testoptiongroup oracle-ee   12.1    Test option group
```

### API<a name="USER_WorkingWithOptionGroups.RemoveOption.API"></a>

To remove an option from an option group, use the Amazon RDS API [ `ModifyOptionGroup`](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyOptionGroup.html) action\. By default, the option is removed from each associated DB instance during its next maintenance window\. To apply the change immediately, include the `ApplyImmediately` parameter and set it to `true`\. 

Include the following parameters: 
+ `OptionGroupName`
+ `OptionsToRemove.OptionName`