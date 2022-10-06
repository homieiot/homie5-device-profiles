# Abstract

This document aims to list a set of predefined device profiles to be used in home automation scenarios.

# goals

- provide a unambiguous representation of the state a device is in
- provide a strict expectation for the user how a device behaves
- allow flexibility in having required/optional/custom elements
- provide hints for a UI, without specifying the UI implementation

# Device structure

Profiles are defined on a node level. Each node represents a certain capability a device posesses which provides state or functionality via its properties.

E.g. a dimmable RGB LED lighbulb could be represented via the following nodes:
- switch
- dimmer
- colorlight


# Node descriptions

## Switch
`type`: "homie-device-profile/v1/type=switch"

*Example usages*: Represent a switchable actor, e.g. wall socket plug, light bulb.

### Properties

|id|type|settable (default)|retained|unit|format|comment
|-|-|-|-|-|-|-|
|state|`boolean`|yes|yes|-|off,on|format specifies labels for true/false
|action|`enum`|yes|no|-|`toggle`| toggles state between true and false


### Comments:
Since a boolean does not carry any context, the format property specifies the labels for the boolean values.
The format is the same as an `enum` where `false` is the first and `true` is the second entry.
Reasoning is that if the `enum` is an `array`, and `false` is often represented as 0 and `true` often as 1. Then those 0/1 values are the indices of the format `array` to ease implementations.

This format entry requires a change to the Homie protocol 

The value could also be specified as an `enum` all together, but having a `boolean` provides a hint to a UI as to how to render the property (see the "goals" section").

## Dimmer
`type`: "homie-device-profile/v1/type=dimmer"

*Example usages*: Dimmable light.

### Properties

|id|type|settable (default)|retained|unit|format|comment
|-|-|-|-|-|-|-|
|brightness|`integer`|yes|yes|%|-|
|action|`enum`|yes|no|-|`brighter,darker`| 

### Comments:
The brighter and darker actions are problematic in so far as that we would need to define the delta adjustment value. But depending on the devices practical values could be difficult to align.
An argument could be made to completly remove this feature all together but it has proved to be rather practical in home automation scenarios.
Another way could be a 'delta' property that simply receives +- values for adjustments.

## Colorlight
`type`: "homie-device-profile/v1/type=colorlight"

*Example usages*: Color control for a lightbulb or LED strip.

### Properties

|id|type|settable (default)|retained|unit|format|comment
|-|-|-|-|-|-|-|
|color|`color`|yes|yes|-|`rgb/hsv`|
|color-temperature|`integer`|yes|yes|Mired|`min:max`| fomat specifies min max values supported by the device
