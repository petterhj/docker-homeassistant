# Home Assistant

Docker image (https://github.com/petterhj/docker-homeassistant) for running [Home Assistant](https://www.home-assistant.io/) with [supervisord](http://supervisord.org/).

## Container

```sh
$ docker-compose up

$ docker-compose ps
$ docker exec -it <container_id> bash

$ docker pull homeassistant/home-assistant:stable # Update base image
$ docker-compose up --no-deps -d homeassistant
```

### Services

* **homeassistant**: `8023`
* **postgres**: `5234`
* **influxdb**: `8086` / `8083`
* **grafana**: `3000`  
  The grafana docker container runs on `user:group 472:472`. The persistent volume must therefor be `chmod` as that user: `chown -R 472:472 /<path>/.grafana`.

```yaml
// configuration.yaml

influxdb:
  host: localhost
  port: 8086
  database: !secret influxdb_database
  username: !secret influxdb_username
  password: !secret influxdb_password

recorder:
  db_url: postgresql://<username>:<password>@localhost/<database>
```

## API

```sh
$ curl -X POST \
    -H "Authorization: Bearer <access_token>" \
    -H "Content-Type: application/json" \
    <host>:8123/api/services/homeassistant/restart
```

## HACS

Install [in container](https://hacs.xyz/docs/installation/installation#home-assistant-container) (configured as an integration):

```sh
$ docker exec -it <container_id> bash
$ wget -q -O - https://install.hacs.xyz | bash -
```

### [mini-graph-card](https://github.com/kalkih/mini-graph-card)

```yaml
type: 'custom:mini-graph-card'
name: Media cabinet
icon: 'mdi:thermometer'
entities:
  - entity: sensor.media_cabinet_speed_level
    name: Fan speed
    show_graph: false
  - entity: sensor.media_cabinet_temp0
    name: Receiver
    show_state: true
    show_indicator: true
  - entity: sensor.media_cabinet_temp1
    name: Router
    show_state: true
    show_indicator: true
  - entity: sensor.media_cabinet_temp2
    name: TV
    show_state: true
    show_indicator: true
  - entity: sensor.media_cabinet_temp3
    name: Ambient
    show_state: true
    show_indicator: true
hours_to_show: 24
hour24: true
decimals: 0
```

## Harmony (Home Control)

https://www.home-assistant.io/integrations/emulated_hue

> Logitech Harmony remotes cannot connect to this emulator via Android and iOS mobile applications because they require the physical button on the hub to be pressed. The MyHarmony desktop software must be used with the original cable to connect it, then “Scan for Devices”.


#### Synology

nginx config: `/etc/nginx/nginx.conf` (replaced on restart)  
nginx template config: `/usr/syno/share/nginx/`

`WWW_Main.mustache`
```nginx
location = /description.xml {
  proxy_pass http://<host>:<port>/description.xml;
}
location /api {
    proxy_pass http://<host>:<port>/api;
}
```
```sh
sudo synoservice --restart nginx
```

Pairing works through app (Android).


https://github.com/hass-emulated-hue/core

> This virtual bridge runs at HTTP port 80 and HTTPS port 443 on your local network. These ports can not be changed as the HUE infrastructure requires them to be at these defaults.

* https://www.reddit.com/r/homeassistant/comments/g8giur/emulated_hue_synology_homeassistant/


## Spotify

* https://www.home-assistant.io/integrations/spotify/
* https://github.com/fondberg/spotcast [HACS]
* https://github.com/custom-cards/spotify-card [HACS]

```yaml
# configuration.yaml
spotify:
  client_id: !secret sp_client_id
  client_secret: !secret sp_client_secret

spotcast:
  sp_dc: !secret primary_sp_dc
  sp_key: !secret primary_sp_key
```


# Resources

* https://www.home-assistant.io/installation/alternative/#synology-nas
https://github.com/timvancann/home-assistant-docker-compose
* https://github.com/home-assistant/docker/blob/master/Dockerfile
* https://github.com/taskinen/home-assistant
* https://pictogrammers.github.io/@mdi/font/5.3.45/
