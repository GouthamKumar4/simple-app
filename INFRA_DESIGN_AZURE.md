# Infrastructure Design (Azure)

## 1. Objective
Define a cloud infrastructure design for deploying `simple-app` on Microsoft Azure with production-ready security, scalability, and observability.

## 2. High-Level Architecture
- **Client Access**: Users connect over HTTPS.
- **Edge**: Azure Front Door (WAF enabled) handles global routing and TLS.
- **Application Tier**: Azure App Service (or Azure Container Apps if containerized).
- **Data Tier**: Azure Database for PostgreSQL (Flexible Server).
- **Cache**: Azure Cache for Redis.
- **Storage**: Azure Blob Storage for static assets/uploads.
- **Secrets & Config**: Azure Key Vault + App Configuration.
- **Observability**: Azure Monitor + Application Insights + Log Analytics.
- **Network Security**: Virtual Network with private endpoints for data services.

## 3. Resource Groups and Environments
Use separate subscriptions or at minimum separate resource groups per environment:
- `rg-simpleapp-dev`
- `rg-simpleapp-staging`
- `rg-simpleapp-prod`

Naming convention:
`<service>-<app>-<env>-<region>`
Example: `app-simpleapp-prod-eastus`

## 4. Networking Design
- Create a VNet per environment (e.g., `10.10.0.0/16` for prod).
- Subnets:
  - `snet-app`
  - `snet-data`
  - `snet-private-endpoints`
- NSGs restrict traffic between subnets.
- Private Endpoints for PostgreSQL, Redis, and Storage.
- Disable public network access for data services where possible.

## 5. Compute and Deployment
Preferred options:
1. **Azure App Service** (simpler ops)
2. **Azure Container Apps** (if container-first)

Deployment flow:
- CI builds app artifact/container image.
- Push image to Azure Container Registry (ACR) if containerized.
- CD deploys to target environment using workload identity/federated credentials.

## 6. Data and State
- PostgreSQL Flexible Server with:
  - Zone-redundant HA (prod)
  - Automated backups and PITR
  - Read replicas (if needed for read scale)
- Redis for session/cache with persistence based on SLA needs.
- Blob Storage lifecycle rules for archival/retention.

## 7. Security Baseline
- Enforce HTTPS everywhere.
- Use managed identities for app-to-service auth.
- Store credentials/keys only in Key Vault.
- Enable Defender for Cloud recommendations.
- Enable WAF managed rule sets in Front Door.
- RBAC with least privilege roles and PIM for admin access.

## 8. Reliability and DR
- Deploy production to at least two availability zones (where supported).
- Use Front Door health probes and failover routing.
- Document RTO/RPO targets:
  - Target RTO: 2 hours
  - Target RPO: 15 minutes
- Test backup restore quarterly.

## 9. Observability and Operations
- Application Insights SDK in app.
- Centralized logs in Log Analytics workspace.
- Alerts:
  - HTTP 5xx rate
  - P95 latency
  - CPU/memory thresholds
  - Database connection saturation
- Dashboards per environment and service.

## 10. IaC and Governance
- Implement with Terraform or Bicep modules.
- Policy enforcement:
  - Required tags: `owner`, `env`, `costCenter`, `dataClass`
  - Deny public IPs on data resources
  - Restrict allowed Azure regions
- Budget alerts per subscription/resource group.

## 11. Migration Notes (to Azure)
- Externalize existing config to App Configuration + Key Vault.
- Replace local file persistence with Blob Storage.
- Migrate relational data to PostgreSQL Flexible Server.
- Add health/readiness endpoints for platform probes.

## 12. Open Decisions
- Final compute choice: App Service vs Container Apps.
- Region strategy: single-region + DR or active-active multi-region.
- Database engine confirmation (PostgreSQL assumed).
