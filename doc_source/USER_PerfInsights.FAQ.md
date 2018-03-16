# Frequently Asked Questions<a name="USER_PerfInsights.FAQ"></a>

**Q: On which instance sizes will Performance Insights be available?**

A: All nonmicro instance sizes\. As RDS introduces new instance sizes, Performance Insights will be made available on those, unless they have limited performance such as current \*\.nano and \*\.micro instance sizes\.

**Q: When will Performance Insights be available for RDS for PostgreSQL, Aurora MySQL, RDS for MySQL, RDS for Oracle, RDS for SQL Server, and RDS for MariaDB?**

A: Performance Insights will be initially available on Aurora PostgreSQL, followed soon after by Aurora MySQL\. Additional engines will be added over time\.

**Q: How does Performance Insights show the cause of performance problems?**

A: Performance problems appear in the Performance Insights section of the RDS console as spikes in the database load graph\. One look at this graph can quickly tell you what type of resources your application has been spending time and resources on in the database\. Using the console, you can zoom in to any period within the retention time\. By selecting the periods of high load, customers can display a list of SQL statements, ordered by overall contribution to load\.

**Q: How does Performance Insights know about load in my RDS DB instance?**

A: Performance Insights collects the state of all of the connected sessions in your DB instance\.  If a session is spending time in a database\-related operation, Performance Insights records the time, the type of operation \(I/O, CPU, locking, and so on\), the current SQL statement and several other session attributes\.  Over periods of time, this data is used to characterize how sessions are contributing to load in your database instance\.

**Q: Can performance data be queried from inside the RDS instance?**

A: No\. Performance Insights does not populate any tables in the database, nor does it present data to be retrieved from within the database via SQL\.

**Q: Can I see what is happening on my instance in real time?**

A: Yes\. By default, Performance Insights displays a moving one hour window of performance data\. The feature is designed to present the latest performance information within a few seconds of real time\.

**Q: How much does Performance Insights cost?**

A: Performance Insights will include 24 hours of retained data and console access only\)\. Performance Insights while in preview, offers a free tier that includes a trailing 24 hours of performance data retention\. Pricing for longer term data retention will be published at GA\. 

**Q: How far back can I look at performance data stored in Performance Insights?**

A: You can see 24 hours of performance history\. Options for longer retention will be made available in the months to come\. 

**Q: Can I turn off Performance Insights on new instances, even though it is enabled by default?**

A: Yes\. The option for Performance Insights is selected by default in the AWS Management Console when you use the instance creation wizard\. You can de\-select the option in the wizard to prevent Performance Insights from being enabled\. You can also disable Performance Insights in an enabled instance by modifying the instance\.

**Q: Does Performance Insights work on RDS database instances using encrypted storage?**

A: Yes\. Performance Insights doesn't read the data you store in your database\.

**Q: What is DB load and why is it the primary measure used in Performance Insights to detect performance issues?**

A: DB load is a time series showing how much time a customer’s applications are spending in the database, and how they are spending that time\. DB load is measured in units of average active sessions \(AAS\)\. An *active session* is a connection \(session\) that has submitted work to the database engine and is waiting for a response from it\. For example, if you submit a SQL statement to a database instance, that session is considered “active” during the time that the instance is processing that query\. By counting the number of sessions active in an instance at a given moment, we can provide a metric\. This metric, averaged over time periods, can show how busy an instance is, and how much time sessions are spending waiting for the instance to respond\. We call this metric DB load\. Performance Insights collects DB load time series data from the monitored instance, encrypts and aggregates it to be displayed in the **DB Load** chart\. In the console, you can select the time frame you want to view\.

**Q: Do I have to do anything special to my database to enable Performance Insights?**

A: No\. However, Performance Insights works even better on some database engines when additional performance tracking is enabled\. For instance, when the `pg_stat_statement` extension is enabled on RDS PostgreSQL or Aurora PostgreSQL, Performance Insights uses the PostgreSQL\-native SQL identifier to identify the statement\. It can then collect the full text of longer statements\. In MySQL, enabling the Performance Schema allows Performance Insights to collect much richer and deeper detail on wait events affecting the database\.

**Q: Does enabling Performance Insights affect my database performance?**

