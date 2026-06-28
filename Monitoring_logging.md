# AWS Logging and Monitoring Scenario Phrases Quick Review

Use this file for quick SAA-C03 review.

The table format is intentionally simple:

| Scenario / requirement | Solution |
|---|---|

---

## Core Decision Table

| Scenario / requirement | Solution |
|---|---|
| Monitor EC2 CPU utilization | CloudWatch default EC2 metric |
| Monitor EC2 network in/out | CloudWatch default EC2 metric |
| Monitor EC2 disk read/write operations | CloudWatch default EC2 metric |
| Monitor EC2 memory utilization | CloudWatch Agent / custom metric |
| Monitor EC2 disk space used inside the OS | CloudWatch Agent / custom metric |
| Monitor swap usage on EC2 | CloudWatch Agent / custom metric |
| Collect application logs from EC2 | CloudWatch Agent to CloudWatch Logs |
| Collect system logs from EC2 | CloudWatch Agent to CloudWatch Logs |
| Collect logs from on-premises servers | CloudWatch Agent to CloudWatch Logs |
| Centralize application logs from many systems | CloudWatch Logs |
| Search logs for error messages | CloudWatch Logs Insights |
| Run SQL-like queries on CloudWatch log data | CloudWatch Logs Insights |
| Create metric from log pattern | CloudWatch Logs metric filter |
| Alarm when a specific log pattern appears | Metric filter + CloudWatch Alarm |
| Send CloudWatch log events to Lambda for processing | CloudWatch Logs subscription filter |
| Send CloudWatch log events to Kinesis Data Streams | CloudWatch Logs subscription filter |
| Send CloudWatch log events to Firehose | CloudWatch Logs subscription filter |
| Send CloudWatch log events to OpenSearch | CloudWatch Logs subscription filter |
| Store CloudWatch Logs for limited number of days | CloudWatch Logs retention setting |
| Store CloudWatch Logs indefinitely | CloudWatch Logs default retention unless changed |
| Encrypt CloudWatch Logs at rest with customer key | CloudWatch Logs + KMS key |
| Trigger notification when metric crosses threshold | CloudWatch Alarm + SNS |
| Trigger Auto Scaling based on metric | CloudWatch Alarm + Auto Scaling policy |
| Create dashboard for operational metrics | CloudWatch Dashboard |
| Detect unusual CloudWatch metric behavior | CloudWatch Anomaly Detection |
| Monitor Lambda errors, duration, invocations, throttles | CloudWatch Lambda metrics |
| View Lambda function logs | CloudWatch Logs log group for Lambda |
| Trace Lambda request path across services | AWS X-Ray |
| Find which downstream service makes a request slow | AWS X-Ray |
| Trace request path across microservices | AWS X-Ray |
| Visualize service map and latency bottlenecks | AWS X-Ray |
| Monitor ECS/EKS container metrics deeply | CloudWatch Container Insights |
| Monitor EKS pod/node/container metrics | CloudWatch Container Insights |
| Monitor application-level custom business metric | CloudWatch custom metric |
| Audit who called AWS APIs | CloudTrail |
| Find who deleted an S3 bucket | CloudTrail |
| Find who changed a security group | CloudTrail |
| Find source IP of an AWS API call | CloudTrail |
| Track management events across AWS account | CloudTrail |
| Track S3 object-level API activity | CloudTrail data events |
| Track Lambda invoke API activity | CloudTrail data events |
| Keep long-term AWS API audit logs in S3 | CloudTrail trail to S3 |
| Query CloudTrail events with managed event store | CloudTrail Lake |
| Detect unusual API activity patterns | CloudTrail Insights |
| Track resource configuration history | AWS Config |
| See what a resource configuration looked like before | AWS Config |
| Detect non-compliant resource configuration | AWS Config Rules |
| Detect public S3 bucket configuration | AWS Config managed rule |
| Detect whether encryption is enabled on resources | AWS Config rule |
| Capture accepted/rejected IP traffic in VPC | VPC Flow Logs |
| See source IP, destination IP, port, protocol, action | VPC Flow Logs |
| Troubleshoot why traffic is rejected by network path | VPC Flow Logs |
| Send VPC Flow Logs to CloudWatch Logs | VPC Flow Logs destination: CloudWatch Logs |
| Send VPC Flow Logs to S3 for analysis | VPC Flow Logs destination: S3 |
| Analyze network flow logs with Athena | VPC Flow Logs to S3 + Athena |
| Need packet payload inspection | Not VPC Flow Logs; use packet capture / Network Firewall tooling depending on scenario |
| Monitor RDS CPU utilization, connections, free storage | RDS CloudWatch metrics |
| Monitor RDS OS process/thread CPU and memory | RDS Enhanced Monitoring |
| See database process list and OS metrics | RDS Enhanced Monitoring |
| Analyze slow SQL queries and database load | Performance Insights / Database Insights |
| Find top SQL statements causing load | Performance Insights / Database Insights |
| Find DB wait events | Performance Insights / Database Insights |
| Export RDS database logs | RDS log exports to CloudWatch Logs |
| Monitor API Gateway request count, latency, errors | CloudWatch metrics for API Gateway |
| Store API Gateway execution/access logs | CloudWatch Logs |
| Monitor ALB request count, target response time, 5xx | CloudWatch metrics for ALB |
| Store ALB access logs | ALB access logs to S3 |
| Monitor CloudFront requests, error rates, cache stats | CloudWatch metrics for CloudFront |
| Store detailed CloudFront request logs | CloudFront standard logs to S3 or real-time logs to Kinesis Data Streams |
| Store S3 access request logs | S3 server access logging to S3 |
| Audit S3 object API calls | CloudTrail S3 data events |
| Detect suspicious AWS account or network activity | GuardDuty |
| Aggregate security findings from multiple services | Security Hub |
| View AWS compliance reports | AWS Artifact |

