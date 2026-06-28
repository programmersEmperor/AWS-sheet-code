---
title: "RDS and Aurora Scenario Phrases Quick Review"
description: "SAA-C03 focused two-column reference for Amazon RDS and Amazon Aurora scenario phrases."
---

# RDS and Aurora Scenario Phrases Quick Review

Use this file for quick scanning before practice tests.

Every table has exactly two columns:

| Scenario / requirement | Solution |
|---|---|

---

## Defaults and architecture

| Scenario / requirement | Solution |
|---|---|
| New basic Aurora provisioned cluster is created | **1 writer, 0 readers** unless you explicitly add Aurora Replicas. Storage is replicated automatically. |
| New Aurora Serverless v2 cluster is created | **1 serverless writer, 0 readers** unless you explicitly add readers. Serverless v2 scales ACUs, not reader count. |
| Aurora stores data in a shared cluster volume | Storage is separate from compute. The writer and readers use the same shared Aurora storage volume. |
| Aurora storage layer is replicated across AZs | This is **storage replication**, not readable DB instances. Do not confuse storage copies with read replicas. |
| Aurora data grows over time | Aurora storage grows automatically. This does not mean compute or readers scale automatically. |
| Basic Aurora cluster has no reader and writer fails | Aurora recreates the primary instance. Failover is faster when Aurora Replicas already exist. |
| Need faster Aurora failover | Add Aurora Replicas in different AZs so one can be promoted. |
| Need maximum Aurora read replicas | Aurora supports up to **15 Aurora Replicas** per cluster. |
| Need more writer instances in normal Aurora | Be careful: standard Aurora has **one writer**. Scale/optimize the writer or redesign. |

## High availability and failover

| Scenario / requirement | Solution |
|---|---|
| Database must survive one Availability Zone failure | Use **Multi-AZ** for RDS, or Aurora with replicas across AZs. |
| Need synchronous standby for RDS | Use **RDS Multi-AZ DB instance deployment**. |
| RDS primary instance fails and app needs automatic failover | Use **Multi-AZ failover**. Do not choose snapshot restore. |
| Mission-critical relational DB across AZs | Use **Multi-AZ**. Backups recover later; Multi-AZ keeps service available. |
| Need lower RTO inside one Region | Use **Multi-AZ** or **Aurora Replica failover**. |
| Need HA plus readable standbys for RDS | Use **RDS Multi-AZ DB cluster**, not traditional Multi-AZ DB instance. |
| Need application to keep using one hostname after failover | Use the **RDS/Aurora endpoint**, not hardcoded instance IPs. |
| Need avoid failover connection disruption | Consider **RDS Proxy** plus application retry logic. |

## Read scaling

| Scenario / requirement | Solution |
|---|---|
| Need to offload SELECT queries from primary DB | Use **Read Replica** for RDS, or **Aurora Replica** for Aurora. |
| Reporting queries slow production DB | Query a **read replica**, not the production writer. |
| Analytics team must query production data without affecting app | Use an **RDS Read Replica** or **Aurora Replica**. |
| Application is read-heavy | Add **read replicas/Aurora Replicas**. |
| Aurora reads are increasing | Add **Aurora Replicas** and use the reader endpoint. |
| Read workload increases and replicas should be added/removed automatically | Use **Aurora Auto Scaling**. |
| Aurora read replica cannot keep up during spikes | Use **Aurora Auto Scaling** to add/remove readers between min and max. |
| Need minimum and maximum number of Aurora readers | Configure an **Aurora Auto Scaling policy**. |
| Need distribute reads across existing Aurora readers | Use the **reader endpoint**. It balances only across readers that already exist. |
| Need more reader instances, not just stronger readers | Use **Aurora Auto Scaling**, not Serverless v2 alone. |
| Need improve write throughput | Read replicas do not help writes. Scale/optimize writer, indexes, queries, or redesign. |

## Aurora endpoints

| Scenario / requirement | Solution |
|---|---|
| Need route writes in Aurora | Use the **cluster/writer endpoint**. |
| Need route general read-only traffic | Use the **reader endpoint**. |
| Need connect to one specific DB instance | Use the **instance endpoint**. |
| Need production reads on high-capacity readers and reporting reads on low-capacity readers | Use **Aurora custom endpoints**. |
| Need separate reader groups by workload | Use **Aurora custom endpoints**. |
| Aurora readers have different instance classes | Use **custom endpoints** to control which group receives which workload. |
| Reader endpoint seems enough but workload groups must be separated | Use **custom endpoint**, because reader endpoint balances across all eligible readers. |

## Serverless v2 and capacity scaling

