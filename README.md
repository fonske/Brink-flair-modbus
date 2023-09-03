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
      - esphome/.brink-labels-en.yaml
      # - esphome/.brink-labels-nl.yaml
      - esphome/board-esp32.yaml
      # - esphome/board-esp32S3.yaml
      # - esphome/board-esp8266.yaml
      # - esphome/sensor-scd41-i2c-dfrobot.yaml
      # - esphome/sensor-scd41-i2c-m5stack.yaml
      # - esphome/sensor-enviii-i2c-m5stack.yaml
      # - esphome/sensor-dht22.yaml
```

# Translations
Currently supported languages are en, nl.
In order to change language, edit file `esphome/brink.yaml`, and change include file (esphome-/.brink-labels-<language>.yaml)

# Custom sensors
Project support additional sensors DHT22 or DFROBOT GRAVITY SCD41, CO2 sensor.
In order to enable them, edit file `esphome/brink.yaml` and uncomment include file for sensor.

## Contact
Contact me: alphonsuijtdehaag at gmail dot com, if you are interested in a PCB with a ESP32 (mh-et-live or wemos s3 mini) and XY-017 ttl to rs485 mounted on with 3d printed housing.
See: /pictures
