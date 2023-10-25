# Sensor capability descriptions

This capability description describes a generic sensor node.

## Sensor introduction

A sensor node measures a single sensor value. The value is published under the property `value`. The descriptions vary by the type of value measured. Depending on the type of sensor, some properties and/or requirements my vary.

They are very strictly defined, allowing only for a single `unit` or `format` attribute. This allows MQTT based querying of specific sensor types, without having to parse the device description. See the topic structure at [Advertizing](#advertizing).

Sensors can be virtual sensors, in the sense that the value is not actually measured, but set by a Homie controller.
For example;

- a temperature sensor that is set by a controller as the averaga value of all temperature sensors in a room.
- a motion sensor called "ground-floor", that has state "presence" as long as any of the sensors on the ground floor is tripped.

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

If any of the calibration properties are present, then the value is calculated as: `value = (raw-value + offset) * factor`

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



## Binary sensor 1.0

### Description

A binary sensor is a sensor for any boolean type data, eg. on/off, open/close, presence/no-presence etc.

Specifically typed sensors are available for the most common ones. Each with their own profile name and version. The Node layout is the same for all, they only differ by supported `format` labels.

### Profile-names

Profile                     | Format (false/true) | description
----------------------------|----------------|-----------------
`homie-binary-sensor/1/0`   | any            | This is the common version if no specific profile is available. 
`homie-sensor-power-switch` | "`off,on`"     | Any power like application; light, switch, pump, etc.
`homie-sensor-window`       | "`close,open`" | Window and door sensors
`homie-sensor-valve`        | "`close,open`" | Valve sensors
`homie-sensor-presence`     | "`no-presence,presence`" | Presence and motion  sensors
`homie-sensor-heat-request` | "`off,heat`"   | Thermostat device requesting heating
`homie-sensor-cool-request` | "`off,cool`"   | Thermostat device requesting cooling

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


