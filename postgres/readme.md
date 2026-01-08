# PostgreSQL Docker Compose Setup

This directory contains a Docker Compose configuration for running PostgreSQL as a containerized service.

This setup creates a PostgreSQL service that:

- Persists database data in a Docker volume
- Includes health checks to ensure the service is running properly
- Exposes a configurable port for direct access from applications

## Prerequisites

- Docker and Docker Compose installed

## Setup Instructions

### Step 1: Start the PostgreSQL Container

Start the PostgreSQL service using Docker Compose:

```bash
docker compose -f postgres-compose.yml up -d
```

The `-d` flag runs the container in detached mode (in the background).

### Step 2: Verify the Service

Check that the container is running:

```bash
docker ps | grep postgres
```

You can also test the connection:

```bash
docker exec -it postgres psql -U postgres
```

## Using PostgreSQL from Applications

Before connecting, configure the port mapping in `postgres-compose.yml` (replace `xxx` with your desired host port).

Applications can connect to PostgreSQL using:

- **Host:** `localhost` (or `127.0.0.1`)
- **Port:** The port you configured in `postgres-compose.yml`
- **Connection String:** `postgresql://postgres:postgres@localhost:<your-port>/postgres`

## Additional Notes

- **Data Storage:** Database data is stored in the `postgres_data` Docker volume and persists across container restarts
- **Default Credentials:** The default username, password, and database are all set to `postgres`. Change these in `postgres-compose.yml` for production use
- **Port Configuration:** Configure the host port in `postgres-compose.yml` by replacing `xxx` with your desired port number
