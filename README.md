
# Home Assistant Configuration by @rrubin0
This is my [Home Assistant](https://home-assistant.io/) configuration.

I run Home Assistant in a Docker Container on a re-purposed [ASUS Chromebox M004U](https://amzn.to/1rdaB85) running Ubuntu Server 18.04

My primary control method is Z-Wave. I chose Z-Wave for its performance and fit, but also have adjusted per use case. For example, I use Z-Wave for instances where I need to control many lights from a switch at once and fine control over dimming or color LED is less important. My kitchen is a good example of this, as there are 6 can lights here and it would be quite expensive to use Philips Hue.

I use Philips Hue for the following use cases:
- Color LED (although there are other options)
- Fine control over dimming is a requirement
- I have a single or just a couple of bulbs
- The area is not one where it is natural for others to hit the wall switch

The switch problem with Hue can be solved by adding one of their wall switches to control, or using automations to control the lights in anticipation of your household users

**Software & Addons in use:**
* [Dasher](https://github.com/stjohnjohnson/mqtt-dasher)
* [Haaska](https://github.com/auchter/haaska)
* [Homebridge](https://github.com/nfarina/homebridge)
* [FloorPlan](https://github.com/pkozul/ha-floorplan)
* [Lets Encrypt](https://home-assistant.io/addons/lets_encrypt/)
* [Mosquitto](https://home-assistant.io/addons/mosquitto/)
* [Terminal](https://github.com/hassio-addons/addon-terminal)


**Devices I Use:**
* [ecobee3 Thermostat](http://amzn.to/2iD0v0z) with [ecobee3 Remote Sensors](http://amzn.to/2iCZFRw)
* ecobee3 Lite Thermostat
* [Amazon Echo Dot Gen 2](http://amzn.to/2hvCexj)
* [Amazon Dash Buttons](http://amzn.to/2i6acYv)
* [Amazon Fire TV Stick](http://amzn.to/2iD9uPx)
* Amazon Dash Wand
* [Philips Hue (Gen 2)](http://amzn.to/2hvyzzK)
* [RGBWW LED Strip WiFi Controller](http://amzn.to/2i6mUqn) with [RGB LED Strips](http://amzn.to/2i68N42)
* Fibaro Z-Wave MultiSensor
* [Aeon Labs Z-Wave Minimote](http://amzn.to/2igetsU)
* Somfy Roller Shades with Z-Wave RTS Bridge
* Pentair 5G LED Pool Light
* Etekcity 433MHz RF Switches
* Broadlink RM2/Pro
* HUSBZB-1 ZigBee/Z-Wave Stick
* GE Z-Wave Wall Switches & Dimmers
* Flux LED strips and bulb
* Sonoff WiFi DIY power strips
* NodeMCU DIY Multisensor
* Aeon Labs Z-Wave MultiSensor 6
* Aeon Labs Z-Wave Door/Window Sensor
* Aeon Labs Z-Wave Minimote
* Aeon Labs Z-Wave Siren
* GoControl Z-Wave Siren
* Yale Real Living Z-Wave Touchscreen Lever Lock

(coming soon):
* IKEA Tradfri Bridge
* IKEA Tradfri Motion
* IKEA Tradfri Dimmer
* IKEA Tradfri Remote
* IKEA Tradfri Light