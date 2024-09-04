# Capability: Sensor - Binary

This capability description describes a generic binary sensor node, and derived unit specific sensors.

## Binary sensors introduction

A binary sensor is a sensor for any boolean type data, eg. on/off, open/close, presence/no-presence etc.

Specifically typed sensors are available for the most common ones. Each with their own profile name and version. The Node layout is the same for all, they only differ by supported `format` labels.

### Examples

A Homie device with 1 node as a presence/motion sensor.

#### Minimal example

This minimal example only implements the required properties.

Description:

```json
{
  "homie": "5.0",
  "name": "Motion sensor",
  "version": 3,
  "nodes": {

    "livingroom": {
      "$profile": ["homie-sensor-presence/1/0"],
      "name": "Livingroom Motion",
      "value": {
        "settable": false,
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
homie/5/living-motion/$state            → "ready"
homie/5/living-motion/$description      → "{... the above json doc ...}"
homie/5/living-motion/livingroom/$profile/homie-sensor-presence/1 → "0"
homie/5/living-motion/livingroom/value  → "false"
```

#### Full example

This example implements a sensor including all optional properties.

Description:

```json
{
  "homie": "5.0",
  "name": "Motion sensor livingroom",
  "version": 7,
  "nodes": {

    "livingroom": {
      "$profile": ["homie-sensor-presence/1/0"],
      "name": "Livingroom Motion",
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
homie/5/living-motion/$state                  → "ready"
homie/5/living-motion/$description            → "{... the above json doc ...}"
homie/5/living-motion/livingroom/$profile/homie-sensor-presence/1 → "0"
homie/5/living-motion/livingroom/value        → "false"
homie/5/living-motion/livingroom/raw          → "true"
homie/5/living-motion/livingroom/raw-topic    → "homeassistant/sensor/some/topic"
homie/5/living-motion/livingroom/topic-falsy  → "false,False,off,Off,0"
homie/5/living-motion/livingroom/invert       → "true"
```

### Virtual sensors

Sensors can be virtual sensors, in the sense that the value is not actually measured, but set by a Homie controller.
For example;

- a virtual motion sensor called "ground-floor", that has state "presence" as long as any of the sensors on the ground floor is tripped.
- a virtual temperature sensor that is set by a controller as the averaga value of all temperature sensors in a room.

By implementing the optional [`raw` property](#raw) the sensor values become settable, and hence the same profile can be used as a virtual sensor.

## homie-sensor-binary/1/0

This is the common version if no specific profile is available. The profile name is: `homie-sensor-binary/1/0`.


### Required properties

#### value

The `value` property (required) is the property of type `boolean` that holds the sensor value (after applying the `invert` logic).

attributes | value | remark
-|-|-
property-id | "`value`" |
settable | `false` | virtual sensors can use the `raw` property to set values.
retained | `true` |
format |  | the `format` attribute SHOULD be specified to add labels for the `false` and `true` values.
type | "`boolean`" |


### Optional properties

#### raw

The `raw` property (optional) is the property that holds the raw sensor value, before inverting.

**Note:**: This property becomes REQUIRED when any of the following properties are present; [`raw-topic`](#raw-topic) or [`invert`](#invert).

attributes | value | remark
-|-|-
property-id | "`raw`" |
settable | `true`/`false` | mostly non-settable, except for virtual sensors.
retained | `true` |
type | "`boolean`" |

#### raw-topic

The "`raw-topic`" property (optional) is a MQTT topic where the raw-value should be fetched (by subscribing to it). If "`raw-topic`" is present
the "`raw`" property MUST also be present (and MAY be settable).
If the value is set to an empty string, then the topic subscription should be cancelled.

The behaviour when receiving a value update from the subscription should be as if the "`raw`" property was settable,
and received a value on its "`/set`" topic. See [`topic-falsy`](#topic-falsy) on how to interpret received values from the subscription.

attributes | value | remark
-|-|-
property-id | "`raw-topic`" |
settable | `true` |
retained | `true` |
type | `string` |
unit | n.a. |

#### topic-falsy

The "`topic-falsy`" property (optional) takes a comma separated list of values that are considered a boolean `false`.
Any value recieved over the topic indicated by "`topic-falsy`", is checked against this list. If there is a match
the value is considered a boolean `false`, otherwise a boolean `true`. Leading- and trailing whitespace is significant, and
the values are case-sensitive.

If "`topic-falsy`" is present the "`raw-topic`" property MUST also be present. If "`topic-falsy`" is not present then
only a string "`false`" is considered a falsy value.

attributes | value | remark
-|-|-
property-id | "`topic-falsy`" |
settable | `true` |
retained | `true` |
type | `string` |
unit | n.a. |

#### invert

The `invert` property (optional) is used to invert the raw sensor value.

attributes | value | remark
-|-|-
property-id | "`invert`" |
settable | `true` |
retained | `true` |
type | "`boolean`" |
format | "`no,yes`"


## homie-sensor-power-switch/1/0

The profile name is: `homie-sensor-power-switch/1/0`

The implementation is identical to the [`homie-sensor-binary/1/0`](#homie-sensor-binary10) capability with the
following modifications:

- the `format` attribute of the `value` property MUST be `"off,on"`.


## homie-sensor-window/1/0

The profile name is: `homie-sensor-window/1/0`

The implementation is identical to the [`homie-sensor-binary/1/0`](#homie-sensor-binary10) capability with the
following modifications:

- the `format` attribute of the `value` property MUST be `"closed,open"`.


## homie-sensor-valve/1/0

The profile name is: `homie-sensor-valve/1/0`

The implementation is identical to the [`homie-sensor-binary/1/0`](#homie-sensor-binary10) capability with the
following modifications:

- the `format` attribute of the `value` property MUST be `"closed,open"`.


## homie-sensor-presence/1/0

The profile name is: `homie-sensor-presence/1/0`

The implementation is identical to the [`homie-sensor-binary/1/0`](#homie-sensor-binary10) capability with the
following modifications:

- the `format` attribute of the `value` property MUST be `"no-presence,presence"`.

