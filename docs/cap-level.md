# Capability: Level-actor

This capability description describes a generic level node, and derived specific levels.

## Level introduction

A level actor is an actor that can change levels indicated by a numeric value. A typical example is the brightness-level of a dimmable light.

### Examples

A Homie device with 1 node as a brightness control.

#### Minimal example

This minimal example only implements the required properties.

Description:

```json
{
  "homie": "5.0",
  "name": "Kitchen light",
  "version": 3,
  "nodes": {

    "table-top-kitchen": {
      "$profile": ["homie-level/1/0"],
      "name": "brightness",
      "value": {
        "settable": true,
        "retained": true,
        "type": "float",
        "format": "0:100"
      }
    }

  }
}
```

Topics:

```
homie/5/kitchen-light/$state                    → "ready"
homie/5/kitchen-light/$description              → "{... the above json doc ...}"
homie/5/kitchen-light/table-top-kitchen/$profile/homie-level/1 → "0"
homie/5/kitchen-light/table-top-kitchen/value   → "70"
```

#### Full example

This example implements a level actor including all optional properties.

Description:

```json
{
  "homie": "5.0",
  "name": "Kitchen light",
  "version": 3,
  "nodes": {

    "table-top-kitchen": {
      "$profile": ["homie-level/1/0"],
      "name": "brightness",
      "value": {
        "settable": true,
        "retained": true,
        "type": "float",
        "format": "0:100:1",
        "unit": "%"
      },
      "step-size": {
        "settable": true,
        "retained": true,
        "type": "float",
        "format": "1:100:1"
      },
      "transition-time": {
        "settable": true,
        "retained": true,
        "type": "float",
        "format": "0:",
        "unit": "s"
      },
      "minimum": {
        "settable": true,
        "retained": true,
        "type": "float",
        "format": "0:100:1",
        "unit": "%"
      },
      "maximum": {
        "settable": true,
        "retained": true,
        "type": "float",
        "format": "0:100:1",
        "unit": "%"
      }
    }

  }
}
```

Topics:
```
homie/5/kitchen-light/$state                            → "ready"
homie/5/kitchen-light/$description                      → "{... the above json doc ...}"
homie/5/kitchen-light/table-top-kitchen/$profile/homie-level/1 → "0"
homie/5/kitchen-light/table-top-kitchen/value           → "70"
homie/5/kitchen-light/table-top-kitchen/step-size       → "10"
homie/5/kitchen-light/table-top-kitchen/transition-time → "30"
homie/5/kitchen-light/table-top-kitchen/minimum         → "0"
homie/5/kitchen-light/table-top-kitchen/maximum         → "100"
```

## homie-level/1/0

The profile name is: `homie-level/1/0`.


### Required properties

#### value

The `value` property (required) is the property of type `float` that holds the level value and allows to update it

attributes | value | remark
-|-|-
property-id | "`value`" |
settable | `true` |
retained | `true` |
format | | the `format` attribute MUST set minimum and maximum, precision MAY be set.
type | "`float`" |
unit | | the unit attribute MAY be specified. It is recommended to use a unit, but not required.


### Optional properties

#### step-size

The `step-size` property (optional) represents the step size for the level-actor to change upon the [`step-up` and `step-down` actions](#actions).

attributes | value | remark
-|-|-
property-id | "`step-size`" |
settable | `true` |
retained | `true` |
format | | the `format` attribute MUST be specified. See details below.
type | "`float`" |
unit | | the unit attribute MUST be the same as the `value` property.

The `format` attribute:

- minimum value MUST be greater-than 0.
- if the [`value` property](#value) has a precision set in its format, then the `step-size` minimum MUST be greater-than or equal-to the precision of the [`value` property](#value).


#### transition-time

The `transition-time` property (optional) represents the time for the level-actor to change from minimum to maximum value. The time specified is for traversing the full range. Smaller changes should use relative times.

This property is used if the change in level is controllable by the device. An example is a brightness setting for a dimmable light, where it can be set to dim slowly over 30 seconds. An example where it doesn't work would be a roller-shutter; these typically do not control the speed of opening/closing.


attributes | value | remark
-|-|-
property-id | "`transition-time`" |
settable | `true` |
retained | `true` |
type | "`float`" |
format | "`0:`" | Value must be 0 or more
unit | `"s"` | The time is provided in seconds


#### minimum

The minimum value to be set.

**Note**: if minimum is provided, then the [`maximum` property](#maximum) becomes required, and MUST be provide as well.

The value specified by the [`value` property](#value) should be mapped to the range from `minimum` to [`maximum`](#maximum). The result should be used in the internal state. Maximum can be less than minimum, mathematically it stays the same, just the result becomes "inverted".

Example:

- value ranges from 1-100%,
- minimum is set to 15,
- maximum is set to 70,
- value is set to 90%

Value 90 within range 1-100, is mapped to the range 15-70. The result is 64.44%.

attributes | value | remark
-|-|-
property-id | "`minimum`" |
settable | `true` |
retained | `true` |
type | "`float`" |
format | | the minimum and maximum MUST be specified in the `format` attribute. It MUST be the same as for the `maximum` property.
unit | | Optional. It does not have to be the same unit as the `value` property, but MUST be the same as the `maximum` property.

#### maximum

The maximum value to be set.

**Note**: if maximum is provided, then the [`minimum` property](#minimum) becomes required, and MUST be provide as well.

The value specified by the [`value` property](#value) should be mapped to the range from [`minimum`](#minimum) to `maximum`. The result should be used in the internal state. Maximum can be less than minimum, mathematically it stays the same, but the result becomes "inverted".

attributes | value | remark
-|-|-
property-id | "`maximum`" |
settable | `true` |
retained | `true` |
type | "`float`" |
format | | the minimum and maximum MUST be specified in the `format` attribute. It MUST be the same as for the `minimum` property.
unit | | Optional. It does not have to be the same unit as the `value` property, but MUST be the same as the `minimum` property.

### Actions

A device implementing this profile can use the following actions (see also [homie-action/1/0](cap-action.md#homie-action10)):

action | behaviour
-|-
`step-up` | Will step up the state of the [`value` property](#value) by [`step-size`](#step-size), but never beyond its maximum.
`step-down` | Will step down the state of the [`value` property](#value) by [`step-size`](#step-size), but never below its minimum.

The behaviour of the step-up/down actions should be as if the new value is posted on the `set` attribute of the [`value` property](#value).

If the optional `step-size` property is not specified, then the `step-up` and `step-down` actions may use an implementation defined step-size.
