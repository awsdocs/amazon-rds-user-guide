# pgtle\.uninstall\_update\_path\_if\_exists<a name="pgtle.uninstall_update_path_if_exists"></a>

The `uninstall_update_path_if_exists` function is similar to `uninstall_update_path` in that it removes the specified update path from a TLE extension\. However, if the update path doesn't exist, this function doesn't raise an error message\. Instead, the function returns `false`\.

## Function prototype<a name="pgtle.uninstall_update_path_if_exists-prototype"></a>

```
pgtle.uninstall_update_path_if_exists(extname text, fromvers text, tovers text)
```

## Role<a name="pgtle.uninstall_update_path_if_exists-role"></a>

`pgtle_admin`

## Arguments<a name="pgtle.uninstall_update_path_if_exists-arguments"></a>
+ `extname` – The name of the TLE extension\. This value is used when calling `CREATE EXTENSION`\.
+ `fromvers` – The source version of the TLE extension used on the update path\.
+ `tovers` – The destination version of the TLE extension used on the update path\.

## Output<a name="pgtle.uninstall_update_path_if_exists-output"></a>
+ `true` – The function has successfully updated the path for the TLE extension\.
+ `false` – The function wasn't able to update the path for the TLE extension\.

## Usage example<a name="pgtle.uninstall_update_path_if_exists-example"></a>

```
SELECT * FROM pgtle.uninstall_update_path_if_exists(pg_tle_test, 0.1, 0.2);
```