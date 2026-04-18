# Project Naming Convention - Short Folders, Long Outputs

## 🎯 Naming Pattern

**Principle**: Keep folder names short, but use fully-qualified names for assemblies and NuGet packages.

```
FOLDER STRUCTURE              ASSEMBLY/PACKAGE NAME                    ROOT NAMESPACE
(Short Names)                 (Fully Qualified)                        (Fully Qualified)
═════════════                 ═════════════════                        ═══════════════

src/Foundation/Data/          Invitrek.Foundation.Data.dll             Invitrek.Foundation.Data
src/Foundation/AI/            Invitrek.Foundation.AI.dll               Invitrek.Foundation.AI
src/Nexus/Server/             Invitrek.Nexus.Server.dll                Invitrek.Nexus.Server
```

---

## 📁 Complete Naming Preview

### Foundation Domain

```
PROJECT FOLDER STRUCTURE              ASSEMBLY NAME                           NUGET PACKAGE                          ROOT NAMESPACE
════════════════════════              ═════════════                           ═════════════                          ══════════════

src/Foundation/
│
├── Core/                             Invitrek.Foundation.Core.dll            Invitrek.Foundation.Core               Invitrek.Foundation.Core
│   └── Core.csproj
│
├── Runtime/                          Invitrek.Foundation.Runtime.dll         Invitrek.Foundation.Runtime            Invitrek.Foundation.Runtime
│   └── Runtime.csproj
│
├── Configuration/                    Invitrek.Foundation.Configuration.dll   Invitrek.Foundation.Configuration      Invitrek.Foundation.Configuration
│   └── Configuration.csproj
│
├── Identity/                         Invitrek.Foundation.Identity.dll        Invitrek.Foundation.Identity           Invitrek.Foundation.Identity
│   └── Identity.csproj
│
├── Data/                             Invitrek.Foundation.Data.dll            Invitrek.Foundation.Data               Invitrek.Foundation.Data
│   └── Data.csproj
│
├── Data.Providers/
│   ├── SqlServer/                    Invitrek.Foundation.Data.SqlServer.dll  Invitrek.Foundation.Data.SqlServer     Invitrek.Foundation.Data
│   │   └── SqlServer.csproj
│   ├── Oracle/                       Invitrek.Foundation.Data.Oracle.dll     Invitrek.Foundation.Data.Oracle        Invitrek.Foundation.Data
│   │   └── Oracle.csproj
│   ├── PostgreSQL/                   Invitrek.Foundation.Data.PostgreSQL.dll Invitrek.Foundation.Data.PostgreSQL    Invitrek.Foundation.Data
│   │   └── PostgreSQL.csproj
│   ├── MySQL/                        Invitrek.Foundation.Data.MySQL.dll      Invitrek.Foundation.Data.MySQL         Invitrek.Foundation.Data
│   │   └── MySQL.csproj
│   ├── SQLite/                       Invitrek.Foundation.Data.SQLite.dll     Invitrek.Foundation.Data.SQLite        Invitrek.Foundation.Data
│   │   └── SQLite.csproj
│   ├── SqlServerCe/                  Invitrek.Foundation.Data.SqlServerCe.dll Invitrek.Foundation.Data.SqlServerCe  Invitrek.Foundation.Data
│   │   └── SqlServerCe.csproj
│   ├── EntityFramework/              Invitrek.Foundation.Data.EntityFramework.dll Invitrek.Foundation.Data.EntityFramework Invitrek.Foundation.Data
│   │   └── EntityFramework.csproj
│   └── Shared/                       Invitrek.Foundation.Data.Shared.dll     Invitrek.Foundation.Data.Shared        Invitrek.Foundation.Data
│       └── Shared.csproj
│
├── AI/                               Invitrek.Foundation.AI.dll              Invitrek.Foundation.AI                 Invitrek.Foundation.AI
│   └── AI.csproj
│
├── AI.Providers/
│   ├── OpenAI/                       Invitrek.Foundation.AI.OpenAI.dll       Invitrek.Foundation.AI.OpenAI          Invitrek.Foundation.AI
│   │   └── OpenAI.csproj
│   ├── LlamaCpp/                     Invitrek.Foundation.AI.LlamaCpp.dll     Invitrek.Foundation.AI.LlamaCpp        Invitrek.Foundation.AI
│   │   └── LlamaCpp.csproj
│   ├── AzureAI/                      Invitrek.Foundation.AI.AzureAI.dll      Invitrek.Foundation.AI.AzureAI         Invitrek.Foundation.AI
│   │   └── AzureAI.csproj
│   └── Ollama/                       Invitrek.Foundation.AI.Ollama.dll       Invitrek.Foundation.AI.Ollama          Invitrek.Foundation.AI
│       └── Ollama.csproj
│
├── Workflow/                         Invitrek.Foundation.Workflow.dll        Invitrek.Foundation.Workflow           Invitrek.Foundation.Workflow
│   └── Workflow.csproj
│
├── Workflow.Providers/
│   ├── WindowsWorkflow/              Invitrek.Foundation.Workflow.WindowsWorkflow.dll Invitrek.Foundation.Workflow.WindowsWorkflow Invitrek.Foundation.Workflow
│   │   └── WindowsWorkflow.csproj
│   ├── WorkflowCore/                 Invitrek.Foundation.Workflow.WorkflowCore.dll Invitrek.Foundation.Workflow.WorkflowCore Invitrek.Foundation.Workflow
│   │   └── WorkflowCore.csproj
│   ├── Elsa/                         Invitrek.Foundation.Workflow.Elsa.dll   Invitrek.Foundation.Workflow.Elsa      Invitrek.Foundation.Workflow
│   │   └── Elsa.csproj
│   └── Shared/                       Invitrek.Foundation.Workflow.Shared.dll Invitrek.Foundation.Workflow.Shared    Invitrek.Foundation.Workflow
│       └── Shared.csproj
│
├── UIAutomation/                     Invitrek.Foundation.UIAutomation.dll    Invitrek.Foundation.UIAutomation       Invitrek.Foundation.UIAutomation
│   └── UIAutomation.csproj
│
├── UIAutomation.Providers/
│   ├── Desktop/                      Invitrek.Foundation.UIAutomation.Desktop.dll Invitrek.Foundation.UIAutomation.Desktop Invitrek.Foundation.UIAutomation
│   │   └── Desktop.csproj
│   ├── Web/                          Invitrek.Foundation.UIAutomation.Web.dll Invitrek.Foundation.UIAutomation.Web  Invitrek.Foundation.UIAutomation
│   │   └── Web.csproj
│   ├── Mobile/                       Invitrek.Foundation.UIAutomation.Mobile.dll Invitrek.Foundation.UIAutomation.Mobile Invitrek.Foundation.UIAutomation
│   │   └── Mobile.csproj
│   └── Shared/                       Invitrek.Foundation.UIAutomation.Shared.dll Invitrek.Foundation.UIAutomation.Shared Invitrek.Foundation.UIAutomation
│       └── Shared.csproj
│
├── ServiceModel/                     Invitrek.Foundation.ServiceModel.dll    Invitrek.Foundation.ServiceModel       Invitrek.Foundation.ServiceModel
│   └── ServiceModel.csproj
│
├── ServiceModel.Providers/
│   ├── AspNetCore/                   Invitrek.Foundation.ServiceModel.AspNetCore.dll Invitrek.Foundation.ServiceModel.AspNetCore Invitrek.Foundation.ServiceModel
│   │   └── AspNetCore.csproj
│   ├── WCF/                          Invitrek.Foundation.ServiceModel.WCF.dll Invitrek.Foundation.ServiceModel.WCF  Invitrek.Foundation.ServiceModel
│   │   └── WCF.csproj
│   └── Shared/                       Invitrek.Foundation.ServiceModel.Shared.dll Invitrek.Foundation.ServiceModel.Shared Invitrek.Foundation.ServiceModel
│       └── Shared.csproj
│
└── Security/                         Invitrek.Foundation.Security.dll        Invitrek.Foundation.Security           Invitrek.Foundation.Security
    └── Security.csproj

    Security.Providers/
    ├── JWT/                          Invitrek.Foundation.Security.JWT.dll    Invitrek.Foundation.Security.JWT       Invitrek.Foundation.Security
    │   └── JWT.csproj
    ├── OAuth/                        Invitrek.Foundation.Security.OAuth.dll  Invitrek.Foundation.Security.OAuth     Invitrek.Foundation.Security
    │   └── OAuth.csproj
    └── Certificate/                  Invitrek.Foundation.Security.Certificate.dll Invitrek.Foundation.Security.Certificate Invitrek.Foundation.Security
        └── Certificate.csproj
```

