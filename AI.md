# Docker Container Repository Specification

## Repository Overview

**Repository:** casjaysdevdocker  
**Maintainer:** CasjaysDev <docker-admin@casjaysdev.pro>  
**License:** WTFPL / MIT  
**Purpose:** Collection of containerized applications using standardized Alpine-based templates

---

## Repository Structure

This repository contains multiple Docker container projects organised as subdirectories. Each project follows a consistent structure and naming convention.

### Directory Layout

```
casjaysdevdocker/
├── ampache/           # Media streaming server
├── apprise/           # Notification service
├── aria2/             # Download manager
├── bind/              # DNS server
├── bun/               # JavaScript runtime
├── caddy/             # Web server
├── code/              # VS Code server
├── couchdb/           # NoSQL database
├── ddns/              # Dynamic DNS client
├── deno/              # JavaScript runtime
├── docker/            # Docker-in-Docker
├── enclosed/          # Encrypted note sharing
├── forgejo/           # Git forge
├── gitea/             # Git service
├── gotify/            # Push notification server
├── i2pd/              # I2P router
├── icecast/           # Audio streaming
├── ifconfig/          # IP info service
├── jekyll/            # Static site generator
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
├── proftpd/           # FTP server
├── python/            # Python runtime
├── redis/             # In-memory database
├── soft-serve/        # Git server
├── sqlite/            # SQL database
├── ssl-ca/            # SSL certificate authority
├── tftpd/             # TFTP server
├── tor/               # Tor relay
├── traefik/           # Reverse proxy
├── transmission/      # BitTorrent client
├── valkey/            # Redis fork
├── vim/               # Text editor
├── webmin/            # Web admin interface
├── wordpress/         # CMS platform
├── wttr/              # Weather service
├── xfce4/             # Desktop environment
├── ympd/              # MPD web client
├── youtube-dl/        # Video downloader
└── tools/             # Shared tools & scripts
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
│   └── docker.yaml        # Build and push workflow
├── .gitignore             # Git ignore patterns
├── Dockerfile             # Container build definition
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
        │   └── init.d/            # Runtime init scripts
        └── share/template-files/
            ├── config/            # Configuration templates
            ├── data/              # Default data
            └── defaults/          # Default files
```

---

## gen-dockerfile Tool

All containers are built using the **gen-dockerfile** tool (`bin/gen-dockerfile` in casjay-dotfiles). It generates standardised Dockerfiles, `.env.scripts`, workflow YAML, `pkmgr` scripts, and `rootfs/` scaffolding.

### Usage

```bash
gen-dockerfile [options] [template] [Dockerfile]
gen-dockerfile --dir ./myapp alpine
gen-dockerfile --dir ./myapp --nginx --tag 1.25
gen-dockerfile --dir ./existing-project --update
gen-dockerfile --dir ./myapp actions
```

### Templates (positional argument or `--template`)

| Template | Description | Base image |
|----------|-------------|------------|
| `alpine` | Default Alpine Linux (default) | `casjaysdev/alpine` |
| `arch` / `archlinux` | Arch Linux | `menci/archlinuxarm` |
| `debian` | Debian | `debian` |
| `ubuntu` | Ubuntu | `ubuntu` |
| `rhel` / `almalinux` / `rockylinux` / `centos` | AlmaLinux/RHEL family | `almalinux` |
| `web` | Alpine + xorg + x11-apps | scratch/web template |
| `xorg` | Alpine + xorg | xorg template |
| `scratch` | Scratch-based | scratch template |

### CLI Flags

