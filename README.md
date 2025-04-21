# 🧪 Homelab Infrastructure

Welcome to my personal homelab repository! This setup is tailored to my needs, preferences, and environment.  
Everything you’ll find here is built the way I like it — opinionated by design.

This repo also documents my **GitOps journey** — starting small with Docker and Portainer Git integration, and gradually evolving.  
The long-term goal is to migrate key services to a Kubernetes cluster managed by **ArgoCD** for high availability and better infrastructure-as-code practices.

I'm always happy to chat, discuss improvements, or hear new ideas. Feel free to open an issue or a pull request if you have suggestions.  
That said, just because something works for you doesn’t mean it’ll fit here — I may not merge changes that don’t align with my goals.

You’re absolutely welcome to fork this repo and make it your own!

---

## 📂 Service Stacks

| Stack                      | Description                                                      |
|---------------------------|------------------------------------------------------------------|
| [home-assistant](#home-assistant)         | Smart home automation platform.                            |
| [homepage](#homepage)                     | Custom dashboard with widgets, bookmarks, and service health. |
| [mediarr](#mediarr)                       | Media stack for automatic downloads and streaming.         |
| [books](#books)                           | Automated ebook and audiobook download and organization.   |
| [monitoring](#monitoring)                 | Metrics and observability stack.                           |
| [databases](#databases-mysql--postgresql) | Centralized MySQL and PostgreSQL services with UI.         |
| [invoiceshelf](#invoiceshelf)             | Lightweight self-hosted invoice/estimate PDF generator.    |
| [nginx-proxy-manager](#nginx-proxy-manager) | Reverse proxy and SSL management (soon migrating to Traefik). |
| [semaphore](#semaphore)                   | Web UI for automating Ansible and OpenTofu on Proxmox.     |
| [vaultwarden](#vaultwarden)               | Self-hosted password manager backend.                      |

---

## ⚙️ How It Works

- 🐿 **Docker Compose** is used to define each stack, with environment variables stored in `.env` files (see each folder’s `sample.env`).
- 📦 **Portainer GitOps** watches the `main` branch and checks for updates **every 60 seconds**, automatically reconciling and redeploying changes.
- ↻ **Dependabot** checks for updated Docker image and GitHub Actions versions **daily** and opens a PR with updated tags — this works smoothly with Portainer’s reconciliation loop.
- ✅ GitHub Actions handle security scanning and YAML linting.

---

## 🚀 Deployment

All stacks are designed for deployment via **Portainer with Git integration**:

1. Create a Git-backed stack in Portainer.
2. Point it to the appropriate folder in this repository.
3. Copy `sample.env` to `prod.env` in Portainer if needed.
4. Portainer automatically syncs changes from the `main` branch.

---

## 🔐 Notes

- `prod.env` files are intentionally **not tracked** in this repo.
- Only `sample.env` files are provided for reference.
- Store secrets securely — never commit them.

---

## 📘 Service Details

### Home Assistant

Used primarily for small automations — motion lighting, device status alerts, etc. It's just getting started, but I plan to expand its capabilities.  
I may soon update the container configuration to enable additional hardware driver mappings for Zigbee, Bluetooth, or USB serial devices.

➡️ [See directory](portainer/home-assistant)

---

### Homepage

My central homelab dashboard. Displays the status of all services, useful bookmarks, and helpful widgets like weather and system status.  
It's the first place I check when accessing the homelab.

I chose it because the config is a declarative `.yaml` file, which fits well with my GitOps approach.

➡️ [See directory](portainer/homepage)

---

### Mediarr

A full media automation and streaming stack:

- **Prowlarr**: Indexer manager — centralizes and manages torrent/indexer configs.
- **Sonarr**: Automatically downloads and organizes TV shows.
- **Radarr**: Same as Sonarr, but for movies.
- **Bazarr**: Automatically fetches and manages subtitles for content handled by Sonarr and Radarr.
- **Jellyfin**: Media server for streaming movies and shows locally or remotely.
- **Jellyseerr**: Request interface for Jellyfin, like Overseerr but compatible with Jellyfin.
- **FlareSolverr**: Bypasses anti-bot protection for indexers and search engines (used by Prowlarr).
- **Glutun**: Manages VPN access for apps that need to tunnel traffic (e.g., qBittorrent).
- **qBittorrent**: Torrent client used for media downloads, often routed through VPN via Glutun.
- **Audiobookshelf**: Dedicated server for managing and streaming audiobooks.

➡️ [See directory](portainer/mediarr)

---

### Books

This stack is designed for **ebook and audiobook automation**. It’s a parallel to my Mediarr setup but tailored for book discovery, downloading, and listening.

- **LazyLibrarian**: Searches, grabs, and organizes ebooks and audiobooks. Works with multiple sources and metadata providers (Goodreads, Google Books, etc.).
- **Jackett**: Provides tracker support for LazyLibrarian by exposing APIs for various torrent sites.
- **qBittorrent-books**: Torrent client used specifically for books, routed through Gluetun for privacy.
- **Gluetun-books**: VPN container used as the network gateway for qBittorrent-books.
- **Audiobookshelf**: Audiobook server that indexes and streams downloaded content, including bookmarks, resume playback, metadata editing, and web-based/mobile clients.

This stack is deployed with proper volume mounts to ensure:
- LazyLibrarian can pass downloads to qBittorrent-books.
- qBittorrent writes completed downloads to a shared path accessible by Audiobookshelf.

➡️ [See directory](portainer/books)

---

### Monitoring

Currently includes:

- **Prometheus**: Scrapes and stores metrics from my services.
- **Grafana**: Dashboards to visualize metrics and track system health.
- **Uptime Kuma**: Service availability monitoring with beautiful dashboards and notifications for uptime.

Planned additions:
- **Loki**: Log aggregation system for Grafana.
- **OpenTelemetry**: For tracing and advanced observability across services.

➡️ [See directory](portainer/monitoring)

---

### Databases (MySQL + PostgreSQL)

This is a **centralized database stack** used by multiple containers that require a shared backend (such as Semaphore or other services).  
Instead of bundling DB containers inside each stack, I’ve opted for centralized services to simplify access and management.

- **MySQL**: Main relational database service (used by Semaphore, Uptime Kuma, etc.).
- **phpMyAdmin**: Web-based UI to manage users, databases, and queries (MySQL).
- **PostgreSQL**: Postgres database for services that require it.
- **pgAdmin**: Browser-based UI for managing PostgreSQL databases.

➡️ [See directory](portainer/mysql)

---

### Invoiceshelf

**Invoiceshelf** is a self-hosted tool designed to generate and manage invoices, estimates, and payment receipts as downloadable PDFs.  
It’s lightweight and ideal for freelancers or small teams who want full control over their billing without relying on cloud services.

Features include:
- Creation of invoices and estimates with customer data and line items.
- Generation of PDF documents for invoices and receipts.
- Support for multilingual/customizable email messages (PDF is attached, not linked).
- Basic branding and business settings (e.g., currency, language, logo).
- Sends documents to clients via email with the PDF attached.
- **Uses the centralized PostgreSQL service** to persist billing and user data.

No `.env` file is required — all configuration is done through container environment variables directly in the `docker-compose.yaml`.

➡️ [See directory](portainer/invoiceshelf)

---

### Nginx Proxy Manager

Handles reverse proxying and SSL termination for internal services via Let's Encrypt. Super easy to use with a web UI.  
**Will be replaced with Traefik** soon, since Traefik’s declarative config makes more sense with my GitOps workflow and improves maintainability.

➡️ [See directory](portainer/npm)

---

### Semaphore

Used to run **Ansible playbooks** through a web UI. I plan to also use it for **OpenTofu (Terraform fork)** to manage and automate provisioning  
and config of my **Proxmox machines** — making the entire infrastructure more reproducible and manageable from a single interface.

Note: I removed the MySQL container from this stack to instead connect it to the centralized MySQL service.

➡️ [See directory](portainer/semaphore)

---

### Vaultwarden

Self-hosted Bitwarden-compatible password manager. I use this to store all my credentials securely and access them across devices,  
synced via browser extensions and mobile apps.

➡️ [See directory](portainer/vaultwarden)

---

## 🤝 Contributions

If you find this helpful or want to adapt it to your own setup, feel free to fork and customize.  
Suggestions are welcome, but just keep in mind this setup is built for *my* environment and use case.
