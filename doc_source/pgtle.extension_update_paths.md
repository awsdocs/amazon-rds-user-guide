# pgtle\.extension\_update\_paths<a name="pgtle.extension_update_paths"></a>

The `extension_update_paths` function is a set\-returning function\. It returns a list of all the possible update paths for a TLE extension\. Each row includes the available upgrades or downgrades for that TLE extension\.

## Function prototype<a name="pgtle.extension_update_paths-prototype"></a>

```
pgtle.extension_update_paths(name)
```

## Role<a name="pgtle.extension_update_paths-role"></a>

None\.

## Arguments<a name="pgtle.extension_update_paths-arguments"></a>

`name` – The name of the TLE extension from which to get upgrade paths\.

## Output<a name="pgtle.extension_update_paths-output"></a>
+ `source` – The source version for an update\.
+ `target` – The target version for an update\.
+ `path` – The upgrade path used to update a TLE extension from `source` version to `target` version, for example, `0.1--0.2`\.

## Usage example<a name="pgtle.extension_update_paths-example"></a>

```
SELECT * FROM pgtle.extension_update_paths('your-TLE);
```