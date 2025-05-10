# Device: Power Switch

This device description describes a power-switch.

## Switch introduction

A power-switch resembles a switch to power a device on/off.

## Profile name

The profile for this device is `homie-power-switch/1/0`. It is located in the device description JSON document in the device level `$profile` attribute, and the `$profile` topic.

## Examples

Description:

```json
{
  "homie": "5.0",
  "name": "Water pump",
  "version": 3,
  "$profile": ["homie-power-switch/1/0"],
  "nodes": {

    "power": {
      "$profile": ["homie-power-switch/1/0"],
      ...
    }

  }
}
```

Topics:

```
homie/5/water-pump/$profile/homie-power-switch/1 → "0"
```

## Nodes

This device-profile implements the following capability-profiles:

Node-ID | Capability-Profile | required | additional<br/>requirements
-|-|-|-
power | [homie-power-switch/1/0](cap-switch.md#homie-power-switch10) | yes | no
action | [homie-action/1/0](cap-action.md#homie-action10) | no | yes

Additional nodes MAY be added to the device.

### power (required)

This node has no additional requirements.

### action (optional)

The action node (optional) implements the following actions:

action | optional | description
-|-|-
toggle | yes | maps to the `power` node, implementing the `toggle` action of the `homie-power-switch` capability.
