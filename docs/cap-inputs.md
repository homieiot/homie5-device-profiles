# Capability: Inputs

This capability description describes an input node, which is derived from the numeric and binary sensor capabilities.

## Input introduction

Inputs are related to sensors. They are to be used in devices that take a specific value as an input to operate, yet do not have their own sensors. For example:

- a thermostat taking a temperature sensor as input
- a fan using a VOC sensor to ventilate based on air quality

## Differences from sensors

The sensors are [listed below](#input-capability-list), with their equivalent "input" capabilities. Their capabilities are identical except for:

* the profile name uses "input" instead of "sensor", so a generic sensor has profile name
  "`homie-sensor-numeric/1/0`", and a generic input has profile name "`homie-input-numeric/1/0`"
* an input must always be able to accept a value, as such either:
    - if there is no `raw` property then the `value` property MUST be settable, or
    - the `raw` property MUST be present and settable, or
    - the `raw-topic` property MUST be present and settable

### Examples

A Homie device with 1 node as a presence/motion input.

#### Minimal example

This minimal example only implements the required properties.

Description:

```json
{
  "homie": "5.0",
  "name": "Auto light",
  "version": 3,
  "nodes": {

    "living-motion": {
      "$profile": ["homie-input-presence/1/0"],
      "name": "Motion detected",
      "value": {
        "settable": true,
        "retained": true,
        "type": "boolean",
        "format": "no-presence,presence"
      }
    }

  }
}
```

Topics:

```
homie/5/living-light/$state               → "ready"
homie/5/living-light/$description         → "{... the above json doc ...}"
homie/5/living-light/living-motion/$profile/homie-sensor-presence/1 → "0"
homie/5/living-light/living-motion/value  → "false"
```

#### Full example

This example implements a sensor including all optional properties.

Description:

```json
{
  "homie": "5.0",
  "name": "Auto light",
  "version": 7,
  "nodes": {

    "livingroom": {
      "$profile": ["homie-input-presence/1/0"],
      "name": "Motion detected",
      "value": {
        "settable": false,
        "retained": true,
        "type": "boolean",
        "format": "no-presence,presence"
      },
      "raw": {
        "settable": true,
        "retained": true,
        "type": "boolean"
      },
      "raw-topic": {
        "settable": true,
        "retained": true,
        "type": "string"
      },
      "topic-falsy": {
        "settable": true,
        "retained": true,
        "type": "string"
      },
      "invert": {
        "settable": true,
        "retained": true,
        "type": "boolean",
        "format": "no,yes"
      }

    }
  }
}
```

Topics:
```
homie/5/living-light/$state                     → "ready"
homie/5/living-light/$description               → "{... the above json doc ...}"
homie/5/living-light/living-motion/$profile/homie-sensor-presence/1 → "0"
homie/5/living-light/living-motion/value        → "false"
homie/5/living-light/living-motion/raw          → "true"
homie/5/living-light/living-motion/raw-topic    → "homeassistant/sensor/some/topic"
homie/5/living-light/living-motion/topic-falsy  → "false,False,off,Off,0"
homie/5/living-light/living-motion/invert       → "true"
```

## Input capability list

For the mandatory changes from the "sensor" capabilities see [differences from sensors](#differences-from-sensors).

### Numeric inputs

Input capability          | based on sensor type
--------------------------|---------------------
`homie-input-numeric`     |[`homie-sensor-numeric`](cap-sensors-numeric.md#homie-sensor-numeric10)
`homie-input-temperature` |[`homie-sensor-temperature`](cap-sensors-numeric.md#homie-sensor-temperature10)
`homie-input-volume`      |[`homie-sensor-volume`](cap-sensors-numeric.md#homie-sensor-volume10)
`homie-input-volt`        |[`homie-sensor-volt`](cap-sensors-numeric.md#homie-sensor-volt10)
`homie-input-current`     |[`homie-sensor-current`](cap-sensors-numeric.md#homie-sensor-current10)
`homie-input-power`       |[`homie-sensor-power`](cap-sensors-numeric.md#homie-sensor-power10)
`homie-input-energy`      |[`homie-sensor-energy`](cap-sensors-numeric.md#homie-sensor-energy10)
`homie-input-frequency`   |[`homie-sensor-frequency`](cap-sensors-numeric.md#homie-sensor-frequency10)
`homie-input-battery`     |[`homie-sensor-battery`](cap-sensors-numeric.md#homie-sensor-battery10)
`homie-input-distance`    |[`homie-sensor-distance`](cap-sensors-numeric.md#homie-sensor-distance10)
`homie-input-speed`       |[`homie-sensor-speed`](cap-sensors-numeric.md#homie-sensor-speed10)
`homie-input-pressure`    |[`homie-sensor-pressure`](cap-sensors-numeric.md#homie-sensor-pressure10)
`homie-input-light`       |[`homie-sensor-light`](cap-sensors-numeric.md#homie-sensor-light10)
`homie-input-gas-co`      |[`homie-sensor-gas-co`](cap-sensors-numeric.md#homie-sensor-gas-co10)
`homie-input-gas-co2`     |[`homie-sensor-gas-co2`](cap-sensors-numeric.md#homie-sensor-gas-co210)
`homie-input-gas-ch4`     |[`homie-sensor-gas-ch4`](cap-sensors-numeric.md#homie-sensor-gas-ch410)
`homie-input-gas-voc`     |[`homie-sensor-gas-voc`](cap-sensors-numeric.md#homie-sensor-gas-voc10)
`homie-input-humidity`    |[`homie-sensor-humidity`](cap-sensors-numeric.md#homie-sensor-humidity10)


### Binary inputs

Input capability          | based on sensor type
--------------------------|---------------------
`homie-input-binary`      |[`homie-sensor-binary`](cap-sensors-binary.md#homie-sensor-binary10)
`homie-input-power-switch`|[`homie-sensor-power-switch`](cap-sensors-binary.md#homie-sensor-power-switch10)
`homie-input-window`      |[`homie-sensor-window`](cap-sensors-binary.md#homie-sensor-window10)
`homie-input-valve`       |[`homie-sensor-valve`](cap-sensors-binary.md#homie-sensor-valve10)
`homie-input-presence`    |[`homie-sensor-presence`](cap-sensors-binary.md#homie-sensor-presence10)
