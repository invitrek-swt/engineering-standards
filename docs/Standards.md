# Engineering Standards — Invitrek-swt

This repository defines mandatory organization-wide engineering standards for all repositories that follow Invitrek-swt practices.

## Canonical standard documents

| Document | Topic |
|----------|-------|
| [coding-standards.md](coding-standards.md) | C# style, SOLID, DRY, automation policy |
| [architecture-guidelines.md](architecture-guidelines.md) | Solution structure, layering, naming, DI |
| [foundation-library-index.md](foundation-library-index.md) | Foundation package index, API samples, dependency map |
| [api-guidelines.md](api-guidelines.md) | API-first, contracts, async, DataContract |
| [security-guidelines.md](security-guidelines.md) | Security standards, threat modeling, CVE policy |
| [sdlc-policy.md](sdlc-policy.md) | Requirements → design → implementation → delivery |
| [quality-standards.md](quality-standards.md) | Quality gates, compliance checklist, bug/CVE tolerance |
| [project-template-defaults.md](project-template-defaults.md) | .csproj defaults, naming, multi-targeting |
| [domain-document-structure.md](domain-document-structure.md) | docs/ structure standard |
| [wpf-standards.md](wpf-standards.md) | WPF control authoring, theming, naming conventions |

## Canonical configuration

- `.editorconfig` — C# coding style
- `global.json` — SDK version pin
- `Directory.Build.props` — MSBuild defaults
- `.gitignore` / `.gitattributes` — Git config
- `.github/copilot-instructions.md` — Copilot/AI guidance

## Sync

This file and all standards are synced into domain repos via `SyncStandards`.  
Do not edit local copies — update here and re-sync.

**Canonical standards repo:** https://github.com/Invitrek-swt/engineering-standards
