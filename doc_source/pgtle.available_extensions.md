# pgtle\.available\_extensions<a name="pgtle.available_extensions"></a>

The `pgtle.available_extensions` function is a set\-returning function\. It returns all available TLE extensions in the database\. Each returned row contains information about a single TLE extension\.

## Function prototype<a name="pgtle.available_extensions-prototype"></a>

```
pgtle.available_extensions()
```

## Role<a name="pgtle.available_extensions-role"></a>

None\.

## Arguments<a name="pgtle.available_extensions-arguments"></a>

None\.

## Output<a name="pgtle.available_extensions-output"></a>
+ `name` – The name of the TLE extension\.
+ `default_version` – The version of the TLE extension to use when `CREATE EXTENSION` is called without a version specified\.
+ `description` – A more detailed description about the TLE extension\.

## Usage example<a name="pgtle.available_extensions-usage-example"></a>

```
SELECT * FROM pgtle.available_extensions();
```