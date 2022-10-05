# Abstract

This document aims to list a set of predefined device profiles to be used in home automation scenarios.

# Device structure

Profiles are defined on a node level. Each node represent a certain capability a device posesses which profide state or functionality via their properties.

e.g. a dimmable RGB LED lighbuld could be represented via the following nodes:
- switch
- dimmer
- colorlight


# Node descriptions

## Switch
`type`: "homie-device-profile/v1/type=switch"

*Example usages*: Represent a switchable actor, e.g. wall socket plug, light bulb.

### Properties

|id|type|settable (defualt)|retained|unit|format|comment
|-|-|-|-|-|-|-|
|state|`boolean`|yes|yes|-|-|`true` = ON, `false` = OFF
|action|`enum`|yes|no|-|`toggle`| 


### Comments:
It can be argued that this is a simple binary switch and that it could be represented by an enum, however boolean is the basic definition of a binary switch and provides a uniform representation. 
In generel there are a lot of devies with binary state representations, e.g. battery low: true/false, light: on/off, contact/door: open/closed, sensor: motion/nomotion, tilt-sensor: tilted/level....
We could opt for a binary state node with an enum, however this would then not properly convey they actual context for the capbility, a switch is not the same as a door-contact sensor, even though they both have essentially 2 states - but the goal should also be to provide context....


## Dimmer
`type`: "homie-device-profile/v1/type=dimmer"

*Example usages*: Dimm capability of a dimmable light.

### Properties

|id|type|settable (defualt)|retained|unit|format|comment
|-|-|-|-|-|-|-|
|brightness|`integer`|yes|yes|%|-|
|action|`enum`|yes|no|-|`brighter,darker`| 

### Comments:
The brighter and darker actions are problematic in so far as that we would need to define the delta adjustment value. But depending on the devices practical values could be difficult to align... 
An argument could be made to completly remove this feature all together but it has proved to be rather practical in home automation scenarios...
Another way could be a 'delta' property that simply receives +- values for adjustments... 

## Colorlight
`type`: "homie-device-profile/v1/type=colorlight"

*Example usages*: Dimm capability of a dimmable light.

### Properties

|id|type|settable (defualt)|retained|unit|format|comment
|-|-|-|-|-|-|-|
|color|`color`|yes|yes|-|`rgb/hsv`|
|color-temperature|`integer`|yes|yes|Mired|`min:max`| fomat specifies min max values supported by the device