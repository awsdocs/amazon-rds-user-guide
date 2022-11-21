# Setting up Kerberos authentication for PostgreSQL DB instances<a name="postgresql-kerberos-setting-up"></a>

You use AWS Directory Service for Microsoft Active Directory \(AWS Managed Microsoft AD\) to set up Kerberos authentication for a PostgreSQL DB instance\. To set up Kerberos authentication, take the following steps\. 

**Topics**
+ [Step 1: Create a directory using AWS Managed Microsoft AD](#postgresql-kerberos-setting-up.create-directory)
+ [Step 2: \(Optional\) Create a trust relationship between your on\-premises Active Directory and AWS Directory Service](#postgresql-kerberos-setting-up.create-trust)
+ [Step 3: Create an IAM role for Amazon RDS to access the AWS Directory Service](#postgresql-kerberos-setting-up.CreateIAMRole)
+ [Step 4: Create and configure users](#postgresql-kerberos-setting-up.create-users)
+ [Step 5: Enable cross\-VPC traffic between the directory and the DB instance](#postgresql-kerberos-setting-up.vpc-peering)
+ [Step 6: Create or modify a PostgreSQL DB instance](#postgresql-kerberos-setting-up.create-modify)
+ [Step 7: Create PostgreSQL users for your Kerberos principals](#postgresql-kerberos-setting-up.create-logins)
+ [Step 8: Configure a PostgreSQL client](#postgresql-kerberos-setting-up.configure-client)

## Step 1: Create a directory using AWS Managed Microsoft AD<a name="postgresql-kerberos-setting-up.create-directory"></a>

AWS Directory Service creates a fully managed Active Directory in the AWS Cloud\. When you create an AWS Managed Microsoft AD directory, AWS Directory Service creates two domain controllers and DNS servers for you\. The directory servers are created in different subnets in a VPC\. This redundancy helps make sure that your directory remains accessible even if a failure occurs\. 

 When you create an AWS Managed Microsoft AD directory, AWS Directory Service performs the following tasks on your behalf: 
+ Sets up an Active Directory within your VPC\. 
+ Creates a directory administrator account with the user name `Admin` and the specified password\. You use this account to manage your directory\. 
**Important**  
Make sure to save this password\. AWS Directory Service doesn't store this password, and it can't be retrieved or reset\.
+ Creates a security group for the directory controllers\. The security group must permit communication with the PostgreSQL DB instance\.

When you launch AWS Directory Service for Microsoft Active Directory, AWS creates an Organizational Unit \(OU\) that contains all of your directory's objects\. This OU, which has the NetBIOS name that you entered when you created your directory, is located in the domain root\. The domain root is owned and managed by AWS\. 

 The `Admin` account that was created with your AWS Managed Microsoft AD directory has permissions for the most common administrative activities for your OU: 
+ Create, update, or delete users
+ Add resources to your domain such as file or print servers, and then assign permissions for those resources to users in your OU 
+ Create additional OUs and containers 
+ Delegate authority 
+ Restore deleted objects from the Active Directory Recycle Bin 
+ Run Active Directory and Domain Name Service \(DNS\) modules for Windows PowerShell on the Active Directory Web Service 

The `Admin` account also has rights to perform the following domain\-wide activities: 
+ Manage DNS configurations \(add, remove, or update records, zones, and forwarders\) 
+ View DNS event logs 
+ View security event logs 

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

![\[Image of details page\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/WinAuth3.png)

## Step 2: \(Optional\) Create a trust relationship between your on\-premises Active Directory and AWS Directory Service<a name="postgresql-kerberos-setting-up.create-trust"></a>

If you don't plan to use your own on\-premises Microsoft Active Directory, skip to [Step 3: Create an IAM role for Amazon RDS to access the AWS Directory Service](#postgresql-kerberos-setting-up.CreateIAMRole)\.

To get Kerberos authentication using your on\-premises Active Directory, you need to create a trusting domain relationship using a forest trust between your on\-premises Microsoft Active Directory and the AWS Managed Microsoft AD directory \(created in [Step 1: Create a directory using AWS Managed Microsoft AD](#postgresql-kerberos-setting-up.create-directory)\)\. The trust can be one\-way, where the AWS Managed Microsoft AD directory trusts the on\-premises Microsoft Active Directory\. The trust can also be two\-way, where both Active Directories trust each other\. For more information about setting up trusts using AWS Directory Service, see [When to create a trust relationship](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/ms_ad_setup_trust.html) in the *AWS Directory Service Administration Guide*\.

**Note**  
If you use an on\-premises Microsoft Active Directory, Windows clients connect using the domain name of the AWS Directory Service in the endpoint rather than rds\.amazonaws\.com\. To learn more, see [Connecting to PostgreSQL with Kerberos authentication](postgresql-kerberos-connecting.md)\. 

Make sure that your on\-premises Microsoft Active Directory domain name includes a DNS suffix routing that corresponds to the newly created trust relationship\. The following screenshot shows an example\.

![\[DNS routing corresponds to the created trust\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/kerberos-auth-trust.png)

## Step 3: Create an IAM role for Amazon RDS to access the AWS Directory Service<a name="postgresql-kerberos-setting-up.CreateIAMRole"></a>

For Amazon RDS to call AWS Directory Service for you, your AWS account needs an IAM role that uses the managed IAM policy `AmazonRDSDirectoryServiceAccess`\. This role allows Amazon RDS to make calls to AWS Directory Service\. 

When you create a DB instance using the AWS Management Console and your console user account has the `iam:CreateRole` permission, the console creates the needed IAM role automatically\. In this case, the role name is `rds-directoryservice-kerberos-access-role`\. Otherwise, you must create the IAM role manually\. When you create this IAM role, choose `Directory Service`, and attach the AWS managed policy `AmazonRDSDirectoryServiceAccess` to it\. 

For more information about creating IAM roles for a service, see [Creating a role to delegate permissions to an AWS service](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-service.html) in the *IAM User Guide*\.

**Note**  
The IAM role used for Windows Authentication for RDS for Microsoft SQL Server can't be used for Amazon RDS for PostgreSQL\.

As an alternative to using the `AmazonRDSDirectoryServiceAccess` managed policy, you can create policies with the required permissions\. In this case, the IAM role must have the following IAM trust policy\.

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

## Step 4: Create and configure users<a name="postgresql-kerberos-setting-up.create-users"></a>

 You can create users by using the Active Directory Users and Computers tool\. This is one of the Active Directory Domain Services and Active Directory Lightweight Directory Services tools\. For more information, see [Add Users and Computers to the Active Directory domain](https://learn.microsoft.com/en-us/troubleshoot/windows-server/identity/create-an-active-directory-server#add-users-and-computers-to-the-active-directory-domain) in the Microsoft documentation\. In this case, users are individuals or other entities, such as their computers that are part of the domain and whose identities are being maintained in the directory\. 

To create users in an AWS Directory Service directory, you must be connected to a Windows\-based Amazon EC2 instance that's a member of the AWS Directory Service directory\. At the same time, you must be logged in as a user that has privileges to create users\. For more information, see [Create a user](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/ms_ad_manage_users_groups_create_user.html) in the *AWS Directory Service Administration Guide*\.

## Step 5: Enable cross\-VPC traffic between the directory and the DB instance<a name="postgresql-kerberos-setting-up.vpc-peering"></a>

If you plan to locate the directory and the DB instance in the same VPC, skip this step and move on to [ Step 6: Create or modify a PostgreSQL DB instance](#postgresql-kerberos-setting-up.create-modify)\.

If you plan to locate the directory and the DB instance in different VPCs, configure cross\-VPC traffic using VPC peering or [AWS Transit Gateway](https://docs.aws.amazon.com/vpc/latest/tgw/what-is-transit-gateway.html)\.

The following procedure enables traffic between VPCs using VPC peering\. Follow the instructions in [What is VPC peering?](https://docs.aws.amazon.com/vpc/latest/peering/Welcome.html) in the *Amazon Virtual Private Cloud Peering Guide*\.

**To enable cross\-VPC traffic using VPC peering**

1. Set up appropriate VPC routing rules to ensure that network traffic can flow both ways\.

1. Ensure that the DB instance security group can receive inbound traffic from the directory security group\.

1. Ensure that there is no network access control list \(ACL\) rule to block traffic\.

If a different AWS account owns the directory, you must share the directory\.

**To share the directory between AWS accounts**

1. Start sharing the directory with the AWS account that the DB instance will be created in by following the instructions in [Tutorial: Sharing your AWS Managed Microsoft AD directory for seamless EC2 Domain\-join](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/ms_ad_tutorial_directory_sharing.html) in the *AWS Directory Service Administration Guide*\.

1. Sign in to the AWS Directory Service console using the account for the DB instance, and ensure that the domain has the `SHARED` status before proceeding\.

1. While signed into the AWS Directory Service console using the account for the DB instance, note the **Directory ID** value\. You use this directory ID to join the DB instance to the domain\.

## Step 6: Create or modify a PostgreSQL DB instance<a name="postgresql-kerberos-setting-up.create-modify"></a>

Create or modify a PostgreSQL DB instance for use with your directory\. You can use the console, CLI, or RDS API to associate a DB instance with a directory\. You can do this in one of the following ways:
+   Create a new PostgreSQL DB instance using the console, the [ create\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html) CLI command, or the [CreateDBInstance](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html) RDS API operation\. For instructions, see [Creating an Amazon RDS DB instance](USER_CreateDBInstance.md)\.
+   Modify an existing PostgreSQL DB instance using the console, the [modify\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) CLI command, or the [ModifyDBInstance](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html) RDS API operation\. For instructions, see [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)\. 
+   Restore a PostgreSQL DB instance from a DB snapshot using the console, the [restore\-db\-instance\-from\-db\-snapshot](https://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-instance-from-db-snapshot.html) CLI command, or the [ RestoreDBInstanceFromDBSnapshot](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBInstanceFromDBSnapshot.html) RDS API operation\. For instructions, see [Restoring from a DB snapshot](USER_RestoreFromSnapshot.md)\. 
+   Restore a PostgreSQL DB instance to a point\-in\-time using the console, the [ restore\-db\-instance\-to\-point\-in\-time](https://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-instance-to-point-in-time.html) CLI command, or the [ RestoreDBInstanceToPointInTime](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBInstanceToPointInTime.html) RDS API operation\. For instructions, see [Restoring a DB instance to a specified time](USER_PIT.md)\. 

Kerberos authentication is only supported for PostgreSQL DB instances in a VPC\. The DB instance can be in the same VPC as the directory, or in a different VPC\. The DB instance must use a security group that allows ingress and egress within the directory's VPC so the DB instance can communicate with the directory\.

### Console<a name="postgresql-kerberos-setting-up.create-modify.Console"></a>

When you use the console to create, modify, or restore a DB instance, choose **Password and Kerberos authentication** in the **Database authentication** section\. Then choose **Browse Directory**\. Select the directory or choose **Create a new directory** to use the Directory Service\.

![\[Choosing Kerberos for authentication and identifying the directory to use.\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/rpg-authentication-use-kerberos.png)

### AWS CLI<a name="postgresql-kerberos-setting-up.create-modify.CLI"></a>

When you use the AWS CLI, the following parameters are required for the DB instance to be able to use the directory that you created:
+ For the `--domain` parameter, use the domain identifier \("d\-\*" identifier\) generated when you created the directory\.
+ For the `--domain-iam-role-name` parameter, use the role you created that uses the managed IAM policy `AmazonRDSDirectoryServiceAccess`\.

For example, the following CLI command modifies a DB instance to use a directory\.

```
aws rds modify-db-instance --db-instance-identifier mydbinstance --domain d-Directory-ID --domain-iam-role-name role-name 
```

**Important**  
If you modify a DB instance to enable Kerberos authentication, reboot the DB instance after making the change\.

## Step 7: Create PostgreSQL users for your Kerberos principals<a name="postgresql-kerberos-setting-up.create-logins"></a>

At this point, your RDS for PostgreSQL DB instance is joined to the AWS Managed Microsoft AD domain\. The users that you created in the directory in [ Step 4: Create and configure users ](#postgresql-kerberos-setting-up.create-users) need to be set up as PostgreSQL database users and granted privileges to login to the database\. You do that by signing in as the database user with `rds_superuser` privileges\. For example, if you accepted the defaults when you created your RDS for PostgreSQL DB instance, you use `postgres`, as shown in the following steps\. 

**To create PostgreSQL database users for Kerberos principals**

1. Use `psql` to connect to your RDS for PostgreSQL DB instance endpoint using `psql`\. The following example uses the default `postgres` account for the `rds_superuser` role\.

   ```
   psql --host=cluster-instance-1.111122223333.aws-region.rds.amazonaws.com --port=5432 --username=postgres --password
   ```

1. Create a database user name for each Kerberos principal \(Active Directory username\) that you want to have access to the database\. Use the canonical username \(identity\) as defined in the Active Directory instance, that is, a lower\-case `alias` \(username in Active Directory\) and the upper\-case name of the Active Directory domain for that user name\. The Active Directory user name is an externally authenticated user, so use quotes around the name as shown following\.

   ```
   postgres=> CREATE USER "username@CORP.EXAMPLE.COM" WITH LOGIN;
   CREATE ROLE
   ```

1. Grant the `rds_ad` role to the database user\.

   ```
   postgres=> GRANT rds_ad TO "username@CORP.EXAMPLE.COM";
   GRANT ROLE
   ```

After you finish creating all the PostgreSQL users for your Active Directory user identities, users can access the RDS for PostgreSQL DB instance by using their Kerberos credentials\. 

It's assumed that the database users who authenticate using Kerberos are doing so from client machines that are members of the Active Directory domain\.

Database users that have been granted the `rds_ad` role can't also have the `rds_iam` role\. This also applies to nested memberships\. For more information, see [IAM database authentication for MariaDB, MySQL, and PostgreSQL](UsingWithRDS.IAMDBAuth.md)\. 

## Step 8: Configure a PostgreSQL client<a name="postgresql-kerberos-setting-up.configure-client"></a>

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