# Capability: Switch

This capability description describes a generic switch node, and derived specific switches.

## Switch introduction

A switch is an actor that can switch between 2 states, indicated by a boolean value.

Specifically typed switches are available for the most common ones. Each with their own profile name and version. The Node layout is the same for all, they only differ by supported `format` labels.

### Examples

A Homie device with 1 node as a valve.

#### Minimal example

This minimal example only implements the required properties.

Description:

```json
{
  "homie": "5.0",
  "name": "Lawn water valve",
  "version": 3,
  "nodes": {

    "lawn-valve": {
      "$profile": ["homie-valve/1/0"],
      "name": "Lawn valve",
      "value": {
        "settable": true,
        "retained": true,
        "type": "boolean",
        "format": "close,open"
      }
    }

  }
}
```

Topics:

```
homie/5/lawn-water/$state            → "ready"
homie/5/lawn-water/$description      → "{... the above json doc ...}"
homie/5/lawn-water/lawn-valve/$profile/homie-valve/1 → "0"
homie/5/lawn-water/lawn-valve/value  → "false"
```

#### Full example

This example implements a switch including all optional properties.

Description:

```json
{
  "homie": "5.0",
  "name": "Lawn water valve",
  "version": 7,
  "nodes": {

    "lawn-valve": {
      "$profile": ["homie-valve/1/0"],
      "name": "Lawn valve",
      "value": {
        "settable": true,
        "retained": true,
        "type": "boolean",
        "format": "close,open"
      },
      "switch-time": {
        "settable": true,
        "retained": true,
        "type": "float",
        "format": "0:",
        "unit": "s"
      },
      "enable-time": {
        "settable": true,
        "retained": true,
        "type": "float",
        "format": "0:",
        "unit": "s"
      },
      "disable-time": {
        "settable": true,
        "retained": true,
        "type": "float",
        "format": "0:",
        "unit": "s"
      },
      "auto-disable": {
        "settable": true,
        "retained": true,
        "type": "float",
        "format": "0:",
        "unit": "s"
      },
      "auto-enable": {
        "settable": true,
        "retained": true,
        "type": "float",
        "format": "0:",
        "unit": "s"
      }

    }
  }
}
```

Topics:
```
homie/5/lawn-water/$state                   → "ready"
homie/5/lawn-water/$description             → "{... the above json doc ...}"
homie/5/lawn-water/lawn-valve/$profile/homie-valve/1 → "0"
homie/5/lawn-water/lawn-valve/value         → "false"
homie/5/lawn-water/lawn-valve/switch-time   → "180"
homie/5/lawn-water/lawn-valve/enable-time   → "0"
homie/5/lawn-water/lawn-valve/disable-time  → "200"
homie/5/lawn-water/lawn-valve/auto-disable  → "3600"
homie/5/lawn-water/lawn-valve/auto-enable   → "0"
```

## homie-switch/1/0

This is the common version if no specific profile is available. The profile name is: `homie-switch/1/0`.


### Required properties

#### value

The `value` property (required) is the property of type `boolean` that holds the switch status and allows to update it

attributes | value | remark
-|-|-
property-id | "`value`" |
settable | `true` |
retained | `true` |
format |  | the `format` attribute SHOULD be specified to add labels for the `false` and `true` values.
type | "`boolean`" |


### Optional properties

#### switch-time

The `switch-time` property (optional) determines the time required for a switch to completely switch state. For most
electrical switches this value is `0`, indicating it switches instantaneously. For a valve however this will
not be the case. A motorized valve for water or gas for example typically has a run time of several minutes to
completely open.

If this is set then a `set` command to change the state will delay updating the actual value by this time. This
behaviour can be made more specific by the optional `enable-time` and `disable-time` properties. If they are
not present, then the `switch-time` will be used for both `enable-time` and `disable-time`.

Consider an underfloor water based heating system with thermal valves. The valves will
take 3 minutes to completely open. If a heat request is send to the boiler immediately
after switching the value to `true`, the pump will go into an error state because
it fails to pump the water through the system as the valve is only just starting to open up.

By setting this value to 180 seconds, the time needed to open the valve completely, this can be prevented.
After setting the `value` property to `true`, the device will wait 180 seconds before it will actually
set the value of the `value` property to `true`. (the `target` attribute will be updated immediately,
if present, to indicate the state change is in progress).

Because the switch might change state again before reaching the end-state, the device will need to
keep track of time spend enabling/disabling to detect where it exactly is.

If the `enable-time` property is available, then the delay between receiving the `set=true` command and
reporting the `true` state will be the time specified in `enable-time`. If the device was not entirely in the
`disabled` state, then the current state should be taken into account.

If the `disable-time` property is available, then the delay between receiving the `set=false` command and
reporting the `false` state will be the time specified in `disable-time`. If the device was not entirely in the
`enabled` state, then the current state should be taken into account.

