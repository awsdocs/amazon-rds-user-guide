# Creating and connecting to a DB instance for Amazon RDS Custom for SQL Server<a name="custom-creating-sqlserver"></a>

You can create an RDS Custom DB instance, and then connect to it using AWS Systems Manager or Remote Desktop Protocol \(RDP\)\.

**Important**  
Before you can create or connect to an RDS Custom for SQL Server DB instance, make sure to complete the tasks in [Setting up your environment for Amazon RDS Custom for SQL Server](custom-setup-sqlserver.md)\.  
You can tag RDS Custom DB instances when you create them, but don't create or modify the `AWSRDSCustom` tag that's required for RDS Custom automation\. For more information, see [Tagging RDS Custom for SQL Server resources](custom-managing-sqlserver.md#custom-managing-sqlserver.tagging)\.  
The first time that you create an RDS Custom for SQL Server DB instance, you might receive the following error: The service\-linked role is in the process of being created\. Try again later\. If you do, wait a few minutes and then try again to create the DB instance\.

**Topics**
+ [Creating an RDS Custom for SQL Server DB instance](#custom-creating-sqlserver.create)
+ [RDS Custom service\-linked role](#custom-creating-sqlserver.slr)
+ [Connecting to your RDS Custom DB instance using AWS Systems Manager](#custom-creating-sqlserver.ssm)
+ [Connecting to your RDS Custom DB instance using RDP](#custom-creating-sqlserver.rdp)

## Creating an RDS Custom for SQL Server DB instance<a name="custom-creating-sqlserver.create"></a>

Create an Amazon RDS Custom for SQL Server DB instance using either the AWS Management Console or the AWS CLI\. The procedure is similar to the procedure for creating an Amazon RDS DB instance\.

For more information, see [Creating an Amazon RDS DB instance](USER_CreateDBInstance.md)\.

### Console<a name="custom-creating-sqlserver.CON"></a>

**To create an RDS Custom for SQL Server DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose **Create database**\.

1. Choose **Standard create** for the database creation method\.

1. For **Engine options**, choose **Microsoft SQL Server** for the engine type\.

1. For **Database management type**, choose **Amazon RDS Custom**\.

1. In the **Edition** section, choose the DB engine edition that you want to use\. For RDS Custom for SQL Server, the choices are Enterprise, Standard, and Web\.

1. \(Optional\) If you intend to create the DB instance from a CEV, check the **Use custom engine version \(CEV\)** check box\. Select your CEV in the drop\-down list\.

1. For **Database version**, keep the SQL Server 2019 default value\.

1. For **Templates**, choose **Production**\.

1. In the **Settings** section, enter a unique name for the **DB instance identifier**\.

1. To enter your master password, do the following:

   1. In the **Settings** section, open **Credential Settings**\.

   1. Clear the **Auto generate a password** check box\.

   1. Change the **Master username** value and enter the same password in **Master password** and **Confirm password**\.

   By default, the new RDS Custom DB instance uses an automatically generated password for the master user\.

1. In the **DB instance size** section, choose a value for **DB instance class**\.

   For supported classes, see [DB instance class support for RDS Custom for SQL Server](custom-reqs-limits-MS.md#custom-reqs-limits.instancesMS)\.

1. Choose **Storage** settings\.

1. For **RDS Custom security**, do the following:

   1. For **IAM instance profile**, choose the instance profile for your RDS Custom for SQL Server DB instance\.

      The IAM instance profile must begin with `AWSRDSCustom`, for example `AWSRDSCustomInstanceProfileForRdsCustomInstance`\.

   1. For **Encryption**, choose **Enter a key ARN** to list the available AWS KMS keys\. Then choose your key from the list\. 

      An AWS KMS key is required for RDS Custom\. For more information, see [Make sure that you have a symmetric encryption AWS KMS key](custom-setup-sqlserver.md#custom-setup-sqlserver.cmk)\.

1. For the remaining sections, specify your preferred RDS Custom DB instance settings\. For information about each setting, see [Settings for DB instances](USER_CreateDBInstance.md#USER_CreateDBInstance.Settings)\. The following settings don't appear in the console and aren't supported:
   + **Processor features**
   + **Storage autoscaling**
   + **Availability & durability**
   + **Password and Kerberos authentication** option in **Database authentication** \(only **Password authentication** is supported\)
   + **Database options** group in **Additional configuration**
   + **Performance Insights**
   + **Log exports**
   + **Enable auto minor version upgrade**
   + **Deletion protection**

   **Backup retention period** is supported, but you can't choose **0 days**\.

1. Choose **Create database**\. 

   The **View credential details** button appears on the **Databases** page\.  

   To view the master user name and password for the RDS Custom DB instance, choose **View credential details**\.

   To connect to the DB instance as the master user, use the user name and password that appear\.
**Important**  
You can't view the master user password again\. If you don't record it, you might have to change it\. To change the master user password after the RDS Custom DB instance is available, modify the DB instance\. For more information about modifying a DB instance, see [Managing an Amazon RDS Custom for SQL Server DB instance](custom-managing-sqlserver.md)\.

1. Choose **Databases** to view the list of RDS Custom DB instances\.

1. Choose the RDS Custom DB instance that you just created\.

   On the RDS console, the details for the new RDS Custom DB instance appear:
   + The DB instance has a status of **creating** until the RDS Custom DB instance is created and ready for use\. When the state changes to **available**, you can connect to the DB instance\. Depending on the instance class and storage allocated, it can take several minutes for the new DB instance to be available\.
   + **Role** has the value **Instance \(RDS Custom\)**\.
   + **RDS Custom automation mode** has the value **Full automation**\. This setting means that the DB instance provides automatic monitoring and instance recovery\.

### AWS CLI<a name="custom-creating-sqlserver.CLI"></a>

You create an RDS Custom DB instance by using the [create\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html) AWS CLI command\.

The following options are required:
+ `--db-instance-identifier`
+ `--db-instance-class` \(for a list of supported instance classes, see [DB instance class support for RDS Custom for SQL Server](custom-reqs-limits-MS.md#custom-reqs-limits.instancesMS)\)
+ `--engine` \(`custom-sqlserver-ee`, `custom-sqlserver-se`, or `custom-sqlserver-web`\)
+ `--kms-key-id`
+ `--custom-iam-instance-profile`

The following example creates an RDS Custom for SQL Server DB instance named `my-custom-instance`\. The backup retention period is 3 days\.

**Note**  
To create a DB instance from a custom engine version \(CEV\), supply an existing CEV name to the `--engine-version` parameter\. For example, `--engine-version 15.00.4249.2.my_cevtest`

**Example**  
For Linux, macOS, or Unix:  

```
 1. aws rds create-db-instance \
 2.     --engine custom-sqlserver-ee \
 3.     --engine-version 15.00.4073.23.v1 \
 4.     --db-instance-identifier my-custom-instance \
 5.     --db-instance-class db.m5.xlarge \
 6.     --allocated-storage 20 \
 7.     --db-subnet-group mydbsubnetgroup \
 8.     --master-username myuser \
 9.     --master-user-password mypassword \
10.     --backup-retention-period 3 \
11.     --no-multi-az \
12.     --port 8200 \
13.     --kms-key-id mykmskey \
14.     --custom-iam-instance-profile AWSRDSCustomInstanceProfileForRdsCustomInstance
```
For Windows:  

```
 1. aws rds create-db-instance ^
 2.     --engine custom-sqlserver-ee ^
 3.     --engine-version 15.00.4073.23.v1 ^
 4.     --db-instance-identifier my-custom-instance ^
 5.     --db-instance-class db.m5.xlarge ^
 6.     --allocated-storage 20 ^
 7.     --db-subnet-group mydbsubnetgroup ^
 8.     --master-username myuser ^
 9.     --master-user-password mypassword ^
10.     --backup-retention-period 3 ^
11.     --no-multi-az ^
12.     --port 8200 ^
13.     --kms-key-id mykmskey ^
14.     --custom-iam-instance-profile AWSRDSCustomInstanceProfileForRdsCustomInstance
```

Get details about your instance by using the `describe-db-instances` command\.

```
1. aws rds describe-db-instances --db-instance-identifier my-custom-instance
```

The following partial output shows the engine, parameter groups, and other information\.

```
 1. {
 2.     "DBInstances": [
 3.         {
 4.             "PendingModifiedValues": {},
 5.             "Engine": "custom-sqlserver-ee",
 6.             "MultiAZ": false,
 7.             "DBSecurityGroups": [],
 8.             "DBParameterGroups": [
 9.                 {
10.                     "DBParameterGroupName": "default.custom-sqlserver-ee-15",
11.                     "ParameterApplyStatus": "in-sync"
12.                 }
13.             ],
14.             "AutomationMode": "full",
15.             "DBInstanceIdentifier": "my-custom-instance",
16.             "TagList": []
17.         }
18.     ]
19. }
```

## RDS Custom service\-linked role<a name="custom-creating-sqlserver.slr"></a>

A *service\-linked role* gives Amazon RDS Custom access to resources in your AWS account\. It makes using RDS Custom easier because you don't have to manually add the necessary permissions\. RDS Custom defines the permissions of its service\-linked roles, and unless defined otherwise, only RDS Custom can assume its roles\. The defined permissions include the trust policy and the permissions policy, and that permissions policy can't be attached to any other IAM entity\.

When you create an RDS Custom DB instance, both the Amazon RDS and RDS Custom service\-linked roles are created \(if they don't already exist\) and used\. For more information, see [Using service\-linked roles for Amazon RDS](UsingWithRDS.IAM.ServiceLinkedRoles.md)\.

The first time that you create an RDS Custom for SQL Server DB instance, you might receive the following error: The service\-linked role is in the process of being created\. Try again later\. If you do, wait a few minutes and then try again to create the DB instance\.

## Connecting to your RDS Custom DB instance using AWS Systems Manager<a name="custom-creating-sqlserver.ssm"></a>

After you create your RDS Custom DB instance, you can connect to it using AWS Systems Manager Session Manager\. Session Manager is a Systems Manager capability that you can use to manage Amazon EC2 instances through a browser\-based shell or through the AWS CLI\. For more information, see [AWS Systems Manager Session Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/session-manager.html)\.

### Console<a name="custom-creating-sqlserver.ssm.CON"></a>

**To connect to your DB instance using Session Manager**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**, and then choose the RDS Custom DB instance to which you want to connect\.

1. Choose **Configuration**\.

1. Note the **Resource ID** value for your DB instance\. For example, the resource ID might be `db-ABCDEFGHIJKLMNOPQRS0123456`\.

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Look for the name of your EC2 instance, and then choose the instance ID associated with it\. For example, the instance ID might be `i-abcdefghijklm01234`\.

1. Choose **Connect**\.

1. Choose **Session Manager**\.

1. Choose **Connect**\.

   A window opens for your session\.

### AWS CLI<a name="custom-creating-sqlserver.ssm.CLI"></a>

You can connect to your RDS Custom DB instance using the AWS CLI\. This technique requires the Session Manager plugin for the AWS CLI\. To learn how to install the plugin, see [Install the Session Manager plugin for the AWS CLI](https://docs.aws.amazon.com/systems-manager/latest/userguide/session-manager-working-with-install-plugin.html)\.

To find the DB resource ID of your RDS Custom DB instance, use `[describe\-db\-instances](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-instances.html)`\.

```
aws rds describe-db-instances \
    --query 'DBInstances[*].[DBInstanceIdentifier,DbiResourceId]' \
    --output text
```

The following sample output shows the resource ID for your RDS Custom instance\. The prefix is `db-`\.

```
db-ABCDEFGHIJKLMNOPQRS0123456
```

To find the EC2 instance ID of your DB instance, use `aws ec2 describe-instances`\. The following example uses `db-ABCDEFGHIJKLMNOPQRS0123456` for the resource ID\.

```
aws ec2 describe-instances \
    --filters "Name=tag:Name,Values=db-ABCDEFGHIJKLMNOPQRS0123456" \
    --output text \
    --query 'Reservations[*].Instances[*].InstanceId'
```

The following sample output shows the EC2 instance ID\.

```
i-abcdefghijklm01234
```

Use the `aws ssm start-session` command, supplying the EC2 instance ID in the `--target` parameter\.

```
aws ssm start-session --target "i-abcdefghijklm01234"
```

A successful connection looks like the following\.

```
Starting session with SessionId: yourid-abcdefghijklm1234
[ssm-user@ip-123-45-67-89 bin]$
```

## Connecting to your RDS Custom DB instance using RDP<a name="custom-creating-sqlserver.rdp"></a>

After you create your RDS Custom DB instance, you can connect to this instance using an RDP client\. The procedure is the same as for connecting to an Amazon EC2 instance\. For more information, see [Connect to your Windows instance](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/connecting_to_windows_instance.html)\.

To connect to the DB instance, you need the key pair associated with the instance\. RDS Custom creates the key pair for you\. The pair name uses the prefix `do-not-delete-rds-custom-DBInstanceIdentifier`\. AWS Secrets Manager stores your private key as a secret\.

Complete the task in the following steps:

1. [Configure your DB instance to allow RDP connections](#custom-creating-sqlserver.rdp.port)\.

1. [Retrieve your secret key](#custom-creating-sqlserver.rdp.key)\.

1. [Connect to your EC2 instance using the RDP utility](#custom-creating-sqlserver.rdp.connect)\.

### Configure your DB instance to allow RDP connections<a name="custom-creating-sqlserver.rdp.port"></a>

To allow RDP connections, configure your VPC security group and set a firewall rule on the host\.

#### Configure your VPC security group<a name="custom-creating-sqlserver.rdp.port.vpc"></a>

Make sure that the VPC security group associated with your DB instance permits inbound connections on port 3389 for Transmission Control Protocol \(TCP\)\. To learn how to configure your VPC security group, see [Configure your VPC security group](custom-setup-sqlserver.md#custom-setup-sqlserver.vpc.sg)\.

#### Set the firewall rule on the host<a name="custom-creating-sqlserver.rdp.port.firewall"></a>

To permit inbound connections on port 3389 for TCP, set a firewall rule on the host\. The following examples show how to do this\.

We recommend that you use the specific `-Profile` value: `Public`, `Private`, or `Domain`\. Using `Any` refers to all three values\. You can also specify a combination of values separated by a comma\. For more information about setting firewall rules, see [Set\-NetFirewallRule](https://docs.microsoft.com/en-us/powershell/module/netsecurity/set-netfirewallrule?view=windowsserver2019-ps) in the Microsoft documentation\.

**To use Systems Manager Session Manager to set a firewall rule**

1. Connect to Session Manager as shown in [Connecting to your RDS Custom DB instance using AWS Systems Manager](#custom-creating-sqlserver.ssm)\.

1. Run the following command\.

   ```
   Set-NetFirewallRule -DisplayName "Remote Desktop - User Mode (TCP-In)" -Direction Inbound -LocalAddress Any -Profile Any
   ```

**To use Systems Manager CLI commands to set a firewall rule**

1. Use the following command to open RDP on the host\.

   ```
   OPEN_RDP_COMMAND_ID=$(aws ssm send-command --region $AWS_REGION \
       --instance-ids $RDS_CUSTOM_INSTANCE_EC2_ID \
       --document-name "AWS-RunPowerShellScript" \
       --parameters '{"commands":["Set-NetFirewallRule -DisplayName \"Remote Desktop - User Mode (TCP-In)\" -Direction Inbound -LocalAddress Any -Profile Any"]}' \
       --comment "Open RDP port" | jq -r ".Command.CommandId")
   ```

1. Use the command ID returned in the output to get the status of the previous command\. To use the following query to return the command ID, make sure that you have the jq plug\-in installed\.

   ```
   aws ssm list-commands \
       --region $AWS_REGION \
       --command-id $OPEN_RDP_COMMAND_ID
   ```

### Retrieve your secret key<a name="custom-creating-sqlserver.rdp.key"></a>

Retrieve your secret key using either AWS Management Console or the AWS CLI\.

#### Console<a name="custom-creating-sqlserver.rdp.key.CON"></a>

**To retrieve the secret key**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**, and then choose the RDS Custom DB instance to which you want to connect\.

1. Choose the **Configuration** tab\.

1. Note the **DB instance ID** for your DB instance, for example, `my-custom-instance`\.

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Look for the name of your EC2 instance, and then choose the instance ID associated with it\.

   In this example, the instance ID is `i-abcdefghijklm01234`\.

1. In **Details**, find **Key pair name**\. The pair name includes the DB identifier\. In this example, the pair name is `do-not-delete-rds-custom-my-custom-instance-0d726c`\.

1. In the instance summary, find **Public IPv4 DNS**\. For the example, the public DNS might be `ec2-12-345-678-901.us-east-2.compute.amazonaws.com`\.

1. Open the AWS Secrets Manager console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\.

1. Choose the secret that has the same name as your key pair\.

1. Choose **Retrieve secret value**\.

#### AWS CLI<a name="custom-creating-sqlserver.rdp.key.CLI"></a>

**To retrieve the private key**

1. Get the list of your RDS Custom DB instances by calling the `aws rds describe-db-instances` command\.

   ```
   aws rds describe-db-instances \
       --query 'DBInstances[*].[DBInstanceIdentifier,DbiResourceId]' \
       --output text
   ```

1. Choose the DB instance identifier from the sample output, for example `do-not-delete-rds-custom-my-custom-instance`\.

1. Find the EC2 instance ID of your DB instance by calling the `aws ec2 describe-instances` command\. The following example uses the EC2 instance name to describe the DB instance\.

   ```
   aws ec2 describe-instances \
       --filters "Name=tag:Name,Values=do-not-delete-rds-custom-my-custom-instance" \
       --output text \
       --query 'Reservations[*].Instances[*].InstanceId'
   ```

   The following sample output shows the EC2 instance ID\.

   ```
   i-abcdefghijklm01234
   ```

1. Find the key name by specifying the EC2 instance ID, as shown in the following example\.

   ```
   aws ec2 describe-instances \
       --instance-ids i-abcdefghijklm01234 \
       --output text \
       --query 'Reservations[*].Instances[*].KeyName'
   ```

   The following sample output shows the key name, which uses the prefix `do-not-delete-rds-custom-DBInstanceIdentifier`\.

   ```
   do-not-delete-rds-custom-my-custom-instance-0d726c
   ```

### Connect to your EC2 instance using the RDP utility<a name="custom-creating-sqlserver.rdp.connect"></a>

Follow the procedure in [Connect to your Windows instance using RDP](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/connecting_to_windows_instance.html#connect-rdp) in the *Amazon EC2 User Guide for Windows Instances*\. This procedure assumes that you created a \.pem file that contains your private key\.