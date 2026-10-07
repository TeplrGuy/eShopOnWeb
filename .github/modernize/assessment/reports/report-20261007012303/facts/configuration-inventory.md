# Configuration & Externalized Settings Inventory

The runtime settings landscape includes nine appsettings files across Web, PublicApi and BlazorAdmin, three launch-settings files, an API test override, Compose overlays and Azure deployment configuration (`src/Web/appsettings*.json`, `src/PublicApi/appsettings*.json`, `src/BlazorAdmin/wwwroot/appsettings*.json`, `tests/PublicApiIntegrationTests/appsettings.test.json:1-3`). Secrets combine local sample credentials, user-secrets mounts and a Web-only Azure Key Vault configuration branch; all sensitive values below are masked.

## Configuration Sources

| Source | Type | Path/Location | Notes |
|---|---|---|---|
| Web appsettings | Base/environment JSON | `src/Web/appsettings.json:1-20`; `.Development.json:1-13`; `.Docker.json:1-17` | Base connection strings, URLs, logging; environment URL/logging overrides |
| PublicApi appsettings | Base/environment JSON | `src/PublicApi/appsettings.json:1-20`; `.Development.json:1-13`; `.Docker.json:1-17` | Same source pattern, different development Web URL |
| BlazorAdmin settings | Browser-delivered JSON | `src/BlazorAdmin/wwwroot/appsettings.json:1-14`; `.Development.json:1-14`; `.Docker.json:1-6` | Public client settings; must not contain secrets |
| Launch profiles | Development tooling JSON | `src/Web/Properties/launchSettings.json:1-39`; `src/PublicApi/Properties/launchSettings.json:1-47`; `src/BlazorAdmin/Properties/launchSettings.json:1-30` | Project/IIS Express; Web production profile; API WSL/Docker |
| API test settings | Additional JSON provider | `tests/PublicApiIntegrationTests/appsettings.test.json:1-3`; `src/PublicApi/Program.cs:30-31` | In-memory override through AddConfigurationFile |
| Environment variables | Runtime provider | `src/Web/Program.cs:61`; `src/PublicApi/Program.cs:86`; `docker-compose.override.yml:3-20` | Explicit environment providers are added after initial persistence/auth registrations |
| User secrets | Local developer store/mount | `src/Web/Web.csproj:7`; `src/PublicApi/PublicApi.csproj:5`; `docker-compose.override.yml:9-11,18-20` | Secret IDs and mounted stores; contents not inspected |
| Azure Key Vault | External configuration provider | `src/Web/Program.cs:29-42`; `infra/main.bicep:59-63` | Only Web's non-Development/non-Docker branch explicitly loads vault secrets |
| Build settings | MSBuild central versions / SDK | `Directory.Packages.props:1-71`; `global.json:1-6`; `src/*/*.csproj` | Central framework/package properties; project build behavior |
| Browser libraries and bundling | LibMan/bundler JSON | `src/Web/libman.json:1-46`; `src/Web/bundleconfig.json:1-27` | cdnjs libraries and CSS/JS bundling inputs |
| Local deployment | Compose and Dockerfiles | `docker-compose.yml:1-24`; `docker-compose.override.yml:1-20`; `src/Web/Dockerfile:10-27`; `src/PublicApi/Dockerfile:3-25` | Server builds, SQL container, bindings and mounts |
| Azure deployment | azd YAML, Bicep and parameter JSON | `azure.yaml:3-8`; `infra/main.bicep:1-144`; `infra/main.parameters.json:1-21` | Web App Service, two SQL deployments and Key Vault |
| CI | GitHub Actions YAML | `.github/workflows/dotnetcore.yml:1-22`; `.github/workflows/richnav.yml:1-24` | .NET builds/tests and optional code indexing; token reference only, not token content |

The repository does not establish an external configuration Git repository or Azure App Configuration service in these sources. Framework default provider precedence is not a substitute for explicit startup ordering: Web loads Key Vault before resolving SQL, then explicitly adds environment variables; API configures its contexts before its later explicit environment provider (`src/Web/Program.cs:22-63`, `src/PublicApi/Program.cs:26-86`).

## Build Profiles