---

### Nexus Domain

```
PROJECT FOLDER STRUCTURE              ASSEMBLY NAME                           NUGET PACKAGE                          ROOT NAMESPACE
════════════════════════              ═════════════                           ═════════════                          ══════════════

src/Nexus/
│
├── Client/                           Invitrek.Nexus.Client.dll               Invitrek.Nexus.Client                  Invitrek.Nexus.Client
│   └── Client.csproj
│
├── Server/                           Invitrek.Nexus.Server.dll               Invitrek.Nexus.Server                  Invitrek.Nexus.Server
│   └── Server.csproj
│
├── Server.Providers/
│   ├── AspNetCore/                   Invitrek.Nexus.Server.AspNetCore.dll    Invitrek.Nexus.Server.AspNetCore       Invitrek.Nexus.Server
│   │   └── AspNetCore.csproj
│   ├── WCF/                          Invitrek.Nexus.Server.WCF.dll           Invitrek.Nexus.Server.WCF              Invitrek.Nexus.Server
│   │   └── WCF.csproj
│   └── Shared/                       Invitrek.Nexus.Server.Shared.dll        Invitrek.Nexus.Server.Shared           Invitrek.Nexus.Server
│       └── Shared.csproj
│
└── AI/                               Invitrek.Nexus.AI.dll                   Invitrek.Nexus.AI                      Invitrek.Nexus.AI
    └── AI.csproj
```

