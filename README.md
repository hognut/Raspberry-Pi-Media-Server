# docker-compose
Docker-Compose install and setup for a Rpi media server.


```
services:
  sabnzbd:
    image: lscr.io/linuxserver/sabnzbd:latest
    container_name: sabnzbd
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Etc/UTC
    volumes:
      - /home/steven/appdata/sabnzbd/config:/config
      - /home/steven/media/downloads:/downloads #optional
      - /home/steven/media/incomplete/:/incomplete-downloads #optional
    ports:
      - 8080:8080
    restart: unless-stopped

  radarr:
    image: lscr.io/linuxserver/radarr:latest
    container_name: radarr
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Etc/UTC
    volumes:
      - /home/steven/appdata/radarr:/config
      - /home/steven/media/movies:/movies #optional
      - /home/steven/media/incomplete:/downloads #optional
    ports:
      - 7878:7878
    restart: unless-stopped

  sonarr:
    image: lscr.io/linuxserver/sonarr:latest
    container_name: sonarr
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Etc/UTC
    volumes:
      - /home/steven/appdata/sonarr:/config
      - /home/steven/media/tv:/tv #optional
      - /home/steven/media/incomplete:/downloads #optional
    ports:
      - 8989:8989
    restart: unless-stopped

  overseerr:
    image: lscr.io/linuxserver/overseerr:latest
    container_name: overseerr
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Etc/UTC
    volumes:
      - /home/steven/appdata/overseerr/config:/config
    ports:
      - 5055:5055
    restart: unless-stopped

  plex:
    image: lscr.io/linuxserver/plex:latest
    container_name: plex
    network_mode: host
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Etc/UTC
      - VERSION=docker
      - PLEX_CLAIM= #optional
    devices:
      - /dev/dri:dev/dri
    volumes:
      - /home/steven/appdata/plex/config:/config
      - /home/steven/media/tv:/tv
      - /home/steven/media/movies:/movies
    restart: unless-stopped

  adguardhome:
    image: adguard/adguardhome
    container_name: adguardhome
    ports:
      - 53:53/tcp
      - 53:53/udp
      - 784:784/udp
      - 853:853/tcp
      - 3000:3000/tcp
      - 80:80/tcp
      - 443:443/tcp
    volumes:
      - /home/steven/appdata/adguard/work:/opt/adguardhome/work
      - /home/steven/appdata/adguard/conf:/opt/adguardhome/conf
    restart: unless-stopped    cloudflared: 
    image: cloudflare/cloudflared 
    container_name: cloudflare-tunnel 
    restart: unless-stopped 
    command: tunnel run 
    environment: 
      - TUNNEL_TOKEN=abcdefghijklmnopqrstuvwxyz0123456789
```
