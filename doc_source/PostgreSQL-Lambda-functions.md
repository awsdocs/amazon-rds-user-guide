# AWS Lambda function reference<a name="PostgreSQL-Lambda-functions"></a>

Following is the reference for the functions to use for invoking Lambda functions with RDS for PostgreSQL\.

**Topics**
+ [aws\_lambda\.invoke](#aws_lambda.invoke)
+ [aws\_commons\.create\_lambda\_function\_arn](#aws_commons.create_lambda_function_arn)

## aws\_lambda\.invoke<a name="aws_lambda.invoke"></a>

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

## aws\_commons\.create\_lambda\_function\_arn<a name="aws_commons.create_lambda_function_arn"></a>

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
An optional text string containing the AWS Region that the Lambda function is in\. For a listing of Region names and associated values, see [Regions, Availability Zones, and Local Zones](Concepts.RegionsAndAvailabilityZones.md)\.