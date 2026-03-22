# Coding Standards (Invitrek-swt)

## 1. Default stack
- Primary technology: .NET
- Primary language: C#
- Web projects: TypeScript (compiled to JavaScript)

## 2. Project / framework targeting
- All C# projects shall use SDK-style `.csproj`.
- To support legacy .NET clients, projects shall target at minimum:
  - net48
  - net10.0
  (multi-targeting where applicable)

## 3. General rules (all languages)
- Follow DRY, SOLID, and Dependency Injection (DI).
- Code must be unit-testable.
- Each function/method should do one thing, be self-contained, and be parameterized for varying inputs.

## 4. C# style (legacy compatibility)
- Prefer classic C# style to maintain backward compatibility with legacy .NET clients.
- Avoid modern syntax sugar unless the project targets modern .NET only (no legacy targets).
- Use nullable `?` only for primitive types where supported across both legacy and modern targets.

## 5. Database naming conventions
- Table names shall be singular.
- Table names shall use PascalCase.

## 6. Testing
- All projects must have associated test projects.
- Test suites must provide comprehensive coverage appropriate to the project scope.
- CI should run tests on each PR.
