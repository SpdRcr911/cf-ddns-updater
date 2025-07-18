## 🔍 Overview

Whenever your WAN IP changes, `cf‑ddns‑updater` will:

1. Fetch your current public IP (via ipify.org).  
2. Compare it to the last‑seen IP stored on disk.  
3. If different, send a Cloudflare API `PUT` to update your A record.  
4. Persist the new IP to avoid redundant updates.

Perfect for running in Docker on a Synology (or any Linux host) and scheduling hourly or daily.

## 🚀 Features

- **Zero external dependencies** beyond Python 3 and `requests`  
- Configurable entirely via environment variables  
- Safe: only calls Cloudflare when the IP actually changes  
- Docker‑ready with a simple `Dockerfile`  
- Mountable volume for persistent IP storage  

## 🛠️ Requirements

- Python 3.8 or newer  
- [requests](https://pypi.org/project/requests/) library  

## ⚙️ Configuration

Create a file named `.env` in the project root (or supply these as container env vars):

```dotenv
# Cloudflare API
CLOUDFLARE_API_TOKEN=your_token_here
CLOUDFLARE_ZONE_ID=xxxxxxxxxxxxxxxxxxxx
CLOUDFLARE_DNS_RECORD_ID=yyyyyyyyyyyyyyyyyyyy

# DNS
DNS_NAME=canyonlake.spdrcr911.net
DNS_TYPE=A
PROXIED=false

# Storage
STORAGE_FILE=/data/last_ip.txt
````

* **CLOUDFLARE\_API\_TOKEN** – scoped to “Zone.DNS\:Edit” on your zone
* **CLOUDFLARE\_ZONE\_ID** – find in your Cloudflare dashboard
* **CLOUDFLARE\_DNS\_RECORD\_ID** – the specific record’s ID
* **DNS\_NAME** – the full hostname to update
* **STORAGE\_FILE** – where your last IP is persisted inside the container

## 🔧 Installation & Usage

1. **Clone** this repo and install deps:

   ```bash
   git clone https://github.com/<your‑user>/cf-ddns-updater.git
   cd cf-ddns-updater
   pip install -r requirements.txt
   ```

2. **Configure** your `.env` (see above).

3. **Run** the updater:

   ```bash
   python3 update_ip.py
   ```

On first run it will create `last_ip.txt`. Subsequent runs only call Cloudflare if the IP has changed.

## 🐳 Docker

A `Dockerfile` is included for headless deployments:

```bash
# Build image
docker build -t cf-ddns-updater .

# Run container (detached)
docker run -d \
  --name cf-ddns-updater \
  --env-file .env \
  -v /host/path/data:/data \
  cf-ddns-updater
```

Then schedule this container to restart or run on a cron schedule (e.g. Synology Task Scheduler every hour).

## 📄 License

This project is open‑source under the MIT License. Feel free to fork, tweak, and contribute!