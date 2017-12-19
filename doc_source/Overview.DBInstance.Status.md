# DB Instance Status<a name="Overview.DBInstance.Status"></a>

The status of a DB instance indicates the health of the instance\. You can view the status of a DB instance by using the RDS console, the AWS CLI command [describe\-db\-instances](http://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-instances.html), or the API action [DescribeDBInstances](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBInstances.html)\. 

**Note**  
Amazon RDS also uses another status called *maintenance status*, which is shown in the Maintenance column of the Amazon RDS console\. This value indicates the status of any maintenance patches that need to be applied to a DB instance\. Maintenance status is independent of DB instance status\. For more information on *maintenance status*, see \. 


| DB Instance Status | Description | 
| --- | --- | 
| available |  The instance is healthy and available\.  | 
| backing\-up |  The instance is currently being backed up\.  | 
| configuring\-enhanced\-monitoring |  Enhanced Monitoring is being enabled or disabled for this instance\.  | 
| creating |  The instance is being created\. The instance is inaccessible while it is being created\.   | 
| deleting |  The instance is being deleted\.  | 
| failed |  The instance has failed and Amazon RDS was unable to recover it\.  Perform a point\-in\-time restore to the latest restorable time of the instance to recover the data\.   | 
| inaccessible\-encryption\-credentials |  The KMS key used to encrypt or decrypt the DB instance could not be accessed\.   | 
| incompatible\-credentials |  The supplied CloudHSM Classic user name or password is incorrect\. Please update the CloudHSM Classic credentials for the DB instance\.   | 
| incompatible\-network |  Amazon RDS is attempting to perform a recovery action on an instance but is unable to do so because the VPC is in a state that is preventing the action from being completed\.  This status can occur if, for example, all available IP addresses in a subnet were in use and Amazon RDS was unable to get an IP address for the DB instance\.   | 
| incompatible\-option\-group |  Amazon RDS attempted to apply an option group change but was unable to do so, and Amazon RDS was unable to roll back to the previous option group state\. Consult the Recent Events list for the DB instance for more information\. This status can occur if, for example, the option group contains an option such as TDE and the DB instance does not contain encrypted information\.   | 
| incompatible\-parameters |  Amazon RDS was unable to start up the DB instance because the parameters specified in the instance's DB parameter group were not compatible\. Revert the parameter changes or make them compatible with the instance to regain access to your instance\. Consult the Recent Events list for the DB instance for more information about the incompatible parameters\.   | 
| incompatible\-restore |  Amazon RDS is unable to do a point\-in\-time restore\. Common causes for this status include using temp tables, using MyISAM tables with MySQL, or using Aria tables with MariaDB\.   | 
| maintenance |  Amazon RDS is applying a maintenance update to the DB instance\. This status is used for instance\-level maintenance that RDS schedules well in advance\. We're evaluating ways to expose additional maintenance actions to customers through this status\.   | 
| modifying |  The instance is being modified because of a customer request to modify the instance\.   | 
| rebooting |  The instance is being rebooted because of a customer request or an Amazon RDS process that requires the rebooting of the instance\.  | 
| renaming |  The instance is being renamed because of a customer request to rename it\.   | 
| resetting\-master\-credentials |  The master credentials for the instance are being reset because of a customer request to reset them\.  | 
| restore\-error |  The DB instance encountered an error attempting to restore to a point\-in\-time or from a snapshot\.  | 
| starting |  The DB instance is starting\.  | 
| stopping |  The DB instance is being stopped\.  | 
| stopped |  The DB instance is stopped\.  | 
| storage\-full |  The instance has reached its storage capacity allocation\. This is a critical status and should be remedied immediately; you should scale up your storage by modifying the DB instance\. Set CloudWatch alarms to warn you when storage space is getting low so you don't run into this situation\.   | 
| storage\-optimization |  Your DB instance is being modified to change the storage size or type\. The DB instance is fully operational, but while the status of your DB instance is storage\-optimization, you can't request any changes to the storage of your DB instance\. The storage optimization process is usually short, but can sometimes take up to and even beyond 24 hours\.   | 
| upgrading |  The database engine version is being upgraded\.   | 