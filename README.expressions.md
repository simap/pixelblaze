# Table of Contents

1. [Writing Patterns](#toc_1)
2. [Watching Variables](#toc_2)
3. [User Interface Controls](#toc_3)
2. [Supported Language Features](#toc_6)
3. [Language limitations](#toc_7)
4. [Variables](#toc_8)
5. [Constants](#toc_9)
6. [Functions](#toc_10)
7. [Expansion Board](#toc_63)

# Writing Patterns

Enter code above, and everything you type is compiled on the fly. If your program is valid, it is sent down to Pixelblaze, and you'll see your changes instantly! Look for syntax or run-time errors in the left sidebar and the status area just below the editor.

Pixelblaze looks for a few exported functions that it can call to generate pixels.

The exported `render(index)` function is called for each pixel in the strip. The `index` argument tells you which pixel is being rendered. Use the `hsv` or `rgb` function to set the current pixel's color.

Alternative render functions `render2D(index, x, y)` and `render3D(index, x, y, z)` can also be specified to use when a 2D or 3D pixel map is available. Multiple alternatives can be in the same program and the appropriate one will be selected automatically. See the Mapper tab for more info.

The exported `beforeRender(delta)` function is called before it is going to render a new frame of pixels to the strip. The `delta` argument is the number of elapsed milliseconds (with a resolution of 6.25ns!) since the last time `beforeRender` was called. You can use `delta` to create animations that run at the same speed regardless of the frame rate.

Pixelblaze's language is based off of JavaScript (ES6) syntax, but with a subset of the language features available. All numbers in Pixelblaze are a 16.16 fixed-point numbers. This can handle values between -32,768 to +32,768 with fractional accuracy down to 1/65,536ths.

A global called `pixelCount` is defined based on how many pixels you've configured in settings. You can use this in initialization code or in any function.

# Watching Variables

Next to the editor is a **Var Watcher** which will display the value of any variable that has been exported. It works for arrays as well, and will show each element with the corresponding index.

Data from the sensor expansion board can be watched as well. Use this to explore data from ADC inputs, accelerometer, light sensor, or all of the sound analysis data.

The data is only sampled at the end of rendering. If you export a global variable that is modified inside `render()` normally only the last value will be shown. If you want to inspect a bit of data only for a particular pixel index, you can conditionally set that variable. e.g.: `if (index == 42) myExportedVar = someInterestingData`

# User Interface Controls

Sliders and color pickers can be created that will show up when a pattern is active. This can be used to change how the pattern behaves without editing the code. These are persistent and their settings will be preserved across restarts or pattern switches.

### Sliders

To create a slider, export a function that starts with the key word `slider` followed by the name of the slider. CamelCase or snake_case can be used to separate words. For example, to create a slider called "My Slider":

`export function sliderMySlider(v) {...}`

Whenever the slider is moved, this function will be called with a new value between 0.0 and 1.0. It will also be called with a saved value when the pattern is switched to before rendering occurs.

To make use of this you may store the value or use it to calculate something used in your pattern. A common way to do this is to create a variable to hold the value and initialize it with a default value:

```
var mySetting = 0.5
export function sliderMySetting(v) {
	mySetting = v
}
```

### Color Pickers

To create a color picker, export a function that starts with the key words `hsvPicker` or `rgbPicker` followed by the name of the color picker. CamelCase or snake_case can be used to separate words. For example, to create a color picker called "Primary Color":

For HSV:

`export function hsvPickerPrimaryColor(h, s, v) {...}`

For RGB:

`export function rgbPickerPrimaryColor(r, g, b) {...}`

This creates a color well that when clicked will open a color picker. When it is changed, this function will be called with either the hue, saturation, and value for `hsvPicker` or the red, green, and blue for `rgbPicker`. All values are between 0.0 and 1.0, and suitable for passing to the `hsv()` or `rgb()` functions later on to set a pixel color.

To make use of this you may store the value or use it to calculate something used in your pattern.

# Supported Language Features

* All of the usual math operators work. Most work on 16.16 fixed-point math. The bit-wise operators work on the top 16 bits. `=`, `+`, `-`, `!`, `*`, `/`, `%`, `>>`, `<<`, `|`, `&`, `~`, `^`, `>`, `<`, `>=`, `<=`, `==`, `!=`, `||`, `&&`, `?`
* Logical operators work like JavaScript and carry over the value, not just a boolean. e.g. `v = 0 || 42` will result in 42.
* Trig and other math functions. `abs`, `floor`, `ceil`, `min`, `max`, `clamp`, `sin`, `cos`, `tan`, `asin`, `acos`, `atan`, `atan2`, `sqrt`, `exp`, `log`, `log2`, `pow`, `random`
* Declare global or function-local variables using `var` or globals implicitly.
* Use `if` and `else` to have some code run conditionally.
* Use `while` and `for` for making loops and use `break` and `continue` statements to escape a loop or loop early.
* Define functions using the `function` keyword or short lambda-style form.
  * `function myFunction(arg1) {return arg1 * 2}`
  * `myFunction = (arg1) => arg1 * 2`
* Functions can be stored in variables, passed as arguments, and returned from other functions.
* Create arrays using the `array(size)` function and access them with the bracket syntax. Arrays can be passed around just like any other type.

# Language limitations

This are language features you'd expect to work writing JavaScript that won't run on Pixelblaze.

* Objects, named properties, classes, etc.
* Garbage collection or freeing memory. Arrays are currently the only dynamically allocated memory, and you can't (yet) free them once created.
* Closures aren't supported, so any function defined in another function won't have access to parameters or local variables. It can still access globals or its own parameters.
* `switch` + `case` statements. You can use chained `else if` statements, or put functions in an array and use it as a lookup table.

```
modes[0] = () => {/* do mode 0 */};
modes[1] = () => {/* do mode 1 */};
// ...
modes[currentMode]();
```

* Narrow scoped variables using `let` or read-only variables with `const`
* Array literals

# Variables

The `pixelCount` variable is available as a global even during initialization. This is the number of LED pixels that have been configured in settings.

Variables can be created/assigned implicitly with the `=` operator. e.g.: `foo = wave(time(0.1))` or explicitly using the `var` keyword. e.g.: `var foo = 1`. 

Implicit variables are global. 

Explicitly declared variables will either be global or local depending on where they are declared. Local variables declared using `var` inside a function are visible inside that function. Local variables can shadow global variables with the same name.

Global variables can also be exported with the `export` keyword. These will be visible in the Var Watcher and can be used with the `getVars` and `setVars` [websocket API](https://www.bhencke.com/pixelblaze-advanced#Websocket-API).

# Constants

`E` , `PI`, `PI2` (PI * 2), `PI3_4` (PI * 3 / 4), `PISQ` (PI * PI), `LN2`, `LN10`, `LOG2E`, `LOG10E`, `SQRT1_2`, `SQRT2`

# Functions

### Math Functions

#### abs(`v`)
#### acos(`x`)
#### asin(`x`)
#### atan(`x`)
#### atan2(`y`, `x`)
#### ceil(`v`)
#### clamp(`value`, `low`, `hi`)
Clamps `value` such that it isn't less than `low` or greater than `high`
#### cos(`angleRads`)
#### exp(`v`)
#### floor(`v`)
#### log(`v`)
#### log2(`v`)
#### max(`v1`, `v2`)
#### min(`v1`, `v2`)
#### pow(`base`, `exponent`)
#### random(`max`)
A random number between 0.0 and `max` (exclusive)
#### sin(`angleRads`)
#### sqrt(`v`)
#### tan(`angleRads`)

### Waveform Functions

#### time(`interval`)

A sawtooth waveform between 0.0 and 1.0 that loops about every 65.536*`interval` seconds. e.g. use .015 for an approximately 1 second.

Patterns using this can be synchronized across the network using either [Firestorm](https://github.com/simap/Firestorm), or a when conecting to a Pixelblaze in AP mode.

#### wave(`v`)

Converts a sawtooth waveform `v` between 0.0 and 1.0 to a sinusoidal waveform between 0.0 to 1.0. Same as `(1+sin(v*PI2))/2` but faster. `v` "wraps" between 0.0 and 1.0.
#### square(`v`, `duty`)
Converts a sawtooth waveform `v` to a square wave using the provided `duty` cycle where `duty` is a number between 0.0 and 1.0. `v` "wraps" between 0.0 and 1.0.
#### triangle(`v`)
Converts a sawtooth waveform `v` between 0.0 and 1.0 to a triangle waveform between 0.0 to 1.0. `v` "wraps" between 0.0 and 1.0.

### Pixel / Color Functions

#### hsv(`hue`, `saturation`, `value`)

Sets the current pixel by calculating the RGB values based on the HSV color space. `Hue` "wraps" between 0.0 and 1.0. Negative values wrap backwards. For LEDs that support it, this uses 24-bit color plus an additional 5 bits of brightness control giving a high dynamic range especially at lower light levels and reduces posterization.

#### hsv24(`hue`, `saturation`, `value`)

Sets the current pixel by calculating the RGB values based on the HSV color space. `Hue` "wraps" between 0.0 and 1.0. Negative values wrap backwards. This uses 24-bit color only, even if the LEDs support additional resolution and may reduce flickering in some LEDs.

#### rgb(`red`, `green`, `blue`)

Sets the current pixel to the RGB value provided. Values range between 0.0 and 1.0.

### Input / Output Functions

#### readAdc()
Reads the value from the ADC as a number between 0.0 and 1.0.
***This function is only available on V2 devices***

#### analogRead(`pin`)
Reads the value from the pin as a number between 0.0 and 1.0.
***This function is only available on V3 devices***

#### pinMode(`pin`,`mode`)
Set the pin mode as an `INPUT`, `INPUT_PULLUP`, `INPUT_PULLDOWN`, `OUTPUT`, `OUTPUT_OPEN_DRAIN`, or `ANALOG`.

***V2 device notes:*** `INPUT_PULLUP` does not work for GP16, instead `INPUT_PULLDOWN_16` is supported. This can be used with a button, connect between GP16 and 3.3v. Pins will read `HIGH` or 1.0 while the button is pressed. On Pixelblaze V2+ pin GP12 is connected to the orange LED. GP2 is used for NeoPixel and WS2811/12/13 support and can't be used unless the trace on the bottom is cut.

#### digitalWrite(`pin`,`state`)
Set a pin `HIGH` or `LOW`. Any non-zero value will set the pin `HIGH`.

#### digitalRead(`pin`)
Read a pin state, returns 1.0 if the pin is `HIGH`, 0.0 for `LOW` otherwise.

#### touchRead(`pin`)
Detect touch and proximity on a pin using capacitive sensing techniques. Returns a value between 0.0 and 1.0 depending on how much capacitance is detected on the pin. 

***V3 device notes:*** You can also specify one of these constants: T0, T2, T4, T6, T7.

### Clock / Time Functions

When the discovery service is enabled and Pixelblaze is connected to the internet, it will know what time it is and these functions can be used.

#### clockYear()
#### clockMonth()
#### clockDay()
#### clockHour()
24 hour format. `13` = 1pm.
#### clockMinute()
#### clockSecond()
#### clockWeekday()
Sunday = 1, Monday = 2, etc.

### Clock / Time Functions

When the discovery service is enabled and Pixelblaze is connected to the internet, it will know what time it is and these functions can be used.

#### clockYear()
#### clockMonth()
#### clockDay()
#### clockHour()
24 hour format. `13` = 1pm.
#### clockMinute()
#### clockSecond()
#### clockWeekday()
Sunday = 1, Monday = 2, etc.

# Sensor Expansion Board

Pixelblaze supports a sensor expansion board that adds:

* A microphone and signal processing that gives:
	* `frequencyData` - 32 element array with frequency magnitude data ranging from 12.5-10khz
	* `energyAverage` - total audio volume
	* `maxFrequency` and `maxFrequencyMagnitude` - detects the strongest tones with resolution of about 39Hz
* A 3-axis 16G `accelerometer` - 3 element array with [x,y,z]
* An ambient light sensor - `light` can be used to automatically dim displays for nightlights
* 5 `analogInputs` - 5 element array with analog values from A0-A4

Each of these can be accessed in a pattern by using the `export var` syntax, with optional defaults if the board is not connected.

```
export var frequencyData
export var energyAverage
export var maxFrequencyMagnitude
export var maxFrequency
export var accelerometer
export var light
export var analogInputs

```