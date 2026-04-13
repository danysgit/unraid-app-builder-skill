# Unraid App Builder for Codex

<p align="center">
  <img src="./assets/unraid-logo.jpg" alt="Unraid" width="120" />
  &nbsp;&nbsp;&nbsp;
  <img src="./assets/codex-logo.png" alt="Codex" width="120" />
</p>

`unraid-app-builder` is a Codex skill for packaging self-hosted apps for Unraid with better first-pass results.

This repo now stands on its own, so generated Unraid templates should live under `templates/` in this repository.

It helps Codex produce:

- Unraid-friendly Docker images
- a canonical manual `my-...xml` user-template
- WebUI, icon, support, and project metadata
- editable host port and appdata mappings
- validation steps that check whether user changes persist across install and update flows

## What It Covers

- use `/config` as the persistent appdata root
- support `PUID`, `PGID`, and `UMASK` when the app writes files
- expose the right-click Unraid WebUI action correctly
- include stable icon, support, project, registry, and template links
- avoid dockerMan base-template mismatches by favoring a single canonical `my-...xml` path
- validate real Unraid installs, edits, and updates before shipping

## Install

Clone the repo directly into your Codex skills directory:

```bash
git clone https://github.com/danysgit/unraid-app-builder-skill.git "${CODEX_HOME:-$HOME/.codex}/skills/unraid-app-builder"
```

If you already have a local copy there, remove it first or clone to a temporary folder and replace it manually.

## Use

Invoke it in Codex with:

```text
$unraid-app-builder
```

Example prompts:

- `Use $unraid-app-builder to package this app for Unraid.`
- `Use $unraid-app-builder to review this Docker app's Unraid template and fix integration issues.`
- `Use $unraid-app-builder to add a proper Unraid manual user-template, icon, WebUI link, and validation checklist.`

## Repo Layout

- [SKILL.md](./SKILL.md): main skill instructions
- [agents/openai.yaml](./agents/openai.yaml): Codex UI metadata
- [templates/](./templates): canonical location for generated `my-...xml` Unraid templates
- [references/unraid-packaging.md](./references/unraid-packaging.md): Unraid-specific guidance and validation notes

## License

[MIT](./LICENSE)
