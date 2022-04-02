# Using PostgreSQL extensions with Amazon RDS for PostgreSQL<a name="Appendix.PostgreSQL.CommonDBATasks.Extensions"></a>

You can install and configure various PostgreSQL extensions to add more functionality to your RDS for PostgreSQL DB instance\. For example, to work with spatial data you can install and use the PostGIS extension\. For more information, see [Managing spatial data with the PostGIS extension](Appendix.PostgreSQL.CommonDBATasks.PostGIS.md)\. As another example, if you want to improve data entry for very large tables, you can consider partitioning your data by using the `pg_partman` extension\. To learn more, see [Managing PostgreSQL partitions with the pg\_partman extension](PostgreSQL_Partitions.md)\.

Following are some of the PostgreSQL extensions that you can install and use with your Amazon RDS for PostgreSQL DB instance\. 

**Topics**
+ [Using functions from the orafce extension](#Appendix.PostgreSQL.CommonDBATasks.orafce)
+ [Managing PostgreSQL partitions with the pg\_partman extension](PostgreSQL_Partitions.md)
+ [Logging at the session and object level with the pgaudit extension](#Appendix.PostgreSQL.CommonDBATasks.pgaudit)
+ [Scheduling maintenance with the PostgreSQL pg\_cron extension](PostgreSQL_pg_cron.md)
+ [Reducing bloat in tables and indexes with the pg\_repack extension](#Appendix.PostgreSQL.CommonDBATasks.pg_repack)
+ [Upgrading and using the PLV8 extension](#PostgreSQL.Concepts.General.UpgradingPLv8)
+ [Managing spatial data with the PostGIS extension](Appendix.PostgreSQL.CommonDBATasks.PostGIS.md)

## Using functions from the orafce extension<a name="Appendix.PostgreSQL.CommonDBATasks.orafce"></a>

The orafce extension provides functions and operators that emulate a subset of functions and packages from an Oracle database\. The orafce extension makes it easier for you to port an Oracle application to PostgreSQL\. RDS for PostgreSQL versions 9\.6\.6 and higher support this extension\. For more information about orafce, see [orafce](https://github.com/orafce/orafce) on GitHub\.

**Note**  
RDS for PostgreSQL doesn't support the `utl_file` package that is part of the orafce extension\. This is because the `utl_file` schema functions provide read and write operations on operating\-system text files, which requires superuser access to the underlying host\. As a managed service, RDS for PostgreSQL doesn't provide host access\.

**To use the orafce extension**

1. Connect to the DB instance with the master user name that you used to create the DB instance\. 

   If you want to turn on orafce for a different database in the same DB instance, use the `/c dbname` psql command\. Using this command, you change from the primary database after initiating the connection\.

1. Turn on the orafce extension with the `CREATE EXTENSION` statement\.

   ```
   CREATE EXTENSION orafce;
   ```

1. Transfer ownership of the oracle schema to the rds\_superuser role with the `ALTER SCHEMA` statement\.

   ```
   ALTER SCHEMA oracle OWNER TO rds_superuser;
   ```

   If you want to see the list of owners for the oracle schema, use the `\dn` psql command\.

## Logging at the session and object level with the pgaudit extension<a name="Appendix.PostgreSQL.CommonDBATasks.pgaudit"></a>

You can log activity at the session level or at the object level by installing the pgaudit extension on your RDS for PostgreSQL DB instance\. This extension is supported for all available RDS for PostgreSQL versions\. It uses the underlying native PostgreSQL logging mechanism\. 

To learn more about the pgaudit extension, see [pgAudit](https://github.com/pgaudit/pgaudit/blob/master/README.md) on GitHub\.

With *session auditing*, you can log audit events from various sources and include the fully qualified command text when available\. Modify the custom parameter group that is associated with your DB instance so that `shared_preload_libraries` contains pgaudit\. Then set the `pgaudit.log` parameter to log any of the following types of events:
+ `READ` – Logs `SELECT` and `COPY` when the source is a relation or a query\.
+ `WRITE` – Logs `INSERT`, `UPDATE`, `DELETE`, `TRUNCATE`, and `COPY` when the destination is a relation\.
+ `FUNCTION` – Logs function calls and `DO` blocks\.
+ `ROLE` – Logs statements related to roles and privileges, such as `GRANT`, `REVOKE`, `CREATE ROLE`, `ALTER ROLE`, and `DROP ROLE`\.
+ `DDL` – Logs all data definition language \(DDL\) statements that aren't included in the `ROLE` class\.
+ `MISC` – Logs miscellaneous commands, such as `DISCARD`, `FETCH`, `CHECKPOINT`, `VACUUM`, and `SET`\.

To log multiple event types with session auditing, use a comma\-separated list\. To log all event types, set `pgaudit.log` to `ALL`\. Reboot your DB instance to apply the changes\.

With *object auditing*, you can refine audit logging to work with specific relations\. For example, you can specify that you want audit logging for `READ` operations on a specific number of tables\. 

To use the pgaudit extension, add it to the `shared_preload_libraries` parameter on the RDS for PostgreSQL DB instance\. You can't edit values in the default DB parameter groups, so that means you need to use a custom DB parameter group for the DB instance\. For more information about parameter groups, see [Working with parameter groups](USER_WorkingWithParamGroups.md)\.

**To use object auditing with the pgaudit extension**

1. Create a database role called `rds_pgaudit` using the following command\.

   ```
   CREATE ROLE rds_pgaudit;
   ```

1. Modify the DB custom parameter group that is associated with your DB instance as follows:

   1. Add pgaudit to the `shared_preload_libraries` parameter list\. Using the AWS CLI, run the following\.

      ```
      aws rds modify-db-parameter-group \
         --db-parameter-group-name custom-param-group-name \
         --parameters "ParameterName=shared_preload_libraries,ParameterValue=pgaudit,ApplyMethod=pending-reboot" \
         --region aws-region
      ```

   1. Set `pgaudit.role` to the role `rds_pgaudit`\. Using the AWS CLI, run the following\.

      ```
      aws rds modify-db-parameter-group \
         --db-parameter-group-name custom-param-group-name \
         --parameters "ParameterName=pgaudit.role,ParameterValue=rds_pgaudit,ApplyMethod=pending-reboot" \
         --region aws-region
      ```

1. Reboot the DB instance so that the changes to the parameter group take effect\.

   ```
   aws rds reboot-db-instance \
       --db-instance-identifier your-RDS-db-instance \
       --region aws-region
   ```

1. Run the following command to confirm that pgaudit has been initialized\.

   ```
   SHOW shared_preload_libraries;
   shared_preload_libraries 
   --------------------------
   rdsutils,pgaudit
   (1 row)
   ```

1. Run the following command to create the pgaudit extension\.

   ```
   CREATE EXTENSION pgaudit;
   ```

1. Run the following command to confirm `pgaudit.role` is set to `rds_pgaudit`\.

   ```
   SHOW pgaudit.role;
   pgaudit.role 
   ------------------
   rds_pgaudit
   ```

To test the pgaudit logging, you can run several example commands that you want to audit\. For example, you might run the following commands\.

```
CREATE TABLE t1 (id int);
GRANT SELECT ON t1 TO rds_pgaudit;
SELECT * FROM t1;
id 
----
(0 rows)
```

The database logs should contain an entry similar to the following\.

```
...
2017-06-12 19:09:49 UTC:...:rds_test@postgres:[11701]:LOG: AUDIT:
OBJECT,1,1,READ,SELECT,TABLE,public.t1,select * from t1;
...
```

For information on viewing the logs, see [Monitoring Amazon RDS log files](USER_LogAccess.md)\.

## Reducing bloat in tables and indexes with the pg\_repack extension<a name="Appendix.PostgreSQL.CommonDBATasks.pg_repack"></a>

You can use the pg\_repack extension to remove bloat from tables and indexes\. This extension is supported on RDS for PostgreSQL versions 9\.6\.3 and higher\. For more information on the pg\_repack extension, see the [GitHub project documentation](https://reorg.github.io/pg_repack/)\.

**To use the pg\_repack extension**

1. Install the pg\_repack extension on your RDS for PostgreSQL DB instance by running the following command\.

   ```
   CREATE EXTENSION pg_repack;
   ```

1. Run the following commands to grant write access to repack temporary log tables created by pg\_repack\.

   ```
   ALTER DEFAULT PRIVILEGES IN SCHEMA repack GRANT INSERT ON TABLES TO PUBLIC;
   ALTER DEFAULT PRIVILEGES IN SCHEMA repack GRANT USAGE, SELECT ON SEQUENCES TO PUBLIC;
   ```

1. Connect to the database using the pg\_repack client utility\. Use an account that has `rds_superuser` privileges\. As an example, assume that `rds_test` role has `rds_superuser` privileges\. The command syntax is shown following\.

   ```
   pg_repack -h db-instance-name.111122223333.aws-region.rds.amazonaws.com -U rds_test -k postgres
   ```

   Connect using the \-k option\. The \-a option is not supported\.

1. The response from the pg\_repack client provides information on the tables on the DB instance that are repacked\.

   ```
   INFO: repacking table "pgbench_tellers"
   INFO: repacking table "pgbench_accounts"
   INFO: repacking table "pgbench_branches"
   ```

## Upgrading and using the PLV8 extension<a name="PostgreSQL.Concepts.General.UpgradingPLv8"></a>

PLV8 is a trusted Javascript language extension for PostgreSQL\. You can use it for stored procedures, triggers, and other procedural code that's callable from SQL\. This language extension is supported by all current releases of PostgreSQL\. 

If you use [PLV8](https://plv8.github.io/) and upgrade PostgreSQL to a new PLV8 version, you immediately take advantage of the new extension\. Take the following steps to synchronize your catalog metadata with the new version of PLV8\. These steps are optional, but we highly recommend that you complete them to avoid metadata mismatch warnings\.

The upgrade process drops all your existing PLV8 functions\. Thus, we recommend that you create a snapshot of your RDS for PostgreSQL DB instance before upgrading\. For more information, see [Creating a DB snapshot](USER_CreateSnapshot.md)\.

**To synchronize your catalog metadata with a new version of PLV8**

1. Verify that you need to update\. To do this, run the following command while connected to your instance\.

   ```
   SELECT * FROM pg_available_extensions WHERE name IN ('plv8','plls','plcoffee');
   ```

   If your results contain values for an installed version that is a lower number than the default version, continue with this procedure to update your extensions\. For example, the following result set indicates that you should update\.

   ```
   name    | default_version | installed_version |                     comment
   --------+-----------------+-------------------+--------------------------------------------------
   plls    | 2.1.0           | 1.5.3             | PL/LiveScript (v8) trusted procedural language
   plcoffee| 2.1.0           | 1.5.3             | PL/CoffeeScript (v8) trusted procedural language
   plv8    | 2.1.0           | 1.5.3             | PL/JavaScript (v8) trusted procedural language
   (3 rows)
   ```

1. Create a snapshot of your RDS for PostgreSQL DB instance if you haven't done so yet\. You can continue with the following steps while the snapshot is being created\. 

1. Get a count of the number of PLV8 functions in your DB instance so you can validate that they are all in place after the upgrade\. For example, the following SQL query returns the number of functions written in plv8, plcoffee, and plls\.

   ```
   SELECT proname, nspname, lanname 
   FROM pg_proc p, pg_language l, pg_namespace n
   WHERE p.prolang = l.oid
   AND n.oid = p.pronamespace
   AND lanname IN ('plv8','plcoffee','plls');
   ```

1. Use pg\_dump to create a schema\-only dump file\. For example, create a file on your client machine in the `/tmp` directory\.

   ```
   ./pg_dump -Fc --schema-only -U master postgres >/tmp/test.dmp
   ```

   This example uses the following options:  
   + `-Fc` – Custom format
   + \-\-schema\-only – Dump only the commands necessary to create schema \(functions in this case\)
   + `-U` – The RDS master user name
   + `database` – The database name for our DB instance

   For more information on pg\_dump, see [pg\_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html ) in the PostgreSQL documentation\.

1. Extract the "CREATE FUNCTION" DDL statement that is present in the dump file\. The following example uses the `grep` command to extract the DDL statement that creates the functions and save them to a file\. You use this in subsequent steps to recreate the functions\. 

   ```
   ./pg_restore -l /tmp/test.dmp | grep FUNCTION > /tmp/function_list/
   ```

   For more information on pg\_restore, see [pg\_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) in the PostgreSQL documentation\. 

1. Drop the functions and extensions\. The following example drops any PLV8 based objects\. The cascade option ensures that any dependent are dropped\.

   ```
   DROP EXTENSION pvl8 CASCADE;
   ```

   If your PostgreSQL instance contains objects based on plcoffee or plls, repeat this step for those extensions\.

1. Create the extensions\. The following example creates the plv8, plcoffee, and plls extensions\.

   ```
   CREATE EXTENSION plv8;
   CREATE EXTENSION plcoffee;
   CREATE EXTENSION plls;
   ```

1. Create the functions using the dump file and "driver" file\.

   The following example recreates the functions that you extracted previously\.

   ```
   ./pg_restore -U master -d postgres -Fc -L /tmp/function_list /tmp/test.dmp
   ```

1. Verify that all your functions have been recreated by using the following query\. 

   ```
   SELECT * FROM pg_available_extensions WHERE name IN ('plv8','plls','plcoffee'); 
   ```

   The PLV8 version 2 adds the following extra row to your result set:

   ```
       proname    |  nspname   | lanname
   ---------------+------------+----------
    plv8_version  | pg_catalog | plv8
   ```