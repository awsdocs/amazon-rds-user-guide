# Using Windows Authentication with an Amazon RDS for SQL Server DB Instance<a name="USER_SQLServerWinAuth"></a>

You can use Microsoft Windows Authentication to authenticate users when they connect to your Amazon RDS for Microsoft SQL Server DB instance\. The DB instance works with AWS Directory Service for Microsoft Active Directory, also called AWS Managed Microsoft AD, to enable Windows Authentication\. When users authenticate with a SQL Server DB instance joined to the trusting domain, authentication requests are forwarded to the domain directory that you create with AWS Directory Service\. 

Amazon RDS supports Windows Authentication for SQL Server in all AWS Regions except Middle East \(Bahrain\)\.

Amazon RDS uses mixed mode for Windows Authentication\. This approach means that the *master user* \(the name and password used to create your SQL Server DB instance\) uses SQL Authentication\. Because the master user account is a privileged credential, you should restrict access to this account\.

To get Windows Authentication using an on\-premises or self\-hosted Microsoft Active Directory, create a forest trust\. The trust can be one\-way or two\-way\. For more information on setting up forest trusts using AWS Directory Service, see [When to Create a Trust Relationship](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/ms_ad_setup_trust.html) in the *AWS Directory Service Administration Guide*\.

To set up Windows authentication for a SQL Server DB instance, do the following steps, explained in greater detail in [Setting Up Windows Authentication for SQL Server DB Instances](#USER_SQLServerWinAuth.SettingUp):

1. Use AWS Managed Microsoft AD, either from the AWS Management Console or AWS Directory Service API, to create an AWS Managed Microsoft AD directory\. 

1. If you use the AWS CLI or Amazon RDS API to create your SQL Server DB instance, create an AWS Identity and Access Management \(IAM\) role\. This role uses the managed IAM policy `AmazonRDSDirectoryServiceAccess` and allows Amazon RDS to make calls to your directory\. If you use the console to create your SQL Server DB instance, AWS creates the IAM role for you\. 

   For the role to allow access, the AWS Security Token Service \(AWS STS\) endpoint must be activated in the AWS Region for your AWS account\. AWS STS endpoints are active by default in all AWS Regions, and you can use them without any further actions\. For more information, see [Managing AWS STS in an AWS Region](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp_enable-regions.html) in the *IAM User Guide*\.

1. Create and configure users and groups in the AWS Managed Microsoft AD directory using the Microsoft Active Directory tools\. For more information about creating users and groups in your Active Directory, see [Manage Users and Groups in AWS Managed Microsoft AD](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/ms_ad_manage_users_groups.html) in the *AWS Directory Service Administration Guide*\.

1. If you plan to locate the directory and the DB instance in different VPCs, enable cross\-VPC traffic\.

1. Use Amazon RDS to create a new SQL Server DB instance either from the console, AWS CLI, or Amazon RDS API\. In the create request, you provide the domain identifier \("`d-*`" identifier\) that was generated when you created your directory and the name of the role you created\. You can also modify an existing SQL Server DB instance to use Windows Authentication by setting the domain and IAM role parameters for the DB instance\.

1. Use the Amazon RDS master user credentials to connect to the SQL Server DB instance as you do any other DB instance\. Because the DB instance is joined to the AWS Managed Microsoft AD domain, you can provision SQL Server logins and users from the Active Directory users and groups in their domain\. \(These are known as SQL Server "Windows" logins\.\) Database permissions are managed through standard SQL Server permissions granted and revoked to these Windows logins\. 

## Creating the Endpoint for Kerberos Authentication<a name="USER_SQLServerWinAuth.KerberosEndpoint"></a>

Kerberos\-based authentication requires that the endpoint be the customer\-specified host name, a period, and then the fully qualified domain name \(FQDN\)\. For example, the following is an example of an endpoint you might use with Kerberos\-based authentication\. In this example, the SQL Server DB instance host name is `ad-test` and the domain name is `corp-ad.company.com`\. 

```
ad-test.corp-ad.company.com
```

If you want to make sure your connection is using Kerberos, run the following query: 

```
1. SELECT net_transport, auth_scheme 
2.   FROM sys.dm_exec_connections 
3.  WHERE session_id = @@SPID;
```

## Setting Up Windows Authentication for SQL Server DB Instances<a name="USER_SQLServerWinAuth.SettingUp"></a>

You use AWS Directory Service for Microsoft Active Directory, also called AWS Managed Microsoft AD, to set up Windows Authentication for a SQL Server DB instance\. To set up Windows Authentication, take the following steps\. 

### Step 1: Create a Directory Using the AWS Directory Service for Microsoft Active Directory<a name="USER_SQLServerWinAuth.SettingUp.CreateDirectory"></a>

AWS Directory Service creates a fully managed, Microsoft Active Directory in the AWS Cloud\. When you create an AWS Managed Microsoft AD directory, AWS Directory Service creates two domain controllers and Domain Name Service \(DNS\) servers on your behalf\. The directory servers are created in two subnets in two different Availability Zones within a VPC\. This redundancy helps ensure that your directory remains accessible even if a failure occurs\.

 When you create an AWS Managed Microsoft AD directory, AWS Directory Service performs the following tasks on your behalf: 
+ Sets up a Microsoft Active Directory within the VPC\. 
+ Creates a directory administrator account with the user name Admin and the specified password\. You use this account to manage your directory\.
**Note**  
Be sure to save this password\. AWS Directory Service doesn't store this password, and you can't retrieve or reset it\.
+ Creates a security group for the directory controllers\.

When you launch an AWS Directory Service for Microsoft Active Directory, AWS creates an Organizational Unit \(OU\) that contains all your directory's objects\. This OU, which has the NetBIOS name that you typed when you created your directory, is located in the domain root\. The domain root is owned and managed by AWS\. 

 The *admin* account that was created with your AWS Managed Microsoft AD directory has permissions for the most common administrative activities for your OU: 
+ Create, update, or delete users, groups, and computers\. 
+ Add resources to your domain such as file or print servers, and then assign permissions for those resources to users and groups in your OU\. 
+ Create additional OUs and containers\.
+ Delegate authority\. 
+ Create and link group policies\. 
+ Restore deleted objects from the Active Directory Recycle Bin\. 
+ Run AD and DNS Windows PowerShell modules on the Active Directory Web Service\. 

The admin account also has rights to perform the following domain\-wide activities: 
+ Manage DNS configurations \(add, remove, or update records, zones, and forwarders\)\. 
+ View DNS event logs\. 
+ View security event logs\. 

**To create a directory with AWS Managed Microsoft AD**

1. In the [AWS Directory Service console](https://console.aws.amazon.com/directoryservicev2/) navigation pane, choose **Directories** and choose **Set up directory**\.

1. Choose **AWS Managed Microsoft AD**\. This is the only option currently supported for use with Amazon RDS\.

1. Choose **Next**\.

1. On the **Enter directory information** page, provide the following information:   
**Edition**  
 Choose the edition that meets your requirements\.  
**Directory DNS name**  
The fully qualified name for the directory, such as `corp.example.com`\. Names longer than 47 characters aren't supported by SQL Server\.  
**Directory NetBIOS name**  
An optional short name for the directory, such as `CORP`\.   
**Directory description**  
An optional description for the directory\.   
**Admin password**  
The password for the directory administrator\. The directory creation process creates an administrator account with the user name Admin and this password\.   
The directory administrator password can't include the word `admin`\. The password is case\-sensitive and must be 8–64 characters in length\. It must also contain at least one character from three of the following four categories:   
   + Lowercase letters \(a\-z\)
   + Uppercase letters \(A\-Z\)
   + Numbers \(0\-9\)
   + Non\-alphanumeric characters \(\~\!@\#$%^&\*\_\-\+=`\|\\\(\)\{\}\[\]:;"'<>,\.?/\)   
**Confirm password**  
Retype the administrator password\. 

1. Choose **Next**\.

1. On the **Choose VPC and subnets** page, provide the following information:  
**VPC**  
Choose the VPC for the directory\.  
You can locate the directory and the DB instance in different VPCs, but if you do so, make sure to enable cross\-VPC traffic\. For more information, see [Step 4: Enable Cross\-VPC Traffic Between the Directory and the DB Instance](#USER_SQLServerWinAuth.SettingUp.VPC-Peering)\.  
**Subnets**  
Choose the subnets for the directory servers\. The two subnets must be in different Availability Zones\.

1. Choose **Next**\.

1. Review the directory information\. If changes are needed, choose **Previous**\. When the information is correct, choose **Create directory**\.   
![\[Review and create page\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/WinAuth2.png)

It takes several minutes for the directory to be created\. When it has been successfully created, the **Status** value changes to **Active**\.

To see information about your directory, choose the directory ID in the directory listing\. Make a note of the **Directory ID**\. You need this value when you create or modify your SQL Server DB instance\.

![\[Directory details page\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/WinAuth3.png)

### Step 2: Create the IAM Role for Use by Amazon RDS<a name="USER_SQLServerWinAuth.SettingUp.CreateIAMRole"></a>

If you use the console to create your SQL Server DB instance, you can skip this step\. If you use the CLI or RDS API to create your SQL Server DB instance, you must create an IAM role that uses the `AmazonRDSDirectoryServiceAccess` managed IAM policy\. This role allows Amazon RDS to make calls to the AWS Directory Service for you\. 

If you are using a custom policy for joining a domain, rather than using the AWS\-managed `AmazonRDSDirectoryServiceAccess` policy, make sure that you allow the `ds:GetAuthorizedApplicationDetails` action\. This requirement is effective starting July 2019, due to a change in the AWS Directory Service API\.

The following IAM policy, `AmazonRDSDirectoryServiceAccess`, provides access to AWS Directory Service\.

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

Create an IAM role using this policy\. For more information about creating IAM roles, see [Creating Customer Managed Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_managed-using.html#create-managed-policy-console) in the *IAM User Guide*\.

### Step 3: Create and Configure Users and Groups<a name="USER_SQLServerWinAuth.SettingUp.CreateUsers"></a>

You can create users and groups with the Active Directory Users and Computers tool\. This tool is one of the Active Directory Domain Services and Active Directory Lightweight Directory Services tools\. Users represent individual people or entities that have access to your directory\. Groups are very useful for giving or denying privileges to groups of users, rather than having to apply those privileges to each individual user\.

To create users and groups in an AWS Directory Service directory, you must be connected to a Windows EC2 instance that is a member of the AWS Directory Service directory\. You must also be logged in as a user that has privileges to create users and groups\. For more information, see [Add Users and Groups \(Simple AD and AWS Managed Microsoft AD\)](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/creating_ad_users_and_groups.html) in the *AWS Directory Service Administration Guide*\.

### Step 4: Enable Cross\-VPC Traffic Between the Directory and the DB Instance<a name="USER_SQLServerWinAuth.SettingUp.VPC-Peering"></a>

If you plan to locate the directory and the DB instance in the same VPC, skip this step and move on to [Step 5: Create or Modify a SQL Server DB Instance](#USER_SQLServerWinAuth.SettingUp.CreateModify)\.

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

### Step 5: Create or Modify a SQL Server DB Instance<a name="USER_SQLServerWinAuth.SettingUp.CreateModify"></a>

Create or modify a SQL Server DB instance for use with your directory\. You can use the console, CLI, or RDS API to associate a DB instance with a directory\. You can do this in one of the following ways:
+ Create a new SQL Server DB instance using the console, the [create\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html) CLI command, or the [CreateDBInstance](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html) RDS API operation\.

  For instructions, see [Creating an Amazon RDS DB Instance](USER_CreateDBInstance.md)\.
+ Modify an existing SQL Server DB instance using the console, the [modify\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) CLI command, or the [ModifyDBInstance](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html) RDS API operation\.

  For instructions, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md)\.
+ Restore a SQL Server DB instance from a DB snapshot using the console, the [restore\-db\-instance\-from\-db\-snapshot](https://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-instance-from-db-snapshot.html) CLI command, or the [RestoreDBInstanceFromDBSnapshot](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBInstanceFromDBSnapshot.html) RDS API operation\.

  For instructions, see [Restoring from a DB Snapshot](USER_RestoreFromSnapshot.md)\.
+ Restore a SQL Server DB instance to a point\-in\-time using the console, the [restore\-db\-instance\-to\-point\-in\-time](https://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-instance-to-point-in-time.html) CLI command, or the [RestoreDBInstanceToPointInTime](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBInstanceToPointInTime.html) RDS API operation\.

  For instructions, see [Restoring a DB Instance to a Specified Time](USER_PIT.md)\.

 Windows Authentication is only supported for SQL Server DB instances in a VPC\. 

 For the DB instance to be able to use the domain directory that you created, the following is required: 
+  For **Directory**, you must choose the domain identifier \(`d-ID`\) generated when you created the directory\.
+  Make sure that the VPC security group has an outbound rule that lets the DB instance communicate with the directory\.

![\[Microsoft SQL Server Windows Authentication directory\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/WinAuth1.png)

When you use the AWS CLI, the following parameters are required for the DB instance to be able to use the directory that you created:
+ For the `--domain` parameter, use the domain identifier \(`d-ID`\) generated when you created the directory\.
+ For the `--domain-iam-role-name` parameter, use the role that you created that uses the managed IAM policy `AmazonRDSDirectoryServiceAccess`\.

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

### Step 6: Create Windows Authentication SQL Server Logins<a name="USER_SQLServerWinAuth.CreateLogins"></a>

Use the Amazon RDS master user credentials to connect to the SQL Server DB instance as you do any other DB instance\. Because the DB instance is joined to the AWS Managed Microsoft AD domain, you can provision SQL Server logins and users\. You do this from the Active Directory users and groups in your domain\. Database permissions are managed through standard SQL Server permissions granted and revoked to these Windows logins\.

For an Active Directory user to authenticate with SQL Server, a SQL Server Windows login must exist for the user or a group that the user is a member of\. Fine\-grained access control is handled through granting and revoking permissions on these SQL Server logins\. A user that doesn't have a SQL Server login or belong to a group with such a login can't access the SQL Server DB instance\.

The ALTER ANY LOGIN permission is required to create an Active Directory SQL Server login\. If you haven't created any logins with this permission, connect as the DB instance's master user using SQL Server Authentication\. Run the following data definition language \(DDL\) command to create a SQL Server login for an Active Directory user or group\.

```
CREATE LOGIN [<user or group>] FROM WINDOWS WITH DEFAULT_DATABASE = [master],
   DEFAULT_LANGUAGE = [us_english];
```

Specify users or groups using the pre\-Windows 2000 login name in the format `domainName\login_name`\. You can't use a User Principle Name \(UPN\) in the format *`login_name`*`@`*`DomainName`*\. For more information about CREATE LOGIN, see [https://msdn\.microsoft\.com/en\-us/library/ms189751\.aspx](https://msdn.microsoft.com/en-us/library/ms189751.aspx) in the Microsoft Developer Network documentation\.

Users \(both humans and applications\) from your domain can now connect to the RDS SQL Server instance from a domain\-joined client machine using Windows authentication\.

## Managing a DB Instance in a Domain<a name="USER_SQLServerWinAuth.Managing"></a>

 You can use the console, AWS CLI, or the Amazon RDS API to manage your DB instance and its relationship with your domain\. For example, you can move the DB instance into, out of, or between domains\. 

 For example, using the Amazon RDS API, you can do the following: 
+  To reattempt a domain join for a failed membership, use the [ModifyDBInstance](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html) API operation and specify the current membership's directory ID\. 
+  To update the IAM role name for membership, use the `ModifyDBInstance` API operation and specify the current membership's directory ID and the new IAM role\. 
+  To remove a DB instance from a domain, use the `ModifyDBInstance` API operation and specify `none` as the domain parameter\. 
+  To move a DB instance from one domain to another, use the `ModifyDBInstance` API operation and specify the domain identifier of the new domain as the domain parameter\. 
+  To list membership for each DB instance, use the [DescribeDBInstances](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/DescribeDBInstances.html) API operation\. 

### Understanding Domain Membership<a name="USER_SQLServerWinAuth.Understanding"></a>

 After you create or modify your DB instance, the instance becomes a member of the domain\. The AWS console indicates the status of the domain membership for the DB instance\. The status of the DB instance can be one of the following: 
+  **joined** – The instance is a member of the domain\. 
+  **joining** – The instance is in the process of becoming a member of the domain\. 
+  **pending\-join** – The instance membership is pending\. 
+  **pending\-maintenance\-join** – AWS will attempt to make the instance a member of the domain during the next scheduled maintenance window\. 
+  **pending\-removal** – The removal of the instance from the domain is pending\. 
+  **pending\-maintenance\-removal** – AWS will attempt to remove the instance from the domain during the next scheduled maintenance window\. 
+  **failed** – A configuration problem has prevented the instance from joining the domain\. Check and fix your configuration before reissuing the instance modify command\. 
+  **removing** – The instance is being removed from the domain\. 

A request to become a member of a domain can fail because of a network connectivity issue or an incorrect IAM role\. For example, you might create a DB instance or modify an existing instance and have the attempt fail for the DB instance to become a member of a domain\. In this case, either reissue the command to create or modify the DB instance or modify the newly created instance to join the domain\. 

## Connecting to SQL Server with Windows Authentication<a name="USER_SQLServerWinAuth.Connecting"></a>

 To connect to SQL Server with Windows Authentication, you must be logged into a domain\-joined computer as a domain user\. After launching SQL Server Management Studio, choose **Windows Authentication** as the authentication type, as shown following\. 

![\[Connect to SQL Server 2012\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/WinAuth4.png)

## Restoring a SQL Server DB Instance and then Adding It to a Domain<a name="USER_SQLServerWinAuth.Restore"></a>

You can restore a DB snapshot or do a point\-in\-time restore \(PITR\) for a SQL Server DB instance and then add it to a domain\. Once the DB instance is restored, modify the instance using the process explained in [Step 5: Create or Modify a SQL Server DB Instance](#USER_SQLServerWinAuth.SettingUp.CreateModify) to add the DB instance to a domain\. 