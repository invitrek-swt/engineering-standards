# Engineering Standards (Organization-wide) — Invitrek-swt

This document defines mandatory organization-wide standards for all repositories that follow Invitrek-swt engineering practices. It is the canonical Copilot/AI instruction file synced to all domain repos.

---

## 1. Default Technology Stack

### 1.1 Primary technology
- Default stack: **.NET**
- Primary language: **C#**
- Web-based projects: **TypeScript** (compiled to JavaScript)

### 1.2 Project format and target frameworks
- All C# projects shall use **SDK-style** `.csproj`.
- Projects shall target at minimum both:
  - `net48`
  - `net10.0`
  (multi-target where applicable and technically feasible)

---

## 2. Core Engineering Principles

- Follow **DRY**, **SOLID**, and **Dependency Injection (DI)**.
- Code shall be **unit-testable**.
- Each method/function shall do **one thing**, be self-contained, and parameterized for varying inputs.
- **Reuse-first (mandatory):** Before creating any new functionality, search existing code first. Only create new code after confirming no existing solution exists.
- **Open/Closed Principle (mandatory):** Contracts and components must be open for extension, closed for modification.
- Use switchable strategy-based implementations to reduce future breakage during optimization.

---

## 3. C# Style and Compatibility Rules

- Prefer classic C# style to maintain compatibility with `.NET Framework 4.8`.
- Avoid modern .NET syntax sugar unless the project exclusively targets modern .NET.
- Nullable `?` for **value types only** (`int?`, `bool?`, `DateTime?`, `Guid?`) where supported across both targets.
- Do **not** use nullable reference type annotations (`string?`) in shared cross-target code.
- Legacy baseline: minimum `.NET Framework 4.8` (Windows 7 SP1+). `.NET Framework 4.0`/Windows XP is out of scope.
- Each C# type shall be defined in its own file.

---

## 4. Automation Policy — C# ONLY, NO PowerShell

**ALL automation, scripts, and utilities MUST be written in C#. PowerShell is FORBIDDEN.**

Use:
- C# console applications
- DevTools Foundation classes (`CSharpProjectManager`, `CSharpFileManager`, `XamlFileManager`, `FileSystemManager`)
- `System.Diagnostics.Process` for external commands (if absolutely necessary)
- dotnet CLI via `Process.Start()`
- Core `CommandLine` framework with `ICommand`-based handlers
- `ConsoleAppBuilder` for CLI host bootstrap

Never use:
- PowerShell scripts (`.ps1` files)
- PowerShell cmdlets
- `powershell.exe` or `pwsh.exe` invocation

If asked to use PowerShell, respond:  
*"Per organization policy, we use C# for all automation. I'll create a C# utility using DevTools Foundation classes instead."*

### 4.1 Safety-first for destructive operations (MANDATORY)

ALL utilities that modify files, databases, or system state MUST implement:

1. **`--dry-run` mode** — preview all changes without executing.
2. **Backup/restore** — before any bulk file modification.
3. **Incremental rollout** — single file → single directory → full scope.
4. **Confirmation prompts** — for operations affecting >10 files.
5. **Detailed logging** — log every change with file path and timestamp.

### 4.2 Dynamic C# code generation

- **Use** CodeModel fluent APIs (`CSharpCodeBuilder`, `NamespaceBuilder`, `ClassBuilder`, `CSharpCodeWriter`).
- **Use** `nameof(...)` for type/member references.
- **Use** `EntitySet<T>` (not `DbSet<T>`) in generated contexts.
- **Never** use hardcoded raw string templates or manual `StringBuilder` for C# syntax.
- Use `@ColumnName`-style parameter identifiers instead of positional `@p0/@p1` in generated SQL.
- Generated extensibility types/contracts should be declared `partial`.
- Always alias DB column names to CLR property names in queries/mappings.

---

## 5. Requirements-First / SDLC Workflow (MANDATORY)

All features MUST follow this workflow. No code without approved requirements AND design.

```
1. Requirements/SRS Document (FIRST — mandatory)
2. Design Document (after requirements approval)
3. Implementation (after design approval)
4. Post-Implementation Reports (Quality, Compatibility, Test, Performance, Improvements)
5. Documentation Update
6. Final Sign-Off
```

When asked to implement any new feature or code:

1. Ask: "Do we have an approved Requirements/SRS document?"
   - If NO → offer to create one using `templates/REQUIREMENTS_SRS_TEMPLATE.md`
   - If YES → verify it exists and is approved, then proceed to step 2
2. Ask: "Do we have an approved Design Document?"
   - If NO → offer to create one using `templates/DESIGN_DOCUMENT_TEMPLATE.md`
   - If YES → verify it exists and is approved, then proceed to implementation
3. Confirm implementation plan with user before writing code.
4. After implementation, prompt for post-implementation reports.

**Forbidden:**
- Writing code without approved requirements AND design
- Skipping post-implementation reports
- Proceeding without user confirmation at each phase gate