---

### DeveloperStudio Domain

```
PROJECT FOLDER STRUCTURE              ASSEMBLY NAME                           NUGET PACKAGE                          ROOT NAMESPACE
════════════════════════              ═════════════                           ═════════════                          ══════════════

src/DeveloperStudio/
│
├── VisualDesigner/                   Invitrek.DeveloperStudio.VisualDesigner.dll Invitrek.DeveloperStudio.VisualDesigner Invitrek.DeveloperStudio.VisualDesigner
│   └── VisualDesigner.csproj
│
├── CodeWeaver/                       Invitrek.DeveloperStudio.CodeWeaver.dll Invitrek.DeveloperStudio.CodeWeaver    Invitrek.DeveloperStudio.CodeWeaver
│   └── CodeWeaver.csproj
│
├── Workflow.AI/                      Invitrek.DeveloperStudio.Workflow.AI.dll Invitrek.DeveloperStudio.Workflow.AI  Invitrek.DeveloperStudio.Workflow.AI
│   └── Workflow.AI.csproj
│
└── DevTools/                         Invitrek.DeveloperStudio.DevTools.dll   Invitrek.DeveloperStudio.DevTools      Invitrek.DeveloperStudio.DevTools
    └── DevTools.csproj
```

---

## 🔧 .csproj Configuration Examples

### Example 1: Foundation.Data (Abstraction)

**Folder**: `src/Foundation/Data/`  
**Project File**: `Data.csproj`

```xml
<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <!-- Short project file name -->
    <ProjectName>Data</ProjectName>
    
    <!-- Fully qualified assembly name -->
    <AssemblyName>Invitrek.Foundation.Data</AssemblyName>
    
    <!-- Fully qualified NuGet package name -->
    <PackageId>Invitrek.Foundation.Data</PackageId>
    
    <!-- Root namespace (same as assembly name) -->
    <RootNamespace>Invitrek.Foundation.Data</RootNamespace>
    
    <!-- Multi-targeting -->
    <TargetFrameworks>net48;net10.0</TargetFrameworks>
    
    <!-- Package metadata -->
    <Title>Invitrek Foundation Data Abstraction Layer</Title>
    <Description>Core data access abstractions (IRepository, IDbEntity) for Invitrek Foundation Framework</Description>
    <Product>Invitrek Foundation Framework</Product>
    <Company>Invitrek Technologies</Company>
    <Authors>Invitrek Technologies</Authors>
    <Copyright>Copyright © Invitrek Technologies 2025</Copyright>
    <Version>1.0.0</Version>
    
    <!-- Documentation -->
    <GenerateDocumentationFile>true</GenerateDocumentationFile>
    <DocumentationFile>bin\$(Configuration)\$(TargetFramework)\Invitrek.Foundation.Data.xml</DocumentationFile>
  </PropertyGroup>

</Project>
```

