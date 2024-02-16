# Brink-flair-modbus
Modbus RTU communication with ESP32/ESP8266 and ttl to rs485 module, to use in home assistant / esphome

# Platforms
Currently supported ESP32 and ESP8266 platforms.
By default ESP32S3 is used. If you want to use ESP8266, edit file `esphome/brink.yaml`, comment out esp32 include and uncomment esp8266 include file.

```
# Choose the correct type for your Brink model, so the correct max flow can be setup with a slider.
# In order to use other language or a different ESP chip, fix file names below.
# Currently supported languages are en, nl. 
# ESP32 is a mh-et-live or wemos d32 mini, esp8266 is a wemos d1 mini, esp32s3 is a lilygo ESP32S3-T7
# Be carefull not to upload the wrong code to the wrong chip. This could brick your ESP chip.

packages:
  remote_package:
    url: https://github.com/fonske/Brink-flair-modbus
    ref: main
    refresh: 0s
    files: [ esphome/type/brink-325.yaml,
             esphome/labels/.brink-labels-en.yaml, 
             esphome/.brink.base.yaml, 
             esphome/boards/board-esp32S3.yaml,
             esphome/sensors/sensor-enviii-i2c-m5stack.yaml,
             esphome/sensors/sensor-brink_hum_sensor.yaml,
             esphome/sensors/sensor-brink_co2_1_sensor.yaml
           ]
    ## options are: 
      # esphome/type/.brink-325.yaml
      # esphome/type/.brink-400.yaml
      # esphome/type/.brink-450.yaml
      # esphome/type/.brink-600.yaml
      # esphome/labels/.brink-labels-en.yaml
      # esphome/labels/.brink-labels-nl.yaml
      # esphome/.brink.base.yaml
      # esphome/boards/board-esp32.yaml
      # esphome/boards/board-esp32S3.yaml
      # esphome/boards/board-esp8266.yaml
      # esphome/boards/board-esp8266-d1-mini-pro.yaml
      # esphome/boards/board-m5stack-atom.yaml
      # esphome/boards/board-m5stack-atoms3-lite.yaml
      # esphome/sensors/sensor-scd41-i2c-dfrobot.yaml
      # esphome/sensors/sensor-scd41-i2c-m5stack.yaml
      # esphome/sensors/sensor-enviii-i2c-m5stack.yaml
      # esphome/sensors/sensor-dht22.yaml
      # esphome/sensors/sensor-brink_hum_sensor.yaml
      # esphome/sensors/sensor-brink_co2_1_sensor.yaml
      # esphome/sensors/sensor-brink_co2_2_sensor.yaml
      # esphome/sensors/sensor-brink_co2_3_sensor.yaml
      # esphome/sensors/sensor-brink_co2_4_sensor.yaml

# for local developing/testing, uncomment local includes and comment out remote_package part.
#packages:
#  substitutions: !include labels/.brink-labels-nl.yaml
#  device_base1: !include .brink.base.yaml
#  device_base2: !include boards/board-m5stack-atoms3-lite.yaml
#  device_base3: !include type/brink-325.yaml
#  device_base4: !include sensors/sensor-scd41-i2c-m5stack.yaml
```

# Translations
Currently supported languages are en, nl.
In order to change language, edit file `esphome/brink.yaml`, and change include file (esphome-/.brink-labels-<language>.yaml)

# Custom sensors
Project support additional sensors DHT22 or M5stack SCD40/41, CO2 sensor or M5stack ENVIII humidity, temperature, pressure sensor.
In order to enable them, edit file `esphome/brink.yaml` and uncomment include file for sensor.

## Contact
Contact me: alphonsuijtdehaag at gmail dot com, if you are interested in a PCB with an ESP32 (lilygo ESP32S3-T7) or M5stack RS485 with Atom s3 lite
Both have I2C port(s) to connect a SCD40/41 or ENVIII sensor

[!["Buy Me A Coffee"](https://www.buymeacoffee.com/assets/img/custom_images/orange_img.png)](https://www.buymeacoffee.com/ebbenberg)
