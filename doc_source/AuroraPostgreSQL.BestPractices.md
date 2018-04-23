# Best Practices with Amazon Aurora PostgreSQL<a name="AuroraPostgreSQL.BestPractices"></a>

This topic includes information on best practices and options for using or migrating data to an Amazon Aurora PostgreSQL DB cluster\.

## Fast Failover with Amazon Aurora PostgreSQL<a name="AuroraPostgreSQL.BestPractices.FastFailover"></a>

There are several things you can do to make a failover perform faster with Aurora PostgreSQL\. This section discusses each of the following ways:
+ Aggressively set TCP keepalives to ensure that longer running queries that are waiting for a server response will be killed before the read timeout expires in the event of a failure\.
+ Set the Java DNS caching timeouts aggressively to ensure the Aurora Read\-Only Endpoint can properly cycle through read\-only nodes on subsequent connection attempts\.
+ Set the timeout variables used in the JDBC connection string as low as possible\. Use separate connection objects for short and long running queries\.
+ Use the provided read and write Aurora endpoints to establish a connection to the cluster\.
+ Use RDS APIs to test application response on server side failures and use a packet dropping tool to test application response for client\-side failures\.

### Setting TCP Keepalives Parameters<a name="AuroraPostgreSQL.BestPractices.FastFailover.TCPKeepalives"></a>

The TCP keepalive process is simple: when you set up a TCP connection, you associate a set of timers\. When the keepalive timer reaches zero, you send a keepalive probe packet\. If you receive a reply to your keepalive probe, you can assume that the connection is still up and running\.

Enabling TCP keepalive parameters and setting them aggressively ensures that if your client is no longer able to connect to the database, then any active connections are quickly closed\. This action allows the application to react appropriately, such as by picking a new host to connect to\.

The following TCP keepalive parameters need to be set:
+ `tcp_keepalive_time` controls the time, in seconds, after which a keepalive packet is sent when no data has been sent by the socket \(ACKs are not considered data\)\. We recommend the following setting: 

   `tcp_keepalive_time = 1`
+ `tcp_keepalive_intvl` controls the time, in seconds, between sending subsequent keepalive packets after the initial packet is sent \(set using the `tcp_keepalive_time` parameter\)\. We recommend the following setting:

  `tcp_keepalive_intvl = 1`
+ `tcp_keepalive_probes` is the number of unacknowledged keepalive probes that occur before the application is notified\. We recommend the following setting: 

  `tcp_keepalive_probes = 5`

These settings should notify the application within five seconds when the database stops responding\. A higher *tcp\_keepalive\_probes *value can be set if keepalive packets are often dropped within the application's network\. This subsequently increases the time it takes to detect an actual failure, but allows for more buffer in less reliable networks\.

**Setting TCP keepalive parameters on Linux**

1. When testing how to configure the TCP keepalive parameters, we recommend doing so via the command line with the following commands: This suggested configuration is system wide, meaning that it affects all other applications that create sockets with the SO\_KEEPALIVE option on\.

   ```
   sudo sysctl net.ipv4.tcp_keepalive_time=1
   sudo sysctl net.ipv4.tcp_keepalive_intvl=1
   sudo sysctl net.ipv4.tcp_keepalive_probes=5
   ```

1. Once you've found a configuration that works for your application, these settings must be persisted by adding the following lines \(including any changes you made\) to */etc/sysctl\.conf*:

   ```
   tcp_keepalive_time = 1
   tcp_keepalive_intvl = 1
   tcp_keepalive_probes = 5
   ```

