# Docker Container Repository Specification

## Repository Overview

**Repository:** casjaysdevdocker  
**Maintainer:** CasjaysDev <docker-admin@casjaysdev.pro>  
**License:** WTFPL / MIT  
**Purpose:** Collection of containerized applications using standardized Alpine-based templates

---

## Repository Structure

This repository contains **71 Docker container projects** organized as subdirectories. Each project follows a consistent structure and naming convention.

### Directory Layout

```
casjaysdevdocker/
├── ampache/           # Media streaming server
├── apprise/           # Notification service
├── aria2/             # Download manager
├── bind/              # DNS server
├── blueonyx/          # Server management
├── buildah/           # Container build tool
├── bun/               # JavaScript runtime
├── caddy/             # Web server
├── cherokee/          # Web server
├── code/              # VS Code server
├── commitment/        # Git commit tool
├── coolify/           # Self-hosting platform
├── couchdb/           # NoSQL database
├── ddns/              # Dynamic DNS client
├── deno/              # JavaScript runtime
├── dictd/             # Dictionary server
├── docker/            # Docker-in-Docker
├── enclosed/          # Encrypted note sharing
├── forgejo/           # Git forge
├── gitea/             # Git service
├── gohttpserver/      # HTTP file server
├── gotify/            # Push notification server
├── i2pd/              # I2P router
├── icecast/           # Audio streaming
├── ifconfig/          # IP info service
├── inn/               # NNTP server
├── jekyll/            # Static site generator
├── lenpaste/          # Pastebin service
├── lighttpd/          # Web server
├── mailman/           # Mailing list manager
├── mariadb/           # SQL database
├── mongodb/           # NoSQL database
├── mpd/               # Music player daemon
├── mysql/             # SQL database
├── navidrome/         # Music streaming
├── neovim/            # Text editor
├── nextcloud/         # Cloud storage
├── nginx/             # Web server
├── nodejs/            # Node.js runtime
├── ntfy/              # Notification service
├── ollama/            # LLM runtime
├── opencloud/         # Cloud platform
├── opengist/          # Gist clone
├── pastebin/          # Pastebin service
├── php/               # PHP runtime
├── podman/            # Container runtime
├── postfix/           # Mail server
├── postgres/          # SQL database
├── proftp/            # FTP server
├── proftpd/           # FTP server
├── python/            # Python runtime
├── rarbg/             # Torrent indexer
├── redis/             # In-memory database
├── remotely/          # Remote desktop
├── soft-serve/        # Git server
├── sqlite/            # SQL database
├── squidguard/        # Web content filter
├── ssl-ca/            # SSL certificate authority
├── stikked/           # Pastebin
├── tftpd/             # TFTP server
├── theHarvester/      # OSINT tool
├── tools/             # Shared tools & scripts
├── tor/               # Tor relay
├── tor-browser/       # Tor browser
├── traefik/           # Reverse proxy
├── transmission/      # BitTorrent client
├── valkey/            # Redis fork
├── vim/               # Text editor
├── webmin/            # Web admin interface
├── wordpress/         # CMS platform
├── wttr/              # Weather service
├── xfce4/             # Desktop environment
├── ympd/              # MPD web client
└── youtube-dl/        # Video downloader
```

---

## Standard Project Structure

Each project directory contains:

```
project-name/
├── .dockerignore          # Docker build exclusions
├── .env.scripts           # Environment variables for gen-dockerfile
├── .git/                  # Git repository
├── .gitattributes         # Git attributes
├── .gitea/workflows/      # CI/CD workflows (Gitea Actions)
├── .gitignore             # Git ignore patterns
├── Dockerfile             # Container build definition
├── Jenkinsfile            # Jenkins CI pipeline (legacy)
├── LICENSE.md             # License information
├── README.md              # Project documentation
└── rootfs/                # Container filesystem overlay
    ├── root/docker/setup/ # Build-time setup scripts
    │   ├── 00-init.sh
    │   ├── 01-system.sh
    │   ├── 02-packages.sh
    │   ├── 03-files.sh
    │   ├── 04-users.sh
    │   ├── 05-custom.sh
    │   ├── 06-post.sh
    │   └── 07-cleanup.sh
    └── usr/local/
        ├── bin/
        │   ├── entrypoint.sh      # Container entrypoint
        │   └── pkmgr              # Package manager wrapper
        ├── etc/docker/
        │   ├── functions/
        │   │   └── entrypoint.sh  # Entrypoint functions
        │   └── init.d/            # Init scripts
        └── share/template-files/
            ├── config/            # Configuration templates
            ├── data/              # Default data
            └── defaults/          # Default files
```

---

## Dockerfile Template System

All containers are built using the **gen-dockerfile** tool, which generates standardized multi-stage Dockerfiles based on Alpine Linux.

### Generated Dockerfile Structure

