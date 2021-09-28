Pixelblaze Websockets API
=========================

Pixelblaze is controlled by a websocket connection on port 81. JSON and binary frames are sent to control and edit the patterns.

## Video Walkthrough

<iframe width="732" height="549" src="https://www.youtube.com/embed/CGz1-_K2PSw" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>


## Getting and Setting Variables

Pattern variables can be controlled over the network using websockets. 

**Pixelblaze V1** (Pre-2019): If your expression uses but does not initialize some variables, you can set these. They default to 0 if otherwise uninitialized. If you do set them to a value in the pattern, they are overwritten each time and setVars won't take effect.

**Pixelblaze V2 and V3 Standard/Pico**: You can export top-level variables to make them public and initialize them to default values. e.g.

```javascript
export var myVar = 2
export var var2 = 0
export var untouched = -1.1
```

To set variables in the currently-active pattern, send a JSON text frame like this:

```json
{
	"setVars": {
		"myVar": 3.14159,
		"var2": 42
	}
}
```

To get the exported variables in the currently-active pattern, send a frame like this:

```json
{
	"getVars": true
}
```

You'll get a response with all variables. Note that this will only show variable state after the last pixel of the current frame has been `render()`ed.

```json
{
	vars: {
		"myVar": 3.14159, 
		"var2": 42,
		"untouched": -1.1,
		...
	}
}
```

## Listing Patterns

To list all patterns on a Pixelblaze board, send: 

```json
{
	"listPrograms": true
}
```

### A Note on Pattern IDs

Pattern IDs uniquely identify a pattern within a Pixelblaze (and are typically, but not guaranteed to be, unique across many different baords like a UUID). An example of a pattern id is `7MuJmcy4FZbs9jGbB`.

Pattern IDs can be retrieved from the pattern list API response, or you can list them in a browser inspector console by inspecting the global variable `programList` within any Pixelblaze's SPA IDE. 

Identical .epe pattern files imported into separate Pixelblaze boards will have different random pattern IDs assigned. After initial creation or import, pattern IDs remain consistent within a single board across pattern edits.


### V1 Pattern List Response

The pattern list returned contains the name and id, along with some other fields about storage space in memory.

```json
{
	"programList": [
		{
			"name": "my pattern",
			"id": "x1234567890",
			"sourceSize": 123,
			"globalSourceSize": 123,
			"compiledSize": 134,
			"imgSize": 5123
		}, ...
	]
}
```

### V2 & V3 Pattern List Response

In Pixelblaze version 2, the pattern list changed a bit. It now uses a binary frame protocol for efficiency and to handle potentially hundreds of patterns. The list is split up into multiple binary frames, to keep response frames somewhat small. Each frame has a 2 byte header that indicates the frame type, and a start/end flag field. The rest of the frame is a tab separated value list of ids and names. Clients reading this format should concatenate response frames until the end frame flag is set (0x04).

| Position | Value | Description  |
| -------- | ----- | ----------|
| 0        | `0x07` | program list |
| 1        | `0x01 / 0x04` | start/end frame flags (or, will be 0x05 if there is a single frame) |
| 2..n     | `<id1> + "\t" + <name1> + "\n"`<br>`<id2> + "\t" + <name2> + "\n"`<br>`...` | Tab separated list of id + names, one per line until the frame ends. |

## Changing The Active Pattern

With a pattern ID handy, you can activate any previously stored pattern. This change is saved and will persist if the device restarts.

```json
{
	"activeProgramId": "<id>"
}
```

## Controlling Brightness

The brightness can also be changed by sending a new value from 0.0 to 1.0. This is the same as the brightness slider, but will not be saved between restarts.

```json
{
	"brightness": 0.5
}
```

## User Interface Controls

In addition to setting variables directly, Pixelblaze supports the idea of user interface controls defined within pattern code. A UI control's handler function is called on pattern activation and upon any interaction with the UI contol. Currently sliders and color pickers are supported. These can be interacted with over the websocket connection as well. These can be optionally persisted, unlike exported variables, such that their values will be restored the next time the pattern is activated.

To get a list of controls and their current settings, you need the ID of the pattern. Control values can be retrieved for any pattern, not just the active pattern.

```json
{
	"getControls": "<id>"
}
```

The response will contain and object keyed to the pattern id, with each control name and their current value(s).

```json
{
	"controls":{
		"qzpL3CLaMP3cqae2R":{
			"hsvPickerMyColor":[0.77,0.84,1],
			"sliderMySlider":0.4431
		}
	}
}
```

Setting control values applies to the active pattern. Sliders take a value between 0.0 and 1.0, while HSV and RGB color pickers take an array with 3 elements, also in the range of 0.0 to 1.0.

```json
{
	"setControls":{
		"hsvPickerMyColor":[0.77,0.84,1],
		"sliderMySlider":0.4431
	}
}
```

By default, control values are not persisted. This is done to avoid wearing out the flash memory. If you do want to persist the values, wait for a period of time after the last user interaction, and add the save: true attribute.

```json
{
	"setControls":{
		"hsvPickerMyColor":[0.77,0.84,1],
		"sliderMySlider":0.4431
	},
	"save": true
}
```
