# Binary sensors capability descriptions

This capability description describes a generic binary sensor node, and derived unit specific sensors.

## Binary sensors introduction

A binary sensor is a sensor for any boolean type data, eg. on/off, open/close, presence/no-presence etc.

Specifically typed sensors are available for the most common ones. Each with their own profile name and version. The Node layout is the same for all, they only differ by supported `format` labels.

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
and received a value on its "`/set`" topic.

attributes | value | remark
-|-|-
property-id | "`raw-topic`" |
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


## homie-sensor-heat-request/1/0

The profile name is: `homie-sensor-heat-request/1/0`

The implementation is identical to the [`homie-sensor-binary/1/0`](#homie-sensor-binary10) capability with the
following modifications:

- the `format` attribute of the `value` property MUST be `"off,heat"`.


## homie-sensor-cool-request/1/0

The profile name is: `homie-sensor-cool-request/1/0`

The implementation is identical to the [`homie-sensor-binary/1/0`](#homie-sensor-binary10) capability with the
following modifications:

- the `format` attribute of the `value` property MUST be `"off,cool"`.

