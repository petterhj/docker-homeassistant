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

# Telldus

https://www.home-assistant.io/integrations/tellstick/

```yaml
# /config/configuration.yaml
tellstick:
  # host: 127.0.0.1
  host: core-tellstick
  port: [50800, 50801]
  signal_repetitions: 3
```

```sh
$ tellcore_controllers          # Found 1 controller(s)
$ tellcore_tool --list-devices  # tellcore.library.TelldusError: Could not connect to the Telldus Service (-6)
$ tellcore_events --all
$ tellcore_tool --on 1          # tellcore.library.TelldusError: TellStick not found (-1
```

|     | Class | Protocol | Model | House | Unit | Group |     |
| --- | ----- | -------- | ----- | ----- | ---- | ----- | --- |
| RMT 1.1 | `command` | `arctech` | `selflearning` | `23562102` | `1` | `0` | Tak |
| RMT 1.2 | `command` | `arctech` | `selflearning` | `23562102` | `2` | `0` | Vindu | 
| RMT 1.3 | `command` | `arctech` | `selflearning` | `23562102` | `3` | `0` | Hylle |
| RMT 1.4 | `command` | `arctech` | `selflearning` | `23562102` | `4` | `0` | - |
| RMT 2.1 | `command` | `arctech` | `selflearning` | `23562102` | `5` | `0` | Kjøkkenbord |
| RMT 2.2 | `command` | `arctech` | `selflearning` | `23562102` | `6` | `0` | - |
| RMT 2.3 | `command` | `arctech` | `selflearning` | `23562102` | `7` | `0` | Gang |
| RMT 2.4 | `command` | `arctech` | `selflearning` | `23562102` | `8` | `0` | - |
| RMT 3.1 | `command` | `arctech` | `selflearning` | `23562102` | `9` | `0` | - |
| RMT 3.2 | `command` | `arctech` | `selflearning` | `23562102` | `10` | `0` | - |
| RMT 3.3 | `command` | `arctech` | `selflearning` | `23562102` | `11` | `0` | - |
| RMT 3.4 | `command` | `arctech` | `selflearning` | `23562102` | `12` | `0` | - |
| RMT 4.1 | `command` | `arctech` | `selflearning` | `23562102` | `13` | `0` | - |
| RMT 4.2 | `command` | `arctech` | `selflearning` | `23562102` | `14` | `0` | - |
| RMT 4.3 | `command` | `arctech` | `selflearning` | `23562102` | `15` | `0` | - |
| RMT 4.4 | `command` | `arctech` | `selflearning` | `23562102` | `16` | `0` | - |
| RMT All | `command` | `arctech` | `selflearning` | `23562102` | `1` | `1` |
| BTN | `command` | `arctech` | `selflearning` | `32439934` | `16` | `0` |
| BTN | `command` | `arctech` | `selflearning` | `32439934` | `16` | `0` |

# Resources

* https://www.home-assistant.io/installation/alternative/#synology-nas
* https://github.com/home-assistant/docker/blob/master/Dockerfile
* https://github.com/taskinen/home-assistant
