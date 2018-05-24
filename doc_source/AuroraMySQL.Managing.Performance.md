# Managing Performance and Scaling for Amazon Aurora MySQL<a name="AuroraMySQL.Managing.Performance"></a>

## Scaling Aurora MySQL DB Instances<a name="AuroraMySQL.Managing.Performance.InstanceScaling"></a>

You can scale Aurora MySQL DB instances in two ways, instance scaling and read scaling\. For more information about read scaling, see [Read Scaling](Aurora.Managing.md#Aurora.Managing.Performance.ReadScaling)\.

You can scale your Aurora MySQL DB cluster by modifying the DB instance class for each DB instance in the DB cluster\. Aurora MySQL supports several DB instance classes optimized for Aurora\. The following table describes the specifications of the DB instance classes supported by Aurora MySQL\.


| Instance Class | vCPU | ECU | Memory \(GiB\) | 
| --- | --- | --- | --- | 
|  db\.t2\.small  |  1  | 1 | 2 | 
|  db\.t2\.medium  |  2  | 2 | 4 | 
|  db\.r3\.large  |  2  | 6\.5 | 15\.25 | 
|  db\.r3\.xlarge  |  4  | 13 | 30\.5 | 
|  db\.r3\.2xlarge  |  8  | 26 | 61 | 
|  db\.r3\.4xlarge  |  16  | 52 | 122 | 
|  db\.r3\.8xlarge  |  32  | 104 | 244 | 
|  db\.r4\.large  |  2  | 7 | 15\.25 | 
|  db\.r4\.xlarge  |  4  | 13\.5 | 30\.5 | 
|  db\.r4\.2xlarge  |  8  | 27 | 61 | 
|  db\.r4\.4xlarge  |  16  | 53 | 122 | 
|  db\.r4\.8xlarge  |  32  | 99 | 244 | 
|  db\.r4\.16xlarge  |  64  | 195 | 488 | 

## Maximum Connections to an Aurora MySQL DB Instance<a name="AuroraMySQL.Managing.MaxConnections"></a>

The maximum number of connections allowed to an Aurora MySQL DB instance is determined by the `max_connections` parameter in the instance\-level parameter group for the DB instance\. By default, this value is set to the following equation \(the log function represents log base 2\):

`GREATEST({log(DBInstanceClassMemory/805306368)*45},{log(DBInstanceClassMemory/8187281408)*1000})`\.

Setting the `max_connections` parameter to this equation makes sure that the number of allowed connection scales well with the size of the instance\. For example, suppose your DB instance class is db\.r3\.xlarge, which has 30\.5 gibibytes \(GiB\) of memory\. Then the maximum connections allowed is 2000, as shown in the following equation:

```
log( (30.5 * 1073741824) / 8187281408 ) * 1000 = 2000
```

The following table lists the resulting default value of `max_connections` for each DB instance class available to Aurora MySQL\. You can increase the maximum number of connections to your Aurora MySQL DB instance by scaling the instance up to a DB instance class with more memory, or by setting a larger value for the `max_connections` parameter, up to 16,000\.


| Instance Class | max\_connections Default Value | 
| --- | --- | 
|  db\.t2\.small  |  45  | 
|  db\.t2\.medium  |  90  | 
|  db\.r3\.large  |  1000  | 
|  db\.r3\.xlarge  |  2000  | 
|  db\.r3\.2xlarge  |  3000  | 
|  db\.r3\.4xlarge  |  4000  | 
|  db\.r3\.8xlarge  |  5000  | 
|  db\.r4\.large  |  1000  | 
|  db\.r4\.xlarge  |  2000  | 
|  db\.r4\.2xlarge  |  3000  | 
|  db\.r4\.4xlarge  |  4000  | 
|  db\.r4\.8xlarge  |  5000  | 
|  db\.r4\.16xlarge  |  6000  | 