# Input capability descriptions

This capability description describes an input node, which is derived from the numeric and binary sensor capabilities.

## Input introduction

Inputs are related to sensors. They are to be used in devices that take a specific value as an input to operate, typically the value originates from a sensor. For example:

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

## Input capability list

For the mandatory changes from the "sensor" capabilities see [differences from sensors](#differences-from-sensors).

### Numeric inputs

Input capability          | based on sensor type
--------------------------|---------------------
`homie-input-numeric`     |[`homie-sensor-numeric`](sensors-numeric.md#homie-sensor-numeric10)
`homie-input-temperature` |[`homie-sensor-temperature`](sensors-numeric.md#homie-sensor-temperature10)
`homie-input-volume`      |[`homie-sensor-volume`](sensors-numeric.md#homie-sensor-volume10)
`homie-input-volt`        |[`homie-sensor-volt`](sensors-numeric.md#homie-sensor-volt10)
`homie-input-current`     |[`homie-sensor-current`](sensors-numeric.md#homie-sensor-current10)
`homie-input-power`       |[`homie-sensor-power`](sensors-numeric.md#homie-sensor-power10)
`homie-input-energy`      |[`homie-sensor-energy`](sensors-numeric.md#homie-sensor-energy10)
`homie-input-frequency`   |[`homie-sensor-frequency`](sensors-numeric.md#homie-sensor-frequency10)
`homie-input-battery`     |[`homie-sensor-battery`](sensors-numeric.md#homie-sensor-battery10)
`homie-input-distance`    |[`homie-sensor-distance`](sensors-numeric.md#homie-sensor-distance10)
`homie-input-speed`       |[`homie-sensor-speed`](sensors-numeric.md#homie-sensor-speed10)
`homie-input-pressure`    |[`homie-sensor-pressure`](sensors-numeric.md#homie-sensor-pressure10)
`homie-input-light`       |[`homie-sensor-light`](sensors-numeric.md#homie-sensor-light10)
`homie-input-gas-co`      |[`homie-sensor-gas-co`](sensors-numeric.md#homie-sensor-gas-co10)
`homie-input-gas-co2`     |[`homie-sensor-gas-co2`](sensors-numeric.md#homie-sensor-gas-co210)
`homie-input-gas-ch4`     |[`homie-sensor-gas-ch4`](sensors-numeric.md#homie-sensor-gas-ch410)
`homie-input-gas-voc`     |[`homie-sensor-gas-voc`](sensors-numeric.md#homie-sensor-gas-voc10)
`homie-input-humidity`    |[`homie-sensor-humidity`](sensors-numeric.md#homie-sensor-humidity10)


### Binary inputs

Input capability          | based on sensor type
--------------------------|---------------------
`homie-input-binary`      |[`homie-sensor-binary`](sensors-binary.md#homie-sensor-binary10)
`homie-input-power-switch`|[`homie-sensor-power-switch`](sensors-binary.md#homie-sensor-power-switch10)
`homie-input-window`      |[`homie-sensor-window`](sensors-binary.md#homie-sensor-window10)
`homie-input-valve`       |[`homie-sensor-valve`](sensors-binary.md#homie-sensor-valve10)
`homie-input-presence`    |[`homie-sensor-presence`](sensors-binary.md#homie-sensor-presence10)
`homie-input-heat-request`|[`homie-sensor-heat-request`](sensors-binary.md#homie-sensor-heat-request10)
`homie-input-cool-request`|[`homie-sensor-cool-request`](sensors-binary.md#homie-sensor-cool-request10)
