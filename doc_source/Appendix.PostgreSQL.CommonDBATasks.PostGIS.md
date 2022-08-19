# Managing spatial data with the PostGIS extension<a name="Appendix.PostgreSQL.CommonDBATasks.PostGIS"></a>

PostGIS is an extension to PostgreSQL for storing and managing spatial information\. To learn more about PostGIS, see [PostGIS\.net](https://postgis.net/)\. 

Starting with version 10\.5, PostgreSQL supports the libprotobuf 1\.3\.0 library used by PostGIS for working with map box vector tile data\.

Setting up the PostGIS extension requires `rds_superuser` privileges\. We recommend that you create a user \(role\) to manage the PostGIS extension and your spatial data\. The PostGIS extension and its related components add thousands of functions to PostgreSQL\. Consider creating the PostGIS extension in its own schema if that makes sense for your use case\. The following example shows how to install the extension in its own database, but this isn't required\.

**Topics**
+ [Step 1: Create a user \(role\) to manage the PostGIS extension](#Appendix.PostgreSQL.CommonDBATasks.PostGIS.Connect)
+ [Step 2: Load the PostGIS extensions](#Appendix.PostgreSQL.CommonDBATasks.PostGIS.LoadExtensions)
+ [Step 3: Transfer ownership of the extensions](#Appendix.PostgreSQL.CommonDBATasks.PostGIS.TransferOwnership)
+ [Step 4: Transfer ownership of the PostGIS objects](#Appendix.PostgreSQL.CommonDBATasks.PostGIS.TransferObjects)
+ [Step 5: Test the extensions](#Appendix.PostgreSQL.CommonDBATasks.PostGIS.Test)
+ [Step 6: Upgrade the PostGIS extension](#Appendix.PostgreSQL.CommonDBATasks.PostGIS.Update)
+ [PostGIS extension versions](#CHAP_PostgreSQL.Extensions.PostGIS)
+ [Upgrading PostGIS 2 to PostGIS 3](#PostgreSQL.Extensions.PostGIS.versions.upgrading.2-to-3)

## Step 1: Create a user \(role\) to manage the PostGIS extension<a name="Appendix.PostgreSQL.CommonDBATasks.PostGIS.Connect"></a>

First, connect to your RDS for PostgreSQL DB instance as a user that has `rds_superuser` privileges\. If you kept the default name when you set up your instance, you connect as `postgres`\. 

```
psql --host=111122223333.aws-region.rds.amazonaws.com --port=5432 --username=postgres --password
```

Create a separate role \(user\) to administer the PostGIS extension\.

```
postgres=>  CREATE ROLE gis_admin LOGIN PASSWORD 'change_me';
CREATE ROLE
```

Grant this role `rds_superuser` privileges, to allow the role to install the extension\.

```
postgres=> GRANT rds_superuser TO gis_admin;
GRANT
```

Create a database to use for your PostGIS artifacts\. This step is optional\. Or you can create a schema in your user database for the PostGIS extensions, but this also isn't required\.

```
postgres=> CREATE DATABASE lab_gis;
CREATE DATABASE
```

Give the `gis_admin` all privileges on the `lab_gis` database\.

```
postgres=> GRANT ALL PRIVILEGES ON DATABASE lab_gis TO gis_admin;
GRANT
```

Exit the session and reconnect to your RDS for PostgreSQL DB instance as `gis_admin`\.

```
postgres=> --host=111122223333.aws-region.rds.amazonaws.com --port=5432 --username=gis_admin --password --dbname=lab_gis
Password for user gis_admin:...
lab_gis=>
```

Continue setting up the extension as detailed in the next steps\.

## Step 2: Load the PostGIS extensions<a name="Appendix.PostgreSQL.CommonDBATasks.PostGIS.LoadExtensions"></a>

The PostGIS extension includes several related extensions that work together to provide geospatial functionality\. Depending on your use case, you might not need all the extensions created in this step\. 

Use `CREATE EXTENSION` statements to load the PostGIS extensions\. 

```
CREATE EXTENSION postgis;
CREATE EXTENSION
CREATE EXTENSION postgis_raster;
CREATE EXTENSION
CREATE EXTENSION postgis_tiger_geocoder;
CREATE EXTENSION
CREATE EXTENSION postgis_topology;
CREATE EXTENSION
CREATE EXTENSION fuzzystrmatch;
CREATE EXTENSION
CREATE EXTENSION address_standardizer_data_us;
CREATE EXTENSION
```

You can verify the results by running the SQL query shown in the following example, which lists the extensions and their owners\. 

```
SELECT n.nspname AS "Name",
  pg_catalog.pg_get_userbyid(n.nspowner) AS "Owner"
  FROM pg_catalog.pg_namespace n
  WHERE n.nspname !~ '^pg_' AND n.nspname <> 'information_schema'
  ORDER BY 1;
List of schemas
     Name     |   Owner
--------------+-----------
 public       | postgres
 tiger        | rdsadmin
 tiger_data   | rdsadmin
 topology     | rdsadmin
(4 rows)
```

## Step 3: Transfer ownership of the extensions<a name="Appendix.PostgreSQL.CommonDBATasks.PostGIS.TransferOwnership"></a>

Use the ALTER SCHEMA statements to transfer ownership of the schemas to the `gis_admin` role\.

```
ALTER SCHEMA tiger OWNER TO gis_admin;
ALTER SCHEMA
ALTER SCHEMA tiger_data OWNER TO gis_admin; 
ALTER SCHEMA
ALTER SCHEMA topology OWNER TO gis_admin;
ALTER SCHEMA
```

You can confirm the ownership change by running the following SQL query\. Or you can use the `\dn` metacommand from the psql command line\. 

```
SELECT n.nspname AS "Name",
  pg_catalog.pg_get_userbyid(n.nspowner) AS "Owner"
  FROM pg_catalog.pg_namespace n
  WHERE n.nspname !~ '^pg_' AND n.nspname <> 'information_schema'
  ORDER BY 1;

       List of schemas
     Name     |     Owner
--------------+---------------
 public       | postgres
 tiger        | gis_admin
 tiger_data   | gis_admin
 topology     | gis_admin
(4 rows)
```

## Step 4: Transfer ownership of the PostGIS objects<a name="Appendix.PostgreSQL.CommonDBATasks.PostGIS.TransferObjects"></a>

Use the following function to transfer ownership of the PostGIS objects to the `gis_admin` role\. Run the following statement from the psql prompt to create the function\.

```
CREATE FUNCTION exec(text) returns text language plpgsql volatile AS $f$ BEGIN EXECUTE $1; RETURN $1; END; $f$;
CREATE FUNCTION
```

Next, run the following query to run the `exec` function that in turn runs the statements and alters the permissions\.

```
SELECT exec('ALTER TABLE ' || quote_ident(s.nspname) || '.' || quote_ident(s.relname) || ' OWNER TO gis_admin;')
  FROM (
    SELECT nspname, relname
    FROM pg_class c JOIN pg_namespace n ON (c.relnamespace = n.oid) 
    WHERE nspname in ('tiger','topology') AND
    relkind IN ('r','S','v') ORDER BY relkind = 'S')
s;
```

## Step 5: Test the extensions<a name="Appendix.PostgreSQL.CommonDBATasks.PostGIS.Test"></a>

To avoid needing to specify the schema name, add the `tiger` schema to your search path using the following command\.

```
SET search_path=public,tiger;
SET
```

Test the `tiger` schema by using the following SELECT statement\.

```
SELECT address, streetname, streettypeabbrev, zip
 FROM normalize_address('1 Devonshire Place, Boston, MA 02109') AS na;
address | streetname | streettypeabbrev |  zip
---------+------------+------------------+-------
       1 | Devonshire | Pl               | 02109
(1 row)
```

To learn more about this extension, see [Tiger Geocoder](https://postgis.net/docs/Extras.html#Tiger_Geocoder) in the PostGIS documentation\. 

Test access to the `topology` schema by using the following `SELECT` statement\. This calls the `createtopology` function to register a new topology object \(my\_new\_topo\) with the specified spatial reference identifier \(26986\) and default tolerance \(0\.5\)\. To learn more, see [CreateTopology](https://postgis.net/docs/CreateTopology.html) in the PostGIS documentation\. 

```
SELECT topology.createtopology('my_new_topo',26986,0.5);
 createtopology
----------------
              1
(1 row)
```

## Step 6: Upgrade the PostGIS extension<a name="Appendix.PostgreSQL.CommonDBATasks.PostGIS.Update"></a>

Each new release of PostgreSQL supports one or more versions of the PostGIS extension compatible with that release\. Upgrading the PostgreSQL engine to a new version doesn't automatically upgrade the PostGIS extension\. Before upgrading the PostgreSQL engine, you typically upgrade PostGIS to the newest available version for the current PostgreSQL version\. For details, see [PostGIS extension versions](#CHAP_PostgreSQL.Extensions.PostGIS)\. 

After the PostgreSQL engine upgrade, you then upgrade the PostGIS extension again, to the version supported for the newly upgraded PostgreSQL engine version\. For more information about upgrading the PostgreSQL engine, see [How to perform a major version upgrade](USER_UpgradeDBInstance.PostgreSQL.md#USER_UpgradeDBInstance.PostgreSQL.MajorVersion.Process)\. 

You can check for available PostGIS extension version updates on your RDS for PostgreSQL DB instance at any time\. To do so, run the following command\. This function is available with PostGIS 2\.5\.0 and higher versions\.

```
SELECT postGIS_extensions_upgrade();
```

If your application doesn't support the latest PostGIS version, you can install an older version of PostGIS that's available in your major version as follows\.

```
CREATE EXTENSION postgis VERSION "2.5.5";
```

If you want to upgrade to a specific PostGIS version from an older version, you can also use the following command\.

```
ALTER EXTENSION postgis UPDATE TO "2.5.5";
```

Depending on the version that you're upgrading from, you might need to use this function again\. The result of the first run of the function determines if an additional upgrade function is needed\. For example, this is the case for upgrading from PostGIS 2 to PostGIS 3\. For more information, see [Upgrading PostGIS 2 to PostGIS 3](#PostgreSQL.Extensions.PostGIS.versions.upgrading.2-to-3)\.

If you upgraded this extension to prepare for a major version upgrade of the PostgreSQL engine, you can continue with other preliminary tasks\. For more information, see [How to perform a major version upgrade](USER_UpgradeDBInstance.PostgreSQL.md#USER_UpgradeDBInstance.PostgreSQL.MajorVersion.Process)\. 

## PostGIS extension versions<a name="CHAP_PostgreSQL.Extensions.PostGIS"></a>

We recommend that you install the versions of all extensions such as PostGIS as listed in [Extension versions for Amazon RDS for PostgreSQL](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-extensions.html) in the *Amazon RDS for PostgreSQL Release Notes\.* To get a list of versions that are available in your release, use the following command\.

```
SELECT * FROM pg_available_extension_versions WHERE name='postgis';
```

You can find version information in the following sections in the *Amazon RDS for PostgreSQL Release Notes*:
+ [ PostgreSQL version 14 extensions supported on Amazon RDS](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-extensions.html#postgresql-extensions-14x)
+ [ PostgreSQL version 13 extensions supported on Amazon RDS](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-extensions.html#postgresql-extensions-13x)
+ [ PostgreSQL version 12 extensions supported on Amazon RDS](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-extensions.html#postgresql-extensions-12x)
+ [ PostgreSQL version 11 extensions supported on Amazon RDS](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-extensions.html#postgresql-extensions-11x)
+ [ PostgreSQL version 10 extensions supported on Amazon RDS](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-extensions.html#postgresql-extensions-101x)
+ [ PostgreSQL version 9\.6\.x extensions supported on Amazon RDS](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-extensions.html#postgresql-extensions-96x)

## Upgrading PostGIS 2 to PostGIS 3<a name="PostgreSQL.Extensions.PostGIS.versions.upgrading.2-to-3"></a>

Starting with version 3\.0, the PostGIS raster functionality is now a separate extension, `postgis_raster`\. This extension has its own installation and upgrade path\. This removes dozens of functions, data types, and other artifacts required for raster image processing from the core `postgis` extension\. That means that if your use case doesn't require raster processing, you don't need to install the `postgis_raster` extension\.

In the following upgrade example, the first upgrade command extracts raster functionality into the `postgis_raster` extension\. A second upgrade command is then required to upgrade `postgres_raster` to the new version\.

**To upgrade from PostGIS 2 to PostGIS 3**

1. Identify the default version of PostGIS that's available to the PostgreSQL version on your RDS for PostgreSQL DB instance\. To do so, run the following query\.

   ```
   SELECT * FROM pg_available_extensions
       WHERE default_version > installed_version;
     name   | default_version | installed_version |                          comment
   ---------+-----------------+-------------------+------------------------------------------------------------
    postgis | 3.1.4           | 2.3.7             | PostGIS geometry and geography spatial types and functions
   (1 row)
   ```

1. Identify the versions of PostGIS installed in each database on your RDS for PostgreSQL DB instance\. In other words, query each user database as follows\.

   ```
   SELECT
       e.extname AS "Name",
       e.extversion AS "Version",
       n.nspname AS "Schema",
       c.description AS "Description"
   FROM
       pg_catalog.pg_extension e
       LEFT JOIN pg_catalog.pg_namespace n ON n.oid = e.extnamespace
       LEFT JOIN pg_catalog.pg_description c ON c.objoid = e.oid
           AND c.classoid = 'pg_catalog.pg_extension'::pg_catalog.regclass
   WHERE
       e.extname LIKE '%postgis%'
   ORDER BY
       1;
     Name   | Version | Schema |                             Description
   ---------+---------+--------+---------------------------------------------------------------------
    postgis | 2.3.7   | public | PostGIS geometry, geography, and raster spatial types and functions
   (1 row)
   ```

   This mismatch between the default version \(PostGIS 3\.1\.4\) and the installed version \(PostGIS 2\.3\.7\) means that you need to upgrade the PostGIS extension\.

   ```
   ALTER EXTENSION postgis UPDATE;
   ALTER EXTENSION
   WARNING: unpackaging raster
   WARNING: PostGIS Raster functionality has been unpackaged
   ```

1. Run the following query to verify that the raster functionality is now in its own package\.

   ```
   SELECT
       probin,
       count(*)
   FROM
       pg_proc
   WHERE
       probin LIKE '%postgis%'
   GROUP BY
       probin;
             probin          | count
   --------------------------+-------
    $libdir/rtpostgis-2.3    | 107
    $libdir/postgis-3        | 487
   (2 rows)
   ```

   The output shows that there's still a difference between versions\. The PostGIS functions are version 3 \(postgis\-3\), while the raster functions \(rtpostgis\) are version 2 \(rtpostgis\-2\.3\)\. To complete the upgrade, you run the upgrade command again, as follows\.

   ```
   postgres=> SELECT postgis_extensions_upgrade();
   ```

   You can safely ignore the warning messages\. Run the following query again to verify that the upgrade is complete\. The upgrade is complete when PostGIS and all related extensions aren't marked as needing upgrade\. 

   ```
   SELECT postgis_full_version();
   ```

1. Use the following query to see the completed upgrade process and the separately packaged extensions, and verify that their versions match\. 

   ```
   SELECT
       e.extname AS "Name",
       e.extversion AS "Version",
       n.nspname AS "Schema",
       c.description AS "Description"
   FROM
       pg_catalog.pg_extension e
       LEFT JOIN pg_catalog.pg_namespace n ON n.oid = e.extnamespace
       LEFT JOIN pg_catalog.pg_description c ON c.objoid = e.oid
           AND c.classoid = 'pg_catalog.pg_extension'::pg_catalog.regclass
   WHERE
       e.extname LIKE '%postgis%'
   ORDER BY
       1;
         Name      | Version | Schema |                             Description
   ----------------+---------+--------+---------------------------------------------------------------------
    postgis        | 3.1.5   | public | PostGIS geometry, geography, and raster spatial types and functions
    postgis_raster | 3.1.5   | public | PostGIS raster types and functions
   (2 rows)
   ```

   The output shows that the PostGIS 2 extension was upgraded to PostGIS 3, and both `postgis` and the now separate `postgis_raster` extension are version 3\.1\.5\.

After this upgrade completes, if you don't plan to use the raster functionality, you can drop the extension as follows\.

```
DROP EXTENSION postgis_raster;
```