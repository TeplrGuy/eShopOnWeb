# Assessment Overview

Use this page to navigate the supplementary architecture, service, data, configuration, workflow, security, and upgrade assessment documents included with this report.

## Architecture and Application Facts

| Document | Description |
|---|---|
| [Architecture diagram](architecture-diagram.md) | Application layers, core components, data stores, and their relationships. |
| [Dependency map](dependency-map.md) | Centrally managed application and test dependencies, grouped by purpose. |
| [API and service contracts](api-service-contracts.md) | Service catalog, public API routes, contracts, and communication flow. |
| [Data architecture](data-architecture.md) | EF Core contexts, domain entities, repositories, caching, and data sensitivity. |
| [Configuration inventory](configuration-inventory.md) | Configuration sources, profiles, properties, startup dependencies, and secret references. |
| [Business workflows](business-workflows.md) | Core domain workflows, business rules, and checkout sequence. |

## Additional Assessments

| Document | Description |
|---|---|
| [Security assessment](../security/security-assessment.md) | Merged dependency advisory and CWE findings, with severity and evidence. |
| [.NET upgrade assessment](../scenarios/dotnet-version-upgrade/assessment.md) | Project-by-project upgrade findings for target framework `net10.0`. |
