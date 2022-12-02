# pgtle\.uninstall\_extension\(name, version\)<a name="pgtle.uninstall_extension-name-version"></a>

The `uninstall_extension(name, version)` function removes the specified version of the TLE extension from the database\. This function prevents `CREATE EXTENSION` and `ALTER EXTENSION` from installing or updating a TLE extension to the specified version\. This function also removes all update paths for the specified version of the TLE extension\. This function won't uninstall the TLE extension if it's currently active in the database\. You must explicitly call `DROP EXTENSION` to remove the TLE extension\. To uninstall all versions of a TLE extension, see [pgtle\.uninstall\_extension\(name\)](pgtle.uninstall_extension-name.md)\.

## Function prototype<a name="pgtle.uninstall_extension-name-version-prototype"></a>

```
pgtle.uninstall_extension(extname text, version text)
```

## Role<a name="pgtle.uninstall_extension-name-version-role"></a>

`pgtle_admin`

## Arguments<a name="pgtle.uninstall_extension-name-version-arguments"></a>
+ `extname` – The name of the TLE extension\. This value is used when calling `CREATE EXTENSION`\.
+ `version` – The version of the TLE extension to uninstall from the database\.

## Output<a name="pgtle.uninstall_extension-name-version-output"></a>

None\. 

## Usage example<a name="pgtle.uninstall_extension-name-version-example"></a>

```
SELECT * FROM pgtle.uninstall_extension(pg_tle_test, 0.2);
```