# data-migration-sync
## Backup & Disaster recovery
- Recovery time objective (RTO):RTO is the maximum acceptable delay between the interruption of service and restoration of service. This determines an acceptable time window when service is unavailable.
- Recovery Point Objective (RPO):RPO is the maximum acceptable amount of time since the last data recovery point. This determines what is considered an acceptable loss of data between the last recovery point and the interruption of service.
  
|Feature                      |RTO|RPO|Cost|Scope|
|---                          | --- | --- | --- |--- |
|auto backups                 |good|better|low|single region|
|manual snapshots             |better|good|medium|cross region|
|read replicas                |best|best|high|cross region|

## NiFi
### QueryDatabaseTable
Runs on the Primary Node only because it is a single-source for fetching. It will not scale to a distributed/parallel solution such as Sqoop. 
### GenerateTableFetch -> ExecuteSQL
Instead of a single processor on a single node doing the full fetch, **GenerateTableFetch** will generate a number of flow files that each contain a SQL statement to fetch a "page" of data from the table. <br>
You can distribute the flow files among the cluster nodes using either a **Remote Process Group -> Input Port** on the same cluster, or in recent versions of NiFi you can use a Load-Balanced Connection between GenerateTableFetch and ExecuteSQL. Once the flow files are distributed among the cluster, each node can run ExecuteSQL on them in parallel and fetch a page of data at a time for downstream processing.