| Flag | Description |
|------|-------------|
| `--dir <path>` | Working directory (default: `$PWD`) |
| `--template <name>` | Override template type |
| `--registry <url>` | Registry provider URL (e.g. `https://docker.io`) |
| `--org <[git\|reg:]name>` | Override org/namespace — prefix `git:` or `reg:` to scope to one system, bare value sets both |
| `--user <[git\|reg:]name>` | Alias for `--org` |
| `--repo <[git\|reg:]name>` | Override repo/image name — same scope prefix rules |
| `--tag <version>` | Image version tag (default: `latest`) |
| `--add-tags <tags>` | Comma-separated additional tags (or `USE_DATE` for auto date tag) |
| `--distro-name <image>` | Base image pull URL (overrides `ENV_PULL_URL`) |
| `--distro-version <tag>` | Base image tag (overrides `ENV_DISTRO_TAG`) |
| `--startup` | Create a container start script only |
| `--nogit` | Skip `git init` in the new project dir |
| `--dockerfile` | Regenerate Dockerfile only — skip other files |
| `--update` | Re-run gen-dockerfile on an existing project to regenerate files |
| `--init` | Init mode |
| `--x11` | Enable X11/xorg support |
| `--ports <ports>` | Additional EXPOSE ports |
| `--pkmgr` | Generate `pkmgr` script only and exit |
| `--apache` | Add Apache2 packages + setup |
| `--nginx` | Add nginx packages + setup |
| `--mysql` / `--mariadb` | Add MariaDB packages + setup |
| `--postgres` | Add PostgreSQL packages + setup |
| `--php` | Add PHP packages + setup |
| `--application <name>` | Add a templatemgr application install step |
| `--alpine` / `--almalinux` / `--archlinux` / `--debian` / `--ubuntu` | Shortcut distro flags |
| `--force` | Overwrite existing files without prompting |
| `--silent` | Suppress non-error output |
| `--debug` | Enable `set -x` tracing |
| `--no-color` | Disable colour output |

### Special Subcommands

```bash
gen-dockerfile --dir ./myapp actions
```

Creates `.gitea/workflows/build.yml` (or `build.$version.yml` for versioned tags) from the existing `Dockerfile`. The example workflow in `.github/example/.gitea/workflows/docker.yaml` is the canonical reference pattern for the legacy hand-written style; `gen-dockerfile actions` generates `build.yml` with the same structure.

### Template Resolution

Templates are loaded from the first match:

1. `$GEN_DOCKERFILE_CONFIG_DIR/templates/<name>.template` (user override)
2. `$CASJAYSDEVDIR/templates/dockerfiles/<name>.template` (installed scripts tree)

`template_options.source` is sourced after `__set_variables` runs, allowing template-specific variable overrides.

---

## `.env.scripts` File

Generated at project root; sourced by gen-dockerfile and by the CI workflow at build time.

### Current Variable Names

