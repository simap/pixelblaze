Pixelblaze V3 Pico: Quick Start
===============

This guide covers getting Pixelblaze up and running in 2 steps:

1. [Connect LEDs / Power](#step1)
1. [WiFi Setup](#step2)
2. [Configure Settings](#step3)

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
</style>

<img src="PB V3 Pico pins.jpg" alt="Pixelblaze V3 Pico Connections" class="full">

# <a name="step1"></a>Step 1: Connect LEDs / Power

Connect Pixelblaze to 5V via the 4 pin connection header. The Pixelblaze Pico was designed so that it may be soldered directly to an LED strip or connected via wires or a wired connector. Many LED strips have power connections and can back-feed Pixelblaze.

For best results:

* Ensure you have a power supply capable of providing the full current necessary for your LEDs.
* Keep metal away from the small blue antenna end of the Pixelblaze, which can interfere with the wireless signal.

<strong class="warning">WARNING:</strong> Do not use a power supply that outputs more than 5V. Connecting 12V to Pixelblaze V3 Pico will permanently damage the controller.

Pixelblaze defaults to APA102 type LEDs and if WS2812 type LEDs are connected may display white and/or flickering patterns until fully configured.

### For APA102 / SK9822 / WS2801 LEDS

Connect both DAT and CLK signals. Pixelblaze V3 Pico's castellated edges are pin-compatible with most APA102 type LED strips.

### For WS2811 / WS2812 / WS2813 / WS2815 / SK6812 LEDs

Connect the DAT signal. If the LED supports a backup data signal, connect the first pixel's backup data input to GND.

<strong class="warning">WARNING:</strong> Connecting 12V to Pixelblaze V3 Pico will permanently damage the controller. For 12V LEDs like the WS2815 and some WS2811 strings, a separate power supply is needed to provide 5V to Pixelblaze. A common ground (GND) between the LED power supply and Pixelblaze must be connected.  The 5V supply must either be connected to the Pixelblaze V3 Pico's VIN/GND pins.


# <a name="step2"></a>Step 2: WiFi Setup

When you power up your Pixelblaze, it will go in to WiFi setup mode. Look for a new wireless network called Pixelblaze_XXXXXX. If you do not see it, sometimes turning WiFi off and back on on your device can help.

Some mobile device operating systems will warn that this setup WiFi network does not provide internet access. If prompted, stay connected to this network.

Once you connect, you should automatically see the setup page. If not, go to

[http://192.168.4.1](http://192.168.4.1)

If you are unable to load this page, first check that you are still connected to the Pixelblaze_XXXXXX WiFi network. Sometimes turning mobile data off temporarily can help if you are unable to connect.

### Choose a WiFi Mode

You can use Pixelblaze in either Client Mode where it connects to an existing WiFi network, or in AP Mode where it will create a new WiFi network.

* Use Client Mode if you want to connect it to your home network or need clock/time functions.
* Use AP Mode if your Pixelblaze will be mobile (such as a wearable), and you need to be able to access it from a mobile device anywhere.

### Finding Pixelblaze in Client Mode

Once the controller has connected to wifi, it will have an IP address on your network.

If you enable the cloud discovery service, you can find your Pixelblaze by visiting the discover page from a device on the same network:

[http://discover.electromage.com](http://discover.electromage.com)

If you did not enable the cloud discovery feature, you'll need to find this IP address to use it. [These instructions for finding a raspberry pi](https://www.raspberrypi.org/documentation/remote-access/ip-address.md) without a display provide a good outline of ways to find a device on your network. If you use the nmap method e.g.:

```
nmap -sn 192.168.1.0/24
```

The controller hostname will likely start with "ESP_" or "Espressif" followed by some hexadecimal.

### Finding Pixelblaze in AP Mode

If using AP Mode to create a new WiFi network, choose a password at least 8 characters long (fewer characters wont work). Then you can always find it at 

[http://192.168.4.1](http://192.168.4.1)

Some mobile device operating systems will warn that this WiFi network does not provide internet access. If prompted, stay connected to this network.

### Resetting WiFi Settings / Getting Back to WiFi Setup Mode

If you need to go back to setup mode, supply power, then press and hold the button for 5 seconds. Shorter presses can be used to switch patterns! The orange status LED will give out 3 quick pusles to let you know you are in Setup Mode.


# <a name="step3"></a>Step 3: Configure Settings

Once your Pixelblaze is online and you can connect to it, the next step is to configure the settings for your LEDs.

On the settings tab:

* Name your Pixelblaze so you can find it more easily. This will set the name that appears in the Discovery page and in other places.
* Configure the number of LEDs, the LED type, LED color order. For RGBW LEDs, be sure to select an RGBW color order!
* Configure other settings. You can set the timezone, auto-off, limit brightness, and more.

If using the Output Expander, change the LED type to "Pixelblaze Output Expander" and then click the "Add Board" button for each expander. In each panel, configure the output channels. For best results, ensure a contiguous pixel address space (without gaps or overlapping sections).

