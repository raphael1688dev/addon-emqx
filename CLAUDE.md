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

- **`main`** → EMQX **5.x LTS** (5.10 is the final 5.x minor). The stable release.
- **`feature/emqx-6.x`** → EMQX **6.x LTS**. Labelled "(Preview)" in
  `repository.json`. EMQX 6.x introduces a new durable-storage engine, so 5.x durable session state
  is reset on upgrade — this is why 6.x is a separate preview line rather than a bump on `main`.

Each branch's current EMQX version is `ARG EMQX_VERSION` in its `emqx/Dockerfile`.

The two branches' `Dockerfile`s download EMQX from **different sources**:
- 6.x: `https://packages.emqx.io/emqx-ee/${EMQX_VERSION}/emqx-enterprise-${EMQX_VERSION}-debian13-${ARCH}.tar.gz`
- 5.x: `https://github.com/emqx/emqx/releases/download/e${EMQX_VERSION}/...` (note the `e` tag prefix)

## Runtime architecture (s6-overlay)

Config flows one direction: **HA add-on options → bashio → `EMQX_*` env vars → EMQX HOCON config.**

1. `emqx/config.yaml` declares the add-on options and their `schema` (the HA UI form).
2. `emqx/rootfs/etc/s6-overlay/s6-rc.d/init-emqx/run` (oneshot) creates the persistent dirs under
   `/data/emqx/{data,etc,log,plugins}` and records the EMQX version in `/data/emqx/.version`.
   Both branches write the file; the 6.x branch reads it to detect a 5.x→6.x upgrade.
3. `emqx/rootfs/etc/s6-overlay/s6-rc.d/emqx/run` (longrun) reads each option with `bashio::config`
   and exports it as an `EMQX_*` env var, then `exec`s `/opt/emqx/bin/emqx foreground`. It also
   draws a random Erlang node cookie once per installation and keeps it in
   `/data/emqx/node.cookie` (the add-on uses host networking, so the cookie must not be guessable).
   Values of `env_vars` entries are never logged, only their names.

The `EMQX_*` names use EMQX's HOCON convention where `__` = nesting (e.g.
`EMQX_LISTENERS__TCP__DEFAULT__BIND`). The `env_vars` option lets users set any `EMQX_*` var
directly; the schema restricts names to `^EMQX_([A-Z0-9_])+$`. Runtime state lives in `/data`
(persistent) and TLS certs are read from the mapped `/ssl` share.

## Releasing / bumping versions

Every release follows the repo's existing convention (see `git log`), whether it bumps EMQX or the
base image or only changes the add-on itself:

- Update these files **together** so the add-on version stays consistent:
  - `emqx/Dockerfile` — `ARG EMQX_VERSION` and/or `ARG BUILD_FROM`
  - `emqx/build.yaml` — `build_from` for both `aarch64` and `amd64`
  - `emqx/config.yaml` — `version` and `description`
  - `repository.json` — `name`
  - `emqx/rootfs/.../init-emqx/run` — `CURRENT_VERSION` (EMQX version only, no `-N` suffix), used
    for upgrade detection
  - `README.md` — the EMQX version and `debian-base` tag under "Key Features & Enhancements"
  - `CHANGELOG.md` — add an entry under the matching version line (6.x releases under the 6.x
    section, 5.x under the 5.x section). Like this file, `CHANGELOG.md` is kept identical on both
    branches, so the new entry is added on whichever branch you release and then carried to the other.
- Bump the add-on revision suffix (`-N`, e.g. `6.3.2-0` → `6.3.2-1`) **even for a base-image-only
  change**, so Home Assistant detects an update.
- Commit message format: `release: <version>-<rev> - <short description>`.

Before changing a version, confirm the artifact exists: the EMQX release tarball URL (curl `-I`)
and, for base image bumps, the `ghcr.io/hassio-addons/debian-base:<tag>` manifest.

## Automation

- `.github/workflows/check-upstream-updates.yaml` — runs weekly and checks both version lines: for
  each of `main` and `feature/emqx-6.x` it compares that branch's `EMQX_VERSION` and `BUILD_FROM`
  in `emqx/Dockerfile` against the latest upstream EMQX for its major and the latest `debian-base`
  release. Scheduled runs only start from `main`, so the workflow checks out each branch itself;
  keep the file identical on both branches. When a newer version exists the run fails, so GitHub
  sends its failure notification, and it also opens an issue if GitHub Issues are enabled.
- `.github/renovate.json` — Renovate config (regex managers for `BUILD_FROM`, the `build.yaml`
  images and `EMQX_VERSION`). It only acts if the Renovate app is installed on the repository;
  don't count on it to surface updates.
