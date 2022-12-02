# pgtle\.set\_default\_version<a name="pgtle.set_default_version"></a>

The `set_default_version` function lets you specify a `default_version` for your TLE extension\. You can use this function to define an upgrade path and designate the version as the default for your TLE extension\. When database users specify your TLE extension in the `CREATE EXTENSION` and `ALTER EXTENSION ... UPDATE` commands, that version of your TLE extension is created in the database for that user\.

This function returns `true` on success\. If the TLE extension specified in the `name` argument doesn't exist, the function returns an error\. Similarly, if the `version` of the TLE extension doesn't exist, it returns an error\.

## Function prototype<a name="pgtle.set_default_version-prototype"></a>

```
pgtle.set_default_version(name text, version text)
```

## Role<a name="pgtle.set_default_version-role"></a>

`pgtle_admin`

## Arguments<a name="pgtle.set_default_version-arguments"></a>
+ `name` – The name of the TLE extension\. This value is used when calling `CREATE EXTENSION`\.
+ `version` – The version of the TLE extension to set the default\.

## Output<a name="pgtle.set_default_version-output"></a>
+ `true` – When setting default version succeeds, the function returns `true`\.
+ `ERROR` – Returns an error message if a TLE extension with the specified name or version doesn't exist\. 

## Usage example<a name="pgtle.set_default_version-example"></a>

```
SELECT * FROM pgtle.set_default_version(my-extension, 1.1);
```