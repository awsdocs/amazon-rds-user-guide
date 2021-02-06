# Working with PostGIS<a name="Appendix.PostgreSQL.CommonDBATasks.PostGIS"></a>

PostGIS is an extension to PostgreSQL for storing and managing spatial information\. If you are not familiar with PostGIS, see [PostGIS\.net](https://postgis.net/)\.

You need to perform a bit of setup before you can use the PostGIS extension\. The following list shows what you need to do; each step is described in greater detail later in this section\.
+ Connect to the DB instance using the master user name used to create the DB instance\.
+ Load the PostGIS extensions\.
+ Transfer ownership of the extensions to the `rds_superuser` role\.
+ Transfer ownership of the objects to the `rds_superuser` role\.
+ Test the extensions\.

## Step 1: Connect to the DB instance using the master user name used to create the DB instance<a name="Appendix.PostgreSQL.CommonDBATasks.PostGIS.Connect"></a>

First, you connect to the DB instance using the master user name that was used to create the DB instance\. That name is automatically assigned the `rds_superuser` role\. You need the `rds_superuser` role that is needed to do the remaining steps\.

The following example uses `SELECT` to show you the current user\. In this case, the current user should be the master user name you chose when creating the DB instance\.

```
SELECT CURRENT_USER;
 current_user
 -------------
  myawsuser
 (1 row)
```

## Step 2: Load the PostGIS extensions<a name="Appendix.PostgreSQL.CommonDBATasks.PostGIS.LoadExtensions"></a>

Use `CREATE EXTENSION` statements to load the PostGIS extensions\. You must also load the `` extension\. You can then use the `\dn` command to list the owners of the PostGIS schemas\.

```
CREATE EXTENSION postgis;
CREATE EXTENSION fuzzystrmatch;
CREATE EXTENSION postgis_tiger_geocoder;
CREATE EXTENSION postgis_topology;
\dn
     List of schemas
     Name     |   Owner
--------------+-----------
 public       | myawsuser
 tiger        | rdsadmin
 tiger_data   | rdsadmin
 topology     | rdsadmin
(4 rows)
```

## Step 3: Transfer ownership of the extensions to the rds\_superuser role<a name="Appendix.PostgreSQL.CommonDBATasks.PostGIS.TransferOwnership"></a>

Use the ALTER SCHEMA statements to transfer ownership of the schemas to the `rds_superuser` role\.

```
ALTER SCHEMA tiger OWNER TO rds_superuser;
ALTER SCHEMA tiger_data OWNER TO rds_superuser;
ALTER SCHEMA topology OWNER TO rds_superuser;
\dn
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

Next, run this query to run the exec function that in turn runs the statements and alters the permissions\.

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