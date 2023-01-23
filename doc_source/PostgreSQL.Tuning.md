# Tuning with wait events for RDS for PostgreSQL<a name="PostgreSQL.Tuning"></a>

Wait events are an important tuning tool for RDS for PostgreSQL\. When you can find out why sessions are waiting for resources and what they are doing, you're better able to reduce bottlenecks\. You can use the information in this section to find possible causes and corrective actions\. This section also discusses basic PostgreSQL tuning concepts\.

The wait events in this section are specific to RDS for PostgreSQL\.

**Topics**
+ [Essential concepts for RDS for PostgreSQL tuning](PostgreSQL.Tuning.concepts.md)
+ [RDS for PostgreSQL wait events](PostgreSQL.Tuning.concepts.summary.md)
+ [Client:ClientRead](wait-event.clientread.md)
+ [Client:ClientWrite](wait-event.clientwrite.md)
+ [CPU](wait-event.cpu.md)
+ [IO:BufFileRead and IO:BufFileWrite](wait-event.iobuffile.md)
+ [IO:DataFileRead](wait-event.iodatafileread.md)
+ [IO:WALWrite](wait-event.iowalwrite.md)
+ [Lock:advisory](wait-event.lockadvisory.md)
+ [Lock:extend](wait-event.lockextend.md)
+ [Lock:Relation](wait-event.lockrelation.md)
+ [Lock:transactionid](wait-event.locktransactionid.md)
+ [Lock:tuple](wait-event.locktuple.md)
+ [LWLock:BufferMapping \(LWLock:buffer\_mapping\)](wait-event.lwl-buffer-mapping.md)
+ [LWLock:BufferIO](wait-event.lwlockbufferio.md)
+ [LWLock:buffer\_content \(BufferContent\)](wait-event.lwlockbuffercontent.md)
+ [LWLock:lock\_manager \(LWLock:lockmanager\)](wait-event.lw-lock-manager.md)
+ [Timeout:PgSleep](wait-event.timeoutpgsleep.md)
+ [Timeout:VacuumDelay](wait-event.timeoutvacuumdelay.md)