```bash
# - - - - - - - - - - - - - - - - - - - - - - - - -
##@Version           :  202601291955-git
# ...
# - - - - - - - - - - - - - - - - - - - - - - - - -

# Entrypoint Settings
DOCKER_ENTYPOINT_PORTS_WEB="${DOCKER_ENTYPOINT_PORTS_WEB}"
DOCKER_ENTYPOINT_PORTS_SRV="${DOCKER_ENTYPOINT_PORTS_SRV}"
DOCKER_ENTYPOINT_HEALTH_APPS="$DOCKER_ENTYPOINT_HEALTH_APPS"
DOCKER_ENTYPOINT_HEALTH_ENDPOINTS="$DOCKER_ENTYPOINT_HEALTH_ENDPOINTS"
# - - - - - - - - - - - - - - - - - - - - - - - - -
# Dockerfile info
ENV_DOCKERFILE="Dockerfile"
# ENV_REGISTRY_REPO: Registry repository/image name
ENV_REGISTRY_REPO="myapp"
ENV_USE_TEMPLATE="alpine"
# - - - - - - - - - - - - - - - - - - - - - - - - -
# Maintainer info
ENV_REGISTRY_ORG="casjaysdevdocker"
ENV_VENDOR="CasjaysDev"
ENV_AUTHOR="CasjaysDev"
ENV_MAINTAINER="CasjaysDev <docker-admin@casjaysdev.pro>"
# - - - - - - - - - - - - - - - - - - - - - - - - -
# Repository URLs (Full URLs)
# ENV_GIT_REPO_URL: Complete Git repository URL for source code
ENV_GIT_REPO_URL="https://github.com/casjaysdevdocker/myapp"
# ENV_REGISTRY_URL: Registry provider base URL
ENV_REGISTRY_URL="https://docker.io"
# - - - - - - - - - - - - - - - - - - - - - - - - -
# Push Configuration
# ENV_REGISTRY_PUSH: Complete push destination (registry/org/repo)
ENV_REGISTRY_PUSH="casjaysdevdocker/myapp"
# ENV_IMAGE_TAG: Default tag for the image
ENV_IMAGE_TAG="latest"
# ENV_ADD_TAGS: Additional tags, comma-separated (USE_DATE = auto date tag)
ENV_ADD_TAGS=""
# - - - - - - - - - - - - - - - - - - - - - - - - -
# Additional push destinations (if needed)
ENV_ADD_IMAGE_PUSH=""
# - - - - - - - - - - - - - - - - - - - - - - - - -
# Pull Configuration
# ENV_PULL_URL: Source image to pull from (base image)
ENV_PULL_URL="casjaysdev/alpine"
# ENV_DISTRO_TAG: Tag for the pull source image
ENV_DISTRO_TAG="${IMAGE_VERSION}"
# - - - - - - - - - - - - - - - - - - - - - - - - -
# Port exposure
SERVICE_PORT=""
EXPOSE_PORTS=""
# - - - - - - - - - - - - - - - - - - - - - - - - -
# Language runtime version (go, php, rust, ruby, etc)
LANG_VERSION=""
# - - - - - - - - - - - - - - - - - - - - - - - - -
# Runtime versions
PHP_VERSION="system"
NODE_VERSION="system"
NODE_MANAGER="system"
# - - - - - - - - - - - - - - - - - - - - - - - - -
ENV_PACKAGES=""
```

### Variable Reference

| Variable | Purpose |
|----------|---------|
| `ENV_REGISTRY_REPO` | Image name in the registry |
| `ENV_REGISTRY_ORG` | Organisation/namespace in the registry |
| `ENV_REGISTRY_URL` | Registry base URL (e.g. `https://docker.io`) |
| `ENV_REGISTRY_PUSH` | Full push path `org/repo` (registry host prepended if not `docker.io`) |
| `ENV_ADD_IMAGE_PUSH` | Extra push destinations |
| `ENV_GIT_REPO_URL` | Full Git repository URL |
| `ENV_USE_TEMPLATE` | Template name to use (alpine, debian, etc.) |
| `ENV_PULL_URL` | Base image to pull from |
| `ENV_DISTRO_TAG` | Tag for the base image |
| `ENV_IMAGE_TAG` | Default image tag (default: `latest`) |
| `ENV_ADD_TAGS` | Additional comma-separated tags; `USE_DATE` auto-generates a `YYMM` tag |
| `ENV_PACKAGES` | Space-separated package list to install |
| `ENV_VENDOR` / `ENV_AUTHOR` / `ENV_MAINTAINER` | Label metadata |
| `SERVICE_PORT` | Primary exposed port |
| `EXPOSE_PORTS` | Additional exposed ports |
| `PHP_VERSION` / `NODE_VERSION` / `NODE_MANAGER` | Runtime versions |
| `DOCKER_ENTYPOINT_PORTS_WEB` | Web ports passed to entrypoint |
| `DOCKER_ENTYPOINT_PORTS_SRV` | Service ports passed to entrypoint |
| `DOCKER_ENTYPOINT_HEALTH_APPS` | Apps to health-check |
| `DOCKER_ENTYPOINT_HEALTH_ENDPOINTS` | Endpoints to health-check |

### Legacy Variable Auto-Migration

`gen-dockerfile --update` (and on every run) calls `__migrate_env_script` which automatically renames old variable names to the current ones. Old names that are no longer used:

