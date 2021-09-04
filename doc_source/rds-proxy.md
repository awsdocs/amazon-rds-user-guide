# Using Amazon RDS Proxy<a name="rds-proxy"></a>

 By using Amazon RDS Proxy, you can allow your applications to pool and share database connections to improve their ability to scale\. RDS Proxy makes applications more resilient to database failures by automatically connecting to a standby DB instance while preserving application connections\. RDS Proxy also enables you to enforce AWS Identity and Access Management \(IAM\) authentication for databases, and securely store credentials in AWS Secrets Manager\. 

**Note**  
 RDS Proxy is fully compatible with MySQL and PostgreSQL\. You can enable RDS Proxy for most applications with no code changes\. 

 Using RDS Proxy, you can handle unpredictable surges in database traffic that otherwise might cause issues due to oversubscribing connections or creating new connections at a fast rate\. RDS Proxy establishes a database connection pool and reuses connections in this pool without the memory and CPU overhead of opening a new database connection each time\. To protect the database against oversubscription, you can control the number of database connections that are created\. 

 RDS Proxy queues or throttles application connections that can't be served immediately from the pool of connections\. Although latencies might increase, your application can continue to scale without abruptly failing or overwhelming the database\. If connection requests exceed the limits you specify, RDS Proxy rejects application connections \(that is, it sheds load\)\. At the same time, it maintains predictable performance for the load that can be served with the available capacity\. 

 You can reduce the overhead to process credentials and establish a secure connection for each new connection\. RDS Proxy can handle some of that work on behalf of the database\. 

**Topics**
+ [RDS Proxy concepts and terminology](rds-proxy.howitworks.md)
+ [Planning for and setting up RDS Proxy](rds-proxy-setup.md)
+ [Connecting to a database through RDS Proxy](rds-proxy-connecting.md)
+ [Managing an RDS Proxy](rds-proxy-managing.md)
+ [Monitoring RDS Proxy using Amazon CloudWatch](rds-proxy.monitoring.md)
+ [Endpoints for Amazon RDS Proxy](rds-proxy-endpoints.md)
+ [Command\-line examples for RDS Proxy](rds-proxy.examples.md)
+ [Troubleshooting for RDS Proxy](rds-proxy.troubleshooting.md)
+ [Using RDS Proxy with AWS CloudFormation](rds-proxy-cfn.md)