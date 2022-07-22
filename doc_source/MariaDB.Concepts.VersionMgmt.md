# MariaDB on Amazon RDS versions<a name="MariaDB.Concepts.VersionMgmt"></a>

For MariaDB, version numbers are organized as version X\.Y\.Z\. In Amazon RDS terminology, X\.Y denotes the major version, and Z is the minor version number\. For Amazon RDS implementations, a version change is considered major if the major version number changes, for example going from version 10\.5 to 10\.6\. A version change is considered minor if only the minor version number changes, for example going from version 10\.6\.5 to 10\.6\.7\. 

**Topics**
+ [Supported MariaDB versions on Amazon RDS](#MariaDB.Concepts.VersionMgmt.Supported)
+ [RDS for MariaDB release calendar](#MariaDB.Concepts.VersionMgmt.ReleaseCalendar)
+ [MariaDB 10\.2 end of life](#MariaDB.Concepts.VersionMgmt.EndOfLife102)
+ [Deprecated versions for Amazon RDS for MariaDB](#MariaDB.Concepts.DeprecatedVersions)

## Supported MariaDB versions on Amazon RDS<a name="MariaDB.Concepts.VersionMgmt.Supported"></a>

Amazon RDS currently supports the following versions of MariaDB: 


****  

| Major version | Minor version | 
| --- | --- | 
| MariaDB 10\.6 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/MariaDB.Concepts.VersionMgmt.html)  | 
| MariaDB 10\.5 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/MariaDB.Concepts.VersionMgmt.html)  | 
| MariaDB 10\.4 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/MariaDB.Concepts.VersionMgmt.html)  | 
| MariaDB 10\.3 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/MariaDB.Concepts.VersionMgmt.html)  | 
| MariaDB 10\.2 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/MariaDB.Concepts.VersionMgmt.html)  | 

You can specify any currently supported MariaDB version when creating a new DB instance\. You can specify the major version \(such as MariaDB 10\.5\), and any supported minor version for the specified major version\. If no version is specified, Amazon RDS defaults to a supported version, typically the most recent version\. If a major version is specified but a minor version is not, Amazon RDS defaults to a recent release of the major version you have specified\. To see a list of supported versions, as well as defaults for newly created DB instances, use the [https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-engine-versions.html](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-engine-versions.html) AWS CLI command\.

The default MariaDB version might vary by AWS Region\. To create a DB instance with a specific minor version, specify the minor version during DB instance creation\. You can determine the default minor version for an AWS Region using the following AWS CLI command:

```
aws rds describe-db-engine-versions --default-only --engine mariadb --engine-version major-engine-version --region region --query "*[].{Engine:Engine,EngineVersion:EngineVersion}" --output text
```

Replace *major\-engine\-version* with the major engine version, and replace *region* with the AWS Region\. For example, the following AWS CLI command returns the default MariaDB minor engine version for the 10\.5 major version and the US West \(Oregon\) AWS Region \(us\-west\-2\):

```
aws rds describe-db-engine-versions --default-only --engine mariadb --engine-version 10.5 --region us-west-2 --query "*[].{Engine:Engine,EngineVersion:EngineVersion}" --output text
```

## RDS for MariaDB release calendar<a name="MariaDB.Concepts.VersionMgmt.ReleaseCalendar"></a>

RDS for MariaDB major versions remain available at least until community end of life for the corresponding community version\. You can use the following dates to plan your testing and upgrade cycles\. If Amazon extends support for an RDS for MariaDB version for longer than originally stated, we plan to update this table to reflect the later date\. 

**Note**  
Dates with only a month and a year are approximate and are updated with an exact date when it’s known\.


****  

| MariaDB major version | Community release date | RDS release date | Community end of life date | RDS end of standard support date | 
| --- | --- | --- | --- | --- | 
|  MariaDB 10\.6 Current minor version: 10\.6\.8  | 6 July 2021 | 3 February 2022 | 6 July 2026 | July 2026 | 
|  MariaDB 10\.5 Current minor version: 10\.5\.16  | 24 June 2020 | 21 January 2021 | 24 June 2025 | June 2025 | 
|  MariaDB 10\.4 Current minor version: 10\.4\.25  | 18 June 2019 | 6 April 2020 | 18 June 2024 | June 2024 | 
|  MariaDB 10\.3 Current minor version: 10\.3\.35  | 25 May 2018 | 23 October 2018 | 25 May 2023 | October 2023 | 
|  MariaDB 10\.2 Current minor version: 10\.2\.44  | 23 May 2017 | 5 Jan 2018 | 23 May 2022 | 15 Oct 2022 | 

## MariaDB 10\.2 end of life<a name="MariaDB.Concepts.VersionMgmt.EndOfLife102"></a>

On October 15, 2022, Amazon RDS is starting the end\-of\-life process for MariaDB version 10\.2 using the following schedule, which includes upgrade recommendations\. The end\-of\-life process ends standard support for this version\. We recommend that you upgrade all MariaDB 10\.2 DB instances to MariaDB 10\.3 or higher as soon as possible\. For more information, see [Upgrading the MariaDB DB engine](USER_UpgradeDBInstance.MariaDB.md)\.


| Action or recommendation | Dates | 
| --- | --- | 
|  We recommend that you upgrade MariaDB 10\.2 DB instances manually to the version of your choice\. You can upgrade directly to MariaDB version 10\.3 or 10\.6\.  |  Now–October 15, 2022  | 
|  We recommend that you upgrade MariaDB 10\.2 snapshots manually to the version of your choice\.  |  Now–October 15, 2022  | 
|  You can no longer create new MariaDB 10\.2 DB instances\. You can still create read replicas of existing MariaDB 10\.2 DB instances and change them from Single\-AZ deployments to Multi\-AZ deployments\.  |  July 15, 2022  | 
|  Amazon RDS starts automatic upgrades of your MariaDB 10\.2 DB instances to version 10\.3\.  |  October 15, 2022  | 
|  Amazon RDS starts automatic upgrades to version 10\.3 for any MariaDB 10\.2 DB instances restored from snapshots\.  |  October 15, 2022  | 

For more information about Amazon RDS for MariaDB 10\.2 end of life, see [ Announcement: Amazon Relational Database Service \(Amazon RDS\) for MariaDB 10\.2 End\-of\-Life date is October 15, 2022](https://repost.aws/questions/QUPGswEbHrT0m4tNgAVNmssw/announcement-amazon-relational-database-service-amazon-rds-for-maria-db-10-2-end-of-life-date-is-october-15-2022)\.

## Deprecated versions for Amazon RDS for MariaDB<a name="MariaDB.Concepts.DeprecatedVersions"></a>

Amazon RDS for MariaDB version 10\.0 and 10\.1 are deprecated\.

For information about the Amazon RDS deprecation policy for MariaDB, see [Amazon RDS FAQs](http://aws.amazon.com/rds/faqs/)\.