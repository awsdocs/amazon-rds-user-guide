# Creating a CEV for RDS Custom for SQL Server<a name="custom-cev-sqlserver.create"></a>

You can create a custom engine version \(CEV\) using the AWS Management Console or the AWS CLI\. You can then use the CEV to create an RDS Custom for SQL Server DB instance\. 

Make sure that the Amazon Machine Image \(AMI\) is in the same AWS account and Region as your CEV\. Otherwise, the process to create a CEV fails\.

For more information, see [Creating and connecting to a DB instance for Amazon RDS Custom for SQL Server](custom-creating-sqlserver.md)\.

## Console<a name="custom-cev-sqlserver.create.console"></a>

**To create a CEV**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Custom engine versions**\.

   The **Custom engine versions** page shows all CEVs that currently exist\. If you haven't created any CEVs, the table is empty\.

1. Choose **Create custom engine version**\.

1. For **Engine type**, choose **Microsoft SQL Server**\.

1. For **Edition**, choose **SQL Server Enterprise, Standard, or Web Edition**\.

1. For **Major version**, choose the major engine version that's installed on your AMI\.

1. In **Version details**, enter a valid name in **Custom engine version name**\.

   The name format is `major-engine-version.minor-engine-version.customized_string`\. You can use 1–50 alphanumeric characters, underscores, dashes, and periods\. For example, you might enter the name **15\.00\.4249\.2\.my\_cevtest**\.

   Optionally, enter a description for your CEV\.

1. For **Installation Media**, browse to or enter the AMI ID that you'd like to create the CEV from\.

1. In the **Tags** section, add any tags to identify the CEV\.

1. Choose **Create custom engine version**\.

The **Custom engine versions** page appears\. Your CEV is shown with the status **pending\-validation**

## AWS CLI<a name="custom-cev-sqlserver.create.CEV"></a>

To create a CEV by using the AWS CLI, run the [create\-custom\-db\-engine\-version](https://docs.aws.amazon.com/cli/latest/reference/rds/create-custom-db-engine-version.html) command\.

The following options are required:
+ `--engine`
+ `--engine-version`
+ `--image-id`

You can also specify the following options:
+ `--kms-key-id`
+ `--description`
+ `--region`
+ `--tags`

The following example creates a CEV named `15.00.4249.2.my_cevtest`\. Make sure that the name of your CEV begins with the major engine version number\.

**Example**  
For Linux, macOS, or Unix:  

```
1. aws rds create-custom-db-engine-version \
2.     --engine custom-sqlserver-ee \
3.     --engine-version 15.00.4249.2.my_cevtest \
4.     --image-id ami-0r93cx31t5r596482 \                    
5.     --kms-key-id my-kms-key \
6.     --description "Custom SQL Server EE 15.00.4249.2 cev test"
```
The following partial output shows the engine, parameter groups, and other information\.  

```
 1. "DBEngineVersions": [
 2.     {
 3.     "Engine": "custom-sqlserver-ee",
 4.     "MajorEngineVersion": "15.00",
 5.     "EngineVersion": "15.00.4249.2.my_cevtest",
 6.     "DBEngineDescription": "Microsoft SQL Server Enterprise Edition for RDS Custom for SQL Server",
 7.     "DBEngineVersionArn": "arn:aws:rds:us-east-1:<my-account-id>:cev:custom-sqlserver-ee/15.00.4249.2.my_cevtest/a1234a1-123c-12rd-bre1-1234567890",
 8.     "DBEngineVersionDescription": "Custom SQL Server EE 15.00.4249.2 cev test",
 9.     "KMSKeyId": "arn:aws:kms:us-east-1:<your-account-id>:key/<my-kms-key-id>",
10.                                                       
11.     "Image": [
12.         "ImageId": "ami-0r93cx31t5r596482",
13.         "Status": "pending-validation"
14.      ],
15.     "CreateTime": "2022-11-20T19:30:01.831000+00:00",
16.     "SupportsLogExportsToCloudwatchLogs": false,
17.     "SupportsReadReplica": false,
18.     "Status": "pending-validation",
19.     "SupportsParallelQuery": false,
20.     "SupportsGlobalDatabases": false,
21.     "TagList": []
22.     }
23. ]
```

If the process to create a CEV fails, RDS Custom for SQL Server issues `RDS-EVENT-0198` with the message `Creation failed for custom engine version major-engine-version.cev_name`\. The message includes details about the failure, for example, the event prints missing files\. To find troubleshooting ideas for CEV creation issues, see [Troubleshooting CEV errors for RDS Custom for SQL Server](custom-troubleshooting.md#custom-troubleshooting-sqlserver.cev)\.

## Create an RDS Custom for SQL Server DB instance from a CEV<a name="custom-cev-sqlserver.create.newdbinstance"></a>

After you successfully create a CEV, the **CEV status** shows `pending-validation`\. You can now create a new RDS Custom for SQL Server DB instance using the CEV\. To create a new RDS Custom for SQL Server DB instance from a CEV, see [Creating an RDS Custom for SQL Server DB instance](custom-creating-sqlserver.md#custom-creating-sqlserver.create)\.

## Lifecycle of a CEV<a name="custom-cev-sqlserver.create.lifecycle"></a>

The CEV lifecycle includes the following statuses\.


****  

| CEV status | Description | Troubleshooting suggestions | 
| --- | --- | --- | 
| `pending-validation` | A CEV was created and is pending the validation of the associated AMI\. A CEV will remain in `pending-validation` until an RDS Custom for SQL Server DB instance is created from it\. | If there are no existing tasks, create a new RDS Custom for SQL Server DB instance from the CEV\. When creating the RDS Custom for SQL Server DB instance, the system attempts to validate the associated AMI for a CEV\.  | 
| `validating` | A creation task for the RDS Custom for SQL Server DB instance based on a new CEV is in progress\. When creating the RDS Custom for SQL Server DB instance, the system attempts to validate the associated AMI of a CEV\. | Wait for the creation task of the existing RDS Custom for SQL Server DB instance to complete\. You can use the RDS EVENTS console to review detailed event messages for troubleshooting\.  | 
| `available` | The CEV was successfully validated\. A CEV will enter the `available` status once an RDS Custom for SQL Server DB instance has been successfully created from it\. | The CEV doesn't require any additional validation\. It can be used to create additional RDS Custom for SQL Server DB instances or modify existing ones\. | 
| `inactive` | The CEV has been modified to an inactive state\. | You can't create or upgrade an RDS Custom DB instance with this CEV\. Also, you can't restore a DB snapshot to create a new RDS Custom DB instance with this CEV\. For information about how to change the state to `ACTIVE`, see [Modifying a CEV for RDS Custom for SQL Server](custom-cev-sqlserver-modifying.md)\. | 
| `failed` | The create DB instance step failed for this CEV before it could validate the AMI\. Alternatively, the underlying AMI used by the CEV isn't in an available state\. | Troubleshoot the root cause for why the system couldn't create the DB instance\. View the detailed error message and try to create a new DB instance again\. Ensure that the underlying AMI used by the CEV is in an available state\. | 
| `incompatible-image-configuration` | There was an error validating the AMI\. | View the technical details of the error\. You can't attempt to validate the AMI with this CEV again\. Review the following: recommendations: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/custom-cev-sqlserver.create.html) Create a new CEV using the correct information\. If needed, create a new EC2 instance using a supported AMI and run the Sysprep process on it\.  | 