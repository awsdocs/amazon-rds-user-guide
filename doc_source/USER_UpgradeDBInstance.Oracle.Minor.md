# Oracle minor version upgrades<a name="USER_UpgradeDBInstance.Oracle.Minor"></a>

A minor version upgrade applies an Oracle Database Patch Set Update \(PSU\) or Release Update \(RU\) in a major version\. 

An Amazon RDS for Oracle DB instance is scheduled to be upgraded automatically during its next maintenance window when it meets the following conditions:
+ The DB instance has the **Auto minor version upgrade** option enabled\.
+ The DB instance isn't running the latest minor DB engine version\.

For most AWS Regions, RDS upgrades your DB instance to the latest quarterly RU or RUR approximately four to six weeks after RDS makes it available\. The scheduled date varies depending on the AWS Region and other factors\. For more information about RUs and RURs, see [https://docs.aws.amazon.com/AmazonRDS/latest/OracleReleaseNotes/Welcome.html](https://docs.aws.amazon.com/AmazonRDS/latest/OracleReleaseNotes/Welcome.html)\.

**Note**  
RDS for Oracle doesn't support minor version downgrades\.