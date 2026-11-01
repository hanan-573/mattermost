# Mattermost Self-Hosted Setup (Docker + Caddy + Calls Plugin)

This repository contains a complete, production-ready Docker Compose configuration to host **Mattermost** with **Caddy** as a reverse proxy (for automatic SSL), a **PostgreSQL** database, and the **Mattermost Calls Plugin** (WebRTC) working seamlessly.

## 🚀 Features

- **Mattermost Team Edition:** Hosted via Docker.
- **Caddy Web Server:** Automatic HTTPS/SSL generation via Let's Encrypt.
- **PostgreSQL 13:** Reliable and persistent database.
- **Calls Plugin (WebRTC):** Fully configured for audio/video calls.
- **Custom Ports:** Uses ports `12000-12005` as per specific network requirements.
- **Persistent Storage:** Data is safely stored in Docker volumes.

## 🛠️ Prerequisites

Before deploying, ensure you have:
1. A Linux Server (Ubuntu 22.04+ recommended).
2. A purchased Domain (e.g., `yourdomain.com`).
3. **DNS Setup:** An `A` record pointing to your server's public IP (e.g., `mattermost.yourdomain.com` -> `YOUR_SERVER_IP`).
4. **Docker & Docker Compose installed** on the server.
5. **Firewall Ports Open:**
   - `80/tcp` (HTTP)
   - `443/tcp` (HTTPS)
   - `12000/tcp` (Mattermost Web UI)
   - `12001/udp` & `12001/tcp` (Mattermost Calls)

## 📂 Project Structure

```text
mattermost/
├── docker-compose.yml
├── caddy/
│   └── Caddyfile
└── volumes/               # Created automatically on first run
    ├── db/
    └── app/mattermost/



⚙️ Configuration Setup
1. docker-compose.yml
The compose file configures PostgreSQL, Mattermost, and Caddy. Note the port mappings for Mattermost Web (12000) and Calls (12001).

Make sure to change the MM_SERVICESETTINGS_SITEURL to your actual domain.

2. caddy/Caddyfile
Caddy handles automatic SSL. Replace the domain and email in this file:

caddyfile
{
    email your-email@example.com
}

mattermost.yourdomain.com {
    reverse_proxy mattermost:8065
    # Security headers...
}
3. Directory Permissions
Mattermost Docker container runs with UID 2000. Before starting, you must give permission to the volumes directory:

bash
mkdir -p volumes/app/mattermost
sudo chown -R 2000:2000 volumes/app/mattermost
🚦 How to Start & Stop the Project
Start the Project
To start all services in the background:

bash
docker compose up -d
Wait 15-20 seconds for Mattermost and Database to initialize.

Check Status
bash
docker compose ps
Ensure caddy, mattermost, and postgres show as Up.

Stop the Project (Safe Shutdown)
To safely stop the containers without deleting data:

bash
docker compose stop
Restart the Project
If you made changes to the docker-compose.yml or Caddyfile:

bash
docker compose down
docker compose up -d
View Logs (Troubleshooting)
To see live logs for Mattermost:

bash
docker compose logs -f mattermost
To see live logs for Caddy (SSL issues):

bash
docker compose logs -f caddy
📞 Calls Plugin Setup
The Calls plugin is pre-installed in Mattermost. To ensure it works perfectly:

Log in to Mattermost as an Admin.

Go to System Console > Plugins > Plugin Management and ensure Calls is Enabled.

Go to System Console > Plugins > Calls and set:

Enable Calls Plugin: true

RTC Server Address: 0.0.0.0

ICE Host Override: YOUR_SERVER_PUBLIC_IP (Crucial for WebRTC connection behind NAT).

In the channel settings (e.g., Main channel), enable calls.

💬 Channels Setup
Town Square: Automatically created by default for all new teams.

Main Channel: Create it manually via the UI (Click + next to Channels -> Create New Channel -> Name: main).

🔒 Security Notes
Change default PostgreSQL passwords in docker-compose.yml.

Keep your server and Docker images updated regularly (docker composose update)
