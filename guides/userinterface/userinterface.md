# Pixelblaze App User Interface

<style>
.warning {
  color: red;
}

pre {
	color: inherit;
	background-color: #222;
	padding: 1em;
}

img.full {
	width: 100%;
	max-width: 600px;
}

img.full-big {
	width: 100%;
}
       
</style>

## Accessing the App

Pixelblaze has a built-in web server and web app that has everything you need! Once WiFi is configured, you access it directly with any modern browser.

### Client Mode IP Address Discovery

In Client mode, Pixelblaze connects to your local WiFi network and gets an IP address from your router. If you enable the Discovery Service, you can always find it here:

[http://discover.electromage.com/](http://discover.electromage.com/)

This service will work as long as the device you are using is on the same network.

If you did not enable the cloud discovery feature, you'll need to find this IP address to use it. [These instructions for finding a raspberry pi](https://www.raspberrypi.org/documentation/remote-access/ip-address.md) without a display provide a good outline of ways to find a device on your network. If you use the nmap method e.g.:

```
nmap -sn 192.168.1.0/24
```

### AP (Access Point) Mode

In AP mode, you can always find Pixelblaze at 

[http://192.168.4.1](http://192.168.4.1)

## Using the Web Interface

Once you know the IP address of your controller, you can point a browser to it. You should see a screen that looks like this:

<img class="full" src="patterns_page.png">


### Navigation and Header

Lets take a closer look at the header. 

<img class="full-big" src="header_annotated.png">

There are 5 tabs across the top: Patterns, Edit, Mapper, Settings, and WiFi.


### Patterns

Here you can preview and select any pattern that has been previously saved to the controller. Your controller should have come with a few patterns to get you started.

You can download a pattern file for sharing by clicking **Export**, and remove patterns you no longer want with **Delete**.

Clicking anywhere else on the line will select the pattern for display.

The Edit button will take you to the pattern editor.

#### Sequencer and Playlist

<img class="full" src="sequencer_editing.png">

The sequencer can shuffle through all patterns or through a playlist with a specific order and timing.

### Edit

Here you can create new patterns, edit existing patterns, or load pattern files.

A pattern is a tiny software program, typically made up of mathematical expressions to generate pixels.

#### Creating a New Pattern

Click the **New Pattern** button.

A very simple boilerplate pattern is filled in for you. Everything you type is compiled on the fly and sent to the controller. An online reference is provided on the same page, just below the editor.

You may want to jump to [Writing Your First Pattern](https://www.bhencke.com/pixelblazegettingstarted#writing-your-first-pattern)

You can save a pattern by providing a unique name and clicking **Save** once a preview has been generated. After each edit, a preview is generated. This takes a few seconds.

If you are editing an existing pattern, **Save** will become an **Update** button.

### Mapper
The mapper tab allows you specify the position of your LEDs in 2D or 3D space, so you write patterns in terms of x, y, and z instead of the pixel index. Learn more on the [Mapping page](https://www.bhencke.com/mapping-in-pixelblaze).

### Settings

Here you can change settings related to the LED strip and other options.


Setting | Description
------------- | -------------
Name | You can name your Pixelblaze, making it easier to find. This is shown on the discovery service page, and in Firestorm.
Limit Brightness | You can limit the brightness to a specific percentage here. This works in comination with the global brightness slider, and can be used to limit power draw.
Pixels | The Pixels setting controls how many pixels will be generated. Since these are calculated as they are sent out, very large strips can be supported with modest frame rates.
Led Type | Pick the LED Type corresponding to the type of pixels you’ve connected. If you are using an Output Expander, select that option here. You can also select "No LEDs" and work via the live preview.
Pixels | The Pixels setting controls how many pixels will be generated. If your animations aren't making it all the way to the end, this is most likely the setting you need to adjust.
Data Speed | Some LED types can accept various data speeds. If you experience flickering or glitches, try differente Data Speed values. Higher values usually result in smoother animations, but may not work with all LEDs, or over long distances.
Color Order | The Color Order determines what is red, green, or blue, and if you have 4-element LEDs with a white LED. If your colors don't match the live preview, try different values here. For RGBW LEDs, you can choose between using the white element, or skipping it with the "-W" variants.
Output Expander Configuration | If using the Output Expander, you can configure each board here. Click `Add Board` if no panels are shown. Each board's address and individual channels can be configured with a type, start index, count, and options like color order.
Discovery Service | If you want to use the discovery service, or want to use any of the time/clock functions, leave this enabled. When enabled, Pixelblaze will report it's local IP and get the time once an hour.
Time Zone | For time/clock functions, this is used to localize the times to your time zone.
Auto Off Timer | You can use this setting to turn the LEDs off at night, and back on in the morning/evening. The LEDs will be turned off if the current time is after the `Dark at` setting or before the `Resume at` setting.

You can also check for version updates when Pixelblaze is connected to the internet. Click `Check for Update` and if a new version is available the `Perform Update and Restart` button will be enabled. Once started, the status will be displayed and Pixelblaze will restart with the new version automatically.

### WiFi

Pixelblaze version 3 introduced a WiFi tab to quickly change WiFi settings. 
