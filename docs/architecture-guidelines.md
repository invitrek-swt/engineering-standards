# Architecture Guidelines — Invitrek-swt

**Applies to:** All repositories  
**Last updated:** See git history

---

## 1. Solution Structure

### 1.1 Solution format
- Use Visual Studio `.slnx` solution format.
- The `.slnx` file shall live at the **repository root**.
- For large multi-domain solutions, maintain product-scoped solution files for daily work (e.g., `DeveloperStudio.slnx`, `DataAccess.slnx`) alongside the master solution.

### 1.2 Standard folder layout

```
RepoRoot/
├── src/                         ← production source projects
│   └── <Domain>/                ← top-level domain folder
│       ├── <Layer>/             ← e.g., DataAccess, Communication, Services
│       └── Shared/              ← domain assembly info + shared build metadata
├── tests/                       ← test projects (mirrors src/<Domain>/<Layer>)
├── tools/                       ← developer automation tools (C# only)
├── docs/                        ← all documentation
│   └── <Domain>/                ← domain-specific docs
│       └── standards/           ← synced from engineering-standards (read-only)
├── templates/                   ← doc + project templates
├── Shared/                      ← org-level shared build metadata
├── .github/                     ← GitHub config, CI/CD workflows, Copilot instructions
├── .editorconfig                ← synced from engineering-standards
├── global.json                  ← synced from engineering-standards
└── Directory.Build.props        ← synced from engineering-standards
```

### 1.3 Domain organization
- Each domain is a top-level folder under `src/`.
- Domains are independent; cross-domain dependencies go through published NuGet packages (post go-live) or direct project references (pre go-live active development).
- `Shared` and `Tests` groupings are mandatory per domain.
- `Client` and `Server` groupings are optional unless explicitly required.

---

## 2. Layered Architecture Pattern

Standard layer stack (innermost to outermost):

```
Domain/Entities  →  Repository.Contracts  →  Repository
                                          ↓
                 Services.Contracts  →  Services  →  Services.Proxy (client)
                                          ↓
                              ViewModels / API Controllers
                                          ↓
                                   Presentation / UI
```

### 2.1 Layer responsibilities

| Layer | Responsibility |
|-------|---------------|
| `Repository.Entities` | Table-mapped POCO types |
| `Repository.Contracts` | Repository interface contracts |
| `Repository` | Data-access implementation (internal by default) |
| `Data.Entities` | Client-side data models (DTOs / view models) |
| `Services.Contracts` | Service interface contracts (async, DataContract-decorated) |
| `Services` | Service implementations |
| `Services.Proxy` | Client-side service proxy (transport-agnostic) |
| `ViewModels` | MVVM view models |
| `Presentation` | UI layer (WPF, WinForms, Web) |

### 2.2 Dependency rules
- Outer layers depend on inner layers via **interfaces only**.
- Consumers code against `Services.Contracts` only — never against `Services` directly.
- Use DI to swap implementations (in-proc, REST proxy, WCF, gRPC) without changing consumer code.

---

## 3. Naming Conventions

### 3.1 Project naming

| Part | Rule | Example |
|------|------|---------|
| Project file | `[Layer].[Provider].csproj` (Provider optional) | `Data.SqlServer.csproj` |
| Assembly name | `Invitrek.[Domain].[Layer].[Provider]` | `Invitrek.Foundation.Data.SqlServer` |
| Root namespace | `Invitrek.[Domain].[Area]` | `Invitrek.Foundation.Data` |
| NuGet package ID | Match assembly name | `Invitrek.Foundation.Data.SqlServer` |

- Use explicit domain-qualified names to avoid Visual Studio reference conflicts.
- Parent folder names may be short (e.g., `Data/`) while project files use full names (e.g., `Data/Foundation.Data.csproj`).

### 3.2 Namespace conventions

**Server-side:**
- Table-mapped entities: `Invitrek.[Domain].Repository.Entities`
- Repository contracts: `Invitrek.[Domain].Repository.Contracts`
- Service contracts: `Invitrek.[Domain].Services.Contracts`
- Service implementations: `Invitrek.[Domain].Services`

**Client-side:**
- Service proxies: `Invitrek.[Domain].Services.Proxy`
- Client-side data models: `Invitrek.[Domain].Data.Entities`

**Optional common libraries:**
- `Invitrek.[Domain].Data.Common`
- `Invitrek.[Domain].Repository.Common`

