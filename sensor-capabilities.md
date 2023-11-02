# Sensor & Input capability descriptions

This capability description describes a generic sensor node, and related input nodes (that take sensor values).

## Sensor introduction

A sensor node measures a single sensor value. The value is published under the property `value`. The descriptions vary by the type of value measured. Depending on the type of sensor, some properties and/or requirements my vary.

They are very strictly defined, allowing only for a single `unit` or `format` attribute. This allows MQTT based querying of specific sensor types, without having to parse the device description. See the topic structure at [Advertizing](#advertizing).

### Inputs

Related to sensors are "inputs". To be used in devices that take a specific value as an input to operate, typically the value originated from a sensor. For example:

- a thermostat taking a temperature sensor as input
- a fan using a VOC sensor to ventilate based on air quality

The sensors are described below, their related inputs are identical except:

* the profile name uses "input" instead of "sensor", so a generic sensor has profile name
  "`homie-sensor-numeric/1/0`", and a generic input has profile name "`homie-input-numeric/1/0`"
* if an "input" has a "`raw`" property, then that property MUST be settable. If it doesn't have a "`raw`" property, then the "`value`" property MUST be settable.

### Virtual sensors

Sensors can be virtual sensors, in the sense that the value is not actually measured, but set by a Homie controller.
For example;

- a virtual temperature sensor that is set by a controller as the averaga value of all temperature sensors in a room.
- a virtual motion sensor called "ground-floor", that has state "presence" as long as any of the sensors on the ground floor is tripped.


## Advertizing

The way the profile is announced is 2 fold. It is advertized using a Homie topic (a Node level attribute), as well as in the JSON description document.

The `node` level attribute is `$profile`.


The topic;
```
homie/5/[device]/[node]/$profile/[profile]/[maj]/[min] → "[patch]"
```

The device description JSON document, in the node-object;
```
   { 
     ...,
     "$profile": "[profile]/[maj]/[min]",
     ...
   }
```


## Numeric sensor 1.0

### Description

A numerical sensor is a sensor for any numerical type data, eg. speed, temperature, volume etc. The type is not further specified, because it can be set using the regular `unit` attribute on the value. In a similar way the limits and precision can be specified using the `format` property.

The most basic implementation will only have a `value` property. There are more advanced options that support calibration, and virtual sensors of this type.

Specifically typed sensors are available for the most common formats. Each with their own profile name and version. The Node layout is the same for all, they only differ by supported `unit` types

### Profile-names

Profile                        | Units | description
-------------------------------|-------|-------------
`homie-sensor-numeric/1/0`     | any   | This is the common version if no specific profile is available
`homie-sensor-temperature/1/0` | `°C`  | Temperature sensor
`homie-sensor-volume/1/0`      | `L`   | Volume sensor
`homie-sensor-volt/1/0`        | `V`   | Voltage sensor (electricity)
`homie-sensor-current/1/0`     | `A`   | Current sensor (Ampere, electricity)
`homie-sensor-power/1/0`       | `W`   | Power sensor
`homie-sensor-energy/1/0`      | `kWh` | Energy sensor
`homie-sensor-frequency/1/0`   | `Hz`  | Frequency sensor
`homie-sensor-battery/1/0`     | `%`   | Battery percentage
`homie-sensor-distance/1/0`    | `m`   | Distance
`homie-sensor-speed/1/0`       | `m/s` | Speed
`homie-sensor-pressure/1/0`    | `Pa`  | Pressure
`homie-sensor-light/1/0`       | `lx`  | Light intensity
`homie-sensor-gas-co/1/0`      | `ppm` | Parts Per Million of carbon monoxide
`homie-sensor-gas-co2/1/0`     | `ppm` | Parts Per Million of carbon dioxide
`homie-sensor-gas-ch4/1/0`     | `ppm` | Parts Per Million of methane gas
`homie-sensor-gas-voc/1/0`     | `ppm` | Parts Per Million of volatile organic compounds
`homie-sensor-humidity/1/0`    | `%`   | Relative humidity


### Properties

#### value

The `value` property is the property that holds the sensor value (after calibration).

element | value | remark
-|-|-
property-id | "`value`" |
required | yes | This property must be present when the profile is set.
settable | no | virtual sensors can set the `raw` property.
retained | yes |
unit | - | the `unit` attribute on this property MUST be specified, see the Homie specification for most common supported units.
format | - | the `format` attribute SHOULD be specified for precision, minimum and maximum values.
type | "`float`" | To represent integers, us the `format` attribute

If any of the calibration properties are present, then the value is calculated as: `value = (raw-value + offset) * factor`. The `offset` defaults to `0` if not present, and the `factor` defaults to `1` if not present.

#### raw

The `raw` property is the property that holds the raw sensor value, before calibration.

element | value | remark
-|-|-
property-id | "`raw`" |
required | no |
settable | yes/no | mostly non-settable, except for virtual sensors.
retained | yes |
type | `float` | 
unit | any | Can be any unit, but most likely the same as the `value` property (or a corresponding type, eg. `°F` as the `raw` type, with `°C` as the `value` type)

#### raw-topic

The "`raw-topic`" property is a MQTT topic where the raw-value should be fetched (by subscribing to it). If "`raw-topic`" is present
the "`raw`" property MUST also be present (and MAY be settable). 
If the value is set to an empty string, then the topic subscription should be cancelled. 

The behaviour when receiving a value update from the subscription should be as if the "`raw`" property was settable,
and received a value on its "`/set`" topic.

element | value | remark
-|-|-
property-id | "`raw-topic`" |
required | no |
settable | yes |
retained | yes |
type | `string` |
unit | n.a. | 

#### calibration offset

The `offset` property is an optional property to calibrate the raw sensor value.

element | value | remark
-|-|-
property-id | "`offset`" |
required | no |
settable | yes |
retained | yes |
type | `float` | 
unit | - |

#### calibration factor

The `factor` property is an optional property to calibrate the raw sensor value.

element | value | remark
-|-|-
property-id | "`factor`" |
required | no |
settable | yes |
retained | yes |
type | `float` | 
unit | - |

#### calibration invert

The `invert` property is an optional property to invert the raw sensor value.

element | value | remark
-|-|-
property-id | "`invert`" |
required | no |
settable | yes |
retained | yes |
type | "`boolean`" | 
format | "`no,yes`"



## Binary sensor 1.0

### Description

A binary sensor is a sensor for any boolean type data, eg. on/off, open/close, presence/no-presence etc.

Specifically typed sensors are available for the most common ones. Each with their own profile name and version. The Node layout is the same for all, they only differ by supported `format` labels.

### Profile-names

Profile                         | Format (false/true) | description
--------------------------------|----------------|-----------------
`homie-sensor-binary/1/0`       | any            | This is the common version if no specific profile is available. 
`homie-sensor-power-switch/1/0` | "`off,on`"     | Any power like application; light, switch, pump, etc.
`homie-sensor-window/1/0`       | "`close,open`" | Window and door sensors
`homie-sensor-valve/1/0`        | "`close,open`" | Valve sensors
`homie-sensor-presence/1/0`     | "`no-presence,presence`" | Presence and motion  sensors
`homie-sensor-heat-request/1/0` | "`off,heat`"   | Thermostat device requesting heating
`homie-sensor-cool-request/1/0` | "`off,cool`"   | Thermostat device requesting cooling

### Properties

#### value

The `value` property is the property of type `boolean` that holds the sensor value.

element | value | remark
-|-|-
property-id | "`value`" |
required | yes | This property must be present when the profile is set.
settable | no | virtual sensors can use the `raw` property to set values.
retained | yes |
format | - | the `format` attribute MUST be specified to add labels for the `true` and `false` values.
type | "`boolean`" |


#### raw

The `raw` property is the property that holds the raw sensor value, before inverting.

element | value | remark
-|-|-
property-id | "`raw`" |
required | no |
settable | yes/no | mostly non-settable, except for virtual sensors.
retained | yes |
type | "`boolean`" | 

#### raw-topic

The "`raw-topic`" property is a MQTT topic where the raw-value should be fetched (by subscribing to it). If "`raw-topic`" is present
the "`raw`" property MUST also be present (and MAY be settable). 
If the value is set to an empty string, then the topic subscription should be cancelled. 

The behaviour when receiving a value update from the subscription should be as if the "`raw`" property was settable,
and received a value on its "`/set`" topic.

element | value | remark
-|-|-
property-id | "`raw-topic`" |
required | no |
settable | yes |
retained | yes |
type | `string` |
unit | n.a. | 

#### invert

The `invert` property is an optional property to invert the raw sensor value.

element | value | remark
-|-|-
property-id | "`invert`" |
required | no |
settable | yes |
retained | yes |
type | "`boolean`" | 
format | "`no,yes`"


## Unit conversions

Unit conversions can be done using the delta and factor properties

Profile                        | Unit  | Raw unit | delta | factor
-------------------------------|-------|----------|-------|-------
`homie-sensor-temperature/1/0` | `°C`  | `°F`     | -32   | 0.555556 (== 5/9)
`homie-sensor-volume/1/0`      | `L`   | `gal`    |       | 3.78541
`homie-sensor-volume/1/0`      | `L`   | `m³`     | 0     | 0.001
`homie-sensor-volt/1/0`        | `V`   | | |
`homie-sensor-current/1/0`     | `A`   | | |
`homie-sensor-power/1/0`       | `W`   | `kW`     | 0     | 0.001
`homie-sensor-energy/1/0`      | `kWh` | | |
`homie-sensor-frequency/1/0`   | `Hz`  | `rpm`    | 0     | 0.01666667 (== 1/60)
`homie-sensor-battery/1/0`     | `%`   | | |
`homie-sensor-distance/1/0`    | `m`   | `ft`     | 0     | 0.3048
`homie-sensor-speed/1/0`       | `m/s` | `kn`     | 0     | 0.514444
`homie-sensor-speed/1/0`       | `m/s` | `km/h`   | 0     | 0.277778
`homie-sensor-pressure/1/0`    | `Pa`  | `psi`    | 0     | 6894.76
`homie-sensor-light/1/0`       | `lx`  | | |
`homie-sensor-gas-co/1/0`      | `ppm` | | |
`homie-sensor-gas-co2/1/0`     | `ppm` | | |
`homie-sensor-gas-ch4/1/0`     | `ppm` | | |
`homie-sensor-gas-voc/1/0`     | `ppm` | | |
`homie-sensor-humidity/1/0`    | `%`   | | |