| Profile | Activation | Purpose | Key Dependencies/Plugins |
|---|---|---|---|
| Debug | Solution configuration / `-c Debug` | Developer build | Declared solution Debug mappings (`eShopOnWeb.sln:41-80`) |
| Release | `-c Release`; CI and Docker publish | Deployment build | Conditional BuildBundlerMinifier package; Docker Release publish (`src/Web/Web.csproj:22`, `src/Web/Dockerfile:18`, `src/PublicApi/Dockerfile:17-20`, `.github/workflows/dotnetcore.yml:18-22`) |
| Central package management | Automatic MSBuild import | Shared target and package version properties | ManagePackageVersionsCentrally true; net8.0 (`Directory.Packages.props:3-8`) |
| WebAssembly client build | BlazorWebAssembly SDK | Browser client output | WebAssembly/DevServer packages (`src/BlazorAdmin/BlazorAdmin.csproj:1-11`) |
| Azure deploy | azd service definition | Deploy Web to App Service | Web only; csharp project and appservice host (`azure.yaml:3-8`) |

No Java/Node build profiles apply to the examined .NET project manifests. Exact MSBuild/C# compiler versions are not pinned separately from SDK selection (`global.json:2-4`).

## Runtime Profiles

| Profile | Activation Method | Config Files | Key Overrides |
|---|---|---|---|
| Development | ASPNETCORE_ENVIRONMENT from project/IIS/WSL launch profiles | Base + Development JSON | Local DB registration; developer middleware; logging overrides (`src/Web/Program.cs:25-28,169-175`, `src/Web/Properties/launchSettings.json:15-27`, `src/PublicApi/Properties/launchSettings.json:15-26`) |
| Docker | Compose environment setting | Base + Docker JSON | SQL container connection strings, HTTP API/Web URLs; Web treats Docker like Development for registrations/middleware (`docker-compose.override.yml:5-6,14-15`, `src/Web/Program.cs:25-28,169-175`) |
| Production / other non-development Web environments | Explicit Web - PROD launch profile or host environment | Base JSON + runtime environment/Key Vault; no Production JSON file among source settings | Key Vault SQL branch; HSTS/error handler (`src/Web/Properties/launchSettings.json:29-36`, `src/Web/Program.cs:29-42,177-182`) |
| PublicApi non-development | Host environment | Base/configured overrides | Still shared Dependencies registration; production Web Key Vault branch does not apply (`src/PublicApi/Program.cs:34,150-153`) |
| API integration test | AddConfigurationFile | appsettings.test.json | In-memory flag true (`src/PublicApi/Program.cs:30-34`, `tests/PublicApiIntegrationTests/appsettings.test.json:1-3`) |

These are single named ASP.NET environments, not composable Spring profiles. Actual environment selection and externally provided values were not inspected.

## Properties Inventory

### Web and PublicApi JSON properties

