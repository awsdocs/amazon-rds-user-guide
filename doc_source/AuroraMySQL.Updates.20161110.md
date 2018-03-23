# Amazon Aurora MySQL Database Engine Updates: 2016\-11\-10<a name="AuroraMySQL.Updates.20161110"></a>

**Version:** 1\.9\.0, 1\.9\.1

## New Features<a name="AuroraMySQL.Updates.20161110.New"></a>
+ **Improved index build** – The implementation for building secondary indexes now operates by building the index in a bottom\-up fashion, which eliminates unnecessary page splits\. This can reduce the time needed to create an index or rebuild a table by up to 75% \(based on an `db.r3.8xlarge` DB instance class\)\. This feature was in lab mode in Aurora MySQL version 1\.7 and is enabled by default in Aurora version 1\.9 and later\. For information, see [Aurora Lab Mode](AuroraMySQL.Updates.md#AuroraMySQL.Updates.LabMode)\.
+ **Lock compression \(lab mode\)** – This implementation significantly reduces the amount of memory that lock manager consumes by up to 66%\. Lock manager can acquire more row locks without encountering an out\-of\-memory exception\. This feature is disabled by default and can be activated by enabling Aurora lab mode\. For information, see [Aurora Lab Mode](AuroraMySQL.Updates.md#AuroraMySQL.Updates.LabMode)\.
+ **Performance schema** – Amazon Aurora MySQL now includes support for performance schema with minimal impact on performance\. In our testing using SysBench, enabling performance schema could degrade MySQL performance by up to 60%\.

  SysBench testing of an Aurora DB cluster showed an impact on performance that is 4x less than MySQL\. Running the `db.r3.8xlarge` DB instance class resulted in 100K SQL writes/sec and over 550K SQL reads/sec, even with performance schema enabled\.
+ **Hot row contention improvement** – This feature reduces CPU utilization and increases throughput when a small number of hot rows are accessed by a large number of connections\. This feature also eliminates ` error 188` when there is hot row contention\.
+ **Improved out\-of\-memory handling** – When non\-essential, locking SQL statements are executed and the reserved memory pool is breached, Aurora forces rollback of those SQL statements\. This feature frees memory and prevents engine crashes due to out\-of\-memory exceptions\.
+ **Smart read selector** – This implementation improves read latency by choosing the optimal storage segment among different segments for every read, resulting in improved read throughput\. SysBench testing has shown up to a 27% performance increase for write workloads \.

## Improvements<a name="AuroraMySQL.Updates.20161110.Improvements"></a>
+ Fixed an issue where an Aurora Replica encounters a shared lock during engine start up\.
+ Fixed a potential crash on an Aurora Replica when the read view pointer in the purge system is NULL\.