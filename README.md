# Brink-flair-modbus
Modbus RTU communication with ESP32/ESP8266 and ttl to rs485 module, to use in home assistant / esphome

If you have a Heat Recovery Ventilator (HRV) device, this project can help control it.

![Architecture diagram](/pictures/architecture.png)

To the HRV on the left (see [supported devices](#supported-hrv-devices)),
an ESP device is connected (see [supported boards](#supported-boards)),
over [Modbus](https://en.wikipedia.org/wiki/Modbus#Modbus_RTU) for data,
and also powered using a separate pair of wires.

On the ESP device runs an [esphome](https://esphome.io/) application.
esphome is a toolkit of software components, that are configured by *this* repository,
using a set of `.yaml` files. The yaml configuration determines the targeted HRV device,
the ESP device, and the offered features.

esphome exports an (optional) web UI, and an API, that is [Home Assistant](https://www.home-assistant.io/) compatible.
It can connect to the configured WiFi network, or make its own WiFi AP.

## Getting Started

1. Ensure that your HRV device is supported (see [supported devices](#supported-hrv-devices))
2. Select a supported ESP board (see [supported boards](#supported-boards))
3. Change the yaml configuration to suite your compoents and needs
4. Compile the esphome project, using the selected yaml configuration, flash it to the device
5. Test then connect the ESP device to your HRV

## Supported HRV devices

The supported HRV models and their flow limits are listed in [esphome/brink.yaml](/esphome/brink.yaml) — set `type_flow_max` and `type_modbus_flow_rate_max` for your model.

This project originally targeted Brink devices, but there are many
brands selling the same (similar) device under different names.
A HRV device is considered compatible, if it exports the same (similar)
content on the used modbus addresses.

To find what your device exports, acquire its documentation
(See examples in the [documents](/documents/) directory, usually the vendor
sends it to you if you ask for it over e-mail), and compare the documented
modbus addresses to the addresses specified in [esphome/.brink.base.yaml](/esphome/.brink.base.yaml).

## Supported Boards

The supported boards can be found in [esphome/boards](/esphome/boards).

If you don't have any similar item, probably you'll need to buy two components
(a microcontroller and a RS485 base unit), and some cables.
See [pictures/connection.jpg](pictures/connection.jpg) for example.

## Configuration

Edit [esphome/brink.yaml](/esphome/brink.yaml). Set your model's flow limits via the `type_flow_max` /
`type_modbus_flow_rate_max` substitutions (see the model table in that file), and uncomment the right board (esphome/boards).
Do not leave multiple `board` files in. Select a language (en/nl), set the timezone.

By default, esphome exports everything publicly. It might be fine on a local network,
but it is still recommended to lock-down the exported endpoints (Web UI, flash API, homeassistant API).
See [Security Best Practices](https://esphome.io/guides/security_best_practices/) for more.

## Compile

[Install the esphome](https://esphome.io/guides/installing_esphome/) software suite on your computer.
It might be packaged by your distribution. Assemble your ESP device, connect it to your computer using USB, then:

```shell
# esphome run brink.yaml
```

It will take some time, compile the project according to the config, and flash the resulting binary to the ESP device.
If successful, it should show logs, connect to the configured WiFi AP, and expose a Web UI (if configured so).

## Connect and Run

Once the ESP is flashed correctly, *power off* your HRV device, and connect the ESP to the modbus and power ports.
(Check the install manual of your HRV to figure out the right ports, and also see documents/ and pictures/).
You might need to take off the topmost cover of the HRV to expose the wire screws.

Once connected, power on the HRV device. The Web UI should show the live values now, and home assistant should
be able to discover the esphome integration. While the ESP and the HRV are connected, you can still connect your computer
to the ESP over USB, and get the logs:

```shell
# esphome logs brink.yaml
```

# Translations
Currently supported languages are en, nl.
In order to change language, edit file `esphome/brink.yaml`, and change include file (esphome-/.brink-labels-<language>.yaml)

# Custom sensors
Project support additional sensors DHT22 or M5stack SCD40/41, CO2 sensor or M5stack ENVIII humidity, temperature, pressure sensor.
In order to enable them, edit file `esphome/brink.yaml` and uncomment include file for sensor.

# Modbus addres inside brink.yaml
The modbus address is now configurable in brink.yaml.
Because there are several Brink versions available on the market, like the Viessmann Vitovent 300w (H32e C400) which uses address 70


[!["Buy Me A Coffee"](https://www.buymeacoffee.com/assets/img/custom_images/orange_img.png)](https://www.buymeacoffee.com/ebbenberg)
