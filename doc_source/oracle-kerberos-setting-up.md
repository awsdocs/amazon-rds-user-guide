# Setting Up Kerberos Authentication for Oracle DB Instances<a name="oracle-kerberos-setting-up"></a>

You use AWS Directory Service for Microsoft Active Directory, also called AWS Managed Microsoft AD, to set up Kerberos authentication for an Oracle DB instance\. To set up Kerberos authentication, complete the following steps:
+ [Step 1: Create a Directory Using the AWS Managed Microsoft AD](#oracle-kerberos-setting-up.create-directory)
+ [Step 2: Create a Forest Trust](#oracle-kerberos-setting-up.create-forest-trust)
+ [Step 3: Create an IAM Role for Use by Amazon RDS](#oracle-kerberos-setting-up.CreateIAMRole)
+ [Step 4: Create and Configure Users](#oracle-kerberos-setting-up.create-users)
+ [Step 5: Configure VPC Peering](#oracle-kerberos-setting-up.vpc-peering)
+ [Step 6: Create or Modify an Oracle DB Instance](#oracle-kerberos-setting-up.create-modify)
+ [Step 7: Create Kerberos Authentication Oracle Logins](#oracle-kerberos-setting-up.create-logins)
+ [Step 8: Configure an Oracle Client](#oracle-kerberos-setting-up.configure-oracle-client)

## Step 1: Create a Directory Using the AWS Managed Microsoft AD<a name="oracle-kerberos-setting-up.create-directory"></a>

AWS Directory Service creates a fully managed Microsoft Active Directory in the AWS Cloud\. When you create an AWS Managed Microsoft AD directory, AWS Directory Service creates two domain controllers and DNS servers on your behalf\. The directory servers are created in different subnets in a VPC\. This redundancy helps ensure that your directory remains accessible even if a failure occurs\. 

 When you create an AWS Managed Microsoft AD directory, AWS Directory Service performs the following tasks on your behalf: 
+  Sets up a Microsoft Active Directory within your VPC\. 
+  Creates a directory administrator account with the user name `Admin` and the specified password\. You use this account to manage your directory\. 
**Note**  
Make sure to save this password\. AWS Directory Service doesn't store this password, and it can't be retrieved or reset\.
+ Creates a security group for the directory controllers\.

When you launch an AWS Directory Service for Microsoft Active Directory, AWS creates an Organizational Unit \(OU\) that contains all of your directory’s objects\. This OU, which has the NetBIOS name that you entered when you created your directory, is located in the domain root\. The domain root is owned and managed by AWS\. 

 The `admin` account that was created with your AWS Managed Microsoft AD directory has permissions for the most common administrative activities for your OU: 
+  Create, update, or delete users
+  Add resources to your domain such as file or print servers, and then assign permissions for those resources to users in your OU 
+  Create additional OUs and containers 
+  Delegate authority 
+  Restore deleted objects from the Active Directory Recycle Bin 
+  Run AD and DNS Windows PowerShell modules on the Active Directory Web Service 

 The `admin` account also has rights to perform the following domain\-wide activities: 
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
The fully qualified domain name of the AWS Managed Microsoft AD must not be longer than 61 characters\.  
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

1. Choose **Next**\.

1. On the **Choose VPC and subnets** page, provide the following information:  
**VPC**  
 Choose the VPC for the directory\. The Oracle DB instance can be created in this same VPC or in a different VPC\.   
**Subnets**  
 Choose the subnets for the directory servers\. The two subnets must be in different Availability Zones\. 

1. Choose **Next**\.

1.  Review the directory information\. If changes are needed, choose **Previous** and make the changes\. When the information is correct, choose **Create directory**\.   
![\[Directory details page\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/WinAuth2.png)

 It takes several minutes for the directory to be created\. When it has been successfully created, the **Status** value changes to **Active**\. 

 To see information about your directory, choose the directory ID in the directory listing\. Make a note of the `Directory ID` value\. You need this value when you create or modify your Oracle DB instance\. 

![\[graphic of details page\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/WinAuth3.png)

## Step 2: Create a Forest Trust<a name="oracle-kerberos-setting-up.create-forest-trust"></a>

If you plan to use AWS Managed Microsoft AD only, move on to [Step 3: Create an IAM Role for Use by Amazon RDS](#oracle-kerberos-setting-up.CreateIAMRole)\.

To get Kerberos authentication using an on\-premises or self\-hosted Microsoft Active Directory, create a two\-way forest trust\. For more information about setting up forest trusts using AWS Directory Service, see [When to Create a Trust Relationship](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/ms_ad_setup_trust.html) in the *AWS Directory Service Administration Guide*\.

## Step 3: Create an IAM Role for Use by Amazon RDS<a name="oracle-kerberos-setting-up.CreateIAMRole"></a>

You must create an IAM role that uses the managed IAM policy `AmazonRDSDirectoryServiceAccess`\. This role allows Amazon RDS to make calls to the AWS Directory Service for you\. When you create the role, choose `Directory Service`, and attach the AWS managed policy `AmazonRDSDirectoryServiceAccess` to it\.

For more information about creating IAM roles for a service, see [Creating a Role to Delegate Permissions to an AWS Service](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-service.html)\.

Optionally, you can create policies with the required permissions instead of using the managed IAM policy `AmazonRDSDirectoryServiceAccess`\. In this case, the role must have the following IAM trust policy\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect:" "Allow",
      "Principal": {
        "Service": [
          "rds.amazonaws.com",
          "directoryservice.rds.amazonaws.com"
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

## Step 5: Configure VPC Peering<a name="oracle-kerberos-setting-up.vpc-peering"></a>

If you plan to locate the directory and the DB instance in different VPCs, configure VPC peering by following the instructions in this step\. If you plan to locate the directory and the DB instance in the same VPC, skip this step and move on to [Step 6: Create or Modify an Oracle DB Instance](#oracle-kerberos-setting-up.create-modify)\.

If the same AWS account owns both VPCs, follow the instructions in [What is VPC Peering?](https://docs.aws.amazon.com/vpc/latest/peering/Welcome.html)\. Specifically, complete the following steps:

1. Set up appropriate VPC routing rules to ensure the network traffic can flow both ways\.

1. Ensure the DB instance's security group can receive ingress traffic from this security group\.

1. Ensure that there is no network access control list \(ACL\) rule to block traffic\.

If different AWS accounts own the VPCs, complete the following steps:

1. Configure VPC peering by following the instructions in [What is VPC Peering?](https://docs.aws.amazon.com/vpc/latest/peering/Welcome.html) in the *Amazon Virtual Private Cloud VPC Peering*\. Specifically, complete the following steps:

   1. Set up appropriate VPC routing rules to ensure the network traffic can flow both ways\.

   1. Ensure that there is no ACL rule to block traffic\.

1. Initiate sharing of the directory with the AWS account that the DB instance will be created in by following the instructions in [Tutorial: Sharing Your AWS Managed Microsoft AD Directory for Seamless EC2 Domain\-Join](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/ms_ad_tutorial_directory_sharing.html) in the *AWS Directory Service Administration Guide*\.

1. Log in to the AWS Directory Service console using the account for the DB instance, and ensure that the domain has the `SHARED` status before proceeding\.

1. While logged into the AWS Directory Service console using the account for the DB instance, make a note of the **Directory ID** value for the directory\.

## Step 6: Create or Modify an Oracle DB Instance<a name="oracle-kerberos-setting-up.create-modify"></a>

Create or modify an Oracle DB instance for use with your directory\. You can use the console, CLI, or RDS API to associate a DB instance with a directory\. You can do this in one of the following ways:
+ Create a new Oracle DB instance using the console, the [ create\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html) CLI command, or the [CreateDBInstance](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html) RDS API operation\.

  For instructions, see [Creating a DB Instance Running the Oracle Database Engine](USER_CreateOracleInstance.md)\.
+ Modify an existing Oracle DB instance using the console, the [modify\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) CLI command, or the [ModifyDBInstance](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html) RDS API operation\.

  For instructions, see [Modifying a DB Instance Running the Oracle Database Engine](USER_ModifyInstance.Oracle.md)\.
+ Restore an Oracle DB instance from a DB snapshot using the console, the [ restore\-db\-instance\-from\-db\-snapshot](https://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-instance-from-db-snapshot.html) CLI command, or the [ RestoreDBInstanceFromDBSnapshot](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBInstanceFromDBSnapshot.html) RDS API operation\.

  For instructions, see [Restoring from a DB Snapshot](USER_RestoreFromSnapshot.md)\.
+ Restore an Oracle DB instance to a point\-in\-time using the console, the [ restore\-db\-instance\-to\-point\-in\-time](https://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-instance-to-point-in-time.html) CLI command, or the [ RestoreDBInstanceToPointInTime](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBInstanceToPointInTime.html) RDS API operation\.

  For instructions, see [Restoring a DB Instance to a Specified Time](USER_PIT.md)\.

Kerberos authentication is only supported for Oracle DB instances in a VPC\. The DB instance can be in the same VPC as the directory, or in a different VPC\. The DB instance must use a security group that allows egress within the directory's VPC so the DB instance can communicate with the directory\.

When you use the console, choose **Password and Kerberos authentication** in the **Database authentication** section\. Choose the directory or choose **Create a new Directory**\.

![\[\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/kerberos-authentication.png)

When you use the AWS CLI, the following parameters are required for the DB instance to be able to use the directory that you created:
+ For the `--domain` parameter, use the domain identifier \("d\-\*" identifier\) generated when you created the directory\.
+ For the `--domain-iam-role-name` parameter, use the role you created that uses the managed IAM policy `AmazonRDSDirectoryServiceAccess`\.

For example, the following CLI command modifies a DB instance to use a directory\.

For Linux, OS X, or Unix:

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