| Scenario / requirement | Solution |
|---|---|
| Workload has sudden unpredictable DB load | Use **Aurora Serverless v2**. |
| Need existing writer capacity to scale automatically | Use **Aurora Serverless v2 ACU scaling**. |
| Need existing reader capacity to scale automatically | Use **Aurora Serverless v2 ACU scaling** for that reader. |
| Need variable relational DB capacity and pay based on capacity used | Use **Aurora Serverless v2**. |
| Need automatic DB compute scaling but not new readers | Use **Aurora Serverless v2**. It scales ACUs of existing instances. |
| Need automatic reader count scaling | Use **Aurora Auto Scaling**, not Serverless v2 alone. |
| Lambda/API Gateway scales but database becomes bottleneck | API compute scaling and DB capacity scaling are different; consider **Aurora Serverless v2** and/or **RDS Proxy**. |

## Disaster recovery and global architecture

| Scenario / requirement | Solution |
|---|---|
| Need disaster recovery from Region failure | Use **Aurora Global Database** or cross-Region read replica depending on RPO/RTO. |
| RPO around 1 second and RTO under 1 minute for relational DB | Use **Aurora Global Database**. |
| Need primary Region and read-only secondary Regions | Use **Aurora Global Database**. |
| Need global users to read with low latency | Use **Aurora Global Database** secondary Regions. |
| Need cross-Region relational DR but low cost and relaxed RTO | Consider **cross-Region read replica** or backup copy. |
| Need writes in secondary Region without failover | Be careful: normal Aurora Global Database writes go to the primary Region. |
| Multi-AZ is suggested for Region outage | Wrong. **Multi-AZ protects against AZ failure, not Region failure**. |

## Backup, restore, and protection

| Scenario / requirement | Solution |
|---|---|
| Need restore DB to a specific time | Use **Point-in-time recovery** with automated backups. |
| Need recover from accidental row deletion | Use **PITR/snapshots**, not Multi-AZ. Multi-AZ can replicate bad deletes. |
| Need long-term backup retention | Use **manual snapshot** or **AWS Backup**. |
| Need central backup policy across accounts/services | Use **AWS Backup**. |
| Need create a test copy of Aurora quickly and cheaply | Use **Aurora cloning**. |
| Need protect DB from accidental deletion | Enable **deletion protection** and keep backups/snapshots. |
| Need high availability | Do not choose backup alone. Use **Multi-AZ/Aurora Replica failover**. |
| Snapshot is suggested for live failover | Wrong. Snapshot is backup/recovery, not high availability. |

## Monitoring, logs, and troubleshooting

| Scenario / requirement | Solution |
|---|---|
| Need CPUUtilization, DatabaseConnections, FreeStorageSpace | Use **CloudWatch metrics**. |
| Need alarms on normal RDS metrics | Use **CloudWatch Alarms**. |
| Need process/thread CPU and memory on DB host | Use **RDS Enhanced Monitoring**. |
| Need OS metrics with high granularity | Use **RDS Enhanced Monitoring**. |
| Need know which SQL queries cause DB load | Use **Performance Insights**. |
| Need DB load by SQL, waits, users, or hosts | Use **Performance Insights**. |
| Need database engine logs for auditing/troubleshooting | Export **RDS/Aurora logs to CloudWatch Logs**. |
| CloudWatch CPU is suggested for DB process details | Wrong. Choose **Enhanced Monitoring** for process/OS-level details. |
| Performance Insights is suggested for OS process metrics | Wrong. Choose **Performance Insights** for SQL/database load analysis. |

## Connections and serverless applications

| Scenario / requirement | Solution |
|---|---|
| Lambda opens too many database connections | Use **RDS Proxy**. |
| Serverless app needs connection pooling | Use **RDS Proxy**. |
| App opens/closes many short-lived DB connections | Use **RDS Proxy**. |
| Need improve failover behavior while preserving app connections | Use **RDS Proxy**. |
| Need reduce connection storms during Lambda burst | Use **RDS Proxy**. |
| API burst handling plus DB connection control | Use **API Gateway/Lambda + RDS Proxy**. |
| API burst handling plus DB capacity scaling | Use **API Gateway/Lambda + Aurora Serverless v2**. |
| API Gateway is suggested for DB connection pooling | Wrong. API Gateway protects APIs; **RDS Proxy** pools DB connections. |

## Encryption and security

