# Azure Cost Visibility & Monitoring Platform

## Overview

This project is an Azure-based cost visibility, monitoring, and alerting environment designed to provide centralized visibility into Azure resources while automating notifications for important events.

The project was initially built and validated manually through the Azure Portal. After validating the architecture, the infrastructure is being converted into Azure CLI scripts to make the environment reproducible and easier to deploy.

The project currently focuses on three areas:

- Centralized monitoring with Azure Monitor and Log Analytics
- Automated alerting using Action Groups and Logic Apps
- Least-privilege access using Microsoft Entra ID, Azure RBAC, and managed identities

A visualization layer using Azure Workbooks and KQL is currently being developed.

---

## Architecture

![Azure Architecture](docs/architecture.png)

### Current Data Flow

Azure Resources
        |
        v
Azure Monitor
        |
        v
Log Analytics Workspace
        |
        v
Log Search Alert Rule
        |
        v
Action Group
        |
        v
Logic App
        |
        v
Email Notification

The Log Analytics workspace will also serve as the data source for the project's visibility dashboard.

---

## Azure Services Used

| Service | Purpose |

| Azure Storage | Resources being monitored within the environment |
| Azure Monitor | Collects and monitors resource telemetry |
| Log Analytics | Centralizes log data and enables querying |
| Azure Monitor Alerts | Detects configured conditions within monitoring data |
| Action Groups | Determines what actions occur when an alert fires |
| Azure Logic Apps | Automates notification workflows |
| Microsoft Entra ID | Provides identity management |
| Azure RBAC | Controls access to Azure resources |
| Managed Identities | Provides Azure workloads with an identity without manually managed credentials |
| Azure Workbooks | Planned visualization layer for monitoring and cost data |

---

## Monitoring and Alerting

Azure Monitor and Log Analytics provide centralized monitoring for the environment.

A Log Search Alert Rule monitors collected telemetry and triggers when configured conditions are met.

When an alert fires:

1. Azure Monitor detects the configured condition.
2. The alert triggers an Action Group.
3. The Action Group invokes the Logic App.
4. The Logic App processes the alert.
5. An email notification is sent.

This separates monitoring, alert routing, and automation into individual Azure components.

## Redundant Notification Design

The alerting architecture uses two notification paths to improve notification reliability.

When an alert fires, the Action Group can send an email notification directly while also triggering the Logic App, which provides a secondary automated email path.


```
Storage Accounts
       │
       ▼
 Azure Monitor
       │
       ▼
 Log Analytics
       │
       ▼
 Log Search Alert
       │
       ▼
   Action Group
      /       \
     /         \
    ▼           ▼
Direct Email   Logic App
                   │
                   ▼
             Secondary Email

```

## Identity and Access Management

The environment uses Microsoft Entra ID and Azure RBAC to implement role-based access.

### Administrative Access

The primary administrator manages the Azure resources and IAM configuration for the lab environment.

### Dashboard Viewers

A security group named:

'Cost Lab Viewers'

was created in Microsoft Entra ID.

The group is assigned read-only access at the Log Analytics workspace resource scope.

This allows monitoring access to be granted through group membership rather than assigning permissions directly to individual users.

### IAM Design Principles

The project follows several IAM principles:

- Least privilege
- Group-based access management
- Resource-scoped RBAC assignments
- Separation of human and workload identities
- Managed identities for Azure workloads
- Avoiding unnecessary permissions

---

## Infrastructure Automation

The environment was initially created through the Azure Portal to understand and validate each Azure service before automating deployment.

Azure CLI scripts are being developed to reproduce the environment programmatically.

Planned deployment structure:


```
scripts/
├── deploy.sh
├── configure-monitoring.sh
└── configure-rbac.sh

```

### Planned: Automated Cloud Governance

A scheduled Python-based auditing system will be added to evaluate
Azure resources against defined governance standards.

Planned capabilities include:

- Required resource tag validation
- Identification of non-compliant resources
- Scheduled subscription-wide compliance audits
- Azure SDK/API integration
- Managed Identity authentication
- Least-privilege read-only auditing
- Structured compliance reports
- Alerting for policy violations
- Integration with Azure Policy compliance data

The initial implementation will operate in detection-only mode.
Automated remediation may be introduced later with separately scoped
write permissions.
