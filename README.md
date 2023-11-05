# Brink-flair-modbus
Modbus RTU communication with ESP32/ESP8266 and ttl to rs485 module, to use in home assistant / esphome

# Platforms
Currently supported ESP32 and ESP8266 platforms.
By default ESP32 is used. If you want to use ESP8266, edit file `esphome/brink.yaml`, comment out esp32 include and uncomment esp8266 include file.

```
packages:
  remote_package:
    url: https://github.com/fonske/Brink-flair-modbus
    ref: main
    files: 
      # - esphome/labels/.brink-labels-en.yaml
      - esphome/labels/.brink-labels-nl.yaml
      # - esphome/.brink.base.yaml
      # - esphome/boards/board-esp32.yaml
      - esphome/boards/board-esp32S3.yaml
      # - esphome/boards/board-esp8266.yaml
      # - esphome/sensors/sensor-scd41-i2c-dfrobot.yaml
      # - esphome/sensors/sensor-scd41-i2c-m5stack.yaml
      # - esphome/sensors/sensor-enviii-i2c-m5stack.yaml
      - esphome/sensors/sensor-dht22.yaml

# for developing/testing, uncomment local includes and comment out remote_package part.
# packages:
#   substitutions: !include labels/.brink-labels-en.yaml
  device_base1: !include .brink.base.yaml
#  device_base2: !include boards/board-esp8266.yaml
```

# Translations
Currently supported languages are en, nl.
In order to change language, edit file `esphome/brink.yaml`, and change include file (esphome-/.brink-labels-<language>.yaml)

# Custom sensors
Project support additional sensors DHT22 or DFROBOT GRAVITY SCD41, CO2 sensor.
In order to enable them, edit file `esphome/brink.yaml` and uncomment include file for sensor.

## Contact
Contact me: alphonsuijtdehaag at gmail dot com, if you are interested in a PCB with a ESP32 (mh-et-live or lilygo esp32s3-T7) and XY-017 ttl to rs485 mounted on with 3d printed housing.
See: /pictures
