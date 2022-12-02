# Password\-check hook \(passcheck\)<a name="passcheck_hook"></a>

The `passcheck` hook is used to customize PostgreSQL behavior during the password\-checking process for the following SQL commands and `psql` metacommand\.
+ `CREATE ROLE username ...PASSWORD` – For more information, see [CREATE ROLE](https://www.postgresql.org/docs/current/sql-createrole.html) in the PostgreSQL documentation\.
+ `ALTER ROLE username...PASSWORD` – For more information, see [ALTER ROLE](https://www.postgresql.org/docs/current/sql-alterrole.html) in the PostgreSQL documentation\.
+ `\password username` – This interactive `psql` metacommand securely changes the password for the specified user by hashing the password before transparently using the `ALTER ROLE ... PASSWORD` syntax\. The metacommand is a secure wrapper for the `ALTER ROLE ... PASSWORD` command, thus the hook applies to the behavior of the `psql` metacommand\.

For an example, see [Password\-check hook code listing](PostgreSQL_trusted_language_extension.md#PostgreSQL_trusted_language_extension-example-hook_code_listing)\.

## Function prototype<a name="passcheck_hook-prototype"></a>

```
passcheck_hook(username text, password text, password_type pgtle.password_types, valid_until timestamptz, valid_null boolean)
```

## Arguments<a name="passcheck_hook-arguments"></a>

A `passcheck` hook function takes the following arguments\.
+ `username` – The name \(as text\) of the role \(username\) that's setting a password\.
+ `password` – The plaintext or hashed password\. The password entered should match the type specified in `password_type`\.
+ `password_type` – Specify the `pgtle.password_type` format of the password\. This format can be one of the following options\.
  + `PASSWORD_TYPE_PLAINTEXT` – A plaintext password\.
  + `PASSWORD_TYPE_MD5` – A password that's been hashed using MD5 \(message digest 5\) algorithm\.
  + `PASSWORD_TYPE_SCRAM_SHA_256` – A password that's been hashed using SCRAM\-SHA\-256 algorithm\.
+ `valid_until` – Specify the time when the password becomes invalid\. This argument is optional\. If you use this argument, specify the time as a `timestamptz` value\.
+ `valid_null` – If this Boolean is set to `true`, the `valid_until` option is set to `NULL`\.

## Configuration<a name="passcheck_hook-configuration"></a>

The function `pgtle.enable_password_check` controls whether the passcheck hook is active\. The passcheck hook has three possible settings\.
+ `off` – Turns off the `passcheck` password\-check hook\. This is the default value\.
+ `on` – Turns on the `passcode` password\-check hook so that passwords are checked against the table\.
+ `require` – Requires a password check hook to be defined\.

## Usage notes<a name="passcheck_hook-usage"></a>

To turn the `passcheck` hook on or off, you need to modify the custom DB parameter group for your RDS for PostgreSQL DB instance\.

For Linux, macOS, or Unix:

```
aws rds modify-db-parameter-group \
    --region aws-region \
    --db-parameter-group-name your-custom-parameter-group \
    --parameters "ParameterName=pgtle.enable_password_check,ParameterValue=on,ApplyMethod=immediate"
```

For Windows:

```
aws rds modify-db-parameter-group ^
    --region aws-region ^
    --db-parameter-group-name your-custom-parameter-group ^
    --parameters "ParameterName=pgtle.enable_password_check,ParameterValue=on,ApplyMethod=immediate"
```