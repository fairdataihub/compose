# Traefik Docker Compose Setup

This directory contains a Docker Compose configuration for running [Traefik](https://traefik.io/) as a containerized reverse proxy and load balancer.

## Overview

This setup creates a Traefik service that:

- Automatically discovers Docker containers and routes traffic to them
- Provides a web dashboard for monitoring and configuration
- Supports HTTP and HTTPS entry points
- Includes health checks to ensure the service is running properly
- Enables access logging for debugging and monitoring

## Prerequisites

- Docker and Docker Compose installed
- Docker socket access (required for automatic service discovery)

## Setup Instructions

### Step 1: Configure Ports

Before starting the service, configure the following in `traefik-compose.yml`:

- **HTTP Port:** Replace the first `xxx` in the ports section with your desired host port for HTTP (e.g., `80:80`)
- **HTTPS Port:** (Optional) Replace the second `xxx` with your desired host port for HTTPS (e.g., `443:443`)
- **Dashboard Port:** Replace the third `xxx` with your desired host port for the dashboard (e.g., `8080:8080`)

### Step 2: Start the Traefik Container

Start the Traefik service using Docker Compose:

```bash
docker compose -f traefik-compose.yml up -d
```

The `-d` flag runs the container in detached mode (in the background).

### Step 3: Verify the Service

Check that the container is running:

```bash
docker ps | grep traefik
```

You can also access the Traefik dashboard by opening a browser and visiting:

```text
http://localhost:<your-dashboard-port>
```

## Using Traefik with Other Services

Traefik automatically discovers Docker containers and can route traffic to them. To enable routing for a service, add the following labels to your service's Docker Compose file:

```yaml
services:
  your-service:
    # ... other configuration ...
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.your-service.rule=Host(`your-service.localhost`)"
      - "traefik.http.routers.your-service.entrypoints=web"
      - "traefik.http.services.your-service.loadbalancer.server.port=3000"
```

### Example: Routing to a Web Application

```yaml
services:
  webapp:
    image: nginx:alpine
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.webapp.rule=Host(`app.localhost`)"
      - "traefik.http.routers.webapp.entrypoints=web"
      - "traefik.http.services.webapp.loadbalancer.server.port=80"
    networks:
      - traefik-network

networks:
  traefik-network:
    external: true # If Traefik is on an external network
```

## Configuration Options

### Basic Configuration

The default configuration includes:

- Docker provider enabled for automatic service discovery
- Dashboard enabled on port 8080
- HTTP entry point on port 80
- HTTPS entry point on port 443 (if configured)

### Advanced Configuration

For more advanced configurations, you can:

1. **Mount a Traefik configuration file:**

   - Uncomment the `traefik.yml` volume mount in the compose file
   - Create a `traefik.yml` file with your configuration

2. **Use dynamic configuration:**

   - Uncomment the `dynamic` volume mount in the compose file
   - Create a `dynamic` directory with your dynamic configuration files

3. **Enable HTTPS with Let's Encrypt:**
   - Add Let's Encrypt configuration to your Traefik config
   - Mount a volume for certificate storage

## Accessing the Dashboard

The Traefik dashboard is available at:

```text
http://localhost:<your-dashboard-port>
```

The dashboard shows:

- All configured routers and services
- Real-time request statistics
- Service health status
- Configuration overview

## Additional Notes

- **Docker Socket Access:** Traefik requires access to the Docker socket to discover services. The compose file mounts the socket as read-only for security
- **Port Configuration:** Configure all ports in `traefik-compose.yml` by replacing `xxx` with your desired port numbers
- **Service Discovery:** Traefik automatically discovers containers on the same Docker network. Make sure your services are on a network that Traefik can access
- **Labels:** Use Docker labels to configure routing rules for your services
- **Production Use:** For production, disable the insecure API (`--api.insecure=false`) and configure proper authentication
- **HTTPS:** To enable HTTPS, configure SSL certificates and update the Traefik configuration accordingly

## Common Use Cases

### Reverse Proxy

Route external traffic to internal services based on hostname or path.

### Load Balancing

Distribute traffic across multiple instances of a service.

### SSL Termination

Handle SSL/TLS certificates and terminate SSL at the proxy level.

### Service Discovery

Automatically discover and route to new services as they start.

For more information, visit the [Traefik documentation](https://doc.traefik.io/traefik/).
