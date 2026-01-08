# Docker Compose Services Collection

This repository contains Docker Compose configurations for various services commonly used in development and production environments. Each service is self-contained in its own directory with its own compose file and detailed documentation.

## Available Services

This collection includes the following services:

### Database & Storage

- **[PostgreSQL](postgres/)** - Open-source relational database management system
- **[Redis](redis/)** - In-memory data structure store, used as a database, cache, and message broker
- **[Valkey](valkey/)** - Open-source fork of Redis with Redis protocol compatibility
- **[Dragonfly](dragonfly/)** - Modern in-memory datastore with Redis and Memcached API compatibility
- **[InfluxDB](influxdb/)** - Time series database built for real-time analytic workloads

### Search & AI

- **[Meilisearch](meilisearch/)** - Fast, open-source search engine
- **[Ollama](ollama/)** - Local AI model runner and API server

### Web Servers & Reverse Proxies

- **[Traefik](traefik/)** - Modern reverse proxy and load balancer with automatic service discovery
- **[Nginx](nginx/)** - High-performance web server and reverse proxy

## Quick Start

Each service directory contains its own Docker Compose file and detailed README with setup instructions. To get started with a specific service:

1. Navigate to the service directory (e.g., `cd postgres`)
2. Review the service-specific README for configuration details
3. Start the service using Docker Compose:

```bash
docker compose -f <service-compose.yml> up -d
```

## Prerequisites

All services require:

- Docker installed and running
- Docker Compose installed (v2.0+)

Some services may have additional requirements:

- **Ollama**: Optional NVIDIA GPU support for better performance
- **PostgreSQL/Redis/Valkey/Dragonfly/InfluxDB**: Port configuration before starting
- **Dragonfly**: Minimum 4GB RAM recommended for optimal performance
- **Traefik**: Docker socket access for automatic service discovery

## Service Details

Each service includes:

- A Docker Compose configuration file
- A detailed README with setup instructions
- Connection examples for common programming languages
- Configuration options and best practices

## Usage Examples

### Starting a Single Service

```bash
# Start PostgreSQL
cd postgres
docker compose -f postgres-compose.yml up -d

# Start Redis
cd redis
docker compose -f redis-compose.yml up -d
```

### Checking Service Status

```bash
# Check if a service is running
docker ps | grep <service-name>

# View logs
docker compose -f <service-compose.yml> logs -f
```

### Stopping a Service

```bash
docker compose -f <service-compose.yml> down
```

## Service-Specific Documentation

For detailed setup instructions, configuration options, and usage examples, refer to each service's README:

- [PostgreSQL Setup](postgres/readme.md)
- [Redis Setup](redis/readme.md)
- [Valkey Setup](valkey/readme.md)
- [Dragonfly Setup](dragonfly/readme.md)
- [InfluxDB Setup](influxdb/readme.md)
- [Meilisearch Setup](meilisearch/readme.md)
- [Ollama Setup](ollama/readme.md)
- [Traefik Setup](traefik/readme.md)
- [Nginx Setup](nginx/readme.md)

## Notes

- **Port Configuration**: Most services require you to configure port mappings before starting. Replace `xxx` placeholders in the compose files with your desired ports.
- **Data Persistence**: All services use Docker volumes to persist data across container restarts.
- **Security**: Default configurations are suitable for development. For production use, review and update security settings (passwords, authentication, etc.) in each service's compose file.
- **Networking**: Services run independently by default. To connect services together, configure Docker networks as needed (see Ollama's README for an example of shared networking).

## Contributing

Each service is designed to be self-contained and independently usable. When adding new services:

1. Create a new directory with a descriptive name
2. Include a `*-compose.yml` file with the Docker Compose configuration
3. Add a comprehensive `readme.md` with setup instructions and usage examples
4. Follow the existing documentation style and structure
