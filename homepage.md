Folgende File-Struktur im Homepage Docker-Ordner für Homepage anlegen:
```
./config/bookmarks.yaml
./config/docker.yaml
./config/services.yaml
./config/settings.yaml
./config/widgets.yaml
```
Im "settings.yaml" legst du die grundsätzliche Strucktur der Seite fest:
[https://gethomepage.dev/latest/configs/settings](https://gethomepage.dev/latest/configs/settings)
```Yaml
---
#Titel, favicon  und Hintergrundbild (habe es mit diesen Settings etwas unscharf gemacht
title: DennisCloud Homepage
favicon: /images/DennisHomelab.ico
background:
 
  image: /images/background.jpg
  blur: sm # sm, md, xl...  https://tailwindcss.com/docs/backdrop-blur
  saturate: 100 # 0, 50, 100...  https://tailwindcss.com/docs/backdrop-saturate
  brightness: 50 # 0, 50, 75...  https://tailwindcss.com/docs/backdrop-brightness
  opacity: 30 # 0-100
#Im  Layout kannst du definieren wie die Kacheln aufgeteilt sind (ich habe sie in eine "row" genommen geht aber auch im style "column"
#Du kannst auch verschiedene Titel machen wie ich hier
layout:
  Infrastructure & Management:
    #tab: Management-Services
    icon: https://cdn-icons-png.flaticon.com/128/2177/2177461.png
    header: true
    style: row #oder row
    columns: 6 #wenn row dann hier column
    statusStyle: "dot"
  Development & Automation:
    #tab: Management-Services
    icon: https://cdn-icons-png.flaticon.com/128/4997/4997543.png
    header: true
    style: row
    columns: 5
    statusStyle: "dot"
```
im docker.yaml kannst du deine Docker API verbinden (dann kannst du states der docker checken) - oder im kubernetes.yaml wenn du kubernetes brauchst
![](https://appflowy.denniscloud.io/api/file_storage/f1683278-ac40-4561-820e-f26e54d4427a/v1/blob/cf324305%2D43b4%2D4a46%2D805f%2Db33f9d35b631/8qen8NUOVehobYQafZ2wJ7kkeaMmgx0RWXslbfykr-o=.png)
Das checken definierst du nicht hier - kommt dann im services.yaml
[https://gethomepage.dev/latest/configs/docker/](https://gethomepage.dev/latest/configs/docker/)
```
raspiantwo:
  host: <ip oder hostname>
  port: 2376 #Docker API Port: Port für non-ssl Verbindung 2375 und mit ssl 2376
dockertower:
  host: 192.168.1.23
  port: 2376

dockertower2:
  socket: /var/run/docker.sock #wenn homepage in einem Docker läuft kannst du den lokal so einbinden
```

Im services.yaml definierst du die Links und widgets für deine kacheln.
Alle verfügbarern widgets findest du hier:
[https://github.com/gethomepage/homepage/tree/main/docs/widgets/services](https://github.com/gethomepage/homepage/tree/main/docs/widgets/services)
proxmox: [https://github.com/gethomepage/homepage/blob/main/docs/widgets/services/proxmox.md](https://github.com/gethomepage/homepage/blob/main/docs/widgets/services/proxmox.md)
Die Icon Namen findest du hier: [https://github.com/homarr-labs/dashboard-icons/tree/main/png](https://github.com/homarr-labs/dashboard-icons/tree/main/png)
Weitere Icons: [https://pictogrammers.com/library/mdi/](https://pictogrammers.com/library/mdi/)
```
- Infrastructure & Management: # Titel 
    - Proxmox-1:
        icon: proxmox.svg
        href: ip oder hostname
        description: pve1 #Beischreibung für die Kachel
        ping:  ip oder hostname #Für Ping checks wenn es keine docker sind
        widget:
            type: proxmox
            url: 192.168.1.24 
            username: #proxmox username
            password: #proxmox passwort
            node: pve #nodename vom Proxmox host
     # Mit docker kannst du das auch als variabeln im .env mitgeben dann sieht man das nicht mit docker inspect etc. macht es etwas sicherer
    # Die variablen müssen mit "HOMEPAGE_VAR" beginnen
    - Proxmox-2:
        icon: proxmox.svg
        href: "{{HOMEPAGE_VAR_PROXMOX_URL}}"
        description: pve2
        ping: "{{HOMEPAGE_VAR_PROXMOX_IP}}"
        widget:
            type: proxmox
            url: "{{HOMEPAGE_VAR_PROXMOX_IP}}"
            username: "{{HOMEPAGE_VAR_PROXMOX_USER}}"
            password: "{{HOMEPAGE_VAR_PROXMOX_API_KEY}}"
            node: pve2
    - Synology:
        icon: synology-dsm.svg
        href: "{{HOMEPAGE_VAR_SYNOLOGY_URL}}"
        description: NAS
        ping: "{{HOMEPAGE_VAR_SYNOLOGY_IP}}"
        widget:
            type: diskstation
            url: "{{HOMEPAGE_VAR_SYNOLOGY_IP}}"
            username: "{{HOMEPAGE_VAR_SYNOLOGY_USER}}"
            password: "{{HOMEPAGE_VAR_SYNOLOGY_PW}}"
            #volume: volume_N # optional
            
  #Hier noch ein Beispiel wie du einen dockerstatus in der Kachel anzeigen lässt anstelle von ping
      - Git DenisCloud:
            icon: gitea.png
            href: "{{HOMEPAGE_VAR_GITEA_URL}}"
            description: Self hosted Git environment
            showStats: false
            server: dockertower2 #server wie du ihn im docker.yaml genannt hast
            container: gitea #name vom container
```

Um im header gewisse Informationen anzuzgeigen kannst du das widgets.yaml schreiben - vom lokalen docker host wo homepage drauf läuft
[https://gethomepage.dev/latest/configs/service-widgets](https://gethomepage.dev/latest/configs/service-widgets)

```
---
- logo:
    icon: /images/DennisHomelab.ico
- greeting:
    text_size: xl
    text: DennisCloud.io

- resources:
    label: CPU
    cpu: true
- resources:
    label: Memory
    memory: true
- resources:
    label: DockerTower
    disk: /mnt/data
- search:
    provider: duckduckgo
    target: _blank
    
- datetime:
    text_size: xl
    format:
      timeStyle: short
```