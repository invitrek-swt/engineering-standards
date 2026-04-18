# Invitrek-swt Engineering Standards

**Organization:** Invitrek-swt  
**Repo:** `invitrek-swt/engineering-standards`  
**Purpose:** Single source of truth for all organization-wide engineering standards, policies, config baselines, and templates. All domain repos sync from here.

---

## How to Use

### Authoritative local source (Foundation solution)

The `engineering-standards/` folder in the Foundation solution (`C:\repos\invitrek\Foundation\Main\engineering-standards\`) is the **live authoritative copy** of all org-wide standards. Edit files here first, then push to GitHub and sync to domain repos.

GitHub Copilot is configured to follow rules from this folder directly — no sync step needed within the Foundation solution itself.

### Sync standards into another repo

**`devtools --sync-standards`** is the canonical command — it is part of the DevTools CLI (single source of truth for all Copilot/team automation).

**Preferred (DevTools CLI, local source — fastest, works offline):**
```
devtools --sync-standards --target=C:\repos\<domain-repo>
```

**DevTools CLI, GitHub source (external repos without access to this solution):**
```
devtools --sync-standards --source=github --target=C:\repos\<domain-repo>
```

**Bootstrap shim (before DevTools is built — CI first-run):**
```
dotnet run --project tools/SyncStandards/SyncStandards.csproj -- --target=C:\repos\<domain-repo>
```
The shim auto-forwards to `devtools --sync-standards` (or `dotnet run DevTools.CLI`) internally.

Options:
- `--source=local` (default) — copy from local `engineering-standards/` folder
- `--source=github` — download from `raw.githubusercontent.com`
- `--target=<path>` — destination repo root (defaults to current directory)
- `--standards-dir=<path>` — override local standards folder path
- `--org=Invitrek-swt`
- `--repo=engineering-standards`
- `--branch=main`
- `--force` — overwrite even if content is unchanged

### What gets synced into your repo

| Source (this repo) | Destination (your repo) |
|--------------------|------------------------|
| `config/git/.gitignore` | `.gitignore` |
| `config/git/.gitattributes` | `.gitattributes` |
| `config/dotnet/.editorconfig` | `.editorconfig` |
| `config/dotnet/global.json` | `global.json` |
| `config/dotnet/Directory.Build.props` | `Directory.Build.props` |
| `templates/docs/Standards.md` | `docs/Standards.md` |
| `config/github/copilot-instructions.md` | `.github/copilot-instructions.md` |
| `docs/coding-standards.md` | `docs/standards/coding-standards.md` |
| `docs/architecture-guidelines.md` | `docs/standards/architecture-guidelines.md` |
| `docs/foundation-library-index.md` | `docs/standards/foundation-library-index.md` |
| `docs/sdlc-policy.md` | `docs/standards/sdlc-policy.md` |
| `docs/quality-standards.md` | `docs/standards/quality-standards.md` |
| `docs/project-template-defaults.md` | `docs/standards/project-template-defaults.md` |

### When to re-sync

- When this repo is updated (standards change, new policy)
- When onboarding a new repo to the org
- On a scheduled basis (e.g., start of sprint/quarter)

---

## Repository Structure

```
engineering-standards/
├── README.md                              ← this file
│
├── config/
│   ├── git/
│   │   ├── .gitignore                     ← canonical org .gitignore
│   │   └── .gitattributes                 ← canonical org .gitattributes
│   ├── dotnet/
│   │   ├── .editorconfig                  ← canonical C# editor config
│   │   ├── global.json                    ← canonical SDK version pin
│   │   └── Directory.Build.props          ← canonical MSBuild defaults
│   └── github/
│       └── copilot-instructions.md        ← canonical Copilot/AI instructions
│
├── docs/
│   ├── Standards.md                       ← summary + links (human entry point)
│   ├── coding-standards.md                ← C#/TS style, SOLID, DRY, patterns, automation
│   ├── architecture-guidelines.md         ← solution structure, layering, naming, DI
│   ├── foundation-library-index.md        ← Foundation package index, API samples, dependency map
│   ├── api-guidelines.md                  ← API-first, contracts, async, DataContract
│   ├── security-guidelines.md             ← security standards, threat modeling, CVE policy
│   ├── sdlc-policy.md                     ← requirements → design → impl → delivery workflow
│   ├── quality-standards.md               ← quality gates, compliance checklist, bug tolerance
│   ├── project-template-defaults.md       ← .csproj defaults, naming conventions, targets
│   ├── domain-document-structure.md       ← docs/ mirror-src/ standard, index conventions
│   └── wpf-standards.md                   ← WPF control authoring, theming, naming
│
└── templates/
    ├── docs/
    │   └── Standards.md                   ← template synced to docs/Standards.md in each repo
    └── projects/
        ├── ProjectTemplate.Library.csproj
        └── ProjectTemplate.Tests.csproj
```

---

## Platform Libraries

**Foundation** and **UIFramework** are the two sub-families of the organization's shared platform libraries, kept in the same solution but strictly separated so non-UI server-side projects have no UI dependencies.

| Sub-family | Packages | Role | Distribution |
|------------|----------|------|--------------|
| **Foundation (Non-UI)** | `Invitrek.Foundation.*` | Pure class libraries — cross-cutting concerns, CLI, DI, CodeModel, mapping, data access, service model, security, workflow, AI | Direct project reference (pre-MVP) → NuGet (post-MVP) |
| **UIFramework** | `Invitrek.Foundation.UI`, `Invitrek.Foundation.Mvvm.*`, `Invitrek.Foundation.Presentation.*`, `Invitrek.Foundation.TaskDialogs`, etc. | Enterprise UI controls (WPF-first today), MVVM base classes, docking, code editor, design canvas, dialogs. Android / iOS / Blazor platforms planned. | Direct project reference (pre-MVP) → NuGet (post-MVP) |

See `docs/architecture-guidelines.md §11` and [`docs/foundation-library-index.md`](docs/foundation-library-index.md) for the full package index with API samples.

---

## Governance

- All org-wide rule changes must be made **here first**, then synced to repos.
- Domain-specific rules stay in their respective domain repos — do **not** add domain-specific content here.
- Changes should be reviewed via PR against `main`.
- Breaking changes (e.g., removal of a config key, rename of a file) must be announced before merging.

---

## Links

- Org: https://github.com/Invitrek-swt
- Foundation (master solution): https://github.com/Invitrek-swt/Foundation
