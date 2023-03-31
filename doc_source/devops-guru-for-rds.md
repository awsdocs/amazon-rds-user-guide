# Analyzing performance anomalies with Amazon DevOps Guru for Amazon RDS<a name="devops-guru-for-rds"></a>

Amazon DevOps Guru is a fully managed operations service that helps developers and operators improve the performance and availability of their applications\. DevOps Guru offloads the tasks associated with identifying operational issues so that you can quickly implement recommendations to improve your application\. For more information, see [What is Amazon DevOps Guru?](https://docs.aws.amazon.com/devops-guru/latest/userguide/welcome.html) in the *Amazon DevOps Guru User Guide*\.

DevOps Guru detects, analyzes, and makes recommendations for existing operational issues for all Amazon RDS DB engines\. DevOps Guru for RDS extends this capability by applying machine learning to Performance Insights metrics for RDS for PostgreSQL databases\. These monitoring features allow DevOps Guru for RDS to detect and diagnose performance bottlenecks and recommend specific corrective actions\. DevOps Guru for RDS can also detect problematic conditions in your RDS for PostgreSQL database before they occur\.

The following video is an overview of DevOps Guru for RDS\.

[![AWS Videos](http://img.youtube.com/vi/N3NNYgzYUDA/0.jpg)](http://www.youtube.com/watch?v=N3NNYgzYUDA)

For a deep dive on this subject, see [Amazon DevOps Guru for RDS under the hood\.](http://aws.amazon.com/blogs/database/amazon-devops-guru-for-rds-under-the-hood/)

**Topics**
+ [Benefits of DevOps Guru for RDS](#devops-guru-for-rds.benefits)
+ [How DevOps Guru for RDS works](#devops-guru-for-rds.how-it-works)
+ [Setting up DevOps Guru for RDS](#devops-guru-for-rds.configuring)

## Benefits of DevOps Guru for RDS<a name="devops-guru-for-rds.benefits"></a>

If you're responsible for RDS for PostgreSQL database, you might not know that an event or regression that is affecting that database is occurring\. When you learn about the issue, you might not know why it's occurring or what to do about it\. Rather than turning to a database administrator \(DBA\) for help or relying on third\-party tools, you can follow recommendations from DevOps Guru for RDS\. 

You gain the following advantages from the detailed analysis of DevOps Guru for RDS:

**Fast diagnosis**  
DevOps Guru for RDS continuously monitors and analyzes database telemetry\. Performance Insights, Enhanced Monitoring, and Amazon CloudWatch collect telemetry data for your database instance\. DevOps Guru for RDS uses statistical and machine learning techniques to mine this data and detect anomalies\. To learn more about telemetry data, see [Monitoring DB load with Performance Insights on Amazon RDS](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_PerfInsights.html) and [Monitoring OS metrics with Enhanced Monitoring](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_Monitoring.OS.html) in the *Amazon RDS User Guide*\.

**Fast resolution**  
Each anomaly identifies the performance issue and suggests avenues of investigation or corrective actions\. For example, DevOps Guru for RDS might recommend that you investigate specific wait events\. Or it might recommend that you tune your application pool settings to limit the number of database connections\. Based on these recommendations, you can resolve performance issues more quickly than by troubleshooting manually\.

**Proactive insights**  
DevOps Guru for RDS uses metrics from your resources to detect potentially problematic behavior before it becomes a bigger problem\. For example, it can detect when your database is using an increasing number of on\-disk temporary tables, which could start to impact performance\. DevOps Guru then provides recommendations to help you address issues before they become bigger problems\.

**Deep knowledge of Amazon engineers and machine learning**  
To detect performance issues and help you resolve bottlenecks, DevOps Guru for RDS relies on machine learning \(ML\) and advanced mathematical formulas\. Amazon database engineers contributed to the development of the DevOps Guru for RDS findings, which encapsulate many years of managing hundreds of thousands of databases\. By drawing on this collective knowledge, DevOps Guru for RDS can teach you best practices\.

## How DevOps Guru for RDS works<a name="devops-guru-for-rds.how-it-works"></a>

DevOps Guru for RDS collects data about your RDS for PostgreSQL databases from Amazon RDS Performance Insights\. The most important metric is `DBLoad`\. DevOps Guru for RDS consumes the Performance Insights metrics, analyzes them with machine learning, and publishes insights to the dashboard\.

An *insight* is a collection of related anomalies that were detected by DevOps Guru\.

In DevOps Guru for RDS, an *anomaly* is a pattern that deviates from what is considered normal performance for your RDS for PostgreSQL database\. 

### Proactive insights<a name="devops-guru-for-rds.how-it-works.insights.proactive"></a>

A *proactive insight* lets you know about problematic behavior before it occurs\. It contains anomalies with recommendations and related metrics to help you address issues in your RDS for PostgreSQL databases before become bigger problems\. These insights are published in the DevOps Guru dashboard\.

For example, DevOps Guru might detect that your RDS for PostgreSQL database is creating many on\-disk temporary tables\. If not addressed, this trend might lead to performance issues\. Each proactive insight includes recommendations for corrective behavior and links to relevant topics in [Tuning RDS for PostgreSQL with Amazon DevOps Guru proactive insights](PostgreSQL.Tuning_proactive_insights.md)\. For more information, see [Working with insights in DevOps Guru](https://docs.aws.amazon.com/devops-guru/latest/userguide/working-with-insights.html) in the *Amazon DevOps Guru User Guide*\. 

### Reactive insights<a name="devops-guru-for-rds.how-it-works.insights.reactive"></a>

A *reactive insight* identifies anomalous behavior as it occurs\. If DevOps Guru for RDS finds performance issues in your RDS for PostgreSQL DB instances, it publishes a reactive insight in the DevOps Guru dashboard\. For more information, see [Working with insights in DevOps Guru](https://docs.aws.amazon.com/devops-guru/latest/userguide/working-with-insights.html) in the *Amazon DevOps Guru User Guide*\.

#### Causal anomalies<a name="devops-guru-for-rds.how-it-works.anomalies.causal"></a>

A *causal anomaly* is a top\-level anomaly within a reactive insight\. **Database load \(DB load\)** is the causal anomaly for DevOps Guru for RDS\. 

An anomaly measures performance impact by assigning a severity level of **High**, **Medium**, or **Low**\. To learn more, see [Key concepts for DevOps Guru for RDS](https://docs.aws.amazon.com/devops-guru/latest/userguide/working-with-rds.overview.definitions.html) in the *Amazon DevOps Guru User Guide*\.

If DevOps Guru detects a current anomaly on your DB instance, you're alerted in the **Databases** page of the RDS console\. The console also alerts you to anomalies that occurred in the past 24 hours\. To go to the anomaly page from the RDS console, choose the link in the alert message\. The RDS console also alerts you in the page for your RDS for PostgreSQL DB instance\.

#### Contextual anomalies<a name="devops-guru-for-rds.how-it-works.anomalies.contextual"></a>

A *contextual anomaly* is a finding within **Database load \(DB load\)** that is related to a reactive insight\. Each contextual anomaly describes a specific RDS for PostgreSQL performance issue that requires investigation\. For example, DevOps Guru for RDS might recommend that you consider increasing CPU capacity or investigate wait events that are contributing to DB load\.

**Important**  
We recommend that you test any changes on a test instance before modifying a production instance\. In this way, you understand the impact of the change\.

To learn more, see [Analyzing anomalies in Amazon RDS](https://docs.aws.amazon.com/devops-guru/latest/userguide/working-with-rds.analyzing.html) in the *Amazon DevOps Guru User Guide*\.

## Setting up DevOps Guru for RDS<a name="devops-guru-for-rds.configuring"></a>

To allow DevOps Guru for Amazon RDS to publish insights for a RDS for PostgreSQL database, complete the following tasks\.

**Topics**
+ [Configuring IAM access policies for DevOps Guru for RDS](#devops-guru-for-rds.configuring.access)
+ [Turning on Performance Insights for your RDS for PostgreSQL DB instances](#devops-guru-for-rds.configuring.performance-insights)
+ [Turning on DevOps Guru and specifying resource coverage](#devops-guru-for-rds.configuring.coverage)

### Configuring IAM access policies for DevOps Guru for RDS<a name="devops-guru-for-rds.configuring.access"></a>

To view alerts from DevOps Guru in the RDS console, your AWS Identity and Access Management \(IAM\) user or role must have either of the following policies:
+ The AWS managed policy `AmazonDevOpsGuruConsoleFullAccess`
+ The AWS managed policy `AmazonDevOpsGuruConsoleReadOnlyAccess` and either of the following policies:
  + The AWS managed policy `AmazonRDSFullAccess`
  + A customer managed policy that includes `pi:GetResourceMetrics` and `pi:DescribeDimensionKeys`

For more information, see [Configuring access policies for Performance Insights](USER_PerfInsights.access-control.md)\.

### Turning on Performance Insights for your RDS for PostgreSQL DB instances<a name="devops-guru-for-rds.configuring.performance-insights"></a>

DevOps Guru for RDS relies on Performance Insights for its data\. Without Performance Insights, DevOps Guru publishes anomalies, but doesn't include the detailed analysis and recommendations\.

When you create or modify a RDS for PostgreSQL DB instance, you can turn on Performance Insights\. For more information, see [Turning Performance Insights on and off](USER_PerfInsights.Enabling.md)\.

### Turning on DevOps Guru and specifying resource coverage<a name="devops-guru-for-rds.configuring.coverage"></a>

You can turn on DevOps Guru to have it monitor your  RDS for PostgreSQL databases in either of the following ways\.

**Topics**
+ [Turning on DevOps Guru in the RDS console](#devops-guru-for-rds.configuring.coverage.rds-console)
+ [Adding RDS for PostgreSQL resources in the DevOps Guru console](#devops-guru-for-rds.configuring.coverage.guru-console)
+ [Adding RDS for PostgreSQL resources using AWS CloudFormation](#devops-guru-for-rds.configuring.coverage.cfn)

#### Turning on DevOps Guru in the RDS console<a name="devops-guru-for-rds.configuring.coverage.rds-console"></a>

You can take multiple paths in the Amazon RDS console to turn on DevOps Guru\.

**Topics**
+ [Turning on DevOps Guru when you create an RDS for PostgreSQL database](#devops-guru-for-rds.configuring.coverage.rds-console.create)
+ [Turning on DevOps Guru from the notification banner](#devops-guru-for-rds.configuring.coverage.rds-console.existing)
+ [Responding to a permissions error when you turn on DevOps Guru](#devops-guru-for-rds.configuring.coverage.rds-console.error)

##### Turning on DevOps Guru when you create an RDS for PostgreSQL database<a name="devops-guru-for-rds.configuring.coverage.rds-console.create"></a>

The creation workflow includes a setting that turns on DevOps Guru coverage for your database\. This setting is turned on by default when you choose the **Production** template\.

**To turn on DevOps Guru when you create an RDS for PostgreSQL database**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. Follow the steps in [Creating a DB instance](USER_CreateDBInstance.md#USER_CreateDBInstance.Creating), up to but not including the step where you choose monitoring settings\.

1. In **Monitoring**, choose **Turn on Performance Insights**\. For DevOps Guru for RDS to provide detailed analysis of performance anomalies, Performance Insights must be turned on\.

1. Choose **Turn on DevOps Guru**\.  
![\[Turn on DevOps Guru when you create a DB instance\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/devops-guru-enable-create.png)

1. Create a tag for your database so that DevOps Guru can monitor it\. Do the following:
   + In the text field for **Tag key**, enter a name that begins with **Devops\-Guru\-**\.
   + In the text field for **Tag value**, enter any value\. For example, if you enter **rds\-database\-1** for the name of your RDS for PostgreSQL database, you can also enter **rds\-database\-1** as the tag value\.

   For more information about tags, see "[Use tags to identify resources in your DevOps Guru applications](https://docs.aws.amazon.com/devops-guru/latest/userguide/working-with-resource-tags.html)" in the *Amazon DevOps Guru User Guide*\.

1. Complete the remaining steps in [Creating a DB instance](USER_CreateDBInstance.md#USER_CreateDBInstance.Creating)\.

##### Turning on DevOps Guru from the notification banner<a name="devops-guru-for-rds.configuring.coverage.rds-console.existing"></a>

If your resources aren't covered by DevOps Guru, Amazon RDS notifies you with a banner in the following locations:
+ The **Monitoring** tab of a DB cluster instance
+ The Performance Insights dashboard

![\[DevOps Guru banner\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/devops-guru-enable-banner.png)

**To turn on DevOps Guru for your RDS for PostgreSQL database**

1. In the banner, choose **Turn on DevOps Guru for RDS**\. 

1. Enter a tag key name and value\. For more information about tags, see "[Use tags to identify resources in your DevOps Guru applications](https://docs.aws.amazon.com/devops-guru/latest/userguide/working-with-resource-tags.html)" in the *Amazon DevOps Guru User Guide*\.  
![\[Turn on DevOps Guru in the RDS console\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/devops-guru-turn-on.png)

1. Choose **Turn on DevOps Guru**\.

##### Responding to a permissions error when you turn on DevOps Guru<a name="devops-guru-for-rds.configuring.coverage.rds-console.error"></a>

If you turn on DevOps Guru from the RDS console when you create a database, RDS might display the following banner about missing permissions\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/devops-guru-permissions-error.png)

**To respond to a permissions error**

1. Grant your IAM user or role the user managed role `AmazonDevOpsGuruConsoleFullAccess`\. For more information, see [Configuring IAM access policies for DevOps Guru for RDS](#devops-guru-for-rds.configuring.access)\.

1. Open the RDS console\.

1. In the navigation pane, choose **Performance Insights**\.

1. Choose a DB instance in the cluster that you just created\.

1. Turn on **DevOps Guru for RDS**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/devops-guru-pi-toggle-off.png)

1. Choose a tag value\. For more information, see "[Use tags to identify resources in your DevOps Guru applications](https://docs.aws.amazon.com/devops-guru/latest/userguide/working-with-resource-tags.html)" in the *Amazon DevOps Guru User Guide*\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/devops-guru-turn-on.png)

1. Choose **Turn on DevOps Guru**\.

#### Adding RDS for PostgreSQL resources in the DevOps Guru console<a name="devops-guru-for-rds.configuring.coverage.guru-console"></a>

You can specify your DevOps Guru resource coverage on the DevOps Guru console\. Follow the step described in [Specify your DevOps Guru resource coverage](https://docs.aws.amazon.com/devops-guru/latest/userguide/choose-coverage.html) in the *Amazon DevOps Guru User Guide*\. When you edit your analyzed resources, choose one of the following options:
+ Choose **All account resources** to analyze all supported resources, including the RDS for PostgreSQL databases, in your AWS account and Region\.
+ Choose **CloudFormation stacks** to analyze the RDS for PostgreSQL databases that are in stacks you choose\. For more information, see [Use AWS CloudFormation stacks to identify resources in your DevOps Guru applications](https://docs.aws.amazon.com/devops-guru/latest/userguide/working-with-cfn-stacks.html) in the *Amazon DevOps Guru User Guide*\.
+ Choose **Tags** to analyze the RDS for PostgreSQL databases that you have tagged\. For more information, see [Use tags to identify resources in your DevOps Guru applications](https://docs.aws.amazon.com/devops-guru/latest/userguide/working-with-resource-tags.html) in the *Amazon DevOps Guru User Guide*\.

For more information, see [Enable DevOps Guru](https://docs.aws.amazon.com/devops-guru/latest/userguide/getting-started-enable-service.html) in the *Amazon DevOps Guru User Guide*\.

#### Adding RDS for PostgreSQL resources using AWS CloudFormation<a name="devops-guru-for-rds.configuring.coverage.cfn"></a>

You can use tags to add coverage for your RDS for PostgreSQL resources to your CloudFormation templates\. The following procedure assumes that you have a CloudFormation template both for your RDS for PostgreSQL DB instance and DevOps Guru stack\.

**To specify an RDS for PostgreSQL DB instance using a CloudFormation tag**

1. In the CloudFormation template for your DB instance, define a tag using a key/value pair\.

   The following example assigns the value `my-db-instance1` to `Devops-guru-cfn-default` for an RDS for PostgreSQL DB instance\.

   ```
   MyDBInstance1:
     Type: "AWS::RDS::DBInstance"
     Properties:
       DBInstanceIdentifier: my-db-instance1
       Tags:
         - Key: Devops-guru-cfn-default
           Value: devopsguru-my-db-instance1
   ```

1. In the CloudFormation template for your DevOps Guru stack, specify the same tag in your resource collection filter\.

   The following example configures DevOps Guru to provide coverage for the resource with the tag value `my-db-instance1`\.

   ```
   DevOpsGuruResourceCollection:
     Type: AWS::DevOpsGuru::ResourceCollection
     Properties:
       ResourceCollectionFilter:
         Tags:
           - AppBoundaryKey: "Devops-guru-cfn-default"
             TagValues:
             - "devopsguru-my-db-instance1"
   ```

   The following example provides coverage for all resources within the application boundary `Devops-guru-cfn-default`\.

   ```
   DevOpsGuruResourceCollection:
     Type: AWS::DevOpsGuru::ResourceCollection
     Properties:
       ResourceCollectionFilter:
         Tags:
           - AppBoundaryKey: "Devops-guru-cfn-default"
             TagValues:
             - "*"
   ```

For more information, see [AWS::DevOpsGuru::ResourceCollection](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-devopsguru-resourcecollection.html) and [AWS::RDS::DBInstance](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-rds-dbinstance.html) in the *AWS CloudFormation User Guide*\.