| Property Key | Default | Profiles | Source |
|---|---|---|---|
| baseUrls:apiBase (string URI) | `https://localhost:5099/api/` | Development same; Docker `http://localhost:5200/api/` | `src/Web/appsettings.json:2-5`, `src/PublicApi/appsettings.json:2-5`, both `appsettings.Docker.json:6-9` |
| baseUrls:webBase (string URI) | Web `https://localhost:44315/`; PublicApi `https://localhost:5001/` | Development same per service; Docker `http://host.docker.internal:5106/` | `src/Web/appsettings.json:2-5`, `src/PublicApi/appsettings.Development.json:2-5`, both `appsettings.Docker.json:6-9` |
| ConnectionStrings:CatalogConnection (string) | `Server=(localdb)\mssqllocaldb;Integrated Security=true;Initial Catalog=Microsoft.eShopOnWeb.CatalogDb;` | Docker server sqlserver:1433, user sa, ******, Trusted_Connection=false, TrustServerCertificate=true | Both hosts `appsettings.json:6-9`, `appsettings.Docker.json:2-5` |
| ConnectionStrings:IdentityConnection (string) | Same LocalDB/integrated settings, database `Microsoft.eShopOnWeb.Identity` | Docker server sqlserver:1433, user sa, ******, Trusted_Connection=false, TrustServerCertificate=true | Both hosts `appsettings.json:6-9`, `appsettings.Docker.json:2-5` |
| CatalogBaseUrl (string) | Empty | Runtime overrides possible | Both hosts `appsettings.json:10`; Web reads it at `src/Web/Program.cs:141-148` |
| Logging:IncludeScopes (boolean) | false | No source environment override | Both hosts `appsettings.json:12` |
| Logging:LogLevel:Default | Warning | Web Development/Docker Debug; API Development/Docker Information | Base `appsettings.json:13-16`; Web environment files `:6-11` / `:10-15`; API environment files `:6-11` / `:10-15` |
| Logging:LogLevel:Microsoft | Warning | Web Development/Docker Information; API Warning | Same logging sections as above |
| Logging:LogLevel:System | Warning | Web Development/Docker Information; inherited API base Warning | Both base files `:13-16`; Web environment files `:7-11` / `:11-15` |
| Logging:LogLevel:Microsoft.Hosting.Lifetime | Not in base | API Development/Docker Information | `src/PublicApi/appsettings.Development.json:10`, `src/PublicApi/appsettings.Docker.json:14` |
| Logging:AllowedHosts | `*` | Base only | Both base files `:18`; **nested under Logging**, not top-level AllowedHosts |
| UseOnlyInMemoryDatabase (boolean) | false in shared registration, absent base JSON | API integration test true; configurable elsewhere using shared registration | `src/Infrastructure/Dependencies.cs:13-17`, `tests/PublicApiIntegrationTests/appsettings.test.json:2` |
| AZURE_KEY_VAULT_ENDPOINT (string URI) | No application default; empty fallback in loader | Web non-development, provisioned vault URI | `src/Web/Program.cs:32`, `infra/main.bicep:62` |
| AZURE_SQL_CATALOG_CONNECTION_STRING_KEY (string secret name) | No application default | Azure: `AZURE-SQL-CATALOG-CONNECTION-STRING` | `src/Web/Program.cs:35`, `infra/main.bicep:60` |
| AZURE_SQL_IDENTITY_CONNECTION_STRING_KEY (string secret name) | No application default | Azure: `AZURE-SQL-IDENTITY-CONNECTION-STRING` | `src/Web/Program.cs:40`, `infra/main.bicep:61` |

The Docker strings also retain Integrated Security=true in the JSON; inventory records actual entries without interpreting or normalizing contradictory connection options (`src/Web/appsettings.Docker.json:3-4`, `src/PublicApi/appsettings.Docker.json:3-4`). Values can be externally overridden; this does not demonstrate correct resolution in a deployed host.

### BlazorAdmin browser configuration

| Property Key | Default | Profiles | Source |
|---|---|---|---|
| baseUrls:apiBase | `https://localhost:5099/api/` | Development same; Docker `http://localhost:5200/api/` | `src/BlazorAdmin/wwwroot/appsettings.json:2-5`, `.Development.json:2-5`, `.Docker.json:2-5` |
| baseUrls:webBase | `https://localhost:44315/` | Development same; Docker `http://host.docker.internal:5106/` | Same sources |
| Logging:IncludeScopes | false | Base/Development; inherited in Docker | `src/BlazorAdmin/wwwroot/appsettings.json:6-12`, `.Development.json:6-12` |
| Logging:LogLevel:Default / Microsoft / System | Information / Warning / Warning | Base/Development; inherited in Docker | Same logging sections |

### Infrastructure parameters and settings

