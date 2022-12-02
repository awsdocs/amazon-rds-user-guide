# pgtle\.uninstall\_extension\_if\_exists<a name="pgtle.uninstall_extension_if_exists"></a>

The `uninstall_extension_if_exists` function removes all versions of a TLE extension from a given database\. If the TLE extension doesn't exist, the function returns silently \(no error message is raised\)\. If the specified extension is currently active within a database, this function doesn't drop it\. You must explicitly call `DROP EXTENSION` to remove the TLE extension before using this function to uninstall its artifacts\.

## Function prototype<a name="pgtle.uninstall_extension_if_exists-prototype"></a>

```
pgtle.uninstall_extension_if_exists(extname text)
```

## Role<a name="pgtle.uninstall_extension_if_exists-role"></a>

`pgtle_admin`

## Arguments<a name="pgtle.uninstall_extension_if_exists-arguments"></a>
+ `extname` – The name of the TLE extension\. This value is used when calling `CREATE EXTENSION`\.

## Output<a name="pgtle.uninstall_extension_if_exists-output"></a>

The `uninstall_extension_if_exists` function returns `true` after uninstalling the specified extension\. If the specified extension doesn't exist, the function returns `false`\.
+ `true` – Returns `true` after uninstalling the TLE extension\.
+ `false` – Returns `false` when the TLE extension doesn't exist in the database\.

## Usage example<a name="pgtle.uninstall_extension_if_exists-example"></a>

```
SELECT * FROM pgtle.uninstall_extension_if_exists(pg_tle_test);
```