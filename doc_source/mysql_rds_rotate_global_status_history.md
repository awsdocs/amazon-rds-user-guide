# mysql\.rds\_rotate\_global\_status\_history<a name="mysql_rds_rotate_global_status_history"></a>

Rotates the contents of the `mysql.global_status_history` table to `mysql.global_status_history_old` on demand\. For more information, see [Managing the Global Status History](Appendix.MySQL.CommonDBATasks.md#Appendix.MySQL.CommonDBATasks.GoSH)\.

## Syntax<a name="mysql_rds_rotate_global_status_history-syntax"></a>

```
CALL mysql.rds_rotate_global_status_history;
```