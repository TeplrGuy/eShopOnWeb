# Configuration & Externalized Settings Inventory

Configuration is supplied by .NET JSON settings, environment variables, development user secrets, and optional Azure Key Vault integration. The solution has Development and Docker-specific settings; production secrets are expected to be injected rather than committed as values.

## Configuration Sources

| Source | Type | Path/Location | Notes |
|---|---|---|---|
| Base settings | JSON | `src/Web/appsettings.json`, `src/PublicApi/appsettings.json`, `src/BlazorAdmin/wwwroot/appsettings.json` | Base URLs, logging, and database configuration; credential values omitted |
| Development settings | JSON | `src/{Web,PublicApi,BlazorAdmin}/appsettings.Development.json` | Local HTTPS URLs and development logging |
| Docker settings | JSON | `src/Web/appsettings.Docker.json`, `src/PublicApi/appsettings.Docker.json`, `src/BlazorAdmin/wwwroot/appsettings.Docker.json` | Container-oriented URLs and SQL configuration; sensitive values omitted |
| Launch profiles | JSON | `src/*/Properties/launchSettings.json` | Local URLs and `ASPNETCORE_ENVIRONMENT=Development`; profiles are developer tooling |
| Container composition | YAML | `docker-compose.yml` | Web and PublicApi build services; SQL Server dependency and environment values |
| Environment variables | .NET configuration | `Web/Program.cs`, `PublicApi/Program.cs`, deployment environment | Includes `ASPNETCORE_ENVIRONMENT`, Key Vault endpoint, and Azure SQL setting-name references |
| User Secrets | .NET Secret Manager | Web and PublicApi project `UserSecretsId` settings | Development-only secret store; actual local secret values are not in the repository |
| Azure Key Vault | External secret/configuration store | Optional endpoint set through environment/configuration | Web config uses Azure credentials to add Key Vault as a configuration provider |

## Build Profiles

| Profile | Activation | Purpose | Key Dependencies/Plugins |
|---|---|---|---|
| Debug | `dotnet build` default or `-c Debug` | Development build | Standard SDK compilation |
| Release | `dotnet build -c Release` | Optimized build/publish | Standard SDK compilation |
| Container build | Dockerfile / Compose invocation | Package Web and PublicApi containers | Microsoft container build targets and project Dockerfiles |

No custom MSBuild conditional package profile was identified. Central Package Management is enabled in `Directory.Packages.props`.

## Runtime Profiles

| Profile | Activation Method | Config Files | Key Overrides |
|---|---|---|---|
| Development | `ASPNETCORE_ENVIRONMENT=Development` | `appsettings.json`, `appsettings.Development.json`, user secrets | Local HTTPS endpoints, development logging |
| Docker | Docker profile/environment | Base settings plus `appsettings.Docker.json` when selected | Container URLs and SQL Server connection values |
| Production | `ASPNETCORE_ENVIRONMENT=Production` / deployment configuration | Base settings, environment variables, optional Key Vault | Production URLs, Azure-managed database and secret references |

The standard .NET environment configuration provider loads environment-specific JSON on top of the base file. Azure Key Vault is added by Web when the configured endpoint is available.

## Properties Inventory

| Service | Property Key | Default / Profile Value | Profiles | Source |
|---|---|---|---|---|
| Web, PublicApi, BlazorAdmin | `baseUrls:apiBase` | Local HTTPS URL; Docker HTTP URL | Base, Development, Docker | `appsettings*.json` |
| Web, PublicApi, BlazorAdmin | `baseUrls:webBase` | Local HTTPS URL; Docker host URL | Base, Development, Docker | `appsettings*.json` |
| Web, PublicApi | `ConnectionStrings:CatalogConnection` | Local SQL Server/LocalDB or Docker SQL; credentials masked | Base and Docker | `appsettings*.json`, environment/User Secrets |
| Web, PublicApi | `ConnectionStrings:IdentityConnection` | Local SQL Server/LocalDB or Docker SQL; credentials masked | Base and Docker | `appsettings*.json`, environment/User Secrets |
| Web, PublicApi | `UseOnlyInMemoryDatabase` | `false` unless set | Runtime configuration | Environment/configuration |
| Web | `AZURE_KEY_VAULT_ENDPOINT` | Not set by default | Azure deployment | Environment variable |
| Web | Azure SQL connection setting names | Key names are configurable; values come from Key Vault/configuration | Azure deployment | Key Vault and configuration |
| Web, PublicApi, BlazorAdmin | `Logging:LogLevel:*` | Varies by environment | Base, Development, Docker | `appsettings*.json` |
| All ASP.NET Core hosts | `ASPNETCORE_ENVIRONMENT` | Development in local launch profiles | Local/deployed | Launch profile/environment |

