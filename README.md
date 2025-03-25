# 🧪 Homelab Infrastructure

Welcome to my personal homelab repository! This setup is tailored to my needs, preferences, and environment. 
Everything you’ll find here is built the way I like it — opinionated by design.

This repo also documents my **GitOps journey** — starting small with Docker and Portainer Git integration, and gradually evolving. The long-term goal is to migrate key services to a Kubernetes cluster managed by **ArgoCD** for high availability and better infrastructure-as-code practices.

I'm always happy to chat, discuss improvements, or hear new ideas. Feel free to open an issue or a pull request if you have suggestions. That said, just because something works for you doesn’t mean it’ll fit here — I may not merge changes that don’t align with my goals.

You’re absolutely welcome to fork this repo and make it your own!

---

## 📂 Service Stacks

| Stack         | Description                                 |
|---------------|---------------------------------------------|
| [home-assistant](#home-assistant)         | Smart home automation platform. |
| [homepage](#homepage)                     | Custom dashboard with widgets, bookmarks, and service health. |
| [mediarr](#mediarr)                       | Media stack for automatic downloads and streaming. |
| [monitoring](#monitoring)                 | Metrics and observability stack. |
| [nginx-proxy-manager](#nginx-proxy-manager) | Reverse proxy and SSL management (soon migrating to Traefik). |
| [semaphore](#semaphore)                   | Web UI for automating Ansible and OpenTofu on Proxmox. |
| [vaultwarden](#vaultwarden)               | Self-hosted password manager backend. |

---

## ⚙️ How It Works

- 🐳 **Docker Compose** is used to define each stack, with environment variables stored in `.env` files (see each folder’s `sample.env`).
- 📦 **Portainer GitOps** watches the `main` branch and checks for updates **every 60 seconds**, automatically reconciling and redeploying changes.
- 🔁 **Dependabot** checks for updated Docker image and Github Actions versions **daily** and opens a PR with updated tags — this works smoothly with Portainer’s reconciliation loop.
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

I chose it because the config is a delarative .yaml file which makes sense with my GitOps shift.

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

➡️ [See directory](portainer/mediarr)

---

### Monitoring

Currently includes:

- **Prometheus**: Scrapes and stores metrics from my services.
- **Grafana**: Dashboards to visualize metrics and track system health.

Planned additions:
- **Loki**: Log aggregation system for Grafana.
- **OpenTelemetry**: For tracing and advanced observability across services.

➡️ [See directory](portainer/monitoring)

---

### Nginx Proxy Manager

Handles reverse proxying and SSL termination for internal services via Let's Encrypt. Super easy to use with a web UI. 
**Will be replaced with Traefik** soon, since Traefik’s declarative config makes more sense with my GitOps and declarative config shift.

➡️ [See directory](portainer/npm)

---

### Semaphore

Used to run **Ansible playbooks** through a web UI. I plan to also use it for **OpenTofu (Terraform fork)** to manage and automate provisioning 
and config of my **Proxmox machines** — making the entire infrastructure more reproducible and manageable from a single UI.

➡️ [See directory](portainer/semaphore)

---

### Vaultwarden

Self-hosted Bitwarden-compatible password manager. I use this to store all my credentials securely and access them across devices, synced via browser and mobile clients.

➡️ [See directory](portainer/vaultwarden)

---

## 🤝 Contributions

If you find this helpful or want to adapt it to your own setup, feel free to fork and customize. Suggestions are welcome, but just keep in mind this setup is built for *my* environment and use case.
