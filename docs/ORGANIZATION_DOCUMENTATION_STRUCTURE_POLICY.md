# Organization Documentation Structure Policy

## 🎯 Policy Statement

**ORGANIZATION-WIDE REQUIREMENT**: All documentation MUST mirror the project structure to ensure maintainability, prevent broken links, enable portal integration, and provide clear team ownership.

**Effective Date**: 2025-01-15  
**Scope**: All products (Foundation Framework, Nexus Platform, Developer Studio, future products)  
**Authority**: Architecture & Documentation Teams  
**Enforcement**: Automated validation in CI/CD pipeline

---

## 📐 Core Principle: Mirror Structure

### The Mirror Rule (MANDATORY)

```
PROJECT STRUCTURE              DOCUMENTATION STRUCTURE
═════════════════              ═══════════════════════
src/{Domain}/{Component}/  →   docs/{Domain}/{Component}/
```

**Every project folder MUST have a corresponding documentation folder with identical hierarchy.**

---

## 📁 Standard Documentation Structure (MANDATORY)

### For Abstraction Components

**Applies to**: Data, AI, Workflow, UIAutomation, ServiceModel, Security, Server (any component with provider implementations)

```
docs/{Domain}/{Component}/
├── ARCHITECTURE.md          ← REQUIRED: High-level overview
├── ABSTRACTION_LAYER.md     ← REQUIRED: Interface definitions, provider examples
├── USAGE_EXAMPLES.md        ← REQUIRED: Real-world usage scenarios (≥3 examples)
└── API/                     ← AUTO-GENERATED: API reference (do not edit manually)
    ├── {Namespace}.md
    ├── {Namespace}.SubNamespace.md
    └── ...
```

### For Regular Components

**Applies to**: Core, Runtime, Configuration, Identity, Client, etc. (components without providers)

```
docs/{Domain}/{Component}/
├── ARCHITECTURE.md          ← REQUIRED: High-level overview
└── API/                     ← AUTO-GENERATED: API reference (do not edit manually)
    └── {Namespace}.md
```

### For Provider Implementations

**Provider documentation MUST be included in the abstraction's ABSTRACTION_LAYER.md file.**

**DO NOT create separate documentation folders for providers.**

```
❌ INCORRECT:
docs/{Domain}/{Component}.Providers/{Provider}/

✅ CORRECT:
docs/{Domain}/{Component}/ABSTRACTION_LAYER.md
  (Include provider-specific documentation here)
```

**Example:**
```markdown
<!-- docs/Foundation/Data/ABSTRACTION_LAYER.md -->

## IRepository<TEntity, TKey> Interface

### Provider Implementations

#### SqlServer Provider
```csharp
services.AddFoundationData(options => options.UseSqlServer(connectionString));
```

#### Oracle Provider
```csharp
services.AddFoundationData(options => options.UseOracle(connectionString));
```
```

---

## 🔗 Linking Guidelines (MANDATORY)

### Rule 1: Use Relative Links ONLY

**✅ CORRECT:**
```markdown
See [Foundation.AI](../AI/ARCHITECTURE.md)
See [Workflow integration](../Workflow/ABSTRACTION_LAYER.md)
See [IRepository interface](./API/Invitrek.Foundation.Data.md#irepository)
```

**❌ FORBIDDEN:**
```markdown
See [Foundation.AI](/docs/Foundation/AI/ARCHITECTURE.md)
See [Foundation.AI](https://github.com/org/repo/docs/Foundation/AI/ARCHITECTURE.md)
```

**Rationale**: Relative links remain valid when documentation is moved as a group. Absolute paths break during refactoring.

### Rule 2: Link Depth Guidelines

**Same Domain:**
```markdown
<!-- docs/Foundation/Data/ARCHITECTURE.md -->
Foundation.Data integrates with [Foundation.AI](../AI/ARCHITECTURE.md).
```

**Cross-Domain:**
```markdown
<!-- docs/Foundation/Data/ARCHITECTURE.md -->
Foundation.Data is used by [Nexus.Server](../../Nexus/Server/ARCHITECTURE.md).
```

**Same Component:**
```markdown
<!-- docs/Foundation/Data/ARCHITECTURE.md -->
See [abstraction layer](./ABSTRACTION_LAYER.md) for interface details.
See [usage examples](./USAGE_EXAMPLES.md) for real-world scenarios.
```

### Rule 3: API Reference Links

**Link to auto-generated API reference:**
```markdown
See [IRepository API](./API/Invitrek.Foundation.Data.md#irepository) for complete method signatures.
```

---

## 🏢 Domain Ownership (MANDATORY)

### Domain Folders = Team Ownership

```
docs/Foundation/        → Foundation Team
docs/Nexus/             → Nexus Team
docs/DeveloperStudio/   → DeveloperStudio Team
```

