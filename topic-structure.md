# Profile topic structure

**Note 1**: this proposal is not tied to the Homie v5-draft proposal. It will equally work with Homie 4.

**Note 2**: the device and capability profiles in this document are just examples and no official references of how those devices and capabilities would/should be implemented.

## Profile types

There are 2 profile types;

- Capability profiles (implemented on Homie Node level), eg.
    - `power-switch`
    - `dimmer`
- Device profiles (implemented on Homie Device level), eg.
    - `binary-light`, having;
        - `power` node, being a `power-switch` capability
    - `dimmable-light`, having;
        - `power` node, being a `power-switch` capability
        - `brightness` node, being a `dimmer` capability

For the remainder of this document we'll assume the `binary-light` to have a `power-switch` capability (called `power`). And the `dimmable-light` to have both the `power-switch` (called `power`) as well as the `dimmer` capability (called `brightness`).

Each Homie device can implement multiple device profiles, even of the same type. Each device-type can have multiple capability profiles, even of the same type.

If multiple instances of devices and/or capabilities are supported on a device, then each type MUST exist only in a single version. So if a device has 2 `binary-light` types, then they both MUST be of the same version.

Capabilities on a single Homie device, but on different device profiles MAY implement different versions of a capability. So if a single Homie device has 1 `binary-light` profile and 1 `dimmable-light` profile. Then the `binary-light` could have `power-switch` v1.0 and the `dimmable-light` could have `power-switch` v1.2. As long as the capability profiles within a single device profile are all the same version.

## Topic structures

Since a controller should be able to query the MQTT server for a specific device-profile
and/or capability profile, the required information must be in the topic structure
such that the proper mqtt subscription only returns the elements the controller
is interested in.


## Device profile versions

Profile information is stored under the `$profile` key. Because the query capability must be able to return compatible devices, the full-key should include the MAJOR version component, and a MINOR version. The value is the number of instances the device has of this profile.

A device implementing the profile with ID `binary-light` version `1.2` posts this topic:
```
homie/device-id/$profile/binary-light/1/2 → "1"
```

| element | meaning |
|---------|---------|
| `homie` | The Homie base-topic |
| `device-id` | The Homie ID of the Homie device |
| `$profile` | The topic indicating supported device profiles |
| `binary-light/1/2` | The device profile `binary-light` is supported at version `1.2` |
| `"1"` | The device implements 1 instance of the `power-switch` capability |

## Querying for device types

A controller that wishes to control `binary-light` instances and has an implementation compatible with version 1.2. Would execute the following steps to control the devices;

1. subscribe to `homie/+/$profile/binary-light/1/+`, this will return all Homie devices implementing a `binary-light` of version `1.x`.
2. any version found that is > `1.2` should be controlled as if it was `1.2`.
3. any version found <= `1.2` should be controlled at its specfied version.

## Multiple devices of the same type

If a device implements multiple of the same device profiles, then the versioned topic tells the controller how many there are.

Assuming a Zwave device with a double relay, controlling 2 lights, exposed as device type `binary-light` v1.0. Then it publishes;
```
homie/device-id/$profile/binary-light/1/0 → "2"
```
The capabilities will get a numbered post-fix to be able to identify them. So if both relais are enabled the following values will be posted;
```
homie/device-id/power-1/state-1 → "true"
homie/device-id/power-2/state-1 → "true"
```

| element | meaning |
|---------|---------|
| `homie` | The Homie base-topic |
| `device-id` | The Homie ID of the Homie device |
| `power-1` / `power-2` | The Homie Node ID, the name is dictated in the `binary-light` device-profile description, and it has 2 instances |
| `state-1` | the Homie property `state` is the property carrying the state of the relay. The ID is defined in either the device profile, or the capability profile (see below). This example has only 1 instance. |
| `"true"` | The value of the Homie property (in this case a boolean). |

(in this case the Homie property `state` is the property carrying the state of the relay)

The numbered postfix MUST be added, even if there is only 1 instance.

## Capability profile version

This is stored similarly to the device profile, but now on the Homie node level.
So a device implementing a single `power-switch` capability of version 1.1 would post;
```
homie/device-id/power-1/$profile/power-switch/1/1 → "1"
```

| element | meaning |
|---------|---------|
| `homie` | The Homie base-topic |
| `device-id` | The Homie ID of the Homie device |
| `power-1` | the Homie ID of the node. In this case it looks like the device profile specified a capability named `power`, and this is instance `1` of that capability. |
| `$profile` | The topic indicating supported capability profiles |
| `power-switch/1/1` | The capability profile `power-switch` is supported in version `1.1` |
| `"1"` | The capability implements 1 instance of the `power-switch` capability |


## Multiple capabilities of the same type

This also works similar to multiple device profiles. Assume a wall-mounted touch-panel used to control lights. It is a single panel, but has 4 touch areas to control 4 devices. (this example uses 4 switches in 1 capability, where it could also have been 4 devices with each 1 switch obviously)

It announces its capabilities like this:
```
homie/device-id/switch-1/$profile/wall-switch/1/2 → "4"
```

It would have these Homie properties, with example values;
```
homie/device-id/switch-1/button-1 → "double-click"
homie/device-id/switch-1/button-2 → "click"
homie/device-id/switch-1/button-3 → "hold"
homie/device-id/switch-1/button-4 → "release"
```

## Querying for capability types

If we change the examples to a device profile called `light`, that has;

- a `power-switch` capability called `power` (required)
- an optional `dimmer` capability called `brightness` 

And in this setting a controller wishes to find all dimmable lights, then it would;

1. subscribe to `homie/+/$profile/light/1/+`, this will return all Homie devices implementing a `light` of version `1.x`.
2. subscribe to `homie/+/+/$profile/dimmer/1/+`, this will return all capabilities implementing a `dimmer` of version `1.x`.
3. The cross-section of the 2 sets returns will be the dimmable lights. And equally so the first set (lights), with the 2nd set (dimmers) substracted will be the set of non-dimmable lights.

