# pgtle\.register\_feature<a name="pgtle.register_feature"></a>

The `register_feature` function adds the specified internal PostgreSQL feature to the `pgtle.feature_info` table\. PostgreSQL hooks are an example of an internal PostgreSQL feature\. The Trusted Language Extensions development kit supports the use of PostgreSQL hooks\. Currently, this function supports the following feature\.
+ `passcheck` – Registers the password\-check hook with your procedure or function that customizes PostgreSQL's password\-check behavior\.

## Function prototype<a name="pgtle.register_feature-prototype"></a>

```
pgtle.register_feature(proc regproc, feature pg_tle_feature)
```

## Role<a name="pgtle.register_feature-role"></a>

`pgtle_admin` 

## Arguments<a name="pgtle.register_feature-arguments"></a>
+ `proc` – The name of a stored procedure or function to use for the feature\.
+ `feature` – The name of the `pg_tle` feature \(such as `passcheck`\) to register with the function\.

## Output<a name="pgtle.register_feature-output"></a>

None\.

## Usage example<a name="pgtle.register_feature-example"></a>

```
SELECT pgtle.register_feature('pw_hook', 'passcheck');
```