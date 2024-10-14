1: Install Docker
```
curl -sSL https://get.docker.com | sh
```
2: Allow default user to run Docker commands without sudo
```
sudo usermod -aG docker $USER
```
3: Logout and SSH back in 
```
exit
```
4: Install Docker Compose
```
sudo apt install docker-compose -y
```
5: Create the docker compose file.
```
nano docker-compose.yml
```
Paste the following into the yml file and save (Ctrl + x -> y & Enter):
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
      - ~/appdata/sabnzbd/config:/config
      - ~/media/downloads:/downloads #optional
      - ~/media/incomplete/:/incomplete-downloads #optional
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
      - ~/appdata/radarr:/config
      - ~/media/movies:/movies #optional
      - ~/media/downloads:/downloads
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
      - ~/appdata/sonarr:/config
      - ~/media/tv:/tv #optional
      - ~/media/downloads:/downloads
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
      - ~/appdata/overseerr/config:/config
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
      - ~/appdata/plex/config:/config
      - ~/media/tv:/tv
      - ~/media/movies:/movies
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
      - ~/appdata/adguard/work:/opt/adguardhome/work
      - ~/appdata/adguard/conf:/opt/adguardhome/conf
    restart: unless-stopped

  cloudflared:
    image: cloudflare/cloudflared
    container_name: cloudflare-tunnel
    restart: unless-stopped
    command: tunnel run
    environment:
      - TUNNEL_TOKEN=abcdefghijklmnopqrstuvwxyz0123456789 #Change to your cloudflare tunnel token
```
6. Run the Docker Compose command
```
docker compose up -d
```
7. Log into the various web services and configure.
```
http://SERVERADDRESS:8080 #Sabnzbd
http://SERVERADDRESS:7878 #Radarr
http://SERVERADDRESS:8989 #Sonarr
http://SERVERADDRESS:32400/web #Plex
http://SERVERADDRESS::5055 #Overseerr
http://SERVERADDRESS:3000 #AdguardHome
