# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Purpose

Docker wrapper for [anytype-cli](https://github.com/anyproto/anytype-cli), a headless CLI for Anytype. The image is built from source using a multi-stage build: Go 1.24 builder stage compiles the binary, then copies it into a minimal `debian:bookworm-slim` runtime.

## Build & Run

```bash
# Build and start
docker compose up -d

# Build a specific upstream version
docker compose build --build-arg ANYTYPE_CLI_VERSION=v0.1.9

# Rebuild and restart
docker compose up -d --build
```

## Setup (first run)

```bash
# 1. Create a bot account (--network-config required for self-hosted networks)
docker compose exec anytype-cli anytype auth create <bot-name> --network-config /root/.config/anytype/network.yml

# 2. Join a space (invite link from the Anytype desktop app)
docker compose exec anytype-cli anytype space join "<invite-link>"

# 3. Generate an API key
docker compose exec anytype-cli anytype auth apikey create <key-name>
```

API is available at `http://localhost:31012` after setup.

## Architecture

- **Dockerfile**: Multi-stage build. Builder clones `anytype-cli` at `ANYTYPE_CLI_VERSION`, runs `make download-tantivy && make build`, outputs binary to `/src/dist/anytype`. Runtime stage copies only the binary.
- **Ports**: 31010, 31011, 31012 (primary API)
- **Data persistence**: `./data:/data` (app data), `./anytype-home:/root/.anytype` (bot credentials/config), `../client-config.yml` mounted as network config (read-only)
- The `docker-compose.yml` overrides the default `CMD ["serve"]` with `serve --listen-address 0.0.0.0:31012` to bind on all interfaces.
