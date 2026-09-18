# smart-stuff — MQTT smart-home firmware fleet

Index/landing page for a set of independent Arduino/ESP firmware projects,
each its own repo, each flashed to a real device in the same home MQTT +
Home Assistant setup. This repo has no firmware of its own — just the map
of how the others relate.

## Projects

| Repo | Board | What it is | Visibility |
|---|---|---|---|
| [car_battery_charger](https://github.com/pachobg2/car_battery_charger) | ESP32-C3-Zero | 3-stage lead-acid charger: INA219 current/voltage sensing, SH1106 OLED, rotary-encoder local UI with easy-mode capacity presets, linear-region MOSFET current regulation | Public |
| [door_sensor](https://github.com/pachobg2/door_sensor) | ESP32-C3 | Battery-powered reed-switch door sensor, deep-sleep on level-change wake + heartbeat, remote (software-switch) OTA | Public |
| [energy_meter](https://github.com/pachobg2/energy_meter) | ESP32-C3 | RS485 Modbus bridge for an ORNO OR-WE-526 energy meter, with firmware-side day/night tariff accounting | Public |
| [plugin_light](https://github.com/pachobg2/plugin_light) | ESP32-C3 | Dimmable plug-in light, PWM dimmer output | Public |
| [smart_switch](https://github.com/pachobg2/smart_switch) | ESP32-C3 | Mains relay switch with physical button + WS2812 status LED | Public |
| [smart_switch_zdravkovec](https://github.com/pachobg2/smart_switch_zdravkovec) | ESP32-C3 | Same firmware as `smart_switch`, deployed at the Zdravkovec site | Private |
| [sonoff_basic](https://github.com/pachobg2/sonoff_basic) | ESP8266 | Sonoff Basic relay switch, ported from ESPHome | Public |
| [temp_humidity_sensor](https://github.com/pachobg2/temp_humidity_sensor) | ESP32-C3 | Battery-powered SHTC3 temp/humidity sensor, deep-sleep between reports | Public |
| [temp_humidity_sensor_zdravkovec](https://github.com/pachobg2/temp_humidity_sensor_zdravkovec) | ESP32-C3 | Same firmware as `temp_humidity_sensor`, deployed at the Zdravkovec site | Private |
| [temp_humidity_sensor_v4](https://github.com/pachobg2/temp_humidity_sensor_v4) | ESP32-C3 | Fork of `temp_humidity_sensor` with a self-service WiFiManager web setup portal instead of a compiled config.h -- one firmware image, configured per-unit from a phone | Public |
| [toshiba_ac_bridge](https://github.com/pachobg2/toshiba_ac_bridge) | ESP32-C5 | UART bridge to a Toshiba Suzumi/Shorai/Seiya AC's internal wifi-module connector | Public |

All tagged with the `smart-stuff` topic on GitHub, so they also show up
together on [github.com/topics/smart-stuff](https://github.com/topics/smart-stuff)
filtered to this account.

## What they share

These aren't copy-pasted independently — they're built to the same house
conventions on purpose, so fixing or improving one often means checking
whether the same fix applies to the others:

- **Stack**: raw Arduino C++ (no ESPHome runtime), [`espMqttClient`](https://github.com/bertmelis/espMqttClient)
  for MQTT with QoS 1 + PUBACK confirmation, `ArduinoOTA` for wireless
  updates after the first USB flash.
- **Home Assistant integration**: retained MQTT discovery configs published
  on every broker connect, one HA device per firmware bundling its primary
  entity plus diagnostic sensors (WiFi RSSI, reset reason, MQTT/connect
  fail counts) — no manual `configuration.yaml` entity setup needed.
- **Reliability pattern**: LWT `online`/`offline` availability topic,
  non-blocking WiFi reconnect, exponential-backoff MQTT reconnect, and a
  consistent `manufacturer: "P@cho"` tag across every HA device.
- **Config convention**: real credentials live in a single gitignored
  `config.h` (or `secrets.h`) per project, with a sanitized
  `config.h.example`/`secrets.h.example` alongside it as the template for a
  fresh checkout or a new device.
- **Multi-site deployment**: `smart_switch` and `temp_humidity_sensor` each
  have a `_zdravkovec` sibling — same firmware, different site's WiFi/MQTT
  broker via that project's own config file, kept as a separate repo (and
  private, since it documents that site's specific network) rather than a
  branch or build flag.

The local working directory these are all checked out under also has a
`CLAUDE.md` (not published, kept local) documenting the standing
auto-commit/push workflow used across all of these.
