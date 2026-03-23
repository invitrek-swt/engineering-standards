# Copilot Instructions (Organization-wide) — Invitrek-swt

## Project Guidelines (mandatory)
- Default stack: .NET; primary language: C#. Web projects: TypeScript (compiled to JavaScript).
- For legacy support, C# projects shall use SDK-style `.csproj` and support multi-targeting `net48` and `net10.0` where applicable.
- Follow DRY, SOLID, and Dependency Injection (DI). Code must be unit-testable.
- Each method/function should do one thing, be self-contained, and be parameterized for varying inputs.
- Prefer classic C# style for legacy compatibility; avoid modern syntax sugar unless the project targets modern .NET only.
- Nullable `?` should be used only for primitive types where supported across both legacy and modern targets.
- Database tables shall be singular and PascalCase.
- Every project shall include tests with comprehensive coverage appropriate to scope.

## Requirements-first (mandatory for new domain repos)
- For every new domain-level repository/project, requirements and design docs (SRS) shall be created first.
- SRS shall be updated as requirements evolve.

## Automation rules (mandatory)
- Avoid PowerShell scripts for standardized/repeated automation tasks.
- Prefer C#/.NET automation (console apps or dotnet tools).
- For repeated tasks, create reusable C# libraries shared across repos; avoid duplicating logic.

## Session continuity (required)
- Maintain a repo-level session tracker to preserve context across crashes/hangs/power failures.
- Update `docs/session/SESSION_TRACKER.md` incrementally after each meaningful requirement/decision/context input.
- At the end of a working session, consolidate and sync key updates into:
  - `docs/SRS_*.md` (requirements)
  - `docs/session/DECISIONS.md` (durable decisions)
  - `docs/session/TODO.md` (backlog)

## Public API documentation rules (mandatory)
- All public APIs shall be documented.
- Public API docs shall describe:
  - what the method/type does
  - possible exceptions that can be thrown
- Private IP/trade-secret implementation details (e.g., proprietary algorithms) shall be documented inline (implementation comments), not exposed as public API docs.

## Source organization (mandatory)
- Each C# type shall be defined in its own file.
- One `.csproj` per layer shall be maintained.

## Release-only packaging and documentation
- XML documentation file generation and NuGet package generation shall be enabled only for `Release` configuration builds.

## Naming conventions (mandatory)
- `.csproj` name: `[Domain].[Layer]` (folder name may omit `[Domain]` and use only `[Layer]`, but the project file name shall include `[Domain]`).
- Root namespace: `Invitrek.[Domain]`.
- Assembly name: `Invitrek.[Domain].[Layer].[Provider]` (Provider optional).
- NuGet package id: `Invitrek.[Domain].[Layer].[Provider]` (Provider optional).

### Provider convention
- `[Provider]` is optional.
- Use `[Provider]` when a project implements a specific contract defined at the layer level.
