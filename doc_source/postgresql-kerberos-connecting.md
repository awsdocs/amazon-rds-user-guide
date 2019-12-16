# Connecting to PostgreSQL with Kerberos Authentication<a name="postgresql-kerberos-connecting"></a>

You can connect to PostgreSQL with Kerberos authentication with the pgAdmin interface or with a command line interface such as psql\. For more information about connecting, see [Connecting to a DB Instance Running the PostgreSQL Database Engine ](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_ConnectToPostgreSQLInstance.html)\. 

## pgAdmin<a name="collapsible-section-pgAdmin"></a>

To use pgAdmin to connect to PostgreSQL with Kerberos authentication, take the following steps:

1. Launch the pgAdmin application on your client computer\.

1. On the **Dashboard** tab, choose **Add New Server**\.

1. In the **Create \- Server** dialog box, type a name on the **General** tab to identify the server in pgAdmin\.

1. On the **Connection** tab, type the following information from your DB instance:
   + For **Host**, type the endpoint\. For example, use the format *PostgreSQL\-instance\-endpoint*\.*AWS\-Region*\.rds\.amazonaws\.com\.
   + For **Port**, type the assigned port\.
   + For **Maintenance database**, type the name of the initial database to which the client will connect\.
   + For **Username**, type the user name that you entered when you created the DB instance\.
   + For **Password**, type the password that you entered when you created the DB instance\.

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
   % dig +short PostgreSQL-instance-endpoint.AWS-Region.rds.amazonaws.com  
   ;; Truncated, retrying in TCP mode.
   ec2-34-210-197-118.AWS-Region.compute.amazonaws.com.
   34.210.197.118 
   
   % echo " 34.210.197.118  PostgreSQL-instance-endpoint.AWS-Region.rds.amazonaws.com" >> /etc/hosts
   ```

1. Use the following psql command to log in to a PostgreSQL DB instance that is integrated with Microsoft Active Directory\.

   ```
   psql postgres -U username@CORP.EXAMPLE.COM -p 5432 postgres -h PostgreSQL-instance-endpoint.AWS-Region.rds.amazonaws.com
   ```