# Redis Docker Compose Setup

This directory contains a Docker Compose configuration for running [Redis](https://redis.io/) as a containerized service.

## Overview

This setup creates a Redis service that:

- Persists data in a Docker volume
- Includes health checks to ensure the service is running properly
- Exposes a configurable port for direct access from applications
- Optionally supports password authentication

## Prerequisites

- Docker and Docker Compose installed

## Setup Instructions

### Step 1: Configure Port (and Optional Password)

Before starting the service, configure the following in `redis-compose.yml`:

- **Port Mapping:** Replace `xxx` in the ports section with your desired host port (e.g., `6379:6379`)
- **Password Authentication:** (Optional) If you want to require a password, uncomment the `command` line and replace `xxx` with your desired password

### Step 2: Start the Redis Container

Start the Redis service using Docker Compose:

```bash
docker compose -f redis-compose.yml up -d
```

The `-d` flag runs the container in detached mode (in the background).

### Step 3: Verify the Service

Check that the container is running:

```bash
docker ps | grep redis
```

You can also test the connection:

```bash
docker exec -it redis redis-cli ping
```

This should return `PONG` if Redis is working correctly.

## Using Redis from Applications

Before connecting, configure the port mapping in `redis-compose.yml` (replace `xxx` with your desired host port).

Applications can connect to Redis using:

- **Host:** `localhost` (or `127.0.0.1`)
- **Port:** The port you configured in `redis-compose.yml`
- **Password:** (Optional) If you configured password authentication, use the password you set

### Example Connection

```javascript
// Using the Redis JavaScript client (ioredis)
import Redis from "ioredis";

const redis = new Redis({
  host: "localhost",
  port: 6379, // Your configured port
  password: "your-password-here", // Only if you configured authentication
});
```

```python
# Using the Redis Python client (redis-py)
import redis

r = redis.Redis(
    host='localhost',
    port=6379,  # Your configured port
    password='your-password-here',  # Only if you configured authentication
    decode_responses=True
)
```

## Additional Notes

- **Data Storage:** Redis data is stored in the `redis_data` Docker volume and persists across container restarts
- **Password Authentication:** By default, Redis runs without authentication. For production use, uncomment the `command` line in `redis-compose.yml` and set a strong password
- **Port Configuration:** Configure the host port in `redis-compose.yml` by replacing `xxx` with your desired port number
- **Persistence:** Redis data is persisted to disk in the Docker volume, so data survives container restarts
- **Image:** Uses the official `redis:7-alpine` image for a smaller footprint
