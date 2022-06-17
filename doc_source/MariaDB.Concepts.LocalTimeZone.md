# Local time zone for MariaDB DB instances<a name="MariaDB.Concepts.LocalTimeZone"></a>

By default, the time zone for a MariaDB DB instance is Universal Time Coordinated \(UTC\)\. You can set the time zone for your DB instance to the local time zone for your application instead\.

To set the local time zone for a DB instance, set the `time_zone` parameter in the parameter group for your DB instance to one of the supported values listed later in this section\. When you set the `time_zone` parameter for a parameter group, all DB instances and read replicas that are using that parameter group change to use the new local time zone\. For information on setting parameters in a parameter group, see [Working with parameter groups](USER_WorkingWithParamGroups.md)\.

After you set the local time zone, all new connections to the database reflect the change\. If you have any open connections to your database when you change the local time zone, you won't see the local time zone update until after you close the connection and open a new connection\.

You can set a different local time zone for a DB instance and one or more of its read replicas\. To do this, use a different parameter group for the DB instance and the replica or replicas and set the `time_zone` parameter in each parameter group to a different local time zone\.

If you are replicating across AWS Regions, then the source DB instance and the read replica use different parameter groups \(parameter groups are unique to an AWS Region\)\. To use the same local time zone for each instance, you must set the `time_zone` parameter in the instance's and read replica's parameter groups\.

When you restore a DB instance from a DB snapshot, the local time zone is set to UTC\. You can update the time zone to your local time zone after the restore is complete\. If you restore a DB instance to a point in time, then the local time zone for the restored DB instance is the time zone setting from the parameter group of the restored DB instance\.

You can set your local time zone to one of the following values\.


****  

|  |  | 
| --- |--- |
| `Africa/Cairo` | `Asia/Riyadh` | 
| `Africa/Casablanca` | `Asia/Seoul` | 
| `Africa/Harare` | `Asia/Shanghai` | 
| `Africa/Monrovia` | `Asia/Singapore` | 
| `Africa/Nairobi` | `Asia/Taipei` | 
| `Africa/Tripoli` | `Asia/Tehran` | 
| `Africa/Windhoek` | `Asia/Tokyo` | 
| `America/Araguaina` | `Asia/Ulaanbaatar` | 
| `America/Asuncion` | `Asia/Vladivostok` | 
| `America/Bogota` | `Asia/Yakutsk` | 
| `America/Buenos_Aires` | `Asia/Yerevan` | 
| `America/Caracas` | `Atlantic/Azores` | 
| `America/Chihuahua` | `Australia/Adelaide` | 
| `America/Cuiaba` | `Australia/Brisbane` | 
| `America/Denver` | `Australia/Darwin` | 
| `America/Fortaleza` | `Australia/Hobart` | 
| `America/Guatemala` | `Australia/Perth` | 
| `America/Halifax` | `Australia/Sydney` | 
| `America/Manaus` | `Brazil/East` | 
| `America/Matamoros` | `Canada/Newfoundland` | 
| `America/Monterrey` | `Canada/Saskatchewan` | 
| `America/Montevideo` | `Canada/Yukon` | 
| `America/Phoenix` | `Europe/Amsterdam` | 
| `America/Santiago` | `Europe/Athens` | 
| `America/Tijuana` | `Europe/Dublin` | 
| `Asia/Amman` | `Europe/Helsinki` | 
| `Asia/Ashgabat` | `Europe/Istanbul` | 
| `Asia/Baghdad` | `Europe/Kaliningrad` | 
| `Asia/Baku` | `Europe/Moscow` | 
| `Asia/Bangkok` | `Europe/Paris` | 
| `Asia/Beirut` | `Europe/Prague` | 
| `Asia/Calcutta` | `Europe/Sarajevo` | 
| `Asia/Damascus` | `Pacific/Auckland` | 
| `Asia/Dhaka` | `Pacific/Fiji` | 
| `Asia/Irkutsk` | `Pacific/Guam` | 
| `Asia/Jerusalem` | `Pacific/Honolulu` | 
| `Asia/Kabul` | `Pacific/Samoa` | 
| `Asia/Karachi` | `US/Alaska` | 
| `Asia/Kathmandu` | `US/Central` | 
| `Asia/Krasnoyarsk` | `US/Eastern` | 
| `Asia/Magadan` | `US/East-Indiana` | 
| `Asia/Muscat` | `US/Pacific` | 
| `Asia/Novosibirsk` | `UTC` | 