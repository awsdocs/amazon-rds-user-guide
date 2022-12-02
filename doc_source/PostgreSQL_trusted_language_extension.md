# Working with Trusted Language Extensions for PostgreSQL<a name="PostgreSQL_trusted_language_extension"></a>

Trusted Language Extensions for PostgreSQL is an open source development kit for building PostgreSQL extensions\. It allows you to build high performance PostgreSQL extensions and safely run them on your RDS for PostgreSQL DB instance\. By using Trusted Language Extensions \(TLE\) for PostgreSQL, you can create PostgreSQL extensions that follow the documented approach for extending PostgreSQL functionality\. For more information, see [Packaging Related Objects into an Extension](https://www.postgresql.org/docs/current/extend-extensions.html) in the PostgreSQL documentation\. 

One key benefit of TLE is that you can use it in environments that don't provide access to the file system underlying the PostgreSQL instance\. Previously, installing a new extension required access to the file system\. TLE removes this constraint\. It provides a development environment for creating new extensions for any PostgreSQL database, including those running on your RDS for PostgreSQL DB instances\.

TLE is designed to prevent access to unsafe resources for the extensions that you create using TLE\. Its runtime environment limits the impact of any extension defect to a single database connection\. TLE also gives database administrators fine\-grained control over who can install extensions, and it provides a permissions model for running them\.

TLE is supported on RDS for PostgreSQL version 14\.5 and higher versions\. The Trusted Language Extensions development environment and runtime are packaged as the `pg_tle` PostgreSQL extension, version 1\.0\.1\. It supports creating extensions in JavaScript, Perl, PL/pgSQL, and SQL\. You install the `pg_tle` extension in your RDS for PostgreSQL DB instance in the same way that you install other PostgreSQL extensions\. After the `pg_tle` is set up, developers can use it to create new PostgreSQL extensions, known as *TLE extensions*\.

 

In the following topics, you can find information about how to set up Trusted Language Extensions and how to get started creating your own TLE extensions\.

**Topics**
+ [Terminology](#PostgreSQL_trusted_language_extension-terminology)
+ [Requirements for using Trusted Language Extensions for PostgreSQL](#PostgreSQL_trusted_language_extension-requirements)
+ [Setting up Trusted Language Extensions in your RDS for PostgreSQL DB instance](#PostgreSQL_trusted_language_extension-setting-up)
+ [Overview of Trusted Language Extensions for PostgreSQL](#PostgreSQL_trusted_language_extension.overview)
+ [Creating TLE extensions for RDS for PostgreSQL](#PostgreSQL_trusted_language_extension-creating-TLE-extensions)
+ [Dropping your TLE extensions from a database](#PostgreSQL_trusted_language_extension-creating-TLE-extensions.dropping-TLEs)
+ [Uninstalling Trusted Language Extensions for PostgreSQL](#PostgreSQL_trusted_language_extension-uninstalling-pg_tle-devkit)
+ [Using PostgreSQL hooks with your TLE extensions](#PostgreSQL_trusted_language_extension.overview.tles-and-hooks)
+ [Functions reference for Trusted Language Extensions for PostgreSQL](PostgreSQL_trusted_language_extension-functions-reference.md)
+ [Hooks reference for Trusted Language Extensions for PostgreSQL](PostgreSQL_trusted_language_extension-hooks-reference.md)

## Terminology<a name="PostgreSQL_trusted_language_extension-terminology"></a>

To help you better understand Trusted Language Extensions, view the following glossary for terms used in this topic\. 

**Trusted Language Extensions for PostgreSQL**  
*Trusted Language Extensions for PostgreSQL* is the official name of the open source development kit that's packaged as the `pg_tle` extension\. It's available for use on any PostgreSQL system\. For more information, see [aws/pg\_tle](https://github.com/aws/pg_tle) on GitHub\.

**Trusted Language Extensions**  
*Trusted Language Extensions* is the short name for Trusted Language Extensions for PostgreSQL\. This shortened name and its abbreviation \(TLE\) are also used in this documentation\.

**trusted language**  
A *trusted language* is a programming or scripting language that has specific security attributes\. For example, trusted languages typically restrict access to the file system, and they limit use of specified networking properties\. The TLE development kit is designed to support trusted languages\. PostgreSQL supports several different languages that are used to create trusted or untrusted extensions\. For an example, see [Trusted and Untrusted PL/Perl](https://www.postgresql.org/docs/current/plperl-trusted.html) in the PostgreSQL documentation\. When you create an extension using Trusted Language Extensions, the extension inherently uses trusted language mechanisms\.

**TLE extension**  
A *TLE extension* is a PostgreSQL extension that's been created by using the Trusted Language Extensions \(TLE\) development kit\.    

## Requirements for using Trusted Language Extensions for PostgreSQL<a name="PostgreSQL_trusted_language_extension-requirements"></a>

Use the following requirements for setting up and using the TLE development kit\.
+ ** RDS for PostgreSQL 14\.5 or higher version** – Trusted Language Extensions is supported on RDS for PostgreSQL version 14\.5 and higher releases only\.
  + If you need to upgrade your RDS for PostgreSQL instance, see [Upgrading the PostgreSQL DB engine for Amazon RDS](USER_UpgradeDBInstance.PostgreSQL.md)\. 
  + If you don't yet have an Amazon RDS DB instance running PostgreSQL, you can create one\. For more information, see RDS for PostgreSQL DB instance, see [Creating a PostgreSQL DB instance and connecting to a database on a PostgreSQL DB instance](CHAP_GettingStarted.CreatingConnecting.PostgreSQL.md)\.  
+ **Requires `rds_superuser` privileges** – To set up and configure the `pg_tle` extension, your database user role must have the permission of the `rds_superuser` role\. By default, this role is granted to the `postgres` user that creates the RDS for PostgreSQL DB instance\.
+ **Requires a custom DB parameter group** – Your RDS for PostgreSQL DB instance must be configured with a custom DB parameter group\. 
  + If your RDS for PostgreSQL DB instance isn't configured with a custom DB parameter group, you should create one and associate it with your RDS for PostgreSQL DB instance\. For a short summary of steps, see [Creating and applying a custom DB parameter group](#PostgreSQL_trusted_language_extension-requirements-create-custom-params)\.
  + If your RDS for PostgreSQL DB instance is already configured using a custom DB parameter group, you can set up Trusted Language Extensions\. For details, see [Setting up Trusted Language Extensions in your RDS for PostgreSQL DB instance](#PostgreSQL_trusted_language_extension-setting-up)\.

### Creating and applying a custom DB parameter group<a name="PostgreSQL_trusted_language_extension-requirements-create-custom-params"></a>

Use the following steps to create a custom DB parameter group and configure your RDS for PostgreSQL DB instance to use it\. 

#### Console<a name="PostgreSQL_trusted_language_extension-requirements-custom-parameters.CON"></a>

**To create a custom DB parameter group and use it with your RDS for PostgreSQL DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. Choose Parameter groups from the Amazon RDS menu\. 

1. Choose **Create parameter group**\.

1. In the **Parameter group details** page, enter the following information\.
   + For **Parameter group family**, choose postgres14\.
   + For **Type**, choose DB Parameter Group\.
   + For **Group name**, give your parameter group a meaningful name in the context of your operations\.
   + For **Description**, enter a useful description so that others on your team can easily find it\.

1. Choose **Create**\. Your custom DB parameter group is created in your AWS Region\. You can now modify your RDS for PostgreSQL DB instance to use it by following the next steps\.

1. Choose **Databases** from the Amazon RDS menu\.

1. Choose the RDS for PostgreSQL DB instance that you want to use with TLE from among those listed, and then choose **Modify\.** 

1. In the Modify DB instance settings page, find **Database options** in the Additional configuration section and choose your custom DB parameter group from the selector\.

1. Choose **Continue** to save the change\.

1. Choose **Apply immediately** so that you can continue setting up the RDS for PostgreSQL DB instance to use TLE\.

To continue setting up your system for Trusted Language Extensions, see [Setting up Trusted Language Extensions in your RDS for PostgreSQL DB instance](#PostgreSQL_trusted_language_extension-setting-up)\.

For more information working with DB parameter groups, see [Working with DB parameter groups](USER_WorkingWithDBInstanceParamGroups.md)\. 

#### AWS CLI<a name="PostgreSQL_trusted_language_extension-requirements-custom-parameters-CLI"></a>

You can avoid specifying the `--region` argument when you use CLI commands by configuring your AWS CLI with your default AWS Region\. For more information, see [Configuration basics](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-quickstart.html#cli-configure-quickstart-config) in the *AWS Command Line Interface User Guide*\. 

**To create a custom DB parameter group and use it with your RDS for PostgreSQL DB instance**

1. Use the [create\-db\-parameter\-group](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-parameter-group.html) AWS CLI command to create a custom DB parameter group based on postgres14 for your AWS Region\. 

   For Linux, macOS, or Unix:

   ```
   aws rds create-db-parameter-group \
     --region aws-region \
     --db-parameter-group-name custom-params-for-pg-tle \
     --db-parameter-group-family postgres14 \
     --description "My custom DB parameter group for Trusted Language Extensions"
   ```

   For Windows:

   ```
   aws rds create-db-parameter-group ^
     --region aws-region ^
     --db-parameter-group-name custom-params-for-pg-tle ^
     --db-parameter-group-family postgres14 ^
     --description "My custom DB parameter group for Trusted Language Extensions"
   ```

   Your custom DB parameter group is available in your AWS Region, so you can modify RDS for PostgreSQL DB instance to use it\. 

1. Use the [modify\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) AWS CLI command to apply your custom DB parameter group to your RDS for PostgreSQL DB instance\. This command immediately reboots the active instance\.

   For Linux, macOS, or Unix:

   ```
   aws rds modify-db-instance \
     --region aws-region \
     --db-instance-identifier your-instance-name \
     --db-parameter-group-name custom-params-for-pg-tle \
     --apply-immediately
   ```

   For Windows:

   ```
   aws rds modify-db-instance ^
     --region aws-region ^
     --db-instance-identifier your-instance-name ^
     --db-parameter-group-name custom-params-for-pg-tle ^
     --apply-immediately
   ```

To continue setting up your system for Trusted Language Extensions, see [Setting up Trusted Language Extensions in your RDS for PostgreSQL DB instance](#PostgreSQL_trusted_language_extension-setting-up)\.

For more information, see [Working with parameter groups](USER_WorkingWithParamGroups.md)\. 

## Setting up Trusted Language Extensions in your RDS for PostgreSQL DB instance<a name="PostgreSQL_trusted_language_extension-setting-up"></a>

The following steps assume that your RDS for PostgreSQL DB instance is associated with a custom DB parameter group\. You can use the AWS Management Console or the AWS CLI for these steps\.

When you set up Trusted Language Extensions in your RDS for PostgreSQL DB instance, you install it in a specific database for use by the database users who have permissions on that database\. 

### Console<a name="PostgreSQL_trusted_language_extension-setting-up.CON"></a>

**To set up Trusted Language Extensions**

Perform the following steps using an account that's a member of the `rds_superuser` group \(role\)\.

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose your RDS for PostgreSQL DB instance\.

1. Open the **Configuration** tab for your RDS for PostgreSQL DB instance\. Among the Instance details, find the **Parameter group** link\.

1. Choose the link to open the custom parameters associated with your RDS for PostgreSQL DB instance\. 

1. In the **Parameters** search field, type `shared_pre` to find the `shared_preload_libraries` parameter\.

1. Choose **Edit parameters** to access the property values\.

1. Add `pg_tle` to the list in the **Values** field\. Use a comma to separate items in the list of values\.  
![\[Image of the shared_preload_libraries parameter with pg_tle added.\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/apg_rpg_shared_preload_pg_tle.png)

1. Reboot the RDS for PostgreSQL DB instance so that your change to the `shared_preload_libraries` parameter takes effect\.

1. When the instance is available, verify that `pg_tle` has been initialized\. Use `psql` to connect to the RDS for PostgreSQL DB instance, and then run the following command\.

   ```
   SHOW shared_preload_libraries;
   shared_preload_libraries 
   --------------------------
   rdsutils,pg_tle
   (1 row)
   ```

1. With the `pg_tle` extension initialized, you can now create the extension\. 

   ```
   CREATE EXTENSION pg_tle;
   ```

   You can verify that the extension is installed by using the following `psql` metacommand\.

   ```
   labdb=> \dx
                            List of installed extensions
     Name   | Version |   Schema   |                Description
   ---------+---------+------------+--------------------------------------------
    pg_tle  | 1.0.1   | pgtle      | Trusted-Language Extensions for PostgreSQL
    plpgsql | 1.0     | pg_catalog | PL/pgSQL procedural language
   ```

1. Grant the `pgtle_admin` role to the primary user name that you created for your RDS for PostgreSQL DB instance when you set it up\. If you accepted the default, it's `postgres`\. 

   ```
   labdb=> GRANT pgtle_admin TO postgres;
   GRANT ROLE
   ```

   You can verify that the grant has occurred by using the `psql` metacommand as shown in the following example\. Only the `pgtle_admin` and `postgres` roles are shown in the output\. For more information, see [Understanding the rds\_superuser role](Appendix.PostgreSQL.CommonDBATasks.Roles.md#Appendix.PostgreSQL.CommonDBATasks.Roles.rds_superuser)\. 

   ```
   labdb=> \du
                             List of roles
       Role name    |           Attributes            |               Member of
   -----------------+---------------------------------+-----------------------------------
   pgtle_admin     | Cannot login                     | {}
   postgres        | Create role, Create DB          +| {rds_superuser,pgtle_admin}
                   | Password valid until infinity    |...
   ```

1. Close the `psql` session using the `\q` metacommand\.

   ```
   \q
   ```

To get started creating TLE extensions, see [Example: Creating a trusted language extension using SQL](#PostgreSQL_trusted_language_extension-simple-example)\. 

### AWS CLI<a name="PostgreSQL_trusted_language_extension-setting-up-CLI"></a>

You can avoid specifying the `--region` argument when you use CLI commands by configuring your AWS CLI with your default AWS Region\. For more information, see [Configuration basics](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-quickstart.html#cli-configure-quickstart-config) in the *AWS Command Line Interface User Guide*\.

**To set up Trusted Language Extensions**

1. Use the [modify\-db\-parameter\-group](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-parameter-group.html) AWS CLI command to add `pg_tle` to the `shared_preload_libraries` parameter\.

   ```
   aws rds modify-db-parameter-group \
      --db-parameter-group-name custom-param-group-name \
      --parameters "ParameterName=shared_preload_libraries,ParameterValue=pg_tle,ApplyMethod=pending-reboot" \
      --region aws-region
   ```

1. Use the [reboot\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/reboot-db-instance) AWS CLI command to reboot the RDS for PostgreSQL DB instance and initialize the `pg_tle` library\.

   ```
   aws rds reboot-db-instance \
       --db-instance-identifier your-instance \
       --region aws-region
   ```

1. When the instance is available, you can verify that `pg_tle` has been initialized\. Use `psql` to connect to the RDS for PostgreSQL DB instance, and then run the following command\.

   ```
   SHOW shared_preload_libraries;
   shared_preload_libraries 
   --------------------------
   rdsutils,pg_tle
   (1 row)
   ```

   With `pg_tle` initialized, you can now create the extension\.

   ```
   CREATE EXTENSION pg_tle;
   ```

1. Grant the `pgtle_admin` role to the primary user name that you created for your RDS for PostgreSQL DB instance when you set it up\. If you accepted the default, it's `postgres`\.

   ```
   GRANT pgtle_admin TO postgres;
   GRANT ROLE
   ```

1. Close the `psql` session as follows\.

   ```
   labdb=> \q
   ```

To get started creating TLE extensions, see [Example: Creating a trusted language extension using SQL](#PostgreSQL_trusted_language_extension-simple-example)\. 

## Overview of Trusted Language Extensions for PostgreSQL<a name="PostgreSQL_trusted_language_extension.overview"></a>

Trusted Language Extensions for PostgreSQL is a PostgreSQL extension that you install in your RDS for PostgreSQL DB instance in the same way that you set up other PostgreSQL extensions\. In the following image of an example database in the pgAdmin client tool, you can view some of the components that comprise the `pg_tle` extension\.

![\[Image showing some of the components that make up the TLE development kit.\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/apg-pg_tle-installed-view-in-pgAdmin.png)

You can see the following details\.

1. The Trusted Language Extensions \(TLE\) for PostgreSQL development kit is packaged as the `pg_tle` extension\. As such, `pg_tle` is added to the available extensions for the database in which it's installed\.

1. TLE has its own schema, `pgtle`\. This schema contains helper functions \(3\) for installing and managing the extensions that you create\.

1. TLE provides over a dozen helper functions for installing, registering, and managing your extensions\. To learn more about these functions, see [Functions reference for Trusted Language Extensions for PostgreSQL](PostgreSQL_trusted_language_extension-functions-reference.md)\. 

Other components of the `pg_tle` extension include the following:
+ **The `pgtle_admin` role** – The `pgtle_admin` role is created when the `pg_tle` extension is installed\. This role is privileged and should be treated as such\. We strongly recommend that you follow the principle of *least privilege* when granting the `pgtle_admin` role to database users\. In other words, grant the `pgtle_admin` role only to database users that are allowed to create, install, and manage new TLE extensions, such as `postgres`\.
+ **The `pgtle.feature_info` table** – The `pgtle.feature_info` table is a protected table that contains information about your TLEs, hooks, and the custom stored procedures and functions that they use\. If you have `pgtle_admin` privileges, you use the following Trusted Language Extensions functions to add and update that information in the table\.
  + [pgtle\.register\_feature](pgtle.register_feature.md)
  + [pgtle\.register\_feature\_if\_not\_exists](pgtle.register_feature_if_not_exists.md)
  + [pgtle\.unregister\_feature](pgtle.unregister_feature.md)
  + [pgtle\.unregister\_feature\_if\_exists](pgtle.unregister_feature_if_exists.md)

## Creating TLE extensions for RDS for PostgreSQL<a name="PostgreSQL_trusted_language_extension-creating-TLE-extensions"></a>

You can install any extensions that you create with TLE in any RDS for PostgreSQL DB instance that has the `pg_tle` extension installed\. The `pg_tle` extension is scoped to the PostgreSQL database in which it's installed\. The extensions that you create using TLE are scoped to the same database\. 

Use the various `pgtle` functions to install the code that makes up your TLE extension\. The following Trusted Language Extensions functions all require the `pgtle_admin` role\.
+ [pgtle\.install\_extension](pgtle.install_extension.md)
+ [pgtle\.install\_update\_path](pgtle.install_update_path.md)
+ [pgtle\.register\_feature](pgtle.register_feature.md)
+ [pgtle\.register\_feature\_if\_not\_exists](pgtle.register_feature_if_not_exists.md)
+ [pgtle\.set\_default\_version](pgtle.set_default_version.md)
+ [pgtle\.uninstall\_extension\(name\)](pgtle.uninstall_extension-name.md)
+ [pgtle\.uninstall\_extension\(name, version\)](pgtle.uninstall_extension-name-version.md)
+ [pgtle\.uninstall\_extension\_if\_exists](pgtle.uninstall_extension_if_exists.md)
+ [pgtle\.uninstall\_update\_path](pgtle.uninstall_update_path.md)
+ [pgtle\.uninstall\_update\_path\_if\_exists](pgtle.uninstall_update_path_if_exists.md)
+ [pgtle\.unregister\_feature](pgtle.unregister_feature.md)
+ [pgtle\.unregister\_feature\_if\_exists](pgtle.unregister_feature_if_exists.md)

### Example: Creating a trusted language extension using SQL<a name="PostgreSQL_trusted_language_extension-simple-example"></a>

The following example shows you how to create a TLE extension named `pg_distance` that contains a few SQL functions for calculating distances using different formulas\. In the listing, you can find the function for calculating the Manhattan distance and the function for calculating the Euclidean distance\. For more information about the difference between these formulas, see [Taxicab geometry](https://en.wikipedia.org/wiki/Taxicab_geometry) and [Euclidean geometry](https://en.wikipedia.org/wiki/Euclidean_geometry) in Wikipedia\. 

You can use this example in your own RDS for PostgreSQL DB instance if you have the `pg_tle` extension set up as detailed in [Setting up Trusted Language Extensions in your RDS for PostgreSQL DB instance](#PostgreSQL_trusted_language_extension-setting-up)\.

**Note**  
You need to have the privileges of the `pgtle_admin` role to follow this procedure\.

**To create the example TLE extension**

The following steps use an example database named `labdb`\. This database is owned by the `postgres` primary user\. The `postgres` role also has the permissions of the `pgtle_admin` role\.

1. Use `psql` to connect to RDS for PostgreSQL DB instance\. 

   ```
   psql --host=db-instance-123456789012.aws-region.rds.amazonaws.com
   --port=5432 --username=postgres --password --dbname=labdb
   ```

1. Create a TLE extension named `pg_distance` by copying the following code and pasting it into your `psql` session console\.

   ```
   SELECT pgtle.install_extension
   (
    'pg_distance',
    '0.1',
     'Distance functions for two points',
   $_pg_tle_$
       CREATE FUNCTION dist(x1 float8, y1 float8, x2 float8, y2 float8, norm int)
       RETURNS float8
       AS $$
         SELECT (abs(x2 - x1) ^ norm + abs(y2 - y1) ^ norm) ^ (1::float8 / norm);
       $$ LANGUAGE SQL;
   
       CREATE FUNCTION manhattan_dist(x1 float8, y1 float8, x2 float8, y2 float8)
       RETURNS float8
       AS $$
         SELECT dist(x1, y1, x2, y2, 1);
       $$ LANGUAGE SQL;
   
       CREATE FUNCTION euclidean_dist(x1 float8, y1 float8, x2 float8, y2 float8)
       RETURNS float8
       AS $$
         SELECT dist(x1, y1, x2, y2, 2);
       $$ LANGUAGE SQL;
   $_pg_tle_$
   );
   ```

   You see the output, such as the following\.

   ```
   install_extension
   ---------------
    t
   (1 row)
   ```

   The artifacts that make up the `pg_distance` extension are now installed in your database\. These artifacts include the control file and the code for the extension, which are items that need to be present so that the extension can be created using the `CREATE EXTENSION` command\. In other words, you still need to create the extension to make its functions available to database users\.

1. To create the extension, use the `CREATE EXTENSION` command as you do for any other extension\. As with other extensions, the database user needs to have the `CREATE` permissions in the database\.

   ```
   CREATE EXTENSION pg_distance;
   ```

1. To test the `pg_distance` TLE extension, you can use it to calculate the [Manhattan distance](https://en.wikipedia.org/wiki/Taxicab_geometry) between four points\.

   ```
   labdb=> SELECT manhattan_dist(1, 1, 5, 5);
   8
   ```

   To calculate the [Euclidean distance](https://en.wikipedia.org/wiki/Euclidean_geometry) between the same set of points, you can use the following\.

   ```
   labdb=> SELECT euclidean_dist(1, 1, 5, 5);
   5.656854249492381
   ```

The `pg_distance` extension loads the functions in the database and makes them available to any users with permissions on the database\.

### Modifying your TLE extension<a name="PostgreSQL_trusted_language_extension-simple-example.modify"></a>

To improve query performance for the functions packaged in this TLE extension, add the following two PostgreSQL attributes to their specifications\.
+ `IMMUTABLE` – The `IMMUTABLE` attribute ensures that the query optimizer can use optimizations to improve query response times\. For more information, see [Function Volatility Categories](https://www.postgresql.org/docs/current/xfunc-volatility.html) in the PostgreSQL documentation\.
+ `PARALLEL SAFE` – The `PARALLEL SAFE` attribute is another attribute that allows PostgreSQL to run the function in parallel mode\. For more information, see [CREATE FUNCTION](https://www.postgresql.org/docs/current/sql-createfunction.html) in the PostgreSQL documentation\.

In the following example, you can see how the `pgtle.install_update_path` function is used to add these attributes to each function to create a version `0.2` of the `pg_distance` TLE extension\. For more information about this function, see [pgtle\.install\_update\_path](pgtle.install_update_path.md)\. You need to have the `pgtle_admin` role to perform this task\. 

**To update an existing TLE extension and specify the default version**

1. Connect to RDS for PostgreSQL DB instance using `psql` or another client tool, such as pgAdmin\.

   ```
   psql --host=db-instance-123456789012.aws-region.rds.amazonaws.com
   --port=5432 --username=postgres --password --dbname=labdb
   ```

1. Modify the existing TLE extension by copying the following code and pasting it into your `psql` session console\.

   ```
   SELECT pgtle.install_update_path
   (
    'pg_distance',
    '0.1',
    '0.2',
   $_pg_tle_$
       CREATE OR REPLACE FUNCTION dist(x1 float8, y1 float8, x2 float8, y2 float8, norm int)
       RETURNS float8
       AS $$
         SELECT (abs(x2 - x1) ^ norm + abs(y2 - y1) ^ norm) ^ (1::float8 / norm);
       $$ LANGUAGE SQL IMMUTABLE PARALLEL SAFE;
   
       CREATE OR REPLACE FUNCTION manhattan_dist(x1 float8, y1 float8, x2 float8, y2 float8)
       RETURNS float8
       AS $$
         SELECT dist(x1, y1, x2, y2, 1);
       $$ LANGUAGE SQL IMMUTABLE PARALLEL SAFE;
   
       CREATE OR REPLACE FUNCTION euclidean_dist(x1 float8, y1 float8, x2 float8, y2 float8)
       RETURNS float8
       AS $$
         SELECT dist(x1, y1, x2, y2, 2);
       $$ LANGUAGE SQL IMMUTABLE PARALLEL SAFE;
   $_pg_tle_$
   );
   ```

   You see a response similar to the following\.

   ```
   install_update_path
   ---------------------
    t
   (1 row)
   ```

   You can make this version of the extension the default version, so that database users don't have to specify a version when they create or update the extension in their database\.

1. To specify that the modified version \(version 0\.2\) of your TLE extension is the default version, use the `pgtle.set_default_version` function as shown in the following example\.

   ```
   SELECT pgtle.set_default_version('pg_distance', '0.2');
   ```

   For more information about this function, see [pgtle\.set\_default\_version](pgtle.set_default_version.md)\.

1. With the code in place, you can update the installed TLE extension in the usual way, by using `ALTER EXTENSION ... UPDATE` command, as shown here:

   ```
   ALTER EXTENSION pg_distance UPDATE;
   ```

## Dropping your TLE extensions from a database<a name="PostgreSQL_trusted_language_extension-creating-TLE-extensions.dropping-TLEs"></a>

You can drop your TLE extensions by using the `DROP EXTENSION` command in the same way that you do for other PostgreSQL extensions\. Dropping the extension doesn't remove the installation files that make up the extension, which allows users to re\-create the extension\. To remove the extension and its installation files, do the following two\-step process\.

**To drop the TLE extension and remove its installation files**

1. Use `psql` or another client tool to connect to the RDS for PostgreSQL DB instance\. 

   ```
   psql --host=.111122223333.aws-region.rds.amazonaws.com --port=5432 --username=postgres --password --dbname=dbname
   ```

1. Drop the extension as you would any PostgreSQL extension\.

   ```
   DROP EXTENSION your-TLE-extension
   ```

   For example, if you create the `pg_distance` extension as detailed in [Example: Creating a trusted language extension using SQL](#PostgreSQL_trusted_language_extension-simple-example), you can drop the extension as follows\.

   ```
   DROP EXTENSION pg_distance;
   ```

   You see output confirming that the extension has been dropped, as follows\.

   ```
   DROP EXTENSION
   ```

   At this point, the extension is no longer active in the database\. However, its installation files and control file are still available in the database, so database users can create the extension again if they like\.
   + If you want to leave the extension files intact so that database users can create your TLE extension, you can stop here\.
   + If you want to remove all files that make up the extension, continue to the next step\.

1. To remove all installation files for your extension, use the `pgtle.uninstall_extension` function\. This function removes all the code and control files for your extension\.

   ```
   SELECT pgtle.uninstall_extension('your-tle-extension-name');
   ```

   For example, to remove all `pg_distance` installation files, use the following command\.

   ```
   SELECT pgtle.uninstall_extension('pg_distance');
    uninstall_extension
   ---------------------
    t
   (1 row)
   ```

## Uninstalling Trusted Language Extensions for PostgreSQL<a name="PostgreSQL_trusted_language_extension-uninstalling-pg_tle-devkit"></a>

If you no longer want to create your own TLE extensions using TLE, you can drop the `pg_tle` extension and remove all artifacts\. This action includes dropping any TLE extensions in the database and dropping the `pgtle` schema\.

**To drop the `pg_tle` extension and its schema from a database**

1. Use `psql` or another client tool to connect to the RDS for PostgreSQL DB instance\. 

   ```
   psql --host=.111122223333.aws-region.rds.amazonaws.com --port=5432 --username=postgres --password --dbname=dbname
   ```

1. Drop the `pg_tle` extension from the database\. If the database has your own TLE extensions still running in the database, you need to also drop those extensions\. To do so, you can use the `CASCADE` keyword, as shown in the following\.

   ```
   DROP EXTENSION pg_tle CASCADE;
   ```

   If the `pg_tle` extension isn't still active in the database, you don't need to use the `CASCADE` keyword\.

1. Drop the `pgtle` schema\. This action removes all the management functions from the database\.

   ```
   DROP SCHEMA pgtle CASCADE;
   ```

   The command returns the following when the process completes\.

   ```
   DROP SCHEMA
   ```

   The `pg_tle` extension, its schema and functions, and all artifacts are removed\. To create new extensions using TLE, go through the setup process again\. For more information, see [Setting up Trusted Language Extensions in your RDS for PostgreSQL DB instance](#PostgreSQL_trusted_language_extension-setting-up)\. 

## Using PostgreSQL hooks with your TLE extensions<a name="PostgreSQL_trusted_language_extension.overview.tles-and-hooks"></a>

A *hook* is a callback mechanism available in PostgreSQL that allows developers to call custom functions or other routines during regular database operations\. The TLE development kit supports PostgreSQL hooks so that you can integrate custom functions with PostgreSQL behavior at runtime\. For example, you can use a hook to associate the authentication process with your own custom code, or to modify the query planning and execution process for your specific needs\.

Your TLE extensions can use hooks\. If a hook is global in scope, it applies across all databases\. Therefore, if your TLE extension uses a global hook, then you need to create your TLE extension in all databases that your users can access\.

When you use the `pg_tle` extension to build your own Trusted Language Extensions, you can use the available hooks from a SQL API to build out the functions of your extension\. You should register any hooks with `pg_tle`\. For some hooks, you might also need to set various configuration parameters\. For example, the `passcode` check hook can be set to on, off, or require\. For more information about specific requirements for available `pg_tle` hooks, see [Hooks reference for Trusted Language Extensions for PostgreSQL](PostgreSQL_trusted_language_extension-hooks-reference.md)\. 

### Example: Creating an extension that uses a PostgreSQL hook<a name="PostgreSQL_trusted_language_extension-example-hook"></a>

The example discussed in this section uses a PostgreSQL hook to check the password provided during specific SQL operations and prevents database users from setting their passwords to any of those contained in the `password_check.bad_passwords` table\. The table contains the top\-ten most commonly used, but easily breakable choices for passwords\. 

To set up this example in your RDS for PostgreSQL DB instance, you must have already installed Trusted Language Extensions\. For details, see [Setting up Trusted Language Extensions in your RDS for PostgreSQL DB instance](#PostgreSQL_trusted_language_extension-setting-up)\. 

**To set up the password\-check hook example**

1. Use `psql` to connect to RDS for PostgreSQL DB instance\. 

   ```
   psql --host=db-instance-123456789012.aws-region.rds.amazonaws.com
   --port=5432 --username=postgres --password --dbname=labdb
   ```

1. Copy the code from the [Password\-check hook code listing](#PostgreSQL_trusted_language_extension-example-hook_code_listing) and paste it into your database\.

   ```
   SELECT pgtle.install_extension (
     'my_password_check_rules',
     '1.0',
     'Do not let users use the 10 most commonly used passwords',
   $_pgtle_$
     CREATE SCHEMA password_check;
     REVOKE ALL ON SCHEMA password_check FROM PUBLIC;
     GRANT USAGE ON SCHEMA password_check TO PUBLIC;
   
     CREATE TABLE password_check.bad_passwords (plaintext) AS
     VALUES
       ('123456'),
       ('password'),
       ('12345678'),
       ('qwerty'),
       ('123456789'),
       ('12345'),
       ('1234'),
       ('111111'),
       ('1234567'),
       ('dragon');
     CREATE UNIQUE INDEX ON password_check.bad_passwords (plaintext);
   
     CREATE FUNCTION password_check.passcheck_hook(username text, password text, password_type pgtle.password_types, valid_until timestamptz, valid_null boolean)
     RETURNS void AS $$
       DECLARE
         invalid bool := false;
       BEGIN
         IF password_type = 'PASSWORD_TYPE_MD5' THEN
           SELECT EXISTS(
             SELECT 1
             FROM password_check.bad_passwords bp
             WHERE ('md5' || md5(bp.plaintext || username)) = password
           ) INTO invalid;
           IF invalid THEN
             RAISE EXCEPTION 'Cannot use passwords from the common password dictionary';
           END IF;
         ELSIF password_type = 'PASSWORD_TYPE_PLAINTEXT' THEN
           SELECT EXISTS(
             SELECT 1
             FROM password_check.bad_passwords bp
             WHERE bp.plaintext = password
           ) INTO invalid;
           IF invalid THEN
             RAISE EXCEPTION 'Cannot use passwords from the common common password dictionary';
           END IF;
         END IF;
       END
     $$ LANGUAGE plpgsql SECURITY DEFINER;
   
     GRANT EXECUTE ON FUNCTION password_check.passcheck_hook TO PUBLIC;
   
     SELECT pgtle.register_feature('password_check.passcheck_hook', 'passcheck');
   $_pgtle_$
   );
   ```

   When the extension has been loaded into your database, you see the output such as the following\.

   ```
    install_extension
   -------------------
    t
   (1 row)
   ```

1. While still connected to the database, you can now create the extension\. 

   ```
   CREATE EXTENSION my_password_check_rules;
   ```

1. You can confirm that the extension has been created in the database by using the following `psql` metacommand\.

   ```
   \dx
                           List of installed extensions
             Name           | Version |   Schema   |                         Description
   -------------------------+---------+------------+-------------------------------------------------------------
    my_password_check_rules | 1.0     | public     | Prevent use of any of the top-ten most common bad passwords
    pg_tle                  | 1.0.1   | pgtle      | Trusted-Language Extensions for PostgreSQL
    plpgsql                 | 1.0     | pg_catalog | PL/pgSQL procedural language
   (3 rows)
   ```

1. Open another terminal session to work with the AWS CLI\. You need to modify your custom DB parameter group to turn on the password\-check hook\. To do so, use the [modify\-db\-parameter\-group](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-parameter-group.html) CLI command as shown in the following example\.

   ```
   aws rds modify-db-parameter-group \
       --region aws-region \
       --db-parameter-group-name your-custom-parameter-group \
       --parameters "ParameterName=pgtle.enable_password_check,ParameterValue=on,ApplyMethod=immediate"
   ```

   When the parameter is successfully turned on, you see the output such as the following\.

   ```
   (
       "DBParameterGroupName": "docs-lab-parameters-for-tle"
   }
   ```

   It might take a few minutes for the change to the parameter group setting to take effect\. This parameter is dynamic, however, so you don't need to restart the RDS for PostgreSQL DB instance for the setting to take effect\.

1. Open the `psql` session and query the database to verify that the password\_check hook has been turned on\.

   ```
   labdb=> SHOW pgtle.enable_password_check;
   pgtle.enable_password_check
   -----------------------------
   on
   (1 row)
   ```

The password\-check hook is now active\. You can test it by creating a new role and using one of the bad passwords, as shown in the following example\.

```
CREATE ROLE test_role PASSWORD 'password';
ERROR:  Cannot use passwords from the common password dictionary
CONTEXT:  PL/pgSQL function password_check.passcheck_hook(text,text,pgtle.password_types,timestamp with time zone,boolean) line 21 at RAISE
SQL statement "SELECT password_check.passcheck_hook(
    $1::pg_catalog.text, 
    $2::pg_catalog.text, 
    $3::pgtle.password_types, 
    $4::pg_catalog.timestamptz, 
    $5::pg_catalog.bool)"
```

The output has been formatted for readability\.

The following example shows that `pgsql` interactive metacommand `\password` behavior is also affected by the password\_check hook\. 

```
postgres=> SET password_encryption TO 'md5';
SET
postgres=> \password
Enter new password for user "postgres":*****
Enter it again:*****
ERROR:  Cannot use passwords from the common password dictionary
CONTEXT:  PL/pgSQL function password_check.passcheck_hook(text,text,pgtle.password_types,timestamp with time zone,boolean) line 12 at RAISE
SQL statement "SELECT password_check.passcheck_hook($1::pg_catalog.text, $2::pg_catalog.text, $3::pgtle.password_types, $4::pg_catalog.timestamptz, $5::pg_catalog.bool)"
```

You can drop this TLE extension and uninstall its source files if you want\. For more information, see [Dropping your TLE extensions from a databaseDropping your TLE extensions from a database](#PostgreSQL_trusted_language_extension-creating-TLE-extensions.dropping-TLEs)\. 

#### Password\-check hook code listing<a name="PostgreSQL_trusted_language_extension-example-hook_code_listing"></a>

The example code shown here defines the specification for the `my_password_check_rules` TLE extension\. When you copy this code and paste it into your database, the code for the `my_password_check_rules` extension is loaded into the database, and the `password_check` hook is registered for use by the extension\.

```
SELECT pgtle.install_extension (
  'my_password_check_rules',
  '1.0',
  'Do not let users use the 10 most commonly used passwords',
$_pgtle_$
  CREATE SCHEMA password_check;
  REVOKE ALL ON SCHEMA password_check FROM PUBLIC;
  GRANT USAGE ON SCHEMA password_check TO PUBLIC;

  CREATE TABLE password_check.bad_passwords (plaintext) AS
  VALUES
    ('123456'),
    ('password'),
    ('12345678'),
    ('qwerty'),
    ('123456789'),
    ('12345'),
    ('1234'),
    ('111111'),
    ('1234567'),
    ('dragon');
  CREATE UNIQUE INDEX ON password_check.bad_passwords (plaintext);

  CREATE FUNCTION password_check.passcheck_hook(username text, password text, password_type pgtle.password_types, valid_until timestamptz, valid_null boolean)
  RETURNS void AS $$
    DECLARE
      invalid bool := false;
    BEGIN
      IF password_type = 'PASSWORD_TYPE_MD5' THEN
        SELECT EXISTS(
          SELECT 1
          FROM password_check.bad_passwords bp
          WHERE ('md5' || md5(bp.plaintext || username)) = password
        ) INTO invalid;
        IF invalid THEN
          RAISE EXCEPTION 'Cannot use passwords from the common password dictionary';
        END IF;
      ELSIF password_type = 'PASSWORD_TYPE_PLAINTEXT' THEN
        SELECT EXISTS(
          SELECT 1
          FROM password_check.bad_passwords bp
          WHERE bp.plaintext = password
        ) INTO invalid;
        IF invalid THEN
          RAISE EXCEPTION 'Cannot use passwords from the common common password dictionary';
        END IF;
      END IF;
    END
  $$ LANGUAGE plpgsql SECURITY DEFINER;

  GRANT EXECUTE ON FUNCTION password_check.passcheck_hook TO PUBLIC;

  SELECT pgtle.register_feature('password_check.passcheck_hook', 'passcheck');
$_pgtle_$
);
```