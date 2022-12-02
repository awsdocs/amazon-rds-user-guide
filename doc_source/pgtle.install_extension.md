# pgtle\.install\_extension<a name="pgtle.install_extension"></a>

The `install_extension` function lets you install the artifacts that make up your TLE extension in the database, after which it can be created using the `CREATE EXTENSION` command\.

## Function prototype<a name="pgtle.install_extension-prototype"></a>

```
pgtle.install_extension(name text, version text, description text, ext text, requires text[] DEFAULT NULL::text[])
```

## Role<a name="pgtle.install_extension-role"></a>

None\.

## Arguments<a name="pgtle.install_extension-arguments"></a>
+ `name` – The name of the TLE extension\. This value is used when calling `CREATE EXTENSION`\.
+ `version` – The version of the TLE extension\.
+ `description` – A detailed description about the TLE extension\. This description is displayed in the `comment` field in `pgtle.available_extensions()`\.
+ `ext` – The contents of the TLE extension\. This value contains objects such as functions\.
+ `requires` – An optional parameter that specifies dependencies for this TLE extension\. The `pg_tle` extension is automatically added as a dependency\.

Many of these arguments are the same as those that are included in an extension control file for installing a PostgreSQL extension on the file system of a PostgreSQL instance\. For more information, see the [Extension Files](http://www.postgresql.org/docs/current/extend-extensions.html#id-1.8.3.20.11) in [Packaging Related Objects into an Extension](https://www.postgresql.org/docs/current/extend-extensions.html) in the PostgreSQL documentation\.

## Output<a name="pgtle.install_extension-output"></a>

This functions returns `OK` on success and `NULL` on error\.
+ `OK` – The TLE extension has been successfully installed in the database\.
+ `NULL` – The TLE extension hasn't been successfully installed in the database\.

## Usage example<a name="pgtle.install_extension-example"></a>

```
SELECT pgtle.install_extension(
 pg_tle_test,
 0.1,
 My first pg_tle extension,
$_pgtle_$
  CREATE FUNCTION my_test()
  RETURNS INT
  AS $$
    SELECT 42;
  $$ LANGUAGE SQL IMMUTABLE;
$_pgtle_$
);
```