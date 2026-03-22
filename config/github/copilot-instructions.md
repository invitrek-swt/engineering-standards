# Copilot Instructions (Organization-wide) — Invitrek-swt

## Project Guidelines
- Default stack: .NET, primary language: C#. Web projects: TypeScript (compiled to JavaScript).
- For legacy support, C# projects should use SDK-style `.csproj` and support multi-targeting `net48` and `net10.0` where applicable.
- Follow DRY, SOLID, and Dependency Injection (DI). Code must be unit-testable.
- Methods should do one thing, be self-contained, and parameterized for varying inputs.
- Prefer classic C# style for legacy compatibility; avoid modern syntax sugar unless the project is modern-only.
- Nullable `?` should be used only for primitive types where supported across both legacy and modern targets.
- Database tables must be singular and PascalCase.
- Every project must include tests with comprehensive coverage appropriate to scope.

## Requirements-first (mandatory for new domain repos)
- For every new domain-level repository/project, create requirements and design documentation (SRS) first.
- Keep SRS updated as requirements evolve.

## Automation scripting rules
- Avoid PowerShell scripts for standardized/repeated automation tasks.
- Prefer C#/.NET tooling (console apps or dotnet tools).
- For repeated tasks, create reusable C# libraries and share them across repos; do not duplicate logic.

## Session continuity (required)
- To preserve context across sessions and reduce loss from crashes/hangs/power failure, maintain a repo-level session tracker.
- Update `docs/session/SESSION_TRACKER.md` incrementally: after each meaningful requirement/decision/context input, append an entry.
- At the end of a working session, consolidate and sync key updates into:
  - `docs/SRS_*.md` or `docs/SRS_*.md` equivalent (requirements)
  - `docs/session/DECISIONS.md` (durable decisions)
  - `docs/session/TODO.md` (backlog)
