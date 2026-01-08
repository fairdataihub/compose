# Dragonfly Docker Compose Setup

This directory contains a Docker Compose configuration for running [Dragonfly](https://www.dragonflydb.io/) as a containerized service.

## Overview

This setup creates a Dragonfly service that:

- Persists data in a Docker volume
- Includes health checks to ensure the service is running properly
- Exposes a configurable port for direct access from applications
- Supports both Redis protocol and HTTP requests on the same port
- Provides high performance with multi-threaded architecture

## Prerequisites

- Docker and Docker Compose installed
- Minimum 4GB of RAM (recommended for optimal performance)
- Minimum 1 CPU core
- Linux Kernel 4.19 or higher (for Linux hosts)

## Setup Instructions

### Step 1: Configure Port

Before starting the service, configure the following in `dragonfly-compose.yml`:

- **Port Mapping:** Replace `xxx` in the ports section with your desired host port (e.g., `6379:6379`)

### Step 2: Start the Dragonfly Container

Start the Dragonfly service using Docker Compose:

```bash
docker compose -f dragonfly-compose.yml up -d
```

The `-d` flag runs the container in detached mode (in the background).

### Step 3: Verify the Service

Check that the container is running:

```bash
docker ps | grep dragonfly
```

You can also test the connection using Redis CLI:

```bash
docker exec -it dragonfly redis-cli ping
```

This should return `PONG` if Dragonfly is working correctly.

You can also test the HTTP interface by opening a browser and visiting `http://localhost:<your-configured-port>`.

## Using Dragonfly from Applications

Before connecting, configure the port mapping in `dragonfly-compose.yml` (replace `xxx` with your desired host port).

Dragonfly is fully Redis-compatible, so you can use any Redis client library. Applications can connect to Dragonfly using:

- **Host:** `localhost` (or `127.0.0.1`)
- **Port:** The port you configured in `dragonfly-compose.yml`
- **Protocol:** Redis protocol (default) or HTTP

### Example Connection

```javascript
// Using the Redis JavaScript client (ioredis) - works with Dragonfly
import Redis from "ioredis";

const dragonfly = new Redis({
  host: "localhost",
  port: 6379, // Your configured port
});
```

```python
# Using the Redis Python client (redis-py) - works with Dragonfly
import redis

r = redis.Redis(
    host='localhost',
    port=6379,  # Your configured port
    decode_responses=True
)
```

### HTTP Access

Dragonfly also supports HTTP requests on the same port. You can access it via:

```bash
curl http://localhost:<your-configured-port>
```

## Additional Notes

- **Data Storage:** Dragonfly data is stored in the `dragonfly_data` Docker volume and persists across container restarts
- **Port Configuration:** Configure the host port in `dragonfly-compose.yml` by replacing `xxx` with your desired port number
- **Persistence:** Dragonfly data is persisted to disk in the Docker volume, so data survives container restarts
- **Image:** Uses the official `docker.dragonflydb.io/dragonflydb/dragonfly` image
- **Redis Compatibility:** Dragonfly is fully compatible with the Redis protocol, so existing Redis clients and applications work without modification
- **HTTP Support:** Dragonfly supports both Redis protocol and HTTP on the same port, making it easy to interact with via web browsers or HTTP clients
- **Performance:** Dragonfly uses a multi-threaded architecture and can handle millions of operations per second. The `ulimit memlock=-1` setting is configured for optimal performance
- **Memory Requirements:** For best performance, ensure your system has at least 4GB of available RAM

## Dragonfly vs Redis

Dragonfly is a modern alternative to Redis that:

- Provides full Redis API compatibility
- Offers better performance with multi-threaded architecture
- Supports both Redis and Memcached protocols
- Includes HTTP interface support
- Uses less memory for certain workloads

For more information, visit the [Dragonfly documentation](https://www.dragonflydb.io/docs).
