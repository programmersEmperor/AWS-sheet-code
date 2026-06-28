---
title: "RDS and Aurora Scenario Phrases Quick Review"
description: "SAA-C03 focused decision table for Amazon RDS and Amazon Aurora scenarios."
---

# RDS and Aurora Scenario Phrases Quick Review

Use this as a fast exam reference.

The goal is not to memorize service definitions. The goal is to map **scenario phrases** to the correct **RDS/Aurora feature**.

## Core rule

| If the scenario is about... | Think... |
|---|---|
| AZ failure | Multi-AZ |
| Read scaling | Read Replica or Aurora Replica |
| Reader count scaling | Aurora Auto Scaling |
| Existing DB instance capacity scaling | Aurora Serverless v2 |
| Region-level DR with very low RPO/RTO | Aurora Global Database |
| Too many DB connections | RDS Proxy |
| DB process CPU/memory | Enhanced Monitoring |
| Slow SQL queries / database load | Performance Insights |
| Restore to a specific time | Point-in-time recovery |
| Long-term backup | Manual snapshots or AWS Backup |
| Encrypt data at rest | KMS encryption |
| Encrypt data in transit | TLS/SSL |
| Restrict network reachability | Security groups and private subnets |
| Avoid hardcoded DB passwords | Secrets Manager or IAM database authentication |

---

## Scenario phrase table

