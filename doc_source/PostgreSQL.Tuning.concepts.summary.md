# RDS for PostgreSQL wait events<a name="PostgreSQL.Tuning.concepts.summary"></a>

The following table lists the wait events for RDS for PostgreSQL that most commonly indicate performance problems, and summarizes the most common causes and corrective actions\.\.


| Wait event | Definition | 
| --- | --- | 
|  [Client:ClientRead](wait-event.clientread.md)  |  This event occurs when RDS for PostgreSQL is waiting to receive data from the client\.  | 
|  [Client:ClientWrite](wait-event.clientwrite.md)  |  This event occurs when RDS for PostgreSQL is waiting to write data to the client\.  | 
|  [CPU](wait-event.cpu.md)  | This event occurs when a thread is active in CPU or is waiting for CPU\.  | 
|  [IO:BufFileRead and IO:BufFileWrite](wait-event.iobuffile.md)  |  These events occur when RDS for PostgreSQL creates temporary files\.  | 
|  [IO:DataFileRead](wait-event.iodatafileread.md)  |  This event occurs when a connection waits on a backend process to read a required page from storage because the page isn't available in shared memory\.   | 
| [IO:WALWrite](wait-event.iowalwrite.md)  | This event occurs when RDS for PostgreSQL is waiting for the write\-ahead log \(WAL\) buffers to be written to a WAL file\.  | 
|  [Lock:advisory](wait-event.lockadvisory.md)  |  This event occurs when a PostgreSQL application uses a lock to coordinate activity across multiple sessions\.  | 
|  [Lock:extend](wait-event.lockextend.md) |  This event occurs when a backend process is waiting to lock a relation to extend it while another process has a lock on that relation for the same purpose\.  | 
|  [Lock:Relation](wait-event.lockrelation.md)  |  This event occurs when a query is waiting to acquire a lock on a table or view that's currently locked by another transaction\.  | 
|  [Lock:transactionid](wait-event.locktransactionid.md)  | This event occurs when a transaction is waiting for a row\-level lock\. | 
|  [Lock:tuple](wait-event.locktuple.md)  |  This event occurs when a backend process is waiting to acquire a lock on a tuple\.  | 
|  [LWLock:BufferMapping \(LWLock:buffer\_mapping\)](wait-event.lwl-buffer-mapping.md)  |  This event occurs when a session is waiting to associate a data block with a buffer in the shared buffer pool\.  | 
|  [LWLock:BufferIO](wait-event.lwlockbufferio.md)  |  This event occurs when RDS for PostgreSQL is waiting for other processes to finish their input/output \(I/O\) operations when concurrently trying to access a page\.  | 
|  [LWLock:buffer\_content \(BufferContent\)](wait-event.lwlockbuffercontent.md)  |  This event occurs when a session is waiting to read or write a data page in memory while another session has that page locked for writing\.  | 
|  [LWLock:lock\_manager \(LWLock:lockmanager\)](wait-event.lw-lock-manager.md)  | This event occurs when the RDS for PostgreSQL engine maintains the shared lock's memory area to allocate, check, and deallocate a lock when a fast path lock isn't possible\. | 
|  [Timeout:PgSleep](wait-event.timeoutpgsleep.md)  |  This event occurs when a server process has called the `pg_sleep` function and is waiting for the sleep timeout to expire\.   | 
|  [Timeout:VacuumDelay](wait-event.timeoutvacuumdelay.md)  | This event indicates that the vacuum process is sleeping because the estimated cost limit has been reached\.  | 