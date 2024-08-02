# Numeric sensors capability descriptions

This capability description describes a generic numeric sensor node, and derived unit-specific sensors.

## Numeric sensors introduction

A sensor node measures a single sensor value. The value is published under the property `value`. The descriptions vary by the type of value measured. Depending on the type of sensor, some properties and/or requirements my vary.

They are very strictly defined, allowing only for a single `unit` or `format` attribute. This allows MQTT based querying of specific sensor types, without having to parse the device description.

### Virtual sensors

Sensors can be virtual sensors, in the sense that the value is not actually measured, but set by a Homie controller. The
profile takes this into account by allowing the (raw) value to be settable in those cases.

For example;

- a virtual temperature sensor that is set by a controller as the averaga value of all temperature sensors in a room.
- a virtual motion sensor called "ground-floor", that has state "presence" as long as any of the sensors on the ground floor is tripped.

By implementing the optional [`raw` property](#raw) the sensor values become settable, and hence the same profile can be used as a virtual sensor.

## homie-sensor-numeric/1/0

This is the common version if no specific profile is available. The profile name is: `homie-sensor-numeric/1/0`.

### Description

A numerical sensor is a sensor for any numerical type data, eg. speed, temperature, volume etc. The type is not further specified, because it can be set using the regular `unit` attribute on the value. In a similar way the limits and precision can be specified using the `format` property.

The most basic implementation will only have a `value` property. There are more advanced options that support calibration, and virtual sensors of this type.

Specifically typed sensors are available for the most common formats. Each with their own profile name and version. The Node layout is the same for all, they only differ by supported `unit` types


### Required properties

#### value

The `value` property (required) is the property that holds the sensor value (after calibration).

attributes | value | remark
-|-|-
property-id | "`value`" |
settable | `false` | virtual sensors can set the `raw` property.
retained | `true` |
unit |  | the `unit` attribute on this property MUST be specified, see the Homie specification for most common supported units.
format |  | the `format` attribute SHOULD be specified for precision, minimum and maximum values.
type | "`float`" | To represent integers, us the `format` attribute

If any of the calibration properties are present, then the value is calculated as:

    value = (raw-value + offset) * factor

The `offset` defaults to `0` if not present, and the `factor` defaults to `1` if not present.

### Optional properties

#### raw

The `raw` property (optional) is the property that holds the raw sensor value, before calibration.

**Note:**: This property becomes REQUIRED when any of the following properties are present; [`raw-topic`](#raw-topic), [`offset`](#offset), or [`factor`](#factor).

attributes | value | remark
-|-|-
property-id | "`raw`" |
settable | `true`/`false` | mostly non-settable, except for virtual sensors.
retained | `true` |
type | `float` |
unit |  | Can be any unit, but most likely the same as the `value` property (or a corresponding type, eg. `°F` as the `raw` type, with `°C` as the `value` type)


#### raw-topic

The "`raw-topic`" property (optional) is an MQTT topic where the raw-value should be fetched (by subscribing to it).
This allows for creating Homie sensors from any MQTT topic in the system.

The behaviour when receiving a value update from the subscription should be as if the "`raw`" property was settable,
and received a value on its "`/set`" topic. If the value is set to an empty string, then the topic subscription should be cancelled.

attributes | value | remark
-|-|-
property-id | "`raw-topic`" |
settable | `true` |
retained | `true` |
type | `string` |
unit | "" | This MUST remain unset, since it relies on the configurable remote value.

#### offset

The `offset` property (optional) is used to calibrate the raw sensor value. See `value` for [calibration calculation](#value).

attributes | value | remark
-|-|-
property-id | "`offset`" |
settable | `true` |
retained | `true` |
type | `float` |
unit |  |

#### factor

The `factor` property (optional) is used to calibrate the raw sensor value. See `value` for [calibration calculation](#value).

attributes | value | remark
-|-|-
property-id | "`factor`" |
settable | `true` |
retained | `true` |
type | `float` |
unit |  |



## homie-sensor-temperature/1/0

The profile name is: `homie-sensor-temperature/1/0`

The implementation is identical to the [`homie-sensor-numeric/1/0`](#homie-sensor-numeric10) capability with the
following modifications:

- the `units` attribute of the `value` property MUST be `"°C"`.

Converting the value into other units, like `"°F"`, for display is considered a [UI responsibility](#unit-conversions).


## homie-sensor-volume/1/0

The profile name is: `homie-sensor-volume/1/0`

The implementation is identical to the [`homie-sensor-numeric/1/0`](#homie-sensor-numeric10) capability with the
following modifications:

- the `units` attribute of the `value` property MUST be `"L"`.

Converting the value into other units for display is considered a [UI responsibility](#unit-conversions).


## homie-sensor-volt/1/0

The profile name is: `homie-sensor-volt/1/0`

The implementation is identical to the [`homie-sensor-numeric/1/0`](#homie-sensor-numeric10) capability with the
following modifications:

- the `units` attribute of the `value` property MUST be `"V"`.

Converting the value into other units for display is considered a [UI responsibility](#unit-conversions).


## homie-sensor-current/1/0

The profile name is: `homie-sensor-current/1/0`

The implementation is identical to the [`homie-sensor-numeric/1/0`](#homie-sensor-numeric10) capability with the
following modifications:

- the `units` attribute of the `value` property MUST be `"A"`.

Converting the value into other units for display is considered a [UI responsibility](#unit-conversions).


## homie-sensor-power/1/0

The profile name is: `homie-sensor-power/1/0`

The implementation is identical to the [`homie-sensor-numeric/1/0`](#homie-sensor-numeric10) capability with the
following modifications:

- the `units` attribute of the `value` property MUST be `"W"`.

Converting the value into other units for display is considered a [UI responsibility](#unit-conversions).


## homie-sensor-energy/1/0

The profile name is: `homie-sensor-energy/1/0`

The implementation is identical to the [`homie-sensor-numeric/1/0`](#homie-sensor-numeric10) capability with the
following modifications:

- the `units` attribute of the `value` property MUST be `"kWh"`.

Converting the value into other units for display is considered a [UI responsibility](#unit-conversions).


## homie-sensor-frequency/1/0

The profile name is: `homie-sensor-frequency/1/0`

The implementation is identical to the [`homie-sensor-numeric/1/0`](#homie-sensor-numeric10) capability with the
following modifications:

- the `units` attribute of the `value` property MUST be `"Hz"`.

Converting the value into other units for display is considered a [UI responsibility](#unit-conversions).


## homie-sensor-battery/1/0

The profile name is: `homie-sensor-battery/1/0`

The implementation is identical to the [`homie-sensor-numeric/1/0`](#homie-sensor-numeric10) capability with the
following modifications:

- the `units` attribute of the `value` property MUST be `"%"`.

Converting the value into other units for display is considered a [UI responsibility](#unit-conversions).


## homie-sensor-distance/1/0

The profile name is: `homie-sensor-distance/1/0`

The implementation is identical to the [`homie-sensor-numeric/1/0`](#homie-sensor-numeric10) capability with the
following modifications:

- the `units` attribute of the `value` property MUST be `"m"`.

Converting the value into other units for display is considered a [UI responsibility](#unit-conversions).


## homie-sensor-speed/1/0

The profile name is: `homie-sensor-speed/1/0`

The implementation is identical to the [`homie-sensor-numeric/1/0`](#homie-sensor-numeric10) capability with the
following modifications:

- the `units` attribute of the `value` property MUST be `"m/s"`.

Converting the value into other units for display is considered a [UI responsibility](#unit-conversions).


## homie-sensor-pressure/1/0

The profile name is: `homie-sensor-pressure/1/0`

The implementation is identical to the [`homie-sensor-numeric/1/0`](#homie-sensor-numeric10) capability with the
following modifications:

- the `units` attribute of the `value` property MUST be `"Pa"`.

Converting the value into other units for display is considered a [UI responsibility](#unit-conversions).


## homie-sensor-light/1/0

The profile name is: `homie-sensor-light/1/0`

The implementation is identical to the [`homie-sensor-numeric/1/0`](#homie-sensor-numeric10) capability with the
following modifications:

- the `units` attribute of the `value` property MUST be `"lx"`.

Converting the value into other units for display is considered a [UI responsibility](#unit-conversions).


## homie-sensor-gas-co/1/0

Measures carbon monoxide, the profile name is: `homie-sensor-gas-co/1/0`

The implementation is identical to the [`homie-sensor-numeric/1/0`](#homie-sensor-numeric10) capability with the
following modifications:

- the `units` attribute of the `value` property MUST be `"ppm"`.

Converting the value into other units for display is considered a [UI responsibility](#unit-conversions).


## homie-sensor-gas-co2/1/0

Measures carbon dioxide, the profile name is: `homie-sensor-gas-co2/1/0`

The implementation is identical to the [`homie-sensor-numeric/1/0`](#homie-sensor-numeric10) capability with the
following modifications:

- the `units` attribute of the `value` property MUST be `"ppm"`.

Converting the value into other units for display is considered a [UI responsibility](#unit-conversions).


## homie-sensor-gas-ch4/1/0

Measures methane, the profile name is: `homie-sensor-gas-ch4/1/0`

The implementation is identical to the [`homie-sensor-numeric/1/0`](#homie-sensor-numeric10) capability with the
following modifications:

- the `units` attribute of the `value` property MUST be `"ppm"`.

Converting the value into other units for display is considered a [UI responsibility](#unit-conversions).


## homie-sensor-gas-voc/1/0

Measures volatile organic compounds, the profile name is: `homie-sensor-gas-voc/1/0`

The implementation is identical to the [`homie-sensor-numeric/1/0`](#homie-sensor-numeric10) capability with the
following modifications:

- the `units` attribute of the `value` property MUST be `"ppm"`.

Converting the value into other units for display is considered a [UI responsibility](#unit-conversions).


## homie-sensor-humidity/1/0

Measures relative air humidity, the profile name is: `homie-sensor-humidity/1/0`

The implementation is identical to the [`homie-sensor-numeric/1/0`](#homie-sensor-numeric10) capability with the
following modifications:

- the `units` attribute of the `value` property MUST be `"%"`.

Converting the value into other units for display is considered a [UI responsibility](#unit-conversions).


## Unit conversions

The reported values will always be in the specified unit as defined by the profile. Formatting them
for display to the user is considered a UI responsibility.

Unit conversions for raw-values (coming in via the `raw` or `raw-topic` properties) can be done
using the `offset` and `factor` properties.

Profile                        | Unit  | Raw unit | delta | factor
-------------------------------|-------|----------|-------|-------
`homie-sensor-temperature/1/0` | `°C`  | `°F`     | -32   | 0.555556 (== 5/9)
`homie-sensor-volume/1/0`      | `L`   | `gal`    | 0     | 3.78541
`homie-sensor-volume/1/0`      | `L`   | `m³`     | 0     | 0.001
`homie-sensor-volt/1/0`        | `V`   | | |
`homie-sensor-current/1/0`     | `A`   | | |
`homie-sensor-power/1/0`       | `W`   | `kW`     | 0     | 0.001
`homie-sensor-energy/1/0`      | `kWh` | `kJ`     | 0     | 0.0002778
`homie-sensor-energy/1/0`      | `kWh` | `kcal`   | 0     | 0.0011622
`homie-sensor-frequency/1/0`   | `Hz`  | `rpm`    | 0     | 0.01666667 (== 1/60)
`homie-sensor-battery/1/0`     | `%`   | | |
`homie-sensor-distance/1/0`    | `m`   | `cm`     | 0     | 0.01
`homie-sensor-distance/1/0`    | `m`   | `km`     | 0     | 1000
`homie-sensor-distance/1/0`    | `m`   | `ft`     | 0     | 0.3048
`homie-sensor-distance/1/0`    | `m`   | `in`     | 0     | 0.0254
`homie-sensor-distance/1/0`    | `m`   | `mi`     | 0     | 1609.3
`homie-sensor-speed/1/0`       | `m/s` | `kn`     | 0     | 0.514444
`homie-sensor-speed/1/0`       | `m/s` | `km/h`   | 0     | 0.277778
`homie-sensor-pressure/1/0`    | `Pa`  | `psi`    | 0     | 6894.76
`homie-sensor-pressure/1/0`    | `Pa`  | `bar`    | 0     | 100000
`homie-sensor-pressure/1/0`    | `Pa`  | `psi`    | 0     | 6894.76
`homie-sensor-pressure/1/0`    | `Pa`  | `atm`    | 0     | 101.325
`homie-sensor-light/1/0`       | `lx`  | | |
`homie-sensor-gas-co/1/0`      | `ppm` | | |
`homie-sensor-gas-co2/1/0`     | `ppm` | | |
`homie-sensor-gas-ch4/1/0`     | `ppm` | | |
`homie-sensor-gas-voc/1/0`     | `ppm` | | |
`homie-sensor-humidity/1/0`    | `%`   | | |
