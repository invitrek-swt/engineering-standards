# Supported Target Frameworks Manifest

Purpose
-------
This document defines the repository-level manifest and guidance for declaring which target frameworks a repository or shared project supports. Tools and generator templates should read this manifest to emit conditional compilation symbols and to validate generated code.

Manifest location
-----------------
- Per-repo manifest: `engineering-standards/supported-targets.json`
- Central (master) manifest: `https://github.com/invitrek-swt/engineering-standards` (master copy)

Example manifest (`engineering-standards/supported-targets.json`)

{
  "repository": "Invitrek.Foundation",
  "supportedTargets": [
    {
      "tfm": "net48",
      "symbol": "NET48",
      "notes": "Legacy .NET Framework baseline"
    },
    {
      "tfm": "net10.0",
      "symbol": "NET10_0_OR_GREATER",
      "notes": "Primary modern target; includes net10+ family"
    }
  ]
}

Guidance
--------
- Generators and templates MUST read the repo-level `engineering-standards/supported-targets.json` when producing conditional code.
- Do not hardcode conditional symbols in templates; use the manifest values.
- When supporting multiple TFMs, favor explicit compile-time checks using the manifest symbol.

Maintenance
-----------
- Update the repo manifest when supported targets change. The central master copy in `invitrek-swt/engineering-standards` should be authoritative; use `tools/SyncStandards/SyncStandards` to push changes to domain repos.
