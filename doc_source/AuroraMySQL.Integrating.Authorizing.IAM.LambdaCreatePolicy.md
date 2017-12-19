# Creating an IAM Policy to Access AWS Lambda Resources<a name="AuroraMySQL.Integrating.Authorizing.IAM.LambdaCreatePolicy"></a>

You can use the following steps to create an IAM policy that provides the minimum required permissions for Aurora to invoke an AWS Lambda function on your behalf\. To allow Aurora to invoke all of your AWS Lambda functions, you can skip these steps and use the predefined `AWSLambdaRole` policy instead of creating your own\.

**To create an IAM policy to grant invoke to your AWS Lambda functions**

1. Open the [IAM Console](https://console.aws.amazon.com/iam/home?#home)\.

1. In the navigation pane, choose **Policies**\.

1. Choose **Create policy**\.

1. On the **Visual editor** tab, choose **Choose a service**, and then choose **Lambda**\.

1. Choose **Select actions** and then choose the AWS Lambda permissions needed for the IAM policy\.

   Ensure that `InvokeFunction` is selected\. It is the minimum required permission to enable Amazon Aurora to invoke an AWS Lambda function\.

1. Choose **Resources** and choose **Add ARN** for **function**\.

1. In the **Add ARN\(s\)** dialog box, provide the details about your resource\.

   Specify the Lambda function to allow access to\. For instance, if you want to allow Aurora to access a Lambda function named `example_function`, then set the ARN value to `arn:aws:lambda:::function:example_function`\. 

   For more information on how to define an access policy for AWS Lambda, see [Authentication and Access Control for AWS Lambda](http://docs.aws.amazon.com/lambda/latest/dg/lambda-auth-and-access-control.html)\.

1. Optionally, choose **Add additional permissions** to add another AWS Lambda function to the policy, and repeat the previous steps for the function\.
**Note**  
You can repeat this to add corresponding function permission statements to your policy for each AWS Lambda function that you want Aurora to access\.

1. Choose **Review policy**\.

1. Set **Name** to a name for your IAM policy, for example `AllowAuroraToExampleFunction`\. You use this name when you create an IAM role to associate with your Aurora DB cluster\. You can also add an optional **Description** value\.

1. Choose **Create policy**\.