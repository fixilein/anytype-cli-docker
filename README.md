# anytype-cli-docker

Docker wrapper for [anytype-cli](https://github.com/anyproto/anytype-cli) — a headless CLI for interacting with Anytype.

## Quick Start

```bash
docker compose up -d
```

## Build a specific version

```bash
docker compose build --build-arg ANYTYPE_CLI_VERSION=v0.1.9
```

## Self-Hosted Network

Place your `client-config.yml` (from your any-sync-dockercompose `etc/` directory) one level above this repo. It is mounted read-only into the container at `/root/.config/anytype/network.yml`.

## Setup

Run these once after the container is first started. The bot credentials and API keys are persisted in `./anytype-home`.

```bash
# 1. Create a bot account, pointing at your self-hosted network config
docker compose exec anytype-cli anytype auth create <bot-name> --network-config /root/.config/anytype/network.yml

# 2. Join a space (get the invite link from the Anytype desktop app)
docker compose exec anytype-cli anytype space join "<invite-link>"

# 3. Create an API key for programmatic access
docker compose exec anytype-cli anytype auth apikey create <key-name>
```

The `--network-config` flag is required when creating the account — it registers the bot against the self-hosted network and saves the path to `/root/.anytype/config.json`. Without it the CLI will attempt to connect to the Anytype cloud network.

The API is then available at `http://localhost:31012`.

## Usage

```bash
docker compose exec anytype-cli anytype --help
docker compose exec anytype-cli anytype auth status
docker compose exec anytype-cli anytype space list
```

## Data

| Mount | Purpose |
|-------|---------|
| `./data:/data` | General app data |
| `./anytype-home:/root/.anytype` | Bot credentials and config (`config.json`) |
| `../client-config.yml:/root/.config/anytype/network.yml` | Self-hosted network config (read-only) |
