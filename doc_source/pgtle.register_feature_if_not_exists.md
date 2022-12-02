# pgtle\.register\_feature\_if\_not\_exists<a name="pgtle.register_feature_if_not_exists"></a>

The `pgtle.register_feature_if_not_exists` function adds the specified PostgreSQL feature to the `pgtle.feature_info` table and identifies the TLE extension or other procedure or function that uses the feature\. For more information about hooks and Trusted Language Extensions, see [Using PostgreSQL hooks with your TLE extensions](PostgreSQL_trusted_language_extension.md#PostgreSQL_trusted_language_extension.overview.tles-and-hooks)\. 

## Function prototype<a name="pgtle.register_feature_if_not_exists-prototype"></a>

```
pgtle.register_feature_if_not_exists(proc regproc, feature pg_tle_feature)
```

## Role<a name="pgtle.register_feature_if_not_exists-role"></a>

`pgtle_admin` 

## Arguments<a name="pgtle.register_feature_if_not_exists-arguments"></a>
+ `proc` – The name of a stored procedure or function that contains the logic \(code\) to use as a feature for your TLE extension\. For example, the `pw_hook` code\.
+ `feature` – The name of the PostgreSQL feature to register for the TLE function\. Currently, the only available feature is the `passcheck` hook\. For more information, see [Password\-check hook \(passcheck\)](passcheck_hook.md)\. 

## Output<a name="pgtle.register_feature_if_not_exists-output"></a>

Returns `true` after registering the feature for the specified extension\. Returns `false` if the feature is already registered\.

## Usage example<a name="pgtle.register_feature_if_not_exists-example"></a>

```
SELECT pgtle.register_feature_if_not_exists('pw_hook', 'passcheck');
```