| Scenario phrase / requirement | Category | Choose | Avoid / trap | Memory rule |
|---|---|---|---|---|
| Database must remain available if one Availability Zone fails | High availability | RDS Multi-AZ or Aurora with replica in another AZ | Read replica alone for normal RDS HA | Multi-AZ is for AZ failure |
| Need synchronous standby for RDS | High availability | RDS Multi-AZ DB instance deployment | RDS Read Replica | Multi-AZ standby is synchronous |
| Need automatic failover in same Region | High availability | Multi-AZ | Snapshot restore | HA is not backup |
| RDS primary instance fails and app needs failover | High availability | Multi-AZ failover | Manual snapshot restore | Fail over, do not restore |
| Mission-critical relational DB across AZs | High availability | Multi-AZ | Single-AZ plus backups | Backups recover later; Multi-AZ keeps service available |
| Need lower RTO inside one Region | High availability | Multi-AZ or Aurora Replica failover | Read-only replica without failover planning | Standby/replica improves failover |
| Need HA and readable standbys for RDS cluster | High availability + read scaling | RDS Multi-AZ DB cluster | Traditional Multi-AZ DB instance if readable standbys required | Multi-AZ DB cluster has readable replicas |
| Need to offload SELECT queries from primary DB | Read scaling | Read Replica / Aurora Replica | Multi-AZ standby | Read replica is for reads |
| Reporting queries slow production DB | Read scaling | Read Replica | Bigger primary instance as first choice | Offload reporting reads |
| Analytics team must query production data without affecting app | Read scaling | RDS Read Replica | Multi-AZ standby | Standby is not for analytics reads |
| Application is read-heavy | Read scaling | Add read replicas / Aurora Replicas | Multi-AZ only | Scale reads horizontally |
| Aurora reads are increasing | Read scaling | Add Aurora Replicas | Increase writer only | Readers handle reads |
| Aurora read replica cannot keep up during spikes | Automatic read scaling | Aurora Auto Scaling | Increase cluster size vaguely | Auto Scaling adds/removes readers |
| Need automatic add/remove Aurora readers | Automatic read scaling | Aurora Auto Scaling | Reader endpoint alone | Reader endpoint balances existing readers only |
| Need minimum and maximum number of Aurora readers | Automatic read scaling | Aurora Auto Scaling policy | Serverless v2 alone | Auto Scaling controls reader count |
| Need to distribute reads across existing Aurora readers | Endpoint routing | Aurora reader endpoint | Aurora Auto Scaling if count is enough | Reader endpoint balances connections to existing readers |
| Need to route writes in Aurora | Endpoint routing | Cluster/writer endpoint | Reader endpoint | Writes go to writer endpoint |
| Need to connect to one specific Aurora instance | Endpoint routing | Instance endpoint | Reader endpoint | Instance endpoint targets one DB instance |
| Need production reads on high-capacity readers and reporting reads on low-capacity readers | Endpoint routing | Aurora custom endpoints | Reader endpoint | Custom endpoint means selected replica group |
| Need separate reader groups by workload | Endpoint routing | Aurora custom endpoints | Instance endpoint hardcoding | Custom endpoints route to subsets |
| Aurora readers have different instance classes | Endpoint routing | Custom endpoints | Reader endpoint only | Different capacities need custom routing |
| Need global users to read with low latency | Global read performance | Aurora Global Database secondary Regions | Multi-AZ only | Global reads need global database |
| Need disaster recovery from Region failure | Disaster recovery | Aurora Global Database or cross-Region read replica depending RPO/RTO | Multi-AZ only | Multi-AZ is not Region DR |
| RPO around 1 second and RTO under 1 minute for relational DB | Disaster recovery | Aurora Global Database | Snapshot restore | Very low RPO/RTO means global database |
| Need primary Region and read-only secondary Regions | Disaster recovery | Aurora Global Database | RDS Multi-AZ | Global Database spans Regions |
| Need cross-Region relational DR, but low cost and not very low RTO | Disaster recovery | Cross-Region read replica or backup copy | Aurora Global Database if overkill | Match DR cost to RTO/RPO |
| Need restore DB to 10:35 AM yesterday | Backup/restore | Point-in-time recovery | Manual snapshot only | Specific time means PITR |
| Need recover data from accidental deletion | Backup/restore | Automated backups / PITR / snapshots | Multi-AZ only | HA does not undo bad SQL |
| Need long-term backup retention | Backup/restore | Manual snapshot or AWS Backup | Automated backup only | Manual snapshots last until deleted |
| Need scheduled backup management across accounts/services | Backup/restore | AWS Backup | Manual-only process | Central backup policy means AWS Backup |
| Need create copy of DB for testing quickly and cheaply | Clone/testing | Aurora cloning | Full snapshot restore if Aurora clone available | Aurora clone is fast and space-efficient |
| Need staging DB replicates production DB for development | Deployment | RDS Blue/Green Deployments | Modify production directly | Blue/green reduces production risk |
| Need handle many Lambda connections to RDS/Aurora | Connection scaling | RDS Proxy | Bigger DB as first answer | Too many connections means proxy |
| Need connection pooling for serverless app | Connection scaling | RDS Proxy | API Gateway | API Gateway does not pool DB connections |
| Need improve failover behavior while preserving app connections | Connection resilience | RDS Proxy | Direct DB connections only | Proxy improves connection resilience |
| App opens/closes many short-lived DB connections | Connection scaling | RDS Proxy | Read replica | Short-lived connections need pooling |
| Need unpredictable relational DB capacity scaling | Capacity scaling | Aurora Serverless v2 | Aurora Auto Scaling if writer capacity issue | Serverless v2 scales ACUs |
| Workload has sudden unpredictable DB load | Capacity scaling | Aurora Serverless v2 | Provisioned fixed instance if no scaling | Serverless v2 scales capacity |
| Existing serverless writer needs more CPU/memory | Capacity scaling | Aurora Serverless v2 ACU scaling | Aurora Auto Scaling | ACU scaling is capacity scaling |
| Existing serverless reader needs more capacity | Capacity scaling | Aurora Serverless v2 ACU scaling | Reader endpoint only | Existing reader grows in ACUs |
| Need more reader instances, not just stronger readers | Reader count scaling | Aurora Auto Scaling | Serverless v2 alone | Count scaling is Auto Scaling |
| Need more writer instances in standard Aurora | Write scaling | Usually not possible as normal multi-writer; optimize, scale instance, shard, or consider special architecture | Aurora Auto Scaling | Standard Aurora has one writer |
| Need relational DB but highly variable traffic | Capacity scaling | Aurora Serverless v2 | RDS read replica | Serverless v2 for variable capacity |
| Need monitor CPUUtilization, connections, free storage | Monitoring | CloudWatch metrics | Enhanced Monitoring if OS process detail needed | Normal metrics mean CloudWatch |
| Need process/thread CPU and memory on DB host | Monitoring | Enhanced Monitoring | Basic CloudWatch CPU | Process-level OS metrics mean Enhanced Monitoring |
| Need OS metrics at high granularity | Monitoring | Enhanced Monitoring | Performance Insights only | OS-level means Enhanced Monitoring |
| Need know which SQL queries cause DB load | Monitoring | Performance Insights | Enhanced Monitoring | SQL load means Performance Insights |
| Need database load by waits, SQL, users, hosts | Monitoring | Performance Insights | CloudWatch only | Query diagnosis means Performance Insights |
| Need alarms on normal RDS metrics | Monitoring | CloudWatch Alarms | Performance Insights dashboard only | Alarms usually use CloudWatch |
| Need export DB logs for auditing/troubleshooting | Logging | RDS/Aurora log export to CloudWatch Logs | Enhanced Monitoring | Engine logs are different from metrics |
| Need encrypt database storage | Encryption at rest | RDS/Aurora encryption with KMS | TLS only | At rest means KMS |
| Need control encryption key yourself | Encryption at rest | Customer managed KMS key | AWS-owned/default only | Control key means CMK |
| Need HSM-backed key material | Encryption/key control | KMS custom key store with CloudHSM | Normal KMS key | HSM requirement means CloudHSM custom key store |
| Need encrypt DB connection from app | Encryption in transit | TLS/SSL | KMS only | In transit means TLS |
| Need force PostgreSQL clients to use SSL | Encryption in transit | DB parameter such as rds.force_ssl | Private subnet only | Private network is not encryption |
| Need avoid hardcoded DB password | Credential management | Secrets Manager or IAM database authentication | Plain env variables | Do not hardcode passwords |
| Need rotate DB credentials automatically | Credential management | Secrets Manager rotation / RDS-managed master password | IAM policy alone | Rotation means Secrets Manager |
| Need login to DB using IAM token instead of password | Authentication | IAM database authentication | IAM policy only | IAM DB auth replaces password with token |
| Need central DB authentication using corporate directory | Authentication | Kerberos / Directory Service if supported | Simple password-only users | Directory auth means Kerberos/AD pattern |
| Need allow EC2/ECS/Lambda app to reach DB | Network access | DB security group allows app security group on DB port | 0.0.0.0/0 | Allow source SG, not the world |
| DB must not be reachable from internet | Network access | Private subnets, not publicly accessible, restrictive SG | Public DB with password only | Private DB means no public exposure |
| Need allow developers to create/modify/delete RDS resources | AWS permissions | IAM policy for RDS actions | Database grants | IAM controls AWS API actions |
| Need allow app user to SELECT/INSERT tables | DB permissions | Database user/role privileges | IAM policy only | SQL access is DB privilege |
| Need least privilege for app database access | DB permissions | Dedicated DB user with minimal grants | Master user in app | App should not use admin user |
| Need MySQL/PostgreSQL compatibility with better cloud-native scaling | Engine choice | Aurora MySQL/PostgreSQL | RDS Oracle/SQL Server | Aurora is MySQL/PostgreSQL compatible |
| Need managed Oracle database | Engine choice | Amazon RDS for Oracle | Aurora | Aurora is not Oracle |
| Need managed SQL Server database | Engine choice | Amazon RDS for SQL Server | Aurora | Aurora is not SQL Server |
| Need MongoDB-compatible database | Engine choice | Amazon DocumentDB, not RDS/Aurora | Aurora | MongoDB compatibility is DocumentDB |
| Need graph database | Engine choice | Amazon Neptune, not RDS/Aurora | Aurora | Graph means Neptune |
| Need OLTP relational application database | Database type | RDS or Aurora | Redshift/Athena | OLTP means relational DB |
| Need OLAP warehouse analytics | Database type | Redshift, not usually RDS/Aurora | RDS primary | OLAP means warehouse |
| Need ad-hoc SQL directly on S3 | Database type | Athena, not RDS/Aurora | RDS | Query S3 means Athena |
| Need cache repeated reads | Performance/cache | ElastiCache | Read replica for every case | Cache is different from replica |
| Need reduce DB read latency for hot data | Performance/cache | ElastiCache | Multi-AZ | Cache handles hot reads |
| Need database migration with minimal downtime | Migration | AWS DMS | Manual dump only | Minimal downtime migration means DMS |
| Need convert schema between engines | Migration | AWS Schema Conversion Tool | DMS alone | Schema conversion means SCT |
| Need homogeneous migration Oracle to Oracle | Migration | DMS to RDS Oracle, usually no SCT needed | Aurora if no app changes allowed | Same engine is homogeneous |
| Need rehost database server as-is on EC2 | Migration | AWS Application Migration Service or EC2 self-managed DB | RDS if unsupported customizations required | Rehost means lift-and-shift |
| Need managed DB and less operational overhead | Operations | RDS/Aurora | Self-managed DB on EC2 | Managed DB reduces ops |
| Need OS-level DB server access | Operations | EC2 self-managed DB or RDS Custom depending engine | Standard RDS | Standard RDS hides OS |
| Need automated patching/backups/failover managed by AWS | Operations | RDS/Aurora | Self-managed EC2 DB | Managed means RDS/Aurora |
| Need lowest cost for dev/test DB stopped outside work hours | Cost | Stop RDS where supported / scheduler | Reserved instances if only part-time | Part-time workload means stop/schedule |
| Need steady production database for 1 or 3 years | Cost | Reserved DB instance / Savings-style commitment where applicable | On-demand only | Steady usage can use reservation |
| Need read scaling only during peaks | Cost/performance | Aurora Auto Scaling | Always max replicas | Scale readers when needed |
| Need variable DB capacity and pay for consumed capacity | Cost/performance | Aurora Serverless v2 | Large provisioned instance always on | Variable capacity means Serverless v2 |
| Need protect DB from accidental deletion | Protection | Deletion protection plus backups/snapshots | Multi-AZ only | Deletion protection prevents accidental delete |
| Need prevent data loss from user deleting rows | Protection | Backups/PITR, not Multi-AZ | Multi-AZ | Multi-AZ replicates bad deletes too |
| Need replicate data changes from Aurora deletion event to processing system | Integration | Aurora native Lambda invocation / DB trigger pattern if engine supports | RDS event subscription | RDS events are DB instance events, not row events |
| Need notify on RDS instance failover/backup/config events | Integration | RDS event notifications/EventBridge/SNS | SQL trigger | Service events are not row changes |
| Need application reads after failover without changing hostname | Endpoint/failover | Cluster endpoint / RDS endpoint DNS failover | Hardcoded instance IP | Use endpoints, not IPs |
| Need avoid DNS cache issues during failover | Failover design | Low DNS TTL / app retry logic / RDS Proxy | Long-lived DNS cache | Apps must handle failover |
| Need multi-master writes in Aurora | Advanced architecture | Aurora multi-master is not normal SAA default; be careful | Aurora Auto Scaling | Standard Aurora is single writer |
| Need petabyte-scale relational-style Aurora storage | Storage scaling | Aurora automatic storage scaling | Manual EBS volume resizing | Aurora storage scales automatically |
| Data grows but compute load stays same | Storage scaling | Aurora storage auto-scaling | Aurora Auto Scaling | Storage and compute scaling are different |
| Storage layer is replicated across AZs | Aurora architecture | Aurora cluster volume replication | Read replicas | Storage replication is not reader instances |
| New basic Aurora cluster created | Defaults | 1 writer, 0 readers unless added | Assuming readers exist automatically | Readers are optional |
| New Aurora Serverless v2 cluster created | Defaults | 1 serverless writer, 0 readers unless added | Assuming readers auto-created | Serverless scales ACUs, not count |
| Need faster Aurora failover | HA/failover | Add Aurora Replicas in other AZs | Rely only on storage replication | Replica can be promoted |
| Aurora cluster has no replicas and writer fails | HA/failover | Aurora recreates primary; slower than replica promotion | Instant reader promotion | No reader means no reader to promote |
| Need max Aurora read replicas | Quota | Up to 15 Aurora Replicas | Unlimited replicas | Aurora replica count has a quota |
| Need up to five workload-specific endpoints | Endpoint routing | Aurora custom endpoints | Unlimited custom endpoints | Custom endpoint count is limited |
| Need read-only secondary Regions | Global database | Aurora Global Database | Multi-AZ | Secondary Regions are read-only until failover |
| Need writes in secondary Region without failover | Global write design | Usually not Aurora Global Database normal mode | Multi-AZ | Writes go to primary Region |
| Need increase RDS storage automatically | Storage scaling | RDS storage autoscaling if configured | Read replica | RDS and Aurora storage scaling differ |
| Need Aurora storage grows automatically | Storage scaling | Aurora automatic storage growth | Manual resize | Aurora storage auto grows |
| Need improve write throughput | Write performance | Scale writer instance, optimize indexes/queries, storage/engine tuning, partition/shard if needed | Read replicas | Read replicas do not scale writes |
| Need reduce connection storms during Lambda burst | Connection scaling | RDS Proxy | Aurora Auto Scaling | Auto Scaling adds readers, not connection pooling |
| Need API burst handling plus DB connection control | Serverless architecture | API Gateway/Lambda plus RDS Proxy | Lambda alone | Lambda scales faster than DB connections |
| Need API burst handling plus DB capacity scaling | Serverless architecture | API Gateway/Lambda plus Aurora Serverless v2 | API Gateway only | API scale and DB scale are separate |

