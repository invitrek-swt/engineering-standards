# Quality Standards — Invitrek-swt

**Applies to:** All repositories  
**Principle:** Track quality, security, bugs, and optimizations at the **requirements phase** — not after release.

---

## 1. Quality Gates

### Build Gates (automated — CI enforced)

| Gate | Target | Tool | Failure action |
|------|--------|------|----------------|
| Code Quality Grade | A | SonarQube / Roslyn Analyzers | **Build FAILS** |
| Technical Debt Ratio | <10% | SonarQube | **Build FAILS** |
| Code Duplication | <3% | SonarQube | **Build FAILS** |
| Unit Test Coverage | >90% | Coverlet | **Build FAILS** |

### Release Gates (manual approval required)

| Gate | Target | Approver | Failure action |
|------|--------|----------|----------------|
| Critical Bugs | 0 | QA Lead | **Release BLOCKED** |
| High Bugs | 0 | QA Lead | **Release BLOCKED** |
| Critical CVEs | 0 | Security Lead | **Release BLOCKED** |
| High CVEs | 0 | Security Lead | **Release BLOCKED** |
| Public API Documentation | 100% | Tech Writer | **Release BLOCKED** |

---

## 2. Bug Tolerance Policy

- **0 critical bugs** allowed at release (Release Blocker).
- **0 high bugs** allowed at release (Release Blocker).
- Medium and low bugs: tracked with milestone target dates.
- Bug tolerance is evaluated against all target frameworks, not just one.

---

## 3. Security Requirements (Zero Tolerance)

### Pre-release checklist
- [ ] STRIDE threat modeling completed
- [ ] SAST (Static Application Security Testing) passed
- [ ] DAST (Dynamic Application Security Testing) passed
- [ ] Dependency vulnerability scan: 0 critical/high CVEs
- [ ] OWASP Top 10: 100% tested
- [ ] Penetration testing completed for critical features

### Secure coding (mandatory)

```csharp
// ✅ Always: parameterized queries
command.CommandText = "SELECT * FROM Users WHERE Id = @Id";
command.Parameters.AddWithValue("@Id", userId);

// ❌ Never: string concatenation (SQL injection risk)
command.CommandText = $"SELECT * FROM Users WHERE Id = {userId}";
```

---

## 4. Test Coverage Requirements

| Test type | Target | Scope |
|-----------|--------|-------|
| Unit tests | >90% | All business logic and domain code |
| Integration tests | >80% | Repository, service, and API layers |
| Critical path coverage | 100% | All happy-path and key error-path scenarios |

- Tests must run against all target frameworks in CI.
- Tests must be repeatable and deterministic (no flaky tests).

---

## 5. Compliance Checklist

### A) SDLC / Process

- [ ] Requirements/SRS approved and linked
- [ ] Design document approved and linked
- [ ] Session tracker active with incremental updates
- [ ] Existing-scope review completed before adding planned scope
- [ ] Planned scope location/structure preview approved before additions
- [ ] End-of-session consolidation completed

### B) Architecture / Design

- [ ] DRY reuse-first check completed (no duplicate functionality)
- [ ] SOLID principles followed
- [ ] Open/Closed Principle enforced
- [ ] GoF/CQRS/repository patterns applied where appropriate
- [ ] API-first flow followed (contract approved before implementation)
- [ ] Domain layer model validated (`Data` client-side, `Repository` data-access)
- [ ] Service namespace defaults reviewed

### C) Coding / Compatibility

- [ ] C#-only automation/scripts (no PowerShell)
- [ ] Cross-target compatibility verified (`net48` + `net10.0`)
- [ ] Nullable `?` used only for compatible value types
- [ ] No modern-only syntax that breaks `.NET Framework 4.8`
- [ ] Target-specific references in conditioned `ItemGroup`
- [ ] XML docs and NuGet packaging configured for Release only
- [ ] PascalCase + singular table naming followed
- [ ] DB `ID` suffix capitalization enforced
- [ ] POCO identity uses `Id` per `IDbEntity` contract
- [ ] DB-to-CLR alias/mapping consistency verified

### D) API / Documentation

- [ ] Public contracts have XML documentation
- [ ] Internal/private functionality documented inline
- [ ] Generic vs project-specific placement reviewed
- [ ] `[Domain]AssemblyInfo` shared project exists and referenced
- [ ] Company/Trademark/Copyright auto-attributes disabled in project templates
- [ ] Service-bound DTOs use validation attributes where required
- [ ] Service entry points enforce model validation before business execution

### E) CLI / Logging / Metrics

- [ ] CLI uses Core `CommandLine` framework
- [ ] CLI host bootstraps with `ConsoleAppBuilder`
- [ ] Built-in help/discovery behavior verified
- [ ] Centralized static `Logger` used (not `Console.WriteLine`)
- [ ] Action-level outputs logged
- [ ] Execution-time metrics captured for major operations

### F) Generation / Mapping

- [ ] Generated types use `partial` classes
- [ ] `nameof(...)` used for type/member references
- [ ] CodeModel builders used over raw string assembly
- [ ] Multi-provider implementations use shared project + conditional flags

### G) Release Evidence

- [ ] Contract readiness sign-off linked
- [ ] Consolidated test report linked and current
- [ ] Benchmark report linked with reproducible metadata
- [ ] Scenario evidence files linked and updated
- [ ] Go/No-Go decision record completed
- [ ] MSBuild-driven public API XML docs generation verified

### H) Gate Decision Readiness

- [ ] No unresolved release-blocking compliance gap
- [ ] All No-Go gates have owner, target date, and evidence path
- [ ] Measurable exit criteria defined and tracked

---

## Audit Summary Template

```
Reviewer:
Date:
Decision: Go / No-Go
Notes:
```
