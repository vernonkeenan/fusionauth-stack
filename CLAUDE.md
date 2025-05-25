# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

This repository contains a Docker Compose stack for FusionAuth, an identity and access management platform. The stack includes FusionAuth along with its required dependencies and optional supporting services.

## Common Commands

### Starting the Stack

```bash
# Start core services (PostgreSQL, OpenSearch, FusionAuth)
docker compose up -d

# Start with specific profiles
docker compose --profile caddy up -d        # Include Caddy reverse proxy
docker compose --profile mailcatcher up -d  # Include email testing
docker compose --profile kafka up -d        # Include Kafka for webhooks
docker compose --profile cleanspeak up -d   # Include content filtering
docker compose --profile monitoring up -d   # Include Prometheus/OpenTelemetry

# Start with multiple profiles
docker compose --profile caddy --profile monitoring up -d
```

### Managing Services

```bash
# View logs
docker compose logs fusionauth
docker compose logs -f fusionauth  # Follow logs

# Restart services
docker compose restart fusionauth

# Stop everything
docker compose down

# Stop and remove volumes (full reset)
docker compose down -v
```

### Building Custom Images

```bash
# Build FusionAuth with MySQL connector
docker compose build fusionauth-app-mysql

# Build standard FusionAuth image
docker compose build fusionauth
```

## Architecture

### Core Services
- **PostgreSQL** (port 5432): Primary database for FusionAuth and Cleanspeak
- **OpenSearch** (port 9200): Search engine for FusionAuth (can be switched to database search via `FUSIONAUTH_SEARCH_TYPE`)
- **FusionAuth** (port 9011): Identity management application

### Service Dependencies
- FusionAuth depends on both `db` and `search` services
- Cleanspeak (when enabled) depends on `db` service
- All services configured with health checks and restart policies

### Configuration
- Environment variables in `.env` file control memory settings, credentials, and feature flags
- Kickstart files in `./kickstart/` directory for initial FusionAuth configuration
- Custom plugins can be placed in `./plugins/` directory
- Caddy configuration in `Caddyfile` for reverse proxy setup

### Key Environment Variables
- `FUSIONAUTH_APP_MEMORY`: FusionAuth JVM memory (default: 512M)
- `FUSIONAUTH_SEARCH_TYPE`: Choose between `elasticsearch` or `database` search
- `DATABASE_USER` / `DATABASE_PASSWORD`: PostgreSQL credentials
- `FUSIONAUTH_APP_RUNTIME_MODE`: Set to `development` or `production`

### Monitoring Integration
When using the monitoring profile:
- OpenTelemetry Collector scrapes metrics from FusionAuth
- Prometheus collects metrics from OpenTelemetry
- Development API key: `33052c8a-c283-4e96-9d2a-eb1215c69f8f-not-for-prod`

## Important Notes

- The stack uses Docker Compose profiles to enable optional services
- Custom Dockerfiles extend official FusionAuth images with additional dependencies
- Persistent data stored in Docker volumes (db_data, search_data, fusionauth_config)
- Health checks ensure services are ready before dependent services start