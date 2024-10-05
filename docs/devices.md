# Devices
## General Notes
- All the devices should work with Home Assistant out-of-the-box or with minimal effort
- Being able to run integration locally is highly preferred

## Tasmota
* [Novostella UT55507 9W](https://templates.blakadder.com/novostella_UT55507.html) (x2)
* [Yuabguo 4AC+2USB Power Strip](https://templates.blakadder.com/yuanguo_4AC_2USB.html) (x1)
* [Gosund SP111 v1.4 Power Monitoring Plug (SP111 v1.4)](https://templates.blakadder.com/gosund_SP111_v1_4) (x1)

## ESPHome
* [Marcel Zuidwijk's Smart Doorbell](https://www.zuidwijk.com/product/smart-doorbell/) (x1)
  * Docs: https://www.zuidwijk.com/smart-doorbell/
  * Repo: https://github.com/zuidwijk/esphome-doorbell/blob/main/smart-doorbell.yaml
* [WaterP1MeterKit](https://waterp1meterkit.nl/) (x1)
  * Docs: https://smarthomeshop.io/handleiding/waterp1meterkit
  * Repo: https://github.com/smarthomeshop/waterp1meterkit
 * [SlimmeLezer](https://www.zuidwijk.com/product/slimmelezer-plus/) (x1 not installed)
   * Replaced with `WaterP1MeterKit` in favour of PoE and water metering
   * Repo: https://github.com/zuidwijk/dsmr 
    
## Zigbee
* [SMLIGHT SLZB-06M Controller with POE](https://smlight.tech/product/slzb-06m/) (x1 not installed)
* [SMLIGHT SLZB-06 Controller with POE](https://smlight.tech/product/slzb-06/) (x1)
  * Used with Zigbee2MQTT
* [Univeral Infrared Remote Controller](https://zigbee.blakadder.com/Moes_UFO-R11.html) (x2)
  * Custom Quirk for ZHA: [TS1201](https://github.com/ferehcarb/zha-device-handlers/blob/dev/zhaquirks/tuya/ts1201.py)
  * Native support in Zigbee2MQTT
  * Usage instructions: [RTX Zigbee Tuya ZIR aka. TS1201](https://github.com/zigpy/zha-device-handlers/issues/1687)
  * Review: [Tuya Zigbee IR Blaster Remote ZS06 Review](https://smarthomescene.com/reviews/tuya-zigbee-infrared-ir-remote-zs06-review/)
* [Ledvance Outdoor Plug](https://zigbee.blakadder.com/Ledvance_4058075729322.html) (x4)
* [Heiman HS1SA Smoke Sensor](https://zigbee.blakadder.com/Heiman_HS1SA.html) (x5)
  * Review: [Heiman ZigBee Smoke Sensor And Alarm Review](https://smarthomescene.com/reviews/heiman-zigbee-smoke-sensor-and-alarm-review/)
* [Lonsonho (?) Tuya Zigbee Smart Plug 16A EU](https://zigbee.blakadder.com/Lonsonho_TS0121.html)
* Tuya SZ-T04 - Temperature & Humidity (x1)
  * Custom Quirk: [TS0601 Temperature](https://github.com/jacekk015/zha_quirks/blob/main/ts0601_temperature.py) 
  * Custom Quirk Issue Thread: [TZE200_locansqn TS0601 Temp / Humi / Clock](https://github.com/zigpy/zha-device-handlers/issues/1286)
  * Review: [Zigbee Temperature & Humidity Sensor With A Screen SZ-T04 Review](https://smarthomescene.com/reviews/zigbee-temperature-humidity-sensor-with-a-screen-sz-t04-review/)
* [Tuya WSD500A - Temperature and Humidity Sensor](https://zigbee.blakadder.com/Tuya_WSD500A.html) (x3)
  * Review: [Tuya Temperature & Humidity Sensor WSD500A](https://smarthomescene.com/reviews/tuya-temperature-humidity-sensor-wsd500a/)
 * [Aqara MCCGQ11LM - Door / Windows Sensor](https://zigbee.blakadder.com/Aqara_MCCGQ11LM.html) (x6)
   * Disassembly instructions before painting: [Painting contact sensors (ft. Aqara E1 disassembly)](https://community.home-assistant.io/t/hhi-painting-contact-sensors-ft-aqara-e1-disassembly/401704)
 * [Aqara SJCGQ11LM - Water Leak Sensor](https://zigbee.blakadder.com/Aqara_SJCGQ11LM.html) (x3)

## Xiaomi
 * [Plant Sensor - HHCC HHCCJCY01](https://esphome.io/components/sensor/xiaomi_ble.html?fbclid=IwZXh0bgNhZW0CMTAAAR38jsIqiCFlHGqq2riR1D2kFn9CKpR2cV1XfjHqSVYGuj0uJQwKFP6W2Gc_aem_t7kL8tHXd7t1HfHCv7Mosw#hhccjcy01) (x10)
   * Review on [Smart Home Scene](https://smarthomescene.com/reviews/xiaomi-miflora-plant-sensor-tuya-version-hhccjcy10-review/)

## Flic (replaced with Shelly buttons)
  * [Flic Hub LR](https://flic.io/shop/flic-hub-lr) (x1)
    * Hub setup requires account creation with email, however the registration process does not send any 
    confirmation emails , hence even the bogus email address can be provided
    * Hub supports MQTT integration with custom JS scripts:
      * [flic2hass](https://github.com/id628/flic2hass) - MQTT integration by **id628** ([discussion](https://community.home-assistant.io/t/direct-flic-button-via-flic-hub-mqtt-integration-cloudless/553108))
      * [flic-hub-sdk-mqtt-js](https://github.com/50ButtonsEach/flic-hub-sdk-mqtt-js) - MQTT client
  * [Flic2 Button](https://flic.io/flic2) (x3 not in use, replaced with `Shelly BLU Button Tough 1`)
  * [Flic Twist](https://flic.io/twist) (x2)

## Shelly
  * [Shelly Plus 2PM](https://www.shelly.com/en-nl/products/product-overview/shelly-plus-2-pm) (x2)
  * [Shelly 1PM Mini Gen3](https://www.shelly.com/en-nl/products/product-overview/shelly-1-pm-mini-gen3) (x1 not installed)
  * [Shelly 1 Mini Gen3](https://www.shelly.com/en/products/shop/shelly-1-mini-gen-3) (x1)
  * [Shelly Dimmer2](https://www.shelly.com/en/products/shop/shelly-dimmer2) (x2)
  * [Shelly BLU H&T](https://www.shelly.com/en-nl/products/product-overview/shelly-blu-h-and-t) (x1)
  * [Shelly BLU Button Tough 1](https://www.shelly.com/en-nl/products/product-overview/shelly-blu-button-tough-1-matte-black) (x3)
