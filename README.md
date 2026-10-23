# Home Assistant Community Add-on: EMQX

[![GitHub Release][releases-shield]][releases]
![Project Stage][project-stage-shield]
[![License][license-shield]](LICENSE.md)

![Supports aarch64 Architecture][aarch64-shield]
![Supports amd64 Architecture][amd64-shield]




[![Github Actions][github-actions-shield]][github-actions]
![Project Maintenance][maintenance-shield]
[![GitHub Activity][commits-shield]][commits]

[![Discord][discord-shield]][discord]
[![Community Forum][forum-shield]][forum]

[![Sponsor Frenck via GitHub Sponsors][github-sponsors-shield]][github-sponsors]

[![Support Frenck on Patreon][patreon-shield]][patreon]

The most scalable open-source MQTT broker for IoT, IIoT, and connected vehicles.

![EMQX in the Home Assistant Frontend](images/screenshot.png)

## About

[EMQX][emqx] is an Open-source MQTT broker with a high-performance real-time
message processing engine, powering event streaming for IoT devices at massive
scale. As the most scalable MQTT broker, EMQX can help you connect any device,
at any scale (including your home).

The [EMQX MQTT broker][emqx] is an advanced alternative to the Mosquitto MQTT
broker/add-on that is generally used in Home Assistant. It has a UI
to configure, manage, and debug your MQTT broker, clients, and traffic.

While EMQX sells their product mainly as a cloud hosted product on their
website, this add-on runs EMQX in a fully local, self-hosted environment.

[:books: Read the full add-on documentation][docs]

## Key Features & Enhancements

This fork of the EMQX Add-on provides an upgraded, reliable, and production-ready experience:

- **EMQX Enterprise 6.3.2 (Latest LTS Release)**:
  - Long-Term Support (LTS) release maintained through September 2029.
  - Full BSL 1.1 enterprise feature suite unlocked: high-throughput MQTT Streams, Apache Parquet data sinks, and native Durable Storage engine.
  - Advanced Feature Gates and hardened security with a PCRE2-powered Rule Engine.
- **Latest Debian 13 Base Image**:
  - Built on `ghcr.io/hassio-addons/debian-base:9.5.1` (Debian 13.7 Trixie).
  - Uses S6-Overlay `3.2.3.0` and the latest `bashio` library for enhanced supervisor compatibility.
- **Persistent Log Storage**:
  - Automatically redirects and stores EMQX logs under `/data/emqx/log`, ensuring logs survive container restarts and add-on updates.
- **Home Assistant Configuration UI**:
  - Tune standard ports (`mqtt_port`, `mqtts_port`, `websocket_port`, `wss_port`) and `log_level` directly from the Home Assistant add-on configuration tab.
  - Securely bootstrap the Dashboard admin password via a masked password field.
