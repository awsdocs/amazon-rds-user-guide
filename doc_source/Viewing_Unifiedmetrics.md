# Viewing combined metrics in the Amazon RDS console<a name="Viewing_Unifiedmetrics"></a>

Amazon RDS now provides a consolidated view of Performance Insights and CloudWatch metrics for your DB instance in the Performance Insights dashboard\. You can use the preconfigured dashboard or create a custom dashboard\. The preconfigured dashboard provides the most commonly used metrics to help diagnose performance issues for a database engine\. Alternatively, you can create a custom dashboard with the metrics for a database engine that meet your analysis requirements\. Then, use this dashboard for all the DB instances of that database engine type in your AWS account\. 

You can choose the new monitoring view in the **Monitoring** tab or **Performance Insights** in the navigation pane\. When you navigate to the Performance Insights page, you see the options to choose between the new monitoring view and legacy view\. The option you choose is saved as the default view\.

Performance Insights must be turned on for your DB instance to view the combined metrics in the Performance Insights dashboard\. For more information about turning on Performance Insights, see [Turning Performance Insights on and off](USER_PerfInsights.Enabling.md)\. 

Currently, the consolidated view of Performance Insights and CloudWatch metrics isn't available in the following AWS Regions: China \(Beijing\), China \(Ningxia\), AWS GovCloud \(US\-East\), and AWS GovCloud \(US\-West\)\.

**Note**  
We recommend that you choose the new monitoring view\. You can continue to use the legacy monitoring view until it is discontinued on December 15, 2023\.

## Choosing the new monitoring view in the **Monitoring** tab<a name="Viewing_Unifiedmetrics.MonitoringTab"></a>

**To choose the new monitoring view in the **Monitoring** tab:**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the left navigation pane, choose **Databases**\.

1. Choose the DB instance that you want to monitor\.

   The database page appears\.

1. Scroll down and choose the **Monitoring** tab\.

   A banner appears with the option to choose the new monitoring view\. The following example shows the banner to choose the new monitoring view\.  
![\[Banner with navigation to new monitoring view.\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/NewMonitoringViewOption.png)

1. Choose **Go to new monitoring view** to open the Performance Insights dashboard with Performance Insights and CloudWatch metrics for your DB instance\.

1. \(Optional\) If Performance Insights is turned off for your DB instance, a banner appears with the option to modify your DB cluster and turn on Performance Insights\.

   The following example shows the banner to modify the DB cluster in the **Monitoring** tab \.  
![\[Modify DB instance to turn on Performance Insights.\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/Monitoring_modifyInstnc_banner.png)

   Choose **Modify** to modify your DB cluster and turn on Performance Insights\. For more information about turning on Performance Insights, see [Turning Performance Insights on and off](USER_PerfInsights.Enabling.md)

## Choosing the new monitoring view with **Performance Insights** in the navigation pane<a name="Viewing_Unifiedmetrics.PInavigationPane"></a>

**To choose the new monitoring view with **Performance Insights** in the navigation pane:**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the left navigation pane, choose **Performance Insights**\.

1. Choose a DB instance to open a window that has the monitoring view options\. 

   The following example shows the window with the monitoring view options\.  
![\[Window with the monitoring view options.\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/Monitoring_NewView.png)

1. Choose the **Performance Insights and CloudWatch metrics view \(New\)** option, and then choose **Continue**\.

   You can now view the Performance Insights dashboard that shows both Performance Insights and CloudWatch metrics for your DB instance\. The following example shows the Performance Insights and CloudWatch metrics in the dashboard\.  
![\[Consolidated Performance Insights and CloudWatch metrics dashboard.\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/Monitoring_UnifiedDashboard.png)

## Choosing the legacy view with **Performance Insights** in the navigation pane<a name="Viewing_Unifiedmetrics.SwitchViews"></a>

You can choose the legacy monitoring view to view only the Performance Insights metrics for your DB instance\.

**Note**  
This view will be discontinued on December 15, 2023\.

**To choose the legacy monitoring view with **Performance Insights** in the navigation pane:**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the left navigation pane, choose **Performance Insights**\.

1. Choose a DB instance\.

1. Choose the settings icon on the Performance Insights dashboard\.

   You can now see the **Settings** window that shows the option to choose the legacy Performance Insights view\.

   The following example shows the window with the option for the legacy monitoring view\.  
![\[Window with the option for the legacy Performance Insights view.\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/Monitoring_ModifyViewSettings.png)

1. Select the **Performance Insights view** option and choose **Continue**\.

   A warning message appears\. Any dashboard configurations that you saved won't be available in this view\.

1. Choose **Confirm** to continue to the legacy Performance Insights view\.

   You can now view the Performance Insights dashboard that shows only Performance Insights metrics for the DB instance\.

## Creating a custom dashboard with **Performance Insights** in the navigation pane<a name="Viewing_Unifiedmetrics.PIcustomizeMetricslist"></a>

In the new monitoring view, you can create a custom dashboard with the metrics you need to meet your analysis requirements\.

You can create a custom dashboard by selecting Performance Insights and CloudWatch metrics for your DB instance\. You can use this custom dashboard for other DB instances of the same database engine type in your AWS account\.

**Note**  
The customized dashboard supports up to 50 metrics\.

Use the widget settings menu to edit or delete the dashboard, and move or resize the widget window\.

**To create a custom dashboard with Performance Insights in the navigation pane:**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the left navigation pane, choose **Performance Insights**\.

1. Choose a DB instance\.

1. Scroll down to the **Metrics tab** in the window\.

1. Select the custom dashboard from the drop down list\. The following example shows the custom dashboard creation\.  
![\[Custom metrics dashboard with no widgets yet.\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/Monitoring_custmDashbrd_addWidget.png)

1. Choose **Add widget** to open the **Add widget** window\. You can open and view the available operating system \(OS\) metrics, database metrics, and CloudWatch metrics in the window\.

   The following example shows the **Add widget** window with the metrics\.  
![\[The metrics options in the Add Widget window.\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/Monitoring_AddWidget.png)

1. Select the metrics that you want to view in the dashboard and choose **Add widget**\. You can use the search field to find a specific metric\. 

   The selected metrics appear on your dashboard\.

1. \(Optional\) If you want to modify or delete your dashboard, choose the settings icon on the upper right of the widget, and then select one of the following actions in the menu\.
   + **Edit** – Modify the metrics list in the window\. Choose **Update widget** after you select the metrics for your dashboard\.
   + **Delete** – Deletes the widget\. Choose **Delete** in the confirmation window\. 

## Choosing the preconfigured dashboard with **Performance Insights** in the navigation pane<a name="Viewing_Unifiedmetrics.PI-preconfigured-dashboard"></a>

You can view the most commonly used metrics with the preconfigured dashboard\. This dashboard helps diagnose performance issues with a database engine and reduce the average recovery time from hours to minutes\.

**Note**  
This dashboard can't be edited\.

**To choose the preconfigured dashboard with Performance Insights in the navigation pane:**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the left navigation pane, choose **Performance Insights**\.

1. Choose a DB instance\.

1. Scroll down to the **Metrics tab** in the window

1. Select a preconfigured dashboard from the drop down list\.

   You can view the metrics for the DB instance in the dashboard\. The following example shows a preconfigured metrics dashboard\.  
![\[Preconfigured metrics dashboard.\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/Monitoring_preconfigDashboard.png)