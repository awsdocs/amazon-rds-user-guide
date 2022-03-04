# RDS for Oracle parameters<a name="Oracle.Concepts.FeatureSupport.Parameters"></a>

In Amazon RDS, you manage parameters using parameter groups\. For more information, see [Working with parameter groups](USER_WorkingWithParamGroups.md)\. To view the supported parameters for a specific Oracle Database edition and version, run the AWS CLI [https://docs.aws.amazon.com/cli/latest/reference/rds/describe-engine-default-parameters.html](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-engine-default-parameters.html) command\.

For example, to view the supported parameters for the Enterprise Edition of Oracle Database 12c Release 2 \(12\.2\), run the following command\.

```
aws rds describe-engine-default-parameters \
    --db-parameter-group-family oracle-ee-12.2
```