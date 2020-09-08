# Oracle Time Zone<a name="Appendix.Oracle.Options.Timezone"></a>

You can use the time zone option to change the system time zone used by your Oracle DB instance\. For example, you might change the time zone of a DB instance to be compatible with an on\-premises environment, or a legacy application\. The time zone option changes the time zone at the host level\. Changing the time zone impacts all date columns and values, including `SYSDATE` and `SYSTIMESTAMP`\.

The time zone option differs from the `rdsadmin_util.alter_db_time_zone` command\. The `alter_db_time_zone` command changes the time zone only for certain data types\. The time zone option changes the time zone for all date columns and values\. For more information about `alter_db_time_zone`, see [Setting the Database Time Zone](Appendix.Oracle.CommonDBATasks.Database.md#Appendix.Oracle.CommonDBATasks.TimeZoneSupport)\. For more information about upgrade considerations, see [Time Zone Considerations](USER_UpgradeDBInstance.Oracle.md#USER_UpgradeDBInstance.Oracle.OGPG.DST)\.

## Prerequisites for Time Zone<a name="Appendix.Oracle.Options.Timezone.PreReqs"></a>

The time zone option is a permanent and persistent option\. You can't remove the option from an option group after you add it\. You can't remove the option group from a DB instance after you add it\. You can't modify the time zone setting of the option to a different time zone\. 

We strongly urge you to take a DB snapshot of your DB instance before adding the time zone option to a DB instance\. By using a snapshot you can recover the DB instance if you set the time zone option incorrectly\. For more information, see [Creating a DB Snapshot](USER_CreateSnapshot.md)\. 

We strongly urge you to test the time zone option on a test DB instance before you add it to a production DB instance\. Adding the time zone option can cause problems with tables that use system date to add dates or times\. You should analyze your data and applications to determine the impact of changing the time zone\. 

## Time Zone Option Settings<a name="Appendix.Oracle.Options.Timezone.Options"></a>

Amazon RDS supports the following settings for the time zone option\. 


****  

| Option Setting | Valid Values | Description | 
| --- | --- | --- | 
| `TIME_ZONE` |  One of the available time zones\. For the full list, see [Available Time Zones](#Appendix.Oracle.Options.Timezone.Zones)\.   |  The new time zone for your DB instance\.   | 

## Adding the Time Zone Option<a name="Appendix.Oracle.Options.Timezone.Add"></a>

The general process for adding the time zone option to a DB instance is the following: 

1. Create a new option group, or copy or modify an existing option group\.

1. Add the option to the option group\.

1. Associate the option group with the DB instance\.

When you add the time zone option, a brief outage occurs while your DB instance is automatically restarted\. 

### Console<a name="Appendix.Oracle.Options.Timezone.Console"></a>

**To add the time zone option to a DB instance**

1. Determine the option group you want to use\. You can create a new option group or use an existing option group\. If you want to use an existing option group, skip to the next step\. Otherwise, create a custom DB option group with the following settings: 

   1. For **Engine** choose the oracle edition for your DB instance\. 

   1. For **Major engine version** choose the version of your DB instance\. 

   For more information, see [Creating an Option Group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.Create)\. 

1. Add the **Timezone** option to the option group, and configure the option settings\. 
**Important**  
If you add the time zone option to an existing option group that is already attached to one or more DB instances, a brief outage occurs while all the DB instances are automatically restarted\. 

   For more information about adding options, see [Adding an Option to an Option Group](USER_WorkingWithOptionGroups.md#USER_WorkingWithOptionGroups.AddOption)\. For more information about each setting, see [Time Zone Option Settings](#Appendix.Oracle.Options.Timezone.Options)\. 

1. Apply the option group to a new or existing DB instance: 
   + For a new DB instance, you apply the option group when you launch the instance\. For more information, see [Creating an Amazon RDS DB Instance](USER_CreateDBInstance.md)\. 
   + For an existing DB instance, you apply the option group by modifying the instance and attaching the new option group\. When you add the time zone option to an existing DB instance, a brief outage occurs while your DB instance is automatically restarted\. For more information, see [Modifying an Amazon RDS DB Instance](Overview.DBInstance.Modifying.md)\. 

### AWS CLI<a name="Appendix.Oracle.Options.Timezone.CLI"></a>

The following example uses the AWS CLI [add\-option\-to\-option\-group](https://docs.aws.amazon.com/cli/latest/reference/rds/add-option-to-option-group.html) command to add the `Timezone` option and the `TIME_ZONE` option setting to an option group called `myoptiongroup`\. The time zone is set to `Africa/Cairo`\. 

For Linux, macOS, or Unix:

```
aws rds add-option-to-option-group \
    --option-group-name "myoptiongroup" \
    --options "OptionName=Timezone,OptionSettings=[{Name=TIME_ZONE,Value=Africa/Cairo}]" \
    --apply-immediately
```

For Windows:

```
aws rds add-option-to-option-group ^
    --option-group-name "myoptiongroup" ^
    --options "OptionName=Timezone,OptionSettings=[{Name=TIME_ZONE,Value=Africa/Cairo}]" ^
    --apply-immediately
```

## Modifying Time Zone Settings<a name="Appendix.Oracle.Options.Timezone.ModifySettings"></a>

The time zone option is a permanent and persistent option\. You can't remove the option from an option group after you add it\. You can't remove the option group from a DB instance after you add it\. You can't modify the time zone setting of the option to a different time zone\. If you set the time zone incorrectly, restore a snapshot of your DB instance from before you added the time zone option\. 

## Removing the Time Zone Option<a name="Appendix.Oracle.Options.Timezone.Remove"></a>

The time zone option is a permanent and persistent option\. You can't remove the option from an option group after you add it\. You can't remove the option group from a DB instance after you add it\. To remove the time zone option, restore a snapshot of your DB instance from before you added the time zone option\. 

## Available Time Zones<a name="Appendix.Oracle.Options.Timezone.Zones"></a>

The following values can be used for the time zone option\. 


****  

| Zone | Time Zone | 
| --- | --- | 
|  Africa  |  Africa/Cairo, Africa/Casablanca, Africa/Harare, Africa/Lagos, Africa/Luanda, Africa/Monrovia, Africa/Nairobi, Africa/Tripoli, Africa/Windhoek   | 
|  America  |  America/Araguaina, America/Argentina/Buenos\_Aires, America/Asuncion, America/Bogota, America/Caracas, America/Chicago, America/Chihuahua, America/Cuiaba, America/Denver, America/Detroit, America/Fortaleza, America/Godthab, America/Guatemala, America/Halifax, America/Lima, America/Los\_Angeles, America/Manaus, America/Matamoros, America/Mexico\_City, America/Monterrey, America/Montevideo, America/New\_York, America/Phoenix, America/Santiago, America/Sao\_Paulo, America/Tijuana, America/Toronto   | 
|  Asia  |  Asia/Amman, Asia/Ashgabat, Asia/Baghdad, Asia/Baku, Asia/Bangkok, Asia/Beirut, Asia/Calcutta, Asia/Damascus, Asia/Dhaka, Asia/Hong\_Kong, Asia/Irkutsk, Asia/Jakarta, Asia/Jerusalem, Asia/Kabul, Asia/Karachi, Asia/Kathmandu, Asia/Kolkata, Asia/Krasnoyarsk, Asia/Magadan, Asia/Manila, Asia/Muscat, Asia/Novosibirsk, Asia/Rangoon, Asia/Riyadh, Asia/Seoul, Asia/Shanghai, Asia/Singapore, Asia/Taipei, Asia/Tehran, Asia/Tokyo, Asia/Ulaanbaatar, Asia/Vladivostok, Asia/Yakutsk, Asia/Yerevan   | 
|  Atlantic  |  Atlantic/Azores, Atlantic/Cape\_Verde   | 
|  Australia  |  Australia/Adelaide, Australia/Brisbane, Australia/Darwin, Australia/Eucla, Australia/Hobart, Australia/Lord\_Howe, Australia/Perth, Australia/Sydney   | 
|  Brazil  |  Brazil/DeNoronha, Brazil/East   | 
|  Canada  |  Canada/Newfoundland, Canada/Saskatchewan   | 
|  Etc  |  Etc/GMT\-3  | 
|  Europe  |  Europe/Amsterdam, Europe/Athens, Europe/Berlin, Europe/Dublin, Europe/Helsinki, Europe/Kaliningrad, Europe/London, Europe/Madrid, Europe/Moscow, Europe/Paris, Europe/Prague, Europe/Rome, Europe/Sarajevo   | 
|  Pacific  |  Pacific/Apia, Pacific/Auckland, Pacific/Chatham, Pacific/Fiji, Pacific/Guam, Pacific/Honolulu, Pacific/Kiritimati, Pacific/Marquesas, Pacific/Samoa, Pacific/Tongatapu, Pacific/Wake   | 
|  US  |  US/Alaska, US/Central, US/East\-Indiana, US/Eastern, US/Pacific   | 
|  UTC  |  UTC  | 

## Related Topics<a name="Appendix.Oracle.Options.Timezone.Related"></a>
+ [Working with Option Groups](USER_WorkingWithOptionGroups.md)
+ [Options for Oracle DB Instances](Appendix.Oracle.Options.md)
+ [Setting the Time Zone for Oracle Scheduler Jobs](Appendix.Oracle.CommonDBATasks.Scheduler.md#Appendix.Oracle.CommonDBATasks.Scheduler.TimeZone)