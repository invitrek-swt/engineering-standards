# Domain Document Structure Standard — Invitrek-swt

**Applies to:** All repositories  
**Rule:** Documentation structure must mirror source structure at Domain/Area/Project levels.

---

## 1. Required Pattern

```
docs/
└── <Domain>/
    ├── INDEX.md                              ← domain index (lists all areas + projects)
    ├── ROADMAP.md                            ← domain-specific planned/carry-forward items
    ├── Standards.md                          ← synced from engineering-standards (read-only)
    ├── session/
    │   ├── SESSION_TRACKER.md                ← incremental session log
    │   ├── DECISIONS.md                      ← architecture decision records
    │   └── TODO.md                           ← backlog + resume point
    └── <AreaOrGroup>/
        ├── INDEX.md                          ← area index (lists all projects/components)
        └── <ProjectOrComponent>/
            ├── REQUIREMENTS.md               ← SRS / requirements
            └── DESIGN.md                     ← design document
```

- Domain top-level must have an `INDEX.md` listing all areas with links.
- Each `<AreaOrGroup>` must have an `INDEX.md` listing all related projects/components with links.
- Each project/component documentation lives in its own subfolder.

---

## 2. Session Documents

| File | Purpose | Update frequency |
|------|---------|-----------------|
| `session/SESSION_TRACKER.md` | Per-session build status and change log | After each meaningful change |
| `session/DECISIONS.md` | Architecture decision records (ADRs) | When decisions are made |
| `session/TODO.md` | Outstanding tasks and current resume point | End of each session |

---

## 3. Synced Files (Read-Only in Domain Repos)

The following files are synced from `invitrek-swt/engineering-standards` and must **not** be manually edited in domain repos:

```
docs/Standards.md
docs/standards/coding-standards.md
docs/standards/architecture-guidelines.md
docs/standards/sdlc-policy.md
docs/standards/quality-standards.md
docs/standards/project-template-defaults.md
.editorconfig
global.json
Directory.Build.props
.gitignore
.gitattributes
.github/copilot-instructions.md
```

To update these, edit the canonical `invitrek-swt/engineering-standards` repo and re-run `SyncStandards`.

---

## 4. Domain-Specific Content (Stays in Domain Repo)

All of the following remain in the domain repo and are **not** part of the sync:

- SRS documents (`REQUIREMENTS.md`, `DESIGN.md`)
- Session tracker, decisions, TODO
- Domain roadmap (`ROADMAP.md`)
- Domain-specific SRS files (e.g., `SRS_HTML5_Visual_Designer.md`)
- Domain INDEX files

---

## 5. Prohibited Patterns

- Do **not** place documentation under `docs/src/...` (do not mirror source path).
- Do **not** create a `Cli` subfolder under `CommandLine` — `CommandLine` is the folder root.
- Do **not** mix domain-specific content with synced org-wide content.
- Do **not** nest documentation more than 3 levels deep (Domain → Area → Project).
