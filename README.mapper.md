# Table of Contents

1. [Using the Pixel Mapper](#toc_1)
2. [Rendering with Pixel Maps](#toc_2)
3. [JSON Array Map](#toc_3)
4. [Generated Map Using Javascript](#toc_4)

# Using the Pixel Mapper

The pixel mapper allows you to generate patterns that are aware of the physical LED layout. This can be used for matrix arrays, LED rings, costumes, or pretty much anything you can imagine!

Each pixel is still rendered one at a time, but special render functions take arguments that give coordinate information. The original linear `render(index)` takes just an integer pixel index, but `render2D(index, x, y)` and `render3D(index, x, y, z)` add additional coordinate information in "world units" - a value between 0.0 and 1.0.

By using "world units" patterns can be written for any possible configuration without having to know the size of the world.

The editor automatically scales input coordinates to world units. This lets you enter real physical dimensions, which are automatically converted. As long as you use the same unit, you can input coordinates using inches, millimeters, pixels, etc. The world size will be calculated based on the limits of the map, and the coordinates will be converted to positions inside that world.

# Rendering with Pixel Maps

To use pixel map data, implement and export a render2D or render3D function. e.g. on a matrix this will show a fading rainbow gradient:

```
export function render2D(index, x, y) {
	hsv(x, 1, y)
}
```

Likewise, render3D can produce a HSV volumetric cube showing all color capabilities:

```
export function render3D(index, x, y, z) {
	hsv(x, y, z)
}
```

Note that x, y, z are all in "world units" and have values between 0.0 and 1.0 (exclusive).

The world units work very similarly to using something like `index/pixelCount`, except its faster and with less code!

# JSON Array Map

The editor can accept a plain JSON array of arrays or a JavaScript value that evaluates to one. Each element in the top level array represents a pixel. Within a pixel, an array with elements for the x, y, and optionally z coordinate.

For example here is a box with 4 pixels, one in each corner: top left, top right, bottom right, and finally bottom left.

```
[
  [0,0],
  [100,0],
  [100,100],
  [0,100],
]
```

# Generated Map Using Javascript

The editor can also run some JavaScript in your browser to generate a pixel map. This can be handy for generated or repetitive structures, but is not limited to those. You could combine arrays and generative structures in creative ways.

Note that this JavaScript is run on your browser, and not the same as Pixelblaze's language. Many of Pixelblazes shortcuts are not available, e.g. use `Math.cos()` instead of `cos()`. Any code you write here is only evaluated once in order to produce the pixel map. The function can take a pixelCount argument.

If you rely on pixelCount and change the number of pixels, visit the mapper page and save it to re-generate the pixel map.

e.g. a 2D matrix with zigzag wiring:

```
function (pixelCount) {
  width = 8
  var map = []
  for (i = 0; i < pixelCount; i++) {
    y = Math.floor(i / width)
    x = i % width
    x = y % 2 == 1 ? width - 1 - x : x //zigzag
    map.push([x, y])
  }
  return map
}
```

