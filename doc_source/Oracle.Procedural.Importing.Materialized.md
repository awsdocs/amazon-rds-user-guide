# Oracle materialized views<a name="Oracle.Procedural.Importing.Materialized"></a>

You can also make use of Oracle materialized view replication to migrate large datasets efficiently\. Replication allows you to keep the target tables in sync with the source on an ongoing basis, so the actual cutover to Amazon RDS can be done later, if needed\. The replication is set up using a database link from the Amazon RDS instance to the source database\. 

One requirement for materialized views is to allow access from the target database to the source database\. In the following example, access rules were enabled on the source database to allow the Amazon RDS target database to connect to the source over SQLNet\. 

1. Create a user account on both source and Amazon RDS target instances that can authenticate with the same password\. 

   ```
   CREATE USER dblink_user IDENTIFIED BY <password>
     DEFAULT TABLESPACE users
     TEMPORARY TABLESPACE temp;
      
   GRANT CREATE SESSION TO dblink_user;
   
   GRANT SELECT ANY TABLE TO dblink_user;
   
   GRANT SELECT ANY DICTIONARY TO dblink_user;
   ```

1. Create a database link from the Amazon RDS target instance to the source instance using the newly created dblink\_user\. 

   ```
   CREATE DATABASE LINK remote_site
     CONNECT TO dblink_user IDENTIFIED BY <password>
     USING '(description=(address=(protocol=tcp) (host=<myhost>) 
       (port=<listener port>)) (connect_data=(sid=<sourcedb sid>)))';
   ```

1. Test the link:

   ```
   SELECT * FROM V$INSTANCE@remote_site; 
   ```

1. Create a sample table with primary key and materialized view log on the source instance\. 

   ```
   CREATE TABLE customer_0 TABLESPACE users 
     AS (SELECT ROWNUM id, o.* 
         FROM   ALL_OBJECTS o, ALL_OBJECTS x
         WHERE  ROWNUM <= 1000000);
   
   ALTER TABLE customer_0 ADD CONSTRAINT pk_customer_0 PRIMARY KEY (id) USING INDEX;
   
   CREATE MATERIALIZED VIEW LOG ON customer_0;
   ```

1. On the target Amazon RDS instance, create a materialized view\. 

   ```
   CREATE MATERIALIZED VIEW customer_0 
     BUILD IMMEDIATE REFRESH FAST 
     AS (SELECT * 
         FROM   cust_dba.customer_0@remote_site);
   ```

1. On the target Amazon RDS instance, refresh the materialized view\.

   ```
   EXEC DBMS_MV.REFRESH('CUSTOMER_0', 'f');            
   ```

1. Drop the materialized view and include the PRESERVE TABLE clause to retain the materialized view container table and its contents\.

   ```
   DROP MATERIALIZED VIEW customer_0 PRESERVE TABLE;            
   ```

   The retained table has the same name as the dropped materialized view\.