---

### Example 2: Foundation.Data.SqlServer (Provider)

**Folder**: `src/Foundation/Data.Providers/SqlServer/`  
**Project File**: `SqlServer.csproj`

```xml
<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <!-- Short project file name -->
    <ProjectName>SqlServer</ProjectName>
    
    <!-- Fully qualified assembly name -->
    <AssemblyName>Invitrek.Foundation.Data.SqlServer</AssemblyName>
    
    <!-- Fully qualified NuGet package name -->
    <PackageId>Invitrek.Foundation.Data.SqlServer</PackageId>
    
    <!-- Root namespace (abstraction namespace, NOT provider-specific) -->
    <RootNamespace>Invitrek.Foundation.Data</RootNamespace>
    
    <!-- Multi-targeting -->
    <TargetFrameworks>net48;net10.0</TargetFrameworks>
    
    <!-- Package metadata -->
    <Title>Invitrek Foundation Data - SQL Server Provider</Title>
    <Description>SQL Server implementation for Invitrek Foundation Data abstractions</Description>
    <Product>Invitrek Foundation Framework</Product>
    <Company>Invitrek Technologies</Company>
    <Authors>Invitrek Technologies</Authors>
    <Copyright>Copyright © Invitrek Technologies 2025</Copyright>
    <Version>1.0.0</Version>
    
    <!-- Documentation -->
    <GenerateDocumentationFile>true</GenerateDocumentationFile>
    <DocumentationFile>bin\$(Configuration)\$(TargetFramework)\Invitrek.Foundation.Data.SqlServer.xml</DocumentationFile>
  </PropertyGroup>

  <!-- Reference to abstraction -->
  <ItemGroup>
    <ProjectReference Include="..\..\Data\Data.csproj" />
  </ItemGroup>

</Project>
```

---

### Example 3: Foundation.Workflow (Abstraction)

**Folder**: `src/Foundation/Workflow/`  
**Project File**: `Workflow.csproj`

```xml
<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <!-- Short project file name -->
    <ProjectName>Workflow</ProjectName>
    
    <!-- Fully qualified assembly name -->
    <AssemblyName>Invitrek.Foundation.Workflow</AssemblyName>
    
    <!-- Fully qualified NuGet package name -->
    <PackageId>Invitrek.Foundation.Workflow</PackageId>
    
    <!-- Root namespace -->
    <RootNamespace>Invitrek.Foundation.Workflow</RootNamespace>
    
    <!-- Multi-targeting -->
    <TargetFrameworks>net48;net10.0</TargetFrameworks>
    
    <!-- Package metadata -->
    <Title>Invitrek Foundation Workflow Abstraction Layer</Title>
    <Description>Core workflow abstractions (IWorkflowEngine, IWorkflow, IActivity) for Invitrek Foundation Framework</Description>
    <Product>Invitrek Foundation Framework</Product>
    <Company>Invitrek Technologies</Company>
    <Authors>Invitrek Technologies</Authors>
    <Copyright>Copyright © Invitrek Technologies 2025</Copyright>
    <Version>1.0.0</Version>
    
    <!-- Documentation -->
    <GenerateDocumentationFile>true</GenerateDocumentationFile>
    <DocumentationFile>bin\$(Configuration)\$(TargetFramework)\Invitrek.Foundation.Workflow.xml</DocumentationFile>
  </PropertyGroup>

</Project>
```

---

### Example 4: Foundation.Workflow.WindowsWorkflow (Provider)

