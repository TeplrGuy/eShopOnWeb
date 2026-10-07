# Assessment overview

Supplementary documentation for the eShopOnWeb application assessment:

| Document | Description |
|---|---|
| [Architecture diagram](architecture-diagram.md) | Application components, deployment boundaries, and component relationships. |
| [Dependency map](dependency-map.md) | Project references, package dependencies, and dependency management. |
| [API and service contracts](api-service-contracts.md) | HTTP endpoints, authentication, request/response contracts, and service interactions. |
| [Data architecture](data-architecture.md) | Domain entities, persistence contexts, database relationships, and data flows. |
| [Configuration inventory](configuration-inventory.md) | Configuration sources, environment overrides, deployment settings, and sensitive-setting handling. |
| [Business workflows](business-workflows.md) | Catalog browsing, baskets, checkout, order access, and administrator workflows. |

## Assessment reports

- [Application assessment](../report.md)
- [Interactive application assessment](../report.html)
- [Security assessment](../security/security-assessment.md)
- [Machine-readable assessment](../report.json)

## Scope and limitations

The core assessment analyzed ten .NET projects with AppCAT 1.0.1127 in Restricted
privacy mode. AppCAT reported MSBuild discovery errors on Linux before completing
source analysis; build-dependent coverage may be incomplete. The repository's
invalid `global.json` SDK version (`8.0.x`) was ignored during SDK resolution.

Architecture documentation and CWE findings are based on static source review,
not deployed-system inspection or dynamic testing. The dependency scan checked
49 exact declared NuGet versions, excluding transitive-only packages, SDK/runtime
vulnerabilities, and LibMan/CDN assets.

The security merge uses a **high** minimum CVE severity. Two medium-severity
dependency advisories remain available in the raw
[CVE results](../security/cve-assessment-result.json) but are excluded from the
merged findings. Three flagged CWE rules represent two underlying vulnerabilities:
missing order-owner authorization and an embedded JWT signing key. CWE assessment
severity labels are not CVSS impact ratings; consult the security report's evidence
and limitations. No application source code was changed.
