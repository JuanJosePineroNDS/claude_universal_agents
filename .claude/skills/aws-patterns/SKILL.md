---
name: aws-patterns
description: AWS cloud patterns for EC2, Lambda, S3, RDS, DynamoDB, ECS/EKS, CloudFront, and IAM best practices
origin: manual
---

# AWS Patterns

## When to Activate

- Deploying applications to AWS
- Designing cloud architecture on AWS
- Configuring AWS services (EC2, Lambda, S3, RDS, ECS, etc.)
- Reviewing AWS infrastructure for security and cost optimization

## Compute Selection

| Workload | Service | When |
|----------|---------|------|
| Web apps, APIs | **ECS Fargate** | Default for containers, no server management |
| Microservices at scale | **EKS** | Need Kubernetes ecosystem, complex orchestration |
| Event-driven, short tasks | **Lambda** | <15min runtime, intermittent traffic |
| Persistent VMs | **EC2** | Custom OS, GPU, legacy apps |
| Batch processing | **AWS Batch** | Large-scale parallel jobs |

## Networking

### VPC Design
```
VPC (10.0.0.0/16)
├── Public Subnets (10.0.1-3.0/24)  → ALB, NAT Gateway, Bastion
├── Private Subnets (10.0.4-6.0/24) → Application (ECS, EC2)
└── Data Subnets (10.0.7-9.0/24)    → RDS, ElastiCache (no internet)
```
- Minimum 2 AZs for high availability
- NAT Gateway for private subnet internet access
- Security Groups: stateful, per-service
- NACLs: stateless, per-subnet (backup layer)

## Database

### RDS Best Practices
- Multi-AZ for production (automatic failover)
- Read replicas for read-heavy workloads
- Automated backups enabled (35-day retention)
- Parameter groups tuned per engine
- IAM authentication instead of passwords where possible
- Encryption at rest and in transit

### DynamoDB Patterns
- Single-table design for related entities
- GSIs for access patterns that differ from primary key
- On-demand capacity for unpredictable workloads
- Provisioned + auto-scaling for predictable traffic
- DynamoDB Streams for change capture
- TTL for automatic item expiration

## Storage

### S3
- Bucket naming: `{org}-{env}-{purpose}` (globally unique)
- Block public access by default
- Lifecycle rules: move to Glacier after 90 days, delete after 365
- Versioning enabled for critical data
- Server-side encryption (SSE-S3 or SSE-KMS)
- Pre-signed URLs for temporary access

## Security (IAM)

### Principles
- Least privilege: start with zero permissions, add as needed
- Use IAM roles (not access keys) for services
- Enable MFA for all human users
- Use AWS Organizations + SCPs for guardrails
- Rotate credentials regularly
- Never use root account for operations

### Policy Pattern
```json
{
  "Effect": "Allow",
  "Action": ["s3:GetObject", "s3:PutObject"],
  "Resource": "arn:aws:s3:::my-bucket/uploads/*",
  "Condition": {
    "StringEquals": { "aws:RequestedRegion": "us-east-1" }
  }
}
```

## Serverless (Lambda)

- Cold start optimization: keep functions small, use provisioned concurrency for critical paths
- Environment variables for config (never hardcode)
- Dead letter queue for failed invocations
- Set appropriate timeout and memory (more memory = faster CPU)
- Use layers for shared dependencies
- X-Ray for tracing

## Monitoring & Observability

- CloudWatch Logs: structured JSON, log groups per service
- CloudWatch Metrics: custom metrics for business KPIs
- CloudWatch Alarms: error rate, latency P99, queue depth
- X-Ray: distributed tracing across services
- CloudTrail: audit log for all API calls

## Cost Optimization

- Right-size instances based on CloudWatch metrics
- Reserved Instances or Savings Plans for steady workloads
- Spot Instances for fault-tolerant batch processing
- S3 Intelligent-Tiering for unknown access patterns
- Review Cost Explorer monthly
- Set billing alerts at 50%, 80%, 100% of budget
