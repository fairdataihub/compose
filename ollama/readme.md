# Ollama Docker Compose Setup

This directory contains Docker Compose configurations for running [Ollama](https://ollama.ai/) as a containerized service with GPU support.

## Compose Files

| File | Platform | Recommended |
|------|----------|-------------|
| `ollama-compose.yml` | Debian / Standard Docker | **Yes** |
| `ollama-compose-ubuntu.yml` | Ubuntu with Snap Docker | No |

### Why Two Files?

**`ollama-compose.yml` (Recommended)** - For Debian and any system with Docker installed via the official apt repository. Uses the modern `gpus:` syntax for GPU passthrough.

**`ollama-compose-ubuntu.yml` (Not Recommended)** - For Ubuntu systems stuck with Snap Docker. We do not recommend this setup due to numerous issues:

- Snap confinement breaks the CDI (Container Device Interface) method
- The `gpus:` syntax fails with permission/path errors
- Requires legacy `runtime: nvidia` workaround
- Inconsistent behavior across Ubuntu versions
- Updates can break GPU passthrough unexpectedly

**If you're on Ubuntu**, we strongly recommend uninstalling Snap Docker and installing Docker via the [official Docker apt repository](https://docs.docker.com/engine/install/ubuntu/) instead.

## Overview

This setup creates an Ollama service that:

- Runs in a dedicated Docker network for easy integration with other services
- Persists model data in a Docker volume
- Pins to GPU 0 for hard isolation (Debian version)
- Includes health checks to ensure the service is running properly
- Can be accessed by other containers on the same network

### Why This Setup is Correct

- **Hard GPU isolation**: Only GPU 0 is passed into the container
- **Future-proof**: Uses modern `gpus:` instead of legacy `runtime: nvidia`
- **Safe coexistence**: Other apps can cleanly pin to GPU 1
- **No accidental multi-GPU memory grabs**

## Prerequisites

- Docker and Docker Compose installed (preferably via official apt repository, not Snap)
- NVIDIA GPU with [nvidia-container-toolkit](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/install-guide.html) installed

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
# For Debian / Standard Docker (recommended)
docker compose -f ollama-compose.yml up -d

# For Ubuntu with Snap Docker (not recommended)
docker compose -f ollama-compose-ubuntu.yml up -d
```

The `-d` flag runs the container in detached mode (in the background).

### Step 3: Verify the Service

Check that the container is running:

```bash
docker ps | grep ollama
```

Verify GPU isolation is working correctly:

```bash
docker exec -it ollama sh -lc 'echo $NVIDIA_VISIBLE_DEVICES'
```

This should output:

```
0
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

## Advanced Configuration

### Using a Different GPU

To use GPU 1 instead of GPU 0, edit `ollama-compose.yml`:

```yaml
environment:
  - NVIDIA_VISIBLE_DEVICES=1

gpus:
  - "device=1"
```

### Pinning by GPU UUID

For systems with mixed GPU models, pin by UUID for reliability:

```yaml
gpus:
  - "device=GPU-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
```

Find your GPU UUIDs with:

```bash
nvidia-smi -L
```

### Running Multiple Ollama Instances

To run a second Ollama instance on GPU 1 for increased throughput, create a copy of the compose file with:

- Different container name (`ollama-gpu1`)
- Different volume name (`ollama-gpu1`)
- GPU device set to `1`

## Additional Notes

- **Model Storage:** Models are stored in the `ollama` Docker volume and persist across container restarts
- **GPU Support:** The configuration includes GPU support pinned to device 0. Modify as needed for your setup
- **Host Access:** By default, Ollama is only accessible from other containers. To access it from your host machine, uncomment the `ports` section in the compose file
