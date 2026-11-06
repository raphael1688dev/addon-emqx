# Changelog

All notable changes to this Home Assistant add-on are documented here.
The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/)
and the add-on uses `<emqx-version>-<revision>` versioning.

This add-on is maintained as **two parallel version lines** (see `CLAUDE.md`):

- **5.x line** — tracked on `main`, the stable release (EMQX 5.x LTS).
- **6.x line** — tracked on `feature/emqx-6.x`, labelled "(Preview)" (EMQX 6.x LTS).

Both lines share the `ghcr.io/hassio-addons/debian-base` base image.

## 6.x line (`feature/emqx-6.x`, Preview)

### 6.3.2-4 — 2026-11-06
- Raise the EMQX Retainer delivery and publish rate limits to `100000/s`
  (upstream EMQX defaults both to `1000/s`). At the upstream default, a wildcard
  subscription matching many retained messages (e.g. Home Assistant discovery)
  could exceed the limit and have retained messages dropped on delivery. Add
  `retainer_delivery_rate` and `retainer_max_publish_rate` options to override;
  the deviation from the EMQX defaults is documented and noted in the service
  script.

### 6.3.2-3 — 2026-11-06
- Default the EMQX Retainer storage backend to `disc` (upstream EMQX defaults to
  `ram`). Retained messages now persist across broker restarts instead of being
  lost in memory. Add a `retainer_storage_type` option (`disc` | `ram`) to
  override; the deviation from the EMQX default is documented in DOCS/README and
  noted in the service script.

### 6.3.2-2 — 2026-11-05
- Upgrade base image to `debian-base` 9.5.2.

### 6.3.2-1 — 2026-10-23
- Upgrade base image to `debian-base` 9.5.1.
- Add `CLAUDE.md` with architecture and release guidance.
- Update README to EMQX 6.3.2 and `debian-base` 9.5.1.

### 6.3.2-0 — 2026-10-23
- Upgrade EMQX to 6.3.2 (Enterprise 6.x LTS).
- Clean up image metadata and labels.

### 6.3.1 — 2026-10-11
- Introduce the 6.x line: adapt the add-on to EMQX Enterprise 6.3.1 LTS.
- EMQX 6.x downloads from `packages.emqx.io/emqx-ee/`.
- Note: EMQX 6.x uses a new durable-storage engine, so 5.x durable session
  state is reset on upgrade — this line is kept as a separate preview.

## 5.x line (`main`, stable)

### 5.10.5-5 — 2026-11-06
- Default the EMQX Retainer storage backend to `disc` (upstream EMQX defaults to
  `ram`). Retained messages now persist across broker restarts instead of being
  lost in memory. Add a `retainer_storage_type` option (`disc` | `ram`) to
  override; the deviation from the EMQX default is documented in DOCS/README and
  noted in the service script.

### 5.10.5-4 — 2026-11-05
- Upgrade base image to `debian-base` 9.5.2.

### 5.10.5-3 — 2026-10-23
- Upgrade base image to `debian-base` 9.5.1.
- Add `CLAUDE.md` with architecture and release guidance.
- Update README base image reference.

### 5.10.5-2 — 2026-10-11
- Add SSL certificate, telemetry toggle, and max packet size UI options.

### 5.10.5-1 — 2026-10-11
- Upgrade base image to `debian-base` 9.5.0.

### 5.10.5 — 2026-10-11
- Upgrade EMQX to 5.10.5 (final 5.10 minor of the 5.x LTS line).

### 5.10.4-1 — 2026-10-11
- Add log persistence, Dockerfile tuning, and the UI configuration schema.

### 5.10.3-1 — 2026-06-02
- Upgrade base image to `debian-base` 9.3.0.

### Earlier (inherited from the upstream fork)
- 5.10.4 (2026-06-13), 5.10.2 (2025-11-13), 5.10.1 (2025-10-25) — EMQX version
  bumps carried over from the frenck/hassio-addons community add-on this
  project was forked from.

## Shared changes

- **2026-10-23** — Added `CLAUDE.md` to both lines.
- **2026-10-11** — Added Renovate config and a weekly upstream-release
  monitoring workflow (`.github/workflows/check-upstream-updates.yaml`).
