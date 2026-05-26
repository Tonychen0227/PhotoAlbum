# Configuration & Externalized Settings Inventory

Configuration is file-driven with a small number of sources covering connection strings, upload constraints, logging, and environment-specific launch settings. The project does not currently use external config servers or managed secret stores.

## Configuration Sources

| Source | Type | Path/Location | Notes |
|---|---|---|---|
| appsettings.json | Runtime app config | `PhotoAlbum/appsettings.json` | Default connection string, file upload, logging |
| appsettings.Development.json | Runtime environment override | `PhotoAlbum/appsettings.Development.json` | Development logging overrides |
| launchSettings.json | Local launch profile config | `PhotoAlbum/Properties/launchSettings.json` | Local URLs and environment variables |
| User Secrets | Secret source | .NET user secret store | Referenced by `UserSecretsId` in csproj |

## Build Profiles

| Profile | Activation | Purpose | Key Dependencies/Plugins |
|---|---|---|---|
| Debug | `dotnet build` default in local dev | Developer build with symbols | Standard .NET SDK pipeline |
| Release | `dotnet build -c Release` | Optimized production build | Standard .NET SDK pipeline |

## Runtime Profiles

| Profile | Activation Method | Config Files | Key Overrides |
|---|---|---|---|
| Development | `ASPNETCORE_ENVIRONMENT=Development` | `appsettings.json` + `appsettings.Development.json` | Logging verbosity and local launch URLs |
| Non-Development | Host environment value | `appsettings.json` (+ env vars) | Enables exception handler/HSTS path in pipeline |
| Test flag mode | `IsTestEnvironment=true` config value | Config provider chain | Skips startup migrations |

## Properties Inventory

| Property Key | Default | Profiles | Source |
|---|---|---|---|
| `ConnectionStrings:DefaultConnection` | LocalDB connection string | All (overridable) | appsettings.json |
| `FileUpload:MaxFileSizeBytes` | 10485760 | All | appsettings.json |
| `FileUpload:AllowedMimeTypes` | jpeg/png/gif/webp | All | appsettings.json |
| `FileUpload:MaxFilesPerUpload` | 10 | All | appsettings.json |
| `FileUpload:UploadPath` | `wwwroot/uploads` | All | appsettings.json |
| `Logging:LogLevel:Default` | Information | All | appsettings.json |
| `Logging:LogLevel:Microsoft.AspNetCore` | Warning | All | appsettings.json |
| `AllowedHosts` | `*` | All | appsettings.json |
| `ASPNETCORE_ENVIRONMENT` | Development (launch profile) | Local dev profile | launchSettings.json |
| `IsTestEnvironment` | false when absent | Test runs | test config injection |

## Startup Parameters & Resource Requirements

| Service | JVM/Runtime Options | Memory | Instance Count |
|---|---|---|---|
| PhotoAlbum | ASP.NET Core defaults; no explicit custom runtime args committed | Not explicitly configured | 1 (single app instance assumption) |

## Startup Dependency Chain

1. PhotoAlbum app startup → ensures upload directory exists.
2. PhotoAlbum startup → applies EF migrations unless `IsTestEnvironment=true`.
3. App begins serving requests after middleware and Razor routes are mapped.

No explicit multi-service startup dependency chain was detected.

## Secrets & Sensitive Configuration

| Secret Reference | Type | Storage (masked) |
|---|---|---|
| `UserSecretsId` (`28fdd5b1-4b72-4763-98cc-ac5ebb3f280d`) | Local development secret pointer | .NET user-secrets store |
| `ConnectionStrings:DefaultConnection` | Potentially sensitive connection info | appsettings.json (no password present) |

### Secrets Provisioning Workflow

Secrets are expected to be supplied via standard .NET configuration providers (user-secrets locally and environment/config overrides in deployment). The repository does not define managed identity, vault integration, or automated secret provisioning workflows in CI/CD manifests.

## Feature Flags

| Flag Name | Default | Controlled By |
|---|---|---|
| `IsTestEnvironment` | false when not set | Configuration value (tests set true) |

## Framework & Runtime Versions

| Component | Version | Source |
|---|---|---|
| .NET Target Framework | net9.0 | `PhotoAlbum.csproj` |
| ASP.NET Core | 9.0 (SDK aligned) | `Microsoft.NET.Sdk.Web` |
| EF Core SqlServer | 9.0.9 | `PhotoAlbum.csproj` |
| EF Core Design | 9.0.9 | `PhotoAlbum.csproj` |
| ImageSharp | 3.1.11 | `PhotoAlbum.csproj` |
| xUnit | 2.9.2 | `PhotoAlbum.Tests.csproj` |
