# Setting Up Kerberos Authentication for PostgreSQL DB Instances<a name="postgresql-kerberos-setting-up"></a>

You use AWS Directory Service for Microsoft Active Directory \(AWS Managed Microsoft AD\) to set up Kerberos authentication for a PostgreSQL DB instance\. To set up Kerberos authentication, take the following steps\. 

**Topics**
+ [Step 1: Create a Directory Using AWS Managed Microsoft AD](#postgresql-kerberos-setting-up.create-directory)
+ [Step 2: \(Optional\) Create a Trust for an On\-Premises Active Directory](#postgresql-kerberos-setting-up.create-trust)
+ [Step 3: Create an IAM Role for Amazon RDS to Access the AWS Directory Service](#postgresql-kerberos-setting-up.CreateIAMRole)
+ [Step 4: Create and Configure Users](#postgresql-kerberos-setting-up.create-users)
+ [Step 5: Enable Cross\-VPC Traffic Between the Directory and the DB Instance](#postgresql-kerberos-setting-up.vpc-peering)
+ [Step 6: Create or Modify a PostgreSQL DB Instance](#postgresql-kerberos-setting-up.create-modify)
+ [Step 7: Create Kerberos Authentication PostgreSQL Logins](#postgresql-kerberos-setting-up.create-logins)
+ [Step 8: Configure a PostgreSQL Client](#postgresql-kerberos-setting-up.configure-client)

## Step 1: Create a Directory Using AWS Managed Microsoft AD<a name="postgresql-kerberos-setting-up.create-directory"></a>

AWS Directory Service creates a fully managed Active Directory in the AWS Cloud\. When you create an AWS Managed Microsoft AD directory, AWS Directory Service creates two domain controllers and DNS servers for you\. The directory servers are created in different subnets in a VPC\. This redundancy helps make sure that your directory remains accessible even if a failure occurs\. 

 When you create an AWS Managed Microsoft AD directory, AWS Directory Service performs the following tasks on your behalf: 
+  Sets up an Active Directory within your VPC\. 
+  Creates a directory administrator account with the user name `Admin` and the specified password\. You use this account to manage your directory\. 
**Important**  
Make sure to save this password\. AWS Directory Service doesn't store this password, and it can't be retrieved or reset\.
+ Creates a security group for the directory controllers\.

When you launch AWS Directory Service for Microsoft Active Directory, AWS creates an Organizational Unit \(OU\) that contains all of your directory's objects\. This OU, which has the NetBIOS name that you entered when you created your directory, is located in the domain root\. The domain root is owned and managed by AWS\. 

 The `Admin` account that was created with your AWS Managed Microsoft AD directory has permissions for the most common administrative activities for your OU: 
+  Create, update, or delete users
+  Add resources to your domain such as file or print servers, and then assign permissions for those resources to users in your OU 
+  Create additional OUs and containers 
+  Delegate authority 
+  Restore deleted objects from the Active Directory Recycle Bin 
+  Run Active Directory and Domain Name Service \(DNS\) modules for Windows PowerShell on the Active Directory Web Service 

 The `Admin` account also has rights to perform the following domain\-wide activities: 
+  Manage DNS configurations \(add, remove, or update records, zones, and forwarders\) 
+  View DNS event logs 
+  View security event logs 

**To create a directory with AWS Managed Microsoft AD**

1.  In the [AWS Directory Service console](https://console.aws.amazon.com/directoryservicev2/) navigation pane, choose **Directories**, and then choose **Set up directory**\. 

1. Choose **AWS Managed Microsoft AD**\. AWS Managed Microsoft AD is the only option currently supported for use with Amazon RDS\. 

1. Choose **Next**\.

1. On the **Enter directory information** page, provide the following information:   
**Edition**  
 Choose the edition that meets your requirements\.  
**Directory DNS name**  
 The fully qualified name for the directory, such as **corp\.example\.com**\.   
**Directory NetBIOS name**  
 An optional short name for the directory, such as `CORP`\.   
**Directory description**  
 An optional description for the directory\.   
**Admin password**  
 The password for the directory administrator\. The directory creation process creates an administrator account with the user name `Admin` and this password\.   
 The directory administrator password can't include the word "admin\." The password is case\-sensitive and must be 8–64 characters in length\. It must also contain at least one character from three of the following four categories:   
   +  Lowercase letters \(a–z\) 
   +  Uppercase letters \(A–Z\) 
   +  Numbers \(0–9\) 
   +  Nonalphanumeric characters \(\~\!@\#$%^&\*\_\-\+=`\|\\\(\)\{\}\[\]:;"'<>,\.?/\)   
**Confirm password**  
 Retype the administrator password\.   
Make sure that you save this password\. AWS Directory Service doesn't store this password, and it can't be retrieved or reset\.

1. Choose **Next**\.

1. On the **Choose VPC and subnets** page, provide the following information:  
**VPC**  
 Choose the VPC for the directory\. You can create the PostgreSQL DB instance in this same VPC or in a different VPC\.   
**Subnets**  
 Choose the subnets for the directory servers\. The two subnets must be in different Availability Zones\. 

1. Choose **Next**\.

1.  Review the directory information\. If changes are needed, choose **Previous** and make the changes\. When the information is correct, choose **Create directory**\.   
![\[Directory details page\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/WinAuth2.png)

 It takes several minutes for the directory to be created\. When it has been successfully created, the **Status** value changes to **Active**\. 

 To see information about your directory, choose the directory ID in the directory listing\. Make a note of the **Directory ID** value\. You need this value when you create or modify your PostgreSQL DB instance\. 

![\[graphic of details page\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/WinAuth3.png)

## Step 2: \(Optional\) Create a Trust for an On\-Premises Active Directory<a name="postgresql-kerberos-setting-up.create-trust"></a>

If you don't plan to use your own on\-premises Microsoft Active Directory, skip to [Step 3: Create an IAM Role for Amazon RDS to Access the AWS Directory Service ](#postgresql-kerberos-setting-up.CreateIAMRole)\.

To get Kerberos authentication using your on\-premises Active Directory, you need to create a trusting domain relationship using a forest trust between your on\-premises Microsoft Active Directory and the AWS Managed Microsoft AD directory \(created in [Step 1: Create a Directory Using AWS Managed Microsoft AD](#postgresql-kerberos-setting-up.create-directory)\)\. The trust can be one\-way, where the AWS Managed Microsoft AD directory trusts the on\-premises Microsoft Active Directory\. The trust can also be two\-way, where both Active Directories trust each other\. For more information about setting up trusts using AWS Directory Service, see [When to Create a Trust Relationship](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/ms_ad_setup_trust.html) in the *AWS Directory Service Administration Guide*\.

**Note**  
If you use an on\-premises Microsoft Active Directory, DB instance endpoints can't be used by Windows clients\.

Make sure that your on\-premises Microsoft Active Directory domain name includes a DNS suffix routing that corresponds to the newly created trust relationship\. The following screenshot shows an example\.

![\[DNS routing corresponds to the created trust\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/kerberos-auth-trust.png)

## Step 3: Create an IAM Role for Amazon RDS to Access the AWS Directory Service<a name="postgresql-kerberos-setting-up.CreateIAMRole"></a>

For Amazon RDS to call AWS Directory Service for you, an IAM role that uses the managed IAM policy `AmazonRDSDirectoryServiceAccess` is required\. This role allows Amazon RDS to make calls to AWS Directory Service\.

When a DB instance is created using the AWS Management Console and the console user has the `iam:CreateRole` permission, the console creates this role automatically\. In this case, the role name is `rds-directoryservice-kerberos-access-role`\. Otherwise, create the IAM role manually\. When you create this IAM role, choose `Directory Service`, and attach the AWS managed policy `AmazonRDSDirectoryServiceAccess` to it\.

For more information about creating IAM roles for a service, see [Creating a Role to Delegate Permissions to an AWS Service](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-service.html) in the *IAM User Guide*\.

**Note**  
The IAM role used for Windows Authentication for RDS for Microsoft SQL Server can't be used for Amazon RDS for PostgreSQL\.

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

## Step 4: Create and Configure Users<a name="postgresql-kerberos-setting-up.create-users"></a>

 You can create users by using the Active Directory Users and Computers tool, which is one of the Active Directory Domain Services and Active Directory Lightweight Directory Services tools\. In this case, *users* are individual people or entities that have access to your directory\. 

To create users in an AWS Directory Service directory, you must be connected to a Windows\-based Amazon EC2 instance\. Also, this EC2 instance must be a member of the AWS Directory Service directory\. At the same time, you must be logged in as a user that has privileges to create users\. For more information, see [Create a User](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/ms_ad_manage_users_groups_create_user.html) in the *AWS Directory Service Administration Guide*\.

## Step 5: Enable Cross\-VPC Traffic Between the Directory and the DB Instance<a name="postgresql-kerberos-setting-up.vpc-peering"></a>

If you plan to locate the directory and the DB instance in the same VPC, skip this step and move on to [ Step 6: Create or Modify a PostgreSQL DB Instance ](#postgresql-kerberos-setting-up.create-modify)\.

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

## Step 6: Create or Modify a PostgreSQL DB Instance<a name="postgresql-kerberos-setting-up.create-modify"></a>

Create or modify a PostgreSQL DB instance for use with your directory\. You can use the console, CLI, or RDS API to associate a DB instance with a directory\. You can do this in one of the following ways:
+   Create a new PostgreSQL DB instance using the console, the [ create\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html) CLI command, or the [CreateDBInstance](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html) RDS API operation\. For instructions, see [Creating an Amazon RDS DB Instance](USER_CreateDBInstance.md)\.
+   Modify an existing PostgreSQL DB instance using the console, the [modify\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) CLI command, or the [ModifyDBInstance](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html) RDS API operation\. For instructions, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md)\. 
+   Restore a PostgreSQL DB instance from a DB snapshot using the console, the [restore\-db\-instance\-from\-db\-snapshot](https://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-instance-from-db-snapshot.html) CLI command, or the [ RestoreDBInstanceFromDBSnapshot](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBInstanceFromDBSnapshot.html) RDS API operation\. For instructions, see [Restoring from a DB Snapshot](USER_RestoreFromSnapshot.md)\. 
+   Restore a PostgreSQL DB instance to a point\-in\-time using the console, the [ restore\-db\-instance\-to\-point\-in\-time](https://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-instance-to-point-in-time.html) CLI command, or the [ RestoreDBInstanceToPointInTime](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBInstanceToPointInTime.html) RDS API operation\. For instructions, see [Restoring a DB Instance to a Specified Time](USER_PIT.md)\. 

Kerberos authentication is only supported for PostgreSQL DB instancesin a VPC\. The DB instance can be in the same VPC as the directory, or in a different VPC\. The DB instance must use a security group that allows egress within the directory's VPC so the DB instance can communicate with the directory\.

When you use the console to create a DB instance, choose **Password and Kerberos authentication** in the **Database authentication** section\. Choose **Browse Directory** and then select the directory, or choose **Create a new directory**\.

![\[Kerberos authentication setting when creating a DB instance\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/kerberos-authentication.png)

When you use the console to modify or restore a DB instance, choose the directory in the **Kerberos authentication** section, or choose **Create a new directory**\.

![\[Kerberos authentication setting when modifying or restoring a DB instance\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/kerberos-auth-modify-restore.png)

When you use the AWS CLI, the following parameters are required for the DB instance to be able to use the directory that you created:
+ For the `--domain` parameter, use the domain identifier \("d\-\*" identifier\) generated when you created the directory\.
+ For the `--domain-iam-role-name` parameter, use the role you created that uses the managed IAM policy `AmazonRDSDirectoryServiceAccess`\.

For example, the following CLI command modifies a DB instance to use a directory\.

```
aws rds modify-db-instance --db-instance-identifier mydbinstance --domain d-Directory-ID --domain-iam-role-name role-name 
```

**Important**  
If you modify a DB instance to enable Kerberos authentication, reboot the DB instance after making the change\.

## Step 7: Create Kerberos Authentication PostgreSQL Logins<a name="postgresql-kerberos-setting-up.create-logins"></a>

Next, use the RDS master user credentials to connect to the PostgreSQL DB instance as you do with any other DB instance\. The DB instance is joined to the AWS Managed Microsoft AD domain\. Thus, you can provision PostgreSQL logins and users from the Microsoft Active Directory users and groups in your domain\. To manage database permissions, you grant and revoke standard PostgreSQL permissions to these logins\. 

To allow an Active Directory user to authenticate with PostgreSQL, use the RDS master user credentials\. You use these credentials to connect to the PostgreSQL DB instance as you do with any other DB instance\. After you're logged in, create an externally authenticated user in PostgreSQL and grant the `rds_ad` role to this user\.

```
CREATE USER "username@CORP.EXAMPLE.COM" WITH LOGIN; 
GRANT rds_ad TO "username@CORP.EXAMPLE.COM";
```

 Replace `username ` with the user name and include the domain name in uppercase\. Users \(both humans and applications\) from your domain can now connect to the RDS PostgreSQL instance from a domain\-joined client machine using Kerberos authentication\. 

## Step 8: Configure a PostgreSQL Client<a name="postgresql-kerberos-setting-up.configure-client"></a>

To configure a PostgreSQL client, take the following steps:
+ Create a krb5\.conf file \(or equivalent\) to point to the domain\. 
+ Verify that traffic can flow between the client host and AWS Directory Service\. Use a network utility such as Netcat for the following:
  + Verify traffic over DNS for port 53\.
  + Verify traffic over TCP/UDP for port 53 and for Kerberos, which includes ports 88 and 464 for AWS Directory Service\.
+ Verify that traffic can flow between the client host and the DB instance over the database port\. For example, use psql to connect and access the database\.

The following is sample krb5\.conf content for AWS Managed Microsoft AD\.

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

The following is sample krb5\.conf content for an on\-premises Microsoft Active Directory\.

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
 .rds.amazonaws.com = EXAMPLE.COM
 .amazonaws.com.cn = EXAMPLE.COM
 .amazon.com = EXAMPLE.COM
```