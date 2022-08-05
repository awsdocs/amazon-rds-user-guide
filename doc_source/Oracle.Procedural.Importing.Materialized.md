# Migrating with Oracle materialized views<a name="Oracle.Procedural.Importing.Materialized"></a>

To migrate large datasets efficiently, you can use Oracle materialized view replication\. With replication, you can keep the target tables synchronized with the source tables\. Thus, you can switch over to Amazon RDS later, if needed\. 

Before you can migrate using materialized views, make sure that you meet the following requirements:
+ Configure access from the target database to the source database\. In the following example, access rules were enabled on the source database to allow the RDS for Oracle target database to connect to the source over SQL\*Net\. 
+ Create a database link from the RDS for Oracle DB instance to the source database\.

**To migrate data using materialized views**

1. Create a user account on both source and RDS for Oracle target instances that can authenticate with the same password\. The following example creates a user named `dblink_user`\.

   ```
   CREATE USER dblink_user IDENTIFIED BY my-password
     DEFAULT TABLESPACE users
     TEMPORARY TABLESPACE temp;
      
   GRANT CREATE SESSION TO dblink_user;
   
   GRANT SELECT ANY TABLE TO dblink_user;
   
   GRANT SELECT ANY DICTIONARY TO dblink_user;
   ```

1. Create a database link from the RDS for Oracle target instance to the source instance using your newly created user\.

   ```
   CREATE DATABASE LINK remote_site
     CONNECT TO dblink_user IDENTIFIED BY my-password
     USING '(description=(address=(protocol=tcp) (host=my-host) 
       (port=my-listener-port)) (connect_data=(sid=my-source-db-sid)))';
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

1. On the target RDS for Oracle DB instance, create a materialized view\. 

   ```
   CREATE MATERIALIZED VIEW customer_0 
     BUILD IMMEDIATE REFRESH FAST 
     AS (SELECT * 
         FROM   cust_dba.customer_0@remote_site);
   ```

1. On the target RDS for Oracle DB instance, refresh the materialized view\.

   ```
   EXEC DBMS_MV.REFRESH('CUSTOMER_0', 'f');
   ```

1. Drop the materialized view and include the `PRESERVE TABLE` clause to retain the materialized view container table and its contents\.

   ```
   DROP MATERIALIZED VIEW customer_0 PRESERVE TABLE;
   ```

   The retained table has the same name as the dropped materialized view\.