- **SSL / TLS Certificate Integration**:
  - Direct integration with Home Assistant SSL certificates (e.g., Let's Encrypt fullchain and private key via `/ssl`).
  - Safe existence verification: Gracefully falls back to self-signed test certificates if custom certificates are not found on disk, preventing startup failures.
- **Privacy & Traffic Tuning**:
  - Built-in `telemetry_enable` toggle (defaults to `false` for complete privacy).
  - Configurable `max_packet_size` to handle large payloads or IoT telemetry streams.
- **Advanced Environment Variable Overrides**:
  - Fully supports overriding any EMQX HOCON configuration parameter via the `env_vars` option.

## Configuration

You can configure the EMQX add-on directly from the Home Assistant Configuration tab or via YAML.

**Note**: *Remember to restart the add-on when configuration changes are made.*

### Example Configuration

```yaml
default_admin_password: "mySecretPassword"
mqtt_port: 1883
mqtts_port: 8883
websocket_port: 8083
wss_port: 8084
log_level: warning
ssl_certfile: "/ssl/fullchain.pem"
ssl_keyfile: "/ssl/privkey.pem"
ssl_cacertfile: ""
telemetry_enable: false
max_packet_size: "1MB"
env_vars: []
```

### Configuration Options

- **`default_admin_password`** *(optional)*: Bootstraps the initial password for the EMQX Dashboard `admin` account.
- **`mqtt_port`**: TCP port for standard MQTT traffic (default: `1883`).
- **`mqtts_port`**: TCP port for encrypted MQTT SSL/TLS traffic (default: `8883`).
- **`websocket_port`**: TCP port for MQTT over WebSocket (default: `8083`).
- **`wss_port`**: TCP port for MQTT over secure WebSocket (default: `8084`).
- **`log_level`**: Console log level for EMQX (`debug`, `info`, `warning`, `error`, `critical`). Default: `warning`.
- **`ssl_certfile`** *(optional)*: Path to the SSL certificate or fullchain file (e.g., `/ssl/fullchain.pem`). Automatically applied to the MQTTS listener when present.
- **`ssl_keyfile`** *(optional)*: Path to the SSL private key file (e.g., `/ssl/privkey.pem`).
- **`ssl_cacertfile`** *(optional)*: Path to the CA certificate file if mutual/client certificate verification is required.
- **`telemetry_enable`**: Enables or disables anonymous telemetry reporting to EMQX (default: `false`).
- **`max_packet_size`**: Maximum allowed MQTT packet size (default: `1MB`, e.g., `5MB`, `10MB`).
- **`env_vars`**: Custom environment variables for advanced HOCON configuration. Each entry must start with `EMQX_` (e.g., `EMQX_NODE__DATA_DIR`).


## Support

Got questions?

You have several options to get them answered:

- The [Home Assistant Community Add-ons Discord chat server][discord] for add-on
  support and feature requests.
- The [Home Assistant Discord chat server][discord-ha] for general Home
  Assistant discussions and questions.
- The Home Assistant [Community Forum][forum].
- Join the [Reddit subreddit][reddit] in [/r/homeassistant][reddit]

You could also [open an issue here][issue] GitHub.

## Contributing

This is an active open-source project. We are always open to people who want to
use the code or contribute to it.

We have set up a separate document containing our
[contribution guidelines](.github/CONTRIBUTING.md).

Thank you for being involved! :heart_eyes:

## Authors & contributors

The original setup of this repository is by [Franck Nijhof][frenck].

For a full list of all authors and contributors,
check [the contributor's page][contributors].

## We have got some Home Assistant add-ons for you

Want some more functionality to your Home Assistant instance?

We have created multiple add-ons for Home Assistant. For a full list, check out
our [GitHub Repository][repository].

## License

MIT License

Copyright (c) 2023-2025 Franck Nijhof

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.

[aarch64-shield]: https://img.shields.io/badge/aarch64-yes-green.svg
[amd64-shield]: https://img.shields.io/badge/amd64-yes-green.svg


[commits-shield]: https://img.shields.io/github/commit-activity/y/raphael1688dev/addon-emqx.svg
[commits]: https://github.com/raphael1688dev/addon-emqx/commits/main
[contributors]: https://github.com/raphael1688dev/addon-emqx/graphs/contributors
[discord-ha]: https://discord.gg/c5DvZ4e
[discord-shield]: https://img.shields.io/discord/478094546522079232.svg
[discord]: https://discord.me/hassioaddons
[docs]: https://github.com/raphael1688dev/addon-emqx/blob/main/emqx/DOCS.md
[emqx]: https://www.emqx.io/
[forum-shield]: https://img.shields.io/badge/community-forum-brightgreen.svg
[forum]: https://community.home-assistant.io/?u=frenck
[frenck]: https://github.com/frenck
[github-actions-shield]: https://github.com/raphael1688dev/addon-emqx/workflows/CI/badge.svg
[github-actions]: https://github.com/raphael1688dev/addon-emqx/actions
[github-sponsors-shield]: https://frenck.dev/wp-content/uploads/2019/12/github_sponsor.png
[github-sponsors]: https://github.com/sponsors/frenck

[issue]: https://github.com/raphael1688dev/addon-emqx/issues
[license-shield]: https://img.shields.io/github/license/raphael1688dev/addon-emqx.svg
[maintenance-shield]: https://img.shields.io/maintenance/yes/2026.svg
[patreon-shield]: https://frenck.dev/wp-content/uploads/2019/12/patreon.png
[patreon]: https://www.patreon.com/frenck
[project-stage-shield]: https://img.shields.io/badge/project%20stage-experimental-yellow.svg
[reddit]: https://reddit.com/r/homeassistant
[releases-shield]: https://img.shields.io/github/release/raphael1688dev/addon-emqx.svg
[releases]: https://github.com/raphael1688dev/addon-emqx/releases
[repository]: https://github.com/hassio-addons/repository
