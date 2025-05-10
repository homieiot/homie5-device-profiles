# Device: Sensor

This device description describes a sensor device containing one or more sensors.

## Sensor introduction

A sensor is a device that contains one or more sensor capabilities. These devices come in many forms. Such as single sensors, multi-sensors, or virtual ones. Many actors also contain sensors capabilities. However sensor devices do generally not contain actor capabilities.

## Profile name

The profile for this device is `homie-sensor/1/0`. It is located in the device description JSON document in the device level `$profile` attribute, and the `$profile` topic.

## Examples

Description:

```json
{
  "homie": "5.0",
  "name": "Living sensor",
  "version": 3,
  "$profile": ["homie-sensor/1/0"],
  "nodes": {

    "presence": {
      "$profile": ["homie-sensor-presence/1/0"],
      ...
    },
    "temperature": {
      "$profile": ["homie-sensor-temperature/1/0"],
      ...
    },
    "light": {
      "$profile": ["homie-sensor-light/1/0"],
      ...
    }

  }
}
```

Topics:

```
homie/5/multi-sensor/$profile/homie-sensor/1 → "0"
```

## Nodes

This device-profile MUST implement at least one of the following capability-profiles:

Node-ID | Capability-Profile | required | additional<br/>requirements
-|-|-|-
power-switch|[homie-sensor-power-switch/1/0](cap-sensors-binary.md#homie-sensor-power-switch10) | no | no
window      |[homie-sensor-window/1/0](cap-sensors-binary.md#homie-sensor-window10) | no | no
valve       |[homie-sensor-valve/1/0](cap-sensors-binary.md#homie-sensor-valve10) | no | no
presence    |[homie-sensor-presence/1/0](cap-sensors-binary.md#homie-sensor-presence10) | no | no
temperature |[homie-sensor-temperature](cap-sensors-numeric.md#homie-sensor-temperature10) | no | no
volume      |[homie-sensor-volume](cap-sensors-numeric.md#homie-sensor-volume10) | no | no
volt        |[homie-sensor-volt](cap-sensors-numeric.md#homie-sensor-volt10) | no | no
current     |[homie-sensor-current](cap-sensors-numeric.md#homie-sensor-current10) | no | no
power       |[homie-sensor-power](cap-sensors-numeric.md#homie-sensor-power10) | no | no
energy      |[homie-sensor-energy](cap-sensors-numeric.md#homie-sensor-energy10) | no | no
frequency   |[homie-sensor-frequency](cap-sensors-numeric.md#homie-sensor-frequency10) | no | no
battery     |[homie-sensor-battery](cap-sensors-numeric.md#homie-sensor-battery10) | no | no
distance    |[homie-sensor-distance](cap-sensors-numeric.md#homie-sensor-distance10) | no | no
speed       |[homie-sensor-speed](cap-sensors-numeric.md#homie-sensor-speed10) | no | no
pressure    |[homie-sensor-pressure](cap-sensors-numeric.md#homie-sensor-pressure10) | no | no
light       |[homie-sensor-light](cap-sensors-numeric.md#homie-sensor-light10) | no | no
gas-co      |[homie-sensor-gas-co](cap-sensors-numeric.md#homie-sensor-gas-co10) | no | no
gas-co2     |[homie-sensor-gas-co2](cap-sensors-numeric.md#homie-sensor-gas-co210) | no | no
gas-ch4     |[homie-sensor-gas-ch4](cap-sensors-numeric.md#homie-sensor-gas-ch410) | no | no
gas-voc     |[homie-sensor-gas-voc](cap-sensors-numeric.md#homie-sensor-gas-voc10) | no | no
humidity    |[homie-sensor-humidity](cap-sensors-numeric.md#homie-sensor-humidity10) | no | no

Additional nodes MAY be added to the device.