## Startup Parameters & Resource Requirements

| Service | JVM/Runtime Options | Memory | Instance Count |
|---|---|---|---|
| Web | .NET 8 ASP.NET Core host; no custom runtime flags identified | No explicit limit found | Not specified |
| PublicApi | .NET 8 ASP.NET Core host; no custom runtime flags identified | No explicit limit found | Not specified |
| SQL Server | Azure SQL Edge container | No explicit limit found | One Compose service |
| BlazorAdmin | WebAssembly client; hosted/static client output | Browser-managed | Not applicable |

No explicit CPU/memory limits, autoscaling, or runtime heap settings were found in the inspected Compose configuration.

## Startup Dependency Chain

1. Docker Compose starts `sqlserver` before `eshopwebmvc` and `eshoppublicapi` using `depends_on`.
2. No Compose health-check/readiness condition or database wait script was identified; dependency ordering does not prove SQL Server is ready for connections.
3. Web and PublicApi configure their contexts and seed catalog/identity data during startup.
4. When Azure Key Vault is configured, Web obtains credentials from Azure Identity before loading the vault-backed configuration provider.

## Secrets & Sensitive Configuration

| Secret Reference | Type | Storage (masked) |
|---|---|---|
| Catalog and identity SQL connection strings | Database credentials | JSON defaults and/or environment/User Secrets; values omitted |
| Seeded Identity account password | Authentication credential | Hard-coded application constant; value omitted |
| JWT signing key | Cryptographic key | Hard-coded application constant; value omitted |
| ASP.NET Data Protection master key | Cryptographic key | Tracked XML key file contains unencrypted material; value omitted |
| Azure SQL setting names and Key Vault endpoint | Secret-store references | Environment/configuration references; secret values not listed |

### Secrets Provisioning Workflow

Development settings can use .NET User Secrets. Web supports Azure Key Vault through Azure Identity credentials and reads configured setting names for SQL connection strings. Docker settings include database connection configuration and should be treated as sample/development configuration. The repository also contains fixed authentication material and an unencrypted tracked Data Protection key; no automatic key rotation or repository secret provisioning workflow was identified.

## Feature Flags

| Flag Name | Default | Controlled By |
|---|---|---|
| `UseOnlyInMemoryDatabase` | `false` | .NET configuration/environment variable |

No dedicated feature-flag service or framework was identified. The in-memory database switch changes the persistence provider and is an infrastructure option, not a business feature toggle.

## Framework & Runtime Versions

| Component | Version | Source |
|---|---|---|
| Target framework | `net8.0` | `Directory.Packages.props` |
| .NET SDK | `8.0.x` | `global.json` |
| ASP.NET Core packages | `8.0.2` | `Directory.Packages.props` |
| Entity Framework Core | `8.0.2` | `Directory.Packages.props` |
| Azure.Identity | `1.10.4` | `Directory.Packages.props` |
| JWT package | `System.IdentityModel.Tokens.Jwt` 7.3.1 | `Directory.Packages.props` |
| API documentation | Swashbuckle 6.5.0 | `Directory.Packages.props` |
| Container database image | Azure SQL Edge | `docker-compose.yml` |
| Build tooling | .NET SDK/MSBuild | SDK selected by `global.json` |
