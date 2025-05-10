# Device: Shutter

This device description describes a shutter for controlling roller-shutters, blinds, or screens.

## Shutter introduction

A shutter is a device that controls the covering of a windoow or door. It can be used for security, to block light, or provide privacy.

## Profile name

The profile for this device is `homie-shutter/1/0`. It is located in the device description JSON document in the device level `$profile` attribute, and the `$profile` topic.

## Examples

Description:

```json
{
  "homie": "5.0",
  "name": "Windows blinds",
  "version": 3,
  "$profile": ["homie-shutter/1/0"],
  "nodes": {

    "covered": {
      "$profile": ["homie-level/1/0"],
      ...
    }

  }
}
```

Topics:

```
homie/5/my-blinds/$profile/homie-level/1 → "0"
```

## Nodes

This device-profile implements the following capability-profiles:

Node-ID | Capability-Profile | required | additional<br/>requirements
-|-|-|-
covered | [homie-level/1/0](cap-level.md#homie-level10) | yes | yes

Additional nodes MAY be added to the device.

### covered (required)

The `covered` node MUST conform to the following additional requirements:

- The `unit` attribute of the `value` property MUST be set to `%` (percent).
- The `format` attribute of the `value` property MUST be set to a minimum of `0` and a maximum of `100`.
- A value of `100` represents the window/opening being fully covered.
