# pgtle\.install\_update\_path<a name="pgtle.install_update_path"></a>

The `install_update_path` function provides an update path between two different versions of a TLE extension\. This function allows users of your TLE extension to update its version by using the `ALTER EXTENSION ... UPDATE` syntax\.

## Function prototype<a name="pgtle.install_update_path-prototype"></a>

```
pgtle.install_update_path(name text, fromvers text, tovers text, ext text)
```

## Role<a name="pgtle.install_update_path-role"></a>

`pgtle_admin`

## Arguments<a name="pgtle.install_update_path-arguments"></a>
+ `name` – The name of the TLE extension\. This value is used when calling `CREATE EXTENSION`\.
+ `fromvers` – The source version of the TLE extension for the upgrade\.
+ `tovers` – The destination version of the TLE extension for the upgrade\.
+ `ext` – The contents of the update\. This value contains objects such as functions\.

## Output<a name="pgtle.install_update_path-output"></a>

None\.

## Usage example<a name="pgtle.install_update_path-example"></a>

```
SELECT pgtle.install_update_path(pg_tle_test, 0.1, 0.2,
  $_pgtle_$
    CREATE OR REPLACE FUNCTION my_test()
    RETURNS INT
    AS $$
      SELECT 21;
    $$ LANGUAGE SQL IMMUTABLE;
  $_pgtle_$
);
```