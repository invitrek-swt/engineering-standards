# Coding Standards — Invitrek-swt

**Applies to:** All repositories  
**Last updated:** See git history

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
- Each method/function shall do **one thing**, be self-contained, and be parameterized for varying inputs.
- **Reuse-first:** Search existing libraries before adding new functionality. Generic reusable functionality goes into the Core library (Foundation); UI controls and MVVM infrastructure go into UIFramework; domain/SDLC utilities go into DevTools. See `architecture-guidelines.md §11` for Foundation and UIFramework platform library policy.
- **Open/Closed Principle** is mandatory: contracts and components must be open for extension and closed for modification.
- Use switchable strategy-based implementations to reduce future breakage during optimization.

---

## 3. C# Style and Compatibility Rules

- Prefer classic C# style to maintain compatibility with `.NET Framework 4.8`.
- Avoid modern .NET syntax sugar unless the project exclusively targets modern .NET (no legacy targets).
- Nullable `?` should be used only for **value types** (primitives/structs: `int?`, `bool?`, `DateTime?`, `Guid?`) where supported across both legacy and modern targets.
- Do **not** use nullable reference type annotations (`string?`) in shared cross-target code.
- Legacy baseline: minimum supported `.NET Framework` target is `.NET Framework 4.8` (Windows 7 SP1+). `.NET Framework 4.0`/Windows XP support is out of scope.

### 3.1 Type declarations
- Each C# type shall be defined in its own file.

### 3.2 Braces and formatting
- Always use braces for control flow blocks (`if`, `for`, `while`, etc.).
- Opening brace on a new line (Allman style).
- 4-space indentation.

### 3.3 `var` usage
- Do not use `var` for built-in types. Use explicit types.
- `var` is acceptable when the type is immediately apparent from the right-hand side.

### 3.4 Naming
- `PascalCase` for types, methods, properties, events, constants.
- `camelCase` for local variables and parameters.
- `_camelCase` for private fields.
- Interface names prefixed with `I` (e.g., `IRepository<T>`).
- Avoid abbreviations except for well-known acronyms (capitalize fully: `ID`, `HTTP`, `ORM`).

---

## 4. Automation Policy

- **C# only** for all automation, scripts, and utilities. PowerShell is forbidden.
- For repeated tasks, implement reusable C# libraries; avoid duplicating logic across repos.
- CLI tools must use the Core `CommandLine` framework and `ICommand`-based handler abstractions.
- CLI host bootstraps with `ConsoleAppBuilder`.

### 4.1 Safety-first for destructive operations

ALL utilities that modify files, databases, or system state MUST implement:

1. **`--dry-run` mode** — preview all changes without executing them.
2. **Backup/restore** — before any bulk file modification (>1 file).
3. **Incremental rollout** — test on single file → single directory → full scope.
4. **Confirmation prompts** — for bulk operations affecting >10 files.
5. **Detailed logging** — log every change (before/after, file path, timestamp).

Never run destructive operations on the full scope without validating with `--dry-run` first.

### 4.2 Dynamic C# code generation

When generating C# code dynamically (entities, DTOs, interfaces, scaffolding):

- **Use** CodeModel fluent APIs (`CSharpCodeBuilder`, `NamespaceBuilder`, `ClassBuilder`, `CSharpCodeWriter`).
- **Use** `nameof(...)` for type/member references wherever possible.
- **Never** use hardcoded raw string templates or manual `StringBuilder` for C# syntax generation.
- Use `@ColumnName`-style parameter identifiers instead of positional `@p0/@p1` in generated SQL.
- Generated extensibility types/contracts should be declared `partial`.
- Auto-generated code must include documentation instructing customization via separate partial files.

---

## 5. Public API Documentation and IP Protection

### 5.1 Public API documentation (mandatory)
- All public APIs shall have XML documentation comments.
- Describe: what the method/type does, parameters, return values, and possible exceptions.
- Do **not** expose private algorithm details or performance tuning strategies in public XML docs.

### 5.2 Intellectual property protection
- Algorithm implementations, performance optimizations, caching strategies, and SQL generation logic → mark `internal` or `private`.
- Public API surface = interfaces and abstract base classes only (contracts).
- `internal` classes with proprietary algorithms may be documented inline for internal developers.

**Access modifier strategy:**

| Visibility | What belongs here |
|------------|-------------------|
| `public` | Interfaces, abstract base classes, standard .NET attribute usages |
| `internal` | Algorithm implementations, performance optimizations, caching, SQL generation |
| `private` | Core proprietary algorithm methods, internal state |

---

## 6. Testing Requirements

- All projects shall have associated test projects under the domain `Tests` folder.
- Test suites shall provide comprehensive coverage appropriate to project scope.
- Target: >90% unit test coverage, >80% integration test coverage, 100% critical path coverage.
- CI must run tests on each PR against all target frameworks.

---

## 7. Serialization Policy

- Use JSON as the default serialization format.
- Use `System.Text.Json` as the default serializer.
- Reuse existing Core serialization helper/extension methods.
- Do not introduce new `Newtonsoft.Json` dependencies.

---

## 8. Logging and Observability

- Use the centralized static `Logger` / `LoggerInstance` surface consistently.
- Log action-level behavior and outputs.
- Capture execution-time metrics for major operations.
- Route log messages through Core `Logger` methods (not direct `Console.WriteLine`) to enable flexible log sinks.

---

## 9. Session Continuity

To prevent context loss from crashes/hangs/power failures:

- Maintain a repo-level session tracker (`docs/session/SESSION_TRACKER.md`).
- Update tracker incrementally after each meaningful requirement/decision/context input.
- At end of session, consolidate changes back into SRS, DECISIONS.md, and TODO.md.
- Maintain per-domain roadmap (`docs/<Domain>/ROADMAP.md`) for planned/carry-forward items.
