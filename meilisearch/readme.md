# Meilisearch Docker Compose Setup

This directory contains a Docker Compose configuration for running [Meilisearch](https://www.meilisearch.com/) as a containerized service.

## Overview

This setup creates a Meilisearch service that:

- Persists search index data in a Docker volume
- Exposes a configurable port for direct access from applications
- Runs in development mode with configurable master key and payload size limits

## Prerequisites

- Docker and Docker Compose installed

## Setup Instructions

### Step 1: Configure Environment Variables and Port

Before starting the service, configure the following in `meilisearch.compose.yml`:

- **Port Mapping:** Replace `xxx` in the ports section with your desired host port (e.g., `42341:7700`)
- **MEILI_MASTER_KEY:** Replace `xxx` with a secure master key for authentication
- **MEILI_HTTP_PAYLOAD_SIZE_LIMIT:** (Optional) Replace `xxx` with the maximum payload size (e.g., `100MB` or `104857600` for 100MB in bytes). This is not needed for 99% of cases and can be removed if not required.

### Step 2: Start the Meilisearch Container

Start the Meilisearch service using Docker Compose:

```bash
docker compose -f meilisearch.compose.yml up -d
```

The `-d` flag runs the container in detached mode (in the background).

### Step 3: Verify the Service

Check that the container is running:

```bash
docker ps | grep meilisearch
```

You can also test the service by checking the health endpoint (replace `42341` with your configured port):

```bash
curl http://localhost:42341/health
```

## Using Meilisearch from Applications

Before connecting, ensure you've configured the port mapping in `meilisearch.compose.yml` (replace `xxx` with your desired host port).

Applications can connect to Meilisearch using:

- **Host:** `localhost` (or `127.0.0.1`)
- **Port:** The host port you configured in `meilisearch.compose.yml` (container port is always `7700`)
- **Master Key:** The value you set for `MEILI_MASTER_KEY` in the compose file
- **API Endpoint:** `http://localhost:<your-configured-port>`

### Example Connection

```javascript
// Using the Meilisearch JavaScript client
import { MeiliSearch } from "meilisearch";

const client = new MeiliSearch({
  host: "http://localhost:<your-configured-port>",
  apiKey: "your-master-key-here",
});
```

## Additional Notes

- **Data Storage:** Search index data is stored in the `meili_data` Docker volume and persists across container restarts
- **Master Key:** The master key is required for all operations. Keep it secure and never commit it to version control
- **Port Configuration:** Configure the port mapping in `meilisearch.compose.yml` by replacing `xxx` with your desired host port. The container port is always `7700`
- **Environment:** Currently set to `development`. Change to `production` for production deployments
- **Payload Size Limit:** The `MEILI_HTTP_PAYLOAD_SIZE_LIMIT` environment variable is optional and not needed for most use cases. Only configure it if you need to handle very large document payloads
