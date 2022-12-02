# pgtle\.available\_extension\_versions<a name="pgtle.available_extension_versions"></a>

The `available_extension_versions` function is a set\-returning function\. It returns a list of all available TLE extensions and their versions\. Each row contains information about a specific version of the given TLE extension, including whether it requires a specific role\.

## Function prototype<a name="pgtle.available_extension_versions-prototype"></a>

```
pgtle.available_extension_versions()
```

## Role<a name="pgtle.available_extension_versions-role"></a>

None\.

## Arguments<a name="pgtle.available_extension_versions-arguments"></a>

None\.

## Output<a name="pgtle.available_extension_versions-output"></a>
+ `name` – The name of the TLE extension\.
+ `version` – The version of the TLE extension\.
+ `superuser` – This value is always `false` for your TLE extensions\. The permissions needed to create the TLE extension or update it are the same as for creating other objects in the given database\. 
+ `trusted` – This value is always `false` for a TLE extension\.
+ `relocatable` – This value is always `false` for a TLE extension\.
+ `schema` – Specifies the name of the schema in which the TLE extension is installed\.
+ `requires` – An array containing the names of other extensions needed by this TLE extension\.
+ `description` – A detailed description of the TLE extension\.

For more information about output values, see [Packaging Related Objects into an Extension > Extension Files](https://www.postgresql.org/docs/current/extend-extensions.html#id-1.8.3.20.11) in the PostgreSQL documentation\.

## Usage example<a name="pgtle.available_extension_versions-example"></a>

```
SELECT * FROM pgtle.available_extension_versions();
```