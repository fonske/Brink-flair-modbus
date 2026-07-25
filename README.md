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

The supported HRVs can be found in [esphome/type](/esphome/type).

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

Edit [esphome/brink.yaml](/esphome/brink.yaml). Uncomment the right HRV (esphome/type) and board (esphome/boards).
Do not leave multiple `type` or `board` files in. Select a language (en/nl), set the timezone.

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
Currently supported languages are de, en, nl.
In order to change language, edit file `esphome/brink.yaml`, and change include file (esphome-/.brink-labels-<language>.yaml)

# Custom sensors
The project supports additional sensors: DHT22, M5Stack SCD40/41 CO2, the modbus CO2/humidity sensors, and
the M5Stack ENV III / ENV Pro modules. To enable one, edit `esphome/brink.yaml` and uncomment the include
for that sensor.

For ENV III / ENV Pro / SHT30 / QMP6988, prefer the parameterized packages described under
[Multiple and external sensors via M5Stack PaHUB](#multiple-and-external-sensors-via-m5stack-pahub-tca9548a)
— they work **with and without** a PaHUB. The older `sensor-enviii-i2c-m5stack*.yaml` packages have been
removed; `sensor-enviv-i2c-m5stack.yaml` (ENV IV, SHT4x + BMP280) is kept as a **legacy** option until a
modernized ENV IV package exists.

# Multiple and external sensors via M5Stack PaHUB (TCA9548A)

A single I2C bus can only host one sensor of a given address (e.g. only one ENV III at `0x44`).
With an M5Stack PaHUB (a TCA9548A I2C multiplexer) you can run up to 6 identical sensors in parallel —
one per multiplexer channel — and wire each channel to an air stream from
[esphome/brink.yaml](/esphome/brink.yaml). The same sensor packages also work **without** a PaHUB
(directly on the board's `bus_a`).

## Available sensor packages

These packages are parameterized: include one per channel and pass `vars` (`channel`, `id_prefix`,
`sensor_name`). Entity ids become `brink_ext_<id_prefix>_<value>`; display names come from the label
files. Each file has a `defaults:` block that documents its variables.

- [m5stack-pahub-tca9548a.yaml](/esphome/sensors/m5stack-pahub-tca9548a.yaml) — the PaHUB multiplexer (include once)
- [sensor-sht30.yaml](/esphome/sensors/sensor-sht30.yaml) — SHT30: temperature, humidity
- [sensor-qmp6988.yaml](/esphome/sensors/sensor-qmp6988.yaml) — QMP6988: pressure
- [sensor-m5stack-enviii.yaml](/esphome/sensors/sensor-m5stack-enviii.yaml) — M5Stack ENV III = SHT30 + QMP6988
- [sensor-m5stack-envpro.yaml](/esphome/sensors/sensor-m5stack-envpro.yaml) — M5Stack ENV Pro (BME688 / BSEC2): temperature, humidity, pressure, gas resistance and air quality (IAQ, static IAQ, CO2/VOC equivalent, accuracy + classification)

> **ENV Pro note:** the BME688 sits at I2C address `0x77` (no conflict with the QMP6988 at `0x70`), and it
> uses Bosch's closed-source BSEC2 library, which is RAM-heavy — running many ENV Pro instances on one ESP
> is demanding.

Every temperature + humidity sensor additionally gets the derived psychrometric values from
[sensor-psychrometrics.yaml](/esphome/sensors/sensor-psychrometrics.yaml): dew point, absolute
humidity, specific enthalpy, wet-bulb temperature and humidex. (It is pulled in automatically by the
temp/humidity packages — you do not include it yourself.)

Heat-recovery efficiency can be computed from external sensors:

- [feature-performance.yaml](/esphome/features/feature-performance.yaml) — temperature based
- [feature-performance-enthalpy.yaml](/esphome/features/feature-performance-enthalpy.yaml) — enthalpy based (also accounts for recovered moisture/latent energy)

Include a performance feature **exactly once**, and do not combine it with a package that already defines
`brink_performance` (the legacy ENV IV, DHT22 and SCD41-dfrobot packages do). While the bypass is open, the
performance is reported as *unavailable* — no heat recovery takes place in that state.

## PaHUB wiring example

Enable I2C on the board (`bus_a`; the M5Stack Atom Lite is preconfigured for GPIO26/GPIO32), then in
[esphome/brink.yaml](/esphome/brink.yaml) use the *block* form of `files:` so per-file `vars:` can be passed:

```yaml
    files:
      - esphome/type/brink-400.yaml
      - esphome/labels/.brink-labels-de.yaml
      - esphome/.brink.base.yaml
      - esphome/boards/board-m5stack-atom-lite.yaml
      # PaHUB once (address 0x71, NOT 0x70 when using ENV III's QMP6988):
      - path: esphome/sensors/m5stack-pahub-tca9548a.yaml
        vars: { pahub_address: "0x71" }
      # one sensor per channel; sensor_name may come from the label file:
      - path: esphome/sensors/sensor-m5stack-envpro.yaml
        vars: { channel: pahub_ch0, id_prefix: supply,  sensor_name: "${brink_air_supply}" }
      - path: esphome/sensors/sensor-m5stack-envpro.yaml
        vars: { channel: pahub_ch1, id_prefix: extract, sensor_name: "${brink_air_extract}" }
      - path: esphome/sensors/sensor-m5stack-envpro.yaml
        vars: { channel: pahub_ch2, id_prefix: outside, sensor_name: "${brink_air_outside}" }
      # enthalpy-based recovery efficiency across the streams:
      - path: esphome/features/feature-performance-enthalpy.yaml
        vars: { perf_supply_enthalpy: brink_ext_supply_enthalpy, perf_extract_enthalpy: brink_ext_extract_enthalpy, perf_outside_enthalpy: brink_ext_outside_enthalpy }
```

Notes:

- `id_prefix` must be a fixed, language-independent key and unique per channel (it forms the entity ids).
- Display names are prefixed with `label_ext` (default `Ext`) so external sensors don't clash with the
  modbus sensors — e.g. `Ext Zuluft Temperatur` next to the modbus `Zuluft Temperatur`. Set `label_ext: ""` to disable.
- Without a PaHUB, omit `channel` (it defaults to `bus_a`) — one sensor directly on the board bus.

# What's new & upgrade notes (migration)

**New**
- Multiple/external sensors via the M5Stack PaHUB (TCA9548A) — up to 6 identical sensors, one per air stream.
- Parameterized sensor packages: `sensor-sht30`, `sensor-qmp6988`, `sensor-m5stack-enviii`, `sensor-m5stack-envpro`.
- Derived psychrometric values (dew point, absolute humidity, specific enthalpy, wet-bulb, humidex) via `sensor-psychrometrics.yaml`.
- Heat-recovery efficiency as configurable packages: `feature-performance.yaml` (temperature) and `feature-performance-enthalpy.yaml` (enthalpy). While the bypass is open the value is reported as *unavailable*.
- Home Assistant metadata (`device_class` / `state_class` / `entity_category`) and web-server icons across the base and sensor entities.

**Upgrade notes / breaking changes**
- **Removed packages** — use the new ENV III package instead:

  | Removed | Replacement |
  |---|---|
  | `sensor-enviii-i2c-m5stack.yaml` | `sensor-m5stack-enviii.yaml` with `vars: { channel, id_prefix, sensor_name }` |
  | `sensor-enviii-i2c-m5stack2.yaml` (was broken) | `sensor-m5stack-enviii.yaml` with `id_prefix: outside` |

  The entity-id scheme changed: `brink_temp/humidity/pressure_from_inside` → `brink_ext_<id_prefix>_temperature/humidity/pressure`, and heat-recovery efficiency now comes from `feature-performance.yaml` (not the sensor file). **Update any dashboards/automations that reference the old ids.**
- **ENV IV**: `sensor-enviv-i2c-m5stack.yaml` is kept as a **legacy** package (no modernized replacement yet). Do not combine it with `feature-performance.yaml` — both define `brink_performance`.
- **Home Assistant long-term statistics**: the airflow sensors changed unit `m3/h` → `m³/h` (plus `device_class: volume_flow_rate`), and `brink_filter_m3_h` changed `state_class: total` → `total_increasing`. Home Assistant does **not** auto-migrate a changed unit — it raises a fixable "units changed" repair per affected sensor; accept the conversion or reset that statistic once.
- **Board**: `board-m5stack-atom-lite.yaml` now enables I2C by default (GPIO26 / GPIO32).
- **Display name**: `brink_performance` is now *Wirkungsgrad* (de) / *Efficiency* (en); nl unchanged (*Rendement*). Entity ids are unchanged.

# Modbus addres inside brink.yaml
The modbus address is now configurable in brink.yaml.
Because there are several Brink versions available on the market, like the Viessmann Vitovent 300w (H32e C400) which uses address 70


[!["Buy Me A Coffee"](https://www.buymeacoffee.com/assets/img/custom_images/orange_img.png)](https://www.buymeacoffee.com/ebbenberg)