| Old Name | Current Name |
|----------|-------------|
| `ENV_IMAGE_NAME` | `ENV_REGISTRY_REPO` |
| `ENV_IMAGE_PUSH` | `ENV_REGISTRY_PUSH` |
| `ENV_HUB_BASE` | `ENV_REGISTRY_URL` |
| `ENV_ORG_NAME` | `ENV_REGISTRY_ORG` |

Never use the old names in new `.env.scripts` files.

---

## Dockerfile Template Structure

### Build Arguments

| Argument | Description | Default |
|----------|-------------|---------|
| `IMAGE_NAME` | Container name | (from `ENV_REGISTRY_REPO`) |
| `IMAGE_REPO` | Full push path `org/repo` | (computed) |
| `IMAGE_VERSION` | Image version | `latest` |
| `BUILD_DATE` | Build timestamp | YYYYMMDDHHMM |
| `BUILD_VERSION` | Build version (same as BUILD_DATE) | YYYYMMDDHHMM |
| `GIT_COMMIT` | Git SHA at build time | (from CI) |
| `LANGUAGE` | System locale | `en_US.UTF-8` |
| `TIMEZONE` | System timezone | `America/New_York` |
| `SERVICE_PORT` | Primary service port | (from `.env.scripts`) |
| `EXPOSE_PORTS` | Additional ports | (from `.env.scripts`) |
| `PHP_VERSION` | PHP version | `system` |
| `NODE_VERSION` | Node.js version | `system` |
| `NODE_MANAGER` | Node manager | `system` |
| `PULL_URL` | Base image | `casjaysdev/alpine` |
| `DISTRO_VERSION` | Base image tag | `${IMAGE_VERSION}` |
| `WWW_ROOT_DIR` | Web root | `/usr/local/share/httpd/default` |

### Generated Dockerfile Structure

#### Stage 1: Build Stage

```dockerfile
FROM tianon/gosu:latest AS gosu
FROM ${PULL_URL}:${DISTRO_VERSION} AS build

# ... ARG declarations ...

ENV SHELL="/bin/sh"
ENV PATH="${PATH}"
ENV TZ="${TIMEZONE}"
ENV LANG="${LANGUAGE}"
ENV TERM="xterm-256color"
ENV HOSTNAME="casjaysdevdocker-${IMAGE_NAME}"

COPY ./rootfs/. /
RUN pkmgr update; pkmgr install bash

ENV SHELL="/bin/bash"
SHELL ["/bin/bash", "-c"]
COPY --from=gosu /usr/local/bin/gosu /usr/local/bin/gosu

# Setup scripts run in order:
# 00-init.sh      - Initialize base system
# 01-system.sh    - Configure system settings
# 02-packages.sh  - Install/configure packages (after PACK_LIST install)
# 03-files.sh     - Copy/modify files
# 04-users.sh     - Create users/groups
# 05-custom.sh    - Custom application setup
# 06-post.sh      - Post-installation tasks
# 07-cleanup.sh   - Clean up temporary files
```

#### Stage 2: Final Image

```dockerfile
FROM scratch

# ... ARG and LABEL declarations ...

ENV SHELL="/bin/bash"
ENV TZ="${TIMEZONE}"
ENV LANG="${LANGUAGE}"
ENV TERM="xterm-256color"
ENV PORT="${SERVICE_PORT}"
ENV HOSTNAME="casjaysdev-${IMAGE_NAME}"
ENV CONTAINER_NAME="${IMAGE_NAME}"

COPY --from=build /. /

VOLUME [ "/config", "/data" ]
EXPOSE ${SERVICE_PORT} ${ENV_PORTS}
STOPSIGNAL SIGRTMIN+3
ENTRYPOINT [ "tini", "-p", "SIGTERM", "--", "/usr/local/bin/entrypoint.sh" ]
HEALTHCHECK --start-period=10m --interval=5m --timeout=15s \
  CMD [ "/usr/local/bin/entrypoint.sh", "healthcheck" ]
```