---

## CloudWatch Logs Boundaries

| Scenario / requirement | Solution |
|---|---|
| Need raw application log events | CloudWatch Logs |
| Need metrics from log text | CloudWatch Logs metric filter |
| Need real-time streaming of log events to another service | CloudWatch Logs subscription filter |
| Need interactive search and aggregation over logs | CloudWatch Logs Insights |
| Need logs from EC2 file path such as `/var/log/app.log` | CloudWatch Agent |
| Need logs from Lambda | CloudWatch Logs automatically for function logs |
| Need to control how long logs are stored | CloudWatch Logs retention |
| Need to archive logs cheaply for long-term analysis | Send logs to S3 |
| Need to alert on repeated error pattern in logs | Metric filter + CloudWatch Alarm |
| Need to transform logs with Lambda | Subscription filter to Lambda |

---

## CloudWatch Agent Boundaries

| Scenario / requirement | Solution |
|---|---|
| EC2 metric is visible from outside the instance hypervisor | Default CloudWatch metric |
| EC2 metric requires OS-level visibility | CloudWatch Agent |
| Need memory usage | CloudWatch Agent |
| Need disk space percentage | CloudWatch Agent |
| Need log file collection | CloudWatch Agent |
| Need custom process-level metrics from server | CloudWatch Agent or custom metric |
| Need agentless AWS API activity audit | CloudTrail |
| Need resource configuration history | AWS Config |

---

## CloudTrail Boundaries

| Scenario / requirement | Solution |
|---|---|
| Who made the API call | CloudTrail |
| When an AWS API call happened | CloudTrail |
| Which source IP made an API call | CloudTrail |
| Whether console, CLI, SDK, or AWS service made the call | CloudTrail |
| Long-term audit trail | CloudTrail trail to S3 |
| S3 object-level reads/writes audit | CloudTrail data events |
| Lambda invoke audit | CloudTrail data events |
| Query audit events over time | CloudTrail Lake |
| Detect unusual write API activity | CloudTrail Insights |
| Metric crossed threshold | CloudWatch Alarm |
| Resource drifted from desired configuration | AWS Config |

---

## AWS Config Boundaries

| Scenario / requirement | Solution |
|---|---|
| What changed in resource configuration | AWS Config |
| What configuration existed yesterday | AWS Config |
| Whether a resource is compliant | AWS Config Rules |
| Evaluate resource configuration continuously | AWS Config Rules |
| Remediate non-compliant configuration | AWS Config remediation |
| Who made the configuration change | CloudTrail |
| Performance metric changed | CloudWatch |
| Log event contains error | CloudWatch Logs |

