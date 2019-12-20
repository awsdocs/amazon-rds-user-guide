# mysql\.rds\_enable\_gsh\_rotation<a name="mysql_rds_enable_gsh_rotation"></a>

Enables rotation of the contents of the `mysql.global_status_history` table to `mysql.global_status_history_old` at intervals specified by `rds_set_gsh_rotation`\. For more information, see [Managing the Global Status History](Appendix.MySQL.CommonDBATasks.md#Appendix.MySQL.CommonDBATasks.GoSH)\.

## Syntax<a name="mysql_rds_enable_gsh_rotation-syntax"></a>

```
CALL mysql.rds_enable_gsh_rotation;
```