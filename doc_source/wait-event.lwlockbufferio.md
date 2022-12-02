# LWLock:BufferIO<a name="wait-event.lwlockbufferio"></a>

The `LWLock:BufferIO` event occurs when RDS for PostgreSQL is waiting for other processes to finish their input/output \(I/O\) operations when concurrently trying to access a page\. Its purpose is for the same page to be read into the shared buffer\.

**Topics**
+ [Relevant engine versions](#wait-event.lwlockbufferio.context.supported)
+ [Context](#wait-event.lwlockbufferio.context)
+ [Causes](#wait-event.lwlockbufferio.causes)
+ [Actions](#wait-event.lwlockbufferio.actions)

## Relevant engine versions<a name="wait-event.lwlockbufferio.context.supported"></a>

This wait event information is relevant for all RDS for PostgreSQL versions\. For RDS for PostgreSQL versions earlier than PostgreSQL 14, this wait event type is `lwlock`\. For PostgreSQL 14 and later releases, the wait\_event type is `IPC`\. 

## Context<a name="wait-event.lwlockbufferio.context"></a>

Each shared buffer has an I/O lock that is associated with the `LWLock:BufferIO` wait event, each time a block \(or a page\) has to be retrieved outside the shared buffer pool\.

This lock is used to handle multiple sessions that all require access to the same block\. This block has to be read from outside the shared buffer pool, defined by the `shared_buffers` parameter\.

As soon as the page is read inside the shared buffer pool, the `LWLock:BufferIO` lock is released\.

**Note**  
The `LWLock:BufferIO` wait event precedes the [IO:DataFileRead](wait-event.iodatafileread.md) wait event\. The `IO:DataFileRead` wait event occurs while data is being read from storage\.

For more information on lightweight locks, see [Locking Overview](https://github.com/postgres/postgres/blob/65dc30ced64cd17f3800ff1b73ab1d358e92efd8/src/backend/storage/lmgr/README#L20)\.

## Causes<a name="wait-event.lwlockbufferio.causes"></a>

Common causes for the `LWLock:BufferIO` event to appear in top waits include the following:
+ Multiple backends or connections trying to access the same page that's also pending an I/O operation
+ The ratio between the size of the shared buffer pool \(defined by the `shared_buffers` parameter\) and the number of buffers needed by the current workload
+ The size of the shared buffer pool not being well balanced with the number of pages being evicted by other operations
+ Large or bloated indexes that require the engine to read more pages than necessary into the shared buffer pool
+ Lack of indexes that forces the DB engine to read more pages from the tables than necessary
+ Checkpoints occurring too frequently or needing to flush too many modified pages
+ Sudden spikes for database connections trying to perform operations on the same page

## Actions<a name="wait-event.lwlockbufferio.actions"></a>

We recommend different actions depending on the causes of your wait event:
+ Observe Amazon CloudWatch metrics for correlation between sharp decreases in the `BufferCacheHitRatio` and `LWLock:BufferIO` wait events\. This effect can mean that you have a small shared buffers setting\. You might need to increase it or scale up your DB instance class\. You can split your workload into more reader nodes\.
+ Tune `max_wal_size` and `checkpoint_timeout` based on your workload peak time if you see `LWLock:BufferIO` coinciding with `BufferCacheHitRatio` metric dips\. Then identify which query might be causing it\.
+ Verify whether you have unused indexes, then remove them\.
+ Use partitioned tables \(which also have partitioned indexes\)\. Doing this helps to keep index reordering low and reduces its impact\.
+ Avoid indexing columns unnecessarily\.
+ Prevent sudden database connection spikes by using a connection pool\.
+ Restrict the maximum number of connections to the database as a best practice\.