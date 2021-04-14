# Invoking an AWS Lambda function from an RDS for PostgreSQL DB instance<a name="PostgreSQL-Lambda"></a>

You can invoke AWS Lambda functions from an RDS for PostgreSQL DB instance\. To do this, use the `aws_lambda` PostgreSQL extension provided with RDS for PostgreSQL\. 

AWS Lambda is a compute service that you can use to run code\. For example, you can use Lambda functions to process event notifications from a DB instance\. For more information about Lambda, see [What is AWS Lambda?](https://docs.aws.amazon.com/lambda/latest/dg/welcome.html) in the *AWS Lambda Developer Guide\.*

**Note**  
Invoking an AWS Lambda function is supported in the following RDS for PostgreSQL versions:  
12\.6 and later minor versions
13\.2 and later minor versions

**Topics**
+ [Overview of using a Lambda function](#PostgreSQL-Lambda-overview)
+ [Specifying the Lambda function to use](#PostgreSQL-Lambda-specify-function)
+ [Giving RDS access to Lambda](#PostgreSQL-Lambda-access)
+ [Invoking Lambda functions](#PostgreSQL-Lambda-invoke)
+ [Function reference](#PostgreSQL-Lambda-functions)

## Overview of using a Lambda function<a name="PostgreSQL-Lambda-overview"></a>

You can invoke a Lambda function from an RDS for PostgreSQL database with the following procedure\.

**To invoke a Lambda function from an RDS for PostgreSQL database**

1. Install the required PostgreSQL extensions\. These include the `aws_lambda` and `aws_commons` extensions\. To do so, start psql and run the following commands\.

   ```
   CREATE EXTENSION IF NOT EXISTS aws_lambda CASCADE;
   ```

   The `aws_lambda` extension provides the [aws\_lambda\.invoke](#aws_lambda.invoke) function that you use to invoke functions in Lambda\. The `aws_commons` extension is included to provide additional helper functions\. 

1. Identify the name or Amazon Resource Name \(ARN\) for the Lambda function to use\. For details about this process, see [Specifying the Lambda function to use](#PostgreSQL-Lambda-specify-function)\.

1. Provide permission to access the Lambda function\.

   To invoke a Lambda function, give the RDS for PostgreSQL DB instance permission to access the Lambda invoke API operation\. Doing this includes the following steps:

   1. Create an AWS Identity and Access Management \(IAM\) policy that provides access to a Lambda function that you want to invoke\.

   1. Create an IAM role\.

   1. Attach the IAM policy that you created to the role that you created\.

   1. Add this IAM role to your DB instance\.

   For details about this process, see [Giving RDS access to Lambda](#PostgreSQL-Lambda-access)\.

1. Use the `aws_lambda.invoke` function to run the Lambda function\. For details about this process, see [Invoking Lambda functions](#PostgreSQL-Lambda-invoke)\.

## Specifying the Lambda function to use<a name="PostgreSQL-Lambda-specify-function"></a>

To identify the Lambda function to use, specify the following information:
+ **Function name** – The name of the Lambda function, ARN, version, or alias\. For a listing of possible formats, see [ Lambda function name formats](https://docs.aws.amazon.com/lambda/latest/dg/API_Invoke.html#API_Invoke_RequestParameters)\.
+ **AWS Region** – \(Optional\) The AWS Region where the Lambda function is located\. If you don't specify a Region value and it's not specified in the function ARN, RDS uses the same Region as the DB instance\.

  For a listing of AWS Region names and associated values, see [ Regions, Availability Zones, and Local Zones ](Concepts.RegionsAndAvailabilityZones.md)\.

To hold the Lambda function name information, you can use the [aws\_commons\.create\_lambda\_function\_arn](#aws_commons.create_lambda_function_arn) function\. This function creates an `aws_commons._lambda_function_arn_1` composite structure to store the name information, as shown following\. 

```
psql=> SELECT aws_commons.create_lambda_function_arn(
   'my-function',
   'us-west-2'
) AS aws_lambda_arn_1 \gset

psql=> SELECT aws_commons.create_lambda_function_arn(
   '123456789012:function:my-function',
   'us-west-2'
) AS lambda_partial_arn_1 \gset

psql=> SELECT aws_commons.create_lambda_function_arn(
   'arn:aws:lambda:us-west-2:123456789012:function:my-function'
) AS lambda_arn_1 \gset
```

You can later provide any of these values as a parameter in calls to the [aws\_lambda\.invoke](#aws_lambda.invoke) function\. For examples, see [Invoking Lambda functions](#PostgreSQL-Lambda-invoke)\.

## Giving RDS access to Lambda<a name="PostgreSQL-Lambda-access"></a>

To use a Lambda function, give your PostgreSQL DB instance permission to access Lambda\. To do this, use the following procedure\.

**To give a PostgreSQL DB instance access to Lambda**

1. Create an IAM policy\. 

   This policy provides the permissions that allow your PostgreSQL DB instance to invoke Lambda functions\.

   As part of creating this policy, take the following steps:

   1. Include in the policy the required action `lambda:InvokeFunction` to allow Lambda invocation from your RDS for PostgreSQL DB instance\.

   1. Include the Amazon Resource Name \(ARN\) that identifies the Lambda function\. The ARN format for accessing Lambda is: `arn:aws:lambda:::function:example_function/*`

   For more information on creating an IAM policy for RDS for PostgreSQL, see [ Creating and using an IAM policy for IAM database access ](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/UsingWithRDS.IAMDBAuth.IAMPolicy.html)\. See also [ IAM Tutorial: Create and attach your first customer managed policy ](https://docs.aws.amazon.com/IAM/latest/UserGuide/tutorial_managed-policies.html) in the *IAM User Guide\.*

   The following AWS CLI command creates an IAM policy named `rds-lambda-policy` with these options\. It grants access to a function named `example_function`\.

   ```
   aws iam create-policy  --policy-name rds-lambda-policy  --policy-document '{
       "Version": "2012-10-17",
       "Statement": [
           {
               "Sid": "AllowAccessToExampleFunction",
               "Effect": "Allow",
               "Action": "lambda:InvokeFunction",
               "Resource": "arn:aws:lambda:<region>:<123456789012>:function:example_function"
           }
       ]
   }'
   ```

   After you create the policy, note the ARN of the policy\. You need the ARN for a subsequent step when you attach the policy to an IAM role\.

1. Create an IAM role\.

   You do this so that RDS for PostgreSQL can assume this IAM role on your behalf to access your Lambda function\. For more information, see [Creating a role to delegate permissions to an IAM user ](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-user.html) in the *IAM User Guide\.*

   The following example shows using the AWS CLI command to create a role named `rds-lambda-role`\.

   ```
   aws iam create-role  --role-name rds-lambda-role --assume-role-policy-document '{
       "Version": "2012-10-17",
       "Statement": [
           {
           "Effect": "Allow",
           "Principal": {
               "Service": "rds.amazonaws.com"
           },
           "Action": "sts:AssumeRole"
           }
       ] 
   }'
   ```

1. Attach the IAM policy that you created to the IAM role that you created\.

   The following AWS CLI command attaches the policy created earlier to the role named `rds-lambda-role`\. Replace `your-policy-arn` with the policy ARN that you noted in an earlier step\.

   ```
   aws iam attach-role-policy  --policy-arn your-policy-arn  --role-name rds-lambda-role
   ```

1. Add the IAM role to the DB instance\. You do so by using the AWS CLI, as described following\. 

   Use the following CLI command to add the IAM role to the RDS for PostgreSQL DB instance named `my-db-instance`\. Replace *`your-role-arn`* with the role ARN that you noted in a previous step\. Use `Lambda` for the value of the `--feature-name` option, as shown following\.

   ```
   aws rds add-role-to-db-instance \
   --db-instance-identifier my-db-instance \
   --feature-name Lambda \
   --role-arn your-role-arn   \
   --region your-region
   ```

## Invoking Lambda functions<a name="PostgreSQL-Lambda-invoke"></a>

Following, you can find some examples of calling the [aws\_lambda\.invoke](#aws_lambda.invoke) function\. Before you use the `aws_lambda.invoke` function, be sure to complete the following prerequisites:
+ Install the required PostgreSQL extensions as described in [Overview of using a Lambda function](#PostgreSQL-Lambda-overview)\.
+ Determine which Lambda function to invoke as described in [Specifying the Lambda function to use](#PostgreSQL-Lambda-specify-function)\.
+ Make sure that the DB instance has invoke access to Lambda as described in [Giving RDS access to Lambda](#PostgreSQL-Lambda-access)\.

You can invoke a Lambda function synchronously or asynchronously\. You control this with the following values for the [aws\_lambda\.invoke](#aws_lambda.invoke) function's `invocation_type` parameter:
+ The `RequestResponse` type of invocation for a Lambda function is synchronous and returns a response payload in the result of the `aws_lambda.invoke` function\. Use the `RequestResponse` invocation type when your workflow depends on receiving the Lambda function result immediately\. Most of the following examples use synchronous invocation\. 

  The `RequestResponse` type of invocation is the default\. 
+ The `Event` type of invocation for a Lambda function is asynchronous and returns immediately without a returned payload\. Use the `Event` type of invocation when you don't need to know the result of the Lambda function before your workflow moves on\. For an example of asynchronous invocation, see [Asynchronous event invocation of Lambda functions](#PostgreSQL-Lambda-Event)\.

The following [aws\_lambda\.invoke](#aws_lambda.invoke) examples use a `aws_lambda_arn_1` structure, which contains the identifying information for the Lambda function\. To create the structure, use the [aws\_commons\.create\_lambda\_function\_arn](#aws_commons.create_lambda_function_arn) function\. For an example of using the `aws_commons.create_lambda_function_arn` function, see [Specifying the Lambda function to use](#PostgreSQL-Lambda-specify-function)\.

**Topics**
+ [Synchronous RequestResponse invocation of Lambda functions](#PostgreSQL-Lambda-RequestResponse)
+ [Asynchronous event invocation of Lambda functions](#PostgreSQL-Lambda-Event)
+ [Requesting a Lambda execution log in a function response](#PostgreSQL-Lambda-log-response)
+ [Including client context in a Lambda function](#PostgreSQL-Lambda-client-context)
+ [Invoking a specific version of a Lambda function](#PostgreSQL-Lambda-function-version)
+ [Lambda function error handling](#PostgreSQL-Lambda-errors)

### Synchronous RequestResponse invocation of Lambda functions<a name="PostgreSQL-Lambda-RequestResponse"></a>

Following is an example of a synchronous Lambda function invocation\. The following two `aws_lambda.invoke` function call results are the same\.

```
psql=> SELECT * FROM aws_lambda.invoke(:'aws_lambda_arn_1', '{"body": "Hello from Postgres!"}'::json);
psql=> SELECT * FROM aws_lambda.invoke(:'aws_lambda_arn_1', '{"body": "Hello from Postgres!"}'::json, 'RequestResponse');
```

The parameters are described as follows:
+ `:'aws_lambda_arn_1'` – This parameter is a structure that identifies the Lambda function to call\. This example uses a variable to identify the previously created structure\. You can instead create the structure by including the [aws\_commons\.create\_lambda\_function\_arn](#aws_commons.create_lambda_function_arn) function call inline within the [aws\_lambda\.invoke](#aws_lambda.invoke) function call as follows\.

  ```
  psql=> SELECT * FROM aws_lambda.invoke(aws_commons.create_lambda_function_arn('my-function', 'us-west-2'),
  '{"body": "Hello from Postgres!"}'::json
  );
  ```
+ `'{"body": "Hello from PostgreSQL!"}'::json` – The JSON payload to pass to the Lambda function\.
+ `'RequestResponse'` – The Lambda invocation type\.

### Asynchronous event invocation of Lambda functions<a name="PostgreSQL-Lambda-Event"></a>

Following is an example of an asynchronous Lambda function invocation\. The `Event` invocation type schedules the Lambda function invocation with the specified input payload and returns immediately\. Use the `Event` invocation type in certain workflows that don't depend on the results of the Lambda function\.

```
psql=> SELECT * FROM aws_lambda.invoke(:'aws_lambda_arn_1', '{"body": "Hello from Postgres!"}'::json, 'Event');
```

### Requesting a Lambda execution log in a function response<a name="PostgreSQL-Lambda-log-response"></a>

You can request to include the last 4 KB of the execution log in the function response, as shown following\.

```
psql=> SELECT *, select convert_from(decode(log_result, 'base64'), 'utf-8') as log FROM aws_lambda.invoke(:'aws_lambda_arn_1', '{"body": "Hello from Postgres!"}'::json, 'RequestResponse', 'Tail');
```

Set the [aws\_lambda\.invoke](#aws_lambda.invoke) function's `log_type` parameter to `Tail` to include the execution log in the response\. The default value for the `log_type` parameter is `None`\.

The `log_result` that's returned is a `base64` encoded string\. You can decode the contents using a combination of the `decode` and `convert_from` PostgreSQL functions\.

### Including client context in a Lambda function<a name="PostgreSQL-Lambda-client-context"></a>

You can pass in client context information that is separate from the payload, as shown following\.

```
psql=> SELECT *, convert_from(decode(log_result, 'base64'), 'utf-8') as log FROM aws_lambda.invoke(:'aws_lambda_arn_1', '{"body": "Hello from Postgres!"}'::json, 'RequestResponse', 'Tail');
```

To include client context, use a JSON object for the [aws\_lambda\.invoke](#aws_lambda.invoke) function's `context` parameter\.

### Invoking a specific version of a Lambda function<a name="PostgreSQL-Lambda-function-version"></a>

For an example of invoking a specific version of a Lambda function, see the following\.

```
psql=> SELECT * FROM aws_lambda.invoke(:'aws_lambda_arn_1', '{"body": "Hello from Postgres!"}'::json, 'RequestResponse', 'None', NULL, 'custom_version');
```

To identify a Lambda function's version, use the [aws\_lambda\.invoke](#aws_lambda.invoke) function's `qualifier` parameter\. In this example, `'custom_version'` is an alias or version that identifies the version of the function to invoke\.

You can instead supply a Lambda function qualifier with the function name information as follows\.

```
psql=> SELECT * FROM aws_lambda.invoke(aws_commons.create_lambda_function_arn('my-function:custom_version', 'us-west-2'),
'{"body": "Hello from Postgres!"}'::json
);
```

### Lambda function error handling<a name="PostgreSQL-Lambda-errors"></a>

If a Lambda function throws an exception during request processing, `aws_lambda.invoke` fails with a PostgreSQL error such as the following\.

```
psql=> SELECT * FROM aws_lambda.invoke(:'aws_lambda_arn_1', '{"body": "Hello from Postgres!"}'::json);
ERROR:  lambda invocation failed
DETAIL:  "arn:aws:lambda:us-west-2:123456789012:function:my-function" returned error "Unhandled", details: "<Error details string>".
```

## Function reference<a name="PostgreSQL-Lambda-functions"></a>

Following is the reference for the functions to use for invoking Lambda functions with RDS for PostgreSQL\.

**Topics**
+ [aws\_lambda\.invoke](#aws_lambda.invoke)
+ [aws\_commons\.create\_lambda\_function\_arn](#aws_commons.create_lambda_function_arn)

### aws\_lambda\.invoke<a name="aws_lambda.invoke"></a>

Runs a Lambda function for an RDS for PostgreSQL DB instance\.

For more details about invoking Lambda functions, see also [Invoke](https://docs.aws.amazon.com/lambda/latest/dg/API_Invoke.html) in the *AWS Lambda Developer Guide\.*

**Syntax**

------
#### [ JSON ]

```
aws_lambda.invoke(
IN function_name TEXT,
IN payload JSON,
IN region TEXT DEFAULT NULL,
IN invocation_type TEXT DEFAULT 'RequestResponse',
IN log_type TEXT DEFAULT 'None',
IN context JSON DEFAULT NULL,
IN qualifier VARCHAR(128) DEFAULT NULL,
OUT status_code INT,
OUT payload JSON,
OUT executed_version TEXT,
OUT log_result TEXT)
```

```
aws_lambda.invoke(
IN function_name aws_commons._lambda_function_arn_1,
IN payload JSON,
IN invocation_type TEXT DEFAULT 'RequestResponse',
IN log_type TEXT DEFAULT 'None',
IN context JSON DEFAULT NULL,
IN qualifier VARCHAR(128) DEFAULT NULL,
OUT status_code INT,
OUT payload JSON,
OUT executed_version TEXT,
OUT log_result TEXT)
```

------
#### [ JSONB ]

```
aws_lambda.invoke(
IN function_name TEXT,
IN payload JSONB,
IN region TEXT DEFAULT NULL,
IN invocation_type TEXT DEFAULT 'RequestResponse',
IN log_type TEXT DEFAULT 'None',
IN context JSONB DEFAULT NULL,
IN qualifier VARCHAR(128) DEFAULT NULL,
OUT status_code INT,
OUT payload JSONB,
OUT executed_version TEXT,
OUT log_result TEXT)
```

```
aws_lambda.invoke(
IN function_name aws_commons._lambda_function_arn_1,
IN payload JSONB,
IN invocation_type TEXT DEFAULT 'RequestResponse',
IN log_type TEXT DEFAULT 'None',
IN context JSONB DEFAULT NULL,
IN qualifier VARCHAR(128) DEFAULT NULL,
OUT status_code INT,
OUT payload JSONB,
OUT executed_version TEXT,
OUT log_result TEXT
)
```

------Input parameters

**function\_name**  
The identifying name of the Lambda function\. The value can be the function name, an ARN, or a partial ARN\. For a listing of possible formats, see [ Lambda function name formats](https://docs.aws.amazon.com/lambda/latest/dg/API_Invoke.html#API_Invoke_RequestParameters) in the *AWS Lambda Developer Guide\.*

*payload*  
The input for the Lambda function\. The format can be JSON or JSONB\. For more information, see [JSON Types](https://www.postgresql.org/docs/current/datatype-json.html) in the PostgreSQL documentation\.

*region*  
\(Optional\) The Lambda Region for the function\. By default, RDS resolves the AWS Region from the full ARN in the `function_name` or it uses the RDS for PostgreSQL DB instance Region\. If this Region value conflicts with the one provided in the `function_name` ARN, an error is raised\.

*invocation\_type*  
The invocation type of the Lambda function\. The value is case\-sensitive\. Possible values include the following:  
+ `RequestResponse` – The default\. This type of invocation for a Lambda function is synchronous and returns a response payload in the result\. Use the `RequestResponse` invocation type when your workflow depends on receiving the Lambda function result immediately\. 
+ `Event` – This type of invocation for a Lambda function is asynchronous and returns immediately without a returned payload\. Use the `Event` invocation type when you don't need results of the Lambda function before your workflow moves on\.
+ `DryRun` – This type of invocation tests access without running the Lambda function\. 

*log\_type*  
The type of Lambda log to return in the `log_result` output parameter\. The value is case\-sensitive\. Possible values include the following:  
+ Tail – The returned `log_result` output parameter will include the last 4 KB of the execution log\. 
+ None – No Lambda log information is returned\.

*context*  
Client context in JSON or JSONB format\. Fields to use include than `custom` and `env`\.

*qualifier*  
A qualifier that identifies a Lambda function's version to be invoked\. If this value conflicts with one provided in the `function_name` ARN, an error is raised\.Output parameters

*status\_code*  
An HTTP status response code\. For more information, see [Lambda Invoke response elements](https://docs.aws.amazon.com/lambda/latest/dg/API_Invoke.html#API_Invoke_ResponseElements) in the *AWS Lambda Developer Guide\.*

*payload*  
The information returned from the Lambda function that ran\. The format is in JSON or JSONB\.

*executed\_version*  
The version of the Lambda function that ran\.

*log\_result*  
The execution log information returned if the `log_type` value is `Tail` when the Lambda function was invoked\. The result contains the last 4 KB of the execution log encoded in Base64\.

### aws\_commons\.create\_lambda\_function\_arn<a name="aws_commons.create_lambda_function_arn"></a>

Creates an `aws_commons._lambda_function_arn_1` structure to hold Lambda function name information\. You can use the results of the `aws_commons.create_lambda_function_arn` function in the `function_name` parameter of the aws\_lambda\.invoke [aws\_lambda\.invoke](#aws_lambda.invoke) function\. 

**Syntax**

```
aws_commons.create_lambda_function_arn(
    function_name TEXT,
    region TEXT DEFAULT NULL
    )
    RETURNS aws_commons._lambda_function_arn_1
```Input parameters

*function\_name*  
A required text string containing the Lambda function name\. The value can be a function name, a partial ARN, or a full ARN\.

*region*  
An optional text string containing the AWS Region that the Lambda function is in\. For a listing of Region names and associated values, see [ Regions, Availability Zones, and Local Zones ](Concepts.RegionsAndAvailabilityZones.md)\.