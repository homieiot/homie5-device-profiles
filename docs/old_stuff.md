# older stuff to review, move, or delete


## Device descriptions

### Light
`type`: "homie-device-profile/v1/type=light"

This device represents binary and dimmable lights with no color.

#### Device capabilities

|id|type|optional|comment
|-|-|-|-|
|switch|`homie-capability-profile/v1/type=switch`|no|n.a.
|dimmer|`homie-capability-profile/v1/type=dimmer`|yes|n.a

The `id` is the required name on the Homie "Node" level

#### Inter-capability behaviour

The switch has no effect on the dimmer state, nor the dimmer state on the switch. Examples:

|state|action|result|remark|
|-|-|-|-|
|`switch.state=true, dimmer.brightness=50`|set `switch.state=false`|`switch.state=false, dimmer.brightness=50`|brightness is unchanged
|`switch.state=false, dimmer.brightness=50`|set `dimmer.brightness=60`|`switch.state=false, dimmer.brightness=60`|light remains off


## Capability descriptions

### Switch
`type`: "homie-capability-profile/v1/type=switch"

*Example usages*: Represent a switchable actor, e.g. wall socket plug, light bulb.

#### Properties

|id|type|settable (default)|retained|unit|format|comment
|-|-|-|-|-|-|-|
|state|`boolean`|yes|yes|-|off,on|format specifies labels for false/true
|action|`enum`|yes|no|-|`toggle`| toggles state between true and false


#### Comments:
Since a boolean does not carry any context, the format property specifies the labels for the boolean values.
The format is the same as an `enum` where `false` is the first and `true` is the second entry.
Reasoning is that if the `enum` is an `array`, and `false` is often represented as 0 and `true` often as 1. Then those 0/1 values are the indices of the format `array` to ease implementations.

This format entry requires a change to the Homie protocol

The value could also be specified as an `enum` all together, but having a `boolean` provides a hint to a UI as to how to render the property (see the "goals" section").

### Dimmer
`type`: "homie-capability-profile/v1/type=dimmer"

*Example usages*: Dimmable light.

#### Properties

|id|type|settable (default)|retained|unit|format|comment
|-|-|-|-|-|-|-|
|brightness|`integer`|yes|yes|%|`1:100`|0 is not allowed since it would be ambiguous
|action|`enum`|yes|no|-|`brighter,darker`|

#### Comments:
`brightness` is restricted from 1 to 100%, 0 would be ambiguous. It is important to note that this is the STATE representation, not the UI representation. A UI is still free in how it renders the state; a single slider 0-100, a switch and 1-100, or a switch and 0-100.

The brighter and darker actions are problematic in so far as that we would need to define the delta adjustment value. But depending on the devices practical values could be difficult to align.
An argument could be made to completly remove this feature all together but it has proved to be rather practical in home automation scenarios.
Another way could be a 'delta' property that simply receives +- values for adjustments.

### Colorlight
`type`: "homie-capability-profile/v1/type=colorlight"

*Example usages*: Color control for a lightbulb or LED strip.

#### Properties

|id|type|settable (default)|retained|unit|format|comment
|-|-|-|-|-|-|-|
|color|`color`|yes|yes|-|`rgb/hsv`|
|color-temperature|`integer`|yes|yes|Mired|`min:max`| fomat specifies min max values supported by the device