---

## 6. IP Protection (MANDATORY)

**Principle: "Open Standards, Closed Innovation"**

| Visibility | What belongs here |
|------------|-------------------|
| `public` | Interfaces, abstract base classes, standard .NET attributes |
| `internal` | Algorithm implementations, caching, SQL generation, performance optimizations |
| `private` | Core proprietary algorithm methods, internal state |

- Before making any class `public`: confirm it is a pure contract or uses only standard .NET attributes.
- Internal implementation details may be documented inline for internal developers only.
- XML comments on public APIs: describe WHAT and HOW to use — never expose implementation details.

---

## 7. Architecture and Naming

### 7.1 Shared assembly metadata
- All domain projects reference a domain-specific `[Domain]AssemblyInfo` shared project.
- Only disable the three attributes provided by shared assembly info:

```xml
<GenerateAssemblyCompanyAttribute>false</GenerateAssemblyCompanyAttribute>
<GenerateAssemblyCopyrightAttribute>false</GenerateAssemblyCopyrightAttribute>
<GenerateAssemblyTrademarkAttribute>false</GenerateAssemblyTrademarkAttribute>
```

### 7.2 Naming conventions
- Project file: `[Layer].[Provider].csproj` (Provider optional)
- Assembly name: `Invitrek.[Domain].[Layer][.[Provider]]`
- Root namespace: `Invitrek.[Domain].[Area]`
- DB tables: singular, PascalCase. Primary key: `[TableName]ID`. POCO identity: `Id`.
- No duplicate short project names (e.g., multiple `Core.csproj`) — use domain-qualified names.

### 7.3 Layer and namespace defaults

| Layer | Namespace |
|-------|-----------|
| Repository entities | `Invitrek.[Domain].Repository.Entities` |
| Repository contracts | `Invitrek.[Domain].Repository.Contracts` |
| Service contracts | `Invitrek.[Domain].Services.Contracts` |
| Service implementations | `Invitrek.[Domain].Services` |
| Service proxies (client) | `Invitrek.[Domain].Services.Proxy` |
| Client-side data models | `Invitrek.[Domain].Data.Entities` |

### 7.4 Cross-target compatibility
- Group target-specific references in conditioned `ItemGroup`.
- Multi-provider implementations use shared project (`*.shproj`) + provider conditional flags.
- Enable XML docs and NuGet packaging in Release builds only.

### 7.5 Documentation structure
- Place docs under `docs/<Domain>/<AreaOrGroup>/`.
- Do not mirror source paths as `docs/src/...`.
- Each domain and each area must have an `INDEX.md`.
- Maintain domain roadmap at `docs/<Domain>/ROADMAP.md`.

---

## 8. Quality Requirements

**Build gates (automated):**
- Code Quality: Grade A — BUILD GATE
- Test Coverage: >90% unit — BUILD GATE
- Code Duplication: <3% — BUILD GATE

**Release gates:**
- 0 critical/high bugs — RELEASE BLOCKER
- 0 critical/high CVEs — RELEASE BLOCKER
- 100% public API documentation — RELEASE GATE

When creating Requirements or Design documents, always emphasize:
- Section: Quality Requirements (test coverage, bug tolerance, technical debt)
- Section: Security Requirements (STRIDE threat modeling, OWASP Top 10, CVE policy)
- Section: Known Limitations (what system WON'T do, edge cases, performance limits)
- Section: Support Prevention (error messages, self-service, monitoring)

---

## 9. WPF / UIFramework Standards

- Treat `Invitrek.Foundation.Presentation.Controls` as the primary reusable WPF foundation library.
- XAML namespace prefix: `ifx`, CLR root: `Invitrek.Foundation.Presentation.Controls`.
- Custom controls contain **no business/domain logic** — general-purpose only.
- Prefer `TemplateBinding` and `DynamicResource` for theme keys in control templates.
- Use vector/geometry-based icons everywhere (no emoji or text glyphs).
- Theme appearance via brushes only — no behavior changes for restyling.
- `ResourceDictionary` per control; indexed in `Themes/Generic.xaml`.
- Always use fully-qualified pack URIs when referencing resource dictionaries.
- UI control semantic naming: `*.Bar`, `*.Menu`, `*.Panel`, `*.Layout`, `*.Canvas`, `*.Surface` — see `docs/wpf-standards.md`.

---

## 10. Session Continuity

- Maintain `docs/session/SESSION_TRACKER.md` — update incrementally after each meaningful change.
- At end of session, consolidate into SRS, `DECISIONS.md`, and `TODO.md`.
- Create a session tracker at the start of each session; append incremental updates; finalize at end.

---

## 11. Org-Level Standards Alignment

When standards are revised:
- Update the canonical standards repo (`invitrek-swt/engineering-standards`) first.
- Propagate updates to all domain repos using the C# `SyncStandards` tool.

**Canonical standards:** https://github.com/Invitrek-swt/engineering-standards
