# Database has long running idle in transaction connection<a name="proactive-insights.idle-txn"></a>

A connection to the database has been in the `idle in transaction` state for more than 1800 seconds\.

**Topics**
+ [Supported engine versions](#proactive-insights.idle-txn.context.supported)
+ [Context](#proactive-insights.idle-txn.context)
+ [Likely causes for this issue](#proactive-insights.idle-txn.causes)
+ [Actions](#proactive-insights.idle-txn.actions)
+ [Relevant metrics](#proactive-insights.idle-txn.metrics)

## Supported engine versions<a name="proactive-insights.idle-txn.context.supported"></a>

This insight information is supported for all versions of RDS for PostgreSQL\.

## Context<a name="proactive-insights.idle-txn.context"></a>

A transaction in the `idle in transaction` state can hold locks that block other queries\. It can also prevent `VACUUM` \(including autovacuum\) from cleaning up dead rows, leading to index or table bloat or transaction ID wraparound\.

## Likely causes for this issue<a name="proactive-insights.idle-txn.causes"></a>

A transaction initiated in an interactive session with BEGIN or START TRANSACTION hasn't ended by using a COMMIT, ROLLBACK, or END command\. This causes the transaction to move to `idle in transaction` state\.

## Actions<a name="proactive-insights.idle-txn.actions"></a>

### <a name="proactive-insights.idle-txn.actions.idle-txn"></a>

You can find idle transactions by querying `pg_stat_activity`\.

In your SQL client, run the following query to list all connections in `idle in transaction` state and to order them by duration:

```
SELECT now() - xact_start as duration ,* 
FROM  pg_stat_activity 
WHERE state  = 'idle in transaction'
AND   xact_start is not null
ORDER BY 1 DESC;
```

### <a name="proactive-insights.idle-txn.actions.end-methods"></a>

We recommend different actions depending on the causes of your insight\.

**Topics**
+ [End transaction](#proactive-insights.idle-txn.actions.end-txn)
+ [Terminate the connection](#proactive-insights.idle-txn.actions.end-connection)
+ [Configure the idle\_in\_transaction\_session\_timeout parameter](#proactive-insights.idle-txn.actions.parameter)
+ [Check the AUTOCOMMIT status](#proactive-insights.idle-txn.actions.autocommit)
+ [Check the transaction logic in your application code](#proactive-insights.idle-txn.actions.app-logic)

#### End transaction<a name="proactive-insights.idle-txn.actions.end-txn"></a>

When you initiate a transaction in an interactive session with BEGIN or START TRANSACTION, it moves to `idle in transaction` state\. It remains in this state until you end the transaction by issuing a COMMIT, ROLLBACK, END command or disconnect the connection completely to roll back the transaction\.

#### Terminate the connection<a name="proactive-insights.idle-txn.actions.end-connection"></a>

Terminate the connection with an idle transaction using the following query:

```
SELECT pg_terminate_backend(pid);
```

pid is the process ID of the connection\.

#### Configure the idle\_in\_transaction\_session\_timeout parameter<a name="proactive-insights.idle-txn.actions.parameter"></a>

Configure the `idle_in_transaction_session_timeout` parameter in the parameter group\. The advantage of configuring this parameter is that it does not require a manual intervention to terminate the long idle in transaction\. For more information on this parameter, see [the PostgreSQL documentation](https://www.postgresql.org/docs/current/runtime-config-client.html)\. 

The following message will be reported in the PostgreSQL log file after the connection is terminated and when there is an open transaction for longer than the specified time\.

```
FATAL: terminating connection due to idle in transaction timeout
```

#### Check the AUTOCOMMIT status<a name="proactive-insights.idle-txn.actions.autocommit"></a>

AUTOCOMMIT is turned on by default\. But if it is accidentally turned off in the client ensure that you turn it back on\.
+ In your psql client, run the following command:

  ```
  postgres=> \set AUTOCOMMIT on
  ```
+ In pgadmin, turn it on by choosing the AUTOCOMMIT option from the down arrow\.  
![\[In pgadmin, choose AUTOCOMMIT to turn it on.\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/apg-insight-pgadmin-autocommit.png)

#### Check the transaction logic in your application code<a name="proactive-insights.idle-txn.actions.app-logic"></a>

Investigate your application logic for possible problems\. Consider the following actions:
+ Check if the JDBC auto commit is set true in your application\. Also, consider using explicit `COMMIT` commands in your code\.
+ Check your error handling logic to see whether it closes a transaction after errors\.
+ Check whether your application is taking long to process the rows returned by a query while the transaction is open\. If so, consider coding the application to close the transaction before processing the rows\.
+ Check whether a transaction contains many long\-running operations\. If so, divide a single transaction into multiple transactions\.

## Relevant metrics<a name="proactive-insights.idle-txn.metrics"></a>

The following PI metrics are related to this insight:
+ idle\_in\_transaction\_count \- Number of sessions in `idle in transaction` state\.
+ idle\_in\_transaction\_max\_time \- The duration of the longest running transaction in the `idle in transaction` state\.