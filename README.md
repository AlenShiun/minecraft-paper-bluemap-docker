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
2. Install [docker-nginx-proxy-autossl](https://github.com/AlenShiun/docker-nginx-proxy-autossl) for SSL termination and reverse proxy
3. Clone this repository
4. Download required plugins to `downloads/plugin/`:
   - `bluemap-5.7-paper.jar`
   - `EssentialsX-2.21.0.jar`
5. Copy the plugins from `downloads/plugin/` to `./plugins/`:
   ```bash
   mkdir -p plugins
   cp downloads/plugin/*.jar plugins/
   ```
   Note: The plugins directory will be mounted to the container's `/data/plugins` directory
6. Set up the required directory structure:
   ```
   .
   ├── docker-compose.yml
   ├── downloads/
   │   └── plugin/
   ├── plugins/       # Plugin files
   ├── nginx/
   │   ├── .htpasswd
   │   └── default.conf
   └── data/         # Created automatically
   ```
7. Configure your domain and email in docker-compose.yml
8. Generate .htpasswd file for basic authentication:
   ```bash
   docker run --rm -it httpd:alpine htpasswd -Bbn map YOUR_PASSWORD > nginx/.htpasswd
   ```
   Replace `YOUR_PASSWORD` with your desired password. The default username is `map`.
9. Start the services:
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
- `./plugins`: Active Minecraft plugins
- `./downloads/plugin`: Plugin download directory
- Nginx configurations mounted from `./nginx/`
