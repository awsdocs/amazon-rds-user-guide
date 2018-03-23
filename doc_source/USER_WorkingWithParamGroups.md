# Working with DB Parameter Groups<a name="USER_WorkingWithParamGroups"></a>

You manage your DB engine configuration through the use of parameters in a DB parameter group\. DB parameter groups act as a *container* for engine configuration values that are applied to one or more DB instances\. 

A default DB parameter group is created if you create a DB instance without specifying a customer\-created DB parameter group\. This default group contains database engine defaults and Amazon RDS system defaults based on the engine, compute class, and allocated storage of the instance\. You cannot modify the parameter settings of a default DB parameter group; you must create your own DB parameter group to change parameter settings from their default value\. Note that not all DB engine parameters can be changed in a customer\-created DB parameter group\.

 If you want to use your own DB parameter group, you simply create a new DB parameter group, modify the desired parameters, and modify your DB instance to use the new DB parameter group\. All DB instances that are associated with a particular DB parameter group get all parameter updates to that DB parameter group\. You can also copy an existing parameter group with the AWS CLI [copy\-db\-parameter\-group](http://docs.aws.amazon.com/cli/latest/reference/rds/copy-db-parameter-group.html) command\. Copying a parameter group is a convenient solution when you have already created a DB parameter group and you want to include most of the custom parameters and values from that group in a new DB parameter group\.

Here are some important points you should know about working with parameters in a DB parameter group:
+ When you change a dynamic parameter and save the DB parameter group, the change is applied immediately regardless of the **Apply Immediately** setting\. When you change a static parameter and save the DB parameter group, the parameter change will take effect after you manually reboot the DB instance\. You can reboot a DB instance using the RDS console or explicitly calling the `RebootDbInstance` API action \(without failover, if the DB instance is in a Multi\-AZ deployment\)\. The requirement to reboot the associated DB instance after a static parameter change helps mitigate the risk of a parameter misconfiguration affecting an API call, such as calling `ModifyDBInstance` to change DB instance class or scale storage\.
+ When you change the DB parameter group associated with a DB instance, you must manually reboot the instance before the new DB parameter group is used by the DB instance\. 
+ The value for a DB parameter can be specified as an integer or as an integer expression built from formulas, variables, functions, and operators\. Functions can include a mathematical log expression\. For more information, see [DB Parameter Values](#USER_ParamValuesRef)\.
+ Set any parameters that relate to the character set or collation of your database in your parameter group prior to creating the DB instance and before you create a database in your DB instance\. This ensures that the default database and new databases in your DB instance use the character set and collation values that you specify\. If you change character set or collation parameters for your DB instance, the parameter changes are not applied to existing databases\.

  You can change character set or collation values for an existing database using the `ALTER DATABASE` command, for example:

  ```
  ALTER DATABASE database_name CHARACTER SET character_set_name COLLATE collation;
  ```
+ Improperly setting parameters in a DB parameter group can have unintended adverse effects, including degraded performance and system instability\. Always exercise caution when modifying database parameters and back up your data before modifying a DB parameter group\. You should try out parameter group setting changes on a test DB instance before applying those parameter group changes to a production DB instance\. 
+ Amazon Aurora uses both DB parameter groups and DB cluster parameter groups\. Parameters in a DB parameter group apply to a single DB instance in an Aurora DB cluster\. Parameters in a DB cluster parameter group apply to every DB instance in a DB cluster\. For more information, see [Amazon Aurora DB Cluster and DB Instance Parameters](Aurora.Managing.md#Aurora.Managing.ParameterGroups)\.

**Topics**
+ [Creating a DB Parameter Group](#USER_WorkingWithParamGroups.Creating)
+ [Modifying Parameters in a DB Parameter Group](#USER_WorkingWithParamGroups.Modifying)
+ [Copying a DB Parameter Group](#USER_WorkingWithParamGroups.Copying)
+ [Listing DB Parameter Groups](#USER_WorkingWithParamGroups.Listing)
+ [Viewing Parameter Values for a DB Parameter Group](#USER_WorkingWithParamGroups.Viewing)
+ [DB Parameter Values](#USER_ParamValuesRef)

## Creating a DB Parameter Group<a name="USER_WorkingWithParamGroups.Creating"></a>

The following section shows you how to create a new DB parameter group\.

### AWS Management Console<a name="USER_WorkingWithParamGroups.Creating.CON"></a>

**To create a DB parameter group**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Parameter groups**\.

1. Choose **Create parameter group**\.

   The **Create parameter group** window appears\.

1. In the **Parameter group family** list, select a DB parameter group family 

1. In the **Group name** box, type the name of the new DB parameter group\.

1. In the **Description** box, type a description for the new DB parameter group\. 

1. Choose **Create**\.

### CLI<a name="USER_WorkingWithParamGroups.Creating.CLI"></a>

To create a DB parameter group, use the AWS CLI [http://docs.aws.amazon.com/cli/latest/reference/rds/create-db-parameter-group.html](http://docs.aws.amazon.com/cli/latest/reference/rds/create-db-parameter-group.html) command\. The following example creates a DB parameter group named *mydbparametergroup* for MySQL version 5\.6 with a description of "*My new parameter group*\."

Include the following required parameters:
+ `--db-parameter-group-name`
+ `--db-parameter-group-family`
+ `--description`

**Example**  
For Linux, OS X, or Unix:  

```
1. aws rds create-db-parameter-group \
2.     --db-parameter-group-name mydbparametergroup \
3.     --db-parameter-group-family MySQL5.6 \
4.     --description "My new parameter group"
```
For Windows:  

```
1. aws rds create-db-parameter-group ^
2.     --db-parameter-group-name mydbparametergroup ^
3.     --db-parameter-group-family MySQL5.6 ^
4.     --description "My new parameter group"
```
This command produces output similar to the following:  

```
1. DBPARAMETERGROUP  mydbparametergroup  mysql5.6  My new parameter group					
```

### API<a name="USER_WorkingWithParamGroups.Creating.API"></a>

To create a DB parameter group, use the Amazon RDS API [http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBParameterGroup.html](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBParameterGroup.html) action\. The following example creates a DB parameter group named *mydbparametergroup* for MySQL version 5\.6 with a description of "*My new parameter group*\."

Include the following required parameters:
+ `DBParameterGroupName = mydbparametergroup`
+ `DBParameterGroupFamily = MySQL5.6`
+ `Description = My new parameter group`

**Example**  

```
 1. https://rds.amazonaws.com/
 2. 	?Action=CreateDBParameterGroup
 3. 	&DBParameterGroupName=mydbparametergroup
 4. 	&Description=My%20new%20parameter%20group
 5. 	&DBParameterGroupFamily=MySQL5.6
 6. 	&Version=2012-01-15						
 7. 	&SignatureVersion=2
 8. 	&SignatureMethod=HmacSHA256
 9. 	&Timestamp=2012-01-15T22%3A06%3A23.624Z
10. 	&AWSAccessKeyId=<AWS Access Key ID>
11. 	&Signature=<Signature>
```
The command returns a response like the following:  

```
 1. <CreateDBParameterGroupResponse xmlns="http://rds.amazonaws.com/admin/2012-01-15/">
 2.   <CreateDBParameterGroupResult>
 3.     <DBParameterGroup>
 4.       <DBParameterGroupFamily>mysql5.6</DBParameterGroupFamily>
 5.       <Description>My new parameter group</Description>
 6.       <DBParameterGroupName>mydbparametergroup</DBParameterGroupName>
 7.     </DBParameterGroup>
 8.   </CreateDBParameterGroupResult>
 9.   <ResponseMetadata>
10.     <RequestId>700a8afe-0b81-11df-85f9-eb5c71b54ddc</RequestId>
11.   </ResponseMetadata>
12. </CreateDBParameterGroupResponse>
```

## Modifying Parameters in a DB Parameter Group<a name="USER_WorkingWithParamGroups.Modifying"></a>

You can modify parameter values in a customer\-created DB parameter group; you cannot change the parameter values in a default DB parameter group\. Changes to parameters in a customer\-created DB parameter group are applied to all DB instances that are associated with the DB parameter group\. 

If you change a parameter value, when the change is applied is determined by the type of parameter\. Changes to dynamic parameters are applied immediately\. Changes to static parameters require that the DB instance associated with DB parameter group be rebooted before the change takes effect\. To determine the type of a parameter, list the parameters in a parameter group using one of the procedures shown in the section [Listing DB Parameter Groups](#USER_WorkingWithParamGroups.Listing)\.

The RDS console shows the status of the DB parameter group associated with a DB instance\. For example, if the DB instance is not using the latest changes to its associated DB parameter group, the RDS console shows the DB parameter group with a status of **pending\-reboot**\. You would need to manually reboot the DB instance for the latest parameter changes to take effect for that DB instance\.

![\[Parameter change pending reboot scenario\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/param-reboot.png)

### AWS Management Console<a name="USER_WorkingWithParamGroups.Modifying.CON"></a>

**To modify a DB parameter group**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Parameter groups**\.

1. In the list, select the parameter group you want to modify\.

1. Choose **Parameter group actions**, and then choose **Edit**\.

1. Change the values of the parameters you want to modify\. You can scroll through the parameters using the arrow keys at the top right of the dialog box\. 

   Note that you cannot change values in a default parameter group\.

1. Choose **Save changes**\.

### CLI<a name="USER_WorkingWithParamGroups.Modifying.CLI"></a>

To modify a DB parameter group, use the AWS CLI [http://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-parameter-group.html](http://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-parameter-group.html) command with the following required parameters:
+ `--db-parameter-group-name`
+ `--parameters`

The following example modifies the` max_connections` and `max_allowed_packet` values in the DB parameter group named *mydbparametergroup*\.

**Note**  
Amazon RDS does not support passing multiple comma\-delimited parameter values for a single parameter\. 

**Example**  
For Linux, OS X, or Unix:  

```
1. aws rds modify-db-parameter-group \
2.     --db-parameter-group-name mydbparametergroup \
3.     --parameters "ParameterName=max_connections,ParameterValue=250,ApplyMethod=immediate" \
4.     --parameters "ParameterName=max_allowed_packet,ParameterValue=1024,ApplyMethod=immediate"
```
For Windows:  

```
1. aws rds modify-db-parameter-group ^
2.     --db-parameter-group-name mydbparametergroup ^
3.     --parameters "ParameterName=max_connections,ParameterValue=250,ApplyMethod=immediate" ^
4.     --parameters "ParameterName=max_allowed_packet,ParameterValue=1024,ApplyMethod=immediate"
```
The command produces output like the following:  

```
1. DBPARAMETERGROUP  mydbparametergroup
```

### API<a name="USER_WorkingWithParamGroups.Modifying.API"></a>

To modify a DB parameter group, use the Amazon RDS API [http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBParameterGroup.html](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBParameterGroup.html) command with the following required parameters:
+ `DBParameterGroupName`
+ `Parameters`

The following example modifies the` max_connections` and `max_allowed_packet` values in the DB parameter group named *mydbparametergroup*\.

**Note**  
Amazon RDS does not support passing multiple comma\-delimited parameter values for a single parameter\. 

**Example**  

```
 1. https://rds.amazonaws.com/
 2.     ?Action=ModifyDBParameterGroup
 3.     &DBParameterGroupName=mydbparametergroup
 4.     &Parameters.member.1.ParameterName=max_connections
 5.     &Parameters.member.1.ParameterValue=250
 6.     &Parameters.member.1.ApplyMethod=immediate
 7.     &Parameters.member.2.ParameterName=max_allowed_packet
 8.     &Parameters.member.2.ParameterValue=1024
 9.     &Parameters.member.2.ApplyMethod=immediate
10.     &Version=2012-01-15   
11.     &SignatureVersion=2
12.     &SignatureMethod=HmacSHA256
13.     &Timestamp=2012-01-15T22%3A29%3A47.865Z
```
The command returns a response like the following:  

```
1. <ModifyDBParameterGroupResponse xmlns="http://rds.amazonaws.com/admin/2012-01-15/">
2.   <ModifyDBParameterGroupResult>
3.     <DBParameterGroupName>mydbparametergroup</DBParameterGroupName>
4.   </ModifyDBParameterGroupResult>
5.   <ResponseMetadata>
6.     <RequestId>3b824e10-0b87-11df-972f-21e99bc6881d</RequestId>
7.   </ResponseMetadata>
8. </ModifyDBParameterGroupResponse>
```

## Copying a DB Parameter Group<a name="USER_WorkingWithParamGroups.Copying"></a>

You can copy custom DB parameter groups that you create\. Copying a parameter group is a convenient solution when you have already created a DB parameter group and you want to include most of the custom parameters and values from that group in a new DB parameter group\. You can copy a DB parameter group by using the AWS CLI [copy\-db\-parameter\-group](http://docs.aws.amazon.com/cli/latest/reference/rds/copy-db-parameter-group.html) command or the Amazon RDS API [CopyDBParameterGroup](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference//API_CopyDBParameterGroup.html) action\.

After you copy a DB parameter group, you should wait at least 5 minutes before creating your first DB instance that uses that DB parameter group as the default parameter group\. This allows Amazon RDS to fully complete the copy action before the parameter group is used as the default for a new DB instance\. This is especially important for parameters that are critical when creating the default database for a DB instance, such as the character set for the default database defined by the `character_set_database` parameter\. You can use the **Parameter Groups** option of the [Amazon RDS console](https://console.aws.amazon.com/rds/) or the [describe\-db\-parameters](http://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-parameters.html) command to verify that your DB parameter group has been created\.

**Note**  
You can't copy a default parameter group\. However, you can create a new parameter group that is based on a default parameter group\.

### AWS Management Console<a name="USER_WorkingWithParamGroups.Copying.CON"></a>

**To copy a DB parameter group**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Parameter groups**\.

1. In the list, select the custom parameter group you want to copy\.

1. Choose **Parameter group actions**, and then choose **Copy**\.

1. In **New DB parameter group identifier**, type a name for the new parameter group\.

1. In **Description**, type a description for the new parameter group\.

1. Choose **Copy**\.

### CLI<a name="USER_WorkingWithParamGroups.Copying.CLI"></a>

To copy a DB parameter group, use the AWS CLI [copy\-db\-parameter\-group](http://docs.aws.amazon.com/cli/latest/reference/rds/copy-db-parameter-group.html) command with the following required parameters:
+ `--source-db-parameter-group-identifier`
+ `--target-db-parameter-group-identifier`
+ `--target-db-parameter-group-description`

The following example creates a new DB parameter group named `mygroup2` that is a copy of the DB parameter group `mygroup1`\.

**Example**  
For Linux, OS X, or Unix:  

```
1. aws rds copy-db-parameter-group \
2.     --source-db-parameter-group-identifier mygroup1 \
3.     --target-db-parameter-group-identifier mygroup2 \
4.     --target-db-parameter-group-description "DB parameter group 2"
```
For Windows:  

```
1. aws rds copy-db-parameter-group ^
2.     --source-db-parameter-group-identifier mygroup1 ^
3.     --target-db-parameter-group-identifier mygroup2 ^
4.     --target-db-parameter-group-description "DB parameter group 2"
```

### API<a name="USER_WorkingWithParamGroups.Copying.API"></a>

To copy a DB parameter group, use the RDS API [CopyDBParameterGroup](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CopyDBParameterGroup.html) action with the following required parameters:
+ `SourceDBPparameterGroupIdentifier = arn%3Aaws%3Ards%3Aus-west-2%3A123456789012%3Apg%3Amygroup1`
+ `TargetDBPparameterGroupIdentifier = mygroup2`
+ `TargetDBPparameterGroupDescription = DB%20parameter%20group%202`

The following example creates a new DB parameter group named `mygroup2` that is a copy of the DB parameter group `mygroup1`\.

**Example**  

```
 1. https://rds.us-east-1.amazonaws.com/
 2.    ?Action=CopyDBParameterGroup
 3.    &SignatureMethod=HmacSHA256
 4.    &SignatureVersion=4
 5.    &SourceDBParameterGroupIdentifier=arn%3Aaws%3Ards%3Aus-west-2%3A123456789012%3Apg%3Amygroup1
 6.    &TargetDBParameterGroupIdentifier=mygroup2
 7.    &TargetDBParameterGroupDescription=DB%20parameter%20group%202
 8.    &Version=2014-09-01
 9.    &X-Amz-Algorithm=AWS4-HMAC-SHA256
10.    &X-Amz-Credential=AKIADQKE4SARGYLE/20140922/us-east-1/rds/aws4_request
11.    &X-Amz-Date=20140922T175351Z
12.    &X-Amz-SignedHeaders=content-type;host;user-agent;x-amz-content-sha256;x-amz-date
13.    &X-Amz-Signature=5164017efa99caf850e874a1cb7ef62f3ddd29d0b448b9e0e7c53b288ddffed2
```
The command returns a response like the following:  

```
 1. <CopyDBParameterGroupResponse xmlns="http://rds.amazonaws.com/doc/2014-10-31/">
 2.   <CopyDBParameterGroupResult>
 3.     <DBParameterGroup>
 4.       <DBParameterGroupFamily>mysql5.6</DBParameterGroupFamily>
 5.       <Description>DB parameter group 2</Description>
 6.       <DBParameterGroupName>mygroup2</DBParameterGroupName>
 7.     </DBParameterGroup>
 8.   </CopyDBParameterGroupResult>
 9.   <ResponseMetadata>
10.     <RequestId>3328d60e-beb6-11d3-8e5c-3ccda5460d76</RequestId>
11.   </ResponseMetadata>
12. </CopyDBParameterGroupResponse>
```

## Listing DB Parameter Groups<a name="USER_WorkingWithParamGroups.Listing"></a>

You can list the DB parameter groups you've created for your AWS account\.

**Note**  
Default parameter groups are automatically created from a default parameter template when you create a DB instance for a particular DB engine and version\. These default parameter groups contain preferred parameter settings and cannot be modified\. When you create a custom parameter group, you can modify parameter settings\. 

### AWS Management Console<a name="USER_WorkingWithParamGroups.Listing.CON"></a>

**To list all DB parameter groups for an AWS account**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Parameter groups**\.

   The DB parameter groups appear in a list\.

### CLI<a name="USER_WorkingWithParamGroups.Listing.CLI"></a>

To list all DB parameter groups for an AWS account, use the AWS CLI [http://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-parameter-groups.html](http://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-parameter-groups.html) command\.

**Example**  
The following example lists all available DB parameter groups for an AWS account\.  

```
1. aws rds describe-db-parameter-groups
```
The command returns a response like the following:  

```
1. DBPARAMETERGROUP  default.mysql5.5     mysql5.5  Default parameter group for MySQL5.5
2. DBPARAMETERGROUP  default.mysql5.6     mysql5.6  Default parameter group for MySQL5.6
3. DBPARAMETERGROUP  mydbparametergroup   mysql5.6  My new parameter group
```
The following example describes the *mydbparamgroup1* parameter group\.  
For Linux, OS X, or Unix:  

```
1. aws rds describe-db-parameter-groups \
2.     --db-parameter-group-name mydbparamgroup1
```
For Windows:  

```
1. aws rds describe-db-parameter-groups ^
2.     --db-parameter-group-name mydbparamgroup1
```
The command returns a response like the following:  

```
1. DBPARAMETERGROUP  mydbparametergroup1  mysql5.5  My new parameter group
```

### API<a name="USER_WorkingWithParamGroups.Listing.API"></a>

To list all DB parameter groups for an AWS account, use the RDS API [http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBParameterGroups.html](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBParameterGroups.html) action\.

**Example**  
The following example lists all available DB parameter groups for an AWS account\.  

```
1. https://rds.amazonaws.com/
2. 	?Action=DescribeDBParameterGroups
3. 	&MaxRecords=100
4. 	&Version=2012-01-15
5. 	&SignatureVersion=2
6. 	&SignatureMethod=HmacSHA256
7. 	&Timestamp=2009-10-22T19%3A31%3A42.262Z
8. 	&AWSAccessKeyId=<AWS Access Key ID>
9. 	&Signature=<Signature>
```
The command returns a response like the following:  

```
 1. <DescribeDBParameterGroupsResponse xmlns="http://rds.amazonaws.com/admin/2012-01-15/">
 2.     <DescribeDBParameterGroupsResult>
 3.         <DBParameterGroups>
 4.             <DBParameterGroup>
 5.                 <Engine>mysql5.6</Engine>
 6.                 <Description>Default parameter group for MySQL5.6</Description>
 7.                 <DBParameterGroupName>default.mysql5.6</DBParameterGroupName>
 8.             </DBParameterGroup>
 9.             <DBParameterGroup>
10.                 <Engine>mysql5.6</Engine>
11.                 <Description>My new parameter group</Description>
12.                 <DBParameterGroupName>mydbparametergroup</DBParameterGroupName>
13.             </DBParameterGroup>            
14.         </DBParameterGroups>
15.     </DescribeDBParameterGroupsResult>
16.     <ResponseMetadata>
17.         <RequestId>41731881-0b82-11df-9a9b-c1bd5894571c</RequestId>
18.     </ResponseMetadata>
19. </DescribeDBParameterGroupsResponse>
```
The following example describes the *mydbparamgroup1* parameter group\.  

```
 1. https://rds.amazonaws.com/
 2. 	?Action=DescribeDBParameterGroups
 3. 	&DBParameterGroupName=mydbparamgroup1
 4. 	&MaxRecords=100
 5. 	&Version=2012-01-15
 6. 	&SignatureVersion=2
 7. 	&SignatureMethod=HmacSHA256
 8. 	&Timestamp=2009-10-22T19%3A31%3A42.262Z
 9. 	&AWSAccessKeyId=<AWS Access Key ID>
10. 	&Signature=<Signature>
```
The command returns a response like the following:  

```
 1. <DescribeDBParameterGroupsResponse xmlns="http://rds.amazonaws.com/admin/2012-01-15/">
 2.     <DescribeDBParameterGroupsResult>
 3.         <DBParameterGroups>
 4.             <DBParameterGroup>
 5.                 <Engine>mysql5.6</Engine>
 6.                 <Description>My new parameter group</Description>
 7.                 <DBParameterGroupName>mydbparamgroup1</DBParameterGroupName>
 8.             </DBParameterGroup>            
 9.         </DBParameterGroups>
10.     </DescribeDBParameterGroupsResult>
11.     <ResponseMetadata>
12.         <RequestId>41731881-0b82-11df-9a9b-c1bd5894571c</RequestId>
13.     </ResponseMetadata>
14. </DescribeDBParameterGroupsResponse>
```

## Viewing Parameter Values for a DB Parameter Group<a name="USER_WorkingWithParamGroups.Viewing"></a>

You can get a list of all parameters in a DB parameter group and their values\.

### AWS Management Console<a name="USER_WorkingWithParamGroups.Viewing.CON"></a>

**To view the parameter values for a DB parameter group**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Parameter groups**\.

   The DB parameter groups appear in a list\.

1. Click the name of the parameter group to see the its list of parameters\.

### CLI<a name="USER_WorkingWithParamGroups.Viewing.CLI"></a>

To view the parameter values for a DB parameter group, use the AWS CLI [http://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-parameters.html](http://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-parameters.html) command with the following required parameter\.
+ `--db-parameter-group-name`

**Example**  
The following example lists the parameters and parameter values for a DB parameter group named *mydbparametergroup\.*  

```
1. aws rds describe-db-parameters --db-parameter-group-name mydbparametergroup
```
The command returns a response like the following:  

```
1. DBPARAMETER  Parameter Name            Parameter Value  Source           Data Type  Apply Type  Is Modifiable
2. DBPARAMETER  allow-suspicious-udfs                      engine-default   boolean    static      false
3. DBPARAMETER  auto_increment_increment                   engine-default   integer    dynamic     true
4. DBPARAMETER  auto_increment_offset                      engine-default   integer    dynamic     true
5. DBPARAMETER  binlog_cache_size         32768            system           integer    dynamic     true
6. DBPARAMETER  socket                    /tmp/mysql.sock  system           string     static      false
```

### API<a name="USER_WorkingWithParamGroups.Viewing.API"></a>

To view the parameter values for a DB parameter group, use the Amazon RDS API [http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBParameters.html](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBParameters.html) command with the following required parameter\.
+ `DBParameterGroupName` = *mydbparametergroup*

**Example**  
The following example lists the parameters and parameter values for a DB parameter group named *mydbparametergroup*\.  

```
 1. https://rds.amazonaws.com/
 2.     ?Action=DescribeDBParameters
 3.     &DBParameterGroupName=mydbparametergroup
 4.     &MaxRecords=100
 5.     &Version=2012-01-15
 6.     &SignatureVersion=2
 7.     &SignatureMethod=HmacSHA256
 8.     &Timestamp=2009-10-22T19%3A31%3A42.262Z
 9.     &AWSAccessKeyId=<AWS Access Key ID>
10.     &Signature=<Signature>
```
The command returns a response like the following:  

```
 1. <DescribeDBParametersResponse xmlns="http://rds.amazonaws.com/admin/2012-01-15/">
 2.   <DescribeDBParametersResult>
 3.     <Marker>bWF4X3RtcF90YWJsZXM=</Marker>
 4.     <Parameters>
 5.       <Parameter>
 6.         <DataType>boolean</DataType>
 7.         <Source>engine-default</Source>
 8.         <IsModifiable>false</IsModifiable>
 9.         <Description>Controls whether user-defined functions that have only an xxx symbol for the main function can be loaded</Description>
10.         <ApplyType>static</ApplyType>
11.         <AllowedValues>0,1</AllowedValues>
12.         <ParameterName>allow-suspicious-udfs</ParameterName>
13.       </Parameter>
14.       <Parameter>
15.         <DataType>integer</DataType>
16.         <Source>engine-default</Source>
17.         <IsModifiable>true</IsModifiable>
18.         <Description>Intended for use with master-to-master replication, and can be used to control the operation of AUTO_INCREMENT columns</Description>
19.         <ApplyType>dynamic</ApplyType>
20.         <AllowedValues>1-65535</AllowedValues>
21.         <ParameterName>auto_increment_increment</ParameterName>
22.       </Parameter>
23.       <Parameter>
24.         <DataType>integer</DataType>
25.         <Source>engine-default</Source>
26.         <IsModifiable>true</IsModifiable>
27.         <Description>Determines the starting point for the AUTO_INCREMENT column value</Description>
28.         <ApplyType>dynamic</ApplyType>
29.         <AllowedValues>1-65535</AllowedValues>
30.         <ParameterName>auto_increment_offset</ParameterName>
31.       </Parameter>
32.  
33.       (... sample truncated...)
34. 
35.     </Parameters>
36.   </DescribeDBParametersResult>
37.   <ResponseMetadata>
38.     <RequestId>99c0937a-0b83-11df-85f9-eb5c71b54ddc</RequestId>
39.   </ResponseMetadata>
40. </DescribeDBParametersResponse>
```

## DB Parameter Values<a name="USER_ParamValuesRef"></a>

The value for a DB parameter can be specified as:
+ An integer constant
+ A DB parameter formula
+ A DB parameter function
+ A character string constant
+ A log expression \(the log function represents log base 2\), such as value=**\{log\(DBInstanceClassMemory/8187281418\)\*1000\}** 

### DB Parameter Formulas<a name="USER_ParamFormulas"></a>

A DB parameter formula is an expression that resolves to an integer value, and is enclosed in braces: \{\}\. Formulas can be specified for either a DB parameter value or as an argument to a DB parameter function\.

Syntax

```
{FormulaVariable}
```

```
{FormulaVariable*Integer}
```

```
{FormulaVariable*Integer/Integer}
```

```
{FormulaVariable/Integer}
```

### DB Parameter Formula Variables<a name="USER_FormulaVariables"></a>

Formula variables return integers\. The names of the variables are case sensitive\.

AllocatedStorage  
Returns the size, in bytes, of the data volume\.

DBInstanceClassMemory  
Returns the number of bytes of memory allocated to the DB instance class associated with the current DB instance, less the memory used by the Amazon RDS processes that manage the instance\.

EndPointPort  
Returns the number of the port used when connecting to the DB instance\.

### DB Parameter Formula Operators<a name="USER_FormulaOperators"></a>

DB parameter formulas support two operators: division and multiplication\.

Division Operator: /  
Divides the dividend by the divisor, returning an integer quotient\. Decimals in the quotient are truncated, not rounded\.  
Syntax  

```
dividend / divisor
```
The dividend and divisor arguments must be integer expressions\.

Multiplication Operator: \*  
Divides the dividend by the divisor, returning an integer quotient\. Decimals in the quotient are truncated, not rounded\.  
Syntax  

```
expression * expression
```
Both expressions must be integers\.

### DB Parameter Functions<a name="USER_ParamFunctions"></a>

The parameter arguments can be specified as either integers or formulas\. Each function must have at least one argument\. Multiple arguments can be specified as a comma\-separated list\. The list cannot have any empty members, such as *argument1*,,*argument3*\. Function names are case insensitive\.

**Note**  
DB Parameter functions are not currently supported in CLI\.

GREATEST\(\)  
Returns the largest value from a list of integers or parameter formulas\.  
Syntax  

```
GREATEST(argument1, argument2,...argumentn)
```
Returns an integer\.

LEAST\(\)  
Returns the smallest value from a list of integers or parameter formulas\.  
Syntax  

```
LEAST(argument1, argument2,...argumentn)
```
Returns an integer\.

SUM\(\)  
Adds the values of the specified integers or parameter formulas\.  
Syntax  

```
SUM(argument1, argument2,...argumentn)
```
Returns an integer\.

### DB Parameter Value Examples<a name="USER_ParamValueExamples"></a>

These examples show using formulas and functions in the values for DB parameters\.

**Warning**  
Improperly setting parameters in a DB parameter group can have unintended adverse effects, including degraded performance and system instability\. Always exercise caution when modifying database parameters and back up your data before modifying your DB parameter group\. You should try out parameter group changes on a test DB instances, created using point\-in\-time\-restores, before applying those parameter group changes to your production DB instances\. 

You can specify the GREATEST function in an Oracle processes parameter to set the number of user processes to the larger of either 80 or DBInstanceClassMemory divided by 9868951\.

```
GREATEST({DBInstanceClassMemory/9868951},80)
```

You can specify the LEAST\(\) function in a MySQL max\_binlog\_cache\_size parameter value to set the maximum cache size a transaction can use in a MySQL instance to the lesser of 1MB or DBInstanceClass/256:

```
LEAST({DBInstanceClassMemory/256},10485760)
```