**Each team is responsible for:**
- ✅ Creating documentation when adding new components
- ✅ Updating documentation when changing APIs
- ✅ Reviewing documentation PRs in their domain
- ✅ Maintaining documentation quality

### GitHub CODEOWNERS

**File**: `.github/CODEOWNERS`

```gitignore
# Domain Documentation Ownership
docs/Foundation/**          @foundation-team
docs/Nexus/**               @nexus-team
docs/DeveloperStudio/**     @devstudio-team

# Require team approval for documentation changes
```

---

## 📚 Documentation Portal Integration (AUTOMATIC)

### Portal Structure Auto-Generated

**The mirror structure enables automatic portal navigation generation.**

**DocFX Configuration**:
```json
{
  "build": {
    "content": [
      {
        "files": ["**/*.md"],
        "src": "docs",
        "dest": "."
      }
    ]
  }
}
```

**MkDocs Configuration**:
```yaml
site_name: Organization Documentation
docs_dir: docs
nav:
  - Foundation:
    - Core: Foundation/Core/ARCHITECTURE.md
    - Data: Foundation/Data/ARCHITECTURE.md
    - AI: Foundation/AI/ARCHITECTURE.md
    - Workflow: Foundation/Workflow/ARCHITECTURE.md
  - Nexus:
    - Server: Nexus/Server/ARCHITECTURE.md
    - Client: Nexus/Client/ARCHITECTURE.md
  - DeveloperStudio:
    - Workflow.AI: DeveloperStudio/Workflow.AI/ARCHITECTURE.md
```

**Result**: Portal navigation tree matches project structure automatically.

---

## ✅ Enforcement Mechanisms

### 1. CI/CD Link Validation (AUTOMATED)

**GitHub Actions Workflow**: `.github/workflows/validate-docs-structure.yml`

```yaml
name: Validate Documentation Structure

on:
  pull_request:
    paths:
      - 'docs/**'
      - 'src/**/*.cs'

jobs:
  validate-structure:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Validate Mirror Structure
        run: |
          # Check that every src/{Domain}/{Component}/ has docs/{Domain}/{Component}/
          python scripts/validate-doc-structure.py
      
      - name: Validate Relative Links
        uses: gaurav-nelson/github-action-markdown-link-check@v1
        with:
          config-file: .markdown-link-check.json
      
      - name: Check Required Files
        run: |
          # Check that ARCHITECTURE.md exists for all components
          python scripts/check-required-docs.py
```

### 2. Pre-Commit Hook Validation (LOCAL)

**Pre-commit hook**: `.git/hooks/pre-commit`

```bash
#!/bin/bash
# Validate documentation structure before commit

# Check for absolute links (forbidden)
if grep -r "](http" docs/ --include="*.md"; then
    echo "❌ ERROR: Absolute links found in documentation"
    echo "   Use relative links only (e.g., ../AI/ARCHITECTURE.md)"
    exit 1
fi

# Check for missing ARCHITECTURE.md
for component in src/*/*/; do
    domain=$(basename $(dirname "$component"))
    component_name=$(basename "$component")
    arch_doc="docs/$domain/$component_name/ARCHITECTURE.md"
    
    if [ ! -f "$arch_doc" ]; then
        echo "⚠️  WARNING: Missing $arch_doc"
    fi
done
```

### 3. Code Review Checklist

**Reviewers MUST verify:**
- [ ] Documentation folder mirrors project folder structure
- [ ] All links are relative (no absolute paths)
- [ ] ARCHITECTURE.md exists for all components
- [ ] ABSTRACTION_LAYER.md exists for abstractions
- [ ] USAGE_EXAMPLES.md has ≥3 examples
- [ ] No separate provider documentation folders (use main component folder)

---

## 🚫 Common Violations & Corrections

### Violation 1: Absolute Links

**❌ INCORRECT:**
```markdown
See [Foundation.AI](https://github.com/org/repo/blob/main/docs/Foundation/AI/ARCHITECTURE.md)
```

**✅ CORRECT:**
```markdown
See [Foundation.AI](../AI/ARCHITECTURE.md)
```

### Violation 2: Broken Mirror Structure

**❌ INCORRECT:**
```
src/Foundation/Data/
docs/api/data/  ← Does not mirror project structure
```

**✅ CORRECT:**
```
src/Foundation/Data/
docs/Foundation/Data/  ← Mirrors project structure
```

### Violation 3: Provider Documentation Folders

**❌ INCORRECT:**
```
docs/Foundation/Data.Providers/SqlServer/
docs/Foundation/Data.Providers/Oracle/
```

**✅ CORRECT:**
```
docs/Foundation/Data/ABSTRACTION_LAYER.md
  (Include provider documentation here)
```

### Violation 4: Missing Required Files