### 3.3 Database naming
- Table names: **singular**, **PascalCase** (e.g., `Customer`, `OrderItem`).
- Primary key column: `[TableName]ID` (e.g., `CustomerID`) — uppercase `ID` suffix.
- POCO identity property: `Id` (enforced via `IDbEntity<TKey>`).
- Foreign key columns: follow `[ReferencedTable]ID` convention.
- Abbreviations: capitalize fully (`ID`, `HTTP`); use only when necessary for readability.
- Always alias DB column names to CLR property names in queries/mappings (e.g., `CustomerID AS Id`).

---

## 4. Shared Assembly Metadata

### 4.1 Shared project requirement
- Under `Shared/`, each domain maintains a shared project named `[Domain]AssemblyInfo` containing:
  - Assembly attributes: `Company`, `Trademark`, `Copyright`
  - Assembly signing key path (`AssemblyKeyFileAttribute`)
  - Shared MSBuild props/targets as needed

### 4.2 Project consumption
- All SDK-style `.csproj` files in the domain shall reference `[Domain]AssemblyInfo.projitems`.
- Disable only the three attributes provided by shared assembly info:

```xml
<GenerateAssemblyCompanyAttribute>false</GenerateAssemblyCompanyAttribute>
<GenerateAssemblyCopyrightAttribute>false</GenerateAssemblyCopyrightAttribute>
<GenerateAssemblyTrademarkAttribute>false</GenerateAssemblyTrademarkAttribute>
```

Do not disable other assembly attributes globally.

### 4.3 Shared project (`.shproj`) usage policy — obsolescence vs. justified

Shared projects predate SDK-style multi-targeting. Apply the following discriminator when deciding whether a `.shproj` is still appropriate:

| Scenario | Tool | Rationale |
|---|---|---|
| Same code, multiple TFMs, **single** assembly output | ❌ Shproj obsolete → ✅ SDK-style `<TargetFrameworks>` | Multi-targeting is a first-class csproj feature. Flatten the shproj content into the consuming SDK-style csproj. |
| Same code compiled **into multiple distinct output assemblies** (each assembly needs its own copy baked in) | ✅ Shproj justified | No other mechanism stamps identical content into N separate assemblies. Examples: `[Domain]AssemblyInfo` (see §4.1), per-provider conditional compilation (see §7). |
| Same code shared as a **dependency** (one implementation, many consumers) | ✅ Class library + `ProjectReference` / NuGet | Standard composition. |

**Key discriminator:** does each consumer need the code *compiled into its own assembly* (shproj), or can it *reference* a separate assembly (class library)?

**Mandatory actions on existing shprojs:**
- Any shproj used purely for multi-TFM code sharing into a single assembly output **must be flattened** into the consuming SDK-style csproj; delete the empty `.shproj` + `.projitems` afterwards.
- Shprojs used for stamping per-assembly attributes (`[Domain]AssemblyInfo`) or per-provider conditional compilation **must be retained**.
- New code sharing scenarios default to class libraries; introduce a shproj only when the "multiple distinct output assemblies" criterion applies.

---

## 5. Cross-Target Compatibility

- Shared/cross-target code must compile on both `.NET Framework 4.8` and `.NET 10`.
- Only use language features supported by both targets.
- Use `T?` for nullable **value types** only (`int?`, `bool?`, `DateTime?`, `Guid?`).
- Do not use nullable reference annotations (`string?`) in cross-target shared models.
- Group target-specific `PackageReference`/`ProjectReference` in conditioned `ItemGroup`:

```xml
<ItemGroup Condition="'$(TargetFramework)' == 'net48'">
  <!-- net48-only references -->
</ItemGroup>
<ItemGroup Condition="'$(TargetFramework)' == 'net10.0'">
  <!-- net10-only references -->
</ItemGroup>
```

---

## 6. Service Contract Standards

- Service contracts shall be **async** (`Task` / `Task<T>` return types).
- DTOs used in service contract methods shall be decorated with `[DataContract]` and `[DataMember(Order=N)]` for deterministic serialization and repeatable proxy generation.
- Consumers depend only on `Services.Contracts` — never on implementation assemblies.
- For multi-protocol support (WCF, gRPC, REST, SignalR), use a single endpoint-agnostic service implementation with a consistent middleware-like pipeline for cross-cutting concerns.

---

## 7. Multi-Provider Pattern

When implementing multiple providers or adapters (database providers, service clients, message brokers, caching, logging):

- Use a **shared project** (`*.shproj`) for common implementation code — this is a justified shproj case under §4.3 because each provider produces a **distinct output assembly** (e.g., `Data.SqlServer.dll`, `Data.Oracle.dll`) from the same source with different conditional compilation flags.
- Separate provider-specific references via conditioned `ItemGroup`.
- Use provider conditional compilation flags (`#if SQLSERVER`, etc.) in the shared project.
- Enable XML docs and NuGet packaging only in `Release` builds.