---

## Aurora architecture defaults and scaling

| Thing | Provisioned Aurora | Aurora Serverless v2 |
|---|---|---|
| Default writer | 1 writer DB instance | 1 serverless writer DB instance |
| Default readers | 0 unless you add them | 0 unless you add them |
| Storage replication | Automatic across AZs | Automatic across AZs |
| Storage scaling | Automatic | Automatic |
| Writer compute scaling | Manual instance class change | ACU scaling within min/max |
| Reader compute scaling | Manual instance class change | ACU scaling within min/max |
| Reader count scaling | Aurora Auto Scaling if configured | Aurora Auto Scaling if configured |
| Reader endpoint | Balances across existing readers | Balances across existing readers |
| Auto Scaling creates writer? | No | No |
| Auto Scaling creates readers? | Yes, if configured | Yes, if configured |

---

## Endpoints quick table

| Endpoint | What it connects to | Use it for |
|---|---|---|
| Cluster/writer endpoint | Current writer DB instance | Writes and general writer connection |
| Reader endpoint | Available Aurora Replicas | General read-only traffic |
| Instance endpoint | One specific DB instance | Troubleshooting or targeting one instance |
| Custom endpoint | Selected group of DB instances | Separate workloads such as reporting vs production reads |
| Global database writer endpoint | Primary Region writer cluster | Writes in Aurora Global Database |

