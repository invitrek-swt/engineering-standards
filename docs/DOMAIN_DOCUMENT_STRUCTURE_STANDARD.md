# Domain Document Structure Standard

## Standard
- Maintain **one canonical top-level document per domain**.
- Group **individual projects/components** under area-specific subfolders.
- Documentation structure must **mirror source structure** at Domain/Area/Project levels.

## Required pattern
- Domain top-level (single canonical doc):
  - `docs/<Domain>/DOMAIN.md`
- Domain must index all `<AreaOrGroup>` entries with links.
- Project/component docs grouped by area/group:
  - `docs/<Domain>/<AreaOrGroup>/INDEX.md` (lists all related projects/components)
  - `docs/<Domain>/<AreaOrGroup>/<ProjectOrComponent>/REQUIREMENTS.md`
  - `docs/<Domain>/<AreaOrGroup>/<ProjectOrComponent>/DESIGN.md`
  - `docs/<Domain>/<AreaOrGroup>/<ProjectOrComponent>/API.md` (or `API_CONTRACTS.md` where needed)

## Canonical grouping format
- `docs/<Domain>/<AreaOrGroup>/<ProjectOrComponent>`

## Source-to-Docs mirroring rule
- Source: `src/<Domain>/<AreaOrGroup>/<ProjectOrComponent>`
- Docs: `docs/<Domain>/<AreaOrGroup>/<ProjectOrComponent>`
- Keep Domain/Area/Project folder naming aligned between `src` and `docs`.

## Indexing requirements
- `<AreaOrGroup>/INDEX.md` must:
  - List all related projects/components.
  - Provide links/references to each project's `REQUIREMENTS.md`, `DESIGN.md`, and API doc.
- `<Domain>/DOMAIN.md` must:
  - List all areas/groups in the domain.
  - Link to each `<AreaOrGroup>/INDEX.md`.

## Domain examples
- `docs/Foundation/DOMAIN.md`
- `docs/DeveloperStudio/DOMAIN.md`
- `docs/Nexus/DOMAIN.md`

## Notes
- Avoid multiple competing top-level docs for the same domain.
- Keep the domain top-level doc as the canonical index + status summary.
- UIFramework remains grouped under Foundation.
- Shared is metadata/signing-oriented and may be excluded from implementation tracking.
