# Modifying an Amazon RDS DB Instance and Using the Apply Immediately Parameter<a name="Overview.DBInstance.Modifying"></a>

Most modifications to a DB instance can be applied immediately or deferred until the next maintenance window\. Some modifications, such as parameter group changes, require that you manually reboot your DB instance for the change to take effect\. 

**Important**  
Some modifications result in an outage because Amazon RDS must reboot your DB instance for the change to take effect\. Review the impact to your database and applications before modifying your DB instance settings\. 

To modify an Amazon RDS DB instance, follow the instructions for your specific database engine\. 

+ [Modifying a DB Instance Running the MariaDB Database Engine](USER_ModifyInstance.MariaDB.md)

+ [Modifying a DB Instance Running the Microsoft SQL Server Database Engine](USER_ModifyInstance.SQLServer.md)

+ [Modifying a DB Instance Running the MySQL Database Engine](USER_ModifyInstance.MySQL.md)

+ [Modifying a DB Instance Running the Oracle Database Engine](USER_ModifyInstance.Oracle.md)

+ [Modifying a DB Instance Running the PostgreSQL Database Engine](USER_ModifyPostgreSQLInstance.md)

## The Impact of Apply Immediately<a name="USER_ModifyInstance.ApplyImmediately"></a>

When you modify a DB instance, you can apply the changes immediately\. To apply changes immediately, you select the **Apply Immediately** option in the AWS Management Console, you use the `--apply-immediately` parameter when calling the AWS CLI, or you set the `ApplyImmediately` parameter to `true` when using the Amazon RDS API\. 

If you don't choose to apply changes immediately, the changes are put into the pending modifications queue\. During the next maintenance window, any pending changes in the queue are applied\. 

**Important**  
If you choose to apply changes immediately, any changes in the pending modifications queue are also applied\. If any of the pending modifications require downtime, choosing apply immediately can cause unexpected downtime\. 

## Related Topics<a name="Overview.DBInstance.Modifying.Related"></a>

+ [Renaming a DB Instance](USER_RenameInstance.md)

+ [Rebooting a DB Instance](USER_RebootInstance.md)

+ [Stopping an Amazon RDS DB Instance Temporarily](USER_StopInstance.md)

+ [modify\-db\-instance](http://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html)

+ [ModifyDBInstance](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html)