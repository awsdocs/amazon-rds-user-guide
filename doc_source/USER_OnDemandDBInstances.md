# On\-Demand DB Instances<a name="USER_OnDemandDBInstances"></a>

Amazon RDS on\-demand DB instances are billed based on the class of the DB instance \(for example, db\.t2\.small or db\.m4\.large\)\. Partial DB instance hours consumed are billed as full hours\. For Amazon RDS pricing information, see the [Amazon RDS product page](https://aws.amazon.com/rds/pricing)\.

Billing starts for a DB instance as soon as the DB instance is available\. DB instance hours are billed for each hour that your DB instance is running in an available state\. Billing continues until the DB instance terminates, which occurs when you delete the DB instance or if the DB instance fails\.

If you no longer want to be charged for your DB instance, you must stop or delete it to avoid being billed for additional DB instance hours\. For more information about the DB instance states for which you are billed, see [DB Instance Status](Overview.DBInstance.Status.md)\.

## Stopped DB Instances<a name="USER_OnDemandDBInstances.Stopped"></a>

While your DB instance is stopped, you are charged for provisioned storage, including Provisioned IOPS\. You are also charged for backup storage, including storage for manual snapshots and automated backups within your specified retention window\. You are not charged for DB instance hours\.

## Multi\-AZ DB Instances<a name="USER_OnDemandDBInstances.MultiAZ"></a>

If you specify that your DB instance should be a Multi\-AZ deployment, you are billed according to the Multi\-AZ pricing posted on the Amazon RDS pricing page\.