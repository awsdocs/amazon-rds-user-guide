# pgtle\.unregister\_feature\_if\_exists<a name="pgtle.unregister_feature_if_exists"></a>

The `unregister_feature` function provides a way to remove functions that were registered to use `pg_tle` features, such as hooks\. For more information, see [Using PostgreSQL hooks with your TLE extensions](PostgreSQL_trusted_language_extension.md#PostgreSQL_trusted_language_extension.overview.tles-and-hooks)\. Returns `true` after successfully unregistering the feature\. Returns `false` if the feature wasn't registered\.

For information about registering `pg_tle` features for your TLE extensions, see [pgtle\.register\_feature](pgtle.register_feature.md)\.

## Function prototype<a name="pgtle.unregister_feature_if_exists-prototype"></a>

```
pgtle.unregister_feature_if_exists(proc regproc, feature pg_tle_features)
```

## Role<a name="pgtle.unregister_feature_if_exists-role"></a>

`pgtle_admin`

## Arguments<a name="pgtle.unregister_feature_if_exists-arguments"></a>
+ `proc` – The name of the stored function that was registered to include a `pg_tle` feature\.
+ `feature` – The name of the `pg_tle` feature that was registered with the trusted language extension\.

## Output<a name="pgtle.unregister_feature_if_exists-output"></a>

Returns `true` or `false`, as follows\.
+ `true` – The function has successfully unregistered the feature from extension\.
+ `false` – The function wasn't able to unregister the feature from the TLE extension\.

## Usage example<a name="pgtle.unregister_feature_if_exists-example"></a>

```
SELECT * FROM pgtle.unregister_feature_if_exists(pw_hook, passcheck);
```