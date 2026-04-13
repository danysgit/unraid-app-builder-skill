# Unraid Packaging Notes

## Goals

Package the app so it feels native in Unraid:

- recognizable icon
- working WebUI shortcut from the Docker context menu
- support and project links
- editable host port
- editable appdata path
- user changes that survive reinstall and update flows

## Canonical Template Strategy

For manual repo installs, publish one template only:

- `unraid/manual/my-<AppName>.xml`

Why:

- Unraid stores local templates under `/boot/config/plugins/dockerMan/templates-user/`
- dockerMan can behave differently when both a base template and a saved `my-...` template compete
- a single canonical `my-` template reduces mismatch risk

When testing on a live server, inspect:

- the saved local template file
- the running container’s port bindings and mounts

Do not assume the `Update Container` screen is always authoritative.

## Template Structure

The template should include:

1. top-level metadata for icon, support, project, and WebUI
2. legacy `Networking`, `Data`, and `Environment` sections
3. `Config` entries for user-editable fields

Use the legacy and v2 sections together for maximum dockerMan compatibility.

## High-Value Template Fields

- `WebUI`: use the internal container port placeholder form such as `http://[IP]:[PORT:3000]/`
- `Icon`: point to a stable raw image URL
- `Support`: issues/support page
- `Project`: main repo or site
- `Repository`: published image such as `ghcr.io/org/app:latest`
- `Registry`: package or registry page

## Required User-Editable Configs

### WebUI Port

- `Type="Port"`
- `Target` should be the internal container port
- description should explain this is the host-side web port used by Unraid’s WebUI shortcut

### AppData

- `Type="Path"`
- `Target="/config"`
- default to a normal Unraid appdata path, but let the user change it

## Container Conventions

- keep app state under `/config`
- support `PUID`, `PGID`, and `UMASK` if files are written
- persist generated secrets under appdata
- provide a simple `/api/health` or equivalent if possible

## Validation Commands

On a real Unraid host, these checks are useful after install:

```bash
docker inspect <container-name> --format '{{json .HostConfig.PortBindings}}'
docker inspect <container-name> --format '{{range .Mounts}}{{println .Source " -> " .Destination}}{{end}}'
grep -n -E 'Name="WebUI Port"|Name="AppData"' /boot/config/plugins/dockerMan/templates-user/my-<AppName>.xml
```

## Practical Lessons Learned

- The Docker list may show the internal container port such as `3000:TCP`; that alone is not the host port bug.
- The clickable host address in the Docker list is the external value that matters.
- If dockerMan’s edit screen shows defaults that disagree with the live container, verify the local template files before changing the app.
- Recreate the container through the Unraid UI before concluding the template is broken if you previously created it via raw `docker run`.

## Official Guidance To Recheck When Needed

Unraid behavior changes over time. When packaging a new app or if dockerMan behavior seems different, browse current Unraid documentation and release notes before assuming the old workaround still applies.

Relevant official sources:

- Unraid docs and release notes: https://docs.unraid.net/
- Community Applications / forums for dockerMan behavior changes: https://forums.unraid.net/
