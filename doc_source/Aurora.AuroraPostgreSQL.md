# Working with Amazon Aurora PostgreSQL<a name="Aurora.AuroraPostgreSQL"></a>

Amazon Aurora PostgreSQL is a fully managed, PostgreSQL\-compatible, relational database engine that combines the speed and reliability of high\-end commercial databases with the simplicity and cost\-effectiveness of open\-source databases\. Aurora PostgreSQL is a drop\-in replacement for PostgreSQL and makes it simple and cost\-effective to set up, operate, and scale your new and existing PostgreSQL deployments, thus freeing you to focus on your business and applications\. Amazon RDS provides administration for Aurora by handling routine database tasks such as provisioning, patching, backup, recovery, failure detection, and repair\. Amazon RDS also provides push\-button migration tools to convert your existing Amazon RDS for PostgreSQL applications to Aurora PostgreSQL\.

Aurora PostgreSQL can work with many industry standards\. For example, you can use Aurora PostgreSQL databases to build HIPAA\-compliant applications and to store healthcare related information, including protected health information \(PHI\), under an executed Business Associate Agreement \(BAA\) with AWS\. For more information about BAAs with AWS, see [HIPAA Compliance](https://aws.amazon.com/compliance/hipaa-compliance/)\.

## Availability for Amazon Aurora PostgreSQL<a name="Aurora.AuroraPostgreSQL.Availability"></a>

The following table shows the regions where Aurora PostgreSQL is currently available\.


| Region | Console Link | 
| --- | --- | 
| US East \(Ohio\) | [https://console\.aws\.amazon\.com/rds/home?region=us\-east\-2](https://console.aws.amazon.com/rds/home?region=us-east-2) | 
| US East \(N\. Virginia\) | [https://console\.aws\.amazon\.com/rds/home?region=us\-east\-1](https://console.aws.amazon.com/rds/home?region=us-east-1) | 
| US West \(Oregon\) | [https://console\.aws\.amazon\.com/rds/home?region=us\-west\-2](https://console.aws.amazon.com/rds/home?region=us-west-2) | 
| Asia Pacific \(Mumbai\) | [https://console\.aws\.amazon\.com/rds/home?region=ap\-south\-1](https://console.aws.amazon.com/rds/home?region=ap-south-1) | 
| Asia Pacific \(Sydney\) | [https://console\.aws\.amazon\.com/rds/home?region=ap\-southeast\-2](https://console.aws.amazon.com/rds/home?region=ap-southeast-2) | 
| Canada \(Central\) | [https://console\.aws\.amazon\.com/rds/home?region=ca\-central\-1](https://console.aws.amazon.com/rds/home?region=ca-central-1) | 
| EU \(Frankfurt\) | [https://console\.aws\.amazon\.com/rds/home?region=eu\-central\-1](https://console.aws.amazon.com/rds/home?region=eu-central-1) | 
| EU \(Ireland\) | [https://console\.aws\.amazon\.com/rds/home?region=eu\-west\-1](https://console.aws.amazon.com/rds/home?region=eu-west-1) | 

## Comparison of Amazon Aurora PostgreSQL and Amazon RDS for PostgreSQL<a name="Aurora.AuroraPostgreSQL.Compare"></a>

Although Aurora instances are compatible with PostgreSQL client applications, Aurora has advantages over PostgreSQL and also limitations to the PostgreSQL features that Aurora supports\. This functionality can influence your decision about whether Amazon Aurora PostgreSQL or PostgreSQL on Amazon RDS is the best cloud database for your solution\. The following table shows the differences between Amazon Aurora PostgreSQL and Amazon RDS for PostgreSQL\.


| Feature | Amazon Aurora PostgreSQL | Amazon RDS for PostgreSQL | 
| --- | --- | --- | 
| wal2json plugin support | Currently, the plugin is not supported\. | The plugin is supported\. | 