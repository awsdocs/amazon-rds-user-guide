# Managing spatial data with the PostGIS extension<a name="Appendix.PostgreSQL.CommonDBATasks.PostGIS"></a>

PostGIS is an extension to PostgreSQL for storing and managing spatial information\. To learn more about PostGIS, see [PostGIS\.net](https://postgis.net/)\. 

Starting with version 10\.5, PostgreSQL supports the libprotobuf 1\.3\.0 library used by PostGIS for working with map box vector tile data\.

Before you can use the PostGIS extension, you need to perform some setup\. The following list shows what you need to do\. Each step is described in greater detail later in this section\.

**Topics**
+ [Step 1: Connect to the DB instance using the user name used to create the DB instance](#Appendix.PostgreSQL.CommonDBATasks.PostGIS.Connect)
+ [Step 2: Load the PostGIS extensions](#Appendix.PostgreSQL.CommonDBATasks.PostGIS.LoadExtensions)
+ [Step 3: Transfer ownership of the extensions to the rds\_superuser role](#Appendix.PostgreSQL.CommonDBATasks.PostGIS.TransferOwnership)
+ [Step 4: Transfer ownership of the objects to the rds\_superuser role](#Appendix.PostgreSQL.CommonDBATasks.PostGIS.TransferObjects)
+ [Step 5: Test the extensions](#Appendix.PostgreSQL.CommonDBATasks.PostGIS.Test)
+ [Step 6: Update the PostGIS extension](#Appendix.PostgreSQL.CommonDBATasks.PostGIS.Update)
+ [PostGIS extension versions](#CHAP_PostgreSQL.Extensions.PostGIS)

## Step 1: Connect to the DB instance using the user name used to create the DB instance<a name="Appendix.PostgreSQL.CommonDBATasks.PostGIS.Connect"></a>

First, you connect to the DB instance using the user name that was used to create the DB instance\. That name is automatically assigned the `rds_superuser` role\. You need the `rds_superuser` role to do the remaining steps\.

The following example uses `SELECT` to show you the current user\. In this case, the current user should be the user name you chose when creating the DB instance\.

```
SELECT CURRENT_USER;

 current_user
 -------------
  myawsuser
 (1 row)
```

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
 public       | myawsuser
 tiger        | rdsadmin
 tiger_data   | rdsadmin
 topology     | rdsadmin
(4 rows)
```

If you use the psql command line, you can get the same information by running the `\dn` metacommand\. 

**Note**  
Extra extensions aren't required for some use cases\.

## Step 3: Transfer ownership of the extensions to the rds\_superuser role<a name="Appendix.PostgreSQL.CommonDBATasks.PostGIS.TransferOwnership"></a>

Use the ALTER SCHEMA statements to transfer ownership of the schemas to the `rds_superuser` role\.

```
ALTER SCHEMA tiger OWNER TO rds_superuser;
ALTER SCHEMA
ALTER SCHEMA tiger_data OWNER TO rds_superuser; 
ALTER SCHEMA
ALTER SCHEMA topology OWNER TO rds_superuser;
ALTER SCHEMA
```

If you want to confirm the ownership change, you can run the following SQL query\. Or you can use the `\dn` metacommand from the psql command line\. 

```
SELECT n.nspname AS "Name",
  pg_catalog.pg_get_userbyid(n.nspowner) AS "Owner"
  FROM pg_catalog.pg_namespace n
  WHERE n.nspname !~ '^pg_' AND n.nspname <> 'information_schema'
  ORDER BY 1;

       List of schemas
     Name     |     Owner
--------------+---------------
 public       | myawsuser
 tiger        | rds_superuser
 tiger_data   | rds_superuser
 topology     | rds_superuser
(4 rows)
```

## Step 4: Transfer ownership of the objects to the rds\_superuser role<a name="Appendix.PostgreSQL.CommonDBATasks.PostGIS.TransferObjects"></a>

Use the following function to transfer ownership of the PostGIS objects to the `rds_superuser` role\. Run the following statement from the psql prompt to create the function\.

```
CREATE FUNCTION exec(text) returns text language plpgsql volatile AS $f$ BEGIN EXECUTE $1; RETURN $1; END; $f$;
```

Next, run the following query to run the `exec` function that in turn runs the statements and alters the permissions\.

```
SELECT exec('ALTER TABLE ' || quote_ident(s.nspname) || '.' || quote_ident(s.relname) || ' OWNER TO rds_superuser;')
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
SELECT * from pg_available_extension_versions where name='postgis';
```

You can also find version information in the following sections in the *Amazon RDS for PostgreSQL Release Notes*:
+ [ PostgreSQL version 13 extensions supported on Amazon RDS](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-extensions.html#postgresql-extensions-13x)
+ [ PostgreSQL version 12 extensions supported on Amazon RDS](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-extensions.html#postgresql-extensions-12x)
+ [ PostgreSQL version 11\.x extensions supported on Amazon RDS](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-extensions.html#postgresql-extensions-11x)
+ [ PostgreSQL version 10\.x extensions supported on Amazon RDS](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-extensions.html#postgresql-extensions-101x)
+ [ PostgreSQL version 9\.6\.x extensions supported on Amazon RDS](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-extensions.html#postgresql-extensions-96x)