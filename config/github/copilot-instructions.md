# Engineering Standards (Organization-wide) — Invitrek-swt

This document defines mandatory organization-wide standards for all repositories (org and personal) that follow Invitrek-swt engineering practices.

---

## 1. Default Technology Stack

### 1.1 Primary technology
- Default stack: **.NET**
- Primary language: **C#**
- Web-based projects: **TypeScript** (compiled to JavaScript)

### 1.2 Project format and target frameworks
- All C# projects shall use **SDK-style** `.csproj`.
- To support legacy .NET clients, projects shall target at minimum:
  - `net48`
  - `net10.0`
  (multi-target where applicable and technically feasible)

---

## 2. Core Engineering Principles (All Languages)

- Follow **DRY**, **SOLID**, and **Dependency Injection (DI)**.
- Code shall be **unit-testable**.
- Each method/function shall do **one thing**, be self-contained, and be parameterized for varying inputs.

---

## 3. C# Style and Compatibility Rules

- Prefer classic C# style to maintain compatibility with legacy .NET clients.
- Avoid modern .NET syntax sugar unless the project targets modern .NET only (no legacy targets).
- Nullable `?` should be used only for primitive types where supported across both legacy and modern targets.

---

## 4. Public API Documentation and IP Documentation

### 4.1 Public API documentation (mandatory)
- All **public APIs** shall be documented.
- Public API documentation shall describe:
  - what the method/type does
  - possible exceptions that can be thrown

### 4.2 Private IP / trade secrets (mandatory)
- Private IP/trade-secret implementation details (e.g., proprietary algorithms) shall be documented **inline** (implementation comments).
- Do not expose private algorithm details in public API documentation.

---

## 5. Source Organization Rules

- Each C# type shall be defined in its own file.
- One `.csproj` per layer shall be maintained.

---

## 6. Repository Structure (VS 2026)

### 6.1 Solution format
- Use Visual Studio 2026 solution format **`.slnx`**.
- The `.slnx` file shall live at the **repository root**.

### 6.2 Folder structure
Repositories shall be structured as:
- `src/` for production projects
- `tests/` for test projects
- `Shared/` for shared build metadata and assembly metadata projects

---

## 7. Shared Assembly Metadata Project (Mandatory)

### 7.1 Shared project requirement
- Under `Shared/`, maintain a shared project named `[Domain]AssemblyInfo` that contains:
  - shared assembly attributes (Company, Trademark, Copyright)
  - path to strong-name key file used for strong-naming assemblies
  - shared MSBuild props/targets as needed

### 7.2 How projects must consume it
- By default, all new SDK-style `.csproj` shall reference the shared `[Domain]AssemblyInfo` project.

### 7.3 Avoiding duplicate metadata attributes
- Project templates shall disable **only** the duplicate SDK-generated assembly metadata attributes centralized in the shared project:
  - Company
  - Trademark
  - Copyright

(Do not disable all assembly attributes globally unless explicitly required.)

---

## 8. Release-only Packaging and XML Docs

- XML documentation file generation shall be enabled only for `Release` builds.
- NuGet package generation (packing) shall be enabled only for `Release` builds.

---

## 9. Naming Conventions (Mandatory)

### 9.1 Project naming
- `.csproj` name: `[Domain].[Layer]`
  - Folder name may omit `[Domain]` and use only `[Layer]`
  - The actual project file name must include `[Domain]`

### 9.2 Namespaces and outputs
- Root namespace: `Invitrek.[Domain]`
- Assembly name: `Invitrek.[Domain].[Layer].[Provider]` (Provider optional)
- NuGet package id: `Invitrek.[Domain].[Layer].[Provider]` (Provider optional)

### 9.3 Provider convention
- `[Provider]` is optional.
- Use `[Provider]` when a project implements a specific contract defined at the layer level.

---

## 10. Database Naming Conventions (Mandatory)

- Table names shall be **singular**.
- Table names shall use **PascalCase**.

---

## 11. Testing Requirements (Mandatory)

- All projects shall have associated test projects.
- Test suites shall provide comprehensive coverage appropriate to project scope.
- CI should run tests on each PR.

---

## 12. Backend Architecture Standards (ASP.NET Core 10)

### 12.1 Persistence
- Server persistence uses **SQL Server**.
- CRUD and persistence shall use **strongly typed C# classes**.

### 12.2 Patterns
- Use **Repository pattern** for data access.
- Use a **Service layer** that delegates to repository implementations via **DI**.

### 12.3 Namespace conventions (mandatory)
Server-side:
- Table-mapped entity types: `Invitrek.[Domain].Repository.Entities`
- Repository contracts/interfaces: `Invitrek.[Domain].Repository.Contracts`
- Repository implementations:
  - internal by default (not part of public API surface)
  - decorated with `[DependencyServiceAtttribute(typeof(IRepositoryContract))]` for automatic DI registration
- Service contracts/interfaces: `Invitrek.[Domain].Services.Contracts`

Client-side:
- Service proxies: `Invitrek.[Domain].Services.Proxy`
- Client-side data models: `Invitrek.[Domain].Data.Entities`

---

## 13. Requirements-first (Mandatory for New Domain Repos)

- For every new domain-level repository/project, requirements and design docs (SRS) shall be created first.
- SRS shall be updated as requirements evolve.

---

## 14. Automation Rules (Mandatory)

- Avoid PowerShell scripts for standardized/repeated automation tasks.
- Prefer C#/.NET automation (console apps or dotnet tools).
- For repeated tasks, implement reusable shared C# libraries; avoid duplicating logic across repos.

---

## 15. Session Continuity (Required)

To prevent context loss from crashes/hangs/power failures:
- Maintain a repo-level session tracker (`docs/session/SESSION_TRACKER.md`).
- Update tracker incrementally after each meaningful requirement/decision/context input.
- At end of session, consolidate changes back into:
  - SRS (`docs/SRS_*.md`)
  - decisions (`docs/session/DECISIONS.md`)
  - backlog (`docs/session/TODO.md`)

---

## 16. Org-level Standards Alignment (Required)

When standards are revised:
- Update the canonical standards repo (`Invitrek-swt/engineering-standards`)
- Propagate updates to repos using the C# SyncStandards tool.
