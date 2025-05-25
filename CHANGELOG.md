# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [0.1.0] - 2025-01-25

### Added
- Initial Docker Compose configuration for FusionAuth stack
- Core services: PostgreSQL 16, OpenSearch 2.11.0, and FusionAuth
- Profile-based optional services:
  - Caddy reverse proxy with automatic HTTPS support
  - Mailcatcher for email testing (SMTP on port 1025, UI on port 1080)
  - Kafka and Zookeeper for event streaming
  - Cleanspeak for content moderation
  - OpenTelemetry Collector and Prometheus for monitoring
- Custom Dockerfile configurations:
  - Standard FusionAuth build with OpenJDK 21 and Ubuntu Noble
  - MySQL variant with MySQL Connector/J included
- Environment variable configuration via `.env` file
- Support for FusionAuth kickstart files in `./kickstart/` directory
- Support for custom plugins in `./plugins/` directory
- Health checks for all services
- Persistent volumes for data storage
- Development-ready configuration with hardcoded API key for testing
- Prometheus configurations for both direct scraping and OpenTelemetry integration
- Caddy configuration for localhost development with HTTPS instructions
- Docker Compose override file for easy customization
- Memory configuration options for Java services
- Comprehensive service dependencies to ensure proper startup order

### Security
- Default development credentials provided (must be changed for production)
- Isolated Docker networks for service communication
- Health check endpoints configured for service monitoring

### Documentation
- Created CLAUDE.md for AI assistant guidance
- Added comprehensive README.md with setup and usage instructions
- Included troubleshooting section for common issues
- Documented all available Docker Compose profiles
- Listed all configurable environment variables

### Configuration
- Set default memory allocations (512M for FusionAuth and Cleanspeak)
- Configured OpenSearch with appropriate JVM settings
- Set restart policies to `unless-stopped` for all services
- Enabled development mode by default for FusionAuth
- Configured PostgreSQL with custom database and user setup