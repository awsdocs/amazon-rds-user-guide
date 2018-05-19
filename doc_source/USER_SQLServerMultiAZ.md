# Multi\-AZ Deployments for Microsoft SQL Server with Database Mirroring<a name="USER_SQLServerMultiAZ"></a>

Amazon RDS supports Multi\-AZ deployments for DB instances running Microsoft SQL Server by using SQL Server Database Mirroring\. Multi\-AZ deployments provide increased availability, data durability, and fault tolerance for DB instances\. In the event of planned database maintenance or unplanned service disruption, Amazon RDS automatically fails over to the up\-to\-date standby so database operations can resume quickly without manual intervention\. The primary and standby instances use the same endpoint, whose physical network address transitions to the mirror as part of the failover process\. You don't have to reconfigure your application when a failover occurs\. 

Amazon RDS manages failover by actively monitoring your Multi\-AZ deployment and initiating a failover when a problem with your primary occurs\. Failover doesn't occur unless the standby and primary are fully in sync\. Amazon RDS actively maintains your Multi\-AZ deployment by automatically repairing unhealthy DB instances and reestablishing synchronous replication\. You don't have to manage anything; Amazon RDS handles the primary, the Mirroring witness, and the standby instance for you\. When you set up SQL Server Multi\-AZ, all databases on the instance are mirrored automatically\. 

Amazon RDS supports Multi\-AZ with Mirroring for the following SQL Server versions and editions:
+ SQL Server 2017: Standard and Enterprise Editions
+ SQL Server 2016: Standard and Enterprise Editions
+ SQL Server 2014: Standard and Enterprise Editions
+ SQL Server 2012: Standard and Enterprise Editions
+ SQL Server 2008 R2: Standard and Enterprise Editions

Amazon RDS supports Multi\-AZ with Mirroring for SQL Server in all AWS Regions, with the following exceptions:
+ Not supported 
  + US West \(N\. California\)
