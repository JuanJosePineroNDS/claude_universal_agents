---
name: azure-patterns
description: Azure cloud patterns for App Service, AKS, Azure SQL, Cosmos DB, Functions, and RBAC best practices
origin: manual
---

# Azure Patterns

## When to Activate

- Deploying applications to Microsoft Azure
- Designing cloud architecture on Azure
- Configuring Azure services (App Service, AKS, Azure SQL, etc.)
- Reviewing Azure infrastructure for security and cost

## Compute Selection

| Workload | Service | When |
|----------|---------|------|
| Web apps, APIs | **App Service** | Default — managed platform, easy deployment |
| Containers | **Container Apps** | Serverless containers, Dapr integration |
| Kubernetes | **AKS** | Full K8s control, complex orchestration |
| Event-driven | **Azure Functions** | Short tasks, event triggers |
| Persistent VMs | **Virtual Machines** | Custom OS, GPU, legacy apps |
| Batch processing | **Azure Batch** | Large-scale parallel computing |

## Networking

### Virtual Network Design
- Hub-spoke topology for enterprise
- Network Security Groups (NSGs) per subnet
- Azure Firewall or third-party NVA in hub
- Private Endpoints for PaaS services (no public exposure)
- Application Gateway + WAF for web traffic
- Azure Front Door for global load balancing + CDN

## Database

### Azure SQL
- Zone-redundant for production
- Geo-replication for disaster recovery
- Elastic pools for multiple databases with variable load
- Serverless tier for intermittent workloads
- Transparent Data Encryption (enabled by default)
- Microsoft Entra authentication (preferred over SQL auth)

### Cosmos DB
- Choose API: NoSQL (document), PostgreSQL, MongoDB, Cassandra, Table, Gremlin
- Partition key: high cardinality, included in most queries
- Request Units (RU): start with auto-scale, optimize later
- Consistency levels: Session (default) for most apps, Strong for financial
- Change feed for event-driven patterns
- Multi-region writes for global apps

## Storage

### Blob Storage
- Hot/Cool/Archive tiers with lifecycle management
- Immutable blobs for compliance (WORM)
- Soft delete for accidental deletion recovery
- SAS tokens for temporary access (short expiry)
- Customer-managed keys with Key Vault
- AzCopy for large data transfers

## Security (RBAC & Identity)

### Principles
- Microsoft Entra ID (Azure AD) for all authentication
- Managed Identities for services (no stored credentials)
- Key Vault for secrets, keys, and certificates
- RBAC with built-in roles (avoid custom unless necessary)
- Conditional Access policies for human users
- Microsoft Defender for Cloud for threat detection

### Pattern
```bash
# Assign minimum role to managed identity
az role assignment create \
  --assignee <managed-identity-id> \
  --role "Storage Blob Data Reader" \
  --scope /subscriptions/{sub}/resourceGroups/{rg}/providers/Microsoft.Storage/storageAccounts/{sa}
```

## Serverless (Azure Functions)

- Consumption plan: scales to zero, pay per execution
- Premium plan: pre-warmed instances for low latency
- Durable Functions for workflows and orchestration
- Input/output bindings for declarative integration
- Application Insights for monitoring and tracing
- Deployment slots for zero-downtime deployments

## Monitoring & Observability

- Application Insights: APM, distributed tracing, live metrics
- Azure Monitor: metrics, alerts, dashboards
- Log Analytics: KQL queries for log analysis
- Azure Monitor Workbooks: custom visualization
- Smart Detection: automatic anomaly detection
- Action Groups: alerts to email, SMS, webhook, ITSM

## Cost Optimization

- Azure Reservations for steady workloads (1 or 3 year)
- Spot VMs for fault-tolerant processing
- Azure Advisor cost recommendations
- Auto-shutdown for dev/test VMs
- Right-size based on Azure Monitor metrics
- Cost Management + Billing budgets and alerts
