# LampSmart Pro Light (ESPHome 2026.x compatible)

Custom ESPHome component for controlling LampSmart Pro / Marpou-style CCT ceiling lights over BLE advertising.

Compatible with **ESPHome 2026.7+** and Device Builder 1.9.x.

## Known limitations

* Only tested with Marpou Ceiling CCT lights (no RGB support).
* All lights of the same variant share control via BLE advertising (no individual addressing beyond the identifier hash).

## Required configuration (ESPHome 2026.x)

Because of changes in ESPHome (conditional BLE advertising + custom API services), you **must** enable two options:

```yaml
api:
  custom_services: true   # required for pair/unpair services from C++

esp32_ble:
  advertising: true       # required so advertising APIs are compiled
```

## How to use

1. Point `external_components` at this updated component (local folder or your fork).
2. Add the `lampsmart_pro_light:` hub and one or more `light:` entries with platform `lampsmart_pro_light`.
3. Flash an **ESP32** (BLE is required).
4. In Home Assistant call the service `esphome.<node_name>_pair_<object_id>` (or the generic `pair`) within ~5 seconds after powering the lamp from the wall switch.
5. Control the light normally.

## Example configuration

```yaml
external_components:
  - source: github://demon7905/esphome-lampsmart   # or local path
    components: [lampsmart_pro_light]

api:
  custom_services: true

esp32_ble:
  advertising: true

lampsmart_pro_light:

light:
  - platform: lampsmart_pro_light
    name: Kitchen Light
    duration: 1000
    default_transition_length: 0s
    # variant: v3          # default
    # reversed: false
    # min_brightness: 0x07
    # constant_brightness: false
    # cold_white_color_temperature: 6500 K
    # warm_white_color_temperature: 2700 K
```

### Variants

| variant | Description              |
|---------|--------------------------|
| `v3`    | Default (most common)    |
| `v2`    | Older protocol           |
| `v1a`   | Legacy                   |
| `v1b`   | Legacy                   |

### Options

* **duration** – how long the BLE advertisement is sent (ms). Default 100.
* **reversed** – swap cold/warm channels if temperatures appear inverted.
* **min_brightness** – lowest PWM value sent when the light is “on” (1–255). Default `0x07`.
* **constant_brightness** – keep total brightness constant while changing color temperature.
* **cold_white_color_temperature** / **warm_white_color_temperature** – mireds or Kelvin (both required if one is set).

## Automations

```yaml
on_...:
  - lampsmartpro.pair: kitchen_light
  - lampsmartpro.unpair: kitchen_light
```

## Original functions preserved

* Pair / Unpair services (via API)
* CWWW control (cold + warm white)
* All four protocol variants
* `reversed`, `min_brightness`, `constant_brightness`, `duration`
* Automation actions `lampsmartpro.pair` / `lampsmartpro.unpair`
