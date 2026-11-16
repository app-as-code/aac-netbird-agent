<p align="center">
  <img src="assets/icon.svg" alt="NetBird logo" width="128">
</p>

<h1 align="center">aac-netbird-agent</h1>

<p align="center">Self-hosted deployment definition for the NetBird agent, a WireGuard-based peer for private mesh access.</p>

<p align="center">
  <a href="https://github.com/netbirdio/netbird"><img alt="Upstream" src="https://img.shields.io/badge/upstream-netbirdio%2Fnetbird-blue?logo=github"></a>
  <img alt="Image" src="https://img.shields.io/badge/image-netbirdio%2Fnetbird%3A0.74.2-2496ED?logo=docker&logoColor=white">
  <a href="https://github.com/netbirdio/netbird/blob/main/LICENSE"><img alt="License" src="https://img.shields.io/badge/license-BSD--3--Clause-green"></a>
</p>

## About

NetBird is an open-source overlay network that connects devices into a private, encrypted
WireGuard mesh. This deployment runs the NetBird agent as a peer that can be reached from other
peers, for example for SSH access or as the target of a reverse proxy.

This definition deliberately limits the agent to remote access. It does not take over DNS
resolution on the host, does not pull or install mesh routes, and does not forward traffic for
other peers. Host-to-host traffic keeps using the existing network path.

Key features (as provided by upstream):

- WireGuard-based, end-to-end encrypted peer-to-peer connectivity
- Central management with access-control policies, groups and setup keys
- Optional built-in SSH server on peers, governed by access policies
- Optional routing of network resources for remote devices (Remote Network Access)
- Available as container image and as native client packages

## Upstream

- Project: <https://github.com/netbirdio/netbird>
- Documentation: <https://docs.netbird.io>
- Container image: `netbirdio/netbird:0.74.2`
- License: BSD-3-Clause (upstream project; the management, signal and relay components are AGPL-3.0)

## Quick start (Docker Compose)

The agent needs no external Docker networks and exposes no ports. It requires the
`NET_ADMIN` capability to create its WireGuard interface, and the host kernel must provide
WireGuard (or `/dev/net/tun` for the userspace fallback).

```bash
# Provide NB_SETUP_KEY (see "Configuration") in .env, then:
docker compose up -d
```

The setup key is generated per network in the NetBird dashboard. Do not commit it to this
repository.

## Configuration

Environment variables (defaults from `service.yml`; `CHANGE_ME` must be set per deployment):

| Variable | Default | Description |
|---|---|---|
| `NB_SETUP_KEY` | `CHANGE_ME` (secret) | Setup key of the NetBird network the peer joins |
| `NB_MANAGEMENT_URL` | `https://netbird.example.com` | NetBird management endpoint; set per environment |
| `NB_DISABLE_DNS` | `true` | `true` leaves `/etc/resolv.conf` untouched |
| `NB_DISABLE_CLIENT_ROUTES` | `true` | `true` does not pull or install mesh routes on the host |
| `NB_DISABLE_SERVER_ROUTES` | `true` | `true` keeps the peer from acting as a router for other peers |
| `TZ` | `Europe/Berlin` | Time zone |

Ports: none. The agent only makes outbound connections.

Volumes:

| Mount | Description |
|---|---|
| `nb_client` -> `/var/lib/netbird` | Peer state: private key and client configuration |

Capabilities: `NET_ADMIN`. Restart policy: `unless-stopped`.

## Deploy with App-as-Code

1. Edit `service.yml`. It is the single source of truth for image, environment, volumes and
   per-stage overrides. Do not edit the rendered output.
2. The same `service.yml` supports two deployment types (`docker_compose` and native
   `ansible`). The deployment type is selected per host in the infrastructure controller.
   The native path installs the NetBird client from the pinned release.
3. Pushing to `main` (or a configured stage branch) runs the service pipeline, which renders
   the manifests with `aac-template-engine` and triggers the deployment.
4. Releases of this definition are published under <https://github.com/app-as-code/aac-netbird-agent>.

## Credits and license

NetBird is developed by NetBird GmbH and contributors and licensed under BSD-3-Clause (with
AGPL-3.0 for some components). This repository only contains the deployment definition and is
not a fork of the application. The logo is the property of the NetBird project; see
[`assets/ICON_SOURCE.md`](assets/ICON_SOURCE.md) for icon sources and licenses.