| Scenario / requirement | Solution |
|---|---|
| Need encrypt DB storage | Use **RDS/Aurora encryption at rest with KMS**. |
| Need control the encryption key | Use a **customer managed KMS key**. |
| Need HSM-backed key material | Use **KMS custom key store with AWS CloudHSM**. |
| Need encrypt traffic between app and DB | Use **TLS/SSL**. |
| Need force SSL for PostgreSQL | Use the **`rds.force_ssl`** parameter. |
| Private subnet is suggested as encryption | Wrong. Private subnet is isolation; **TLS** is encryption in transit. |
| KMS is suggested to control who can connect to DB | Wrong. KMS encrypts data; auth/network controls connection. |
| Need avoid hardcoded DB passwords | Use **Secrets Manager** or **IAM database authentication**. |
| Need rotate DB password | Use **Secrets Manager rotation**. |
| Need use IAM token instead of DB password | Use **IAM database authentication**. |
| Need control who can create/modify/delete RDS resources | Use **IAM policy**. |
| Need control who can run SQL | Use **database users, roles, and grants**. |
| Need only app tier to reach DB | DB security group allows the **application security group**. |
| DB must not be internet-reachable | Use private subnet, **publicly accessible = no**, and restrictive security group. |
| IAM permission is suggested as SQL permission | Wrong. IAM controls AWS API actions; DB grants control SQL actions. |

## Migration and engine choice

| Scenario / requirement | Solution |
|---|---|
| Need migrate Oracle to managed Oracle with minimal changes | Use **Amazon RDS for Oracle**. |
| Need migrate SQL Server to managed SQL Server with minimal changes | Use **Amazon RDS for SQL Server**. |
| Need MySQL/PostgreSQL-compatible managed DB with higher cloud-native performance | Use **Aurora MySQL/Aurora PostgreSQL**. |
| Need homogeneous database migration | Use **AWS DMS**, usually without Schema Conversion Tool. |
| Need heterogeneous database migration | Use **AWS DMS + AWS Schema Conversion Tool**. |
| Need rehost database server as-is | Use **AWS Application Migration Service** or self-managed DB on EC2. |
| Need managed DB and less operational overhead | Use **RDS/Aurora**, not self-managed DB on EC2. |
| Need OS-level DB server access | Use **self-managed DB on EC2** or **RDS Custom** depending on engine. |
| Need automated patching, backups, and failover managed by AWS | Use **RDS/Aurora**. |

## Cost and operations

| Scenario / requirement | Solution |
|---|---|
| Dev/test DB runs only during working hours | Stop/schedule DB where supported; do not buy reservations first. |
| Steady production database for 1 or 3 years | Consider **Reserved DB Instances** where applicable. |
| Need read scaling only during peaks | Use **Aurora Auto Scaling** instead of always running maximum readers. |
| Need variable DB capacity and avoid overprovisioning | Use **Aurora Serverless v2**. |
| Need lowest operational overhead | Prefer **managed RDS/Aurora features** over EC2 scripts/manual jobs. |
| Need safer major database changes | Use **RDS Blue/Green Deployments**. |

## Integrations and events

| Scenario / requirement | Solution |
|---|---|
| Need notify on RDS failover, backup, or configuration events | Use **RDS event notifications/EventBridge/SNS**. |
| Need react to row-level table change such as deleted vehicle listing | Use a **database trigger/native integration pattern** if supported. |
| Need invoke Lambda from Aurora MySQL logic | Use **Aurora MySQL native Lambda invocation** where supported. |
| RDS event subscription is suggested for row deletion | Wrong. RDS events are service-level events, not table-row events. |

---

## One-line exam rules

- **Multi-AZ = Availability Zone failure protection.**
- **Read Replica / Aurora Replica = read scaling.**
- **Aurora Auto Scaling = automatically add/remove Aurora readers.**
- **Aurora Serverless v2 = scale ACUs of existing DB instances.**
- **Reader endpoint = distribute reads across existing readers.**
- **Custom endpoint = route reads to a selected reader group.**
- **Aurora Global Database = Region-level DR and global reads.**
- **RDS Proxy = database connection pooling.**
- **Enhanced Monitoring = OS/process metrics.**
- **Performance Insights = SQL/database load analysis.**
- **KMS = encryption at rest.**
- **TLS/SSL = encryption in transit.**
- **Security groups = network access.**
- **IAM = AWS API permissions.**
- **Database roles/grants = SQL permissions.**

## Official AWS references

- [Amazon Aurora overview](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.Overview.html)
- [Aurora high availability](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Concepts.AuroraHighAvailability.html)
- [Aurora endpoints](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.Endpoints.html)
- [Aurora Auto Scaling](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.Integrating.AutoScaling.html)
- [Aurora Serverless v2](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/aurora-serverless-v2.html)
- [Aurora Global Database](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/aurora-global-database.html)
- [RDS Multi-AZ](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Concepts.MultiAZ.html)
- [RDS Read Replicas](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_ReadRepl.html)
- [RDS Enhanced Monitoring](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_Monitoring.OS.html)
- [RDS Performance Insights](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_PerfInsights.html)
- [RDS Proxy](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/rds-proxy.html)
- [RDS encryption](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Overview.Encryption.html)
- [IAM database authentication](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/UsingWithRDS.IAMDBAuth.html)
- [RDS backups and PITR](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_WorkingWithAutomatedBackups.html)