### Container Labels

All containers include OpenContainers standard labels:

```dockerfile
LABEL maintainer="CasjaysDev <docker-admin@casjaysdev.pro>"
LABEL org.opencontainers.image.vendor="CasjaysDev"
LABEL org.opencontainers.image.authors="CasjaysDev"
LABEL org.opencontainers.image.description="Containerized version of ${IMAGE_NAME}"
LABEL org.opencontainers.image.title="${IMAGE_NAME}"
LABEL org.opencontainers.image.created="${BUILD_DATE}"
LABEL org.opencontainers.image.version="${BUILD_VERSION}"
LABEL org.opencontainers.image.source="https://github.com/casjaysdevdocker/${IMAGE_NAME}"
LABEL org.opencontainers.image.documentation="https://github.com/casjaysdevdocker/${IMAGE_NAME}"
LABEL org.opencontainers.image.revision="${GIT_COMMIT}"
```

---

## Common Package List

Standard packages included in most containers (Alpine template):

```
bash-completion git curl wget sudo unzip iproute2 ssmtp openssl jq tzdata
mailcap ncurses util-linux pciutils usbutils coreutils binutils findutils
grep rsync zip tini py3-pip procps net-tools sed gawk attr readline lsof
less shadow certbot ca-certificates
```

---

## Setup Scripts Convention

Build-time scripts in `rootfs/root/docker/setup/` run inside the Docker build layer.

### 00-init.sh
- Initialize base directory structure
- Set up template directories
- Create initial environment

### 01-system.sh
- Configure APK/apt/yum repositories
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

```
/usr/local/bin/entrypoint.sh
├─ Load functions from /usr/local/etc/docker/functions/entrypoint.sh
├─ Set up environment variables (sources /root/env.sh, /usr/local/etc/docker/env/*.sh, /config/env/*.sh)
├─ Create /config and /data volumes
├─ Run ALL init scripts from /usr/local/etc/docker/init.d/ (sorted order) via __start_init_scripts
├─ Handle healthcheck command
└─ Execute main application
```

### Init.d Scripts — CRITICAL RULES

**Each service in a repo gets its own numbered init.d script. Never merge or remove them.**

- `__start_init_scripts` iterates and sources **every** `*.sh` file in `init.d/` in sort order.
- Multi-process repos have **one script per service**. Example for a repo running three daemons:
  ```
  init.d/01-named.sh     — BIND/named DNS server
  init.d/02-nginx.sh     — nginx web front-end
  init.d/03-php-fpm.sh   — PHP-FPM for web UI
  ```
- **Migration task = UPDATE each script to the canonical pattern, never delete services.**
- The canonical pattern is `.github/example/rootfs/usr/local/etc/docker/init.d/04-example.sh`.

### Required Variables in Every Init.d Script

```bash
SERVICE_NAME="myapp"           # used for PID files, log dirs, config dirs
EXEC_CMD_BIN='myapp'          # daemon binary (single-quoted — expanded later)
EXEC_CMD_ARGS=''               # daemon arguments (single-quoted)
EXEC_PRE_SCRIPT=''             # pre-start script (single-quoted)
SERVICE_USES_PID=''            # '' for long-running daemons; 'no' for config-only steps
IS_WEB_SERVER="no"             # 'yes' sets RESET_ENV and default port 80
IS_DATABASE_SERVICE="no"
USES_DATABASE_SERVICE="no"
DATABASE_SERVICE_TYPE="sqlite" # custom|sqlite|redis|postgres|mariadb|mysql|couchdb|mongodb|supabase
RUNAS_USER="root"
```

### Required Hook Functions in Every Init.d Script

All eight must be defined (even if they just `return 0`):

