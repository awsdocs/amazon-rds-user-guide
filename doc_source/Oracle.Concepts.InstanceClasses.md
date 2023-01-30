# RDS for Oracle instance classes<a name="Oracle.Concepts.InstanceClasses"></a>

The computation and memory capacity of a DB instance is determined by its instance class\. The DB instance class you need depends on your processing power and memory requirements\.



## Supported RDS for Oracle instance classes<a name="Oracle.Concepts.InstanceClasses.Supported"></a>

The supported RDS for Oracle instance classes are a subset of the RDS DB instance classes\. For the complete list of RDS instance classes, see [DB instance classes](Concepts.DBInstanceClass.md)\.

RDS for Oracle also offers instance classes that are optimized for workloads that require additional memory, storage, and I/O per vCPU\. These instance classes use the following naming convention:

```
db.r5b.instance_size.tpcthreads_per_core.memratio
db.r5.instance_size.tpcthreads_per_core.memratio
```

The following is an example of a supported instance class:

```
db.r5b.4xlarge.tpc2.mem2x
```

The components of the preceding instance class name are as follows:
+ `db.r5b.4xlarge` – The name of the instance class\.
+ `tpc2` – The threads per core\. A value of 2 means that multithreading is turned on\. If the value is 1, multithreading is turned off\. 
+ `mem2x` – The ratio of additional memory to the standard memory for the instance class\. In this example, the optimization provides twice as much memory as a standard db\.r5\.4xlarge instance\. 

The following table lists all instance classes supported for Oracle Database\. Oracle Database 12c Release 1 \(12\.1\.0\.2\) and Oracle Database 12c Release 2 \(12\.2\.0\.2\) are listed in the table, but support for these releases is deprecated\. For information about the memory attributes of each type, see [ RDS for Oracle instance types](http://aws.amazon.com/rds/oracle/instance-types)\.


****  
<a name="rds-oracle-instance-class-reference"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Oracle.Concepts.InstanceClasses.html)

**Note**  
We encourage all BYOL customers to consult their licensing agreement to assess the impact of Amazon RDS for Oracle deprecations\. For more information on the compute capacity of DB instance classes supported by RDS for Oracle, see [DB instance classes](Concepts.DBInstanceClass.md) and [Configuring the processor for a DB instance class](Concepts.DBInstanceClass.md#USER_ConfigureProcessor)\.

**Note**  
If you have DB snapshots of DB instances that were using deprecated DB instance classes, you can choose a DB instance class that is not deprecated when you restore the DB snapshots\. For more information, see [Restoring from a DB snapshot](USER_RestoreFromSnapshot.md)\.

## Deprecated Oracle DB instance classes<a name="Oracle.Concepts.InstanceClasses.Deprecated"></a>

The following DB instance classes are deprecated for RDS for Oracle:
+ db\.m1, db\.m2, db\.m3, db\.m4
+ db\.t3\.micro \(supported only on 12\.1\.0\.2, which is deprecated\)
+ db\.t1, db\.t2
+ db\.r1, db\.r2, db\.r3, db\.r4

The preceding DB instance classes have been replaced by better performing DB instance classes that are generally available at a lower cost\. If you have DB instances that use deprecated DB instance classes, you have the following options:
+ Allow Amazon RDS to modify each DB instance automatically to use a comparable non\-deprecated DB instance class\. For deprecation timelines, see [DB instance class types](Concepts.DBInstanceClass.md#Concepts.DBInstanceClass.Types)\.
+ Change the DB instance class yourself by modifying the DB instance\. For more information, see [Modifying an Amazon RDS DB instance](Overview.DBInstance.Modifying.md)\. 

If you have DB snapshots of DB instances that were using deprecated DB instance classes, you can choose a DB instance class that is not deprecated when you restore the DB snapshots\. For more information, see [Restoring from a DB snapshot](USER_RestoreFromSnapshot.md)\.