| Property Key | Default | Profiles | Source |
|---|---|---|---|
| environmentName / location / principalId | Environment substitutions AZURE_ENV_NAME / AZURE_LOCATION / AZURE_PRINCIPAL_ID | azd parameter file | `infra/main.parameters.json:5-12`; name min 1/max 64; location min 1 (`infra/main.bicep:3-10`) |
| resourceGroupName / webServiceName / catalogDatabaseServerName / identityDatabaseServerName / appServicePlanName / keyVaultName | Empty string; generated naming fallback | Azure | `infra/main.bicep:16-23,41-55,76-113,120-126` |
| catalogDatabaseName / identityDatabaseName | catalogDatabase / identityDatabase | Azure | `infra/main.bicep:18,20` |
| sqlAdminPassword / appUserPassword | [MASKED]; secretOrRandomPassword references | Azure secure parameters | `infra/main.parameters.json:14-18`, `infra/main.bicep:28-34` |
| SQL appUser / sqlAdmin / connectionStringKey | appUser / sqlAdmin / AZURE-SQL-CONNECTION-STRING; parent sets per-context secret name | Azure SQL module | `infra/core/database/sqlserver/sqlserver.bicep:5-14`, `infra/main.bicep:88,104` |
| SQL minimalTlsVersion / publicNetworkAccess / firewall range | 1.2 / Enabled / 0.0.0.1–255.255.255.254 | Azure SQL module | `infra/core/database/sqlserver/sqlserver.bicep:21-40` |
| App Service kind / runtimeName / runtimeVersion | app,linux / dotnetcore / 8.0 | Azure | `infra/core/host/appservice.bicep:12-20`, `infra/main.bicep:56-57` |
| App Service alwaysOn / clientAffinityEnabled / use32BitWorkerProcess / ftpsState | true / false / false / FtpsOnly | Azure module defaults | `infra/core/host/appservice.bicep:23-35` |
| App Service minTlsVersion / httpsOnly / allowedOrigins | 1.2 / true / portal origins plus allowedOrigins, default empty array | Azure | `infra/core/host/appservice.bicep:23,49,56-61` |
| SCM_DO_BUILD_DURING_DEPLOYMENT / ENABLE_ORYX_BUILD | false / true for Linux default | Azure module generated app settings | `infra/core/host/appservice.bicep:28,33,68-74` |
| applicationInsightsName / APPLICATIONINSIGHTS_CONNECTION_STRING | Empty name; connection setting only conditional | Not supplied by main Web module | `infra/core/host/appservice.bicep:6,73,95-97`, `infra/main.bicep:51-64` |
| Logs file-system level / retention | Verbose; HTTP retention 1 day / 35 MB; detailed errors and failed-request tracing enabled | Azure module | `infra/core/host/appservice.bicep:77-83` |
| Key Vault principalId / secret permissions | Empty principal by default; get/list when principal supplied | Caller principal and Web managed identity | `infra/core/security/keyvault.bicep:5,14-20`, `infra/core/security/keyvault-access.bicep:4-15` |
| DOCKER_REGISTRY | Empty expansion fallback in image names | Compose | `docker-compose.yml:5,12` |
| SQL container ACCEPT_EULA / SA_PASSWORD | Y / [MASKED] | Compose | `docker-compose.yml:22-24` |

Launch tooling properties include commandName, launchBrowser, applicationUrl, inspectUri (Web/Blazor), API launchUrl, publishAllPorts/useSSL (Docker), WSL distributionName, and IIS windowsAuthentication=false/anonymousAuthentication=true (`src/Web/Properties/launchSettings.json:2-36`, `src/PublicApi/Properties/launchSettings.json:3-45`, `src/BlazorAdmin/Properties/launchSettings.json:2-27`). Bindings/environment/resource parameters are separated below.

## Startup Parameters & Resource Requirements

| Service | JVM/Runtime Options | Memory | Instance Count |
|---|---|---|---|
| Web Docker | dotnet Web.dll; ASPNETCORE_ENVIRONMENT=Docker; ASPNETCORE_URLS=http://+:8080; host 5106 | No limit/CPU/heap declared | One Compose service definition; replicas not specified (`src/Web/Dockerfile:27`, `docker-compose.override.yml:3-11`) |
| PublicApi Docker | dotnet PublicApi.dll; same environment and URL listener; host 5200 | No limit/CPU/heap declared | One Compose service definition; replicas not specified (`src/PublicApi/Dockerfile:25`, `docker-compose.override.yml:12-20`) |
| SQL Docker | SQL Edge image; host/container 1433; ACCEPT_EULA=Y | No limit/CPU declared | One service definition (`docker-compose.yml:18-24`) |
| Web App Service | Linux dotnetcore 8.0; appCommandLine empty; alwaysOn true; SKU B1 | SKU named, no numerical memory/CPU requirement in repository | numberOfWorkers/minimumElasticInstanceCount/functionAppScaleLimit default -1 and emitted as null; not a fixed replica claim (`infra/main.bicep:56-57,128-130`, `infra/core/host/appservice.bicep:24-34,50-54`) |
| Development launch | ASPNETCORE_ENVIRONMENT=Development; Web PROD=Production; API WSL sets ASPNETCORE_URLS | No numerical allocation declared | Developer processes, not scaling configuration (`src/Web/Properties/launchSettings.json:15-36`, `src/PublicApi/Properties/launchSettings.json:15-26`) |

