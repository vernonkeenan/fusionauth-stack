# FusionAuth Docker Stack

A comprehensive Docker Compose stack for deploying FusionAuth with optional supporting services including database, search, monitoring, and more.

## Overview

This repository provides a production-ready Docker Compose configuration for FusionAuth, an identity and access management platform. The stack includes all necessary dependencies and optional services that can be enabled using Docker Compose profiles.

## Prerequisites

- Docker Engine 20.10.0 or higher
- Docker Compose v2.0.0 or higher
- At least 4GB of available RAM (8GB recommended for full stack)

## Quick Start

1. Clone this repository:
   ```bash
   git clone <repository-url>
   cd fusionauth-stack
   ```

2. Start the core services:
   ```bash
   docker compose up -d
   ```

3. Access FusionAuth at http://localhost:9011

## Services

### Core Services (Always Started)

- **PostgreSQL 16**: Primary database
  - Port: 5432
  - Credentials: See `.env` file

- **OpenSearch 2.11.0**: Search engine
  - Port: 9200
  - Alternative: Can use database search by setting `FUSIONAUTH_SEARCH_TYPE=database`

- **FusionAuth**: Identity and access management
  - Port: 9011
  - Admin UI: http://localhost:9011

### Optional Services (Profile-Based)

Enable optional services using Docker Compose profiles:

#### Caddy (Profile: `caddy`)
Reverse proxy with automatic HTTPS
```bash
docker compose --profile caddy up -d
```
- Access FusionAuth via http://localhost
- Configure domain in `Caddyfile` for HTTPS

#### Mailcatcher (Profile: `mailcatcher`)
SMTP server for development/testing
```bash
docker compose --profile mailcatcher up -d
```
- SMTP Port: 1025
- Web UI: http://localhost:1080

#### Kafka (Profile: `kafka`)
Event streaming for webhooks
```bash
docker compose --profile kafka up -d
```
- Kafka Port: 9092
- Zookeeper Port: 2181

#### Cleanspeak (Profile: `cleanspeak`)
Content filtering and moderation
```bash
docker compose --profile cleanspeak up -d
```
- Port: 8001
- Requires license ID in `.env`

#### Monitoring (Profile: `monitoring`)
OpenTelemetry and Prometheus
```bash
docker compose --profile monitoring up -d
```
- Prometheus UI: http://localhost:9090
- Metrics endpoint: http://localhost:9011/api/prometheus/metrics

## Configuration

### Environment Variables

Key configuration options in `.env`:

```bash
# FusionAuth Settings
FUSIONAUTH_APP_MEMORY=512M              # JVM memory allocation
FUSIONAUTH_APP_RUNTIME_MODE=development # or 'production'
FUSIONAUTH_SEARCH_TYPE=elasticsearch    # or 'database'

# Database Settings
DATABASE_USER=fusionauth
DATABASE_PASSWORD=<secure-password>

# Optional Services
CLEANSPEAK_LICENSE_ID=<your-license-id>
```

### Kickstart Configuration

Place kickstart files in the `./kickstart/` directory to configure FusionAuth on first startup. The default file path is set to `/usr/local/fusionauth/kickstart/kickstart.json`.

### Custom Plugins

Add custom FusionAuth plugins to the `./plugins/` directory. They will be automatically loaded on startup.

## Custom Builds

Two custom Dockerfile configurations are provided:

1. **fusionauth-app**: Standard FusionAuth with OpenJDK 21
2. **fusionauth-app-mysql**: FusionAuth with MySQL Connector/J included

To use the MySQL variant:
```bash
docker compose -f docker-compose.yml -f docker-compose.override.yml up -d
```

## Management Commands

### View Logs
```bash
docker compose logs fusionauth
docker compose logs -f fusionauth  # Follow mode
```

### Restart Services
```bash
docker compose restart fusionauth
```

### Stop All Services
```bash
docker compose down
```

### Full Reset (Including Data)
```bash
docker compose down -v
```

### Update Images
```bash
docker compose pull
docker compose up -d
```

## Volumes

Persistent data is stored in Docker volumes:

- `db_data`: PostgreSQL database files
- `search_data`: OpenSearch indices
- `fusionauth_config`: FusionAuth configuration
- `cleanspeak_config`: Cleanspeak configuration (when enabled)

## Monitoring

When the monitoring profile is enabled:

1. FusionAuth exposes Prometheus metrics at `/api/prometheus/metrics`
2. OpenTelemetry Collector scrapes and processes metrics
3. Prometheus stores and queries metrics
4. Default API key for development: `33052c8a-c283-4e96-9d2a-eb1215c69f8f-not-for-prod`

**Note**: Generate a secure API key for production use.

## Troubleshooting

### Service Won't Start
- Check logs: `docker compose logs <service-name>`
- Verify port availability: `netstat -tulpn | grep <port>`
- Ensure sufficient memory: `docker stats`

### Database Connection Issues
- Verify PostgreSQL is healthy: `docker compose ps db`
- Check credentials in `.env` match compose configuration
- Ensure database initialization completed: `docker compose logs db`

### Search Service Issues
- OpenSearch requires at least 262144 for vm.max_map_count
- Set with: `sudo sysctl -w vm.max_map_count=262144`
- For permanent change, add to `/etc/sysctl.conf`

## Security Considerations

1. **Change Default Passwords**: Update all passwords in `.env` before production use
2. **API Keys**: Replace development API keys with secure generated keys
3. **Network Security**: Consider using Docker networks to isolate services
4. **HTTPS**: Enable Caddy profile and configure proper domain for HTTPS
5. **Firewall**: Restrict port access as needed for your environment

## License

This Docker Compose configuration is provided as-is. FusionAuth is licensed separately - see [FusionAuth Licensing](https://fusionauth.io/license) for details.

## Support

- FusionAuth Documentation: https://fusionauth.io/docs
- FusionAuth Community: https://fusionauth.io/community
- Docker Compose Documentation: https://docs.docker.com/compose/