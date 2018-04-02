# Invoking a Lambda Function from an Amazon Aurora MySQL DB Cluster<a name="AuroraMySQL.Integrating.Lambda"></a>

You can invoke an AWS Lambda function from an Amazon Aurora with MySQL compatibility DB cluster with a native function or a stored procedure\. Before invoking a Lambda function from an Aurora MySQL, the Aurora DB cluster must have access to Lambda\.

**Topics**
+ [Giving Aurora Access to Lambda](#AuroraMySQL.Integrating.LambdaAccess)
+ [Invoking a Lambda Function with an Aurora MySQL Native Function](#AuroraMySQL.Integrating.NativeLambda)
+ [Invoking a Lambda Function with an Aurora MySQL Stored Procedure](#AuroraMySQL.Integrating.ProcLambda)

For Aurora MySQL version 1\.16 and later, we recommend using an Aurora MySQL native function\. Starting with Aurora MySQL version 1\.16, using a stored procedure is deprecated\.

## Giving Aurora Access to Lambda<a name="AuroraMySQL.Integrating.LambdaAccess"></a>

Before you can invoke Lambda functions from an Aurora MySQL, you must first give your Aurora MySQL DB cluster permission to access Lambda\.

**To give Aurora MySQL access to Lambda**

1. Create an AWS Identity and Access Management \(IAM\) policy that provides the permissions that allow your Aurora MySQL DB cluster to invoke Lambda functions\. For instructions, see [Creating an IAM Policy to Access AWS Lambda Resources](AuroraMySQL.Integrating.Authorizing.IAM.LambdaCreatePolicy.md)\.

1. Create an IAM role, and attach the IAM policy you created in [Creating an IAM Policy to Access AWS Lambda Resources](AuroraMySQL.Integrating.Authorizing.IAM.LambdaCreatePolicy.md) to the new IAM role\. For instructions, see [Creating an IAM Role to Allow Amazon Aurora to Access AWS Services](AuroraMySQL.Integrating.Authorizing.IAM.CreateRole.md)\.

1. Set the `aws_default_lambda_role` DB cluster parameter to the Amazon Resource Name \(ARN\) of the new IAM role\.

   For more information about DB cluster parameters, see [Amazon Aurora DB Cluster and DB Instance Parameters](Aurora.Managing.md#Aurora.Managing.ParameterGroups)\.

1. To permit database users in an Aurora MySQL DB cluster to invoke Lambda functions, associate the role that you created in [Creating an IAM Role to Allow Amazon Aurora to Access AWS Services](AuroraMySQL.Integrating.Authorizing.IAM.CreateRole.md) with the DB cluster\. For information about associating an IAM role with a DB cluster, see [Associating an IAM Role with an Amazon Aurora MySQL DB Cluster](AuroraMySQL.Integrating.Authorizing.IAM.AddRoleToDBCluster.md)\.

1. Configure your Aurora MySQL DB cluster to allow outbound connections to Lambda\. For instructions, see [Enabling Network Communication from Amazon Aurora MySQL to Other AWS Services](AuroraMySQL.Integrating.Authorizing.Network.md)\.

## Invoking a Lambda Function with an Aurora MySQL Native Function<a name="AuroraMySQL.Integrating.NativeLambda"></a>

**Note**  
You can call the native functions `lambda_sync` and `lambda_async` when you use Aurora MySQL version 1\.16 and later\. For more information about Aurora MySQL versions, see [Amazon Aurora MySQL Database Engine Updates](AuroraMySQL.Updates.md)\.

You can invoke an AWS Lambda function from an Aurora MySQL DB cluster by calling the native functions `lambda_sync` and `lambda_async`\. This approach can be useful when you want to integrate your database running on Aurora MySQL with other AWS services\. For example, you might want to send a notification using Amazon Simple Notification Service \(Amazon SNS\) whenever a row is inserted into a specific table in your database\.

### Working with Native Functions to Invoke a Lambda Function<a name="AuroraMySQL.Integrating.NativeLambda.lambda_functions"></a>

The `lambda_sync` and `lambda_async` functions are built\-in, native functions that invoke a Lambda function synchronously or asynchronously\. When you must know the result of the invoked function's execution before moving on to another action, use the synchronous function `lambda_sync`\. When you don't need to know the result of the execution before moving on to another action, use the asynchronous function `lambda_async`\.

The user invoking a native function must be granted the `INVOKE LAMBDA` privilege\. To grant this privilege to a user, connect to the DB instance as the master user, and run the following statement\.

```
GRANT INVOKE LAMBDA ON *.* TO user@domain-or-ip-address                
```

You can revoke this privilege by running the following statement\.

```
REVOKE INVOKE LAMBDA ON *.* FROM user@domain-or-ip-address               
```

#### Syntax for the lambda\_sync Function<a name="AuroraMySQL.Integrating.NativeLambda.lambda_functions.Sync.Syntax"></a>

You invoke the `lambda_sync` function synchronously with the `RequestResponse` invocation type\. The function returns the result of the Lambda invocation in a JSON payload\. The function has the following syntax\.

```
lambda_sync (
  lambda_function_ARN,
  JSON_payload
)
```

**Note**  
You can use triggers to call Lambda on data\-modifying statements\. Remember that triggers are not executed once per SQL statement, but once per row modified, one row at a time\. Trigger execution is synchronous, and the data\-modifying statement will not return until trigger execution completes\.

#### Parameters for the lambda\_sync Function<a name="AuroraMySQL.Integrating.NativeLambda.lambda_functions.Sync.Parameters"></a>

The `lambda_sync` function has the following parameters\.

 *lambda\_function\_ARN*   
The Amazon Resource Name \(ARN\) of the Lambda function to invoke\.

 *JSON\_payload*   
The payload for the invoked Lambda function, in JSON format\.

#### Example for the lambda\_sync Function<a name="AuroraMySQL.Integrating.NativeLambda.lambda_functions.Sync.Example"></a>

The following query based on `lambda_sync` invokes the Lambda function `BasicTestLambda` synchronously using the function ARN\. The payload for the function is `{"operation": "ping"}`\.

```
SELECT lambda_sync(
    'arn:aws:lambda:us-east-1:868710585169:function:BasicTestLambda', 
    '{"operation": "ping"}');
```

#### Syntax for the lambda\_async Function<a name="AuroraMySQL.Integrating.NativeLambda.lambda_functions.Async.Syntax"></a>

You invoke the `lambda_async` function asynchronously with the `Event` invocation type\. The function returns the result of the Lambda invocation in a JSON payload\. The function has the following syntax\.

```
lambda_async (
  lambda_function_ARN,
  JSON_payload
)
```

#### Parameters for the lambda\_async Function<a name="AuroraMySQL.Integrating.NativeLambda.lambda_functions.Async.Parameters"></a>

The `lambda_async` function has the following parameters\.

 *lambda\_function\_ARN*   
The Amazon Resource Name \(ARN\) of the Lambda function to invoke\.

 *JSON\_payload*   
The payload for the invoked Lambda function, in JSON format\.

**Note**  
Aurora MySQL doesn't support JSON parsing\. JSON parsing isn't required when a Lambda function returns an atomic value, such as a number or a string\.

#### Example for the lambda\_async Function<a name="AuroraMySQL.Integrating.NativeLambda.lambda_functions.Async.Example"></a>

The following query based on `lambda_async` invokes the Lambda function `BasicTestLambda` asynchronously using the function ARN\. The payload for the function is `{"operation": "ping"}`\.

```
SELECT lambda_async(
    'arn:aws:lambda:us-east-1:868710585169:function:BasicTestLambda', 
    '{"operation": "ping"}');
```

### Related Topics<a name="AuroraMySQL.Integrating.NativeLambda.RelatedTopics"></a>
+ [Integrating Aurora with Other AWS Services](Aurora.Integrating.md)
+ [Amazon Aurora on Amazon RDS](CHAP_Aurora.md)
+ [http://docs.aws.amazon.com/lambda/latest/dg/welcome.html.html](http://docs.aws.amazon.com/lambda/latest/dg/welcome.html.html)

## Invoking a Lambda Function with an Aurora MySQL Stored Procedure<a name="AuroraMySQL.Integrating.ProcLambda"></a>

**Note**  
For Aurora MySQL version 1\.8 and later, you can use direct integration with AWS Lambda\. For more information about Aurora MySQL versions, see [Amazon Aurora MySQL Database Engine Updates](AuroraMySQL.Updates.md)\.  
Starting with Amazon Aurora version 1\.16, the stored procedure `mysql.lambda_async` is deprecated\. If you are using Aurora version 1\.16 or later, we strongly recommend that you work with native Lambda functions instead\. For more information, see [Working with Native Functions to Invoke a Lambda Function](#AuroraMySQL.Integrating.NativeLambda.lambda_functions)\.

You can invoke an AWS Lambda function from an Aurora MySQL DB cluster by calling the `mysql.lambda_async` procedure\. This approach can be useful when you want to integrate your database running on Aurora MySQL with other AWS services\. For example, you might want to send a notification using Amazon Simple Notification Service \(Amazon SNS\) whenever a row is inserted into a specific table in your database\. 

### Working with the mysql\.lambda\_async Procedure to Invoke a Lambda Function<a name="AuroraMySQL.Integrating.Lambda.mysql_lambda_async"></a>

The `mysql.lambda_async` procedure is a built\-in stored procedure that invokes a Lambda function asynchronously\. To use this procedure, your database user must have execute privilege on the `mysql.lambda_async` stored procedure\.

#### Syntax<a name="AuroraMySQL.Integrating.Lambda.mysql_lambda_async.Syntax"></a>

The `mysql.lambda_async` procedure has the following syntax\.

```
CALL mysql.lambda_async (
  lambda_function_ARN,
  lambda_function_input
)
```

#### Parameters<a name="AuroraMySQL.Integrating.Lambda.mysql_lambda_async.Parameters"></a>

The `mysql.lambda_async` procedure has the following parameters\.

 *lambda\_function\_ARN*   
The Amazon Resource Name \(ARN\) of the Lambda function to invoke\.

 *lambda\_function\_input*   
The input string, in JSON format, for the invoked Lambda function\.

#### Examples<a name="AuroraMySQL.Integrating.Lambda.mysql_lambda_async.Examples"></a>

As a best practice, we recommend that you wrap calls to the `mysql.lambda_async` procedure in a stored procedure that can be called from different sources such as triggers or client code\. This approach can help to avoid impedance mismatch issues and make it easier to invoke Lambda functions\. 

**Note**  
Be careful when invoking an AWS Lambda function from triggers on tables that experience high write traffic\. INSERT, UPDATE, and DELETE triggers are activated per row\. A write\-heavy workload on a table with INSERT, UPDATE, or DELETE triggers results in a large number of calls to your AWS Lambda function\.   
Although calls to the `mysql.lambda_async` procedure are asynchronous, triggers are synchronous\. A statement that results in a large number of trigger activations doesn't wait for the call to the AWS Lambda function to complete, but it does wait for the triggers to complete before returning control to the client\.

**Example Example: Invoke an AWS Lambda Function to Send Email**  
The following example creates a stored procedure that you can call in your database code to send an email using a Lambda function\.  
**AWS Lambda Function**  

```
import boto3

ses = boto3.client('ses')

def SES_send_email(event, context):

    return ses.send_email(
        Source=event['email_from'],
        Destination={
            'ToAddresses': [
            event['email_to'],
            ]
        },

        Message={
            'Subject': {
            'Data': event['email_subject']
            },
            'Body': {
                'Text': {
                    'Data': event['email_body']
                }
            }
        }
    )
```
**Stored Procedure**  

```
DROP PROCEDURE IF EXISTS SES_send_email;
DELIMITER ;;
	CREATE PROCEDURE SES_send_email(IN email_from VARCHAR(255), 
	                                IN email_to VARCHAR(255), 
	                                IN subject VARCHAR(255), 
	                                IN body TEXT) LANGUAGE SQL 
	BEGIN
		CALL mysql.lambda_async(
	       'arn:aws:lambda:us-west-2:123456789012:function:SES_send_email',
	       CONCAT('{"email_to" : "', email_to, 
	           '", "email_from" : "', email_from, 
	           '", "email_subject" : "', subject, 
	           '", "email_body" : "', body, '"}')
	   );
	END
	;;
DELIMITER ;
```
**Call the Stored Procedure to Invoke the AWS Lambda Function**  

```
mysql> call SES_send_email('example_from@amazon.com', 'example_to@amazon.com', 'Email subject', 'Email content');
```

**Example Example: Invoke an AWS Lambda Function to Publish an Event from a Trigger**  
The following example creates a stored procedure that publishes an event by using Amazon SNS\. The code calls the procedure from a trigger when a row is added to a table\.  
**AWS Lambda Function**  

```
import boto3

sns = boto3.client('sns')

def SNS_publish_message(event, context):
    
    return sns.publish(
        TopicArn='arn:aws:sns:us-west-2:123456789012:Sample_Topic',
        Message=event['message'],
        Subject=event['subject'],
        MessageStructure='string'
    )
```
**Stored Procedure**  

```
DROP PROCEDURE IF EXISTS SNS_Publish_Message;
DELIMITER ;;
CREATE PROCEDURE SNS_Publish_Message (IN subject VARCHAR(255), 
                                      IN message TEXT) LANGUAGE SQL 
BEGIN
  CALL mysql.lambda_async('arn:aws:lambda:us-west-2:123456789012:function:SNS_publish_message', 
     CONCAT('{ "subject" : "', subject, 
            '", "message" : "', message, '" }')
     );
END
;;
DELIMITER ;
```
**Table**  

```
CREATE TABLE 'Customer_Feedback' (
  'id' int(11) NOT NULL AUTO_INCREMENT,
  'customer_name' varchar(255) NOT NULL,
  'customer_feedback' varchar(1024) NOT NULL,
  PRIMARY KEY ('id')
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```
**Trigger**  

```
DELIMITER ;;
CREATE TRIGGER TR_Customer_Feedback_AI 
  AFTER INSERT ON Customer_Feedback 
  FOR EACH ROW
BEGIN
  SELECT CONCAT('New customer feedback from ', NEW.customer_name), NEW.customer_feedback INTO @subject, @feedback;
  CALL SNS_Publish_Message(@subject, @feedback);
END
;;
DELIMITER ;
```
**Insert a Row into the Table to Trigger the Notification**  

```
mysql> insert into Customer_Feedback (customer_name, customer_feedback) VALUES ('Sample Customer', 'Good job guys!');
```

### Related Topics<a name="AuroraMySQL.Integrating.Lambda.RelatedTopics"></a>
+ [Integrating Aurora with Other AWS Services](Aurora.Integrating.md)
+ [Amazon Aurora on Amazon RDS](CHAP_Aurora.md)
+ [http://docs.aws.amazon.com/lambda/latest/dg/welcome.html.html](http://docs.aws.amazon.com/lambda/latest/dg/welcome.html.html)