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

* https://github.com/kalkih/mini-graph-card


# Resources

* https://www.home-assistant.io/installation/alternative/#synology-nas
* https://github.com/home-assistant/docker/blob/master/Dockerfile
* https://github.com/taskinen/home-assistant
