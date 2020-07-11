# Setting Up Kerberos Authentication for Oracle DB Instances<a name="oracle-kerberos-setting-up"></a>

You use AWS Directory Service for Microsoft Active Directory, also called AWS Managed Microsoft AD, to set up Kerberos authentication for an Oracle DB instance\. To set up Kerberos authentication, complete the following steps:
+ [Step 1: Create a Directory Using the AWS Managed Microsoft AD](#oracle-kerberos-setting-up.create-directory)
+ [Step 2: Create a Trust](#oracle-kerberos-setting-up.create-forest-trust)
+ [Step 3: Create an IAM Role for Use by Amazon RDS](#oracle-kerberos-setting-up.CreateIAMRole)
+ [Step 4: Create and Configure Users](#oracle-kerberos-setting-up.create-users)
+ [Step 5: Enable Cross\-VPC Traffic Between the Directory and the DB Instance](#oracle-kerberos-setting-up.vpc-peering)
+ [Step 6: Create or Modify an Oracle DB Instance](#oracle-kerberos-setting-up.create-modify)
+ [Step 7: Create Kerberos Authentication Oracle Logins](#oracle-kerberos-setting-up.create-logins)
+ [Step 8: Configure an Oracle Client](#oracle-kerberos-setting-up.configure-oracle-client)

## Step 1: Create a Directory Using the AWS Managed Microsoft AD<a name="oracle-kerberos-setting-up.create-directory"></a>

 AWS Directory Service creates a fully managed Active Directory in the AWS Cloud\. When you create an AWS Managed Microsoft AD directory, AWS Directory Service creates two domain controllers and Domain Name System \(DNS\) servers on your behalf\. The directory servers are created in different subnets in a VPC\. This redundancy helps make sure that your directory remains accessible even if a failure occurs\. 

 When you create an AWS Managed Microsoft AD directory, AWS Directory Service performs the following tasks on your behalf: 
+  Sets up an Active Directory within the VPC\. 
+  Creates a directory administrator account with the user name Admin and the specified password\. You use this account to manage your directory\. 
**Note**  
 Be sure to save this password\. AWS Directory Service doesn't store it\. You can reset it, but you can't retrieve it\. 
+  Creates a security group for the directory controllers\. 

 When you launch an AWS Managed Microsoft AD, AWS creates an Organizational Unit \(OU\) that contains all of your directory's objects\. This OU has the NetBIOS name that you typed when you created your directory and is located in the domain root\. The domain root is owned and managed by AWS\. 

 The Admin account that was created with your AWS Managed Microsoft AD directory has permissions for the most common administrative activities for your OU: 
+  Create, update, or delete users 
+  Add resources to your domain such as file or print servers, and then assign permissions for those resources to users in your OU 
+  Create additional OUs and containers 
+  Delegate authority 
+  Restore deleted objects from the Active Directory Recycle Bin 
+  Run AD and DNS Windows PowerShell modules on the Active Directory Web Service 

 The Admin account also has rights to perform the following domain\-wide activities: 
+  Manage DNS configurations \(add, remove, or update records, zones, and forwarders\) 
+  View DNS event logs 
+  View security event logs 

**To create a directory with AWS Managed Microsoft AD**

1. Sign in to the AWS Management Console and open the AWS Directory Service console at [https://console\.aws\.amazon\.com/directoryservicev2/](https://console.aws.amazon.com/directoryservicev2/)\.

1.  In the navigation pane, choose **Directories** and choose **Set up Directory**\. 

1.  Choose **AWS Managed Microsoft AD**\. AWS Managed Microsoft AD is the only option that you can currently use with Amazon RDS\. 

1.  Enter the following information:   
**Directory DNS name**  
 The fully qualified name for the directory, such as **corp\.example\.com**\.   
**Directory NetBIOS name**  
 The short name for the directory, such as **CORP**\.   
**Directory description**  
 \(Optional\) A description for the directory\.   
**Admin password**  
 The password for the directory administrator\. The directory creation process creates an administrator account with the user name Admin and this password\.   
 The directory administrator password and can't include the word "admin\." The password is case\-sensitive and must be 8–64 characters in length\. It must also contain at least one character from three of the following four categories:   
   +  Lowercase letters \(a–z\) 
   +  Uppercase letters \(A–Z\) 
   +  Numbers \(0–9\) 
   +  Non\-alphanumeric characters \(\~\!@\#$%^&\*\_\-\+=`\|\\\(\)\{\}\[\]:;"'<>,\.?/\)   
**Confirm password**  
 The administrator password retyped\. 

1. Choose **Next**\.

1.  Enter the following information in the **Networking** section and then choose **Next**:   
**VPC**  
 The VPC for the directory\. Create the Oracle DB instance in this same VPC\.   
**Subnets**  
 Subnets for the directory servers\. The two subnets must be in different Availability Zones\. 

1.  Review the directory information and make any necessary changes\. When the information is correct, choose **Create directory**\.   
![\[Directory details page during creation\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/WinAuth2.png)

 It takes several minutes for the directory to be created\. When it has been successfully created, the **Status** value changes to **Active**\. 

 To see information about your directory, choose the directory name in the directory listing\. Note the **Directory ID** value because you need this value when you create or modify your Oracle DB instance\. 

![\[Directory details page\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/WinAuth3.png)

## Step 2: Create a Trust<a name="oracle-kerberos-setting-up.create-forest-trust"></a>

If you plan to use AWS Managed Microsoft AD only, move on to [Step 3: Create an IAM Role for Use by Amazon RDS](#oracle-kerberos-setting-up.CreateIAMRole)\.

To get Kerberos authentication using an on\-premises or self\-hosted Microsoft Active Directory, create a forest trust or external trust\. The trust can be one\-way or two\-way\. For more information about setting up forest trusts using AWS Directory Service, see [When to Create a Trust Relationship](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/ms_ad_setup_trust.html) in the *AWS Directory Service Administration Guide*\.

## Step 3: Create an IAM Role for Use by Amazon RDS<a name="oracle-kerberos-setting-up.CreateIAMRole"></a>

For Amazon RDS to call AWS Directory Service for you, an IAM role that uses the managed IAM policy `AmazonRDSDirectoryServiceAccess` is required\. This role allows Amazon RDS to make calls to the AWS Directory Service\.

When a DB instance is created using the AWS Management Console and the console user has the `iam:CreateRole` permission, the console creates this role automatically\. In this case, the role name is `rds-directoryservice-kerberos-access-role`\. Otherwise, you must create the IAM role manually\. When you create this IAM role, choose `Directory Service`, and attach the AWS managed policy `AmazonRDSDirectoryServiceAccess` to it\.

For more information about creating IAM roles for a service, see [Creating a Role to Delegate Permissions to an AWS Service](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-service.html) in the *IAM User Guide*\.

**Note**  
The IAM role used for Windows Authentication for RDS for Microsoft SQL Server can't be used for RDS for Oracle\.

Optionally, you can create policies with the required permissions instead of using the managed IAM policy `AmazonRDSDirectoryServiceAccess`\. In this case, the IAM role must have the following IAM trust policy\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "",
      "Effect": "Allow",
      "Principal": {
        "Service": [
          "directoryservice.rds.amazonaws.com",
          "rds.amazonaws.com"
        ]
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```

The role must also have the following IAM role policy\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Action": [
        "ds:DescribeDirectories",
        "ds:AuthorizeApplication",
        "ds:UnauthorizeApplication",
        "ds:GetAuthorizedApplicationDetails"
      ],
    "Effect": "Allow",
    "Resource": "*"
    }
  ]
}
```

## Step 4: Create and Configure Users<a name="oracle-kerberos-setting-up.create-users"></a>

 You can create users with the Active Directory Users and Computers tool, which is one of the Active Directory Domain Services and Active Directory Lightweight Directory Services tools\. In this case, *users* are individual people or entities that have access to your directory\. 

To create users in an AWS Directory Service directory, you must be connected to a Windows\-based Amazon EC2 instance that is a member of the AWS Directory Service directory\. At the same time, you must be logged in as a user that has privileges to create users\. For more information, see [Create a User](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/ms_ad_manage_users_groups_create_user.html) in the *AWS Directory Service Administration Guide*\.

## Step 5: Enable Cross\-VPC Traffic Between the Directory and the DB Instance<a name="oracle-kerberos-setting-up.vpc-peering"></a>

If you plan to locate the directory and the DB instance in the same VPC, skip this step and move on to [Step 6: Create or Modify an Oracle DB Instance](#oracle-kerberos-setting-up.create-modify)\.

If you plan to locate the directory and the DB instance in different VPCs, configure cross\-VPC traffic using VPC peering or [AWS Transit Gateway](https://docs.aws.amazon.com/vpc/latest/tgw/what-is-transit-gateway.html)\.

The following procedure enables traffic between VPCs using VPC peering\. Follow the instructions in [What is VPC Peering?](https://docs.aws.amazon.com/vpc/latest/peering/Welcome.html) in the *Amazon Virtual Private Cloud Peering Guide*\.

**To enable cross\-VPC traffic using VPC peering**

1. Set up appropriate VPC routing rules to ensure that network traffic can flow both ways\.

1. Ensure that the DB instance's security group can receive inbound traffic from the directory's security group\.

1. Ensure that there is no network access control list \(ACL\) rule to block traffic\.

If a different AWS account owns the directory, you must share the directory\.

**To share the directory between AWS accounts**

1. Start sharing the directory with the AWS account that the DB instance will be created in by following the instructions in [Tutorial: Sharing Your AWS Managed Microsoft AD Directory for Seamless EC2 Domain\-Join](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/ms_ad_tutorial_directory_sharing.html) in the *AWS Directory Service Administration Guide*\.

1. Sign in to the AWS Directory Service console using the account for the DB instance, and ensure that the domain has the `SHARED` status before proceeding\.

1. While signed into the AWS Directory Service console using the account for the DB instance, note the **Directory ID** value\. You use this directory ID to join the DB instance to the domain\.

## Step 6: Create or Modify an Oracle DB Instance<a name="oracle-kerberos-setting-up.create-modify"></a>

Create or modify an Oracle DB instance for use with your directory\. You can use the console, CLI, or RDS API to associate a DB instance with a directory\. You can do this in one of the following ways:
+ Create a new Oracle DB instance using the console, the [ create\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html) CLI command, or the [CreateDBInstance](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html) RDS API operation\.

  For instructions, see [Creating an Amazon RDS DB Instance](USER_CreateDBInstance.md)\.
+ Modify an existing Oracle DB instance using the console, the [modify\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) CLI command, or the [ModifyDBInstance](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html) RDS API operation\.

  For instructions, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md)\.
+ Restore an Oracle DB instance from a DB snapshot using the console, the [ restore\-db\-instance\-from\-db\-snapshot](https://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-instance-from-db-snapshot.html) CLI command, or the [ RestoreDBInstanceFromDBSnapshot](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBInstanceFromDBSnapshot.html) RDS API operation\.

  For instructions, see [Restoring from a DB Snapshot](USER_RestoreFromSnapshot.md)\.
+ Restore an Oracle DB instance to a point\-in\-time using the console, the [ restore\-db\-instance\-to\-point\-in\-time](https://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-instance-to-point-in-time.html) CLI command, or the [ RestoreDBInstanceToPointInTime](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBInstanceToPointInTime.html) RDS API operation\.

  For instructions, see [Restoring a DB Instance to a Specified Time](USER_PIT.md)\.

Kerberos authentication is only supported for Oracle DB instances in a VPC\. The DB instance can be in the same VPC as the directory, or in a different VPC\. The DB instance must use a security group that allows egress within the directory's VPC so the DB instance can communicate with the directory\.

When you use the console to create a DB instance, choose **Password and Kerberos authentication** in the **Database authentication** section\. Choose **Browse Directory** and then select the directory, or choose **Create a new directory**\.

![\[Kerberos authentication setting when creating a DB instance\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/kerberos-authentication.png)

When you use the console to modify or restore a DB instance, choose the directory in the **Kerberos authentication** section, or choose **Create a new directory**\.

![\[Kerberos authentication setting when modifying or restoring a DB instance\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/kerberos-auth-modify-restore.png)

When you use the AWS CLI, the following parameters are required for the DB instance to be able to use the directory that you created:
+ For the `--domain` parameter, use the domain identifier \("d\-\*" identifier\) generated when you created the directory\.
+ For the `--domain-iam-role-name` parameter, use the role you created that uses the managed IAM policy `AmazonRDSDirectoryServiceAccess`\.

For example, the following CLI command modifies a DB instance to use a directory\.

For Linux, macOS, or Unix:

```
aws rds modify-db-instance \
    --db-instance-identifier mydbinstance \
    --domain d-ID \
    --domain-iam-role-name role-name
```

For Windows:

```
aws rds modify-db-instance ^
    --db-instance-identifier mydbinstance ^
    --domain d-ID ^
    --domain-iam-role-name role-name
```

**Important**  
If you modify a DB instance to enable Kerberos authentication, reboot the DB instance after making the change\.

## Step 7: Create Kerberos Authentication Oracle Logins<a name="oracle-kerberos-setting-up.create-logins"></a>

 Use the Amazon RDS master user credentials to connect to the Oracle DB instance as you do any other DB instance\. The DB instance is joined to the AWS Managed Microsoft AD domain\. Thus, you can provision Oracle logins and users from the Microsoft Active Directory users and groups in your domain\. To manage database permissions, you grant and revoke standard Oracle permissions to these logins\. 

To allow a Microsoft Active Directory user to authenticate with Oracle, use the Amazon RDS master user credentials\. You use these credentials to connect to the Oracle DB instance as you do any other DB instance\. After you're logged in, create an externally authenticated user in Oracle\.

```
CREATE USER "KRBUSER@CORP.EXAMPLE.COM" IDENTIFIED EXTERNALLY; 
GRANT CREATE SESSION TO "KRBUSER@CORP.EXAMPLE.COM";
```

 Replace `KRBUSER@CORP.EXAMPLE.COM `with the user name and domain name\. Users \(both humans and applications\) from your domain can now connect to the RDS Oracle instance from a domain joined client machine using Kerberos authentication\. 

## Step 8: Configure an Oracle Client<a name="oracle-kerberos-setting-up.configure-oracle-client"></a>

To configure an Oracle client, meet the following requirements:
+ Create a krb5\.conf file \(or equivalent\) to point to the domain\. Configure the Oracle client to use this krb5\.conf file\.
+ Verify that traffic can flow between the client host and AWS Directory Service over DNS port 53 and Kerberos ports \(88 and 464 for managed AWS Directory Service\) over TCP/UDP\.
+ Verify that traffic can flow between the client host and the DB instance over the database port\.

The following is sample krb5\.conf content for AWS Managed Microsoft AD:

```
[libdefaults]
 default_realm = EXAMPLE.COM
 default_ccache_name = /tmp/kerbcache
[realms]
 EXAMPLE.COM = {
  kdc = example.com
  admin_server = example.com
 }
[domain_realm]
 .example.com = EXAMPLE.COM
 example.com = EXAMPLE.COM
```

The following is sample krb5\.conf content for on\-premise Microsoft AD:

```
[libdefaults]
 default_realm = EXAMPLE.COM
 default_ccache_name = /tmp/kerbcache
[realms]
 EXAMPLE.COM = {
  kdc = example.com
  admin_server = example.com
 }
 ONPREM.COM = {
  kdc = onprem.com
  admin_server = onprem.com
 }
[domain_realm]
 .example.com = EXAMPLE.COM
 example.com = EXAMPLE.COM
 .onprem.com = ONPREM.COM
 onprem.com = ONPREM.COM
```

The following is sample sqlnet\.ora content for a SQL\*Plus configuration:

```
SQLNET.AUTHENTICATION_SERVICES=(KERBEROS5PRE,KERBEROS5)
SQLNET.KERBEROS5_CONF=path_to_krb5.conf_file
```

For an example of a SQL Developer configuration, see [Document 1609359\.1](https://support.oracle.com/epmos/faces/DocumentDisplay?id=1609359.1) from Oracle Support\.