**Folder**: `src/Foundation/Workflow.Providers/WindowsWorkflow/`  
**Project File**: `WindowsWorkflow.csproj`

```xml
<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <!-- Short project file name -->
    <ProjectName>WindowsWorkflow</ProjectName>
    
    <!-- Fully qualified assembly name -->
    <AssemblyName>Invitrek.Foundation.Workflow.WindowsWorkflow</AssemblyName>
    
    <!-- Fully qualified NuGet package name -->
    <PackageId>Invitrek.Foundation.Workflow.WindowsWorkflow</PackageId>
    
    <!-- Root namespace (abstraction namespace) -->
    <RootNamespace>Invitrek.Foundation.Workflow</RootNamespace>
    
    <!-- Multi-targeting -->
    <TargetFrameworks>net48;net10.0</TargetFrameworks>
    
    <!-- Package metadata -->
    <Title>Invitrek Foundation Workflow - Windows Workflow Foundation Provider</Title>
    <Description>Windows Workflow Foundation (WF 4.x) implementation for Invitrek Foundation Workflow abstractions</Description>
    <Product>Invitrek Foundation Framework</Product>
    <Company>Invitrek Technologies</Company>
    <Authors>Invitrek Technologies</Authors>
    <Copyright>Copyright © Invitrek Technologies 2025</Copyright>
    <Version>1.0.0</Version>
    
    <!-- Documentation -->
    <GenerateDocumentationFile>true</GenerateDocumentationFile>
    <DocumentationFile>bin\$(Configuration)\$(TargetFramework)\Invitrek.Foundation.Workflow.WindowsWorkflow.xml</DocumentationFile>
  </PropertyGroup>

  <!-- Reference to abstraction -->
  <ItemGroup>
    <ProjectReference Include="..\..\Workflow\Workflow.csproj" />
  </ItemGroup>

  <!-- Windows Workflow Foundation references (net48 only) -->
  <ItemGroup Condition="'$(TargetFramework)' == 'net48'">
    <Reference Include="System.Activities" />
    <Reference Include="System.Activities.Core.Presentation" />
    <Reference Include="System.Activities.Presentation" />
  </ItemGroup>

</Project>
```

---

### Example 5: Nexus.Server (Abstraction)

**Folder**: `src/Nexus/Server/`  
**Project File**: `Server.csproj`

```xml
<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <!-- Short project file name -->
    <ProjectName>Server</ProjectName>
    
    <!-- Fully qualified assembly name -->
    <AssemblyName>Invitrek.Nexus.Server</AssemblyName>
    
    <!-- Fully qualified NuGet package name -->
    <PackageId>Invitrek.Nexus.Server</PackageId>
    
    <!-- Root namespace -->
    <RootNamespace>Invitrek.Nexus.Server</RootNamespace>
    
    <!-- Multi-targeting -->
    <TargetFrameworks>net48;net10.0</TargetFrameworks>
    
    <!-- Package metadata -->
    <Title>Invitrek Nexus Platform - Server Abstractions</Title>
    <Description>Core server abstractions (IServiceHost, ITenantManager) for Invitrek Nexus Platform</Description>
    <Product>Invitrek Nexus Platform</Product>
    <Company>Invitrek Technologies</Company>
    <Authors>Invitrek Technologies</Authors>
    <Copyright>Copyright © Invitrek Technologies 2025</Copyright>
    <Version>1.0.0</Version>
    
    <!-- Documentation -->
    <GenerateDocumentationFile>true</GenerateDocumentationFile>
    <DocumentationFile>bin\$(Configuration)\$(TargetFramework)\Invitrek.Nexus.Server.xml</DocumentationFile>
  </PropertyGroup>

  <!-- Dependencies -->
  <ItemGroup>
    <ProjectReference Include="..\..\Foundation\Data\Data.csproj" />
    <ProjectReference Include="..\..\Foundation\ServiceModel\ServiceModel.csproj" />
  </ItemGroup>

</Project>
```

---

### Example 6: DeveloperStudio.Workflow.AI

**Folder**: `src/DeveloperStudio/Workflow.AI/`  
**Project File**: `Workflow.AI.csproj`