+ Supported in most cases 
  + Asia Pacific \(Sydney\) – Supported for [DB instances in VPCs](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_VPC.WorkingWithRDSInstanceinaVPC.html#USER_VPC.Non-VPC2VPC)\.
  + Asia Pacific \(Tokyo\) – Supported for [DB instances in VPCs](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_VPC.WorkingWithRDSInstanceinaVPC.html#USER_VPC.Non-VPC2VPC)\.
  + South America \(São Paulo\) – Supported on all [DB instance classes](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Concepts.DBInstanceClass.html) except m1 or m2\.

## Adding Multi\-AZ with Mirroring to a Microsoft SQL Server DB Instance<a name="USER_SQLServerMultiAZ.Adding"></a>

When creating a new SQL Server DB instance using the AWS Management Console, you can simply select **Yes \(Mirroring\)** from the **Multi\-AZ Deployment** list on the **Specify DB Details** page to add Multi\-AZ with Mirroring\. For more information, see [Creating a DB Instance Running the Microsoft SQL Server Database Engine](USER_CreateMicrosoftSQLServerInstance.md)\. 

When modifying an existing SQL Server DB instance using the AWS Management Console, you can simply select **Yes \(Mirroring\)** from the **Multi\-AZ Deployment** list on the **Modify DB Instance** page to add Multi\-AZ with Mirroring\. For more information, see [Modifying a DB Instance Running the Microsoft SQL Server Database Engine](USER_ModifyInstance.SQLServer.md)\. 

## Microsoft SQL Server Multi\-AZ Deployment Notes and Recommendations<a name="USER_SQLServerMultiAZ.Recommendations"></a>

The following are some restrictions when working with Multi\-AZ deployments for Microsoft SQL Server DB instances: 
+ Cross\-region Multi\-AZ is not currently supported\. 
+ You can't configure the standby to accept database read activity\. 
+ Multi\-AZ with Mirroring is not supported for DB instances with dedicated tenancy\. 
+ Multi\-AZ with Mirroring is not supported for DB instances with in\-memory optimization enabled\. For more information, see [Unsupported SQL Server Features for In\-Memory OLTP](https://msdn.microsoft.com/en-us/library/dn133181.aspx) in the Microsoft documentation\. 
+ You can't rename a database on a SQL Server DB instance that is in a SQL Server Multi\-AZ with Mirroring deployment\. If you need to rename a database on such an instance, first turn off Multi\-AZ for the DB instance, then rename the database, and finally turn Multi\-AZ back on for the DB instance\. 

The following are some notes about working with Multi\-AZ deployments for Microsoft SQL Server DB instances: 
+ To use SQL Server Multi\-AZ with Mirroring with a SQL Server DB instance in a VPC, you first create a DB subnet group that has subnets in at least two distinct Availability Zones\. You then assign the DB subnet group to the SQL Server DB instance that is being mirrored\. 
+ When a DB instance is modified to be a Multi\-AZ deployment, during the modification it has a status of **modifying**\. Amazon RDS creates the standby mirror, and makes a backup of the primary DB instance\. Once the process is complete, the status of the primary DB instance becomes **available**\. 
+ Multi\-AZ deployments maintain all databases on the same node\. If a database on the primary host fails over, all your SQL Server databases fail over as one atomic unit to your standby host\. Amazon RDS provisions a new healthy host, and replace the unhealthy host\. 
+ Multi\-AZ with Mirroring supports one standby mirror\. 
+ Users, logins, and permissions are automatically replicated for you on the standby mirror\. You don’t need to recreate them\. User\-defined server roles \(a SQL Server 2012 feature\) are not replicated in Multi\-AZ instances\. 
+ If you have SQL Server Agent jobs, you need to recreate them in the secondary, as these jobs are stored in the msdb database, and this database can't be replicated via Mirroring\. Create the jobs first in the original primary, then fail over, and create the same jobs in the new primary\. 
+ You might observe elevated latencies compared to a standard DB instance deployment \(in a single Availability Zone\) as a result of the synchronous data replication performed on your behalf\. 
+ Failover times are affected by the time it takes to complete the recovery process\. Large transactions increase the failover time\. 
+ When you restore a backup file to a Multi\-AZ DB instance, mirroring is terminated and then reestablished\. Mirroring is terminated and reestablished for all databases on the DB instance, not just the one you are restoring\. While RDS reestablishes mirroring, your DB instance can't failover\. It can take 30 minutes or more to reestablish mirroring, depending on the size of the restore\. For more information, see [Importing and Exporting SQL Server Databases](SQLServer.Procedural.Importing.md)\. 

The following are some recommendations for working with Multi\-AZ deployments for Microsoft SQL Server DB instances: 
+ For databases used in production or preproduction, we recommend Multi\-AZ deployments for high availability, Provisioned IOPS for fast, consistent performance, and instance classes \(m3\.large and larger, m4\.large and larger\) that are optimized for Provisioned IOPS\. 
+ You can't select the Availability Zone \(AZ\) for the standby instance, so when you deploy application hosts, take this into account\. Your database could fail over to another AZ, and the application hosts might not be in the same AZ as the database\. For this reason, it is a best practice to balance your application hosts across all AZs in the region\. 
+ For best performance, do not enable mirroring during a large data load operation\. If you want your data load to be as fast as possible, complete the loading before you convert your DB instance to a Multi\-AZ deployment\. 
+ Applications that access the SQL Server databases should have exception handling that catches connection errors\. The following code sample shows a try/catch block that catches a communication error\. 

  ```
  for (int iRetryCount = 0; (iRetryCount < RetryMaxAttempts && keepInserting); iRetryCount++) 
  {
     using (SqlConnection connection = new SqlConnection(DatabaseConnString)) 
     {
        using (SqlCommand command = connection.CreateCommand()) 
        {
           command.CommandText = "INSERT INTO SOME_TABLE VALUES ('SomeValue');";
  
           try 
           {
              connection.Open();
  				
              while (keepInserting) 
              {
                 command.ExecuteNonQuery();
                 intervalCount++;
              }
                    connection.Close();          
           }
           
           catch (Exception ex) 
           {
                    Logger(ex.Message);
           }
        }
     }
  
     if (iRetryCount < RetryMaxAttempts && keepInserting) 
     {
              Thread.Sleep(RetryIntervalPeriodInSeconds * 1000);
     }
  }
  ```
+ You should not use the `Set Partner Off` command when working with Multi\-AZ instances\. For example, *do not* do the following: 

  ```
  ALTER DATABASE db1 SET PARTNER off   
  ```
+ You should not set the recovery mode to `simple`\. For example, *do not* do the following: 

  ```
  ALTER DATABASE db1 SET RECOVERY simple   
  ```
+ You should not use the `DEFAULT_DATABASE` parameter when creating new logins on Multi\-AZ DB instances, as these settings can't be applied to the standby mirror\. For example, *do not* do the following: 

  ```
  CREATE LOGIN [test_dba] WITH PASSWORD=foo, DEFAULT_DATABASE=[db2]   
  ```

  and *do not* do the following:

  ```
  ALTER LOGIN [test_dba] SET DEFAULT_DATABASE=[db3]   
  ```

## Determining the Location of the Standby Mirror<a name="USER_SQLServerMultiAZ.Location"></a>

You can determine the location of the standby mirror by using the AWS Management Console\. You need to know the location of the standby mirror if you are setting up your primary DB instance in a VPC\. 

![\[Single AZ Scenario\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/SQLSvr-MultiAZ.png)

You can also view the Availability Zone of the standby mirror using the AWS CLI command `describe-db-instances` or RDS API action `DescribeDBInstances`\. The output will show the secondary AZ where the standby mirror is located\. 

## Related Topics<a name="USER_SQLServerMultiAZ.related"></a>
+ [Licensing Microsoft SQL Server on Amazon RDS](SQLServer.Concepts.General.Licensing.md)
+ [High Availability \(Multi\-AZ\)](Concepts.MultiAZ.md)
