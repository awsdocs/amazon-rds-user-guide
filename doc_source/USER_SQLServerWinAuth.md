# Using Windows Authentication with a Microsoft SQL Server DB Instance<a name="USER_SQLServerWinAuth"></a>

You can use Windows Authentication to authenticate users when they connect to your Amazon RDS DB instance running Microsoft SQL Server\. The DB instance works with AWS Directory Service for Microsoft Active Directory, also called AWS Managed Microsoft AD, to enable Windows Authentication\. When users authenticate with a SQL Server DB instance joined to the trusting domain, authentication requests are forwarded to the domain directory that you create with AWS Directory Service\. 

Amazon RDS supports Windows Authentication for SQL Server in all AWS Regions except the following: 
+ US West \(N\. California\)
+ Asia Pacific \(Mumbai\)
+ South America \(São Paulo\)
+ AWS GovCloud \(US\-East\)
+ AWS GovCloud \(US\-West\)

Amazon RDS uses Mixed Mode for Windows Authentication\. This approach means that the *master user *\(the name and password used to create your SQL Server DB instance\) uses SQL Authentication\. Because the master user account is a privileged credential, you should restrict access to this account\. 

To get Windows Authentication using an on\-premises or self\-hosted Microsoft Active Directory, you need to create a forest trust\. The trust can be one\-way or two\-way\. For more information on setting up forest trusts using AWS Directory Service, see [When to Create a Trust Relationship](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/ms_ad_setup_trust.html)\.

To set up Windows authentication for a SQL Server DB instance, do the following steps \(explained in greater detail in this section\):

1. Use the AWS Directory Service for Microsoft Active Directory, also called AWS Managed Microsoft AD, either from the AWS console or AWS Directory Service API to create an AWS Managed Microsoft AD directory\. 

1. If you use the AWS CLI or Amazon RDS API to create your SQL Server DB instance, you need to create an AWS Identity and Access Management \(IAM\) role that uses the managed IAM policy AmazonRDSDirectoryServiceAccess\. The role allows Amazon RDS to make calls to your directory\. If you use the AWS console to create your SQL Server DB instance, AWS creates the IAM role for you\. 

   For the role to allow access, the AWS Security Token Service \(AWS STS\) endpoint must be activated in the AWS Region for your AWS account\. AWS STS endpoints are active by default in all AWS Regions, and you can use them without any further actions\. For more information, see [Managing AWS STS in an AWS Region](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp_enable-regions.html)\.

1. Create and configure users and groups in the AWS Managed Microsoft AD directory using the Microsoft Active Directory tools\. For more information about creating users and groups in your Active Directory, see [Manage Users and Groups in AWS Managed Microsoft AD](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/ms_ad_manage_users_groups.html)\.

1. Use Amazon RDS to create a new SQL Server DB instance either from the AWS console, AWS CLI, or Amazon RDS API\. In the create request, you provide the domain identifier \("d\-\*" identifier\) that was generated when you created your directory and the name of the role you created\. You can also modify an existing SQL Server DB instance to use Windows Authentication by setting the domain and IAM role parameters for the DB instance, and locating the DB instance in the same VPC as the domain directory\.

1. Use the Amazon RDS *master user* credentials to connect to the SQL Server DB instance as you would any other DB instance\. Because the DB instance is joined to the AWS Managed Microsoft AD domain, you can provision SQL Server logins and users from the Active Directory users and groups in their domain \(known as SQL Server "Windows" logins\)\. Database permissions are managed through standard SQL Server permissions granted and revoked to these windows logins\. 

## Creating the Endpoint for Kerberos Authentication<a name="USER_SQLServerWinAuth.KerberosEndpoint"></a>

Kerberos\-based authentication requires that the endpoint be the customer\-specified host name, a period, and then the fully qualified domain name \(FQDN\)\. For example, the following is an example of an endpoint you would use with Kerberos\-based authentication\. In this example, the SQL Server DB instance host name is `ad-test` and the domain name is `corp-ad.company.com`: 