```xml
<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <!-- Short project file name -->
    <ProjectName>Workflow.AI</ProjectName>
    
    <!-- Fully qualified assembly name -->
    <AssemblyName>Invitrek.DeveloperStudio.Workflow.AI</AssemblyName>
    
    <!-- Fully qualified NuGet package name -->
    <PackageId>Invitrek.DeveloperStudio.Workflow.AI</PackageId>
    
    <!-- Root namespace -->
    <RootNamespace>Invitrek.DeveloperStudio.Workflow.AI</RootNamespace>
    
    <!-- Multi-targeting -->
    <TargetFrameworks>net48;net10.0</TargetFrameworks>
    
    <!-- Package metadata -->
    <Title>Invitrek Developer Studio - Workflow AI</Title>
    <Description>AI-powered workflow optimization and automation for Invitrek Developer Studio</Description>
    <Product>Invitrek Developer Studio</Product>
    <Company>Invitrek Technologies</Company>
    <Authors>Invitrek Technologies</Authors>
    <Copyright>Copyright © Invitrek Technologies 2025</Copyright>
    <Version>1.0.0</Version>
    
    <!-- Documentation -->
    <GenerateDocumentationFile>true</GenerateDocumentationFile>
    <DocumentationFile>bin\$(Configuration)\$(TargetFramework)\Invitrek.DeveloperStudio.Workflow.AI.xml</DocumentationFile>
  </PropertyGroup>

  <!-- Dependencies -->
  <ItemGroup>
    <ProjectReference Include="..\..\Foundation\Workflow\Workflow.csproj" />
    <ProjectReference Include="..\..\Foundation\AI\AI.csproj" />
  </ItemGroup>

</Project>
```

---

## 🎯 Naming Rules Summary

### Rule 1: Folder Names (SHORT)

```
✅ CORRECT (Short folder names):
src/Foundation/Data/
src/Foundation/Data.Providers/SqlServer/
src/Nexus/Server/
src/DeveloperStudio/Workflow.AI/

❌ INCORRECT (Long folder names):
src/Foundation/Invitrek.Foundation.Data/
src/Foundation/Invitrek.Foundation.Data.Providers/Invitrek.Foundation.Data.SqlServer/
```

### Rule 2: Assembly Names (FULLY QUALIFIED)

```
✅ CORRECT (Fully qualified):
Invitrek.Foundation.Data.dll
Invitrek.Foundation.Data.SqlServer.dll
Invitrek.Nexus.Server.dll
Invitrek.DeveloperStudio.Workflow.AI.dll

❌ INCORRECT (Short names):
Data.dll
SqlServer.dll
Server.dll
Workflow.AI.dll
```

### Rule 3: NuGet Package Names (FULLY QUALIFIED)

```
✅ CORRECT (Fully qualified):
Invitrek.Foundation.Data
Invitrek.Foundation.Data.SqlServer
Invitrek.Nexus.Server
Invitrek.DeveloperStudio.Workflow.AI

❌ INCORRECT (Short names):
Data
SqlServer
Server
Workflow.AI
```

### Rule 4: Root Namespace (ABSTRACTION NAMESPACE)

```
✅ CORRECT (Abstraction namespace):
Abstraction: Invitrek.Foundation.Data     → RootNamespace: Invitrek.Foundation.Data
Provider:    Invitrek.Foundation.Data     → RootNamespace: Invitrek.Foundation.Data (SAME!)

✅ CORRECT (Non-provider):
Abstraction: Invitrek.Nexus.Server        → RootNamespace: Invitrek.Nexus.Server

❌ INCORRECT (Provider-specific namespace):
Provider:    Invitrek.Foundation.Data     → RootNamespace: Invitrek.Foundation.Data.SqlServer (WRONG!)
```

---

## 📊 Full Naming Pattern

