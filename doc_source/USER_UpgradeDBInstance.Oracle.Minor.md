# Oracle minor version upgrades<a name="USER_UpgradeDBInstance.Oracle.Minor"></a>

A minor version upgrade applies an Oracle Database Patch Set Update \(PSU\) or Release Update \(RU\) in a major version\. 

An Amazon RDS for Oracle DB instance is scheduled to be upgraded automatically during its next maintenance window when it meets the following conditions:
+ The DB instance has the **Auto minor version upgrade** option enabled\.
+ The DB instance is not running the latest minor DB engine version\.

The DB instance is upgraded to the latest quarterly PSU or RU four to six weeks after it is made available by Amazon RDS for Oracle, although this time interval can sometimes vary\. For more information about PSUs and RUs, see [Oracle database engine release notes](Appendix.Oracle.PatchComposition.md)\. 

The minor version upgrades shown in the following table aren't supported\. 


****  

| Current version | Upgrade not supported | 
| --- | --- | 
| 12\.1\.0\.2\.v6 | 12\.1\.0\.2\.v7 | 
| 12\.1\.0\.2\.v5 | 12\.1\.0\.2\.v7 | 
| 12\.1\.0\.2\.v5 | 12\.1\.0\.2\.v6 | 

**Note**  
Minor version downgrades aren't supported\.