---

## Monitoring quick table

| Requirement phrase | Choose |
|---|---|
| CPUUtilization, DatabaseConnections, FreeStorageSpace | CloudWatch metrics |
| Alarms on RDS metrics | CloudWatch Alarms |
| Process/thread CPU and memory | Enhanced Monitoring |
| OS metrics with high granularity | Enhanced Monitoring |
| Slow SQL query analysis | Performance Insights |
| DB load by SQL/waits/users/hosts | Performance Insights |
| DB engine logs | RDS logs / CloudWatch Logs export |
| RDS Proxy connection metrics | CloudWatch RDS Proxy metrics |

---

## Security quick table

| Requirement phrase | Choose |
|---|---|
| Encrypt DB storage | KMS encryption at rest |
| Control the encryption key | Customer managed KMS key |
| HSM-backed key material | KMS custom key store with CloudHSM |
| Encrypt traffic to DB | TLS/SSL |
| Force SSL for PostgreSQL | `rds.force_ssl` parameter |
| Avoid hardcoded DB passwords | Secrets Manager or IAM database authentication |
| Rotate DB password | Secrets Manager |
| Use IAM token instead of password | IAM database authentication |
| Control who can create/modify/delete RDS | IAM policy |
| Control who can run SQL | DB users/roles/grants |
| Allow only app tier to reach DB | DB security group allows app security group |
| DB must not be internet-reachable | Private subnet, not publicly accessible, restrictive SG |

