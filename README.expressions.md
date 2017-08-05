	
Writing Expressions
========================

Each pixel is run through the given expression. With a bit of math, interesting generated patterns can be created.

The expression parser follows C precedence conventions. Expressions are parsed, compiled, and run on the fly as you type. Use either `hsv` or `rgb` functions to set a pixel.

The expression engine runs on 16.16 fixed-point math. This can work on numbers between -32,768 to +32,768 with fractional accuracy down to 1/65,536ths.

The usual math functions are available, as well as a handful of constants.

Expressions can span multiple lines, and you can create variables to store intermediate calculations or to make your expression more readable.

Evaluation is done depth first, there isn't support for conditional evaluation, though there is support for conditional value. Normally this isn't a problem, but will cause unexpected results if you rely on `&&`, `||` or `? :` to perform an action with side effects.

# Variables
Some variables are provided for you:

* i = The current pixel index (starting at 0) **Only available in the pixel expression**
* l = The length of the pixel strip.
* t = Raw millis()/65536. **WARNING** this easily saturates for usable expressions, use `time()` instead.

User variables can be created/assigned with the `=` operator. e.g.: `foo = sin(time(0.1,PI2))`
Precedence for the assignment operator is kinda broken right now, you may need to wrap the right side value in parenthesis if things aren't working as intended.

Variables created in the global expression are available in the pixel expression. This can speed up refresh rates because they are evaluated only once per refresh cycle.

# Constants

`E` , `PI`, `PI2`, `PI3_4`, `PISQ`, `LN2`, `LN10`, `LOG2E`, `LOG10E`, `SQRT1_2`, `SQRT2`

# Operators

`=`, `+`, `-`, `!`, `*`, `/`, `%`, `>`, `<`, `>=`, `<=`, `==`, `!=`, `||`, `&&`, `?`

*NOTE* test operators result in either 0.0 or 1.0. Any non 0.0 value is considered "truthy" for boolean logic.

# Functions

`abs`, `acos`, `asin`, `atan2`, `atan`, `ceil`, `clamp`, `cos`, `deg_to_rad`, `exp`, `floor`, `hsv`, `lerp`, `log2`, `log`, `max`, `min`, `pow`, `rad_to_deg`, `random`, `rgb`, `sadd`, `sdiv`, `sin_parabola`, `sin`, `slog2`, `smul`, `sq`, `sqrt`, `square`, `ssub`, `tan`, `time`, `triangle`, `wave`
  
### hsv(`hue`, `saturation`, `value`)
Sets the current pixel by calculating the RGB values based on the HSV color space. `Hue` "wraps" between 0.0 and 1.0. Nevative values wrap backwards. 

e.g. 0.9, 1.9, and -0.1 are the same color. `Saturation` and `value` are clampped (saturate, heh) between 0.0 and 1.0.

### rgb(`red`, `green`, `blue`)
Sets the current pixel using RGB values.
### clamp (`value`, `low`, `hi`)
### sadd, ssub, smul, sdiv
These functions provide saturating arithmetic and won't overflow.
### lerp(`a`, `b`, `frac`)
Linear interpolation: (`a` * (1 - `frac`)) + (`b` * `frac`)

`frac` is clamped to 0.0 to 1.0
### random()
A random number between 0.0 and 1.0 (exclusive)
### random(`max`)
A random number between 0.0 and `max` (exclusive)
### random(`min`, `max`)
A random number between `min` (inclusive) and `max` (exclusive)
### time()
A value between 0.0 and 1.0 that loops about every 32.768 seconds. (Sawtooth waveform)
### time(`base`)
A value between 0.0 and 1.0 that loops about every 65.536*`base` seconds. e.g. use .015 for an aproximately 1 second.
### time(`base`, `magnitude`)
Same as time(base) * `magnitude`.
### square(`v`)
Converts a sawtooth waveform `v` between 0.0 and 1.0 to a square wave between 0.0 and 1.0 at 50% duty cycle.
### square(`v`, `duty`)
Converts a sawtooth waveform `v` to a square wave using the provided `duty` cycle where `duty` is a number between 0.0 and 1.0.
### square(`v`, `duty`, `magnitude`)
Same as square(`v`, `duty`) * `magnitude`
### triangle(`v`)
Converts a sawtooth waveform `v` between 0.0 and 1.0 to a triangle waveform between 0.0 to 1.0.
### triangle(`v`, `magnitude`)
Same as triange(`v`) * `magnitude`
### wave(`v`)
Converts a sawtooth waveform `v` between 0.0 and 1.0 to a sinusoidal waveform between 0.0 to 1.0. Same as `(1+sin(v*PI2))/2` but faster. `v` "wraps" between 0.0 and 1.0.
### wave(`v`, `magnitude`)
Same as wave(`v`) * `magnitude`

