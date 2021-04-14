# Working with the PostGIS extension<a name="Appendix.PostgreSQL.CommonDBATasks.PostGIS"></a>

PostGIS is an extension to PostgreSQL for storing and managing spatial information\. If you are not familiar with PostGIS, see [PostGIS\.net](https://postgis.net/)\. 

You need to perform some setup before you can use the PostGIS extension\. The following list shows what you need to do\. Each step is described in greater detail later in this section\.

**Topics**
+ [Step 1: Connect to the DB instance using the user name used to create the DB instance](#Appendix.PostgreSQL.CommonDBATasks.PostGIS.Connect)
+ [Step 2: Load the PostGIS extensions](#Appendix.PostgreSQL.CommonDBATasks.PostGIS.LoadExtensions)
+ [Step 3: Transfer ownership of the extensions to the rds\_superuser role](#Appendix.PostgreSQL.CommonDBATasks.PostGIS.TransferOwnership)
+ [Step 4: Transfer ownership of the objects to the rds\_superuser role](#Appendix.PostgreSQL.CommonDBATasks.PostGIS.TransferObjects)
+ [Step 5: Test the extensions](#Appendix.PostgreSQL.CommonDBATasks.PostGIS.Test)
+ [PostGIS extension components](#CHAP_PostgreSQL.Extensions.PostGIS)

## Step 1: Connect to the DB instance using the user name used to create the DB instance<a name="Appendix.PostgreSQL.CommonDBATasks.PostGIS.Connect"></a>

First, you connect to the DB instance using the user name that was used to create the DB instance\. That name is automatically assigned the `rds_superuser` role\. You need the `rds_superuser` role that is needed to do the remaining steps\.

The following example uses `SELECT` to show you the current user\. In this case, the current user should be the user name you chose when creating the DB instance\.

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

## PostGIS extension component versions<a name="CHAP_PostgreSQL.Extensions.PostGIS"></a>

The following table shows the PostGIS component versions that ship with the RDS for PostgreSQL versions\.


| PostgreSQL | PostGIS | GEOS | GDAL | PROJ | 
| --- | --- | --- | --- | --- | 
| 11\.1 | 2\.5\.1 r17027 | 3\.7\.0\-CAPI\-1\.11\.0 673b9939 | 2\.3\.1, released 2018/06/22 | Rel\. 5\.2\.0, September 15th, 2018 | 
| 10\.6 | 2\.4\.4 r16526 | 3\.6\.2\-CAPI\-1\.10\.2 4d2925d6 | 2\.1\.4, released 2017/06/23 | Rel\. 4\.9\.3, September 15th, 2016  | 
| 10\.5  | 2\.4\.4 r16526 | 3\.6\.2\-CAPI\-1\.10\.2 4d2925d6 | 2\.1\.4, released 2017/06/23 | Rel\. 4\.9\.3, September 15th, 2016  | 
| 10\.4 | 2\.4\.4 r16526 | 3\.6\.2\-CAPI\-1\.10\.2 4d2925d6 | 2\.1\.4, released 2017/06/23 | Rel\. 4\.9\.3, September 15th, 2016 | 
| 10\.3 | 2\.4\.2 | 3\.6\.2\-CAPI\-1\.10\.2 4d2925d6 | 2\.1\.3, released 2017/20/01  | Rel\. 4\.9\.3, September 15th, 2016 | 
| 10\.1 | 2\.4\.2 | 3\.6\.2\-CAPI\-1\.10\.2 4d2925d6 | 2\.1\.3, released 2017/20/01  | Rel\. 4\.9\.3, September 15th, 2016 | 
| 9\.6\.11 | 2\.3\.7 r16523 | 3\.6\.2\-CAPI\-1\.10\.2 4d2925d6 | 2\.1\.4, released 2017/06/23 | Rel\. 4\.9\.3, September 15th, 2016 | 
| 9\.6\.10 | 2\.3\.7 r16523 | 3\.6\.2\-CAPI\-1\.10\.2 4d2925d6 | 2\.1\.4, released 2017/06/23 | Rel\. 4\.9\.3, September 15th, 2016 | 
| 9\.6\.9 | 2\.3\.7 r16523 | 3\.6\.2\-CAPI\-1\.10\.2 4d2925d6 | 2\.1\.4, released 2017/06/23 | Rel\. 4\.9\.3, September 15th, 2016 | 
| 9\.6\.8 | 2\.3\.4 r16009 | 3\.6\.2\-CAPI\-1\.10\.2 4d2925d6 | 2\.1\.3, released 2017/20/01 | Rel\. 4\.9\.3, September 15th, 2016 | 
| 9\.6\.6 | 2\.3\.4 r16009 | 3\.6\.2\-CAPI\-1\.10\.2 4d2925d6 | 2\.1\.3, released 2017/20/01 | Rel\. 4\.9\.3, September 15th, 2016 | 
| 9\.6\.3 | 2\.3\.2 r15302 | 3\.5\.1\-CAPI\-1\.9\.1 r4246 | 2\.1\.3, released 2017/20/01 | Rel\. 4\.9\.3, September 15th, 2016 | 
| 9\.6\.2 | 2\.3\.2 r15302 | 3\.5\.1\-CAPI\-1\.9\.1 r4246 | 2\.1\.3, released 2017/20/01 | Rel\. 4\.9\.3, September 15th, 2016 | 
| 9\.6\.1 | 2\.3\.0 r15146 | 3\.5\.0\-CAPI\-1\.9\.0 r4084 | 2\.1\.1, released 2016/07/07 | Rel\. 4\.9\.2, September 8th, 2016 | 
| 9\.5\.7 | 2\.2\.5 r15298 | 3\.5\.1\-CAPI\-1\.9\.1 r4246 | 2\.0\.3, released 2016/07/01 | Rel\. 4\.9\.3, September 15th, 2016 | 
| 9\.5\.6 | 2\.2\.5 r15298 | 3\.5\.1\-CAPI\-1\.9\.1 r4246 | 2\.0\.3, released 2016/07/01 | Rel\. 4\.9\.3, September 15th, 2016 | 
| 9\.5\.4 | 2\.2\.2 r14797 | 3\.5\.0\-CAPI\-1\.9\.0 r4084 | 2\.0\.3, released 2016/07/01 | Rel\. 4\.9\.2, September 8th, 2015 | 
| 9\.5\.2 | 2\.2\.2 r14797 | 3\.5\.0\-CAPI\-1\.9\.0 r4084 | 2\.0\.2, released 2016/01/26 | Rel\. 4\.9\.2, September 8th, 2015 | 

**Note**  
PostgreSQL 10\.5 added support for the `libprotobuf` extension version 1\.3\.0 to the PostGIS component\. 