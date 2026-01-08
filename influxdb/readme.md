# InfluxDB Docker Compose Setup

This directory contains a Docker Compose configuration for running InfluxDB 3 Core as a containerized service.

This setup creates an InfluxDB service that:

- Persists database data in Docker volumes
- Includes health checks to ensure the service is running properly
- Exposes a configurable port for direct access from applications
- Uses InfluxDB 3 Core (latest OSS version) with Python processing engine enabled

## Prerequisites

- Docker and Docker Compose installed

## Setup Instructions

### Step 1: Start the InfluxDB Container

Start the InfluxDB service using Docker Compose:

```bash
docker compose -f influxdb-compose.yml up -d
```

The `-d` flag runs the container in detached mode (in the background).

### Step 2: Verify the Service

Check that the container is running:

```bash
docker ps | grep influxdb
```

You can also test the connection:

```bash
curl http://localhost:8181/health
```

## Using InfluxDB from Applications

Before connecting, configure the port mapping in `influxdb-compose.yml` (replace `xxx` with your desired host port).

### Connection Details

- **Host:** `localhost` (or `127.0.0.1`)
- **Port:** The port you configured in `influxdb-compose.yml` (default: 8181)
- **API Endpoint:** `http://localhost:<your-port>`

### Getting Started with InfluxDB 3 Core

After starting your InfluxDB 3 server, follow the [Get Started guide](https://docs.influxdata.com/influxdb/v3/get-started/) to:

1. Create an authorization token
2. Start writing data
3. Query data using SQL or InfluxQL
4. Process data using the built-in Python processing engine

### Using the InfluxDB CLI

You can use the built-in `influxdb3` CLI from within the container:

```bash
docker exec -it influxdb influxdb3 --help
```

## Additional Notes

- **Data Storage:** Database data is stored in the `influxdb_data` Docker volume and persists across container restarts
- **Plugins:** Python plugins are stored in the `influxdb_plugins` Docker volume
- **Port Configuration:** Configure the host port in `influxdb-compose.yml` by replacing `xxx` with your desired port number
- **Version:** This setup uses InfluxDB 3 Core, the latest open-source version with sub-10ms queries and unlimited cardinality
- **Query Languages:** Supports both SQL and InfluxQL queries
- **Compatibility:** Includes InfluxDB v1 and v2 compatibility APIs

## Recommended Tools

- **InfluxDB 3 Explorer UI**: Visualize, query, and manage your data with the standalone web interface
- **Telegraf**: Collect, transform, and send metrics from hundreds of sources directly to InfluxDB 3
- **Official Client Libraries**: Integrate InfluxDB 3 into your applications using supported libraries for Python, Go, JavaScript, and more

## Resources

- [InfluxDB 3 Core Documentation](https://docs.influxdata.com/influxdb/v3/)
- [InfluxDB Docker Hub](https://hub.docker.com/_/influxdb)
- [InfluxDB 3 Get Started Guide](https://docs.influxdata.com/influxdb/v3/get-started/)
