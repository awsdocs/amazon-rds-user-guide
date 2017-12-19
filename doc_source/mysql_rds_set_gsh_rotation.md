# mysql\.rds\_set\_gsh\_rotation<a name="mysql_rds_set_gsh_rotation"></a>

Specifies the interval, in days, between rotations of the `mysql.global_status_history` table\. Default value is 7\. For more information, see [Managing the Global Status History](Appendix.MySQL.CommonDBATasks.md#Appendix.MySQL.CommonDBATasks.GoSH)\.

## Syntax<a name="mysql_rds_set_gsh_rotation-syntax"></a>

```
CALL mysql.rds_set_gsh_rotation(intervalPeriod);
```

## Parameters<a name="mysql_rds_set_gsh_rotation-parameters"></a>

 *intervalPeriod*   
The interval, in days, between table rotations\. Default value is 7\. 

## Related Topics<a name="mysql_rds_set_gsh_rotation.related"></a>

+ [mysql\.rds\_enable\_gsh\_collector](mysql_rds_enable_gsh_collector.md)

+ [mysql\.rds\_set\_gsh\_collector](mysql_rds_set_gsh_collector.md)

+ [mysql\.rds\_disable\_gsh\_collector](mysql_rds_disable_gsh_collector.md)

+ [mysql\.rds\_collect\_global\_status\_history](mysql_rds_collect_global_status_history.md)

+ [mysql\.rds\_enable\_gsh\_rotation](mysql_rds_enable_gsh_rotation.md)

+ [mysql\.rds\_disable\_gsh\_rotation](mysql_rds_disable_gsh_rotation.md)

+ [mysql\.rds\_rotate\_global\_status\_history](mysql_rds_rotate_global_status_history.md)