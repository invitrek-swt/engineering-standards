# Project Template Defaults — Invitrek-swt

**Applies to:** All new projects across all domains.

---

## 1. Mandatory Pre-Creation Checks

Before creating any new project:

- Confirm whether the project is **generic/reusable** (belongs in Core/Foundation) or **domain/tool-specific**.
- For planned scope, complete and approve a **location/structure preview** first.
- Confirm target frameworks required by project scope (cross-target compatibility policy).
- Confirm project short-name format: `[Layer].[Provider]` (Provider optional).
- Confirm project category placement under domain:
  - `Shared` and `Tests` are mandatory categories.
  - `Client` / `Server` are optional unless explicitly required.
- Confirm project name is unique within its selected category under the domain.

---

## 2. Common `.csproj` Property Defaults

| Property | Required | Default / Rule |
|---|---|---|
| `Sdk` | Yes | `Microsoft.NET.Sdk` |
| `Project file name` | Yes | `[Layer].[Provider].csproj` (Provider optional) |
| `TargetFrameworks` | Yes (multi-target) | `net48;net10.0` |
| `ImplicitUsings` | Yes | `disable` |
| `Nullable` | Yes | `disable` for cross-target shared models |
| `AssemblyName` | Yes | `Invitrek.<Domain>.<Layer>[.<Provider>]` |
| `RootNamespace` | Yes | `Invitrek.<Domain>.<Area>` |
| `PackageId` | Required for packable libs | Match `AssemblyName` pattern |
| `LangVersion` | Yes | `latest` (while enforcing .NET Framework-compatible usage) |
| `TreatWarningsAsErrors` | Recommended | `true` |
| `Deterministic` | Recommended | `true` |
| `GenerateAssemblyCompanyAttribute` | Yes | `false` |
| `GenerateAssemblyCopyrightAttribute` | Yes | `false` |
| `GenerateAssemblyTrademarkAttribute` | Yes | `false` |
| `GenerateDocumentationFile` (Release) | Yes | `true` |
| `GeneratePackageOnBuild` (Release) | Packable libs | `true` in Release only |

---

## 3. Library Template (Packable)

```xml
<Project Sdk="Microsoft.NET.Sdk">
  <PropertyGroup>
    <TargetFrameworks>net48;net10.0</TargetFrameworks>
    <ImplicitUsings>disable</ImplicitUsings>
    <Nullable>disable</Nullable>
    <AssemblyName>Invitrek.<Domain>.<Library></AssemblyName>
    <RootNamespace>Invitrek.<Domain>.<Area></RootNamespace>
    <PackageId>Invitrek.<Domain>.<Library></PackageId>
    <IsPackable>true</IsPackable>
    <GeneratePackageOnBuild>false</GeneratePackageOnBuild>
    <LangVersion>latest</LangVersion>
    <TreatWarningsAsErrors>true</TreatWarningsAsErrors>
    <GenerateAssemblyCompanyAttribute>false</GenerateAssemblyCompanyAttribute>
    <GenerateAssemblyCopyrightAttribute>false</GenerateAssemblyCopyrightAttribute>
    <GenerateAssemblyTrademarkAttribute>false</GenerateAssemblyTrademarkAttribute>
  </PropertyGroup>

  <PropertyGroup Condition="'$(Configuration)' == 'Release'">
    <GeneratePackageOnBuild>true</GeneratePackageOnBuild>
    <GenerateDocumentationFile>true</GenerateDocumentationFile>
    <DocumentationFile>bin\$(Configuration)\$(TargetFramework)\$(AssemblyName).xml</DocumentationFile>
    <Deterministic>true</Deterministic>
    <ContinuousIntegrationBuild>true</ContinuousIntegrationBuild>
  </PropertyGroup>

  <!-- Shared assembly info (adjust relative path to [Domain]AssemblyInfo.projitems) -->
  <Import Project="..\..\Shared\[Domain]AssemblyInfo\[Domain]AssemblyInfo.projitems" Label="Shared" />

  <!-- Target-specific references -->
  <ItemGroup Condition="'$(TargetFramework)' == 'net48'">
    <!-- net48-only references -->
  </ItemGroup>
  <ItemGroup Condition="'$(TargetFramework)' == 'net10.0'">
    <!-- net10-only references -->
  </ItemGroup>
</Project>
```

---

## 4. Test Project Template

```xml
<Project Sdk="Microsoft.NET.Sdk">
  <PropertyGroup>
    <TargetFrameworks>net48;net10.0</TargetFrameworks>
    <ImplicitUsings>disable</ImplicitUsings>
    <Nullable>disable</Nullable>
    <IsPackable>false</IsPackable>
    <LangVersion>latest</LangVersion>
    <GenerateAssemblyCompanyAttribute>false</GenerateAssemblyCompanyAttribute>
    <GenerateAssemblyCopyrightAttribute>false</GenerateAssemblyCopyrightAttribute>
    <GenerateAssemblyTrademarkAttribute>false</GenerateAssemblyTrademarkAttribute>
  </PropertyGroup>

  <Import Project="..\..\Shared\[Domain]AssemblyInfo\[Domain]AssemblyInfo.projitems" Label="Shared" />

  <ItemGroup>
    <PackageReference Include="xunit" Version="2.*" />
    <PackageReference Include="xunit.runner.visualstudio" Version="2.*" />
    <PackageReference Include="coverlet.collector" Version="*" />
  </ItemGroup>
</Project>
```

---

## 5. SharedAssemblyInfo Template

```csharp
using System.Reflection;

[assembly: AssemblyCompanyAttribute("Invitrek Software Technologies")]
[assembly: AssemblyCopyrightAttribute("© Invitrek Software Technologies. All rights reserved.")]
[assembly: AssemblyTrademarkAttribute("Invitrek")]
[assembly: AssemblyKeyFileAttribute(@"<relative-path-to-snk>")]
```

---

## 6. Reference Grouping Rule

- Group `PackageReference` and `ProjectReference` by target framework when dependencies differ:

```xml
<ItemGroup Condition="'$(TargetFramework)' == 'net48'">
  <PackageReference Include="SomeNet48Package" Version="1.0" />
</ItemGroup>
<ItemGroup Condition="'$(TargetFramework)' == 'net10.0'">
  <PackageReference Include="SomeNet10Package" Version="2.0" />
</ItemGroup>
```
