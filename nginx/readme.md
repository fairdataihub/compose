# Nginx Docker Compose Setup

This directory contains a Docker Compose configuration for running [Nginx](https://nginx.org/) as a containerized web server and reverse proxy.

## Overview

This setup creates an Nginx service that:

- Serves static files and acts as a reverse proxy
- Includes health checks to ensure the service is running properly
- Supports custom configuration files
- Exposes a configurable port for web access
- Uses the lightweight Alpine Linux image

## Prerequisites

- Docker and Docker Compose installed

## Setup Instructions

### Step 1: Configure Port

Before starting the service, configure the following in `nginx-compose.yml`:

- **HTTP Port:** Replace `xxx` in the ports section with your desired host port (e.g., `80:80`)
- **HTTPS Port:** (Optional) Uncomment and configure the HTTPS port if you need SSL support

### Step 2: (Optional) Custom Configuration

If you need custom Nginx configuration:

1. **Custom nginx.conf:**
   - Create a `nginx.conf` file in this directory
   - Uncomment the `nginx.conf` volume mount in the compose file

2. **Server configurations:**
   - Create a `conf.d` directory
   - Add your server configuration files (e.g., `default.conf`)
   - Uncomment the `conf.d` volume mount in the compose file

3. **Static files:**
   - Create an `html` directory
   - Add your static files (HTML, CSS, JS, etc.)
   - Uncomment the `html` volume mount in the compose file

### Step 3: Start the Nginx Container

Start the Nginx service using Docker Compose:

```bash
docker compose -f nginx-compose.yml up -d
```

The `-d` flag runs the container in detached mode (in the background).

### Step 4: Verify the Service

Check that the container is running:

```bash
docker ps | grep nginx
```

You can also test the service by opening a browser and visiting:
```
http://localhost:<your-configured-port>
```

You should see the default Nginx welcome page (if using default configuration).

## Basic Configuration Examples

### Serving Static Files

Create an `html` directory and add your static files:

```bash
mkdir html
echo "<h1>Hello from Nginx!</h1>" > html/index.html
```

Then uncomment the html volume mount in `nginx-compose.yml`.

### Reverse Proxy Configuration

Create a `conf.d/default.conf` file:

```nginx
server {
    listen 80;
    server_name localhost;

    location / {
        proxy_pass http://your-backend-service:3000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

Then uncomment the `conf.d` volume mount in `nginx-compose.yml`.

### Multiple Server Blocks

Create multiple configuration files in `conf.d/`:

```nginx
# conf.d/app1.conf
server {
    listen 80;
    server_name app1.localhost;

    location / {
        proxy_pass http://app1-service:3000;
    }
}

# conf.d/app2.conf
server {
    listen 80;
    server_name app2.localhost;

    location / {
        proxy_pass http://app2-service:4000;
    }
}
```

## Using Nginx as a Reverse Proxy

Nginx can proxy requests to other Docker services. To connect Nginx to other services:

1. **Create a shared network:**
   ```yaml
   networks:
     app-network:
       driver: bridge
   ```

2. **Add the network to both Nginx and your services:**
   ```yaml
   services:
     nginx:
       networks:
         - app-network
     your-service:
       networks:
         - app-network
   ```

3. **Configure Nginx to proxy to the service:**
   ```nginx
   location / {
       proxy_pass http://your-service:port;
   }
   ```

## Common Use Cases

### Static Website Hosting
Serve static HTML, CSS, and JavaScript files directly from Nginx.

### Reverse Proxy
Route requests to backend services (Node.js, Python, etc.) based on path or hostname.

### Load Balancing
Distribute traffic across multiple backend instances.

### SSL Termination
Handle HTTPS connections and forward to HTTP backends.

### API Gateway
Route API requests to different microservices based on paths.

## Additional Notes

- **Port Configuration:** Configure the host port in `nginx-compose.yml` by replacing `xxx` with your desired port number
- **Default Configuration:** If no custom configuration is mounted, Nginx uses its default configuration
- **Static Files:** The default document root is `/usr/share/nginx/html` inside the container
- **Configuration Reload:** After changing configuration files, reload Nginx with: `docker exec nginx nginx -s reload`
- **Logs:** View Nginx logs with: `docker compose -f nginx-compose.yml logs -f`
- **Image:** Uses the official `nginx:alpine` image for a smaller footprint
- **Production Use:** For production, configure proper SSL certificates, security headers, and access controls

## Testing the Configuration

Test your Nginx configuration before reloading:

```bash
docker exec nginx nginx -t
```

If the test passes, reload Nginx:

```bash
docker exec nginx nginx -s reload
```

## Viewing Logs

View access logs:
```bash
docker exec nginx tail -f /var/log/nginx/access.log
```

View error logs:
```bash
docker exec nginx tail -f /var/log/nginx/error.log
```

Or use Docker Compose:
```bash
docker compose -f nginx-compose.yml logs -f
```

For more information, visit the [Nginx documentation](https://nginx.org/en/docs/).
