# pgtle\.uninstall\_extension\(name\)<a name="pgtle.uninstall_extension-name"></a>

The `uninstall_extension` function removes all versions of a TLE extension from a database\. This function prevents future calls of `CREATE EXTENSION` from installing the TLE extension\. If the TLE extension doesn't exist in the database, an error is raised\.

The `uninstall_extension` function won't drop a TLE extension that's currently active in the database\. To remove a TLE extension that's currently active, you need to explicitly call `DROP EXTENSION` to remove it\. 

## Function prototype<a name="pgtle.uninstall_extension-name-prototype"></a>

```
pgtle.uninstall_extension(extname text)
```

## Role<a name="pgtle.uninstall_extension-name-role"></a>

`pgtle_admin`

## Arguments<a name="pgtle.uninstall_extension-name-arguments"></a>
+ `extname` – The name of the TLE extension to uninstall\. This name is the same as the one used with `CREATE EXTENSION` to load the TLE extension for use in a given database\. 

## Output<a name="pgtle.uninstall_extension-name-output"></a>

None\. 

## Usage example<a name="pgtle.uninstall_extension-name-example"></a>

```
SELECT * FROM pgtle.uninstall_extension(pg_tle_test);
```