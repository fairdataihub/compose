# Valkey Docker Compose Setup

This directory contains a Docker Compose configuration for running [Valkey](https://valkey.io/) as a containerized service.

## Overview

Valkey is an open-source fork of Redis that maintains Redis compatibility while providing additional features and improvements. This setup creates a Valkey service that:

- Persists data in a Docker volume
- Includes health checks to ensure the service is running properly
- Exposes a configurable port for direct access from applications
- Optionally supports password authentication
- Maintains Redis protocol compatibility

## Prerequisites

- Docker and Docker Compose installed

## Setup Instructions

### Step 1: Configure Port (and Optional Password)

Before starting the service, configure the following in `valkey-compose.yml`:

- **Port Mapping:** Replace `xxx` in the ports section with your desired host port (e.g., `6379:6379`)
- **Password Authentication:** (Optional) If you want to require a password, uncomment the `command` line and replace `xxx` with your desired password

### Step 2: Start the Valkey Container

Start the Valkey service using Docker Compose:

```bash
docker compose -f valkey-compose.yml up -d
```

The `-d` flag runs the container in detached mode (in the background).

### Step 3: Verify the Service

Check that the container is running:

```bash
docker ps | grep valkey
```

You can also test the connection:

```bash
docker exec -it valkey valkey-cli ping
```

This should return `PONG` if Valkey is working correctly.

## Using Valkey from Applications

Before connecting, configure the port mapping in `valkey-compose.yml` (replace `xxx` with your desired host port).

Valkey is Redis-compatible, so you can use any Redis client library. Applications can connect to Valkey using:

- **Host:** `localhost` (or `127.0.0.1`)
- **Port:** The port you configured in `valkey-compose.yml`
- **Password:** (Optional) If you configured password authentication, use the password you set

### Example Connection

```javascript
// Using the Redis JavaScript client (ioredis) - works with Valkey
import Redis from "ioredis";

const valkey = new Redis({
  host: "localhost",
  port: 6379, // Your configured port
  password: "your-password-here", // Only if you configured authentication
});
```

```python
# Using the Redis Python client (redis-py) - works with Valkey
import redis

r = redis.Redis(
    host='localhost',
    port=6379,  # Your configured port
    password='your-password-here',  # Only if you configured authentication
    decode_responses=True
)
```

## Additional Notes

- **Data Storage:** Valkey data is stored in the `valkey_data` Docker volume and persists across container restarts
- **Password Authentication:** By default, Valkey runs without authentication. For production use, uncomment the `command` line in `valkey-compose.yml` and set a strong password
- **Port Configuration:** Configure the host port in `valkey-compose.yml` by replacing `xxx` with your desired port number
- **Persistence:** Valkey data is persisted to disk in the Docker volume, so data survives container restarts
- **Image:** Uses the official `valkey/valkey:7.2-alpine` image for a smaller footprint
- **Redis Compatibility:** Valkey is fully compatible with the Redis protocol, so existing Redis clients and applications work without modification
- **Valkey vs Redis:** Valkey is a fork of Redis that continues development under the Linux Foundation. It maintains Redis compatibility while offering additional features and improvements
