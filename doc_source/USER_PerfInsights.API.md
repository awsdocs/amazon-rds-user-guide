# Retrieving Data with the Performance Insights API<a name="USER_PerfInsights.API"></a>

The Amazon RDS Performance Insights API provides visibility into the performance of your RDS instance, when Performance Insights is enabled for supported engine types\. Amazon CloudWatch Logs provides the authoritative source for vended monitoring metrics for AWS services\. Performance Insights offers a domain\-specific view of database load measured as average active sessions and provided to API consumers as a two\-dimensional time\-series dataset\. The time dimension of the data provides database load data for each time point in the queried time range\. Each time point decomposes overall load in relation to the requested dimensions, such as `SQL`, `Wait-event`, `User`, or `Host`, measured at that time point\.

Amazon RDS Performance Insights monitors your Amazon RDS DB instance so that you can analyze and troubleshoot database performance\. One way to view Performance Insights data is in the AWS Management Console\. Performance Insights also provides a public API so that you can query your own data\. The API can be used to offload data into a database, add Performance Insights data to existing monitoring dashboards, or to build monitoring tools\. To use the Performance Insights API, enable Performance Insights on one of your Amazon RDS DB instances\. For information about enabling Performance Insights, see [Enabling and Disabling Performance Insights](USER_PerfInsights.Enabling.md)\.

The Performance Insights API provides the following operations\.


****  

