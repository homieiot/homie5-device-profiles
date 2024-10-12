# Capability: Action

This capability description describes a generic action node.

## Action introduction

An action is something that happens **relative** to the state of a device.

What does that mean? Most of the homie convention is based on "state" that lives as 'retained' values on the MQTT bus. Interaction is done by modifying that state. For example a dimmer;

- pass `30` to the `set` attribute of the property that holds the curent state. This will set the **absolute** value of that dimmer to `30%`.
- pass `100` to the `set` attribute of the property that holds the curent state. This will set the **absolute** value of that dimmer to `100%`.

However, not all interactions can be modeled that way. For example if we want to 'step-up' or 'step-down' the dimmer value. In either case the new state value will be relative to the previous state. For cases like this the `action` capability can be used.

**Caution**: the preferred way with Homie devices is to use state-based controls. Only use actions if there is no way to do it state-based. For example don't used actions `on`, `off`, and `toggle`, but model `on`/`off` as state, and only `toggle` as an action.

### Explicit vs embedded

The actions may be scoped on Node level or Device level. Meaning:

- **Node level:** An action impacts only a specific node.
- **Device level**: An action impacts multiple Nodes within a device.

This action capability is specified and advertized if used on a Device explicitly, as a separate Node.
If the action is used on a Node level, it can be embedded in other capabilities. See the [`homie-switch/1/0` capability](cap-switch.md#action) for exmaple.


### Examples

A Homie device with 1 node as an action.

#### Example

This minimal example only implements the required properties.

Description:

```json
{
  "homie": "5.0",
  "name": "dimmable light",
  "version": 3,
  "nodes": {

    "actions": {
      "$profile": ["homie-action/1/0"],
      "name": "Light actions",
      "action": {
        "settable": true,
        "retained": false,
        "type": "enum",
        "format": "dim,brigten,toggle"
      }
    }

  }
}
```

Topics:

```
homie/5/my-light/$state            → "ready"
homie/5/my-light/$description      → "{... the above json doc ...}"
homie/5/my-light/actions/$profile/homie-action/1 → "0"
```


## homie-action/1/0

The profile name is: `homie-action/1/0`.


### Required properties

#### action

The `acton` property (required) is the property of type `enum` that accepts commands.

Actions that interact with other properties of a device SHOULD operate on `$target` attributes of those properties if present.

attributes | value | remark
-|-|-
property-id | "`action`" |
settable | `true` |
retained | `false` |
format | "`some-action`" | at least 1 action MUST be present, more MAY be added.
type | "`enum`" |



### Optional properties

none
