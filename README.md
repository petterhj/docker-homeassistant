# Container

```sh
$ docker-compose up

$ docker ps
$ docker exec -it <id> bash
```

## API

```sh
$ curl -X POST -H "Authorization: Bearer <access_token>" -H "Content-Type: application/json" <host>:8123/api/services/homeassistant/restart
```

# Resources

* https://www.home-assistant.io/installation/alternative/#synology-nas
* https://github.com/home-assistant/docker/blob/master/Dockerfile
* https://github.com/taskinen/home-assistant
