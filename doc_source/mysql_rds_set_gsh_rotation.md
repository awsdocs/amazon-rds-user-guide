# mysql\.rds\_set\_gsh\_rotation<a name="mysql_rds_set_gsh_rotation"></a>

Specifies the interval, in days, between rotations of the `mysql.global_status_history` table\. Default value is 7\. For more information, see [Managing the Global Status History](Appendix.MySQL.CommonDBATasks.md#Appendix.MySQL.CommonDBATasks.GoSH)\.

## Syntax<a name="mysql_rds_set_gsh_rotation-syntax"></a>

```
CALL mysql.rds_set_gsh_rotation(intervalPeriod);
```

## Parameters<a name="mysql_rds_set_gsh_rotation-parameters"></a>

 *intervalPeriod*   
The interval, in days, between table rotations\. Default value is 7\. 