#### Stage 1: Build Stage
```dockerfile
FROM tianon/gosu:latest AS gosu
FROM alpine:latest AS build

# Install packages
RUN pkmgr install ${PACK_LIST}

# Run setup scripts in order:
# 00-init.sh      - Initialize base system
# 01-system.sh    - Configure system settings
# 02-packages.sh  - Install/configure packages
# 03-files.sh     - Copy/modify files
# 04-users.sh     - Create users/groups
# 05-custom.sh    - Custom application setup
# 06-post.sh      - Post-installation tasks
# 07-cleanup.sh   - Clean up temporary files
```

#### Stage 2: Final Image
```dockerfile
FROM scratch

COPY --from=build /. /

VOLUME [ "/config", "/data" ]
EXPOSE ${SERVICE_PORT} ${ENV_PORTS}
ENTRYPOINT [ "tini", "-p", "SIGTERM", "--", "/usr/local/bin/entrypoint.sh" ]
HEALTHCHECK CMD [ "/usr/local/bin/entrypoint.sh", "healthcheck" ]
```

### Standard Build Arguments

| Argument | Description | Default |
|----------|-------------|---------|
| `IMAGE_NAME` | Container name | (varies) |
| `BUILD_DATE` | Build timestamp | YYYYMMDDHHMM |
| `LANGUAGE` | System locale | en_US.UTF-8 |
| `TIMEZONE` | System timezone | America/New_York |
| `SERVICE_PORT` | Primary service port | (varies) |
| `EXPOSE_PORTS` | Additional ports | (varies) |
| `PHP_VERSION` | PHP version | system |
| `NODE_VERSION` | Node.js version | system |
| `PULL_URL` | Base image | casjaysdev/alpine |
| `DISTRO_VERSION` | Alpine version | latest |

### Standard Environment Variables

```bash
ENV SHELL="/bin/bash"
ENV TZ="${TIMEZONE}"
ENV LANG="${LANGUAGE}"
ENV TERM="xterm-256color"
ENV HOSTNAME="casjaysdev-${IMAGE_NAME}"
ENV WWW_ROOT_DIR="/usr/local/share/httpd/default"
```

---

## Common Package List

Standard packages included in most containers:

```
bash-completion git curl wget sudo unzip iproute2 ssmtp openssl jq tzdata 
mailcap ncurses util-linux pciutils usbutils coreutils binutils findutils 
grep rsync zip tini py3-pip procps net-tools sed gawk attr readline lsof 
less shadow certbot ca-certificates
```

---

## Setup Scripts Convention

### 00-init.sh
- Initialize base directory structure
- Set up template directories
- Create initial environment

### 01-system.sh
- Configure APK repositories
- Set up system settings
- Configure locales and timezone

### 02-packages.sh
- Install application-specific packages
- Configure package managers (pip, npm, etc.)
- Set up language runtimes

### 03-files.sh
- Copy configuration files
- Set file permissions
- Create symlinks

### 04-users.sh
- Create application users
- Set up user directories
- Configure user permissions

### 05-custom.sh
- Application-specific installation
- Download/compile software
- Custom configuration

### 06-post.sh
- Post-installation configuration
- Initialize databases
- Generate default configs

### 07-cleanup.sh
- Remove build dependencies
- Clean package caches
- Remove temporary files

---

## Entrypoint System

### Entrypoint Script Flow

```bash
/usr/local/bin/entrypoint.sh
├─ Load functions from /usr/local/etc/docker/functions/
├─ Set up environment variables
├─ Create /config and /data volumes
├─ Run init scripts from /usr/local/etc/docker/init.d/
├─ Handle healthcheck command
└─ Execute main application
```

### Volume Mounts

- `/config` - Persistent configuration files
- `/data` - Persistent application data

---

## CI/CD Integration

### Gitea Actions Workflow

Located at `.gitea/workflows/docker.yaml`:

```yaml
name: Build and Push Docker Image
on:
  push:
    branches: [main, master]
  pull_request:
    branches: [main, master]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
      - name: Setup Docker Buildx
      - name: Login to Registry
      - name: Build and Push
```

### Jenkins Pipeline (Legacy)

Some projects include `Jenkinsfile` for Jenkins CI integration.

---

## Package Manager Wrapper (pkmgr)

The `pkmgr` script provides a unified interface for package management:

```bash
pkmgr update          # Update package lists
pkmgr install <pkg>   # Install packages
pkmgr remove <pkg>    # Remove packages
pkmgr clean           # Clean package cache
```

Automatically detects and uses the appropriate package manager (apk, apt, yum, etc.)

---

## Generated Alpine Template Example

The `gen-dockerfile` tool was executed with:

```bash
gen-dockerfile --dir /tmp/fjwuenfng/alpine alpine
```

### Generated Files

