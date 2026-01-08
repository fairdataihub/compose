# Ollama Docker Compose Setup

This directory contains a Docker Compose configuration for running [Ollama](https://ollama.ai/) as a containerized service.

## Overview

This setup creates an Ollama service that:

- Runs in a dedicated Docker network for easy integration with other services
- Persists model data in a Docker volume
- Supports GPU acceleration (if available)
- Includes health checks to ensure the service is running properly
- Can be accessed by other containers on the same network

## Prerequisites

- Docker and Docker Compose installed
- (Optional) NVIDIA GPU with Docker GPU support for better performance

## Setup Instructions

### Step 1: Create the Docker Network

First, create the external Docker network that will be shared between Ollama and your other services:

```bash
docker network create ollama-network
```

**Note:** This network must be created before starting the Ollama container, as the compose file references it as an external network.

### Step 2: Start the Ollama Container

Start the Ollama service using Docker Compose:

```bash
docker compose -f ollama-compose.yml up -d
```

The `-d` flag runs the container in detached mode (in the background).

### Step 3: Verify the Service

Check that the container is running:

```bash
docker ps | grep ollama
```

You can also test the service by listing available models:

```bash
docker exec ollama ollama list
```

## Using Ollama from Other Services

To use Ollama from another Docker Compose project, add the following configuration to your `docker-compose.yml`:

```yaml
services:
  your-app:
    # Set the Ollama host URL to access the service by container name
    environment:
      - OLLAMA_HOST=http://ollama:11434
    networks:
      - ollama-network

networks:
  ollama-network:
    external: true
    name: ollama-network
```

**Key Points:**

- Use `http://ollama:11434` as the host URL (the container name resolves to the service)
- Ensure your service is on the same `ollama-network` network
- The network must be marked as `external: true` since it's created outside this compose file

## Additional Notes

- **Model Storage:** Models are stored in the `ollama` Docker volume and persist across container restarts
- **GPU Support:** The configuration includes GPU support. If you don't have a GPU, you may need to remove or comment out the `gpus` section
- **Host Access:** By default, Ollama is only accessible from other containers. To access it from your host machine, uncomment the `ports` section in `ollama-compose.yml`
