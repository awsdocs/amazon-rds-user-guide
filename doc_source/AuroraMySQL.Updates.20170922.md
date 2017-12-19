# Amazon Aurora MySQL Database Engine Updates: 2017\-09\-22<a name="AuroraMySQL.Updates.20170922"></a>

**Version:** 1\.14\.1

Amazon Aurora MySQL v1\.14\.1 is generally available\. All new database clusters, including those restored from snapshots, will be created in Aurora MySQL v1\.14\.1\. Aurora MySQL v1\.14\.1 is also a mandatory upgrade for existing Aurora MySQL DB clusters\. For more information, see [Announcement: Extension to Mandatory Upgrade Schedule for Amazon Aurora](https://forums.aws.amazon.com/ann.jspa?annID=4983) on the AWS Developer Forums website\.

With version 1\.14\.1 of Aurora MySQL, we are using a cluster patching model where all nodes in an Aurora MySQL DB cluster are patched at the same time\. Updates require a database restart, so you will experience 20 to 30 seconds of downtime, after which you can resume using your DB cluster or clusters\. If your DB clusters are currently running version 1\.13 or greater, Aurora MySQL's zero\-downtime patching feature may allow client connections to your Aurora MySQL primary instance to persist through the upgrade, depending on your workload\.

Should you have any questions or concerns, the AWS Support Team is available on the community forums and through AWS Premium Support at [http://aws\.amazon\.com/support](http://aws.amazon.com/support)\.

## Improvements<a name="AuroraMySQL.Updates.20170922.Improvements"></a>

+ Fixed race conditions associated with inserts and purge to improve the stability of the Fast DDL feature, which remains in Aurora MySQL lab mode\.