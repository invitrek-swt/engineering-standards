# Organization Structure Guidelines

**Version:** 1.0  
**Status:** 🚀 Active Policy  
**Scope:** All Projects, Documentation, Portals, Solutions  
**Last Updated:** 2026-01-XX

---

## 🎯 Core Principle: Mirror Structure Everywhere

**ALL organizational artifacts (code, docs, portals, solutions) MUST follow the same category-based structure pattern.**

---

## 📐 Universal Structure Pattern

### **Foundation Domain Structure**

```
{Root}/
├── Core/                           ← Core abstractions
├── Runtime/                        ← DI, application lifecycle
├── Configuration/                  ← Configuration abstractions
│
├── DataAccess/                     ← DATA ACCESS FRAMEWORK (category)
│   ├── {Abstraction}.csproj        ← Data.csproj
│   └── Providers/
│       ├── {Provider1}/
│       ├── {Provider2}/
│       └── Shared/
│
├── Communication/                  ← COMMUNICATION FRAMEWORK (category)
│   ├── {Abstraction}.csproj        ← ServiceModel.csproj
│   └── Providers/
│
├── Identity/                       ← IDENTITY & SECURITY FRAMEWORK (category)
│   ├── {Abstraction}.csproj        ← Security.csproj
│   └── Providers/
│
├── AI/                             ← AI FRAMEWORK (category)
│   ├── {Abstraction}.csproj        ← AI.csproj
│   └── Providers/
│
├── Workflow/                       ← WORKFLOW FRAMEWORK (category)
│   ├── {Abstraction}.csproj        ← Workflow.csproj
│   └── Providers/
│
├── UIAutomation/                   ← UI AUTOMATION FRAMEWORK (category)
│   ├── {Abstraction}.csproj        ← UIAutomation.csproj
│   └── Providers/
│
└── UI/                             ← UI FRAMEWORK (category)
    ├── Core/
    ├── MvvmCore/
    ├── Presentation/
    └── {Platform}/
```

---

## 🗂️ Apply Pattern to All Organizational Artifacts

### **1. Source Code Structure** (`src/Foundation/`)

```
src/Foundation/
├── Core/
├── DataAccess/
│   ├── Data.csproj
│   └── Providers/
├── Communication/
│   ├── ServiceModel.csproj
│   └── Providers/
└── ...
```

### **2. Documentation Structure** (`docs-portal/products/foundation/`)

```
docs-portal/products/foundation/
├── core/                           ← Maps to src/Foundation/Core/
├── data-access/                    ← Maps to src/Foundation/DataAccess/
│   ├── README.md                   ← Data abstraction docs
│   ├── api-reference/              ← Auto-generated API docs
│   ├── usage-samples/              ← Code examples (no IP)
│   └── providers/
│       ├── sql-server/
│       ├── oracle/
│       └── ...
├── communication/                  ← Maps to src/Foundation/Communication/
├── identity/                       ← Maps to src/Foundation/Identity/
├── ai/                             ← Maps to src/Foundation/AI/
├── workflow/                       ← Maps to src/Foundation/Workflow/
├── ui-automation/                  ← Maps to src/Foundation/UIAutomation/
└── ui/                             ← Maps to src/Foundation/UI/
```

### **3. Solution Files Structure**

```
Foundation/Main/
├── Master.sln                      ← All projects
├── Foundation.sln                  ← All Foundation libraries
├── Foundation.DataAccess.sln       ← Data + 9 providers (category-specific)
├── Foundation.Communication.sln    ← ServiceModel + providers
├── Foundation.Identity.sln         ← Security + providers
├── Foundation.AI.sln               ← AI + providers
├── Foundation.Workflow.sln         ← Workflow + providers
├── Foundation.UIAutomation.sln     ← UIAutomation + providers
└── Foundation.UI.sln               ← UI framework
```

### **4. Test Structure** (`src/Foundation/Tests/`)

```
src/Foundation/Tests/
├── Core.Tests/
├── DataAccess/
│   ├── Data.Tests/
│   └── Providers/
│       ├── SqlServer.Tests/
│       ├── Oracle.Tests/
│       └── ...
├── Communication/
│   ├── ServiceModel.Tests/
│   └── Providers/
│       └── WCF.Tests/
└── ...
```

### **5. Samples Structure** (`src/Foundation/Samples/`)

```
src/Foundation/Samples/
├── Core/
│   └── BasicConsoleApp/
├── DataAccess/
│   ├── SqlServerSample/
│   ├── OracleSample/
│   └── MultiProviderSample/
├── Communication/
│   └── WcfServiceSample/
├── AI/
│   ├── OpenAISample/
│   └── LlamaCppSample/
└── ...
```

---

## 🔄 DevTools Post-Build Automation

### **Automated Tasks**

**1. API Reference Generation** (Post-Build)
- Extract XML documentation from compiled assemblies
- Generate Markdown API reference for each abstraction
- Place in `docs-portal/products/foundation/{category}/api-reference/`
- **IP Protection:** Only include public APIs, exclude internal implementations

**2. Usage Sample Extraction**
- Scan sample projects
- Extract usage patterns for public APIs
- Generate code snippets with syntax highlighting
- Place in `docs-portal/products/foundation/{category}/usage-samples/`

**3. Structure Validation**
- Verify folder structure matches pattern
- Check for missing documentation
- Validate category alignment across src/docs/tests/samples

