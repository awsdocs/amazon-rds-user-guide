# Tutorial: Creating an Amazon CloudWatch alarm for Multi\-AZ DB cluster replica lag<a name="multi-az-db-cluster-cloudwatch-alarm"></a>

You can create an Amazon CloudWatch alarm that sends an Amazon SNS message when replica lag for a Multi\-AZ DB cluster has exceeded a threshold\. An alarm watches the `ReplicaLag` metric over a time period that you specify\. The action is a notification sent to an Amazon SNS topic or Amazon EC2 Auto Scaling policy\.

**To set a CloudWatch alarm for Multi\-AZ DB cluster replica lag**

1. Sign in to the AWS Management Console and open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Alarms**, **All alarms**\.

1. Choose **Create alarm**\.

1. On the **Specify metric and conditions** page, choose **Select metric**\.

1. In the search box, enter the name of your Multi\-AZ DB cluster and press Enter\.

   The following image shows the **Select metric** page with a Multi\-AZ DB cluster named `rds-cluster` entered\.  
![\[Select metric page\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/multi-az-db-cluster-cw-tutorial-select-metric.png)

1. Choose **RDS**, **Per\-Database Metrics**\.

1. In the search box, enter **ReplicaLag** and press Enter, then select each DB instance in the DB cluster\.

   The following image shows the **Select metric** page with the DB instances selected for the **ReplicaLag** metric\.  
![\[Select metric page with DB instances selected for the ReplicaLag metric\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/multi-az-db-cluster-cw-tutorial-metric-replica-lag.png)

   This alarm considers the replica lag for all three of the DB instances in the Multi\-AZ DB cluster\. The alarm responds when any DB instance exceeds the threshold\. It uses a math expression that returns the maximum value of the three metrics\. Start by sorting by metric name, and then choose all three **ReplicaLag** metrics\.

1. From **Add math**, choose **All functions**, **MAX**\.  
![\[The Add math setting\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/multi-az-db-cluster-cw-tutorial-select-metric-math.png)

1. Choose the **Graphed metrics** tab, and edit the details for **Expression1** to **MAX\(\[m1,m2,m3\]\)**\.

1. For all three **ReplicaLag** metrics, change the **Period** to **1 minute**\.

1. Clear selection from all metrics except for **Expression1**\.

   The **Select metric** page should look similar to the following image\.  
![\[The Select metric page with the metric selected\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/multi-az-db-cluster-cw-tutorial-select-metric-expression1.png)

1. Choose **Select metric**\.

1. On the **Specify metric and conditions** page, change the label to a meaningful name, such as **ClusterReplicaLag**, and enter a number of seconds in **Define the threshold value**\. For this tutorial, enter **1200** seconds \(20 minutes\)\. You can adjust this value for your workload requirements\.

   The **Specify metric and conditions** page should look similar to the following image\.  
![\[The Specify metric and conditions page\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/multi-az-db-cluster-cw-tutorial-specify-metric-conditions.png)

1. Choose **Next**, and the **Configure actions** page appears\.

1. Keep **In alarm** selected, choose **Create new topic**, and enter the topic name and a valid email address\.  
![\[The Configure actions page\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/multi-az-db-cluster-cw-tutorial-configure-actions.png)

1. Choose **Create topic**, and then choose **Next**\.

1. On the **Add name and description** page, enter the **Alarm name** and **Alarm description**, and then choose **Next**\.  
![\[The Add name and description page\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/multi-az-db-cluster-cw-tutorial-add-name-and-description.png)

1. Preview the alarm that you're about to create on the **Preview and create** page, and then choose **Create alarm**\. 