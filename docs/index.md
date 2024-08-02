# Introduction to Homie Profiles

## Abstract

This document aims to list a set of predefined device and capability profiles to be used in IoT and home automation scenarios.
Where the [Homie Convention](https://homieiot.github.io/) provides a standardized way for device discovery, descriptions,
and value/event update publishing, the profiles defined here will add meaning to those devices, nodes and properties published.

When using the Homie Convention to describe a light-bulb for example, there are many ways in which this can be done;

1. a single property `power`: 0-100%
2. a single property `brightness`: 0-255
3. a boolean `power` property and a `brightness` property 0-100%

Now how is a Homie controller to know all of the possible different implementations? Hence we define a profile with a
unique name and publish it along with the device description.

The official Homie Profiles are documented in this project.

## Goals

- provide a unambiguous representation of the state a device is in
- provide a strict expectation for the user how a device behaves
- make it easy to discover compatible devices/capabilities
- allow flexibility in having required/optional/custom elements
- provide hints for a UI, without specifying the UI implementation

## Types of profiles

Profiles are defined on 2 levels;

1. Capability level: this maps to a Homie "node" within a device, and it defines the properties for the capability/node.
2. Device level: this maps to the Homie device, and it defines the capabilities for the device

This structure allows for reuse of Capability profiles across different Device profiles

### Capability profiles

Capability profiles are defined on a node level. Each node represents a certain capability a device posesses which provides state or functionality via its properties.

A Capability profile specifies the following:

- required properties; eg. a Dimmer MUST have a "brightness" property
- optional properties; eg. a Dimmer MAY have a "minimum" and "maximum" property
- node/capability behaviour across properties to make behaviour unambiguous. For example:
    - if either "minimum" or "maximum" is provided, the other MUST also be provided
    - If "minimum" and "maximum" are provided, then the actual value sent to the device will be the "brightness" value mapped to the range between "minimum" and "maximum".

Note:

 - Capabilities MAY have additional properties, unless specified otherwise.
 - Each node represents an instantiation of a capability. There can be more than one of the same capability on a device (e.g. 2 button capability nodes for a wall switch with 2 buttons)

### Device profiles

A device profile specifies the following:

- required capabilities; eg. a Light MUST have a "switch" capability
- optional capabilities; eg. a Light MAY have a "dimmer" capability
- device/node/property behaviour across capabilities to make behaviour unambiguous. For example:
    - If a device has a "color" capability, then it MUST also have a "dimmer" capability
    - Changing the "dimmer" state MUST NOT change the "switch" state

Note: devices MAY have additional Capabilities and/or Nodes, unless specified otherwise.


## Advertizing

The way the profile is announced is 2 fold. It is advertized using a Homie MQTT topic, as well as in the JSON description document.
The components of the profile consist of the profile name, as well as the supported version (major+minor).

### Capability advertizing

The `node` level attribute where to publish the profile in use is: `$profile`.

The topic will have the name and the major version number, where the value contains the minor version number;

    homie/5/[device]/[node]/$profile/[profile]/[maj] → "[min]"


The device description JSON document, in the node-object;

```json
  {
    ...,
    "$profile": ["[profile]/[maj]/[min]"],
    ...
  }
```
**Note**: in the JSON document the `$profile` property holds an `array` value, not a single string. See [versioning](#versioning) below.

So for a `homie-sensor-temperature`, version `1.3`, documented as `homie-sensor-temperature/1/3` the capability would be advertized as:

    homie/5/[device]/[node]/$profile/homie-sensor-temperature/1 → "3"

```json
  {
    ...,
    "$profile": ["homie-sensor-temperature/1/3"],
    ...
  }
```

### Capability searching

For controllers looking to find a specific type of device they are compatible with, they can use the
published MQTT topic to query for the specific capabilities. For example:

Topic subscription | result
-------------------|--------
`homie/5/*/*/$profile/homie-sensor-temperature/*` | Will return all `homie-sensor-temperature` capabilities
`homie/5/mydevice/*/$profile/homie-sensor-temperature/*` | Will return all `homie-sensor-temperature` capabilities of device `mydevice`
`homie/5/*/*/$profile/homie-sensor-temperature/2` | Will return all `homie-sensor-temperature` capabilities compatible with version `2`

By parsing the returned topics and values, all relevant data can be extracted:

- device id
- node id
- supported capability major version
- supported capability minor version



## Versioning

Versioning is based on SemVer and is only done using the major and minor version components. This is because the profiles are
description documents describing features and functionality. Any patch update would only be a clarification, never a change. Hence
it does not make sense to advertize or search for versions including a patch version.

Since minor versions are backward compatible within a major version, there is no need to ever list multiple minor versions.
A device should only advertize the latest minor version it is compatible with. Eg. if a device advertizes version `1.2`, it
is implicit that it also supports versions `1.0` and `1.1` of the same profile.

If a device or a node supports either multiple profiles, or supports multiple major versions of a profile, then they can all
be advertized. Here's an example of a capability supporting 2 profiles, and one of them in 2 versions:

    homie/5/[device]/[node]/$profile/homie-sensor-temperature/1 → "2"
    homie/5/[device]/[node]/$profile/homie-sensor-temperature/2 → "0"
    homie/5/[device]/[node]/$profile/power-switch/1 → "3"

```json
  {
    ...,
    "$profile": [
      "homie-sensor-temperature/1/2",
      "homie-sensor-temperature/2/0",
      "power-switch/1/3"
    ],
    ...
  }
```

So overall profile support in this example is:

- `homie-sensor-temperature`, versions `1.0` to `1.2`, and `2.0`
- `power-switch`, versions `1.0` to `1.3`

**Note**: for this to work it is essential that there are no naming collissions between the properties used in
each of the profiles.
