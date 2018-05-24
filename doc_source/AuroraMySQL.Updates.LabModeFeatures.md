# Aurora Lab Mode Features<a name="AuroraMySQL.Updates.LabModeFeatures"></a>

The following table lists the Aurora features currently available when Aurora lab mode is enabled\. You must enable Aurora lab mode before any of these features can be used\. For more information about Aurora lab mode, see [Aurora Lab Mode](AuroraMySQL.Updates.md#AuroraMySQL.Updates.LabMode)\.


| Feature | Description | 
| --- | --- | 
|  Scan Batching  |  Aurora MySQL scan batching speeds up in\-memory, scan\-oriented queries significantly\. The feature boosts the performance of table full scans, index full scans, and index range scans by batch processing\.  | 
|  Hash Joins  |  This feature can improve query performance when you need to join a large amount of data by using an equijoin\. For more information about using this feature, see [Working with Hash Joins in Aurora MySQL](AuroraMySQL.BestPractices.md#Aurora.BestPractices.HashJoin)\.  | 
|  Fast DDL  |  This feature allows you to execute an ALTER TABLE *tbl\_name* ADD COLUMN *col\_name* *column\_definition* operation nearly instantaneously\. The operation completes without requiring the table to be copied and without materially impacting other DML statements\. Since it does not consume temporary storage for a table copy, it makes DDL statements practical even for large tables on small instance types\. Fast DDL is currently only supported for adding a nullable column, without a default value, at the end of a table\. For more information about using this feature, see [Altering Tables in Amazon Aurora Using Fast DDL](AuroraMySQL.Managing.FastDDL.md)\.  | 
|  Lock Compression  |  This feature significantly reduces the amount of memory that lock manager consumes by up to 66%\. Lock manager can acquire more row locks without encountering an out\-of\-memory exception\.  | 