---

## VPC Flow Logs Boundaries

| Scenario / requirement | Solution |
|---|---|
| Capture IP traffic metadata in VPC | VPC Flow Logs |
| Know if network traffic was ACCEPT or REJECT | VPC Flow Logs |
| See source and destination IP addresses | VPC Flow Logs |
| See ports and protocol | VPC Flow Logs |
| Troubleshoot security group or NACL connectivity | VPC Flow Logs |
| Send network flow records to CloudWatch Logs | VPC Flow Logs destination: CloudWatch Logs |
| Store network flow records in S3 | VPC Flow Logs destination: S3 |
| Query network flow records with SQL | VPC Flow Logs to S3 + Athena |
| Inspect packet payloads | Not VPC Flow Logs |
| Trace application request latency | AWS X-Ray |

---

## X-Ray Boundaries

| Scenario / requirement | Solution |
|---|---|
| Trace a single request across services | AWS X-Ray |
| See service map | AWS X-Ray |
| Find latency bottleneck in microservices | AWS X-Ray |
| Correlate calls to downstream AWS services | AWS X-Ray |
| Debug distributed application performance | AWS X-Ray |
| Count errors over time | CloudWatch metrics |
| Search application logs | CloudWatch Logs Insights |
| Audit AWS API calls | CloudTrail |

---

## RDS Monitoring Boundaries

| Scenario / requirement | Solution |
|---|---|
| RDS CPUUtilization | CloudWatch metric |
| RDS database connections | CloudWatch metric |
| RDS free storage space | CloudWatch metric |
| RDS read/write IOPS | CloudWatch metric |
| DB host OS process CPU and memory | RDS Enhanced Monitoring |
| DB process or thread resource consumption | RDS Enhanced Monitoring |
| SQL query performance bottleneck | Performance Insights / Database Insights |
| Top SQL statements by load | Performance Insights / Database Insights |
| Database wait events | Performance Insights / Database Insights |
| Database engine logs | RDS log exports to CloudWatch Logs |

---

## Common Exam Traps

| Scenario / requirement | Solution |
|---|---|
| EC2 memory is needed | Use CloudWatch Agent, not default CloudWatch metrics |
| EC2 disk read/write operations are needed | Use default CloudWatch metrics, not custom metric |
| EC2 disk space used is needed | Use CloudWatch Agent, not default CloudWatch metrics |
| RDS process-level CPU and memory are needed | Use RDS Enhanced Monitoring |
| Slow SQL query analysis is needed | Use Performance Insights / Database Insights |
| Need to know who deleted a resource | Use CloudTrail |
| Need to know resource configuration history | Use AWS Config |
| Need network accepted/rejected traffic | Use VPC Flow Logs |
| Need request path across microservices | Use X-Ray |
| Need compliance reports from AWS | Use AWS Artifact |
| Need security threat findings | Use GuardDuty / Security Hub |

---

## One-Line Memory Rules

| Scenario / requirement | Solution |
|---|---|
| Metrics | CloudWatch |
| Logs | CloudWatch Logs |
| OS-level EC2 metrics/log files | CloudWatch Agent |
| Metric threshold reaction | CloudWatch Alarm |
| API audit | CloudTrail |
| Resource configuration history | AWS Config |
| VPC traffic metadata | VPC Flow Logs |
| Distributed tracing | AWS X-Ray |
| RDS process-level OS metrics | Enhanced Monitoring |
| SQL/database load analysis | Performance Insights / Database Insights |
| Security threat detection | GuardDuty |
| Security finding aggregation | Security Hub |
| AWS compliance reports | AWS Artifact |

---

## Log Storage and Retention

