# Home Assistant

Docker setup for running [Home Assistant](https://www.home-assistant.io/) with [PostgreSQL](https://www.postgresql.org/), [InfluxDB](https://www.influxdata.com/), [Grafana](https://grafana.com/), [Mosquitto](https://mosquitto.org/) and [Zigbee2MQTT](https://www.zigbee2mqtt.io/).

## Container

```sh
$ docker-compose up

$ docker-compose ps
$ docker exec -it <container_id> bash

$ docker pull homeassistant/home-assistant:stable # Update base image
$ docker-compose up --no-deps -d homeassistant
```

### Services

| Service | Port | Note |
| ------- | -----| ---- |
| **homeassistant** | `8123` | |
| **postgres** | `5234` | |
| **influxdb** | `8086`/`8083` | |
| **grafana** | `3000` | The grafana docker container runs on `user:group 472:472`. The persistent volume must therefore be `chmod` as that user: `chown -R 472:472 /<path>/.grafana`. |
| **mosquitto** | `1883` | |
| **zigbee2mqtt** | `8180` | |
| **esphome** | `6052` | |

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

#### Zigbee2MQTT

* https://www.zigbee2mqtt.io/guide/installation/02_docker.html
* https://www.zigbee2mqtt.io/guide/configuration/homeassistant.html

```yaml
# .zigbee2mqtt/configuration.yaml
homeassistant: true
permit_join: true

mqtt:
  base_topic: zigbee2mqtt
  server: mqtt://localhost

serial:
  adapter: deconz
  port: /dev/ttyACM0

frontend:
  port: 
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


## Harmony

```yaml
# configuration.yaml
sensor:
  - platform: template
    sensors:
      harmony_hub:
        friendly_name: "Current activity"
        value_template: '{{ state_attr("remote.harmony_hub", "current_activity") }}'
```

### Home Control

https://www.home-assistant.io/integrations/emulated_hue

> Logitech Harmony remotes cannot connect to this emulator via Android and iOS mobile applications because they require the physical button on the hub to be pressed. The MyHarmony desktop software must be used with the original cable to connect it, then “Scan for Devices”.

##### Synology

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


## Emulated Roku

https://www.home-assistant.io/integrations/emulated_roku/

```sh
tail -f home-assistant.log | grep roku
DEBUG (MainThread) [homeassistant.core] Bus:Handling <Event roku_command[L]: source_name=Home Assistant, type=keypress, key=Search>
```

```yaml
# automations.yaml
alias: Run NRK TV (Google TV)
trigger:
  - platform: event
    event_type: roku_command
    event_data:
      source_name: Home Assistant
      type: keypress
      key: Info
action:
  - service: media_player.select_source
    target:
      entity_id: media_player.google_tv_2
    data:
      source: no.nrk.tv
```

| Key             | Action                   |
| --------------- | ------------------------ |
| `Fwd`           |                          |
| `Rev`           |                          |
| `...`           |                          |
| `Back`          | Google TV: Kodi          |
| `Home`          | Google TV: Netflix       |
| `Info`          | Google TV: NRK TV        |
| `InstantReplay` | Google TV: Spotify       |
| `Search`        | Google TV: YouTube       |


## Keyboard Remote (Flirc)

https://www.home-assistant.io/integrations/keyboard_remote/

```yaml
# configuration.yaml
keyboard_remote:
  - device_name: 'flirc.tv flirc Keyboard'
  # - device_descriptor: '/dev/input/event0'
    type:
      - 'key_up'
      - 'key_down'
      # - 'key_hold'
```

```yaml
alias: 'Remote: Power'
description: ''
trigger:
  - platform: event
    event_type: keyboard_remote_command_received
    event_data:
      device_name: flirc.tv flirc Keyboard
      key_code: 31
      type: key_down
condition: []
action:
  - service: light.toggle
    target:
      device_id: d081d5962686e9904ebbfec152dc64e9
    data: {}
mode: single
```

```sh
# with `debug` logging enabled:
$ tail -f home-assistant.log | grep keyboard_remote
DEBUG (MainThread) [homeassistant.components.keyboard_remote] key event at 1647365376.381469, 31 (KEY_S), down
DEBUG (MainThread) [homeassistant.core] Bus:Handling <Event keyboard_remote_command_received[L]: key_code=31, type=key_down, device_descriptor=/dev/input/event0, device_name=flirc.tv flirc Keyboard>
DEBUG (MainThread) [homeassistant.components.keyboard_remote] key event at 1647365376.445474, 31 (KEY_S), up
DEBUG (MainThread) [homeassistant.core] Bus:Handling <Event keyboard_remote_command_received[L]: key_code=31, type=key_up, device_descriptor=/dev/input/event0, device_name=flirc.tv flirc Keyboard>
```


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
* https://github.com/timvancann/home-assistant-docker-compose
* https://gist.github.com/connor4312/f16544bcc5b48af345a94feedb5a0ee1
* https://github.com/home-assistant/docker/blob/master/Dockerfile
* https://github.com/taskinen/home-assistant
* https://pictogrammers.github.io/@mdi/font/5.3.45/
