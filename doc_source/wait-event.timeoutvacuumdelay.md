# Timeout:VacuumDelay<a name="wait-event.timeoutvacuumdelay"></a>

The `Timeout:VacuumDelay` event indicates that the cost limit for vacuum I/O has been exceeded and that the vacuum process has been put to sleep\. Vacuum operations stop for the duration specified in the respective cost delay parameter and then it resumes its work\. For the manual vacuum command, the delay is specified in the `vacuum_cost_delay` parameter\. For the autovacuum daemon, the delay is specified in the `autovacuum_vacuum_cost_delay parameter.` 

**Topics**
+ [Supported engine versions](#wait-event.timeoutvacuumdelay.context.supported)
+ [Context](#wait-event.timeoutvacuumdelay.context)
+ [Likely causes of increased waits](#wait-event.timeoutvacuumdelay.causes)
+ [Actions](#wait-event.timeoutvacuumdelay.actions)

## Supported engine versions<a name="wait-event.timeoutvacuumdelay.context.supported"></a>

This wait event information is supported for all versions of RDS for PostgreSQL\.

## Context<a name="wait-event.timeoutvacuumdelay.context"></a>

PostgreSQL has both an autovacuum daemon and a manual vacuum command\. The autovacuum process is "on" by default for RDS for PostgreSQL DB instances\. The manual vacuum command is used on an as\-needed basis, for example, to purge tables of dead tuples or generate new statistics\.

When vacuuming is underway, PostgreSQL uses an internal counter to keep track of estimated costs as the system performs various I/O operations\. When the counter reaches the value specified by the cost limit parameter, the process performing the operation sleeps for the brief duration specified in the cost delay parameter\. It then resets the counter and continues operations\. 

The vacuum process has parameters that can be used to regulate resource consumption\. The autovacuum and the manual vacuum command have their own parameters for setting the cost limit value\. They also have their own parameters to specify a cost delay, an amount of time to put the vacuum to sleep when the limit is reached\. In this way, the cost delay parameter works as a throttling mechanism for resource consumption\. In the following lists, you can find description of these parameters\. 

**Parameters that affect throttling of the autovacuum daemon**
+ `[autovacuum\_vacuum\_cost\_limit](https://www.postgresql.org/docs/current/static/runtime-config-autovacuum.html#GUC-AUTOVACUUM-VACUUM-COST-LIMIT)` – Specifies the cost limit value to use in automatic vacuum operations\. Increasing the setting for this parameter allows the vacuum process to use more resources and decreases the `Timeout:VacuumDelay` wait event\. 
+ `[autovacuum\_vacuum\_cost\_delay](https://www.postgresql.org/docs/current/static/runtime-config-autovacuum.html#GUC-AUTOVACUUM-VACUUM-COST-DELAY)` – Specifies the cost delay value to use in automatic vacuum operations\. The default value is 2 milliseconds\. Setting the delay parameter to 0 turns off the throttling mechanism and thus, the `Timeout:VacuumDelay` wait event won't appear\. 

For more information, see [Automatic Vacuuming](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-VACUUM-COST-DELAY) in the PostgreSQL documentation\.

**Parameters that affect throttling of the manual vacuum process**
+ `vacuum_cost_limit` – The threshold at which the vacuuming process is put to sleep\. By default, the limit is 200\. This number represents the accumulated cost estimates for extra I/O needed by various resources\. Increasing this value reduces the number of the `Timeout:VacuumDelay` wait event\. 
+ `vacuum_cost_delay` – The amount of time that the vacuum process sleeps when the vacuum cost limit has been reached\. The default setting is 0, which means that this feature is off\. You can set this to an integer value to specify the number of milliseconds to turn on this feature, but we recommend that you leave it at its default setting\.

For more information about the `vacuum_cost_delay` parameter, see [Resource Consumption](https://www.postgresql.org/docs/current/runtime-config-resource.html#RUNTIME-CONFIG-RESOURCE-VACUUM-COST) in the PostgreSQL documentation\. 

To learn more about how to configure and use the autovacuum with RDS for PostgreSQL, see [Working with the PostgreSQL autovacuum on Amazon RDS for PostgreSQL](Appendix.PostgreSQL.CommonDBATasks.Autovacuum.md)\. 

## Likely causes of increased waits<a name="wait-event.timeoutvacuumdelay.causes"></a>

The `Timeout:VacuumDelay` is affected by the balance between the cost limit parameter settings \(`vacuum_cost_limit`, `autovacuum_vacuum_cost_limit`\) and the cost delay parameters \(`vacuum_cost_delay`, `autovacuum_vacuum_cost_delay`\) that control the vacuum's sleep duration\. Raising a cost limit parameter value allows more resources to be used by the vacuum before being put to sleep\. That results in fewer `Timeout:VacuumDelay` wait events\. Increasing either of the delay parameters causes the `Timeout:VacuumDelay` wait event to occur more frequently and for longer periods of time\. 

The `autovacuum_max_worker` parameter setting can also increase numbers of the `Timeout:VacuumDelay`\. Each additional autovacuum worker process contributes to the internal counter mechanism, and thus the limit can be reached more quickly than with a single autovacuum worker process\. As the cost limit is reached more quickly, the cost delay is put to effect more frequently, resulting in more `Timeout:VacuumDelay` wait events\. For more information, see [autovacuum\_max\_worker](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-MAX-WORKERS) in the PostgreSQL documentation\.

Large objects, such as 500GB or larger, also raise this wait event because it can take some time for the vacuum to complete processing large objects\.

## Actions<a name="wait-event.timeoutvacuumdelay.actions"></a>

If the vacuum operations complete as expected, no remediation is needed\. In other words, this wait event doesn't necessarily indicate a problem\. It indicates that the vacuum is being put to sleep for the period of time specified in the delay parameter so that resources can be applied to other processes that need to complete\. 

If you want vacuum operations to complete faster, you can lower the delay parameters\. This shortens the time that the vacuum sleeps\. 