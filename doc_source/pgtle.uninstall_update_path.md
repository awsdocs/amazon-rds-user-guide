# pgtle\.uninstall\_update\_path<a name="pgtle.uninstall_update_path"></a>

The `uninstall_update_path` function removes the specific update path from a TLE extension\. This prevents `ALTER EXTENSION ... UPDATE TO` from using this as an update path\.

If the TLE extension is currently being used by one of the versions on this update path, it remains in the database\.

If the update path specified doesn't exist, this function raises an error\.

## Function prototype<a name="pgtle.uninstall_update_path-prototype"></a>

```
pgtle.uninstall_update_path(extname text, fromvers text, tovers text)
```

## Role<a name="pgtle.uninstall_update_path-role"></a>

`pgtle_admin`

## Arguments<a name="pgtle.uninstall_update_path-arguments"></a>
+ `extname` – The name of the TLE extension\. This value is used when calling `CREATE EXTENSION`\.
+ `fromvers` – The source version of the TLE extension used on the update path\.
+  `tovers` – The destination version of the TLE extension used on the update path\.

## Output<a name="pgtle.uninstall_update_path-output"></a>

None\.

## Usage example<a name="pgtle.uninstall_update_path-example"></a>

```
SELECT * FROM pgtle.uninstall_update_path(pg_tle_test, 0.1, 0.2);
```