**4. Reference Path Verification**
- Scan all `.csproj` files for `<ProjectReference>`
- Verify relative paths are correct after reorganization
- Generate report of any broken references

---

## 🛠️ DevTools Commands

### **Structure Validation**

```bash
# Dry-run: Verify structure compliance
devtools validate-structure --dry-run

# Output:
# ✅ src/Foundation/ structure matches pattern
# ✅ docs-portal/products/foundation/ structure matches pattern
# ❌ Missing: docs-portal/products/foundation/data-access/usage-samples/
# ✅ Test structure matches pattern
```

### **Documentation Sync**

```bash
# Sync API docs from compiled assemblies
devtools sync-docs --category DataAccess --dry-run

# Output:
# ✅ Would generate API docs for Invitrek.Foundation.Data.dll
# ✅ Would extract samples from DataAccess/Samples/
# ✅ Would update docs-portal/products/foundation/data-access/
```

### **Reference Validation**

```bash
# Verify all project references
devtools validate-references --dry-run

# Output:
# ✅ 50 project references validated
# ❌ Broken reference: src/DeveloperStudio/DevTools/DevTools.csproj
#    Reference: ..\..\Foundation\Data\Data.csproj
#    Should be: ..\..\Foundation\DataAccess\Data.csproj
```

---

## 📋 Reorganization Checklist

### **Phase 1: Physical Reorganization**

- [ ] Backup current structure (`git checkout -b backup/before-category-reorg`)
- [ ] Create category folders under `src/Foundation/`
- [ ] Move abstraction projects into category folders
- [ ] Move provider folders under category
- [ ] Update all `.csproj` `<ProjectReference>` paths
- [ ] Update solution files to reflect new paths

### **Phase 2: Documentation Reorganization**

- [ ] Create category folders under `docs-portal/products/foundation/`
- [ ] Move existing docs to category folders
- [ ] Run `devtools sync-docs --all --dry-run`
- [ ] Review generated structure
- [ ] Execute `devtools sync-docs --all`

### **Phase 3: Test & Sample Reorganization**

- [ ] Reorganize tests under category folders
- [ ] Reorganize samples under category folders
- [ ] Update test project references
- [ ] Update sample project references

### **Phase 4: Validation**

- [ ] Run `devtools validate-structure`
- [ ] Run `devtools validate-references`
- [ ] Build Foundation.sln
- [ ] Build category-specific solutions
- [ ] Run all tests

### **Phase 5: Commit**

- [ ] Commit only if all validations pass
- [ ] Update SOLUTION_STRUCTURE_VISUAL_PREVIEW.md
- [ ] Update ORGANIZATION_STRUCTURE_GUIDELINES.md
- [ ] Push to repository

---

## 🚨 Mandatory Rules

### **Rule 1: Category Alignment**

All artifacts for a framework MUST be in the same category:

```
✅ CORRECT:
src/Foundation/DataAccess/Data.csproj
src/Foundation/DataAccess/Providers/SqlServer/
src/Foundation/Tests/DataAccess/Data.Tests/
src/Foundation/Samples/DataAccess/SqlServerSample/
docs-portal/products/foundation/data-access/

❌ WRONG:
src/Foundation/Data/Data.csproj
src/Foundation/DataProviders/SqlServer/
src/Foundation/Tests/Data.Tests/
docs-portal/products/foundation/data/
```

### **Rule 2: No Orphaned Artifacts**

Every category folder in `src/Foundation/` MUST have corresponding folders in:
- `docs-portal/products/foundation/`
- `src/Foundation/Tests/`
- `src/Foundation/Samples/`

### **Rule 3: Automated Documentation**

Developers MUST NOT manually write API reference docs. Use `devtools sync-docs` to auto-generate from XML comments.

### **Rule 4: IP Protection**

Public documentation MUST NOT reveal:
- Internal implementation details
- Proprietary algorithms
- Performance optimization strategies
- Trade secrets

Use `internal` and `private` access modifiers aggressively. Only document public APIs.

---

## 📊 Verification Matrix

| Artifact Type | Location | Pattern | Auto-Sync |
|---------------|----------|---------|-----------|
| **Abstraction** | `src/Foundation/{Category}/{Abstraction}.csproj` | ✅ | N/A |
| **Providers** | `src/Foundation/{Category}/Providers/{Provider}/` | ✅ | N/A |
| **API Docs** | `docs-portal/products/foundation/{category}/api-reference/` | ✅ | ✅ Post-build |
| **Usage Samples** | `docs-portal/products/foundation/{category}/usage-samples/` | ✅ | ✅ Post-build |
| **Tests** | `src/Foundation/Tests/{Category}/` | ✅ | N/A |
| **Samples** | `src/Foundation/Samples/{Category}/` | ✅ | N/A |
| **Solution Files** | `Foundation.{Category}.sln` | ✅ | ✅ On demand |

---

## 🔗 Related Documentation

- `SOLUTION_STRUCTURE_VISUAL_PREVIEW.md` - Complete structure visualization
- `docs/PROJECT_NAMING_CONVENTION.md` - Naming rules
- `docs/ARCHITECTURE_GUIDELINES_SHARED_PROJECT_PATTERN.md` - Shared project pattern
- `SESSION_29_REORGANIZATION_PLAN.md` - Execution plan

---

**Document Version:** 1.0  
**Status:** 🚀 Active Policy  
**Owner:** Architecture Team  

**© 2024-2026 Invitrek Software Technologies. All rights reserved.**
