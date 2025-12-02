Folder structure - 
/home/user/docker/arr-stack/
│
├── gluetun/
├── qbittorrent/
│   ├── config/
│   └── downloads/
├── sonarr/
│   └── data/
├── radarr/
│   └── data/
├── prowlarr/
│   └── data/
└── config/ (Overseerr)

🧰 Requirements

Before using this docker-compose:

Make sure CIFS utilities are installed

sudo apt install cifs-utils


Create your NAS credentials and replace:

username=Username

password=Password

device: "//0.0.0.0/share"

Fill in your VPN provider environment variables inside the gluetun service.

⚙️ Docker Compose Overview
🔒 Gluetun (VPN Wrapper)

All containers use Gluetun as their network stack through:

network_mode: "service:gluetun"


This ensures every outbound connection is VPN-routed.

Exposed ports on Gluetun allow access to the other services’ web interfaces.

🌐 CIFS NAS Volume

The NAS share is mounted as a Docker volume:

volumes:
  network_nas:
    driver_opts:
      type: cifs
      o: "username=... ,password=...,uid=1000,gid=1000,file_mode=0777,dir_mode=0777"
      device: "//NAS-IP/share"


Mapped to /share/ inside qBittorrent, Sonarr, and Radarr.

🔄 Key Services
Service	Port	Description
qBittorrent	8085	Torrent client (behind VPN)
Sonarr	8989	TV automation
Radarr	7878	Movie automation
Prowlarr	9696	Indexer manager
Overseerr	5058	Media request system

All traffic is routed through Gluetun except their web UI ports exposed on the host.

🚀 Usage
Start the full stack
docker compose up -d

Stop the stack
docker compose down

View logs
docker logs gluetun -f

📝 Important Notes
1. Hostname requirement for Gluetun

The hostname field is necessary to allow external containers to connect to Gluetun:

hostname: gluetun


This is required by qBittorrent, Radarr, Sonarr, and others.

2. NAS Share Paths in Sonarr/Radarr/qBittorrent

Inside containers, your NAS media directory appears at:

/share/


Use this when configuring:

qBittorrent download location

Sonarr/Radarr root folders

Completed download removal

3. VPN Credentials

These must be filled in:

- VPN_SERVICE_PROVIDER=
- OPENVPN_USER=
- OPENVPN_PASSWORD=
- SERVER_COUNTRIES=


Consult the Gluetun documentation for provider-specific settings.

🧪 Testing Your Setup
Confirm containers use Gluetun’s network:
docker exec -it qbittorrent curl ifconfig.me


You should see the VPN IP, not your home IP.

Confirm NAS mount is accessible:
docker exec -it sonarr ls -la /share

⚖️ Legal Notice

This configuration is provided for personal, lawful, and ethical use only.
You are solely responsible for ensuring that your use of the services in this stack—such as qBittorrent, Sonarr, Radarr, Prowlarr, and Gluetun—complies with all applicable local, national, and international laws.

The authors and contributors of this configuration do not endorse or encourage the downloading, sharing, or distribution of copyrighted content without permission.
Any use of this stack to access or distribute illegal or copyrighted material may violate copyright laws and could result in civil or criminal penalties.

By using this configuration, you agree that:

You will use it only for legally obtained media.

You assume full responsibility for all activity performed through your network and devices.

The authors, maintainers, and distributors of this configuration are not liable for any misuse, data loss, damage, legal claims, or violations arising from its use.

If you are unsure whether a specific use is legal in your jurisdiction, you should consult a qualified legal professional.