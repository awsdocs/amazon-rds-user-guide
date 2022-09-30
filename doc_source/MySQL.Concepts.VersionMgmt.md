# MySQL on Amazon RDS versions<a name="MySQL.Concepts.VersionMgmt"></a>

For MySQL, version numbers are organized as version = X\.Y\.Z\. In Amazon RDS terminology, X\.Y denotes the major version, and Z is the minor version number\. For Amazon RDS implementations, a version change is considered major if the major version number changes—for example, going from version 5\.7 to 8\.0\. A version change is considered minor if only the minor version number changes—for example, going from version 8\.0\.27 to 8\.0\.30\. 

**Topics**
+ [Supported MySQL versions on Amazon RDS](#MySQL.Concepts.VersionMgmt.Supported)
+ [RDS for MySQL release calendar](#MySQL.Concepts.VersionMgmt.ReleaseCalendar)
+ [Deprecated versions for Amazon RDS for MySQL](#MySQL.Concepts.DeprecatedVersions)

## Supported MySQL versions on Amazon RDS<a name="MySQL.Concepts.VersionMgmt.Supported"></a>

Amazon RDS currently supports the following versions of MySQL: 


****  

| Major version | Minor version | 
| --- | --- | 
| MySQL 8\.0 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/MySQL.Concepts.VersionMgmt.html)  | 
| MySQL 5\.7 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/MySQL.Concepts.VersionMgmt.html)  | 

You can specify any currently supported MySQL version when creating a new DB instance\. You can specify the major version \(such as MySQL 5\.7\), and any supported minor version for the specified major version\. If no version is specified, Amazon RDS defaults to a supported version, typically the most recent version\. If a major version is specified but a minor version is not, Amazon RDS defaults to a recent release of the major version you have specified\. To see a list of supported versions, as well as defaults for newly created DB instances, use the [https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-engine-versions.html](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-engine-versions.html) AWS CLI command\.

For example, to list the supported engine versions for RDS for MySQL, run the following CLI command:

```
aws rds describe-db-engine-versions --engine mysql --query "*[].{Engine:Engine,EngineVersion:EngineVersion}" --output text
```

The default MySQL version might vary by AWS Region\. To create a DB instance with a specific minor version, specify the minor version during DB instance creation\. You can determine the default minor version for an AWS Region using the following AWS CLI command:

```
aws rds describe-db-engine-versions --default-only --engine mysql --engine-version major-engine-version --region region --query "*[].{Engine:Engine,EngineVersion:EngineVersion}" --output text
```

Replace *major\-engine\-version* with the major engine version, and replace *region* with the AWS Region\. For example, the following AWS CLI command returns the default MySQL minor engine version for the 5\.7 major version and the US West \(Oregon\) AWS Region \(us\-west\-2\):

```
aws rds describe-db-engine-versions --default-only --engine mysql --engine-version 5.7 --region us-west-2 --query "*[].{Engine:Engine,EngineVersion:EngineVersion}" --output text
```

With Amazon RDS, you control when to upgrade your MySQL instance to a new major version supported by Amazon RDS\. You can maintain compatibility with specific MySQL versions, test new versions with your application before deploying in production, and perform major version upgrades at times that best fit your schedule\.

When automatic minor version upgrade is enabled, your DB instance will be upgraded automatically to new MySQL minor versions as they are supported by Amazon RDS\. This patching occurs during your scheduled maintenance window\. You can modify a DB instance to enable or disable automatic minor version upgrades\. 

If you opt out of automatically scheduled upgrades, you can manually upgrade to a supported minor version release by following the same procedure as you would for a major version update\. For information, see [Upgrading a DB instance engine version](USER_UpgradeDBInstance.Upgrading.md)\. 

Amazon RDS currently supports the major version upgrades from MySQL version 5\.6 to version 5\.7, and from MySQL version 5\.7 to version 8\.0\. Because major version upgrades involve some compatibility risk, they do not occur automatically; you must make a request to modify the DB instance\. You should thoroughly test any upgrade before upgrading your production instances\. For information about upgrading a MySQL DB instance, see [Upgrading the MySQL DB engine](USER_UpgradeDBInstance.MySQL.md)\. 

You can test a DB instance against a new version before upgrading by creating a DB snapshot of your existing DB instance, restoring from the DB snapshot to create a new DB instance, and then initiating a version upgrade for the new DB instance\. You can then experiment safely on the upgraded clone of your DB instance before deciding whether or not to upgrade your original DB instance\. 

## RDS for MySQL release calendar<a name="MySQL.Concepts.VersionMgmt.ReleaseCalendar"></a>

RDS for MySQL major versions remain available at least until community end of life for the corresponding community version\. You can use the following dates to plan your testing and upgrade cycles\. If Amazon extends support for an RDS for MySQL version for longer than originally stated, we plan to update this table to reflect the later date\. 

**Note**  
Dates with only a month and a year are approximate and are updated with an exact date when it’s known\.


****  

| MySQL major version | Community release date | RDS release date | Community end of life date | RDS end of standard support date | 
| --- | --- | --- | --- | --- | 
|  MySQL 8\.0 Current minor version: 8\.0\.30  | 19 April 2018 | 23 October 2018 | April 2026 | April 2026 | 
|  MySQL 5\.7 Current minor version: 5\.7\.39  | 21 October 2015 | 22 February 2016 | October 2023 | October 2023 | 
|  MySQL 5\.6 Current minor version: N/A  | 5 February 2013 | 1 July 2013 | 5 February 2021 | 1 March 2022 | 

## Deprecated versions for Amazon RDS for MySQL<a name="MySQL.Concepts.DeprecatedVersions"></a>

Amazon RDS for MySQL version 5\.1, 5\.5, and 5\.6 are deprecated\.

For information about the Amazon RDS deprecation policy for MySQL, see [Amazon RDS FAQs](http://aws.amazon.com/rds/faqs/)\.