```bash
__run_precopy()              # runs before copying /config to /etc
__execute_prerun()           # runs before service starts
__run_pre_execute_checks()   # validation before exec; non-zero aborts start
__update_conf_files()        # update config files (sed replacements, etc.)
__pre_execute()              # final setup before exec
__post_execute()             # background post-start tasks
__pre_message()              # message to show before exec
__update_ssl_conf()          # SSL certificate setup
__create_service_env()       # generates /config/env/$SERVICE_NAME.sh template
__run_start_script()         # builds and runs the exec script
__run_secure_function()      # chmod 600 on credential files
```

### PID Sentinel Check (CRITICAL)

Every init.d script **must** guard on the correct sentinel file:

```bash
# Correct — dot prefix, no double underscore
if [ ! -f "/run/.start_init_scripts.pid" ]; then
  echo "__start_init_scripts function hasn't been Initialized" >&2
  SERVICE_IS_RUNNING="no"
  __script_exit 1
fi
```

The file is `/run/.start_init_scripts.pid` — a leading dot, no underscores in the filename portion. Any other form silently skips the guard.

### Directory Variables

```bash
DATA_DIR="/data/$SERVICE_NAME"         # persistent data
CONF_DIR="/config/$SERVICE_NAME"       # persistent config
ETC_DIR="/etc/$SERVICE_NAME"           # runtime etc (synced from CONF_DIR)
LOG_DIR="/data/logs/$SERVICE_NAME"     # logs
TMP_DIR="/tmp/$SERVICE_NAME"
RUN_DIR="/run/$SERVICE_NAME"
ROOT_FILE_PREFIX="/config/secure/auth/root"
USER_FILE_PREFIX="/config/secure/auth/user"
```

### Volume Mounts

- `/config` — Persistent configuration files
- `/data` — Persistent application data

---

## CI/CD Integration

### Gitea Actions Workflow

Located at `.gitea/workflows/docker.yaml` (canonical example). Key structure:

```yaml
name: example
on: push

jobs:
  release-example:
    runs-on: act_runner
    container:
      image: catthehacker/ubuntu:act-latest
    env:
      RUNNER_TOOL_CACHE: /toolcache
    steps:
      - name: Checkout
        uses: actions/checkout@v3

      - name: Set up QEMU
        uses: docker/setup-qemu-action@v2

      - name: Get Meta
        id: meta
        run: |
          repo_version="$(git describe --tags --always)"
          repo_version="${repo_version#v}"
          docker_org="${GITHUB_REPOSITORY%%/*}"
          repo_name="${GITHUB_REPOSITORY#*/}"
          repo_name="${repo_name#docker-}"
          docker_tag="${DOCKER_TAG:-latest}"
          docker_hub="${DOCKER_HUB:-docker.io}"
          printf 'DATE_TAG=%s\n' "$(date +'%y%m')" >> "$GITHUB_OUTPUT"
          printf 'REPO_VERSION=%s\n' "$repo_version" >> "$GITHUB_OUTPUT"
          printf 'DOCKER_ORG=%s\n' "$docker_org" >> "$GITHUB_OUTPUT"
          printf 'DOCKER_TAG=%s\n' "$docker_tag" >> "$GITHUB_OUTPUT"
          printf 'DOCKER_HUB=%s\n' "$docker_hub" >> "$GITHUB_OUTPUT"
          printf 'REPO_NAME=%s\n' "$repo_name" >> "$GITHUB_OUTPUT"

      - name: Set up Docker BuildX
        uses: docker/setup-buildx-action@v2

      - name: Login to DockerHub
        uses: docker/login-action@v2
        with:
          password: ${{ secrets.DOCKER_TOKEN }}
          username: ${{ secrets.DOCKER_USERNAME }}
          registry: ${{ steps.meta.outputs.DOCKER_HUB }}

      - name: Build and push
        uses: docker/build-push-action@v4
        with:
          context: .
          file: ./Dockerfile
          platforms: |
            linux/amd64
            linux/arm64
          push: true
          build-args: |
            IMAGE_NAME=${{ steps.meta.outputs.REPO_NAME }}
            BUILD_DATE=$(date -u +'%Y%m%d%H%M')
            BUILD_VERSION=$(date -u +'%Y%m%d%H%M')
            GIT_COMMIT=${{ github.sha }}
            TIMEZONE=America/New_York
            LANGUAGE=en_US.UTF-8
            LICENSE=WTFPL
            TZ=America/New_York
          tags: |
            ${{ steps.meta.outputs.DOCKER_HUB }}/${{ steps.meta.outputs.DOCKER_ORG }}/${{ steps.meta.outputs.REPO_NAME }}:${{ steps.meta.outputs.DATE_TAG }}
            ${{ steps.meta.outputs.DOCKER_HUB }}/${{ steps.meta.outputs.DOCKER_ORG }}/${{ steps.meta.outputs.REPO_NAME }}:${{ steps.meta.outputs.DOCKER_TAG }}
```

