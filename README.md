# Abstract

This document aims to list a set of predefined device profiles to be used in home automation scenarios.

# Goals

- provide a unambiguous representation of the state a device is in
- provide a strict expectation for the user how a device behaves
- allow flexibility in having required/optional/custom elements
- provide hints for a UI, without specifying the UI implementation

# Profile structure

Profiles are defined on 2 levels;
1. Device level: this maps to the Homie device, and it defines the capabilities for the device
2. Capability level: this maps to a Homie "node" within a device, and it defines the properties for the capability/node.

This structure allows for reuse of Capability profiles across different Device profiles.

## Profile identifiers and versioning

Profile identifiers look like these examples:

    iot.homie.dev:light:v1.0
    iot.homie.cap:switch:v1.1

This makes an identifier based of 3 components, separated by `':'` (colon):
1. domain (reverse) to enable custom, extensible, unique identifiers
    - format: reverse ordered domain name (case insensitive), this allows anyone to register their own domain and create their own unique device/capability profile.
    - Note that despite the fact that there is (not yet) a domain `home.iot`, it is by means of this specification reserved for official Homie standardized profiles.
    - domain `dev.homie.iot` is reserved for offical Homie devices
    - domain `cap.homie.iot` is reserved for offical Homie capabilities
3. The profile name;
    - format: alphanumeric characters(a-z, 0-9), must start with a-z (case insensitive)
4. The profile version;
    - the format is the [Semantic Versioning](https://semver.org/) version, prefixed with a `v`
    - when using the profile to describe a device or capability, the Major and Minor version MUST be included, everything else (Patch, pre-release, etc.) MUST NOT be included.
    - when using to reference the specification document, all details may be included.

### Versioning

The identifiers refer to 2 different types of things;
1. the specification document of the device/capability
2. implementations of a device/capability

Since a Patch level version identifier indicates a fix or other non-functional change in the specification document. It should not be included when listing/announcing device capabilities. In those cases only Major and Minor version identifiers should be included, since those are the only ones that carry "functional" information.


## Device profiles

A device profile specifies the following:

- required capabilities; eg. a Light MUST have a "switch" capability
- optional capabilities; eg. a Light MAY have a "dimmer" capability
- device/node/property behaviour across capabilities to make behaviour unambiguous. eg.
    - If a device has a "color" capability, then it MUST also have a "dimmer" capability
    - Changing the "dimmer" state MUST NOT change the "switch" state

Note: devices MAY have additional Capabilities and/or Nodes, unless specified otherwise.

## Capability profiles

Capability profiles are defined on a node level. Each node represents a certain capability a device posesses which provides state or functionality via its properties.

A Capability profile specifies the following:

- required properties; eg. a Dimmer MUST have a "brightness" property
- optional properties; eg. a Dimmer MAY have a "minimum" and "maximum" property
- node/capability behaviour across properties to make behaviour unambiguous. eg. 
    - if either "minimum" or "maximum" is provided, the other MUST also be provided
    - If "minimum" and "maximum" are provided, then the actual value sent to the device will be the "brightness" value mapped to the range between "minimum" and "maximum".

Note: 
 - Capabilities MAY have additional properties, unless specified otherwise.
 - Each node represents an instantiation of a capability. There can be more than one of the same capability on a device (e.g. 2 button capability nodes for a wall switch with 2 buttons)



# Device descriptions

## Light
`type`: "iot.homie.dev:light:v1.0"

This device represents binary and dimmable lights with no color.

### Device capabilities

|id|type|optional|comment
|-|-|-|-|
|switch|`iot.homie.cap:switch:v1.0`|no|n.a.
|dimmer|`iot.homie.cap:dimmer:v1.0`|yes|n.a

The `id` is the required name on the Homie "Node" level

### Inter-capability behaviour

The switch has no effect on the dimmer state, nor the dimmer state on the switch. Examples:

|state|action|result|remark|
|-|-|-|-|
|`switch.state=true, dimmer.brightness=50`|set `switch.state=false`|`switch.state=false, dimmer.brightness=50`|brightness is unchanged
|`switch.state=false, dimmer.brightness=50`|set `dimmer.brightness=60`|`switch.state=false, dimmer.brightness=60`|light remains off


# Capability descriptions

## Switch
`type`: "iot.homie.cap:switch:v1.0"

*Example usages*: Represent a switchable actor, e.g. wall socket plug, light bulb.

### Properties

|id|type|settable (default)|retained|unit|format|comment
|-|-|-|-|-|-|-|
|state|`boolean`|yes|yes|-|off,on|format specifies labels for false/true
|action|`enum`|yes|no|-|`toggle`| toggles state between true and false


### Comments:
Since a boolean does not carry any context, the format property specifies the labels for the boolean values.
The format is the same as an `enum` where `false` is the first and `true` is the second entry.
Reasoning is that if the `enum` is an `array`, and `false` is often represented as 0 and `true` often as 1. Then those 0/1 values are the indices of the format `array` to ease implementations.

This format entry requires a change to the Homie protocol 

The value could also be specified as an `enum` all together, but having a `boolean` provides a hint to a UI as to how to render the property (see the "goals" section").

## Dimmer
`type`: "iot.homie.cap:dimmer:v1.0"

*Example usages*: Dimmable light.

### Properties

|id|type|settable (default)|retained|unit|format|comment
|-|-|-|-|-|-|-|
|brightness|`integer`|yes|yes|%|`1:100`|0 is not allowed since it would be ambiguous
|action|`enum`|yes|no|-|`brighter,darker`| 

### Comments:
`brightness` is restricted from 1 to 100%, 0 would be ambiguous. It is important to note that this is the STATE representation, not the UI representation. A UI is still free in how it renders the state; a single slider 0-100, a switch and 1-100, or a switch and 0-100.

The brighter and darker actions are problematic in so far as that we would need to define the delta adjustment value. But depending on the devices practical values could be difficult to align.
An argument could be made to completly remove this feature all together but it has proved to be rather practical in home automation scenarios.
Another way could be a 'delta' property that simply receives +- values for adjustments.

## Colorlight
`type`: "iot.homie.cap:colorlight:v1.0"

*Example usages*: Color control for a lightbulb or LED strip.

### Properties

|id|type|settable (default)|retained|unit|format|comment
|-|-|-|-|-|-|-|
|color|`color`|yes|yes|-|`rgb/hsv`|
|color-temperature|`integer`|yes|yes|Mired|`min:max`| fomat specifies min max values supported by the device