A: The Performance Insights agent is designed to stay out of the way of your database workloads\. Performance Insights runs at a lower priority than the other processes on your instance, and monitors the health of the host and database\. When Performance Insights detects heavy load or depleted resources, it backs off from the usual frequency of data gathering\. It still collects data, but only when it is safe to do so\. Database options, such as `pg_stat_statement` in RDS PostgreSQL and Aurora PostgreSQL, and Performance Schema in MySQL can use some database resources and potentially affect performance\. Whether enabling these options affects a particular system depends on the application workload\. AWS recommends testing any database options against your workload before enabling them on a production system\.

**Q: Should I keep using Enhanced Monitoring or just use Performance Insights?**

A: Customers using Enhanced Monitoring to monitor operating system metrics should continue to obtain that data by using Enhanced Monitoring\. In the months to come, that data, and also an extensive collection of database metrics, will also become available through the Performance Insights console and an API\. At that point, customers will be able to obtain all performance data from Performance Insights\. Enhanced Monitoring will remain available for those customers who prefer to use it, but we will encourage customers to standardize their database monitoring on Performance Insights\. 

**Q: Is the data stored in Performance Insights encrypted?**

A: Yes\. Performance Insights encrypts all potentially sensitive data using your own AWS Key Management Service \(AWS KMS\) key\. Data is encrypted in flight and at rest\. AWS personnel cannot access or see any potentially sensitive performance data\. Only your users on your AWS account with full access to RDS can view Performance Insights\. You can revoke the RDS grant for your KMS key, which enables us to process and display your performance data, at any time\.

**Q: If I turn off Performance Insights, does AWS retain the data or is it deleted?**

A: Performance data retention free tier is restricted to one day\. Disabling Performance Insights on an instance causes your performance data for that instance to be deleted\.

**Q: What happens to Performance Insights data retention when I stop my RDS database instance?**

A: Stopping an RDS instance that has Performance Insights enabled has no effect on retention or visibility of historical data for that instance\. The period during which the instance was stopped will simply contain no data\.

**Q: How would I interface Performance Insights with my existing performance tools?** 

A: In the coming months, Performance Insights will expose a publicly available API designed to enable customers and third parties to take advantage of the valuable data in Performance Insights\.

**Q: Is there any way for third\-party performance tools to integrate with Performance Insights?**

A: In the coming months, Performance Insights will expose a publicly available API designed to enable customers and third parties to take advantage of the valuable data in Performance Insights\.

**Q: Will Performance Insights be available in all the AWS Regions that RDS is?**

A: Yes\. Performance Insights will initially be available in four AWS Regions: US East \(N\. Virginia, Ohio\), US West \(Oregon\), and EU \(Ireland\)\. Over time, the feature will be made available in all AWS Regions where RDS is supported\.

**Q: Can I turn on Performance Insights on existing instances or only on new ones?**

A: Performance Insights can be enabled on existing instances by modifying the instance to enable Performance Insights\. It can be enabled on new instances by specifying that Performance Insights should be enabled when creating the instance\.

**Q: Does Performance Insights use any of the storage on my database instance?**

A: No, Performance Insights doesn't consume storage space on your RDS instances\.

**Q: How will Performance Insights be different, if at all, when running against different database engines?**

A: Performance Insights is designed to present a common approach, look, and feel to tuning across all database engines in RDS\. Because certain attributes like wait events and SQL identifiers vary by engine type, these also vary in Performance Insights when working with different database engines\. One of the core tenets of Performance Insights is that existing concepts, identifiers, and attributes in a database engine should remain intact\. Performance Insights generally doesn't re\-interpret or rename wait events and other engine\-specific attributes\. Instead, it presents them faithfully as reported by the database engine\.

**Q: Does Performance Insights work on Multi\-AZ instances and Read Replica instances?**

A: Yes\. If an RDS database uses Multi\-AZ and has Performance Insights enabled, then Performance Insights remains enabled when and if that instance fails over to the other Availability Zone\. Because Read Replicas are independent instances, customers can either enable or disable Performance Insights on those instances\.

**Q: Can I export my data from Performance Insights?**

A: In the coming months, Performance Insights will be adding functionality to export data\. 

**Q: Can I re\-import my data to Performance Insights later in order to do performance analysis?**

A: No\. Performance Insights only shows data that has been collected directly from an instance\. Data obtained through Performance Insights is available in the months to come by using an API\. At that point, you will be able to perform analysis using one of the analytics\-oriented services in AWS, such as Amazon Athena, Amazon Redshift, Amazon Redshift Spectrum, and Amazon QuickSight\.