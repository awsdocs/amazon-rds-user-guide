# Managing spatial data with the PostGIS extension<a name="Appendix.PostgreSQL.CommonDBATasks.PostGIS"></a>

PostGIS is an extension to PostgreSQL for storing and managing spatial information\. To learn more about PostGIS, see [PostGIS\.net](https://postgis.net/)\. 

Starting with version 10\.5, PostgreSQL supports the libprotobuf 1\.3\.0 library used by PostGIS for working with map box vector tile data\.

Setting up the PostGIS extension requires `rds_superuser` privileges\. We recommend that you create a user \(role\) to manage the PostGIS extension and your spatial data\. The PostGIS extension and its related components add thousands of functions to PostgreSQL\. Consider creating the PostGIS extension in its own schema if that makes sense for your use case\. The following example shows how to install the extension in its own separate database, but this isn't required\.

**Topics**
+ [Step 1: Create a user \(role\) to manage the PostGIS extension](#Appendix.PostgreSQL.CommonDBATasks.PostGIS.Connect)
+ [Step 2: Load the PostGIS extensions](#Appendix.PostgreSQL.CommonDBATasks.PostGIS.LoadExtensions)
+ [Step 3: Transfer ownership of the extensions](#Appendix.PostgreSQL.CommonDBATasks.PostGIS.TransferOwnership)
+ [Step 4: Transfer ownership of the PostGIS objects](#Appendix.PostgreSQL.CommonDBATasks.PostGIS.TransferObjects)
+ [Step 5: Test the extensions](#Appendix.PostgreSQL.CommonDBATasks.PostGIS.Test)
+ [Step 6: Update the PostGIS extension](#Appendix.PostgreSQL.CommonDBATasks.PostGIS.Update)
+ [PostGIS extension versions](#CHAP_PostgreSQL.Extensions.PostGIS)

## Step 1: Create a user \(role\) to manage the PostGIS extension<a name="Appendix.PostgreSQL.CommonDBATasks.PostGIS.Connect"></a>

First, you connect to your RDS for PostgreSQL DB instance as a user that has `rds_superuser` privileges\. If you kept the default name when you set up your instance, you connect as `postgres`: 

```
psql=> psql --host=111122223333.aws-region.rds.amazonaws.com --port=5432 --username=postgres --password
```

Create a separate role \(user\) to administer the PostGIS extension:

```
postgres=>  CREATE ROLE gis_admin LOGIN PASSWORD 'change_me';
CREATE ROLE
```

Grant this role `rds_superuser` privileges, to allow the role to install the extension:

```
postgres=> GRANT rds_superuser TO gis_admin;
GRANT
```

Create a database to use for your PostGIS artifacts:

```
postgres=> CREATE DATABASE lab_gis;
CREATE DATABASE
```

Give the `gis_admin` all privileges on the `lab_gis` database\.

```
postgres=> GRANT ALL PRIVILEGES ON DATABASE lab_gis TO gis_admin;
GRANT
```

Exit the session and re\-connect to your RDS for PostgreSQL DB instance as `gis_admin`:

```
postgres=> --host=111122223333.aws-region.rds.amazonaws.com --port=5432 --username=gis_admin --password --dbname=lab_gis
Password for user gis_admin:...
lab_gis=>
```

Continue setting up the extension as detailed in the next steps\.

## Step 2: Load the PostGIS extensions<a name="Appendix.PostgreSQL.CommonDBATasks.PostGIS.LoadExtensions"></a>

Use `CREATE EXTENSION` statements to load the PostGIS extensions\. 

```
CREATE EXTENSION postgis;
CREATE EXTENSION
CREATE EXTENSION fuzzystrmatch;
CREATE EXTENSION
CREATE EXTENSION postgis_tiger_geocoder;
CREATE EXTENSION
CREATE EXTENSION postgis_topology;
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

**Note**  
Some use cases don't require all the extensions created in this step\.

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

Add `tiger` to your search path using the following command\.

```
SET search_path=public,tiger;
SET
```

Test `tiger` by using the following SELECT statement\.

```
SELECT na.address, na.streetname, na.streettypeabbrev, na.zip
 FROM normalize_address('1 Devonshire Place, Boston, MA 02109') AS na;
address | streetname | streettypeabbrev |  zip
---------+------------+------------------+-------
       1 | Devonshire | Pl               | 02109
(1 row)
```

Test `topology` by using the following SELECT statement\.

```
SELECT topology.createtopology('my_new_topo',26986,0.5);
 createtopology
----------------
              1
(1 row)
```

## Step 6: Update the PostGIS extension<a name="Appendix.PostgreSQL.CommonDBATasks.PostGIS.Update"></a>

Different versions of PostgreSQL support different versions of the PostGIS extension\. You can check to see if an upgrade is available by running the following command\. This function is available with PostGIS 2\.5\.0 and higher versions\.

```
SELECT PostGIS_Extensions_Upgrade();
```

Depending on the version that you're upgrading from, this function might need to run a second time\. The result of the first run of the function determines if an additional upgrade function is needed\. 

If your application doesn't support the latest PostGIS version, you can still create an older version of PostGIS that is available in your major version as follows\.

```
CREATE EXTENSION postgis VERSION "2.5.5"
```

If you want to upgrade to a specific PostGIS VERSION from an older version, you can also use the following command\.

```
ALTER EXTENSION postgis UPDATE TO "2.5.5"
```

## PostGIS extension versions<a name="CHAP_PostgreSQL.Extensions.PostGIS"></a>

You can list the versions that are available in your release by using the following command\.

```
SELECT * FROM pg_available_extension_versions WHERE name='postgis';
```

You can also find version information in the following sections in the *Amazon RDS for PostgreSQL Release Notes*:
+ [ PostgreSQL version 14 extensions supported on Amazon RDS](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-extensions.html#postgresql-extensions-14x)
+ [ PostgreSQL version 13 extensions supported on Amazon RDS](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-extensions.html#postgresql-extensions-13x)
+ [ PostgreSQL version 12 extensions supported on Amazon RDS](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-extensions.html#postgresql-extensions-12x)
+ [ PostgreSQL version 11 extensions supported on Amazon RDS](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-extensions.html#postgresql-extensions-11x)
+ [ PostgreSQL version 10 extensions supported on Amazon RDS](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-extensions.html#postgresql-extensions-101x)
+ [ PostgreSQL version 9\.6\.x extensions supported on Amazon RDS](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-extensions.html#postgresql-extensions-96x)