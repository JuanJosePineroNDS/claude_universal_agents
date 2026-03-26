---
name: gcp-patterns
description: Google Cloud patterns for Cloud Run, GKE, Cloud SQL, Firestore, BigQuery, and IAM best practices
origin: manual
---

# GCP Patterns

## When to Activate

- Deploying applications to Google Cloud Platform
- Designing cloud architecture on GCP
- Configuring GCP services (Cloud Run, GKE, Cloud SQL, etc.)
- Reviewing GCP infrastructure for security and cost

## Compute Selection

| Workload | Service | When |
|----------|---------|------|
| Containers, APIs | **Cloud Run** | Default — serverless containers, scales to zero |
| Kubernetes workloads | **GKE Autopilot** | Need K8s ecosystem, complex orchestration |
| Event-driven, short tasks | **Cloud Functions** | <9min runtime, event triggers |
| Persistent VMs | **Compute Engine** | Custom OS, GPU, legacy apps |
| Batch processing | **Batch** | Large-scale parallel jobs |

## Networking

### VPC Design
- Shared VPC for multi-project organizations
- Private Google Access for services without external IPs
- Cloud NAT for outbound internet from private resources
- Cloud Armor for WAF and DDoS protection
- VPC Service Controls for data exfiltration prevention

### Load Balancing
- Global HTTP(S) LB for multi-region web apps
- Regional LB for single-region, low-latency
- Cloud CDN for static content and API caching
- Managed SSL certificates (auto-provisioned and renewed)

## Database

### Cloud SQL
- High availability with regional instance
- Read replicas for read-heavy workloads
- Automated backups with PITR
- Private IP (no public exposure)
- Cloud SQL Proxy for secure connections
- IAM database authentication

### Firestore
- Document model with subcollections
- Real-time listeners for live updates
- Security rules for direct client access
- Composite indexes for complex queries
- TTL policies for auto-deletion
- Multi-region for global apps

### BigQuery
- Partitioned tables by date/timestamp
- Clustered by frequently filtered columns
- Materialized views for repeated expensive queries
- BI Engine for sub-second dashboard queries
- Slots reservation for predictable costs
- Data Transfer Service for scheduled imports

## Storage

### Cloud Storage
- Uniform bucket-level access (not ACLs)
- Lifecycle rules: Nearline after 30 days, Coldline after 90, Archive after 365
- Signed URLs for temporary access
- Object versioning for critical buckets
- Customer-managed encryption keys (CMEK) for compliance
- Transfer Service for large data migrations

## Security (IAM)

### Principles
- Use service accounts for applications (not user accounts)
- Workload Identity for GKE pods
- Organization policies for guardrails
- VPC Service Controls for sensitive data
- Secret Manager for all secrets (not env vars for sensitive data)
- Binary Authorization for container image verification

### Pattern
```yaml
# Least privilege service account
roles:
  - roles/cloudsql.client      # Connect to Cloud SQL
  - roles/storage.objectViewer # Read from GCS
  # NOT roles/editor or roles/owner
```

## Serverless (Cloud Run)

- Min instances = 0 for cost, min instances = 1 for latency-sensitive
- Concurrency: set per container (default 80, tune based on app)
- CPU always allocated for background processing
- Cloud Run Jobs for batch/scheduled work
- Service-to-service auth with IAM invoker role
- Custom domains with managed SSL

## Monitoring & Observability

- Cloud Logging: structured JSON, log-based metrics, log sinks
- Cloud Monitoring: uptime checks, custom metrics, SLOs
- Cloud Trace: distributed tracing (OpenTelemetry integration)
- Error Reporting: automatic exception grouping and notification
- Alerting policies: error rate, latency, resource utilization

## Cost Optimization

- Committed use discounts for steady workloads
- Preemptible/Spot VMs for fault-tolerant processing
- Cloud Run scales to zero (pay per request)
- BigQuery on-demand pricing for irregular queries
- Recommender Hub for right-sizing suggestions
- Budget alerts at 50%, 80%, 100%