---

## 8. Dependency Injection

- Register services via DI; prefer constructor injection.
- Use `IServiceCollection` extension methods for module registration.
- Mark internal repository/service implementations with `[DependencyServiceAttribute(typeof(IContract))]` for automatic DI registration where applicable.
- Clients code against abstractions only — DI swaps implementations without consumer code changes.

---

## 9. Release and Packaging

- XML documentation file generation: **Release builds only**.
- NuGet package generation: **Release builds only** (`GeneratePackageOnBuild` in Release config).
- Enable `Deterministic` and `ContinuousIntegrationBuild` in Release.
- Post initial go-live, domain components are consumed primarily as published NuGet packages.

---

## 11. Shared Platform Libraries — Foundation and UIFramework

### 11.1 Purpose and scope

**Foundation** and **UIFramework** are the two sub-families of the organization's shared platform libraries. Both live in the same `Foundation/Main` solution but are kept strictly separate so that server-side / non-UI projects can depend on Foundation without pulling in any UI assemblies.

| Sub-family | Folder | Provides | UI dependency |
|------------|--------|----------|---------------|
| **Foundation (Non-UI)** | `src/Foundation/` | Cross-cutting kernel, DI, logging, mapping, validation, CQRS, change tracking, serialization, caching, messaging, CLI framework, CodeModel, repository/data access framework, service model base classes, communication stack, security, tenant model, workflow, AI abstractions | None — pure class libraries targeting `net48` + `net10.0` |
| **UIFramework** | `src/UIFramework/` | Reusable enterprise UI controls (WinUI3-equivalent WPF suite), MVVM base classes, docking framework, code editor, design-surface canvas, task dialogs, localization helpers, Windows Shell integration | WPF / Windows-bound — targets `net48` + `net10.0-windows` |

**UIFramework current state and roadmap:**
- **Today (WPF-first):** Full WPF control suite, MVVM infrastructure, docking, dialogs, theming.
- **Near-term (planned):** Android (MAUI), iOS (MAUI), and ASP.NET Core Blazor/Razor platform layers will be added as separate projects under `src/UIFramework/` following the same shared-project pattern with per-platform conditional compilation.
- Platform abstractions (`IViewModel`, `IDockManager`, dialog service interfaces, navigation contracts) are already defined in platform-agnostic assemblies so MVVM code written today will be portable across future UI platforms without changes.

### 11.2 Dependency rule (mandatory)

- Every domain project (DeveloperStudio, Html5Designer, Nexus, and all future domains) **must** reference Foundation and UIFramework (where applicable) rather than re-implementing cross-cutting or UI concerns.
- Domain projects must **never** fork or duplicate functionality that belongs in Foundation or UIFramework.
- Generic, reusable functionality discovered during domain development must be **promoted to Foundation or UIFramework** rather than staying in the domain project.

### 11.3 Evolution policy (pre-MVP)

- Foundation and UIFramework currently live inside the multi-domain `Foundation/Main` solution.
- New generic functionality is continuously added and evolved in these libraries until the MVP (first public release).
- Active domain projects (DeveloperStudio, Html5Designer, Nexus) consume them via **direct project references** during this phase.
- **Post-MVP:** Foundation and UIFramework will be published as versioned **NuGet packages** (`Invitrek.Foundation.*`, `Invitrek.UIFramework.*`). Domain projects will then consume them as package references. Direct project references must be replaced with NuGet package references at that point.

### 11.4 Contribution guidelines

- Any code added to Foundation or UIFramework must be **domain-agnostic** and broadly reusable.
- All public API surfaces require XML documentation.
- Proprietary algorithms and internal optimizations must be marked `internal` or `private` (see IP Protection policy in `coding-standards.md`).
- Changes to Foundation or UIFramework public contracts must go through the API-First workflow (see `api-guidelines.md`) before implementation.

### 11.5 Foundation library reference

For a complete index of all Foundation packages — including descriptions, public API usage samples, and the cross-cutting dependency map — see:

> **[`docs/foundation-library-index.md`](foundation-library-index.md)**

---

## 12. Go-Live and Evidence Governance

- Use evidence-driven go-live plans with explicit gate criteria.
- Track gate ownership, targets, and evidence artifacts.
- Final Go decision only after all No-Go gates are closed against measurable exit criteria.
- Build/test evidence must include all target frameworks in scope.
- XML doc generation must run automatically via MSBuild for release of any production-ready framework/layer.

---

*For Foundation package details and API usage, see [`foundation-library-index.md`](foundation-library-index.md).*
