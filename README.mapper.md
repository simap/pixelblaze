# Table of Contents

1. [Pixel Mapping System Overview](#toc_1)
2. [Coordinate World Units](#toc_2)
3. [Using the Map Editor](#toc_3)
4. [Map Using a JSON Array](#toc_4)
5. [Map Using JavaScript](#toc_5)
6. [Rendering with Pixel Maps](#toc_6)

# Pixel Mapping System Overview

The pixel mapping system allows you to generate patterns that are aware of the physical LED layout. This can be used for matrix arrays, LED rings, costumes, or pretty much anything you can imagine! Patterns that support 2D and/or 3D can usually be used with any physical pixel layout without modification because they scale to the size of the mapped world.

First, you need a pixel map. This tells the system what the coordinates are for each pixel. This can be either a pair of coordinates for 2D mapping, or a triplet for 3D mapping. More details on how to specify these maps is provided below.

Once you have a pixel map saved on the Mapper tab, patterns can then start using these coordinates by having a `render2D(index, x, y)` and/or `render3D(index, x, y, z)` function, which will be preferred when a pixel map is installed.

Many patterns implement all 3 render variants and are universally compatible with any pixel map type.

# Coordinate World Units

The system automatically scales pixel map coordinates to 0.0 to 1.0 values. This represents the position within the mapped world.

This lets you enter real physical dimensions, which are automatically converted. You can input coordinates using inches, millimeters, pixels, etc. The world size will be calculated based on the limits of the map, and the coordinates will be converted to positions inside that world.

There are two options for how coordinates are scaled:

* Fill - If necessary, the map will be stretched or squished to fit every dimension.
* Contain - The map keeps its aspect ratio, but is resized to fit the largest dimension.


# Using the Map Editor

The editor supports either a JSON array of coordinates, or a JavaScript function that generates a coordinate array. Each element in the top level array represents a pixel. Within a pixel, an array with elements for the x, y, and optionally z coordinate.

Changes to the editor, if valid, are applied live so you can quickly see how your changes affect the map. Both the map preview and the rendered pattern will use the updated coordinates.

Once you are happy with the map, it can be saved and will be loaded again the next time Pixelblaze starts up.

# Map Using a JSON Array

The editor can accept a plain JSON format array of arrays. Each element in the top level array represents a pixel. Within a pixel, an array with elements for the x, y, and optionally z coordinate.

For example here is a box with 4 pixels, one in each corner: top left, top right, bottom right, and finally bottom left.

```
[
  [0,0],
  [100,0],
  [100,100],
  [0,100],
]
```

# Map Using JavaScript

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

This code snippet can generate a pixel map for many kinds of LED matrices by using the `width` variable in combination with the pixelCount which is configured in Settings and passed in to this function. The height is calculated automatically, or rather it keeps adding rows of `width` size until pixelCount is reached.

# Rendering with Pixel Maps

Once a pixel map is configured, patterns can start using the coordinate information in pattern code.

Each pixel is still rendered one at a time, but special render functions take arguments that give coordinate information. The default 1D `render(index)` takes just an integer pixel index, but `render2D(index, x, y)` and `render3D(index, x, y, z)` add additional coordinate information in "world units" - a value between 0.0 and 1.0.

To use pixel map data, a pattern exports a render2D and/or render3D function. This happens in the pattern code, over on the Edit tab. e.g. on a matrix this will show a fading rainbow gradient:

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

Note that x, y, z are all in "world units" and have values between 0.0 and 1.0 (exclusive). By using "world units" patterns can be written for any possible configuration without having to know the size of the world.
