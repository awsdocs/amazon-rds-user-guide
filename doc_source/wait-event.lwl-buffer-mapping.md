# LWLock:BufferMapping \(LWLock:buffer\_mapping\)<a name="wait-event.lwl-buffer-mapping"></a>

This event occurs when a session is waiting to associate a data block with a buffer in the shared buffer pool\.

**Note**  
This event is named `LWLock:BufferMapping` for RDS for PostgreSQL version 13 and higher versions\. For RDS for PostgreSQL version 12 and older versions, this event is named `LWLock:buffer_mapping`\. 

**Topics**
+ [Supported engine versions](#wait-event.lwl-buffer-mapping.context.supported)
+ [Context](#wait-event.lwl-buffer-mapping.context)
+ [Causes](#wait-event.lwl-buffer-mapping.causes)
+ [Actions](#wait-event.lwl-buffer-mapping.actions)

## Supported engine versions<a name="wait-event.lwl-buffer-mapping.context.supported"></a>

This wait event information is relevant for RDS for PostgreSQL version 9\.6 and higher\.

## Context<a name="wait-event.lwl-buffer-mapping.context"></a>

The *shared buffer pool* is a PostgreSQL memory area that holds all pages that are or were being used by processes\. When a process needs a page, it reads the page into the shared buffer pool\. The `shared_buffers` parameter sets the shared buffer size and reserves a memory area to store the table and index pages\. If you change this parameter, make sure to restart the database\. \.

The `LWLock:buffer_mapping` wait event occurs in the following scenarios:
+ A process searches the buffer table for a page and acquires a shared buffer mapping lock\.
+ A process loads a page into the buffer pool and acquires an exclusive buffer mapping lock\.
+ A process removes a page from the pool and acquires an exclusive buffer mapping lock\.

## Causes<a name="wait-event.lwl-buffer-mapping.causes"></a>

When this event appears more than normal, possibly indicating a performance problem, the database is paging in and out of the shared buffer pool\. Typical causes include the following:
+ Large queries
+ Bloated indexes and tables
+ Full table scans
+ A shared pool size that is smaller than the working set

## Actions<a name="wait-event.lwl-buffer-mapping.actions"></a>

We recommend different actions depending on the causes of your wait event\.

**Topics**
+ [Monitor buffer\-related metrics](#wait-event.lwl-buffer-mapping.actions.monitor-metrics)
+ [Assess your indexing strategy](#wait-event.lwl-buffer-mapping.actions.indexes)
+ [Reduce the number of buffers that must be allocated quickly](#wait-event.lwl-buffer-mapping.actions.buffers)

### Monitor buffer\-related metrics<a name="wait-event.lwl-buffer-mapping.actions.monitor-metrics"></a>

When `LWLock:buffer_mapping` waits spike, investigate the buffer hit ratio\. You can use these metrics to get a better understanding of what is happening in the buffer cache\. Examine the following metrics:

`BufferCacheHitRatio`  
This Amazon CloudWatch metric measures the percentage of requests that are served by the buffer cache of a DB instance in your DB instance\. You might see this metric decrease in the lead\-up to the `LWLock:buffer_mapping` wait event\.

`blks_hit`  
This Performance Insights counter metric indicates the number of blocks that were retrieved from the shared buffer pool\. After the `LWLock:buffer_mapping` wait event appears, you might observe a spike in `blks_hit`\.

`blks_read`  
This Performance Insights counter metric indicates the number of blocks that required I/O to be read into the shared buffer pool\. You might observe a spike in `blks_read` in the lead\-up to the `LWLock:buffer_mapping` wait event\.

### Assess your indexing strategy<a name="wait-event.lwl-buffer-mapping.actions.indexes"></a>

To confirm that your indexing strategy is not degrading performance, check the following:

Index bloat  
Ensure that index and table bloat aren't leading to unnecessary pages being read into the shared buffer\. If your tables contain unused rows, consider archiving the data and removing the rows from the tables\. You can then rebuild the indexes for the resized tables\.

Indexes for frequently used queries  
To determine whether you have the optimal indexes, monitor DB engine metrics in Performance Insights\. The `tup_returned` metric shows the number of rows read\. The `tup_fetched` metric shows the number of rows returned to the client\. If `tup_returned` is significantly larger than `tup_fetched`, the data might not be properly indexed\. Also, your table statistics might not be current\.

### Reduce the number of buffers that must be allocated quickly<a name="wait-event.lwl-buffer-mapping.actions.buffers"></a>

To reduce the `LWLock:buffer_mapping` wait events, try to reduce the number of buffers that must be allocated quickly\. One strategy is to perform smaller batch operations\. You might be able to achieve smaller batches by partitioning your tables\.