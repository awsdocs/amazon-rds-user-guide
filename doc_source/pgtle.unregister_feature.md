# pgtle\.unregister\_feature<a name="pgtle.unregister_feature"></a>

The `unregister_feature` function provides a way to remove functions that were registered to use `pg_tle` features, such as hooks\. For information about registering a feature, see [pgtle\.register\_feature](pgtle.register_feature.md)\.

## Function prototype<a name="pgtle.unregister_feature-prototype"></a>

```
pgtle.unregister_feature(proc regproc, feature pg_tle_features)
```

## Role<a name="pgtle.unregister_feature-role"></a>

`pgtle_admin`

## Arguments<a name="pgtle.unregister_feature-arguments"></a>
+ `proc` – The name of a stored function to register with a `pg_tle` feature\.
+ `feature` – The name of the `pg_tle` feature to register with the function\. For example, `passcheck` is a feature that can be registered for use by the trusted language extensions that you develop\. For more information, see [Password\-check hook \(passcheck\)](passcheck_hook.md)\. 

## Output<a name="pgtle.unregister_feature-output"></a>

None\.

## Usage example<a name="pgtle.unregister_feature-example"></a>

```
SELECT * FROM pgtle.unregister_feature(pw_hook, passcheck);
```