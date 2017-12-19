# Creating a DB Instance Running the PostgreSQL Database Engine<a name="USER_CreatePostgreSQLInstance"></a>

 The basic building block of Amazon RDS is the DB instance\. This is the environment in which you will run your PostgreSQL databases\.

**Important**  
You must complete the tasks in the [Setting Up for Amazon RDS](CHAP_SettingUp.md) section before you can create or connect to a DB instance\.

## AWS Management Console<a name="USER_CreatePostgreSQLInstance.CON"></a>

**To launch a PostgreSQL DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the top right corner of the AWS Management Console, select the AWS Region where you want to create the DB instance\. 

1. In the navigation pane, click **Instances**\.

1. Click **Launch DB Instance** to start the **Launch DB Instance Wizard**\.

    The wizard opens on the **Select Engine** page\.   
![\[Engine selection\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/Postgres-Launch01.png)

1. On the **Select Engine** page, click the PostgreSQL icon and then click the **Select** button for the PostgreSQL DB engine\.

1. Next, the **Production?** page asks if you are planning to use the DB instance you are creating for production\. If you are, select **Yes**\. By selecting **Yes**, the failover option **Multi\-AZ** and the **Provisioned IOPS** storage option are preselected in the following step\. Click **Next** when you are finished\.

1. On the **Specify DB Details** page, specify your DB instance information\. Click **Next** when you are finished\.    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_CreatePostgreSQLInstance.html)  
![\[DB instance details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/Postgres-Launch02.png)

1.  On the **Configure Advanced Settings** page, provide additional information that Amazon RDS needs to launch the PostgreSQL DB instance\. The table shows settings for an example DB instance\. Specify your DB instance information, then click **Launch DB Instance**\.    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_CreatePostgreSQLInstance.html)  
![\[Additional Configuration panel\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/Postgres-Launch03.png)

1.  On the final page of the wizard, click **Close**\. 

1. On the Amazon RDS console, the new DB instance appears in the list of DB instances\. The DB instance will have a status of **creating** until the DB instance is created and ready for use\. When the state changes to **available**, you can connect to the DB instance\. Depending on the DB instance class and store allocated, it could take several minutes for the new instance to be available\.  
![\[My DB instances list\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/Postgres-Launch06.png)

## CLI<a name="USER_CreatePostgreSQLInstance.CLI"></a>

To create a PostgreSQL DB instance, use the AWS CLI [http://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html](http://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html) command with the following parameters:

+ `--db-instance-identifier`

+ `--allocated-storage`

+ `--db-instance-class`

+ `--engine`

+ `--master-username`

+ `--master-user-password`

**Example**  
For Linux, OS X, or Unix:  

```
1. aws rds create-db-instance 
2.     --db-instance-identifier pgdbinstance \
3.     --allocated-storage 20 \ 
4.     --db-instance-class db.t2.small \
5.     --engine postgres \
6.     --master-username masterawsuser \
7.     --master-user-password masteruserpassword
```
For Windows:  

```
1. aws rds create-db-instance 
2.     --db-instance-identifier pgdbinstance ^
3.     --allocated-storage 20 ^ 
4.     --db-instance-class db.t2.small ^
5.     --engine postgres ^
6.     --master-username masterawsuser ^
7.     --master-user-password masteruserpassword
```
This command should produce output similar to the following:  

```
1. DBINSTANCE  pgdbinstance  db.t2.small  postgres  20  sa  creating  3  ****  n  9.3
2. SECGROUP  default  active
3. PARAMGRP  default.PostgreSQL9.3  in-sync
```

## API<a name="USER_CreatePostgreSQLInstance.API"></a>

To create a PostgreSQL DB instance, use the Amazon RDS API[http://docs.aws.amazon.com/](http://docs.aws.amazon.com/) command with the following parameters:

+ `Engine = postgres`

+ `DBInstanceIdentifier = pgdbinstance`

+ `DBInstanceClass = db.t2.small`

+ `AllocatedStorage = 20`

+ `BackupRetentionPeriod = 3`

+ `MasterUsername = masterawsuser`

+ `MasterUserPassword = masteruserpassword`

**Example**  

```
 1. https://rds.amazonaws.com/
 2.     ?Action=CreateDBInstance
 3.     &AllocatedStorage=20
 4.     &BackupRetentionPeriod=3
 5.     &DBInstanceClass=db.t2.small
 6.     &DBInstanceIdentifier=pgdbinstance
 7.     &DBName=mydatabase
 8.     &DBSecurityGroups.member.1=mysecuritygroup
 9.     &DBSubnetGroup=mydbsubnetgroup
10.     &Engine=postgres
11.     &MasterUserPassword=<masteruserpassword>
12.     &MasterUsername=<masterawsuser>
13.     &SignatureMethod=HmacSHA256
14.     &SignatureVersion=4
15.     &Version=2013-09-09
16.     &X-Amz-Algorithm=AWS4-HMAC-SHA256
17.     &X-Amz-Credential=AKIADQKE4SARGYLE/20140212/us-west-2/rds/aws4_request
18.     &X-Amz-Date=20140212T190137Z
19.     &X-Amz-SignedHeaders=content-type;host;user-agent;x-amz-content-sha256;x-amz-date
20.     &X-Amz-Signature=60d520ca0576c191b9eac8dbfe5617ebb6a6a9f3994d96437a102c0c2c80f88d
```

## Related Topics<a name="USER_CreatePostgreSQLInstance.related"></a>

+  [Amazon RDS DB Instances](Overview.DBInstance.md) 

+  [DB Instance Class](Concepts.DBInstanceClass.md) 

+  [Deleting a DB Instance](USER_DeleteInstance.md) 