---

## Common traps

| Trap | Correct thinking |
|---|---|
| Aurora scales by default | Aurora storage scales by default; readers and provisioned compute do not automatically appear |
| Serverless v2 creates readers automatically | Serverless v2 scales ACUs of existing instances; Aurora Auto Scaling changes reader count |
| Reader endpoint creates read replicas | Reader endpoint only balances across existing readers |
| Multi-AZ improves read scaling | Traditional RDS Multi-AZ standby is for HA, not read scaling |
| Read replica gives synchronous HA | RDS read replicas are for read scaling and are typically asynchronous |
| Snapshot gives high availability | Snapshot is backup/recovery, not live failover |
| Private subnet means encrypted | Private subnet is isolation; TLS is encryption in transit |
| IAM permission means SQL permission | IAM controls AWS API access; DB grants control SQL |
| KMS controls who can connect to DB | KMS encrypts data; network and DB auth control connection |
| CloudWatch CPU shows DB process details | Enhanced Monitoring is for process/OS-level details |
| Performance Insights is for OS metrics | Performance Insights is for DB load and SQL analysis |
| RDS event subscription detects row deletion | RDS events are service-level events, not row-level table changes |
| Read replica solves write bottleneck | Read replicas scale reads, not writes |
| Auto Scaling solves writer bottleneck | Aurora Auto Scaling adds/removes readers, not the writer |