**❌ INCORRECT:**
```
docs/Foundation/Data/
└── random-notes.md  ← No ARCHITECTURE.md
```

**✅ CORRECT:**
```
docs/Foundation/Data/
├── ARCHITECTURE.md           ← REQUIRED
├── ABSTRACTION_LAYER.md      ← REQUIRED for abstractions
├── USAGE_EXAMPLES.md         ← REQUIRED
└── API/                      ← AUTO-GENERATED
```

---

## 📋 Documentation Checklist (When Adding New Component)

### For Developers

**When creating a new component** (e.g., `src/Foundation/NewComponent/`):

- [ ] **Step 1**: Create project folder `src/{Domain}/{Component}/`
- [ ] **Step 2**: Create documentation folder `docs/{Domain}/{Component}/`
- [ ] **Step 3**: Create `ARCHITECTURE.md` (high-level overview)
- [ ] **Step 4**: If abstraction, create `ABSTRACTION_LAYER.md` (interface definitions)
- [ ] **Step 5**: Create `USAGE_EXAMPLES.md` (≥3 examples)
- [ ] **Step 6**: Build in Release mode (API reference auto-generated)
- [ ] **Step 7**: Verify `docs/{Domain}/{Component}/API/*.md` generated
- [ ] **Step 8**: Use relative links when referencing other components
- [ ] **Step 9**: Commit all documentation with code

### For Code Reviewers

**When reviewing PRs that add/modify components**:

- [ ] **Check 1**: Documentation folder mirrors project folder structure
- [ ] **Check 2**: All required files present (ARCHITECTURE.md, ABSTRACTION_LAYER.md, USAGE_EXAMPLES.md)
- [ ] **Check 3**: All links are relative (no absolute paths)
- [ ] **Check 4**: API reference folder exists (if component built in Release)
- [ ] **Check 5**: Provider documentation in abstraction folder (not separate folders)
- [ ] **Check 6**: Examples compile and make sense (≥3 examples)
- [ ] **Check 7**: Cross-references are accurate

---

## 🎯 Benefits Recap

**Why this policy matters:**

1. ✅ **Prevents Broken Links** - Relative paths stay valid when docs move with code
2. ✅ **Portal Integration** - Auto-generated navigation matches project structure
3. ✅ **Team Ownership** - Clear domain-based ownership for documentation
4. ✅ **No Duplicates** - Single source of truth per component
5. ✅ **Easy Discovery** - Same path as code = intuitive location
6. ✅ **Version Control** - Documentation moves with code during refactoring
7. ✅ **Automated Validation** - CI/CD checks enforce policy
8. ✅ **IDE Integration** - Predictable location for tooling integration

---

## 📖 Reference Documentation

**For complete details, see:**

- **DOCUMENTATION_STRUCTURE_MIRROR.md** - Visual side-by-side comparison
- **DOCUMENTATION_STRUCTURE_BENEFITS.md** - Detailed benefits analysis
- **DOCUMENTATION_SYNC_POLICY.md** - 5-location synchronization policy
- **POST_BUILD_DOCUMENTATION_COMPLETE.md** - Post-build automation guide

---

## 🚀 Implementation Timeline

### Phase 1: Immediate (New Components)
- ✅ All NEW components MUST follow mirror structure
- ✅ CI/CD validation enabled for new PRs

### Phase 2: Migration (Existing Components - 30 Days)
- 📅 Migrate existing documentation to mirror structure
- 📅 Update all absolute links to relative links
- 📅 Create missing ARCHITECTURE.md files

### Phase 3: Enforcement (60 Days)
- 📅 Enable CI/CD blocking for violations
- 📅 Pre-commit hook enforcement
- 📅 CODEOWNERS enforcement

---

## 📞 Questions & Support

**Policy Owners**: Architecture Team (@architecture-team)

**For questions:**
1. Review reference documentation (links above)
2. Ask in #documentation-support Slack channel
3. Open issue in GitHub: `[DOCS] Your question`

**Policy Violations:**
1. Automated checks will flag violations in CI/CD
2. Code reviewers will request corrections
3. If unclear, ask in #documentation-support

---

## 📝 Policy Updates

**Version**: 1.0  
**Last Updated**: 2025-01-15  
**Next Review**: 2025-04-15

**Change History**:
- 2025-01-15: Initial policy creation
- Future updates will be tracked here

---

## ✅ Acceptance

**By merging code that adds/modifies documentation, you acknowledge:**
- ✅ I have read and understood this policy
- ✅ My documentation follows the mirror structure
- ✅ All links are relative (no absolute paths)
- ✅ Required files are present (ARCHITECTURE.md, ABSTRACTION_LAYER.md, USAGE_EXAMPLES.md)
- ✅ Provider documentation is in abstraction folder (not separate)

---

**This policy ensures documentation stays maintainable, discoverable, and synchronized with code.** 🎯✨