```
Component Type          Folder Name            Assembly Name                          Package Name                           Root Namespace
═══════════════         ═══════════            ═════════════                          ════════════                           ══════════════

ABSTRACTION             Data                   Invitrek.Foundation.Data               Invitrek.Foundation.Data               Invitrek.Foundation.Data
PROVIDER                SqlServer              Invitrek.Foundation.Data.SqlServer     Invitrek.Foundation.Data.SqlServer     Invitrek.Foundation.Data
PROVIDER                Oracle                 Invitrek.Foundation.Data.Oracle        Invitrek.Foundation.Data.Oracle        Invitrek.Foundation.Data
PROVIDER SHARED         Shared                 Invitrek.Foundation.Data.Shared        Invitrek.Foundation.Data.Shared        Invitrek.Foundation.Data

ABSTRACTION             AI                     Invitrek.Foundation.AI                 Invitrek.Foundation.AI                 Invitrek.Foundation.AI
PROVIDER                OpenAI                 Invitrek.Foundation.AI.OpenAI          Invitrek.Foundation.AI.OpenAI          Invitrek.Foundation.AI
PROVIDER                LlamaCpp               Invitrek.Foundation.AI.LlamaCpp        Invitrek.Foundation.AI.LlamaCpp        Invitrek.Foundation.AI

ABSTRACTION             Workflow               Invitrek.Foundation.Workflow           Invitrek.Foundation.Workflow           Invitrek.Foundation.Workflow
PROVIDER                WindowsWorkflow        Invitrek.Foundation.Workflow.WindowsWorkflow Invitrek.Foundation.Workflow.WindowsWorkflow Invitrek.Foundation.Workflow
PROVIDER                WorkflowCore           Invitrek.Foundation.Workflow.WorkflowCore Invitrek.Foundation.Workflow.WorkflowCore Invitrek.Foundation.Workflow
PROVIDER                Elsa                   Invitrek.Foundation.Workflow.Elsa      Invitrek.Foundation.Workflow.Elsa      Invitrek.Foundation.Workflow

ABSTRACTION             Server                 Invitrek.Nexus.Server                  Invitrek.Nexus.Server                  Invitrek.Nexus.Server
PROVIDER                AspNetCore             Invitrek.Nexus.Server.AspNetCore       Invitrek.Nexus.Server.AspNetCore       Invitrek.Nexus.Server
PROVIDER                WCF                    Invitrek.Nexus.Server.WCF              Invitrek.Nexus.Server.WCF              Invitrek.Nexus.Server

COMPONENT               Workflow.AI            Invitrek.DeveloperStudio.Workflow.AI   Invitrek.DeveloperStudio.Workflow.AI   Invitrek.DeveloperStudio.Workflow.AI
```

---

## 🔑 Key Insights

### Benefits of Short Folder Names + Long Assembly Names

1. ✅ **Readable file paths** - `src/Foundation/Data/IRepository.cs` (not `src/Foundation/Invitrek.Foundation.Data/IRepository.cs`)
2. ✅ **Consistent outputs** - `Invitrek.Foundation.Data.dll` (fully qualified)
3. ✅ **Clear dependencies** - `Install-Package Invitrek.Foundation.Data.SqlServer` (obvious what it is)
4. ✅ **Namespace consistency** - All providers share abstraction namespace (e.g., `Invitrek.Foundation.Data`)
5. ✅ **Easy refactoring** - Move `src/Data/` to `src/Foundation/Data/` without changing assembly name

### Root Namespace Strategy (CRITICAL!)

**Providers use ABSTRACTION namespace as root:**

```csharp
// src/Foundation/Data.Providers/SqlServer/SqlServerRepository.cs
namespace Invitrek.Foundation.Data  // ← Abstraction namespace, NOT Invitrek.Foundation.Data.SqlServer
{
    internal class SqlServerRepository<TEntity, TKey> : IRepository<TEntity, TKey>
    {
        // Implementation
    }
}
```

**Why?**
- ✅ Consumers don't need to change `using` directives when switching providers
- ✅ Shared base classes in abstraction can be easily inherited
- ✅ Internal classes stay in same namespace as public interfaces

---

## ✅ Summary

**Short folder names keep paths clean.**  
**Fully qualified assembly/package names ensure clarity.**  
**Consistent root namespaces enable easy provider switching.**

**This pattern is used across ALL products (Foundation, Nexus, DeveloperStudio)!** 🎯✨
