# Creating a rule that triggers on an Amazon RDS event<a name="rds-cloud-watch-events"></a>

Using Amazon CloudWatch Events and Amazon EventBridge, you can automate AWS services and respond to system events such as application availability issues or resource changes\. 

**Topics**
+ [Creating rules to send Amazon RDS events to CloudWatch Events](#rds-cloudwatch-events.sending-to-cloudwatch-events)
+ [Tutorial: Log DB instance state changes using Amazon EventBridge](#log-rds-instance-state)

## Creating rules to send Amazon RDS events to CloudWatch Events<a name="rds-cloudwatch-events.sending-to-cloudwatch-events"></a>

You can write simple rules to indicate which Amazon RDS events interest you and which automated actions to take when an event matches a rule\. You can set a variety of targets, such as an AWS Lambda function or an Amazon SNS topic, which receive events in JSON format\. For example, you can configure Amazon RDS to send events to CloudWatch Events or Amazon EventBridge whenever a DB instance is created or deleted\. For more information, see the [Amazon CloudWatch Events User Guide](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/) and the [Amazon EventBridge User Guide](https://docs.aws.amazon.com/eventbridge/latest/userguide/)\.

**To create a rule that triggers on an RDS event:**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. Under **Events** in the navigation pane, choose **Rules**\.

1. Choose **Create rule**\.

1. For **Event Source**, do the following:

   1. Choose **Event Pattern**\.

   1. For **Service Name**, choose **Relational Database Service \(RDS\)**\.

   1. For **Event Type**, choose the type of Amazon RDS resource that triggers the event\. For example, if a DB instance triggers the event, choose **RDS DB Instance Event**\.

1. For **Targets**, choose **Add Target** and choose the AWS service that is to act when an event of the selected type is detected\. 

1. In the other fields in this section, enter information specific to this target type, if any is needed\. 

1. For many target types, CloudWatch Events needs permissions to send events to the target\. In these cases, CloudWatch Events can create the IAM role needed for your event to run: 
   + To create an IAM role automatically, choose **Create a new role for this specific resource**\.
   + To use an IAM role that you created before, choose **Use existing role**\.

1. Optionally, repeat steps 5\-7 to add another target for this rule\.

1. Choose **Configure details**\. For **Rule definition**, type a name and description for the rule\.

   The rule name must be unique within this Region\.

1. Choose **Create rule**\.

For more information, see [Creating a CloudWatch Events Rule That Triggers on an Event](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/Create-CloudWatch-Events-Rule.html) in the *Amazon CloudWatch User Guide*\.

## Tutorial: Log DB instance state changes using Amazon EventBridge<a name="log-rds-instance-state"></a>

In this tutorial, you create an AWS Lambda function that logs the state changes for an Amazon RDS instance\. You then create a rule that runs the function whenever there is a state change of an existing RDS DB instance\. The tutorial assumes that you have a small running test instance that you can shut down temporarily\.

**Important**  
Don't perform this tutorial on a running production DB instance\.

**Topics**
+ [Step 1: Create an AWS Lambda function](#rds-create-lambda-function)
+ [Step 2: Create a rule](#rds-create-rule)
+ [Step 3: Test the rule](#rds-test-rule)

### Step 1: Create an AWS Lambda function<a name="rds-create-lambda-function"></a>

Create a Lambda function to log the state change events\. You specify this function when you create your rule\.

**To create a Lambda function**

1. Open the AWS Lambda console at [https://console\.aws\.amazon\.com/lambda/](https://console.aws.amazon.com/lambda/)\.

1. If you're new to Lambda, you see a welcome page\. Choose **Get Started Now**\. Otherwise, choose **Create function**\.

1. Choose **Author from scratch**\.

1. On the **Create function** page, do the following:

   1. Enter a name and description for the Lambda function\. For example, name the function **RDSInstanceStateChange**\. 

   1. In **Runtime**, select **Node\.js 16x**\. 

   1. For **Architecture**, choose **x86\_64**\.

   1. For **Execution role**, do either of the following:
      + Choose **Create a new role with basic Lambda permissions**\.
      + For **Existing role**, choose **Use an existing role**\. Choose the role that you want to use\. 

   1. Choose **Create function**\.

1. On the **RDSInstanceStateChange** page, do the following:

   1. In **Code source**, select **index\.js**\. 

   1. In the **index\.js** pane, delete the existing code\.

   1. Enter the following code:

      ```
      console.log('Loading function');
      
      exports.handler = async (event, context) => {
          console.log('Received event:', JSON.stringify(event));
      };
      ```

   1. Choose **Deploy**\.

### Step 2: Create a rule<a name="rds-create-rule"></a>

Create a rule to run your Lambda function whenever you launch an Amazon RDS instance\.

**To create the EventBridge rule**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the navigation pane, choose **Rules**\.

1. Choose **Create rule**\.

1. Enter a name and description for the rule\. For example, enter **RDSInstanceStateChangeRule**\.

1. Choose **Rule with an event pattern**, and then choose **Next**\.

1. For **Event source**, choose **AWS events or EventBridge partner events**\.

1. Scroll down to the **Event pattern** section\.

1. For **Event source**, choose **AWS services**\.

1. For **AWS service**, choose **Relational Database Service \(RDS\)**\.

1. For **Event type**, choose **RDS DB Instance Event**\.

1. Leave the default event pattern\. Then choose **Next**\.

1. For **Target types**, choose **AWS service**\.

1. For **Select a target**, choose **Lambda function**\.

1. For **Function**, choose the Lambda function that you created\. Then choose **Next**\.

1. In **Configure tags**, choose **Next**\.

1. Review the steps in your rule\. Then choose **Create rule**\.

### Step 3: Test the rule<a name="rds-test-rule"></a>

To test your rule, shut down an RDS DB instance\. After waiting a few minutes for the instance to shut down, verify that your Lambda function was invoked\.

**To test your rule by stopping a DB instance**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. Stop an RDS DB instance\.

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the navigation pane, choose **Rules**, choose the name of the rule that you created\.

1. In **Rule details**, choose **Metrics for the rule**\.

   You are redirected to the Amazon CloudWatch console\.

1. In **All metrics**, choose the name of the rule that you created\.

   The graph should indicate that the rule was invoked\.

1. In the navigation pane, choose **Log groups**\.

1. Choose the name of the log group for your Lambda function \(**/aws/lambda/*function\-name***\)\.

1. Choose the name of the log stream to view the data provided by the function for the instance that you launched\. You should see a received event similar to the following:

   ```
   {
       "version": "0",
       "id": "12a345b6-78c9-01d2-34e5-123f4ghi5j6k",
       "detail-type": "RDS DB Instance Event",
       "source": "aws.rds",
       "account": "111111111111",
       "time": "2021-03-19T19:34:09Z",
       "region": "us-east-1",
       "resources": [
           "arn:aws:rds:us-east-1:111111111111:db:testdb"
       ],
       "detail": {
           "EventCategories": [
               "notification"
           ],
           "SourceType": "DB_INSTANCE",
           "SourceArn": "arn:aws:rds:us-east-1:111111111111:db:testdb",
           "Date": "2021-03-19T19:34:09.293Z",
           "Message": "DB instance stopped",
           "SourceIdentifier": "testdb",
           "EventID": "RDS-EVENT-0087"
       }
   }
   ```

   For more examples of RDS events in JSON format, see [Overview of events for Amazon RDS](rds-cloudwatch-events.sample.md)\.

1. \(Optional\) When you're finished, you can open the Amazon RDS console and start the instance that you stopped\.