`gen-dockerfile actions` regenerates this as `.gitea/workflows/build.yml` (or `build.$version.yml`) from the existing Dockerfile.

---

## Package Manager Wrapper (pkmgr)

The `pkmgr` script in `rootfs/usr/local/bin/pkmgr` provides a unified interface:

```bash
pkmgr update          # Update package lists
pkmgr install <pkg>   # Install packages
pkmgr remove <pkg>    # Remove packages
pkmgr clean           # Clean package cache
```

Automatically detects and uses the appropriate package manager (apk, apt-get, dnf, pacman).

---

## Development Workflow

### Creating a New Container

```bash
# 1. Generate scaffolding
gen-dockerfile --dir ./myapp alpine

# 2. Customise .env.scripts (set ENV_REGISTRY_REPO, ENV_REGISTRY_ORG, ports, etc.)

# 3. Customise setup scripts in rootfs/root/docker/setup/

# 4. Add init.d runtime script (copy from .github/example)

# 5. Build and test
docker build -t myapp .
docker run -it myapp

# 6. Commit and push to trigger CI/CD
```

### Updating an Existing Container

```bash
# Regenerate Dockerfile and related files from current .env.scripts
gen-dockerfile --dir ./myapp --update

# Regenerate workflow file only
gen-dockerfile --dir ./myapp actions
```

### Adding a New Container from Scratch

1. `gen-dockerfile --dir /path/to/new-container --nogit alpine`
2. Edit `.env.scripts` — set `ENV_REGISTRY_REPO`, `ENV_REGISTRY_PUSH`, `ENV_GIT_REPO_URL`
3. Copy `04-example.sh` from `.github/example/` as the starting point for init.d scripts
4. Add application-specific setup in `rootfs/root/docker/setup/05-custom.sh`

---

## Project Categories

### Web Servers
- nginx, lighttpd, caddy

### Databases
- mysql, mariadb, postgres, mongodb, couchdb, redis, valkey, sqlite

### Development Tools
- code, neovim, vim, nodejs, python, php, deno, bun

### Container Tools
- docker, podman

### Git Services
- gitea, forgejo, soft-serve, opengist

### Media Services
- ampache, navidrome, mpd, ympd, icecast, youtube-dl

### Networking
- bind, traefik, tor, i2pd, ddns, ifconfig

### Communication
- mailman, postfix, gotify, ntfy, apprise

### Cloud Services
- nextcloud, wordpress

### Utilities
- tools, transmission, aria2, tftpd, ssl-ca, wttr, xfce4, webmin

---

## Resources

- **Docker Hub:** <https://hub.docker.com/u/casjaysdevdocker>
- **Git Repository:** <https://github.com/casjaysdevdocker>
- **gen-dockerfile Tool:** `/usr/local/bin/gen-dockerfile` (source: `casjay-dotfiles/scripts/bin/gen-dockerfile`)
- **Template Source:** `casjay-dotfiles/scripts/templates/dockerfiles/`
- **Example Project:** `.github/example/`