| Scenario / requirement | Solution |
|---|---|
| CloudWatch Logs storage location | CloudWatch Logs log groups and log streams |
| CloudWatch Logs default retention | Never expire / stored indefinitely unless retention is configured |
| CloudWatch Logs custom retention | Configure retention per log group |
| CloudWatch Logs retention range | From 1 day up to 10 years, or never expire |
| CloudWatch Logs older than retention setting | Deleted automatically |
| Lambda function logs storage location | CloudWatch Logs log group |
| API Gateway execution/access logs storage location | CloudWatch Logs |
| EC2 application logs storage location | CloudWatch Logs after CloudWatch Agent sends them |
| RDS/Aurora database logs storage location | CloudWatch Logs when log export is enabled |
| Aurora MySQL general/slow/audit/error logs | CloudWatch Logs if publishing is configured |
| Aurora PostgreSQL PostgreSQL log | CloudWatch Logs if log export is enabled |
| CloudTrail Event history default availability | Past 90 days of management events in each Region |
| CloudTrail long-term storage location | S3 bucket through a trail |
| CloudTrail retention in S3 | As long as S3 lifecycle/retention keeps the objects |
| CloudTrail data events storage location | S3 through trail or CloudTrail Lake if configured |
| CloudTrail Lake storage location | CloudTrail event data store |
| VPC Flow Logs storage options | CloudWatch Logs, S3, or Firehose |
| VPC Flow Logs retention in CloudWatch Logs | CloudWatch Logs retention setting |
| VPC Flow Logs retention in S3 | S3 lifecycle/retention policy |
| ALB access logs storage location | S3 bucket that you specify |
| NLB access logs storage location | S3 bucket that you specify |
| CloudFront standard logs storage location | S3 or configured v2 log delivery destination |
| CloudFront real-time logs storage location | Kinesis Data Streams |
| S3 server access logs storage location | Target S3 bucket that you choose |
| S3 server access logs retention | Controlled by S3 lifecycle/retention on the target bucket |
| CloudWatch metrics retention | CloudWatch retains metric data with different granularity over time |
| Short-term troubleshooting logs | CloudWatch Logs with short retention |
| Long-term audit logs | S3 with lifecycle rules, often from CloudTrail or service access logs |
| Need cheaper long-term log archive | S3 lifecycle to Glacier storage classes |
| Need query archived S3 logs with SQL | S3 logs + Athena |

---

## Official AWS References

| Scenario / requirement | Solution |
|---|---|
| CloudWatch Agent metrics and logs | https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/Install-CloudWatch-Agent.html |
| Metrics collected by CloudWatch Agent | https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/metrics-collected-by-CloudWatch-agent.html |
| CloudWatch Logs overview | https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/WhatIsCloudWatchLogs.html |
| CloudWatch Logs metric and subscription filters | https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/FilterAndPatternSyntax.html |
| CloudWatch Logs retention | https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/Working-with-log-groups-and-streams.html |
| CloudTrail API audit logging | https://docs.aws.amazon.com/awscloudtrail/latest/APIReference/Welcome.html |
| VPC Flow Logs | https://docs.aws.amazon.com/vpc/latest/userguide/flow-logs.html |
| AWS X-Ray | https://docs.aws.amazon.com/xray/latest/devguide/aws-xray.html |
| RDS Enhanced Monitoring | https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_Monitoring.OS.html |
| Performance Insights | https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_PerfInsights.html |
| CloudWatch Logs default retention and retention periods | https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/Working-with-log-groups-and-streams.html |
| CloudTrail Event history 90 days | https://docs.aws.amazon.com/awscloudtrail/latest/userguide/view-cloudtrail-events.html |
| CloudTrail S3 bucket policy and log delivery | https://docs.aws.amazon.com/awscloudtrail/latest/userguide/create-s3-bucket-policy-for-cloudtrail.html |
| VPC Flow Logs destinations | https://docs.aws.amazon.com/vpc/latest/userguide/flow-logs.html |
| ALB access logs to S3 | https://docs.aws.amazon.com/elasticloadbalancing/latest/application/enable-access-logging.html |
| S3 server access logging target bucket | https://docs.aws.amazon.com/AmazonS3/latest/userguide/enable-server-access-logging.html |
| RDS log exports to CloudWatch Logs | https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_LogAccess.Procedural.UploadtoCloudWatch.html |
| CloudFront real-time logs to Kinesis Data Streams | https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/real-time-logs.html |
