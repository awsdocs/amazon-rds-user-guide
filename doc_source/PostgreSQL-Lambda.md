# Invoking an AWS Lambda function from an RDS for PostgreSQL DB instance<a name="PostgreSQL-Lambda"></a>

AWS Lambda is an event\-driven compute service that lets you run code without provisioning or managing servers\. It's available for use with many AWS services, including RDS for PostgreSQL\. For example, you can use Lambda functions to process event notifications from a database, or to load data from files whenever a new file is uploaded to Amazon S3\. To learn more about Lambda, see [What is AWS Lambda?](https://docs.aws.amazon.com/lambda/latest/dg/welcome.html) in the *AWS Lambda Developer Guide\.* 

**Note**  
Invoking an AWS Lambda function is supported in these RDS for PostgreSQL versions:  
14\.1 and higher minor versions
13\.2 and higher minor versions
12\.6 and higher minor versions

Setting up RDS for PostgreSQL to work with Lambda functions is a multi\-step process involving AWS Lambda, IAM, your VPC, and your RDS for PostgreSQL DB instance\. Following, you can find summaries of the necessary steps\. 

For more information about Lambda functions, see [Getting started with Lambda](https://docs.aws.amazon.com/lambda/latest/dg/getting-started.html) and [AWS Lambda foundations](https://docs.aws.amazon.com/lambda/latest/dg/lambda-foundation.html) in the *AWS Lambda Developer Guide*\. 

**Topics**
+ [Step 1: Configure your RDS for PostgreSQL DB instance for outbound connections to AWS Lambda](#PostgreSQL-Lambda-network)
+ [Step 2: Configure IAM for your RDS for PostgreSQL DB instance and AWS Lambda](#PostgreSQL-Lambda-access)
+ [Step 3: Install the `aws_lambda` extension for an RDS for PostgreSQL DB instance](#PostgreSQL-Lambda-install-extension)
+ [Step 4: Use Lambda helper functions with your RDS for PostgreSQL DB instance \(Optional\)](#PostgreSQL-Lambda-specify-function)
+ [Step 5: Invoke a Lambda function from your RDS for PostgreSQL DB instance](#PostgreSQL-Lambda-invoke)
+ [Step 6: Grant other users permission to invoke Lambda functions](#PostgreSQL-Lambda-grant-users-permissions)
+ [Examples: Invoking Lambda functions from your RDS for PostgreSQL DB instance](#PostgreSQL-Lambda-examples)
+ [Lambda function error messages](#PostgreSQL-Lambda-errors)
+ [AWS Lambda function reference](PostgreSQL-Lambda-functions.md)

## Step 1: Configure your RDS for PostgreSQL DB instance for outbound connections to AWS Lambda<a name="PostgreSQL-Lambda-network"></a>

Lambda functions always run inside an Amazon VPC that's owned by the AWS Lambda service\. Lambda applies network access and security rules to this VPC and it maintains and monitors the VPC automatically\. Your RDS for PostgreSQL DB instance sends network traffic to the Lambda service's VPC\. How you configure this depends on whether your DB instance is public or private\.
+ **Public RDS for PostgreSQL DB instance** – A DB instance is public if it's located in a public subnet on your VPC, and if the instance's "PubliclyAccessible" property is `true`\. To find the value of this property, you can use the [describe\-db\-instances](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-instances.html) AWS CLI command\. Or, you can use the AWS Management Console to open the **Connectivity & security** tab and check that **Publicly accessible** is **Yes**\. To verify that the instance is in the public subnet of your VPC, you can use the AWS Management Console or the AWS CLI\. 

  To set up access to Lambda, you use the AWS Management Console or the AWS CLI to create an outbound rule on your VPC's security group\. The outbound rule specifies that TCP can use port 443 to send packets to any IPv4 addresses \(0\.0\.0\.0/0\)\.
+ **Private RDS for PostgreSQL DB instance** – In this case, the instance's "PubliclyAccessible" property is `false` or it's in a private subnet\. To allow the instance to work with Lambda, you can use a Network Address Translation\) NAT gateway\. For more information, see [NAT gateways](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html)\. Or, you can configure your VPC with a VPC endpoint for Lambda\. For more information, see [VPC endpoints](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-endpoints.html) in the *Amazon VPC User Guide*\. The endpoint responds to calls made by your RDS for PostgreSQL DB instance to your Lambda functions\. The VPC endpoint uses its own private DNS resolution\. RDS for PostgreSQL can't use the Lambda VPC endpoint until you change the value of the `rds.custom_dns_resolution` from its default value of 0 \(not enabled\) to 1\. To do so:
  + Create a custom DB parameter group\.
  + Change the value of the `rds.custom_dns_resolution` parameter from its default of `0` to `1`\. 
  + Modify your DB instance to use your custom DB parameter group\.
  + Reboot the instance to have the modified parameter take effect\.

Your VPC can now interact with the AWS Lambda VPC at the network level\. Next, you configure the permissions using IAM\. 

## Step 2: Configure IAM for your RDS for PostgreSQL DB instance and AWS Lambda<a name="PostgreSQL-Lambda-access"></a>

Invoking Lambda functions from your RDS for PostgreSQL DB instance requires certain privileges\. To configure the necessary privileges, we recommend that you create an IAM policy that allows invoking Lambda functions, assign that policy to a role, and then apply the role to your DB instance\. This approach gives the DB instance privileges to invoke the specified Lambda function on your behalf\. The following steps show you how to do this using the AWS CLI\.

**To configure IAM permissions for using your Amazon RDS instance with Lambda**

1. Use the [create\-policy](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/iam/create-policy.html) AWS CLI command to create an IAM policy that allows your RDS for PostgreSQL DB instance to invoke the specified Lambda function\. \(The statement ID \(Sid\) is an optional description for your policy statement and has no effect on usage\.\) This policy gives your  DB instance the minimum permissions needed to invoke the specified Lambda function\. 

   ```
   aws iam create-policy  --policy-name rds-lambda-policy --policy-document '{
       "Version": "2012-10-17",
       "Statement": [
           {
           "Sid": "AllowAccessToExampleFunction",
           "Effect": "Allow",
           "Action": "lambda:InvokeFunction",
           "Resource": "arn:aws:lambda:aws-region:444455556666:function:my-function"
           }
       ]
   }'
   ```

   Alternatively, you can use the predefined `AWSLambdaRole` policy that allows you to invoke any of your Lambda functions\. For more information, see [Identity\-based IAM policies for Lambda](https://docs.aws.amazon.com/lambda/latest/dg/access-control-identity-based.html#access-policy-examples-aws-managed) 

1. Use the [create\-role](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/iam/create-role.html) AWS CLI command to create an IAM role that the policy can assume at runtime\.

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

1. Apply the policy to the role by using the [attach\-role\-policy](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/iam/attach-role-policy.html) AWS CLI command\.

   ```
   aws iam attach-role-policy \
       --policy-arn arn:aws:iam::444455556666:policy/rds-lambda-policy \
       --role-name rds-lambda-role --region aws-region
   ```

1. Apply the role to your RDS for PostgreSQL DB instance by using the  [add\-role\-to\-db\-instance](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/rds/add-role-to-db-instance.html) AWS CLI command\. This last step allows your DB instance's database users to invoke Lambda functions\. 

   ```
   aws rds add-role-to-db-instance \
          --db-cluster-identifier my-cluster-name \
          --feature-name Lambda \
          --role-arn  arn:aws:iam::444455556666:role/rds-lambda-role   \
          --region aws-region
   ```

With the VPC and the IAM configurations complete, you can now install the `aws_lambda` extension\. \(Note that you can install the extension at any time, but until you set up the correct VPC support and IAM privileges, the `aws_lambda` extension adds nothing to your RDS for PostgreSQL DB instance's capabilities\.\)

## Step 3: Install the `aws_lambda` extension for an RDS for PostgreSQL DB instance<a name="PostgreSQL-Lambda-install-extension"></a>

To use AWS Lambda with your RDS for PostgreSQL DB instance, the `aws_lambda` PostgreSQL extension to your RDS for PostgreSQL\. This extension provides your RDS for PostgreSQL DB instance with the ability to call Lambda functions from PostgreSQL\. 

**To install the `aws_lambda` extension in your RDS for PostgreSQL DB instance**

Use the PostgreSQL `psql` command\-line or the pgAdmin tool to connect to your RDS for PostgreSQL DB instance\. 

1. Connect to your RDS for PostgreSQL DB instance as a user with `rds_superuser` privileges\. The default `postgres` user is shown in the example\.

   ```
   psql -h instance.444455556666.aws-region.rds.amazonaws.com -U postgres -p 5432
   ```

1. Install the `aws_lambda` extension\. The `aws_commons` extension is also required\. It provides helper functions to `aws_lambda` and many other Aurora extensions for PostgreSQL\. If it's not already on your RDS for PostgreSQLDB instance, it's installed with `aws_lambda` as shown following\. 

   ```
   CREATE EXTENSION IF NOT EXISTS aws_lambda CASCADE;
   NOTICE:  installing required extension "aws_commons"
   CREATE EXTENSION
   ```

The `aws_lambda` extension is installed in your DB instance\. You can now create convenience structures for invoking your Lambda functions\. 

## Step 4: Use Lambda helper functions with your RDS for PostgreSQL DB instance \(Optional\)<a name="PostgreSQL-Lambda-specify-function"></a>

You can use the helper functions in the `aws_commons` extension to prepare entities that you can more easily invoke from PostgreSQL\. To do this, you need to have the following information about your Lambda functions:
+ **Function name** – The name, Amazon Resource Name \(ARN\), version, or alias of the Lambda function\. The IAM policy created in [Step 2: Configure IAM for your instance and Lambda](#PostgreSQL-Lambda-access) requires the ARN, so we recommend that you use your function's ARN\.
+ **AWS Region** – \(Optional\) The AWS Region where the Lambda function is located if it's not in the same Region as your RDS for PostgreSQL DB instance\.

To hold the Lambda function name information, you use the [aws\_commons\.create\_lambda\_function\_arn](PostgreSQL-Lambda-functions.md#aws_commons.create_lambda_function_arn) function\. This helper function creates an `aws_commons._lambda_function_arn_1` composite structure with the details needed by the invoke function\. Following, you can find three alternative approaches to setting up this composite structure\.

```
SELECT aws_commons.create_lambda_function_arn(
   'my-function',
   'aws-region'
) AS aws_lambda_arn_1 \gset
```

```
SELECT aws_commons.create_lambda_function_arn(
   '111122223333:function:my-function',
   'aws-region'
) AS lambda_partial_arn_1 \gset
```

```
SELECT aws_commons.create_lambda_function_arn(
   'arn:aws:lambda:aws-region:111122223333:function:my-function'
) AS lambda_arn_1 \gset
```

Any of these values can be used in calls to the [aws\_lambda\.invoke](PostgreSQL-Lambda-functions.md#aws_lambda.invoke) function\. For examples, see [Step 5: Invoke a Lambda function from your RDS for PostgreSQL DB instance](#PostgreSQL-Lambda-invoke)\.

## Step 5: Invoke a Lambda function from your RDS for PostgreSQL DB instance<a name="PostgreSQL-Lambda-invoke"></a>

The `aws_lambda.invoke` function behaves synchronously or asynchronously, depending on the `invocation_type`\. The two alternatives for this parameter are `RequestResponse` \(the default\) and `Event`, as follows\. 
+ **`RequestResponse`** – This invocation type is *synchronous*\. It's the default behavior when the call is made without specifying an invocation type\. The response payload includes the results of the `aws_lambda.invoke` function\. Use this invocation type when your workflow requires receiving results from the Lambda function before proceeding\. 
+ **`Event`** – This invocation type is *asynchronous*\. The response doesn't include a payload containing results\. Use this invocation type when your workflow doesn't need a result from the Lambda function to continue processing\.

As a simple test of your setup, you can connect to your DB instance using `psql` and invoke an example function from the command line\. Suppose that you have one of the basic functions set up on your Lambda service, such as the simple Python function shown in the following screenshot\.

![\[Example Lambda function shown in the AWS CLI for AWS Lambda\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/lambda_simple_function.png)

**To invoke an example function**

1. Connect to your DB instance using `psql` or pgAdmin\.

   ```
   psql -h instance.444455556666.aws-region.rds.amazonaws.com -U postgres -p 5432
   ```

1. Invoke the function using its ARN\.

   ```
   SELECT * from aws_lambda.invoke(aws_commons.create_lambda_function_arn('arn:aws:lambda:aws-region:444455556666:function:simple', 'us-west-1'), '{"body": "Hello from Postgres!"}'::json );
   ```

   The response looks as follows\.

   ```
   status_code |                        payload                        | executed_version | log_result
   -------------+-------------------------------------------------------+------------------+------------
            200 | {"statusCode": 200, "body": "\"Hello from Lambda!\""} | $LATEST          |
   (1 row)
   ```

If your invocation attempt doesn't succeed, see [Lambda function error messages ](#PostgreSQL-Lambda-errors)\. 

## Step 6: Grant other users permission to invoke Lambda functions<a name="PostgreSQL-Lambda-grant-users-permissions"></a>

At this point in the procedures, only you as `rds_superuser` can invoke your Lambda functions\. To allow other users to invoke any functions that you create, you need to grant them permission\. 

**To grant others permission to invoke Lambda functions**

1. Connect to your DB instance using `psql` or pgAdmin\.

   ```
   psql -h instance.444455556666.aws-region.rds.amazonaws.com -U postgres -p 5432
   ```

1. Run the following SQL commands:

   ```
   postgres=>  GRANT USAGE ON SCHEMA aws_lambda TO db_username;
   GRANT EXECUTE ON ALL FUNCTIONS IN SCHEMA aws_lambda TO db_username;
   ```

## Examples: Invoking Lambda functions from your RDS for PostgreSQL DB instance<a name="PostgreSQL-Lambda-examples"></a>

Following, you can find several examples of calling the [aws\_lambda\.invoke](PostgreSQL-Lambda-functions.md#aws_lambda.invoke) function\. Most all the examples use the composite structure `aws_lambda_arn_1` that you create in [Step 4: Use Lambda helper functions with your RDS for PostgreSQL DB instance \(Optional\)](#PostgreSQL-Lambda-specify-function) to simplify passing the function details\. For an example of asynchronous invocation, see [Example: Asynchronous \(Event\) invocation of Lambda functions](#PostgreSQL-Lambda-Event)\. All the other examples listed use synchronous invocation\. 

To learn more about Lambda invocation types, see [Invoking Lambda functions](https://docs.aws.amazon.com/lambda/latest/dg/lambda-invocation.html) in the *AWS Lambda Developer Guide*\. For more information about `aws_lambda_arn_1`, see [aws\_commons\.create\_lambda\_function\_arn](PostgreSQL-Lambda-functions.md#aws_commons.create_lambda_function_arn)\. 

**Topics**
+ [Example: Synchronous \(RequestResponse\) invocation of Lambda functions](#PostgreSQL-Lambda-RequestResponse)
+ [Example: Asynchronous \(Event\) invocation of Lambda functions](#PostgreSQL-Lambda-Event)
+ [Example: Capturing the Lambda execution log in a function response](#PostgreSQL-Lambda-log-response)
+ [Example: Including client context in a Lambda function](#PostgreSQL-Lambda-client-context)
+ [Example: Invoking a specific version of a Lambda function](#PostgreSQL-Lambda-function-version)

### Example: Synchronous \(RequestResponse\) invocation of Lambda functions<a name="PostgreSQL-Lambda-RequestResponse"></a>

Following are two examples of a synchronous Lambda function invocation\. The results of these `aws_lambda.invoke` function calls are the same\.

```
SELECT * FROM aws_lambda.invoke(:'aws_lambda_arn_1', '{"body": "Hello from Postgres!"}'::json);
```

```
SELECT * FROM aws_lambda.invoke(:'aws_lambda_arn_1', '{"body": "Hello from Postgres!"}'::json, 'RequestResponse');
```

The parameters are described as follows:
+ `:'aws_lambda_arn_1'` – This parameter identifies the composite structure created in [Step 4: Use Lambda helper functions with your RDS for PostgreSQL DB instance \(Optional\)](#PostgreSQL-Lambda-specify-function), with the `aws_commons.create_lambda_function_arn` helper function\. You can also create this structure inline within your `aws_lambda.invoke` call as follows\. 

  ```
  SELECT * FROM aws_lambda.invoke(aws_commons.create_lambda_function_arn('my-function', 'aws-region'),
  '{"body": "Hello from Postgres!"}'::json
  );
  ```
+ `'{"body": "Hello from PostgreSQL!"}'::json` – The JSON payload to pass to the Lambda function\.
+ `'RequestResponse'` – The Lambda invocation type\.

### Example: Asynchronous \(Event\) invocation of Lambda functions<a name="PostgreSQL-Lambda-Event"></a>

Following is an example of an asynchronous Lambda function invocation\. The `Event` invocation type schedules the Lambda function invocation with the specified input payload and returns immediately\. Use the `Event` invocation type in certain workflows that don't depend on the results of the Lambda function\.

```
SELECT * FROM aws_lambda.invoke(:'aws_lambda_arn_1', '{"body": "Hello from Postgres!"}'::json, 'Event');
```

### Example: Capturing the Lambda execution log in a function response<a name="PostgreSQL-Lambda-log-response"></a>

You can include the last 4 KB of the execution log in the function response by using the `log_type` parameter in your `aws_lambda.invoke` function call\. By default, this parameter is set to `None`, but you can specify `Tail` to capture the results of the Lambda execution log in the response, as shown following\.

```
SELECT *, select convert_from(decode(log_result, 'base64'), 'utf-8') as log FROM aws_lambda.invoke(:'aws_lambda_arn_1', '{"body": "Hello from Postgres!"}'::json, 'RequestResponse', 'Tail');
```

Set the [aws\_lambda\.invoke](PostgreSQL-Lambda-functions.md#aws_lambda.invoke) function's `log_type` parameter to `Tail` to include the execution log in the response\. The default value for the `log_type` parameter is `None`\.

The `log_result` that's returned is a `base64` encoded string\. You can decode the contents using a combination of the `decode` and `convert_from` PostgreSQL functions\.

For more information about `log_type`, see [aws\_lambda\.invoke](PostgreSQL-Lambda-functions.md#aws_lambda.invoke)\.

### Example: Including client context in a Lambda function<a name="PostgreSQL-Lambda-client-context"></a>

The `aws_lambda.invoke` function has a `context` parameter that you can use to pass information separate from the payload, as shown following\. 

```
SELECT *, convert_from(decode(log_result, 'base64'), 'utf-8') as log FROM aws_lambda.invoke(:'aws_lambda_arn_1', '{"body": "Hello from Postgres!"}'::json, 'RequestResponse', 'Tail');
```

To include client context, use a JSON object for the [aws\_lambda\.invoke](PostgreSQL-Lambda-functions.md#aws_lambda.invoke) function's `context` parameter\.

For more information about the `context` parameter, see the [aws\_lambda\.invoke](PostgreSQL-Lambda-functions.md#aws_lambda.invoke) reference\. 

### Example: Invoking a specific version of a Lambda function<a name="PostgreSQL-Lambda-function-version"></a>

You can specify a particular version of a Lambda function by including the `qualifier` parameter with the `aws_lambda.invoke` call\. Following, you can find an example that does this using `'custom_version'` as an alias for the version\.

```
SELECT * FROM aws_lambda.invoke(:'aws_lambda_arn_1', '{"body": "Hello from Postgres!"}'::json, 'RequestResponse', 'None', NULL, 'custom_version');
```

You can also supply a Lambda function qualifier with the function name details instead, as follows\.

```
SELECT * FROM aws_lambda.invoke(aws_commons.create_lambda_function_arn('my-function:custom_version', 'us-west-2'),
'{"body": "Hello from Postgres!"}'::json);
```

For more information about `qualifier` and other parameters, see the [aws\_lambda\.invoke](PostgreSQL-Lambda-functions.md#aws_lambda.invoke) reference\.

## Lambda function error messages<a name="PostgreSQL-Lambda-errors"></a>

In the following list you can find information about error messages, with possible causes and solutions\.
+ **VPC configuration issues**

  VPC configuration issues can raise the following error messages when trying to connect: 

  ```
  ERROR:  invoke API failed
  DETAIL: AWS Lambda client returned 'Unable to connect to endpoint'.
  CONTEXT:  SQL function "invoke" statement 1
  ```

  A common cause for this error is improperly configured VPC security group\. Make sure you have an outbound rule for TCP open on port 443 of your VPC security group so that your VPC can connect to the Lambda VPC\.

  If your DB instance is private, check the private DNS setup for your VPC\. Make sure that you set the `rds.custom_dns_resolution` parameter to 1 and setup AWS PrivateLink as outlined in [Step 1: Configure your RDS for PostgreSQL DB instance for outbound connections to AWS Lambda](#PostgreSQL-Lambda-network)\. For more information, see [Interface VPC endpoints \(AWS PrivateLink\)](https://docs.aws.amazon.com/vpc/latest/privatelink/vpce-interface.html#vpce-private-dns)\. 
+ **Lack of permissions needed to invoke Lambda functions**

  If you see either of the following error messages, the user \(role\) invoking the function doesn't have proper permissions\.

  ```
  ERROR:  permission denied for schema aws_lambda
  ```

  ```
  ERROR:  permission denied for function invoke
  ```

  A user \(role\) must be given specific grants to invoke Lambda functions\. For more information, see [Step 6: Grant other users permission to invoke Lambda functions](#PostgreSQL-Lambda-grant-users-permissions)\. 
+ **Improper handling of errors in your Lambda functions**

  If a Lambda function throws an exception during request processing, `aws_lambda.invoke` fails with a PostgreSQL error such as the following\.

  ```
  SELECT * FROM aws_lambda.invoke(:'aws_lambda_arn_1', '{"body": "Hello from Postgres!"}'::json);
  ERROR:  lambda invocation failed
  DETAIL:  "arn:aws:lambda:us-west-2:555555555555:function:my-function" returned error "Unhandled", details: "<Error details string>".
  ```

  Be sure to handle errors in your Lambda functions or in your PostgreSQL application\.