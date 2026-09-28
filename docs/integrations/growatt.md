# Growatt (Draft)

For some reason it took me a while to set this up, but it finally works and I want to consolidate the knowledge.

The following table compares five ways to integrate Growatt inverters into Home Assistant. Here, “OpenInverter” refers to OpenInverterGateway, the replacement firmware for compatible Growatt logger sticks.

| Option | How it connects | Pros | Cons |
| --- | --- | --- | --- |
| [Growatt](https://www.home-assistant.io/integrations/growatt_server/) | Growatt Cloud API | Built into Home Assistant | Depends on Growatt Cloud API; polls every five minutes; username/password authentication can trigger rate limits and account lockouts
| [Growatt Server API via HACS](https://github.com/muppet3000/homeassistant-growatt_server_api) | Growatt Cloud API | Quicker development life cycle; Upstream of Growatt integration | Still depends on Growatt's cloud and API; Essentially has all the same problems as Growatt integration |
| [Grott](https://github.com/johanmeijer/grott) | Intercepts logger traffic using a proxy or packet sniffer and publishes decoded data to MQTT | Avoids cloud API polling; Retains Growatt portal reporting by forwarding traffic | Requires a running service, network/logger configuration, and MQTT setup for Home Assistant;  stopping the proxy interrupts cloud uploads. |
| [Grottserver](https://github.com/johanmeijer/grott/wiki/Grottserver) | Emulates the Growatt server locally, working with Grott to publish data to MQTT. | Operates without Growatt cloud access; retains existing logger firmware; exposes an API to read/write inverter and logger registers. | More setup and maintenance than a native integration; replacing the cloud destination stops uploads to the Growatt portal through this path; controls require model-specific register knowledge and custom integration; documented register reads can be slow or require retries. |
| [OpenInverterGateway](https://github.com/OpenInverterGateway/OpenInverterGateway) | Replacement firmware on a compatible ShineWiFi-S/X or custom ESP8266/ESP32 stick reads Modbus directly and publishes locally via MQTT. | Fully local operation; direct inverter access without cloud API limits; built-in web interface plus JSON/Prometheus output; optional Modbus read/write support. | Requires flashing compatible hardware and potentially compiling firmware; MQTT setup is needed for the documented Home Assistant integration; does not upload to Growatt Cloud; ShineLAN-X is unsupported; inverter protocol compatibility must be checked. |

Compatibility and available controls vary by inverter, logger, firmware, and integration version. The links in each row point to the project documentation used for this comparison.

```
Since 07/02/2023 (7th Feb) Growatt have started implementing rate limiting and blocking to user accounts that make excessive API calls i.e. this integration.
```

If blockcmd = False, Growatt server can send commands to Datalogger
Read Register = 4, default value - refresh every 5 minutes (also visibl in the UI)
Set Register = 4, value = 1 - refresh every one minute.
Password: growattYYYYMMDD
Ref: https://github.com/johanmeijer/grott/discussions/93?utm_source=chatgpt.com


- Merge history: https://github.com/mayerwin/HA-Merge-Sensor-History
- More details: https://community.home-assistant.io/t/guide-migrate-copy-energy-statistics-history-to-new-entity-keep-full-history-in-energy-dashboard-2026/982415
- https://github.com/muppet3000/homeassistant-growatt_server_api/blob/v1.0.4/FAQ.md
- Add ModBus integration: https://0xaha.github.io/Growatt_ModbusTCP/#power-flow-sensor-glossary
    - https://community.home-assistant.io/t/esphome-modbus-growatt-shinewifi-s/369171
    - https://github.com/WilbertVerhoeff/Growatt
- OpenInverterGateway:
    - MQTT: https://github.com/OpenInverterGateway/OpenInverterGateway/blob/master/Doc/MQTT.md
    - Pros: Prometheus https://github.com/OpenInverterGateway/OpenInverterGateway/blob/53049a1bf727ed9954ef17c6e21631bd227e839b/Doc/Prometheus.md
    - Flashing:
        - https://github.com/OpenInverterGateway/OpenInverterGateway/issues/123
        - https://github.com/OpenInverterGateway/OpenInverterGateway/blob/master/Doc/ShineWiFi-S/ShineWiFi-S.md