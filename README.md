# Minecraft Server with Paper, BlueMap & Nginx

This repository contains a Docker Compose configuration for running a Minecraft server with Paper, BlueMap web interface, and secure Nginx reverse proxy.

## Features

- Paper Minecraft server
- BlueMap web interface with basic authentication
- Nginx reverse proxy with SSL support
- Geyser support for Bedrock clients
- Whitelist enabled for security

## Services

### Minecraft Server (`mc`)
- Using `itzg/minecraft-server` image
- Paper server type
- Ports:
  - `25565`: Java Edition
  - `19132/udp`: Bedrock Edition (via Geyser)
  - `8123`: BlueMap web interface (internal)
- Whitelisted players: Configure in docker-compose.yml

### Nginx (`nginx`)
- Reverse proxy for BlueMap web interface
- Basic authentication enabled
- SSL support via Let's Encrypt
- Domain: Configure in docker-compose.yml

## Setup

1. Ensure Docker and Docker Compose are installed
2. Clone this repository
3. Set up the required directory structure:
   ```
   .
   ├── docker-compose.yml
   ├── downloads/
   │   └── plugin/
   ├── nginx/
   │   ├── .htpasswd
   │   └── default.conf
   └── data/           # Created automatically
   ```
4. Configure your domain and email in docker-compose.yml
5. Generate .htpasswd file for basic authentication:
   ```bash
   docker run --rm -it httpd:alpine htpasswd -Bbn map YOUR_PASSWORD > nginx/.htpasswd
   ```
   Replace `YOUR_PASSWORD` with your desired password. The default username is `map`.
6. Start the services:
   ```bash
   docker compose up -d
   ```

## Configuration

### Nginx
- Protected by basic authentication
- Proxies BlueMap web interface
- Configuration file: `nginx/default.conf`
- Authentication file: `nginx/.htpasswd`

### Networks
- Uses external `nginx-network` for container communication
- This allows integration with other nginx-proxy setups

## Access

- Minecraft Java Edition: Connect to your configured domain on port 25565
- Minecraft Bedrock Edition: Connect to your configured domain on port 19132
- BlueMap Web Interface: Access via HTTPS on your configured domain
  - Protected by basic authentication

## Whitelist

The server has whitelist enabled for security. Configure whitelisted players in the `WHITELIST` environment variable in `docker-compose.yml`.

## Volumes

- `./data`: Minecraft server data
- `./downloads/plugin`: Plugin directory
- Nginx configurations mounted from `./nginx/`