For information on setting TCP keepalive parameters on Windows, see [ Things You May Want to Know About TCP Keepalive](https://blogs.technet.microsoft.com/nettracer/2010/06/03/things-that-you-may-want-to-know-about-tcp-keepalives/)\.

### Configuring Your Application for Fast Failover<a name="AuroraPostgreSQL.BestPractices.FastFailover.Configuring"></a>

This section discusses several Aurora PostgreSQL specific configuration changes you can make\. Documentation for general setup and configuration of the JDBC driver is available from the [PostgreSQL JDBC site](https://jdbc.postgresql.org/documentation/documentation.html)\. 

#### Reducing DNS Cache Timeouts<a name="AuroraPostgreSQL.BestPractices.FastFailover.Configuring.Timeouts"></a>

When your application tries to establish a connectionafter a failover, the new Aurora PostgreSQL writer will be a previous reader, which can be found using the Aurora **read only** endpoint before DNS updates have fully propagated\. Setting the java DNS TTL to a low value helps cycle between reader nodes on subsequent connection attempts\.

```
// Sets internal TTL to match the Aurora RO Endpoint TTL
java.security.Security.setProperty("networkaddress.cache.ttl" , "1");
// If the lookup fails, default to something like small to retry
java.security.Security.setProperty("networkaddress.cache.negative.ttl" , "3");
```

#### Setting an Aurora PostgreSQL Connection String for Fast Failover<a name="AuroraPostgreSQL.BestPractices.FastFailover.Configuring.ConnectionString"></a>

To make use of Aurora PostgreSQL fast failover, your application's connection string should have a list of hosts \(highlighted in bold in the following example\) instead of just a single host\. Here is an example connection string you could use to connect to an Aurora PostgreSQL cluster:

```
jdbc:postgresql://myauroracluster.cluster-c9bfei4hjlrd.us-east-1-beta.rds.amazonaws.com:5432,
myauroracluster.cluster-ro-c9bfei4hjlrd.us-east-1-beta.rds.amazonaws.com:5432
/postgres?user=<masteruser>&password=<masterpw>&loginTimeout=2
&connectTimeout=2&cancelSignalTimeout=2&socketTimeout=60
&tcpKeepAlive=true&targetServerType=master&loadBalanceHosts=true
```

 For best availability and to avoid a dependency on the RDS API, the best option for connecting is to maintain a file with a host string that your application reads from when you establish a connection to the database\. This host string would have all the Aurora endpoints available for the cluster\. For more information about Aurora endpoints, see [Aurora Endpoints](Aurora.Overview.md#Aurora.Overview.Endpoints)\. For example, you could store the endpoints in a file locally like the following: 

```
myauroracluster.cluster-c9bfei4hjlrd.us-east-1-beta.rds.amazonaws.com:5432,
myauroracluster.cluster-ro-c9bfei4hjlrd.us-east-1-beta.rds.amazonaws.com:5432
```

Your application would read from this file to populate the host section of the JDBC connection string\. Renaming the DB Cluster causes these endpoints to change; ensure that your application handles that event should it occur\.

Another option is to use a list of DB instance nodes:

```
my-node1.cksc6xlmwcyw.us-east-1-beta.rds.amazonaws.com:5432,
my-node2.cksc6xlmwcyw.us-east-1-beta.rds.amazonaws.com:5432,
my-node3.cksc6xlmwcyw.us-east-1-beta.rds.amazonaws.com:5432,
my-node4.cksc6xlmwcyw.us-east-1-beta.rds.amazonaws.com:5432
```

The benefit of this approach is that the PostgreSQL JDBC connection driver will loop through all nodes on this list to find a valid connection, whereas when using the Aurora endpoints only two nodes will be tried per connection attempt\. The downside of using DB instance nodes is that if you add or remove nodes from your cluster and the list of instance endpoints becomes stale, the connection driver may never find the correct host to connect to\.

Setting the following parameters aggressively helps ensure that your application doesn't wait too long to connect to any one host\. 
+ `loginTimeout` \- Controls how long your application waits to login to the database *after* a socket connection has been established\.
+ `connectTimeout` \- Controls how long the socket waits to establish a connection to the database\.

Other application parameters can be modified to speed up the connection process, depending on how aggressive you want your application to be\.
+ `cancelSignalTimeout` \- In some applications, you may want to send a "best effort" cancel signal on a query that has timed out\. If this cancel signal is in your failover path, you should consider setting it aggressively to avoid sending this signal to a dead host\.
+ `socketTimeout` \- This parameter controls how long the socket waits for read operations\. This parameter can be used as a global "query timeout" to ensure no query waits longer than this value\. A good practice is to have one connection handler that runs short lived queries and sets this value lower, and to have another connection handler for long running queries with this value set much higher\. Then, you can rely on TCP keepalive parameters to kill long running queries if the server goes down\.
+ `tcpKeepAlive` \- Enable this parameter to ensure the TCP keepalive parameters that you set are respected\.
+ `targetServerType`\- This parameter can be used to control whether the driver connects to a read \(slave\) or write \(master\) node\. Possible values are: `any`, `master`, `slave `and `preferSlave`\. The `preferSlave` value attempts to establish a connection to a reader first but falls back and connects to the writer if no reader connection can be established\.
+ `loadBalanceHosts` \- When set to `true`, this parameter has the application connect to a random host chosen from a list of candidate hosts\.

#### Other Options for Obtaining The Host String<a name="AuroraPostgreSQL.BestPractices.FastFailover.Configuring.HostString"></a>

You can get the host string from several sources, including the `aurora_replica_status` function and by using the Amazon RDS API\.

Your application can connect to any DB instance in the DB Cluster and query the `aurora_replica_status` function to determine who the writer of the cluster is, or to find any other reader nodes in the cluster\. You can use this function to reduce the amount of time it takes to find a host to connect to, though in certain scenarios the `aurora_replica_status` function may show out of date or incomplete information in certain network failure scenarios\.

A good way to ensure your application can find a node to connect to is to attempt to connect to the **cluster writer****endpoint ** and then the **cluster reader****endpoint **until you can establish a readable connection\. These endpoints do not change unless you rename your DB Cluster, and thus can generally be left as static members of your application or stored in a resource file that your application reads from\.

Once you establish a connection using one of these endpoints, you can call the `aurora_replica_status` function to get information about the rest of the cluster\. For example, the following command retrieves information with the `aurora_replica_status` function\.

```
postgres=> select server_id, session_id, highest_lsn_rcvd, 
cur_replay_latency_in_usec, now(), last_update_timestamp from 
aurora_replica_status();
    server_id     |              session_id              |    
vdl    | highest_lsn_rcvd | cur_replay_latency |              
now              |    last_update_time    
-----------------------------------+---------------------------
-----------+-----------+------------------+--------------------+-
------------------------------+-------
         mynode-1 | 3e3c5044-02e2-11e7-b70d-95172646d6ca | 
594220999 |        594221001 |             201421 | 2017-03-07 
19:50:24.695322+00 | 2017-03-07 19:50:23+00
         mynode-2 | 1efdd188-02e4-11e7-becd-f12d7c88a28a | 
594220999 |        594221001 |             201350 | 2017-03-07 
19:50:24.695322+00 | 2017-03-07 19:50:23+00
         mynode-3 |                    MASTER_SESSION_ID | 
594220999 |                  |                    | 2017-03-07 
19:50:24.695322+00 | 2017-03-07 19:50:23+00
(3 rows)
```

So for example, the hosts section of your connection string could start with both the writer and reader cluster endpoints:

```
myauroracluster.cluster-c9bfei4hjlrd.us-east-1-beta.rds.amazonaws.com:5432,
myauroracluster.cluster-ro-c9bfei4hjlrd.us-east-1-beta.rds.amazonaws.com:5432
```

In this scenario, your application would attempt to establish a connection to any node type, master or slave\. Once connected, a good practice is to first examine the read\-write status of the node by querying for the result of the command `SHOW transaction_read_only`\. 

If the return value of the query is `OFF`, then you've successfully connected to the master node\. If the return value is `ON`, and your application requires a read\-write connection, you can then call the `aurora_replica_status` function to determine the `server_id` that has `session_id='MASTER_SESSION_ID'`\. This function gives you the name of the master node\. You can use this in conjunction with the 'endpointPostfix' described below\.

One thing to watch out for is when you connect to a replica that has data that has become stale\. When this happens, the `aurora_replica_status` function may show out\-of\-date information\. A threshold for staleness can be set at the application level and examined by looking at the difference between the server time and the last\_update\_time\. In general, your application should be sure to avoid flip\-flopping between two hosts due to conflicting information returned by the aurora\_replica\_status function\. That is, your application should err on the side of trying all known hosts first instead of blindly following the data returned by the aurora\_replica\_status function\.

##### API<a name="AuroraPostgreSQL.BestPractices.FastFailover.Configuring.HostString.API"></a>

You can programatically find the list of instances by using the [AWS Java SDK](https://aws.amazon.com/sdk-for-java/), specifically the [DescribeDbClusters ](http://docs.aws.amazon.com/AWSJavaSDK/latest/javadoc/com/amazonaws/services/rds/AmazonRDS.html#describeDBClusters-com.amazonaws.services.rds.model.DescribeDBClustersRequest-)API\. Here's a small example of how you might do this in java 8:

```
AmazonRDS client = AmazonRDSClientBuilder.defaultClient();
DescribeDBClustersRequest request = new DescribeDBClustersRequest()
   .withDBClusterIdentifier(clusterName);
DescribeDBClustersResult result = 
rdsClient.describeDBClusters(request);

DBCluster singleClusterResult = result.getDBClusters().get(0);

String pgJDBCEndpointStr = 
singleClusterResult.getDBClusterMembers().stream()
   .sorted(Comparator.comparing(DBClusterMember::getIsClusterWriter)
   .reversed()) // This puts the writer at the front of the list
   .map(m -> m.getDBInstanceIdentifier() + endpointPostfix + ":" + singleClusterResult.getPort()))
   .collect(Collectors.joining(","));
```

pgJDBCEndpointStr will contain a formatted list of endpoints, e\.g:

```
my-node1.cksc6xlmwcyw.us-east-1-beta.rds.amazonaws.com:5432,
my-node2.cksc6xlmwcyw.us-east-1-beta.rds.amazonaws.com:5432
```

The variable 'endpointPostfix' can be a constant that your application sets, or can be obtained by querying the DescribeDBInstances API for a single instance in your cluster\. This value remains constant within a region and for an individual customer, so it would save an API call to simply keep this constant in a resource file that your application reads from\. In the example above, it would be set to:

```
.cksc6xlmwcyw.us-east-1-beta.rds.amazonaws.com
```

For availability purposes, a good practice would be to default to using the [Aurora Endpoints](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Aurora.Overview.html#Aurora.Overview.Endpoints) of your DB Cluster if the API is not responding, or taking too long to respond\. The endpoints are guaranteed to be up to date within the time it takes to update the DNS record \(typically less than 30 seconds\)\. This again can be stored in a resource file that your application consumes\.

### Testing Failover<a name="AuroraPostgreSQL.BestPracticesFastFailover.Testing"></a>

In all cases you must have a DB Cluster with >= 2 DB instances in it\.

From the server side, certain APIs can cause an outage that can be used to test how your applications responds:
+ [FailoverDBCluster ](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_FailoverDBCluster.html) \- Will attempt to promote a new DB Instance in your DB Cluster to writer

  ```
  public void causeFailover() {
      /*
       * See http://docs.aws.amazon.com/sdk-for-java/v1/developer-guide/basics.html for more details on setting up an RDS client
       */
      final AmazonRDS rdsClient = AmazonRDSClientBuilder.defaultClient();
     
      FailoverDBClusterRequest request = new FailoverDBClusterRequest();
      request.setDBClusterIdentifier("cluster-identifier");
  
      rdsClient.failoverDBCluster(request);
  }
  ```
+ [RebootDBInstance ](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RebootDBInstance.html)\- Failover is not guaranteed in this API\. It will shutdown the database on the writer, though, and can be used to test how your application responds to connections dropping \(note that the **ForceFailover **parameter is not applicable for Aurora engines and instead should use the FailoverDBCluster API\)
+ [ModifyDBCluster ](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html)\- Modifying the **Port **will cause an outage when the nodes in the cluster begin listening on a new port\. In general your application can respond to this failure by ensuring that only your application controls port changes and can appropriately update the endpoints it depends on, by having someone manually update the port when they make modifications at the API level, or by querying the RDS API in your application to determine if the port has changed\.
+ [ModifyDBInstance ](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html)\- Modifying the **DBInstanceClass** will cause an outage
+ [DeleteDBInstance ](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DeleteDBInstance.html)\- Deleting the master/writer will cause a new DB Instance to be promoted to writer in your DB Cluster

From the application/client side, if using Linux, you can test how the application responds to sudden packet drops based on port, host, or if tcp keepalive packets are not sent or received by using iptables\.

### Fast Failover Example<a name="AuroraPostgreSQL.BestPractices.FastFailover.Example"></a>

The following code sample shows how an application might set up an Aurora PostgreSQL driver manager\. The application would call `getConnection(...)` when it needed a connection\. A call to that function can fail to find a valid host, such as when no writer is found but the targetServerType was set to "master"\), and the calling application should simply retry\. This can easily be wrapped into a connection pooler to avoid pushing the retry behavior onto the application\. Most connection poolers allow you to specify a JDBC connection string, so your application could call into `getJdbcConnectionString(...)` and pass that to the connection pooler to make use of faster failover on Aurora PostgreSQL\.

```
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;
import java.sql.Statement;
import java.util.ArrayList;
import java.util.List;
import java.util.stream.Collectors;
import java.util.stream.IntStream;

import org.joda.time.Duration;

public class FastFailoverDriverManager {
   private static Duration LOGIN_TIMEOUT = Duration.standardSeconds(2);
   private static Duration CONNECT_TIMEOUT = Duration.standardSeconds(2);
   private static Duration CANCEL_SIGNAL_TIMEOUT = Duration.standardSeconds(1);
   private static Duration DEFAULT_SOCKET_TIMEOUT = Duration.standardSeconds(5);

   public FastFailoverDriverManager() {
       try {
            Class.forName("org.postgresql.Driver");
       } catch (ClassNotFoundException e) {
            e.printStackTrace();
       }

       /*
         * RO endpoint has a TTL of 1s, we should honor that here. Setting this aggressively makes sure that when
         * the PG JDBC driver creates a new connection, it will resolve a new different RO endpoint on subsequent attempts
         * (assuming there is > 1 read node in your cluster)
         */
        java.security.Security.setProperty("networkaddress.cache.ttl" , "1");
       // If the lookup fails, default to something like small to retry
       java.security.Security.setProperty("networkaddress.cache.negative.ttl" , "3");
   }

   public Connection getConnection(String targetServerType) throws SQLException {
       return getConnection(targetServerType, DEFAULT_SOCKET_TIMEOUT);
   }

   public Connection getConnection(String targetServerType, Duration queryTimeout) throws SQLException {
        Connection conn = DriverManager.getConnection(getJdbcConnectionString(targetServerType, queryTimeout));

       /*
         * A good practice is to set socket and statement timeout to be the same thing since both 
         * the client AND server will kill the query at the same time, leaving no running queries 
         * on the backend
         */
        Statement st = conn.createStatement();
        st.execute("set statement_timeout to " + queryTimeout.getMillis());
        st.close();

       return conn;
   }

   private static String urlFormat = "jdbc:postgresql://%s"
           + "/postgres"
           + "?user=%s"
           + "&password=%s"
           + "&loginTimeout=%d"
           + "&connectTimeout=%d"
           + "&cancelSignalTimeout=%d"
           + "&socketTimeout=%d"
           + "&targetServerType=%s"
           + "&tcpKeepAlive=true"
           + "&ssl=true"
           + "&loadBalanceHosts=true";
   public String getJdbcConnectionString(String targetServerType, Duration queryTimeout) {
       return String.format(urlFormat, 
                getFormattedEndpointList(getLocalEndpointList()),
                CredentialManager.getUsername(),
                CredentialManager.getPassword(),
                LOGIN_TIMEOUT.getStandardSeconds(),
                CONNECT_TIMEOUT.getStandardSeconds(),
                CANCEL_SIGNAL_TIMEOUT.getStandardSeconds(),
                queryTimeout.getStandardSeconds(),
                targetServerType
       );
   }

   private List<String> getLocalEndpointList() {
       /*
         * As mentioned in the best practices doc, a good idea is to read a local resource file and parse the cluster endpoints. 
         * For illustration purposes, the endpoint list is hardcoded here
         */
        List<String> newEndpointList = new ArrayList<>();
        newEndpointList.add("myauroracluster.cluster-c9bfei4hjlrd.us-east-1-beta.rds.amazonaws.com:5432");
        newEndpointList.add("myauroracluster.cluster-ro-c9bfei4hjlrd.us-east-1-beta.rds.amazonaws.com:5432");

       return newEndpointList;
   }

   private static String getFormattedEndpointList(List<String> endpoints) {
       return IntStream.range(0, endpoints.size())
               .mapToObj(i -> endpoints.get(i).toString())
               .collect(Collectors.joining(","));
   }
}
```

## Troubleshooting storage issues<a name="AuroraPostgreSQL.BestPractices.TroubleshootingStorage"></a>

If the amount of memory required by a sort or index creation operation exceeds the amount of memory available, Aurora PostgreSQL writes the excess data to storage\. When it writes the data it uses the same storage space it uses for storing error and message logs\. If your sorts or index creation functions exceed memory available, you could develop a local storage shortage\. If you experience issues with Aurora PostgreSQL running out of storage space, you can either reconfigure your data sorts to use more memory, or reduce the data retention period for your PostgreSQL log files\. For more information about changing the log retention period see, [PostgreSQL Database Log Files](USER_LogAccess.Concepts.PostgreSQL.md)\. 