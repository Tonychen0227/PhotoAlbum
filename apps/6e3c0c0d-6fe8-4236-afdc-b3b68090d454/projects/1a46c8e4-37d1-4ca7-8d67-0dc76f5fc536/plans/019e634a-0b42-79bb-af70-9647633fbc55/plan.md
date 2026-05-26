# Modernization Plan: PhotoAlbum Azure Modernization

**Project**: PhotoAlbum

---

## Technical Framework

- **Language**: C# / .NET 9.0 (EOL — upgrading to .NET 10.0 LTS)
- **Framework**: ASP.NET Core 9.0 Razor Pages
- **Build Tool**: dotnet CLI / MSBuild (SDK-style project)
- **Database**: SQL Server LocalDB (migrating to Azure SQL Database)
- **Key Dependencies**: Entity Framework Core 9.0, SixLabors.ImageSharp 3.1.11

---

## Overview

> This migration modernizes the PhotoAlbum ASP.NET Core Razor Pages application for Azure cloud deployment. The application currently stores uploaded photos on the local filesystem (`wwwroot/uploads`) and uses SQL Server LocalDB for metadata. The new architecture will:
>
> - Upgrade the .NET runtime from 9.0 (EOL) to .NET 10 LTS to ensure ongoing support and security updates
> - Migrate photo file storage from the local disk to Azure Blob Storage for scalable, durable, and cloud-native storage
> - Migrate the database from SQL Server LocalDB to Azure SQL Database with Managed Identity authentication for secure, serverless connectivity
> - Remediate known CVEs in project dependencies to ensure the application is secure before deployment
>
> The migration follows a phased approach: runtime upgrade first, then service migrations, and finally security hardening.

---

## Migration Impact Summary

| Application  | Original Service          | New Azure Service         | Authentication     | Comments                          |
|--------------|---------------------------|---------------------------|--------------------|-----------------------------------|
| PhotoAlbum   | Local filesystem uploads  | Azure Blob Storage        | Managed Identity   | Migrate photo file upload/delete  |
| PhotoAlbum   | SQL Server LocalDB        | Azure SQL Database        | Managed Identity   | EF Core connection migration      |

---

## Open Questions & Questionnaire

- [x] Q: Should the plan include environment/infrastructure provisioning? → A: No — focus on code migration only (user did not request provisioning)
- [x] Q: Should the plan include integration testing? → A: No — not requested by user
- [x] Q: Should the plan include security/CVE remediation? → A: Yes — default, scan and fix known vulnerabilities
- [x] Q: Which Azure deployment target should the plan use? → A: No deployment — migration only (user did not request deployment)
