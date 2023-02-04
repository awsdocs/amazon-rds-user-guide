# Amazon EFS integration<a name="oracle-efs-integration"></a>

You can transfer files between your RDS for Oracle DB instance and an Amazon EFS file system\. You can use Amazon EFS integration with Oracle Database features such as Oracle Data Pump\. For example, you can import Data Pump files from Amazon EFS to your RDS for Oracle DB instance\. You don’t need to copy these files locally because Data Pump imports directly from the EFS file system\. For more information, see [Importing data into Oracle on Amazon RDS](Oracle.Procedural.Importing.md)\.

Note the following requirements and restrictions:
+ Amazon EFS integration is supported only for Oracle Database 19c \- July 2022 Patch Set Update \(PSU\) 19\.0\.0\.0\.ru\-2022\-07\.rur\-2022\-07\.r1 or later\.
+ Your DB instance and your EFS file system must be in the same AWS Region and the same VPC\.
+ Your VPC must have the `enableDnsSupport` attribute enabled\. For more information, see [DNS attributes in your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-dns.html#vpc-dns-support) in the *Amazon Virtual Private Cloud User Guide*\.
+ Your EFS file system must use the Standard or Standard\-IA storage class\.
+ RDS for Oracle doesn't support automated backups or manual DB snapshots of an EFS file system\. Instead, you must use AWS Backup or other solutions to back up your EFS file system\. For more information, see [Backing up your Amazon EFS file systems](https://docs.aws.amazon.com/efs/latest/ug/efs-backup-solutions.html)\.

**Topics**
+ [Configuring network permissions for RDS for Oracle integration with Amazon EFS](#oracle-efs-integration.network)
+ [Configuring IAM permissions for RDS for Oracle integration with Amazon EFS](#oracle-efs-integration.iam)
+ [Adding the EFS\_INTEGRATION option](#oracle-efs-integration.adding)
+ [Configuring Amazon EFS file system permissions](#oracle-efs-integration.file-system)
+ [Transferring files between RDS for Oracle and an Amazon EFS file system](#oracle-efs-integration.transferring)
+ [Removing the EFS\_INTEGRATION option](#oracle-efs-integration.removing)
+ [Troubleshooting Amazon EFS integration](#oracle-efs-integration.troubleshooting)

## Configuring network permissions for RDS for Oracle integration with Amazon EFS<a name="oracle-efs-integration.network"></a>

For RDS for Oracle to integrate with Amazon EFS, make sure that your DB instance has network access to an EFS file system\. For more information, see [Controlling network access to Amazon EFS file systems for NFS clients](https://docs.aws.amazon.com/efs/latest/ug/NFS-access-control-efs.html) in the *Amazon Elastic File System User Guide*\.

**Topics**
+ [Controlling network access with security groups](#oracle-efs-integration.network.inst-access)
+ [Controlling network access with file system policies](#oracle-efs-integration.network.file-system-policy)

### Controlling network access with security groups<a name="oracle-efs-integration.network.inst-access"></a>

You can control your DB instance access to EFS file systems using network layer security mechanisms such as VPC security groups\. To allow access to an EFS file system for your DB instance, make sure that your EFS file system meets the following requirements:
+ A mount target exists in every Availability Zone\.
+ A security group is attached to the mount target\.
+ The security group has an inbound rule to allow the network subnet or security group of the RDS for Oracle DB instance on TCP/2049 \(Type NFS\)\.

For more information, see [Creating Amazon EFS file systems](https://docs.aws.amazon.com/efs/latest/ug/creating-using-create-fs.html#configure-efs-network-access) and [Creating and managing EFS mount targets and security groups](https://docs.aws.amazon.com/efs/latest/ug/accessing-fs.html) in the *Amazon Elastic File System User Guide*\.

### Controlling network access with file system policies<a name="oracle-efs-integration.network.file-system-policy"></a>

Amazon EFS integration with RDS for Oracle works with the default \(empty\) EFS file system policy\. The default policy doesn't use IAM to authenticate\. Instead, it grants full access to any anonymous client that can connect to the file system using a mount target\. The default policy is in effect whenever a user\-configured file system policy isn't in effect, including at file system creation\. For more information, see [Default EFS file system policy](https://docs.aws.amazon.com/efs/latest/ug/iam-access-control-nfs-efs.html#default-filesystempolicy) in the *Amazon Elastic File System User Guide*\.

To strengthen access to your EFS file system for all clients, including RDS for Oracle, you can configure IAM permissions\. In this approach, you create a file system policy\. For more information, see [Creating file system policies](https://docs.aws.amazon.com/efs/latest/ug/create-file-system-policy.html) in the *Amazon Elastic File System User Guide*\.

## Configuring IAM permissions for RDS for Oracle integration with Amazon EFS<a name="oracle-efs-integration.iam"></a>

For RDS for Oracle to integrate with Amazon EFS, your DB instance must have IAM permissions to access to an Amazon EFS file system\.

### Step 1: Create an IAM role for your DB instance and attach your policy<a name="oracle-efs-integration.iam.role"></a>

In this step, you create a role for your RDS for Oracle DB instance to allow Amazon RDS to access your EFS file system\.

#### Console<a name="oracle-efs-integration.iam.role.console"></a>

**To create an IAM role to allow Amazon RDS access to an EFS file system**

1. Open the [IAM Management Console](https://console.aws.amazon.com/iam/home?#home)\.

1. In the navigation pane, choose **Roles**\.

1. Choose **Create role**\.

1. For **AWS service**, choose **RDS**\.

1. For **Select your use case**, choose **RDS – Add Role to Database**\.

1. Choose **Next**\.

1. Don't add any permissions policies\. Choose **Next**\.

1. Set **Role name** to a name for your IAM role, for example `rds-efs-integration-role`\. You can also add an optional **Description** value\.

1. Choose **Create role**\.

#### AWS CLI<a name="integration.preparing.role.CLI"></a>

To limit the service's permissions to a specific resource, we recommend using the [https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html#condition-keys-sourcearn](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html#condition-keys-sourcearn) and [https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html#condition-keys-sourceaccount](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html#condition-keys-sourceaccount) global condition context keys in resource\-based trust relationships\. This is the most effective way to protect against the [confused deputy problem](https://docs.aws.amazon.com/IAM/latest/UserGuide/confused-deputy.html)\.

You might use both global condition context keys and have the `aws:SourceArn` value contain the account ID\. In this case, the `aws:SourceAccount` value and the account in the `aws:SourceArn` value must use the same account ID when used in the same statement\.
+ Use `aws:SourceArn` if you want cross\-service access for a single resource\.
+ Use `aws:SourceAccount` if you want to allow any resource in that account to be associated with the cross\-service use\.

In the trust relationship, make sure to use the `aws:SourceArn` global condition context key with the full Amazon Resource Name \(ARN\) of the resources accessing the role\.

The following AWS CLI command creates the role named `rds-efs-integration-role` for this purpose\.

**Example**  
For Linux, macOS, or Unix:  

```
aws iam create-role \
   --role-name rds-efs-integration-role \
   --assume-role-policy-document '{
     "Version": "2012-10-17",
     "Statement": [
       {
         "Effect": "Allow",
         "Principal": {
            "Service": "rds.amazonaws.com"
          },
         "Action": "sts:AssumeRole",
         "Condition": {
             "StringEquals": {
                 "aws:SourceAccount": my_account_ID,
                 "aws:SourceArn": "arn:aws:rds:Region:my_account_ID:db:dbname"
             }
         }
       }
     ]
   }'
```
For Windows:  

```
aws iam create-role ^
   --role-name rds-efs-integration-role ^
   --assume-role-policy-document '{
     "Version": "2012-10-17",
     "Statement": [
       {
         "Effect": "Allow",
         "Principal": {
            "Service": "rds.amazonaws.com"
          },
         "Action": "sts:AssumeRole",
         "Condition": {
             "StringEquals": {
                 "aws:SourceAccount": my_account_ID,
                 "aws:SourceArn": "arn:aws:rds:Region:my_account_ID:db:dbname"
             }
         }
       }
     ]
   }'
```

For more information, see [Creating a role to delegate permissions to an IAM user](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-user.html) in the *IAM User Guide*\.

### Step 2: Create a file system policy for your Amazon EFS file system<a name="oracle-efs-integration.iam.policy"></a>

In this step, you create a file system policy for your EFS file system\.

**To create or edit an EFS file system policy**

1. Open the [EFS Management Console](https://console.aws.amazon.com/efs/home?#home)\.

1. Choose **File Systems**\.

1. On the **File systems** page, choose the file system that you want to edit or create a file system policy for\. The details page for that file system is displayed\.

1. Choose the **File system policy** tab\.

   If the policy is empty, then the default EFS file system policy is in use\. For more information, see [Default EFS file system policy](https://docs.aws.amazon.com/efs/latest/ug/iam-access-control-nfs-efs.html#default-filesystempolicy ) in the *Amazon Elastic File System User Guide*\.

1. Choose **Edit**\. The **File system policy** page appears\.

1. In **Policy editor**, enter a policy such as the following, and then choose **Save**\.

   ```
   {
       "Version": "2012-10-17",
       "Id": "ExamplePolicy01",
       "Statement": [
           {
               "Sid": "ExampleStatement01",
               "Effect": "Allow",
               "Principal": {
                   "AWS": "arn:aws:iam::123456789012:role/rds-efs-integration-role"
               },
               "Action": [
                   "elasticfilesystem:ClientMount",
                   "elasticfilesystem:ClientWrite",
                   "elasticfilesystem:ClientRootAccess"
               ],
               "Resource": "arn:aws:elasticfilesystem:Region:123456789012:file-system/fs-1234567890abcdef0"
           }
       ]
   }
   ```

### Step 3: Associate your IAM role with your RDS for Oracle DB instance<a name="oracle-efs-integration.iam.instance"></a>

In this step, you associate your IAM role with your DB instance\. Be aware of the following requirements:
+ You must have access to an IAM role with the required Amazon EFS permissions policy attached to it\. 
+ You can associate only one IAM role with your RDS for Oracle DB instance at a time\.
+ The status of your instance must be **Available**\.

For more information, see [Identity and access management for Amazon EFS](https://docs.aws.amazon.com/efs/latest/ug/auth-and-access-control.html) in the *Amazon Elastic File System User Guide*\.

#### Console<a name="oracle-efs-integration.iam.instance.console"></a>

**To associate your IAM role with your RDS for Oracle DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. Choose **Databases**\.

1. If your database instance is unavailable, choose **Actions** and then **Start**\. When the instance status shows **Started**, go to the next step\.

1. Choose the Oracle DB instance name to display its details\.

1. On the **Connectivity & security** tab, scroll down to the **Manage IAM roles** section at the bottom of the page\.

1. Choose the role to add in the **Add IAM roles to this instance** section\.

1. For **Feature**, choose **EFS\_INTEGRATION**\.

1. Choose **Add role**\.

#### AWS CLI<a name="oracle-efs-integration.iam.instance.CLI"></a>

The following AWS CLI command adds the role to an Oracle DB instance named `mydbinstance`\.

**Example**  
For Linux, macOS, or Unix:  

```
aws rds add-role-to-db-instance \
   --db-instance-identifier mydbinstance \
   --feature-name EFS_INTEGRATION \
   --role-arn your-role-arn
```
For Windows:  

```
aws rds add-role-to-db-instance ^
   --db-instance-identifier mydbinstance ^
   --feature-name EFS_INTEGRATION ^
   --role-arn your-role-arn
```

Replace `your-role-arn` with the role ARN that you noted in a previous step\. `EFS_INTEGRATION` must be specified for the `--feature-name` option\.

## Adding the EFS\_INTEGRATION option<a name="oracle-efs-integration.adding"></a>

To integrate Amazon RDS for Oracle with Amazon EFS, your DB instance must be associated with an option group that includes the `EFS_INTEGRATION` option\. 

Multiple Oracle DB instances that belong to the same option group share the same EFS file system\. Different DB instances can access the same data, but access can be divided by using different Oracle directories\. For more information see [Transferring files between RDS for Oracle and an Amazon EFS file system](#oracle-efs-integration.transferring)\.

### Console<a name="oracle-efs-integration.adding.console"></a>

**To configure an option group for Amazon EFS integration**

1. Create a new option group or identify an existing option group to which you can add the `EFS_INTEGRATION` option\.

   For information about creating an option group, see [Creating an option group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.Create)\.

1. Add the `EFS_INTEGRATION` option to the option group\. You need to specify the `EFS_ID` file system ID and set the `USE_IAM_ROLE` flag\.

   For more information, see [Adding an option to an option group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.AddOption)\.

1. Associate the option group with your DB instance in either of the following ways:
   + Create a new Oracle DB instance and associate the option group with it\. For information about creating a DB instance, see [Creating an Amazon RDS DB instance](USER_CreateDBInstance.md)\.
   + Modify an Oracle DB instance to associate the option group with it\. For information about modifying an Oracle DB instance, see [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)\.

### AWS CLI<a name="oracle-efs-integration.adding.cli"></a>

**To configure an option group for EFS integration**

1. Create a new option group or identify an existing option group to which you can add the `EFS_INTEGRATION` option\.

   For information about creating an option group, see [Creating an option group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.Create)\.

1. Add the `EFS_INTEGRATION` option to the option group\.

   For example, the following AWS CLI command adds the `EFS_INTEGRATION` option to an option group named **myoptiongroup**\.  
**Example**  

   For Linux, macOS, or Unix:

   ```
   aws rds add-option-to-option-group \
      --option-group-name myoptiongroup \
      --options "OptionName=EFS_INTEGRATION,OptionSettings=\ 
      [{Name=EFS_ID,Value=fs-1234567890abcdef0},{Name=USE_IAM_ROLE,Value=TRUE}]"
   ```

   For Windows:

   ```
   aws rds add-option-to-option-group ^
      --option-group-name myoptiongroup ^
      --options "OptionName=EFS_INTEGRATION,OptionSettings=^
      [{Name=EFS_ID,Value=fs-1234567890abcdef0},{Name=USE_IAM_ROLE,Value=TRUE}]"
   ```

1. Associate the option group with your DB instance in either of the following ways:
   + Create a new Oracle DB instance and associate the option group with it\. For information about creating a DB instance, see [Creating an Amazon RDS DB instance](USER_CreateDBInstance.md)\.
   + Modify an Oracle DB instance to associate the option group with it\. For information about modifying an Oracle DB instance, see [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)\.

## Configuring Amazon EFS file system permissions<a name="oracle-efs-integration.file-system"></a>

By default, only the root user \(UID `0`\) has read, write, and execute permissions for a newly created EFS file system\. For other users to modify the file system, the root user must explicitly grant them access\. The user for the RDS for Oracle DB instance is in the `others` category\. For more information, see [Working with users, groups, and permissions at the Network File System \(NFS\) Level](https://docs.aws.amazon.com/efs/latest/ug/accessing-fs-nfs-permissions.html) in the *Amazon Elastic File System User Guide*\.

To allow your RDS for Oracle DB instance to read and write files on an EFS file system, do the following:
+ Mount an EFS file system locally on your Amazon EC2 or on\-premises instance\.
+ Configure fine grain permissions\.

For example, to grant `other` users permissions to write to the EFS file system root, run `chmod 777` on this directory\. For more information, see [Example Amazon EFS file system use cases and permissions](https://docs.aws.amazon.com/efs/latest/ug/accessing-fs-nfs-permissions.html#accessing-fs-nfs-permissions-ex-scenarios) in the *Amazon Elastic File System User Guide*\. 

## Transferring files between RDS for Oracle and an Amazon EFS file system<a name="oracle-efs-integration.transferring"></a>

To transfer files between an RDS for Oracle instance and an Amazon EFS file system, create at least one Oracle directory and configure EFS file system permissions to control DB instance access\.

**Topics**
+ [Creating an Oracle directory](#oracle-efs-integration.transferring.od)
+ [Transferring data to and from an EFS file system: examples](#oracle-efs-integration.transferring.upload)

### Creating an Oracle directory<a name="oracle-efs-integration.transferring.od"></a>

To create an Oracle directory, use the procedure `rdsadmin.rdsadmin_util.create_directory_efs`\. The procedure has the following parameters\.


****  

| Parameter name | Data type | Default | Required | Description | 
| --- | --- | --- | --- | --- | 
|  `p_directory_name`  |  VARCHAR2  |  –  |  Yes  |  The name of the Oracle directory\.   | 
|  `p_path_on_efs`  |  VARCHAR2  |  –  |  Yes  |  The path on the EFS file system\. The prefix of the path name uses the pattern `/rdsefs-fsid/`, where *fsid* is a placeholder for your EFS file system ID\. For example, if your EFS file system is named `fs-1234567890abcdef0`, and you create a subdirectory on this file system named `mydir`, you could specify the following value: <pre>/rdsefs-fs-1234567890abcdef0/mydir</pre>  | 

Assume that you create a subdirectory named `/datapump1` on the EFS file system `fs-1234567890abcdef0`\. The following example creates an Oracle directory `DATA_PUMP_DIR_EFS` that points to the `/datapump1` directory on the EFS file system\. The file system path value for the `p_path_on_efs` parameter is prefixed with the string `/rdsefs-`\.

```
BEGIN
  rdsadmin.rdsadmin_util.create_directory_efs(
    p_directory_name => 'DATA_PUMP_DIR_EFS', 
    p_path_on_efs    => '/rdsefs-fs-1234567890abcdef0/datapump1');
END;
/
```

### Transferring data to and from an EFS file system: examples<a name="oracle-efs-integration.transferring.upload"></a>

The following example uses Oracle Data Pump to export the table named `MY_TABLE` to file `datapump.dmp`\. This file resides on an EFS file system\.

```
DECLARE
  v_hdnl NUMBER;
BEGIN
  v_hdnl := DBMS_DATAPUMP.OPEN(operation => 'EXPORT', job_mode => 'SCHEMA', job_name=>null);
  DBMS_DATAPUMP.ADD_FILE(
    handle    => v_hdnl,
    filename  => 'datapump.dmp',
    directory => 'DATA_PUMP_DIR_EFS',
    filetype  => dbms_datapump.ku$_file_type_dump_file);
  DBMS_DATAPUMP.ADD_FILE(
    handle    => v_hdnl,
    filename  => 'datapump-exp.log',
    directory => 'DATA_PUMP_DIR_EFS',
    filetype  => dbms_datapump.ku$_file_type_log_file);
  DBMS_DATAPUMP.METADATA_FILTER(v_hdnl,'SCHEMA_EXPR','IN (''MY_TABLE'')');
  DBMS_DATAPUMP.START_JOB(v_hdnl);
END;
/
```

The following example uses Oracle Data Pump to import the table named `MY_TABLE` from file `datapump.dmp`\. This file resides on an EFS file system\.

```
DECLARE
  v_hdnl NUMBER;
BEGIN
  v_hdnl := DBMS_DATAPUMP.OPEN(
    operation => 'IMPORT',
    job_mode  => 'SCHEMA',
    job_name  => null);
  DBMS_DATAPUMP.ADD_FILE(
    handle    => v_hdnl,
    filename  => 'datapump.dmp',
    directory => 'DATA_PUMP_DIR_EFS',
    filetype  => dbms_datapump.ku$_file_type_dump_file );
  DBMS_DATAPUMP.ADD_FILE(
    handle    => v_hdnl,
    filename  => 'datapump-imp.log',
    directory => 'DATA_PUMP_DIR_EFS',
    filetype  => dbms_datapump.ku$_file_type_log_file);
  DBMS_DATAPUMP.METADATA_FILTER(v_hdnl,'SCHEMA_EXPR','IN (''MY_TABLE'')');
  DBMS_DATAPUMP.START_JOB(v_hdnl);
END;
/
```

For more information, see [Importing data into Oracle on Amazon RDS](Oracle.Procedural.Importing.md)\.

## Removing the EFS\_INTEGRATION option<a name="oracle-efs-integration.removing"></a>

To remove the `EFS_INTEGRATION` option from an RDS for Oracle DB instance, do one of the following: 
+ To remove the `EFS_INTEGRATION` option from multiple DB instances, remove the `EFS_INTEGRATION` option from the option group to which the DB instances belong\. This change affects all DB instances that use the option group\. For more information, see [Removing an option from an option group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.RemoveOption)\.

   
+ To remove the `EFS_INTEGRATION` option from a single DB instance, modify the instance and specify a different option group that doesn't include the `EFS_INTEGRATION` option\. You can specify the default \(empty\) option group or a different custom option group\. For more information, see [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)\.

## Troubleshooting Amazon EFS integration<a name="oracle-efs-integration.troubleshooting"></a>

Your RDS for Oracle DB instance monitors the connectivity to an Amazon EFS file system\. When monitoring detects an issue, it might try to correct the issue and publish an event in the RDS console\. For more information, see [Viewing Amazon RDS events](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_ListEvents.html)\.

Use the information in this section to help you diagnose and fix common issues when you work with Amazon EFS integration\.


| Notification | Description | Action | 
| --- | --- | --- | 
|  `The EFS for RDS Oracle instance instance_name isn't available on the primary host. NFS port 2049 of your EFS isn't reachable.`  |  The DB instance can't communicate with the EFS file system\.  |  Make sure of the following: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/oracle-efs-integration.html)  | 
|  `The EFS isn't reachable.`  |  An error occurred during the installation of the `EFS_INTEGRATION` option\.  |  Make sure of the following: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/oracle-efs-integration.html)  | 
|  `The associated role with your DB instance wasn't found.`  |  An error occurred during the installation of the `EFS_INTEGRATION` option\.  |  Make sure that you associated an IAM role with your RDS for Oracle DB instance\.  | 
|  `N/A`  |  You might see the following error: `PLS-00302: component 'CREATE_DIRECTORY_EFS' must be declared`\. This error can occur when you're using a version of RDS for Oracle that doesn't support Amazon EFS\.  |  Make sure that you are using RDS for Oracle DB instance version 19\.0\.0\.0\.ru\-2022\-07\.rur\-2022\-07\.r1 or higher\.  | 
|  `Read access of your EFS is denied. Check your file system policy.`  |  Your DB instance can't read the EFS file system\.  |  Make sure that your EFS file system allows read access through the IAM role or on the EFS file system level\.   | 
|  N/A  |  Your DB instance can't write to the EFS file system\.  |  Take the following steps: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/oracle-efs-integration.html)  | 