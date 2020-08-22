# Connecting to PostgreSQL with Kerberos Authentication<a name="postgresql-kerberos-connecting"></a>

You can connect to PostgreSQL with Kerberos authentication with the pgAdmin interface or with a command line interface such as psql\. For more information about connecting, see  [Connecting to a DB Instance Running the PostgreSQL Database Engine](USER_ConnectToPostgreSQLInstance.md)  \. 

## pgAdmin<a name="collapsible-section-pgAdmin"></a>

To use pgAdmin to connect to PostgreSQL with Kerberos authentication, take the following steps:

1. Launch the pgAdmin application on your client computer\.

1. On the **Dashboard** tab, choose **Add New Server**\.

1. In the **Create \- Server** dialog box, enter a name on the **General** tab to identify the server in pgAdmin\.

1. On the **Connection** tab, enter the following information from your RDS for PostgreSQL database:
   + For **Host**, enter the endpoint\. Use a format such as `PostgreSQL-endpoint.AWS-Region.rds.amazonaws.com`\.

     If you're using an on\-premises Microsoft Active Directory from a Windows client, then you need to connect using a special endpoint\. Instead of using the Amazon domain `rds.amazonaws.com` in the host endpoint, use the domain name of the AWS Managed Active Directory\. 

     For example, suppose that the domain name for the AWS Managed Active Directory is `corp.example.com`\. Then for **Host**, use the format `PostgreSQL-endpoint.AWS-Region.corp.example.com.`
   + For **Port**, enter the assigned port\.
   + For **Maintenance database**, enter the name of the initial database to which the client will connect\.
   + For **Username**, enter the user name that you entered for Kerberos authentication in [ Step 7: Create Kerberos Authentication PostgreSQL Logins ](postgresql-kerberos-setting-up.md#postgresql-kerberos-setting-up.create-logins)\. 

1. Choose **Save**\.

## psql<a name="collapsible-section-psql"></a>

To use psql to connect to PostgreSQL with Kerberos authentication, take the following steps:

1. At a command prompt, run the following command\.

   ```
   kinit username                
   ```

   Replace *`username`* with the user name\. At the prompt, enter the password stored in the Microsoft Active Directory for the user\.

1. If the PostgreSQL DB instance is using a publicly accessible VPC, put a private IP address for your DB instance endpoint in your `/etc/hosts` file on the EC2 client\. For example, the following commands obtain the private IP address and then put it in the `/etc/hosts` file\.

   ```
   % dig +short PostgreSQL-endpoint.AWS-Region.rds.amazonaws.com  
   ;; Truncated, retrying in TCP mode.
   ec2-34-210-197-118.AWS-Region.compute.amazonaws.com.
   34.210.197.118 
   
   % echo " 34.210.197.118  PostgreSQL-endpoint.AWS-Region.rds.amazonaws.com" >> /etc/hosts
   ```

   If you're using an on\-premises Microsoft Active Directory from a Windows client, then you need to connect using a special endpoint\. Instead of using the Amazon domain `rds.amazonaws.com` in the host endpoint, use the domain name of the AWS Managed Active Directory\.

   For example, suppose that the domain name for your AWS Managed Active Directory is `corp.example.com`\. Then use the format `PostgreSQL-endpoint.AWS-Region.corp.example.com` for the endpoint and put it in the `/etc/hosts` file\.

   ```
   % echo " 34.210.197.118  PostgreSQL-endpoint.AWS-Region.corp.example.com" >> /etc/hosts
   ```

1. Use the following psql command to log in to a PostgreSQL DB instance that is integrated with Active Directory\. 

   ```
   psql -U username@CORP.EXAMPLE.COM -p 5432 -h PostgreSQL-endpoint.AWS-Region.rds.amazonaws.com postgres
   ```

   To log in to the PostgreSQL DB cluster from a Windows client using an on\-premises Active Directory, use the following psql command with the domain name from the previous step \(`corp.example.com`\):

   ```
   psql -U username@CORP.EXAMPLE.COM -p 5432 -h PostgreSQL-endpoint.AWS-Region.corp.example.com postgres
   ```