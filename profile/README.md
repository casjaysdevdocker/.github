# 🐳 CasjaysDevDocker

A curated collection of self-hostable Docker containers — built on a standardised foundation supporting Alpine, Debian, Ubuntu, RHEL-compatible, and other base images, designed for simplicity, consistency, and easy self-hosting.

---

## 📦 What's Here

Every repository in this organisation is a single containerised application:

| Category | Containers |
|----------|-----------|
| **Web & Proxy** | nginx, lighttpd, caddy, traefik, cherokee |
| **Databases** | mariadb, mysql, postgres, mongodb, couchdb, redis, valkey, sqlite |
| **Git & Dev** | gitea, forgejo, soft-serve, code, neovim, vim |
| **Media** | ampache, navidrome, icecast, mpd, ympd |
| **Cloud & Storage** | nextcloud, opencloud |
| **Languages & Runtimes** | nodejs, bun, deno, python, php |
| **Mail** | postfix, mailman |
| **Networking** | bind, ddns, tor, i2pd, ifconfig |
| **Utilities** | ntfy, gotify, apprise, aria2, transmission, pastebin, wttr, ssl-ca, tftpd, proftpd |
| **AI & LLMs** | ollama |
| **Desktop** | xfce4 |
| **CMS & Web Apps** | wordpress, jekyll, enclosed, opengist, webmin |
| **Downloads** | youtube-dl |

---

## 🏗️ How Containers Are Built

All images follow the same two-stage pattern regardless of base distro:

- **Stage 1** — distro bootstrap (Alpine, Debian, Ubuntu, RHEL-compatible, etc.): install `bash`, `ca-certificates`, and update trust store
- **Stage 2** — `FROM scratch`: copy the full rootfs, layer in the application, and configure via environment variables

Every container is generated and maintained using the **[gen-dockerfile](https://github.com/casjay-dotfiles/scripts)** tool, which produces consistent Dockerfiles, entrypoint scaffolding, and CI workflows across all repositories.

---

## 🚀 Quick Start

```bash
docker pull casjaysdev/<image-name>
docker run -d \
  -e TZ=America/New_York \
  -p <port>:<port> \
  --name <image-name> \
  casjaysdev/<image-name>
```

Each repository's `README.md` documents the available environment variables, ports, and volume mounts.

---

## 🔧 Common Environment Variables

All containers share a consistent set of environment variables:

| Variable | Default | Description |
|----------|---------|-------------|
| `TZ` | `America/New_York` | Container timezone |
| `HOSTNAME` | `casjaysdev-<name>` | Container hostname |
| `PORT` | service-specific | Primary service port |
| `ENV_PORTS` | service-specific | Additional exposed ports |
| `SERVICE_NAME` | image name | Name used for config/log paths |
| `CONTAINER_NAME` | image name | Internal container identifier |

---

## 📂 Registry

Images are published to:

- **Docker Hub:** `docker.io/casjaysdev/<name>`
- **Gitea Registry:** `git.casjay.pro/casjaysdevdocker/<name>`

---

## 👤 Maintainer

🤖 **casjay** — [GitHub](https://github.com/casjay) · [Website](https://casjay.pro)

---

## 📄 License

MIT / WTFPL — use freely.