No JVM settings apply. Docker HTTPS/user-secret mounts are read-only; API Dockerfile exposes 80/443 while Compose's actual override binds 8080, so EXPOSE is not the effective listener (`docker-compose.override.yml:9-11,18-20`, `src/PublicApi/Dockerfile:5-6`).

## Startup Dependency Chain

1. **SQL container → both hosts:** Compose depends_on gives creation/start order, not a readiness condition; no healthcheck, wait utility or startup timeout is declared in Compose (`docker-compose.yml:9-24`).
2. **Web non-development → Azure credential/Key Vault → contexts:** vault loading occurs before host creation. Credentials chain AzureDeveloperCliCredential then DefaultAzureCredential; no custom timeout/retry here (`src/Web/Program.cs:29-42`).
3. **Hosts → migration/seed → request pipeline:** both hosts await catalog/Identity seed in a scope and log seed failures while continuing (`src/Web/Program.cs:120-139`, `src/PublicApi/Program.cs:129-148`). Catalog seed recursively retries up to ten increments with no backoff and rethrows even after a recursive return; it is not a configured delayed readiness wait (`src/Infrastructure/Data/CatalogContextSeed.cs:12-56`).
4. **Azure provisioning:** Web references plan/vault outputs; managed-identity vault access is provisioned using the Web output; SQL modules take the vault name. The SQL deployment script has PT5M timeout and PT1H retention (`infra/main.bicep:47-105`, `infra/core/database/sqlserver/sqlserver.bicep:45-53`).
5. **Readiness indicators:** Web health checks fetch API/home content and require a seed product text; no explicit HTTP timeout is assigned. App Service healthCheckPath defaults empty and is not overridden by main (`src/Web/HealthChecks/ApiHealthCheck.cs:23-32`, `src/Web/HealthChecks/HomePageHealthCheck.cs:22-33`, `infra/core/host/appservice.bicep:36,55`, `infra/main.bicep:51-64`). Endpoint paths are cataloged in `api-service-contracts.md`.

## Secrets & Sensitive Configuration

| Secret Reference | Type | Storage (masked) |
|---|---|---|
| ConnectionStrings:CatalogConnection / IdentityConnection in Docker JSON | Credential-bearing database strings | `src/Web/appsettings.Docker.json:3-4`, `src/PublicApi/appsettings.Docker.json:3-4`: password [MASKED] |
| SA_PASSWORD | SQL container administrator credential | `docker-compose.yml:23`: [MASKED] |
| AuthorizationConstants.DEFAULT_PASSWORD | Seed-user sample credential | Source constant [MASKED]; consumed in `src/Infrastructure/Identity/AppIdentityDbContextSeed.cs:21-25` |
| AuthorizationConstants.JWT_SECRET_KEY | Symmetric signing key | Source constant [MASKED]; used in `src/PublicApi/Program.cs:54-66`, `src/Infrastructure/Identity/IdentityTokenClaimService.cs:26,41` |
| sqlAdminPassword / appUserPassword | Secure Bicep parameters / vault secrets | Values [MASKED]; references in `infra/main.parameters.json:14-18`, `infra/core/database/sqlserver/sqlserver.bicep:99-120` |
| AZURE-SQL-CATALOG-CONNECTION-STRING / AZURE-SQL-IDENTITY-CONNECTION-STRING | Runtime database secret names | Azure Key Vault, values [MASKED] (`infra/main.bicep:60-62,88,104`) |
| UserSecretsId-backed stores | Local development secrets | External mounted stores, not inspected (`src/Web/Web.csproj:7`, `src/PublicApi/PublicApi.csproj:5`, `docker-compose.override.yml:10-11,19-20`) |
| github.token | CI indexing authentication | GitHub expression reference only, value [MASKED] (`.github/workflows/richnav.yml:20-22`) |

### Secrets Provisioning Workflow