|  Performance Insights Operation  |  AWS CLI Command  |  Description  | 
| --- | --- | --- | 
|  [https://docs.aws.amazon.com/performance-insights/latest/APIReference/API_DescribeDimensionKeys.html](https://docs.aws.amazon.com/performance-insights/latest/APIReference/API_DescribeDimensionKeys.html)  |  [https://docs.aws.amazon.com/cli/latest/reference/pi/describe-dimension-keys.html](https://docs.aws.amazon.com/cli/latest/reference/pi/describe-dimension-keys.html)  |  Retrieves the top N dimension keys for a metric for a specific time period\.  | 
|  [https://docs.aws.amazon.com/performance-insights/latest/APIReference/API_GetResourceMetrics.html](https://docs.aws.amazon.com/performance-insights/latest/APIReference/API_GetResourceMetrics.html)  |  [https://docs.aws.amazon.com/cli/latest/reference/pi/get-resource-metrics.html](https://docs.aws.amazon.com/cli/latest/reference/pi/get-resource-metrics.html)  |  Retrieves Performance Insights metrics for a set of data sources, over a time period\. You can provide specific dimension groups and dimensions, and provide aggregation and filtering criteria for each group\.  | 

For more information about the Performance Insights API, see the [Amazon RDS Performance Insights API Reference](https://docs.aws.amazon.com/performance-insights/latest/APIReference/Welcome.html)\.

## AWS CLI for Performance Insights<a name="USER_PerfInsights.API.CLI"></a>

You can view Performance Insights data using the AWS CLI\. You can view help for the AWS CLI commands for Performance Insights by entering the following on the command line\.

```
aws pi help				
```

If you don't have the AWS CLI installed, see [Installing the AWS Command Line Interface](https://docs.aws.amazon.com/cli/latest/userguide/installing.html) in the *AWS CLI User Guide *for information about installing it\.

## Retrieving Time\-Series Metrics<a name="USER_PerfInsights.API.TimeSeries"></a>

The `GetResourceMetrics` operation retrieves one or more time\-series metrics from the Performance Insights data\. `GetResourceMetrics` requires a metric and time period, and returns a response with a list of data points\. 

For example, the AWS Management Console uses `GetResourceMetrics` in two places in the Performance Insights dashboard\. `GetResourceMetrics` is used to populate the **Counter Metrics** chart and in the **Database Load** chart, as seen in the following image\.

![\[Counter Metrics and Database Load charts\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/perf-insights-api-charts.png)

All the metrics returned by `GetResourceMetrics` are standard time\-series metrics with one exception\. The exception is `db.load`, which is the core metric in Performance Insights\. This metric is displayed in the **Database Load** chart\. The `db.load` metric is different from the other time\-series metrics because you can break it into subcomponents called dimensions\. In the previous image, `db.load` is broken down and grouped by the waits states that make up the `db.load`\.

**Note**  
`GetResourceMetrics` can also return the `db.sampleload` metric, but the `db.load` metric is appropriate in most cases\.

For information about the counter metrics returned by `GetResourceMetrics`, see [Customizing the Performance Insights Dashboard](USER_PerfInsights_Counters.md)\.

The following calculations are supported for the metrics:
+ Average – The average value for the metric over a period of time\. Append `.avg` to the metric name\.
+ Minimum – The minimum value for the metric over a period of time\. Append `.min` to the metric name\.
+ Maximum – The maximum value for the metric over a period of time\. Append `.max` to the metric name\.
+ Sum – The sum of the metric values over a period of time\. Append `.sum` to the metric name\.
+ Sample count – The number of times the metric was collected over a period of time\. Append `.sample_count` to the metric name\.

For example, assume that a metric is collected for 300 seconds \(5 minutes\), and that the metric is collected one time each minute\. The values for each minute are 1, 2, 3, 4, and 5\. In this case, the following calculations are returned:
+ Average – 3
+ Minimum – 1
+ Maximum – 5
+ Sum – 15
+ Sample count – 5

For information about using the `get-resource-metrics` AWS CLI command, see [ `get-resource-metrics`](https://docs.aws.amazon.com/cli/latest/reference/pi/get-resource-metrics.html)\.

For the `--metric-queries` option, specify one or more queries that you want to get results for\. Each query consists of a mandatory `Metric` and optional `GroupBy` and `Filter` parameters\. The following is an example of a `--metric-queries` option specification\.

```
{
   "Metric": "string",
   "GroupBy": {
     "Group": "string",
     "Dimensions": ["string", ...],
     "Limit": integer
   },
   "Filter": {"string": "string"
     ...}
}
```

## AWS CLI Examples for Performance Insights<a name="USER_PerfInsights.API.Examples"></a>

The following are several examples that use the AWS CLI for Performance Insights\.

**Topics**
+ [Retrieving Counter Metrics](#USER_PerfInsights.API.Examples.CounterMetrics)
+ [Retrieving the DB Load Average for Top Wait Events](#USER_PerfInsights.API.Examples.DBLoadAverage)
+ [Retrieving the DB Load Average for Top SQL](#USER_PerfInsights.API.Examples.DBLoadAverageTop10SQL)
+ [Retrieving the DB Load Average Filtered by SQL](#USER_PerfInsights.API.Examples.DBLoadAverageFilterBySQL)

### Retrieving Counter Metrics<a name="USER_PerfInsights.API.Examples.CounterMetrics"></a>

The following screenshot shows two counter metrics charts in the AWS Management Console\.

![\[Counter Metrics charts.\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/perf-insights-api-counters-charts.png)

The following example shows how to gather the same data that the AWS Management Console uses to generate the two counter metric charts\.

For Linux, macOS, or Unix:

```
aws pi get-resource-metrics \
   --service-type RDS \
   --identifier db-ID \
   --start-time 2018-10-30T00:00:00Z \
   --end-time   2018-10-30T01:00:00Z \
   --period-in-seconds 60 \
   --metric-queries '[{"Metric": "os.cpuUtilization.user.avg"  },
                      {"Metric": "os.cpuUtilization.idle.avg"}]'
```

For Windows:

```
aws pi get-resource-metrics ^
   --service-type RDS ^
   --identifier db-ID ^
   --start-time 2018-10-30T00:00:00Z ^
   --end-time   2018-10-30T01:00:00Z ^
   --period-in-seconds 60 ^
   --metric-queries '[{"Metric": "os.cpuUtilization.user.avg"  },
                      {"Metric": "os.cpuUtilization.idle.avg"}]'
```

You can also make a command easier to read by specifying a file for the `--metrics-query` option\. The following example uses a file called query\.json for the option\. The file has the following contents\.

```
[
    {
        "Metric": "os.cpuUtilization.user.avg"
    },
    {
        "Metric": "os.cpuUtilization.idle.avg"
    }
]
```

Run the following command to use the file\.

For Linux, macOS, or Unix:

```
aws pi get-resource-metrics \
   --service-type RDS \
   --identifier db-ID \
   --start-time 2018-10-30T00:00:00Z \
   --end-time   2018-10-30T01:00:00Z \
   --period-in-seconds 60 \
   --metric-queries file://query.json
```

For Windows:

```
aws pi get-resource-metrics ^
   --service-type RDS ^
   --identifier db-ID ^
   --start-time 2018-10-30T00:00:00Z ^
   --end-time   2018-10-30T01:00:00Z ^
   --period-in-seconds 60 ^
   --metric-queries file://query.json
```

The preceding example specifies the following values for the options:
+ `--service-type` – `RDS` for Amazon RDS
+ `--identifier` – The resource ID for the DB instance
+ `--start-time` and `--end-time` – The ISO 8601 `DateTime` values for the period to query, with multiple supported formats

It queries for a one\-hour time range:
+ `--period-in-seconds` – `60` for a per\-minute query
+ `--metric-queries` – An array of two queries, each just for one metric\.

  The metric name uses dots to classify the metric in a useful category, with the final element being a function\. In the example, the function is `avg` for each query\. As with Amazon CloudWatch, the supported functions are `min`, `max`, `total`, and `avg`\.

The response looks similar to the following\.

```
{
    "Identifier": "db-XXX",
    "AlignedStartTime": 1540857600.0,
    "AlignedEndTime": 1540861200.0,
    "MetricList": [
        { //A list of key/datapoints 
            "Key": {
                "Metric": "os.cpuUtilization.user.avg" //Metric1
            },
            "DataPoints": [
                //Each list of datapoints has the same timestamps and same number of items
                {
                    "Timestamp": 1540857660.0, //Minute1
                    "Value": 4.0
                },
                {
                    "Timestamp": 1540857720.0, //Minute2
                    "Value": 4.0
                },
                {
                    "Timestamp": 1540857780.0, //Minute 3
                    "Value": 10.0
                }
                //... 60 datapoints for the os.cpuUtilization.user.avg metric
            ]
        },
        {
            "Key": {
                "Metric": "os.cpuUtilization.idle.avg" //Metric2
            },
            "DataPoints": [
                {
                    "Timestamp": 1540857660.0, //Minute1
                    "Value": 12.0
                },
                {
                    "Timestamp": 1540857720.0, //Minute2
                    "Value": 13.5
                },
                //... 60 datapoints for the os.cpuUtilization.idle.avg metric 
            ]
        }
    ] //end of MetricList
} //end of response
```

The response has an `Identifier`, `AlignedStartTime`, and `AlignedEndTime`\. B the `--period-in-seconds` value was `60`, the start and end times have been aligned to the minute\. If the `--period-in-seconds` was `3600`, the start and end times would have been aligned to the hour\.

The `MetricList` in the response has a number of entries, each with a `Key` and a `DataPoints` entry\. Each `DataPoint` has a `Timestamp` and a `Value`\. Each `Datapoints` list has 60 data points because the queries are for per\-minute data over an hour, with `Timestamp1/Minute1`, `Timestamp2/Minute2`, and so on, up to `Timestamp60/Minute60`\. 

Because the query is for two different counter metrics, there are two elements in the response `MetricList`\.

### Retrieving the DB Load Average for Top Wait Events<a name="USER_PerfInsights.API.Examples.DBLoadAverage"></a>

The following example is the same query that the AWS Management Console uses to generate a stacked area line graph\. This example retrieves the `db.load.avg` for the last hour with load divided according to the top seven wait events\. The command is the same as the command in [Retrieving Counter Metrics](#USER_PerfInsights.API.Examples.CounterMetrics)\. However, the query\.json file has the following contents\.

```
[
    {
        "Metric": "db.load.avg",
        "GroupBy": { "Group": "db.wait_event", "Limit": 7 }
    }
]
```

Run the following command\.

For Linux, macOS, or Unix:

```
aws pi get-resource-metrics \
   --service-type RDS \
   --identifier db-ID \
   --start-time 2018-10-30T00:00:00Z \
   --end-time   2018-10-30T01:00:00Z \
   --period-in-seconds 60 \
   --metric-queries file://query.json
```

For Windows:

```
aws pi get-resource-metrics ^
   --service-type RDS ^
   --identifier db-ID ^
   --start-time 2018-10-30T00:00:00Z ^
   --end-time   2018-10-30T01:00:00Z ^
   --period-in-seconds 60 ^
   --metric-queries file://query.json
```

The example specifies the metric of `db.load.avg` and a `GroupBy` of the top seven wait events\. For details about valid values for this example, see [DimensionGroup](https://docs.aws.amazon.com/performance-insights/latest/APIReference/API_DimensionGroup.html) in the *Performance Insights API Reference\.*

The response looks similar to the following\.

```
{
    "Identifier": "db-XXX",
    "AlignedStartTime": 1540857600.0,
    "AlignedEndTime": 1540861200.0,
    "MetricList": [
        { //A list of key/datapoints 
            "Key": {
                //A Metric with no dimensions. This is the total db.load.avg
                "Metric": "db.load.avg"
            },
            "DataPoints": [
                //Each list of datapoints has the same timestamps and same number of items
                {
                    "Timestamp": 1540857660.0, //Minute1
                    "Value": 0.5166666666666667
                },
                {
                    "Timestamp": 1540857720.0, //Minute2
                    "Value": 0.38333333333333336
                },
                {
                    "Timestamp": 1540857780.0, //Minute 3
                    "Value": 0.26666666666666666
                }
                //... 60 datapoints for the total db.load.avg key
            ]
        },
        {
            "Key": {
                //Another key. This is db.load.avg broken down by CPU
                "Metric": "db.load.avg",
                "Dimensions": {
                    "db.wait_event.name": "CPU",
                    "db.wait_event.type": "CPU"
                }
            },
            "DataPoints": [
                {
                    "Timestamp": 1540857660.0, //Minute1
                    "Value": 0.35
                },
                {
                    "Timestamp": 1540857720.0, //Minute2
                    "Value": 0.15
                },
                //... 60 datapoints for the CPU key
            ]
        },
        //... In total we have 8 key/datapoints entries, 1) total, 2-8) Top Wait Events
    ] //end of MetricList
} //end of response
```

In this response, there are eight entries in the `MetricList`\. There is one entry for the total `db.load.avg`, and seven entries each for the `db.load.avg` divided according to one of the top seven wait events\. Unlike in the first example, because there was a grouping dimension, there must be one key for each grouping of the metric\. There can't be only one key for each metric, as in the basic counter metric use case\.

### Retrieving the DB Load Average for Top SQL<a name="USER_PerfInsights.API.Examples.DBLoadAverageTop10SQL"></a>

The following example groups `db.wait_events` by the top 10 SQL statements\. There are two different groups for SQL statements:
+ `db.sql` – The full SQL statement, such as `select * from customers where customer_id = 123`
+ `db.sql_tokenized` – The tokenized SQL statement, such as `select * from customers where customer_id = ?`

When analyzing database performance, it can be useful to consider SQL statements that only differ by their parameters as one logic item\. So, you can use `db.sql_tokenized` when querying\. However, especially when you are interested in explain plans, sometimes it's more useful to examine full SQL statements with parameters, and query grouping by `db.sql`\. There is a parent\-child relationship between tokenized and full SQL, with multiple full SQL \(children\) grouped under the same tokenized SQL \(parent\)\.

The command in this example is the similar to the command in [Retrieving the DB Load Average for Top Wait Events](#USER_PerfInsights.API.Examples.DBLoadAverage)\. However, the query\.json file has the following contents\.

```
[
    {
        "Metric": "db.load.avg",
        "GroupBy": { "Group": "db.sql_tokenized", "Limit": 10 }
    }
]
```

The following example uses `db.sql_tokenized`\.

For Linux, macOS, or Unix:

```
aws pi get-resource-metrics \
   --service-type RDS \
   --identifier db-ID \
   --start-time 2018-10-29T00:00:00Z \
   --end-time   2018-10-30T00:00:00Z \
   --period-in-seconds 3600 \
   --metric-queries file://query.json
```

For Windows:

```
aws pi get-resource-metrics ^
   --service-type RDS ^
   --identifier db-ID ^
   --start-time 2018-10-29T00:00:00Z ^
   --end-time   2018-10-30T00:00:00Z  ^
   --period-in-seconds 3600 ^
   --metric-queries file://query.json
```

This example queries over 24 hours, with a one hour period\-in\-seconds\.

The example specifies the metric of `db.load.avg` and a `GroupBy` of the top seven wait events\. For details about valid values for this example, see [DimensionGroup](https://docs.aws.amazon.com/performance-insights/latest/APIReference/API_DimensionGroup.html) in the *Performance Insights API Reference\.*

The response looks similar to the following\.

```
{
    "AlignedStartTime": 1540771200.0,
    "AlignedEndTime": 1540857600.0,
    "Identifier": "db-XXX",

    "MetricList": [ //11 entries in the MetricList
        {
            "Key": { //First key is total
                "Metric": "db.load.avg"
            }
            "DataPoints": [ //Each DataPoints list has 24 per-hour Timestamps and a value
                {
                    "Value": 1.6964980544747081,
                    "Timestamp": 1540774800.0
                },
                //... 24 datapoints
            ]
        },
        {
            "Key": { //Next key is the top tokenized SQL  
                "Dimensions": {
                    "db.sql_tokenized.statement": "INSERT INTO authors (id,name,email) VALUES\n( nextval(?)  ,?,?)",
                    "db.sql_tokenized.db_id": "pi-2372568224",
                    "db.sql_tokenized.id": "AKIAIOSFODNN7EXAMPLE"
                },
                "Metric": "db.load.avg"
            },
            "DataPoints": [ //... 24 datapoints 
            ]
        },
        // In total 11 entries, 10 Keys of top tokenized SQL, 1 total key 
    ] //End of MetricList
} //End of response
```

This response has 11 entries in the `MetricList` \(1 total, 10 top tokenized SQL\), with each entry having 24 per\-hour `DataPoints`\.

For tokenized SQL, there are three entries in each dimensions list:
+ `db.sql_tokenized.statement` – The tokenized SQL statement\.
+ `db.sql_tokenized.db_id ` – Either the native database ID used to refer to the SQL, or a synthetic ID that Performance Insights generates for you if the native database ID isn't available\. This example returns the `pi-2372568224` synthetic ID\.
+ `db.sql_tokenized.id` – The ID of the query inside Performance Insights\.

  In the AWS Management Console, this ID is called the Support ID\. It's named this because the ID is data that AWS Support can examine to help you troubleshoot an issue with your database\. AWS takes the security and privacy of your data extremely seriously, and almost all data is stored encrypted with your AWS KMS customer master key \(CMK\)\. Therefore, nobody inside AWS can look at this data\. In the example preceding, both the `tokenized.statement` and the `tokenized.db_id` are stored encrypted\. If you have an issue with your database, AWS Support can help you by referencing the Support ID\.

When querying, it might be convenient to specify a `Group` in `GroupBy`\. However, for finer\-grained control over the data that's returned, specify the list of dimensions\. For example, if all that is needed is the `db.sql_tokenized.statement`, then a `Dimensions` attribute can be added to the query\.json file\.

```
[
    {
        "Metric": "db.load.avg",
        "GroupBy": {
            "Group": "db.sql_tokenized",
            "Dimensions":["db.sql_tokenized.statement"],
            "Limit": 10
        }
    }
]
```

### Retrieving the DB Load Average Filtered by SQL<a name="USER_PerfInsights.API.Examples.DBLoadAverageFilterBySQL"></a>

![\[Filter by SQL chart.\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/perf-insights-api-filter-chart.png)

The preceding image shows that a particular query is selected, and the top average active sessions stacked area line graph is scoped to that query\. Although the query is still for the top seven overall wait events, the value of the response is filtered\. The filter causes it to take into account only sessions that are a match for the particular filter\.

The corresponding API query in this example is similar to the command in [Retrieving the DB Load Average for Top SQL](#USER_PerfInsights.API.Examples.DBLoadAverageTop10SQL)\. However, the query\.json file has the following contents\.

```
[
 {
        "Metric": "db.load.avg",
        "GroupBy": { "Group": "db.wait_event", "Limit": 5  }, 
        "Filter": { "db.sql_tokenized.id": "AKIAIOSFODNN7EXAMPLE" }
    }
]
```

For Linux, macOS, or Unix:

```
aws pi get-resource-metrics \
   --service-type RDS \
   --identifier db-ID \
   --start-time 2018-10-30T00:00:00Z \
   --end-time   2018-10-30T01:00:00Z \
   --period-in-seconds 60 \
   --metric-queries file://query.json
```

For Windows:

```
aws pi get-resource-metrics ^
   --service-type RDS ^
   --identifier db-ID ^
   --start-time 2018-10-30T00:00:00Z ^
   --end-time   2018-10-30T01:00:00Z ^
   --period-in-seconds 60 ^
   --metric-queries file://query.json
```

The response looks similar to the following\.

```
{
    "Identifier": "db-XXX", 
    "AlignedStartTime": 1556215200.0, 
    "MetricList": [
        {
            "Key": {
                "Metric": "db.load.avg"
            }, 
            "DataPoints": [
                {
                    "Timestamp": 1556218800.0, 
                    "Value": 1.4878117913832196
                }, 
                {
                    "Timestamp": 1556222400.0, 
                    "Value": 1.192823803967328
                }
            ]
        }, 
        {
            "Key": {
                "Metric": "db.load.avg", 
                "Dimensions": {
                    "db.wait_event.type": "io", 
                    "db.wait_event.name": "wait/io/aurora_redo_log_flush"
                }
            }, 
            "DataPoints": [
                {
                    "Timestamp": 1556218800.0, 
                    "Value": 1.1360544217687074
                }, 
                {
                    "Timestamp": 1556222400.0, 
                    "Value": 1.058051341890315
                }
            ]
        }, 
        {
            "Key": {
                "Metric": "db.load.avg", 
                "Dimensions": {
                    "db.wait_event.type": "io", 
                    "db.wait_event.name": "wait/io/table/sql/handler"
                }
            }, 
            "DataPoints": [
                {
                    "Timestamp": 1556218800.0, 
                    "Value": 0.16241496598639457
                }, 
                {
                    "Timestamp": 1556222400.0, 
                    "Value": 0.05163360560093349
                }
            ]
        }, 
        {
            "Key": {
                "Metric": "db.load.avg", 
                "Dimensions": {
                    "db.wait_event.type": "synch", 
                    "db.wait_event.name": "wait/synch/mutex/innodb/aurora_lock_thread_slot_futex"
                }
            }, 
            "DataPoints": [
                {
                    "Timestamp": 1556218800.0, 
                    "Value": 0.11479591836734694
                }, 
                {
                    "Timestamp": 1556222400.0, 
                    "Value": 0.013127187864644107
                }
            ]
        }, 
        {
            "Key": {
                "Metric": "db.load.avg", 
                "Dimensions": {
                    "db.wait_event.type": "CPU", 
                    "db.wait_event.name": "CPU"
                }
            }, 
            "DataPoints": [
                {
                    "Timestamp": 1556218800.0, 
                    "Value": 0.05215419501133787
                }, 
                {
                    "Timestamp": 1556222400.0, 
                    "Value": 0.05805134189031505
                }
            ]
        }, 
        {
            "Key": {
                "Metric": "db.load.avg", 
                "Dimensions": {
                    "db.wait_event.type": "synch", 
                    "db.wait_event.name": "wait/synch/mutex/innodb/lock_wait_mutex"
                }
            }, 
            "DataPoints": [
                {
                    "Timestamp": 1556218800.0, 
                    "Value": 0.017573696145124718
                }, 
                {
                    "Timestamp": 1556222400.0, 
                    "Value": 0.002333722287047841
                }
            ]
        }
    ], 
    "AlignedEndTime": 1556222400.0
} //end of response
```

In this response, all values are filtered according to the contribution of tokenized SQL AKIAIOSFODNN7EXAMPLE specified in the query\.json file\. The keys also might follow a different order than a query without a filter, because it's the top five wait events that affected the filtered SQL\.