```
ad-test.corp-ad.company.com
```

If you want to check to make sure your connection is using Kerberos, you can run the following query: 

```
1. SELECT net_transport, auth_scheme 
2.   FROM sys.dm_exec_connections 
3.  WHERE session_id = @@SPID;
```

## Setting Up Windows Authentication for SQL Server DB Instances<a name="USER_SQLServerWinAuth.SettingUp"></a>

You use AWS Directory Service for Microsoft Active Directory, also called AWS Managed Microsoft AD, to set up Windows Authentication for a SQL Server DB instance\. To set up Windows Authentication, you take the following steps: 

### Step 1: Create a Directory Using the AWS Directory Service for Microsoft Active Directory<a name="USER_SQLServerWinAuth.SettingUp.CreateDirectory"></a>

AWS Directory Service creates a fully managed, Microsoft Active Directory in the AWS cloud\. When you create an AWS Managed Microsoft AD directory, AWS Directory Service creates two domain controllers and DNS servers on your behalf\. The directory servers are created in two subnets in two different Availability Zones within a VPC\. This redundancy helps ensure that your directory remains accessible even if a failure occurs\.

 When you create an AWS Managed Microsoft AD directory, AWS Directory Service performs the following tasks on your behalf: 
+  Sets up a Microsoft Active Directory within the VPC\. 
+  Creates a directory administrator account with the user name Admin and the specified password\. You use this account to manage your directory\. 
**Note**  
Be sure to save this password\. AWS Directory Service does not store this password and it cannot be retrieved or reset\.
+  Creates a security group for the directory controllers\. 

When you launch an AWS Directory Service for Microsoft Active Directory, AWS creates an Organizational Unit \(OU\) that contains all your directory’s objects\. This OU, which has the NetBIOS name that you typed when you created your directory, is located in the domain root\. The domain root is owned and managed by AWS\. 

 The *admin* account that was created with your AWS Managed Microsoft AD directory has permissions for the most common administrative activities for your OU: 
+  Create update, or delete users, groups, and computers 
+  Add resources to your domain such as file or print servers, and then assign permissions for those resources to users and groups in your OU 
+  Create additional OUs and containers 
+  Delegate authority 
+  Create and link group policies 
+  Restore deleted objects from the Active Directory Recycle Bin 
+  Run AD and DNS Windows PowerShell modules on the Active Directory Web Service 

 The *admin* account also has rights to perform the following domain\-wide activities: 
+  Manage DNS configurations \(Add, remove, or update records, zones, and forwarders\) 
+  View DNS event logs 
+  View security event logs 

**To create a directory with AWS Managed Microsoft AD**

