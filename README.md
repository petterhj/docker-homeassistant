# Container

```sh
$ docker-compose build
$ docker-compose up

$ docker ps
$ docker exec -it <container_id> bash
```

# Home Assistant

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


## Tellstick (Duo)

```sh
$ tellcore_events --device
```

```
# /etc/tellstick.conf
user="root"
group="root"
deviceNode="/dev/tellstick"
ignoreControllerConfirmation="false"
device {
  id=2
  name="Button"
  controller=0
  protocol="arctech"
  model="selflearning-switch"
  parameters {
    house="32439934"
    unit="16"
  }
}
```

```yaml
# configuration.yaml
tellstick:
  signal_repetitions: 3

switch:
  - platform: tellstick
```
```yaml
# automations.yaml
- id: button_off
  alias: 'Button off'
  description: ''
  trigger:
  - platform: state
    entity_id: switch.button
    to: 'off'
  condition: []
  action:
  - scene: scene.lighting_living_room_off
  - scene: scene.all_devices_off
  mode: single
- id: button_on
  alias: 'Button on'
  description: ''
  trigger:
  - platform: state
    entity_id: switch.button
    to: 'on'
  condition: []
  action:
  - scene: scene.new_scene
  mode: single
```


# Resources

* https://www.home-assistant.io/installation/alternative/#synology-nas
* https://github.com/home-assistant/docker/blob/master/Dockerfile
* https://github.com/taskinen/home-assistant
* https://pictogrammers.github.io/@mdi/font/5.3.45/
