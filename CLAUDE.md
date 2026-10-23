# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A Home Assistant add-on that packages the **EMQX Enterprise** MQTT broker, forked from the
frenck/hassio-addons community add-on. It is not an application with its own source code — it is a
container image definition (`Dockerfile` + `build.yaml`) plus an **s6-overlay** service tree
(`emqx/rootfs/`) that configures and runs the upstream EMQX binary. The HA Supervisor builds and
runs the image; there is no local compile/lint/test step and no CI build workflow in this repo.

## Two parallel version lines (important)

The two long-lived branches track two different EMQX major versions. Keep both maintained; do not
collapse one into the other.

- **`main`** → EMQX **5.x LTS** (currently 5.10.5; 5.10 is the final 5.x minor). The stable release.
- **`feature/emqx-6.x`** → EMQX **6.x LTS** (currently 6.3.2). Labelled "(Preview)" in
  `repository.json`. EMQX 6.x introduces a new durable-storage engine, so 5.x durable session state
  is reset on upgrade — this is why 6.x is a separate preview line rather than a bump on `main`.

The two branches' `Dockerfile`s download EMQX from **different sources**:
- 6.x: `https://packages.emqx.io/emqx-ee/${EMQX_VERSION}/emqx-enterprise-${EMQX_VERSION}-debian13-${ARCH}.tar.gz`
- 5.x: `https://github.com/emqx/emqx/releases/download/e${EMQX_VERSION}/...` (note the `e` tag prefix)

## Runtime architecture (s6-overlay)

Config flows one direction: **HA add-on options → bashio → `EMQX_*` env vars → EMQX HOCON config.**

1. `emqx/config.yaml` declares the add-on options and their `schema` (the HA UI form).
2. `emqx/rootfs/etc/s6-overlay/s6-rc.d/init-emqx/run` (oneshot) creates the persistent dirs under
   `/data/emqx/{data,etc,log,plugins}` and records the EMQX version in `/data/emqx/.version` to
   detect 5.x→6.x upgrades.
3. `emqx/rootfs/etc/s6-overlay/s6-rc.d/emqx/run` (longrun) reads each option with `bashio::config`
   and exports it as an `EMQX_*` env var, then `exec`s `/opt/emqx/bin/emqx foreground`.

The `EMQX_*` names use EMQX's HOCON convention where `__` = nesting (e.g.
`EMQX_LISTENERS__TCP__DEFAULT__BIND`). The `env_vars` option lets users set any `EMQX_*` var
directly; the schema restricts names to `^EMQX_([A-Z0-9_])+$`. Runtime state lives in `/data`
(persistent) and TLS certs are read from the mapped `/ssl` share.

## Releasing / bumping versions

When bumping EMQX or the base image, follow the repo's existing release convention (see `git log`):

- Update these files **together** so the add-on version stays consistent:
  - `emqx/Dockerfile` — `ARG EMQX_VERSION` and/or `ARG BUILD_FROM`
  - `emqx/build.yaml` — `build_from` for both `aarch64` and `amd64`
  - `emqx/config.yaml` — `version` and `description`
  - `repository.json` — `name`
  - (6.x only) `emqx/rootfs/.../init-emqx/run` — `CURRENT_VERSION`, used for upgrade detection
- Bump the add-on revision suffix (`-N`, e.g. `6.3.2-0` → `6.3.2-1`) **even for a base-image-only
  change**, so Home Assistant detects an update.
- Commit message format: `release: <version>-<rev> - <short description>`.

Before changing a version, confirm the artifact exists: the EMQX release tarball URL (curl `-I`)
and, for base image bumps, the `ghcr.io/hassio-addons/debian-base:<tag>` manifest.

## Automation

- `.github/workflows/check-upstream-updates.yaml` — runs weekly, compares the `EMQX_VERSION` and
  `BUILD_FROM` in `emqx/Dockerfile` against the latest upstream EMQX (for the current major) and
  latest `debian-base` release, and opens a GitHub issue when a newer version exists.
- `.github/renovate.json` — Renovate dependency updates.