**Note:**: This property becomes REQUIRED when any of the following properties are
present; [`enable-time`](#enable-time) or [`disable-time`](#disable-time).

attributes | value | remark
-|-|-
property-id | "`switch-time`" |
settable | `true` |
retained | `true` |
type | "`float`" |
format | "`0:`" | Value must be 0 or more
unit | `"s"` | The time is provided in seconds

Example: *underfloor heating valve*

property | setting | remarks
---------|---------|----
switch-time|`180`| 3 minutes to reach the end state
enable-time|`60` | after 1 minute the valve has opened enough for the pump to start
disable-time|`0` | right after starting to close the valve, the pump and boiler are instructed to stop

Example: *garden watering valve*

property | setting | remarks
---------|---------|----
switch-time|`120` | 2 minutes to reach the end state
enable-time|`0`| immediate. Right after the valve starts moving, the first drops of water will flow and a user would consider the valve to be open.
disable-time|`120`| the valve must be completely closed for the last drops to stop

Example: time tracking

Considering these settings (underfloor heating example above):

property | setting
---------|--------
switch-time|`180`
enable-time|`60`
disable-time|`0`

This is the timeline and reported state and target values

time (s) | event | target | value | remark
---------|-------|--------|------|---
0 | start | `false` | `false` | initial state
0 | `set=true` | `true` | `false` | command received, new target set, valve motor runs to open
60 | `enable-time` elapsed | `true` | `true` | reported state now switched to `true`, valve motor still runs to open
120 | `set=false` | `false` | `true` | command received, new target set, valve motor runs to close
120 | `disable-time` elapsed | `false` | `false` | reported state now switched to `false`
150 | `set=true` | `true` | `false` | command received, new target set, valve motor runs to open
150 | `enable-time` elapsed | `true` | `true` | ran open for `120 s`, then close for `30 s`, so nett state is `90 s` open, so we're past the `enable-time`.



#### enable-time

The "`enable-time`" property (optional) is the time required for the switch to be
considered enabled after it is set to `true`. For details and examples see the [`switch-time` property](#switch-time).

**Note**: this value can be larger than the configured `switch-time`.

attributes | value | remark
-|-|-
property-id | "`enable-time`" |
settable | `true` |
retained | `true` |
type | `float` |
format | "`0:`" | Value must be 0 or more
unit | `"s"` | The time is provided in seconds


#### disable-time

The "`disable-time`" property (optional) is the time required for the switch to be
considered disabled after it is set to `false`. For details and examples see the [`switch-time` property](#switch-time).

**Note**: this value can be larger than the configured `switch-time`.

attributes | value | remark
-|-|-
property-id | "`disable-time`" |
settable | `true` |
retained | `true` |
type | `float` |
format | "`0:`" | Value must be 0 or more
unit | `"s"` | The time is provided in seconds


#### auto-disable

The "`auto-disable`" property (optional) is the time after which the switch automatically disables itself again after it reaches (and reports) the `true` state.

Setting `auto-disable=0` will disable the auto-disable behaviour.

Behaviour:

- the time starts after the `true` state has been reported on the `value` property. This means that the `enable-time` has past.
- if the `value` property receives a `set=true` command while it still has state `true`, and the `auto-disable` delay hasn't expired yet, the `auto-disable` delay is reset to start over.
- if the `value` property changes state to `false` before the `auto-disable` delay expires (for example due to a command received), the auto-disable action is cancelled
- when the `auto-disable` delay expires, the behaviour is as if a command `set=false` has been received on the `value` property. The `target` attribute becomes `false`, and after the `disable-time` has passed the actual value of the `value` property switches to `false`, etc.


attributes | value | remark
-|-|-
property-id | "`auto-disable`" |
settable | `true` |
retained | `true` |
type | `float` |
format | "`0:`" | Value must be 0 or more. If `0` the auto-disable behaviour is disabled.
unit | `"s"` | The time is provided in seconds


#### auto-enable

The "`auto-enable`" property (optional) is the time after which the switch automatically enables itself again after it reaches (and reports) the `false` state.

Setting `auto-enable=0` will disable the auto-enable behaviour.

Behaviour:

- the time starts after the `false` state has been reported on the `value` property. This means that the `disable-time` has past.
- if the `value` property receives a `set=false` command while it still has state `false`, and the `auto-enable` delay hasn't expired yet, the `auto-enable` delay is reset to start over.
- if the `value` property changes state to `true` before the `auto-enable` delay expires (for example due to a command received), the auto-enable action is cancelled
- when the `auto-enable` delay expires, the behaviour is as if a command `set=true` has been received on the `value` property. The `target` attribute becomes `true`, and after the `enable-time` has passed the actual value of the `value` property switches to `true`, etc.


attributes | value | remark
-|-|-
property-id | "`auto-enable`" |
settable | `true` |
retained | `true` |
type | `float` |
format | "`0:`" | Value must be 0 or more. If `0` the auto-enable behaviour is disabled.
unit | `"s"` | The time is provided in seconds

### Actions

A device implementing this profile can use the following actions (see also [homie-action/1/0](cap-action.md#homie-action10)):

action | behaviour
-|-
`toggle` | Will toggle the state of the [`value` property](#value).


## homie-power-switch/1/0

The profile name is: `homie-power-switch/1/0`

The implementation is identical to the [`homie-switch/1/0`](#homie-switch10) capability with the
following modifications:

- the `format` attribute of the `value` property MUST be `"off,on"`.


## homie-valve/1/0

The profile name is: `homie-valve/1/0`

The implementation is identical to the [`homie-switch/1/0`](#homie-switch10) capability with the
following modifications:

- the `format` attribute of the `value` property MUST be `"closed,open"`.
