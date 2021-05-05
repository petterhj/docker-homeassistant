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

### https://github.com/kalkih/mini-graph-card

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


# Resources

* https://www.home-assistant.io/installation/alternative/#synology-nas
* https://github.com/home-assistant/docker/blob/master/Dockerfile
* https://github.com/taskinen/home-assistant
* https://pictogrammers.github.io/@mdi/font/5.3.45/