```
/tmp/fjwuenfng/alpine/
├── .dockerignore
├── .env.scripts
├── .gitattributes
├── .gitea/workflows/docker.yaml
├── .gitignore
├── Dockerfile
├── LICENSE.md
├── README.md
└── rootfs/
    ├── root/docker/setup/
    │   ├── 00-init.sh
    │   ├── 01-system.sh
    │   ├── 02-packages.sh
    │   ├── 03-files.sh
    │   ├── 04-users.sh
    │   ├── 05-custom.sh
    │   ├── 06-post.sh
    │   └── 07-cleanup.sh
    └── usr/local/
        ├── bin/
        │   ├── entrypoint.sh
        │   └── pkmgr
        ├── etc/docker/
        │   ├── functions/entrypoint.sh
        │   └── init.d/
        └── share/template-files/
            ├── config/env/
            │   ├── default.sample
            │   └── examples/alpine/
            ├── data/
            └── defaults/
```

### Key Dockerfile Features

1. **Multi-stage build** - Separates build from runtime
2. **Bash shell** - Replaces default sh with bash
3. **Tini init** - Proper signal handling
4. **Health checks** - Built-in container health monitoring
5. **Security** - Uses gosu for proper privilege dropping
6. **Reproducibility** - All setup scripted and versioned

---

## Container Labels

All containers include OpenContainers standard labels:

```dockerfile
LABEL org.opencontainers.image.vendor="CasjaysDev"
LABEL org.opencontainers.image.authors="CasjaysDev"
LABEL org.opencontainers.image.description="Containerized version of ${IMAGE_NAME}"
LABEL org.opencontainers.image.source="https://github.com/casjaysdevdocker/${IMAGE_NAME}"
LABEL org.opencontainers.image.documentation="https://github.com/casjaysdevdocker/${IMAGE_NAME}"
```

---

## Project Categories

### Web Servers
- nginx, lighttpd, caddy, cherokee

### Databases
- mysql, mariadb, postgres, mongodb, couchdb, redis, valkey, sqlite

### Development Tools
- code, neovim, vim, nodejs, python, php, deno, bun

### Container Tools
- docker, podman, buildah

### Git Services
- gitea, forgejo, soft-serve, opengist

### Media Services
- ampache, navidrome, mpd, ympd, icecast, youtube-dl

### Networking
- bind, traefik, tor, i2pd, ddns, ifconfig

### Communication
- mailman, postfix, gotify, ntfy, apprise

### Cloud Services
- nextcloud, coolify, opencloud, wordpress

### Security
- ssl-ca

### Utilities
- tools, transmission, aria2, tftpd, dictd, stikked, lenpaste, pastebin, commitment, enclosed, theHarvester, rarbg, squidguard, webmin, wttr, xfce4

---

## Development Workflow

### Creating a New Container

1. Run gen-dockerfile:
   ```bash
   gen-dockerfile --dir ./myapp myapp
   ```

2. Customize setup scripts in `rootfs/root/docker/setup/`

3. Add application-specific configuration

4. Build and test:
   ```bash
   docker build -t myapp .
   docker run -it myapp
   ```

5. Commit and push to trigger CI/CD

---

## Environment Variable Configuration

Each container supports configuration via `.env.scripts`:

```bash
# Versions
##@Version           :  202601291955-git

# Entrypoint Settings
DOCKER_ENTYPOINT_PORTS_WEB=""
DOCKER_ENTYPOINT_PORTS_SRV=""
DOCKER_ENTYPOINT_HEALTH_APPS=""
DOCKER_ENTYPOINT_HEALTH_ENDPOINTS=""

# Application specific vars
# ...
```

---

## Common Issues and Solutions

### Bash Integration
All containers replace `/bin/sh` with bash for consistency and feature support.

### Package Management
The `pkmgr` wrapper abstracts differences between Alpine (apk), Debian (apt), and RedHat (yum).

### Init System
Tini is used as PID 1 to handle zombie processes and signal forwarding properly.

### Permissions
Gosu is used instead of su/sudo for proper privilege dropping without TTY issues.

---

## Repository Statistics

- **Total Projects:** 71
- **Total Files/Directories (depth 2-3):** 2,419
- **Base Image:** Alpine Linux
- **Template Tool:** gen-dockerfile
- **CI/CD:** Gitea Actions + Jenkins (legacy)
- **Registry:** hub.docker.com/casjaysdevdocker

---

## Resources

- **Docker Hub:** https://hub.docker.com/casjaysdevdocker/
- **Git Repository:** https://github.com/casjaysdevdocker/
- **gen-dockerfile Tool:** /usr/local/bin/gen-dockerfile
- **Template Source:** /usr/local/share/CasjaysDev/scripts/templates/dockerfiles/

---

*This specification was generated based on repository analysis and the gen-dockerfile alpine template.*