---

## One-line exam rules

- **Multi-AZ = AZ failure protection.**
- **Read Replica = read scaling.**
- **Aurora Auto Scaling = add/remove Aurora readers.**
- **Aurora Serverless v2 = scale ACUs of existing writer/readers.**
- **Reader endpoint = distribute reads across existing readers.**
- **Custom endpoint = selected reader group.**
- **Aurora Global Database = Region-level DR and global reads.**
- **RDS Proxy = too many DB connections.**
- **Enhanced Monitoring = OS/process metrics.**
- **Performance Insights = SQL/database load.**
- **PITR = restore to a specific time.**
- **KMS = encryption at rest.**
- **TLS = encryption in transit.**
- **Security group = network permission.**
- **IAM = AWS API permission.**
- **DB grants = SQL permission.**
- **Secrets Manager = store/rotate DB credentials.**

---

## Official AWS references

- [What is Amazon RDS?](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Welcome.html)
- [Amazon RDS Multi-AZ failover](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Concepts.MultiAZ.Failover.html)
- [Amazon RDS read replicas](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_ReadRepl.html)
- [Amazon RDS Multi-AZ DB clusters](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/multi-az-db-clusters-concepts.html)
- [Amazon Aurora Auto Scaling](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.Integrating.AutoScaling.html)
- [Add an Aurora Auto Scaling policy](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.Integrating.AutoScaling.Add.html)
- [Aurora Serverless v2](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/aurora-serverless-v2.html)
- [How Aurora Serverless v2 works](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/aurora-serverless-v2.how-it-works.html)
- [Aurora Global Database](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/aurora-global-database.html)
- [Aurora reader endpoints](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.Endpoints.Reader.html)
- [Aurora custom endpoints](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.Endpoints.Custom.html)
- [Aurora endpoint connections](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.Overview.Endpoints.html)
- [RDS Proxy for Aurora](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/rds-proxy.html)
- [Enhanced Monitoring](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_Monitoring.OS.html)
- [Performance Insights for Aurora](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/USER_PerfInsights.html)
- [RDS point-in-time restore](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_PIT.html)
- [Aurora KMS key management](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Overview.Encryption.Keys.html)
- [Aurora encryption](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Overview.Encryption.html)
- [Aurora SSL/TLS](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/UsingWithRDS.SSL.html)
- [Aurora IAM database authentication](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/UsingWithRDS.IAMDBAuth.html)
- [Aurora database authentication](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/database-authentication.html)
- [RDS and Secrets Manager](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/rds-secrets-manager.html)

---

## How to add more later

Add new rows to the **Scenario phrase table** using this format:

| Scenario phrase / requirement | Category | Choose | Avoid / trap | Memory rule |
|---|---|---|---|---|
| Your new scenario phrase | Category | Correct feature | Wrong common option | Simple rule |
