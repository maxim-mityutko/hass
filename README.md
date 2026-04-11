# Home Assistant

This repo aims to consolidate various aspects of the Home Assistant based infrastructure, including hardware devices
and configurations.

## Quick Links

* [Devices](/docs/devices.md)
* [Extensions](/docs/extensions.md)

## Firewall

All smart devices, both wired and wireless, are connected to the dedicated VLANs with fully blocked Internet traffic.

### Internet Traffic

| Device        | Port                | Protocol | Reason                                                                         |
| ------------- | ------------------- | -------- | ------------------------------------------------------------------------------ |
| Shelly        | update.shelly.cloud | TCP      | (Optional) Allow traffic to Shelly update server                               |
| HomeWizard    | homewizard.com      | TCP      | Required for Plug-In batteries as they constantly monitor for firmware updates ([ref](https://api-documentation.homewizard.com/docs/v2/system/#plug-in-battery-exceptions)) |

### Inernal Traffic

Extra firewall rules are setup to control the internal traffic:

* **Source**: allow traffic from the VLAN if it originates from the specified port
* **Target**: allow traffic from the VLAN if it targets the specified port
* All other outgoing traffic from VLAN is blocked

#### Smart Devices VLAN

| Device        | Port         | Protocol | Direction |
| ------------- | ------------ | -------- | --------- |
| Tasmota       | HTTP (80)    | TCP      | Source    |
| Tasmota       | MQTT (1883)  | TCP      | Target    |
| Shelly        | HTTP (80)    | TCP      | Source    |
| Shelly (Gen1) | CoIoT (5683) | UDP      | Target    |
| ESPHome       | HTTP (80)    | TCP      | Source    |
| ESPHome       | 6053         | TCP      | Source    |
| SLZB-06       | 6638         | TCP      | Source    |
| HomeWizard    | HTTPS (443)  | TCP      | Source    |

#### Cameras VLAN

| Device        | Port                 | Protocol | Direction |
| ------------- | -------------------- | -------- | --------- |
| TP-Link Vigi  | HTTPS (443)          | TCP      | Source    |
| TP-Link Vigi  | RTSP (554)           | TCP      | Source    |
| TP-Link Vigi  | Video Service (8800) | TCP      | Source    |
| TP-Link Vigi  | Web Stream (8443)    | TCP      | Source    |

## Integrations

* [Citroen C5X](docs/integrations/citroen-c5x.md)