azd parameters request existing/generated passwords through secretOrRandomPassword; secure Bicep parameters reach SQL deployment, user provisioning and vault secret resources (`infra/main.parameters.json:14-18`, `infra/core/database/sqlserver/sqlserver.bicep:11-14,54-79,99-120`). SQL script creates an application user and assigns db_owner (`infra/core/database/sqlserver/sqlserver.bicep:85-94`). Key Vault holds per-database connection strings; Web receives **secret names** and endpoint settings, not inline credentials (`infra/main.bicep:59-63,88,104`).

The Web App Service obtains a system-assigned identity when keyVaultName is provided; the vault access-policy module grants get/list secrets permissions to that identity (`infra/core/host/appservice.bicep:8-9,64`, `infra/main.bicep:67-73`, `infra/core/security/keyvault-access.bicep:4-15`). Application startup uses the Azure credential chain to load vault configuration and dereference selected connection-string keys (`src/Web/Program.cs:31-42`). PublicApi has no corresponding loader in its entry point. Local Docker samples instead embed masked credentials and mount user secrets; source constants remain used for seed passwords/signing. No repository evidence establishes a rotation workflow or encrypted source-secret format.

## Feature Flags

| Flag Name | Default | Controlled By |
|---|---|---|
| UseOnlyInMemoryDatabase | false; test override true | Shared Dependencies reads boolean configuration; used by API and Web Development/Docker branches (`src/Infrastructure/Dependencies.cs:13-25`, `src/PublicApi/Program.cs:34`, `src/Web/Program.cs:25-43`) |
| Development/Docker environment branches | Selected by host environment | Web chooses local registration/developer tools versus vault/HSTS; API developer exception page only Development (`src/Web/Program.cs:25-43,169-182`, `src/PublicApi/Program.cs:150-153`) |
| Image upload disabled | Fixed source behavior, not external flag | API writes placeholder image; no runtime rollout toggle (`src/PublicApi/CatalogItemEndpoints/CreateCatalogItemEndpoint.cs:53-60`) |
| Optional Application Insights / managed identity | Empty Insights name; identity tied to vault name | Infrastructure parameters, not application feature-management framework (`infra/core/host/appservice.bicep:6-9,64,73`) |

No dedicated feature-management/A-B framework appears in the declared packages (`Directory.Packages.props:11-70`). Code constants and deployment options are distinguished from externally managed flags.

## Framework & Runtime Versions

| Component | Version | Source |
|---|---|---|
| Target .NET | net8.0 | `Directory.Packages.props:4` |
| SDK selector | Literal 8.0.x, rollForward latestFeature; exact installed version unverified | `global.json:2-4` |
| ASP.NET packages / EF Core | 8.0.2 / 8.0.2 | `Directory.Packages.props:5,7,25-41` |
| System extension packages / JSON | 8.0.0 / System.Text.Json 8.0.3 | `Directory.Packages.props:6,36,49,51` |
| JWT library | 7.3.1 | `Directory.Packages.props:52` |
| Azure credentials / secrets configuration | 1.10.4 / 1.3.1 | `Directory.Packages.props:17-18` |
| Ardalis specification / MediatR / AutoMapper DI | 7.0.0 / 12.0.1 / 12.0.1 | `Directory.Packages.props:13,15,19,24` |
| Server/runtime Docker images | dotnet/sdk:8.0, dotnet/aspnet:8.0 | `src/Web/Dockerfile:10,20`, `src/PublicApi/Dockerfile:3,8` |
| Compose file schema / SQL container | 3.4 / Azure SQL Edge image with no explicit tag | `docker-compose.yml:1,19` |
| Azure App Service runtime / plan | dotnetcore 8.0 / B1 | `infra/main.bicep:56-57,128-130` |
| Azure SQL server declared platform version / provisioning CLI | 12.0 / Azure CLI 2.37.0, sqlcmd artifact 0.8.1 | `infra/core/database/sqlserver/sqlserver.bicep:21,50,82` |
| CI SDK / actions | 8.0.x / checkout v2, setup-dotnet v1 | `.github/workflows/dotnetcore.yml:11-16` |

Limitations: external environment variables, user secrets, Key Vault contents, deployed resource capacity and actual package/runtime resolution were not inspected. This is configuration evidence, not verification that localhost/host.docker.internal URLs are reachable from every consumer.