1.  In the [AWS Directory Service console](https://console.aws.amazon.com/directoryservicev2/) navigation pane, select **Directories** and choose **Set up directory**\. 

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
   +  Lowercase letters \(a\-z\) 
   +  Uppercase letters \(A\-Z\) 
   +  Numbers \(0\-9\) 
   +  Non\-alphanumeric characters \(\~\!@\#$%^&\*\_\-\+=`\|\\\(\)\{\}\[\]:;"'<>,\.?/\)   
**Confirm password**  
 Retype the administrator password\. 

1. Choose **Next**\.

1. On the **Choose VPC and subnets** page, provide the following information:  
**VPC**  
 Select the VPC for the directory\. The SQL Server DB instance must be created in this same VPC\.   
**Subnets**  
 Select the subnets for the directory servers\. The two subnets must be in different Availability Zones\. 

1. Choose **Next**\.

1.  Review the directory information\. If changes are needed, choose **Previous**\. When the information is correct, choose **Create directory**\.   
![\[Review and create page\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/WinAuth2.png)

 It takes several minutes for the directory to be created\. When it has been successfully created, the **Status** value changes to **Active**\. 

 To see information about your directory, choose the directory ID in the directory listing\. Make a note of the **Directory ID**\. You will need this value when you create or modify your SQL Server DB instance\. 

![\[Directory details page\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/WinAuth3.png)

### Step 2: Create the IAM role for Use by Amazon RDS<a name="USER_SQLServerWinAuth.SettingUp.CreateIAMRole"></a>

If you use the AWS console to create your SQL Server DB instance, you can skip this step\. If you used the AWS CLI or Amazon RDS API to create your SQL Server DB instance, you must create an IAM role that uses the AmazonRDSDirectoryServiceAccess managed IAM policy\. This role allows Amazon RDS to make calls to the AWS Directory Service for you\. 

If you are using a custom policy for joining a domain, rather than using the AWS\-managed `AmazonRDSDirectoryServiceAccess` policy, you must allow the "ds:GetAuthorizedApplicationDetails" action\. This is effective starting July 2019, due to a change in AWS Directory Service's API\.

 The following IAM policy, AmazonRDSDirectoryServiceAccess, provides access to AWS Directory Service: 

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

 Create an IAM role using this policy\. For more information about creating IAM roles, see [Creating Customer Managed Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_managed-using.html#create-managed-policy-console)\. 

### Step 3: Create and Configure Users and Groups<a name="USER_SQLServerWinAuth.SettingUp.CreateUsers"></a>

 You can create users and groups with the Active Directory Users and Computers tool, which is part of the Active Directory Domain Services and Active Directory Lightweight Directory Services tools\. Users represent individual people or entities that have access to your directory\. Groups are very useful for giving or denying privileges to groups of users, rather than having to apply those privileges to each individual user\. 

To create users and groups in an AWS Directory Service directory, you must be connected to a Windows EC2 instance that is a member of the AWS Directory Service directory, and be logged in as a user that has privileges to create users and groups\. For more information, see [Add Users and Groups \(Simple AD and AWS Managed Microsoft AD\)](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/creating_ad_users_and_groups.html)\. 

### Step 4: Create or Modify a SQL Server DB Instance<a name="USER_SQLServerWinAuth.SettingUp.CreateModify"></a>

 Next, you create or modify a Microsoft SQL Server DB instance for use with the directory\. You can do this in one of the following ways: 
+  Create a new SQL Server DB instance\.
+  Modify an existing SQL Server DB instance\.
+  Restore a SQL Server DB instance from a DB snapshot\.
+  Restore a SQL Server DB instance from a point\-in\-time restore \(PITR\)\.

 Windows Authentication is only supported for SQL Server DB instances in a VPC, and the DB instance must be in the same VPC as the directory\. 

 Several parameters are required for the DB instance to be able to use the domain directory you created: 
+  For the **Directory** parameter, you must choose the domain identifier \("d\-\*" identifier\) generated when you created the directory\. 
+  Use the same VPC that was used when you created the directory\. 
+  Make sure that the VPC security group has an outbound rule that lets the DB instance communicate with the directory\.

![\[Microsoft SQL Server Windows Authentication directory\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/WinAuth1.png)

### Step 5: Create Windows Authentication SQL Server Logins<a name="USER_SQLServerWinAuth.CreateLogins"></a>

 Use the Amazon RDS *master user* credentials to connect to the SQL Server DB instance as you would any other DB instance\. Because the DB instance is joined to the AWS Managed Microsoft AD domain, you can provision SQL Server logins and users from the Active Directory users and groups in your domain\. Database permissions are managed through standard SQL Server permissions granted and revoked to these windows logins\. 

 To allow an Active Directory user to authenticate with SQL Server, a SQL Server Windows login must exist for the user or a group that the user is a member of\. Fine\-grained access control is handled through granting and revoking permissions on these SQL Server logins\. If a user does not have a corresponding SQL Server login and is not a member of a group with a corresponding SQL Server login, that user cannot access the SQL Server DB instance\. 

 The ALTER ANY LOGIN permission is required to create an Active Directory SQL Server login\. If you have not yet created any logins with this permission, connect as the DB instance's *master user* using SQL Server Authentication\. Run the following data definition language \(DDL\) command to create a SQL Server login for an Active Directory user or group: 

```
CREATE LOGIN [<user or group>] FROM WINDOWS WITH DEFAULT_DATABASE = [master],
   DEFAULT_LANGUAGE = [us_english];
```

 Users or groups must be specified using the pre–Windows 2000 login name in the format *domainName*\\*login\_name*\. You cannot use a User Principle Name \(UPN\) in the format *login\_name*@*DomainName*\. For more information about CREATE LOGIN, go to [https://msdn\.microsoft\.com/en\-us/library/ms189751\.aspx](https://msdn.microsoft.com/en-us/library/ms189751.aspx) in the Microsoft Developer Network documentation\. 

 Users \(both humans and applications\) from your domain can now connect to the RDS SQL Server instance from a domain joined client machine using Windows authentication\. 

## Managing a DB Instance in a Domain<a name="USER_SQLServerWinAuth.Managing"></a>

 You can use the AWS console, AWS CLI, or the Amazon RDS API to manage your DB instance and its relationship with your domain, such as moving the DB instance into, out of, or between domains\. 

 For example, using the Amazon RDS API, you can do the following: 
+  To re\-attempt a domain join for a failed membership, use the ModifyDBInstance API operation and specify the current membership's directory ID\. 
+  To update the IAM role name for membership, use the ModifyDBInstance API operation and specify the current membership's directory ID and the new IAM role\. 
+  To remove a DB instance from a domain, use the ModifyDBInstance API operation and specify `none` as the domain parameter\. 
+  To move a DB instance from one domain to another, use the ModifyDBInstance API operation and specify the domain identifier of the new domain as the domain parameter\. 
+  To list membership for each DB instance, use the DescribeDBInstances API operation\. 

### Understanding Domain Membership<a name="USER_SQLServerWinAuth.Understanding"></a>

 After you create or modify your DB instance, the instance becomes a member of the domain\. The AWS console indicates the status of the domain membership for the DB instance\. The status of the DB instance can be one of the following: 
+  **joined** – The instance is a member of the domain\. 
+  **joining** – The instance is in the process of becoming a member of the domain\. 
+  **pending\-join** – The instance membership is pending \. 
+  **pending\-maintenance\-join** – AWS will attempt to make the instance a member of the domain during the next scheduled maintenance window\. 
+  **pending\-removal** – The removal of the instance from the domain is pending\. 
+  **pending\-maintenance\-removal** – AWS will attempt to remove the instance from the domain during the next scheduled maintenance window\. 
+  **failed** – A configuration problem has prevented the instance from joining the domain\. Check and fix your configuration before re\-issuing the instance modify command\. 
+  **removing** – The instance is being removed from the domain\. 

 A request to become a member of a domain can fail because of a network connectivity issue or an incorrect IAM role\. If you create a DB instance or modify an existing instance and the attempt to become a member of a domain fails, you should re\-issue the modify command or modify the newly created instance to join the domain\. 

## Connecting to SQL Server with Windows Authentication<a name="USER_SQLServerWinAuth.Connecting"></a>

 To connect to SQL Server with Windows Authentication, you must be logged into a domain\-joined computer as a domain user\. After launching SQL Server Management Studio, choose **Windows Authentication** as the authentication type, as shown following\. 

![\[Connect to SQL Server 2012\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/WinAuth4.png)

## Restoring a SQL Server DB Instance and then Adding It to a Domain<a name="USER_SQLServerWinAuth.Restore"></a>

You can restore a DB snapshot or do a point\-in\-time restore \(PITR\) for a SQL Server DB instance and then add it to a domain\. Once the DB instance is restored, modify the instance using the process explained in [Step 4: Create or Modify a SQL Server DB Instance](#USER_SQLServerWinAuth.SettingUp.CreateModify) to add the DB instance to a domain\. 

## Related Topics<a name="USER_SQLServerWinAuth.related"></a>
+ [Microsoft SQL Server on Amazon RDS](CHAP_SQLServer.md)
+ [Security in Amazon RDS](UsingWithRDS.md)