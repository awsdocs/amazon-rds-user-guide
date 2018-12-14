# mysql\.rds\_set\_gsh\_collector<a name="mysql_rds_set_gsh_collector"></a>

Specifies the interval, in minutes, between snapshots taken by the Global Status History \(GoSH\)\. Default value is For more information, see [Managing the Global Status History](Appendix.MySQL.CommonDBATasks.md#Appendix.MySQL.CommonDBATasks.GoSH)\. 

## Syntax<a name="mysql_rds_set_gsh_collector-syntax"></a>

```
CALL mysql.rds_set_gsh_collector(intervalPeriod);
```

## Parameters<a name="mysql_rds_set_gsh_collector-parameters"></a>

 *intervalPeriod*   
The interval, in minutes, between snapshots\. Default value is 