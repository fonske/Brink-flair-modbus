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
      # esphome/type/brink-300.yaml
      # esphome/type/brink-325.yaml
      # esphome/type/brink-400.yaml
      # esphome/type/brink-450.yaml
      # esphome/type/brink-600.yaml
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
      # esphome/sensors/sensor-enviv-i2c-m5stack.yaml
      # esphome/sensors/sensor-dht22.yaml
      # esphome/sensors/sensor-brink_hum_sensor.yaml
      # esphome/sensors/sensor-brink_co2_1_sensor.yaml
      # esphome/sensors/sensor-brink_co2_2_sensor.yaml
      # esphome/sensors/sensor-brink_co2_3_sensor.yaml
      # esphome/sensors/sensor-brink_co2_4_sensor.yaml
      # esphome/features/feature-performance.yaml
      # esphome/features/feature-performance-enthalpy.yaml
      # esphome/sensors/m5stack-pahub-tca9548a.yaml
      # esphome/sensors/sensor-enviii.yaml
      # esphome/sensors/sensor-sht30.yaml
      # esphome/sensors/sensor-qmp6988.yaml
      # esphome/sensors/sensor-envpro.yaml

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

# Multiple I2C sensors via M5Stack PaHUB (TCA9548A)
A single I2C bus can only hold one sensor of a given address (e.g. only one ENV III at 0x44).
With an M5Stack PaHUB (TCA9548A I2C multiplexer) you can run up to 6 identical sensors in parallel,
one per multiplexer channel, and wire each channel to a name entirely from `esphome/brink.yaml`.

Enable the I2C bus on the board (`bus_a`, already configured for the M5Stack Atom Lite on GPIO26/GPIO32),
then convert the `files:` list to block form so per-file `vars:` can be passed:

```
    files:
      - esphome/type/brink-325.yaml
      - esphome/labels/.brink-labels-de.yaml
      - esphome/.brink.base.yaml
      - esphome/boards/board-m5stack-atom-lite.yaml
      # define the PaHUB once (address 0x71, NOT 0x70 when using ENV III's QMP6988):
      - path: esphome/sensors/m5stack-pahub-tca9548a.yaml
        vars: { pahub_address: "0x71" }
      # one sensor per channel; sensor_name comes from the label file, id_prefix is a fixed key:
      - path: esphome/sensors/sensor-enviii.yaml   # extract air: SHT30 + QMP6988 (temp+hum+pressure)
        vars: { channel: pahub_ch0, id_prefix: extract, sensor_name: "${brink_air_extract}" }
      - path: esphome/sensors/sensor-sht30.yaml    # supply air: SHT30 only (temp+hum, no pressure)
        vars: { channel: pahub_ch1, id_prefix: supply,  sensor_name: "${brink_air_supply}" }
      # optional: heat-recovery efficiency computed from an external sensor instead of modbus:
      - path: esphome/features/feature-performance.yaml
        vars: { perf_extract_temp: brink_ext_extract_temperature }
```

The ENV III is really two I2C chips: SHT30 (temp/humidity @0x44) and QMP6988 (pressure @0x70). They
are available as separate packages, and `sensor-enviii.yaml` simply wires both together. So you
enable pressure per channel from your own config by choosing the file:
- `sensor-enviii.yaml` – SHT30 **and** QMP6988 (temperature, humidity, dew point, pressure)
- `sensor-sht30.yaml`  – SHT30 only (temperature, humidity, dew point)
- `sensor-qmp6988.yaml` – QMP6988 only (pressure)
- `sensor-envpro.yaml` – ENV Pro (BME688 via BSEC2): temperature, humidity, pressure, gas resistance and
  air quality (IAQ, static IAQ, CO2 equivalent, breath VOC equivalent, IAQ accuracy + classification text)

Notes:
- Internal ids become `brink_ext_<id_prefix>_temperature` / `_humidity` / `_pressure` etc. (used for
  lambdas and `feature-performance`); the id is NOT what Home Assistant shows.
- The Home Assistant name/entity comes from `name:`, which is `${label_ext}${sensor_name} <quantity>`.
  `label_ext` (default `Ext`, in the label files) is prepended so external sensors don't clash with
  the modbus sensors — e.g. `sensor_name: "Zuluft"` shows as `Ext Zuluft Temperatur`, next to the
  modbus `Zuluft Temperatur`. Set `label_ext: ""` to disable the prefix.
- `id_prefix` must be a fixed, language-independent ASCII key (`extract`, `supply`, `exhaust`, `outside`);
  only the display `sensor_name` is localized (via the `brink_air_*` keys in the label files).
- All packages also work **without** a PaHUB: omit `channel` and it defaults to `bus_a` (one sensor
  directly on the board bus).

# Modbus addres inside brink.yaml
The modbus address is now configurable in brink.yaml.
Because there are several Brink versions available on the market, like the Viessmann Vitovent 300w (H32e C400) which uses address 70


[!["Buy Me A Coffee"](https://www.buymeacoffee.com/assets/img/custom_images/orange_img.png)](https://www.buymeacoffee.com/ebbenberg)
