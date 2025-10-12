# Shelly Power Strip 4 Gen4

The UI of the device becomes occasionally not available and power strip stops responding to ping. At the same time the Zigbee integration via Zigbee2MQTT and native Home Assistant integration via WebSocket operates without delays or issues. After some time UI and ping starts to work again.
If the device is constantly pinged then connectivity to the UI is never lost.

## Debugging

*Collecting logs as per request of the Shelly support team. Logs are shipped via UDP to the `rsyslog` server running in Ubuntu.*

### Log Collector

1. `sudo mkdir -p /var/log/shelly`
1. `sudo chown syslog:adm /var/log/shelly`
1. `sudo nano /etc/rsyslog.d/10-udp-logging.conf`

    ```c
    module(load="imudp")
    input(type="imudp" port="10514")

    template(name="ShellyLogFormat" type="string" string="%msg%\n")

    if $inputname == "imudp" then {
        action(type="omfile" fileOwner="syslog" fileGroup="adm"
            fileCreateMode="0644" dirCreateMode="0755"
            file="/var/log/shelly/udp.log"
            template="ShellyLogFormat")
        stop
    }
    ```

1. `sudo systemctl restart rsyslog`
1. Check if server is listening on required port: `sudo ss -uln | grep 10514`

### Log Rotation

1. `sudo nano /etc/logrotate.d/shelly-udp`

    ```yaml
    /var/log/shelly/udp.log {
        size 50M
        rotate 50
        compress
        missingok
        notifempty
        copytruncate
    }
    ```

1. Test rotation: `sudo logrotate --force /etc/logrotate.d/shelly-udp`

### Configure Shelly

Go to: `Settings` -> `Debug` -> `UDP logs`

![udp-log](image.png)
