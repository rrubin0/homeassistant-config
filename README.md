
# Home Assistant Configuration by @rrubin0
This is my [Home Assistant](https://home-assistant.io/) configuration.

I run Home Assistant in a Docker Container on a re-purposed [ASUS Chromebox M004U](https://amzn.to/1rdaB85) running Ubuntu Server 18.04




**My Docker Container Setup**
* [Home Assistant](https://home-assistant.io)
* [Dasher](https://github.com/Nekmo/amazon-dash)
* [Mosquitto](https://hub.docker.com/_/eclipse-mosquitto/)
* [Portainer](https://portainer.io)
* [Influxdb](https://www.influxdb.com)
* [Grafana](https://grafana.com)
* [HA Dockermon](https://github.com/philhawthorne/ha-dockermon)
* [ESP Home](https://esphome.io/)


**My Docker Compose File**
```
version: '2.1'

services:
  mqtt:
    image: eclipse-mosquitto:latest
    container_name: "MQTT"
    restart: always
    network_mode: "host"
    environment:
      - TZ=America/Phoenix
    ports:
      - 1883:1883
      - 9001:9001
    volumes:
      - /home/hauser/mqtt/data:/mosquitto/data
      - /home/hauser/mqtt/config:/mosquitto/config
      - /home/hauser/mqtt/logs:/mosquitto/logs


  grafana:
    image: grafana/grafana:latest
    container_name: "grafana"
    depends_on:
      influxdb:
        condition: service_healthy
    network_mode: "host"
    environment:
      - TZ=America/Phoenix
    ports:
      - 3000:3000
    restart: unless-stopped
    volumes:
      - /home/hauser/grafana:/var/lib/grafana


  influxdb:
    image: influxdb:latest
    container_name: "influxdb"
    healthcheck: 
      test: ["CMD", "curl", "-sI", "http://127.0.0.1:8086/ping"]
      interval: 30s
      timeout: 1s
      retries: 24
    network_mode: host
    environment:
      - TZ=America/Phoenix
    ports:
      - 8083:8083
      - 8086:8086
    restart: unless-stopped
    volumes:
      - /home/hauser/influxdb:/var/lib/influxdb


  home-assistant:
    #image: homeassistant/home-assistant:0.78.3
    image: homeassistant/home-assistant:latest
    container_name: "home-assistant"
    restart: always
    depends_on:
      influxdb:
        condition: service_healthy
      mqtt:
        condition: service_started
    healthcheck:
      test: ["CMD", "curl", "-f", "https://home_assistant_url.com:8123"]
      interval: 30s
      timeout: 10s
      retries: 6
    network_mode: "host"
    environment:
      - TZ=America/Phoenix
    ports:
      - 8123:8123
    volumes:
      - /home/hauser/hass_data/config:/config
      - /usr/share/ssl:/ssl
    devices:
      - /dev/ttyACM0:/dev/ttyACM0


  dasher:
    command: amazon-dash run --ignore-perms --root-allowed --config /config/amazon-dash.yml
    image: nekmo/amazon-dash:latest
    container_name: "dasher"
    restart: unless-stopped
    network_mode: "host"
    volumes:
      -  /home/hauser/dasher/config/amazon-dash.yml:/config/amazon-dash.yml


  portainer:
    image: portainer/portainer
    container_name: "portainer"
    restart: unless-stopped
    network_mode: "host"
    ports:
      - 9000:9000
    volumes:
      -  /var/run/docker.sock:/var/run/docker.sock
      -  /home/hauser/portainer:/data


  esphome:
    command: config/ dashboard
    image: esphome/esphome
    container_name: "esphome"
    restart: unless-stopped
    network_mode: "host"
    ports:
      - 6052:6052
      - 6123:6123
    volumes:
      -  /home/hauser/esphome_data:/config


  unifi-controller:
    image: linuxserver/unifi-controller
    container_name: unifi-controller
    environment:
      - PUID=1000
      - PGID=1000
    restart: unless-stopped
    network_mode: "host"
    ports:
      - 3478:3478/udp
      - 10001:10001/udp
      - 8080:8080
      - 8081:8081
      - 8443:8443
      - 8843:8843
      - 8880:8880
      - 6789:6789
    volumes:
      - /home/hauser/unifi/config:/config



#  unifi:
#    image: jacobalberty/unifi:latest
#    container_name: "unifi"
#    restart: unless-stopped
#    volumes:
#      - ./unifi/data:/unifi/data
#    ports:
#      - 3478:3478/udp
#      - 6789:6789/tcp
#      - 8080:8080/tcp
#      - 8443:8443/tcp
#      - 8843:8843/tcp
#      - 8880:8880/tcp
#      - 10001:10001/udp

#    environment:
#      - TZ=America/Phoenix
#    network_mode: "host"


########################
### RESERVED FUTURE ###
########################
#  watchtower:
#    image: v2tec/watchtower
#    container_name: "watchtower"
#    command: watchtower grafana ha-dockermon portainer dasher --cleanup --interval 30
#    restart: unless-stopped
#    volumes:
#      - /var/run/docker.sock:/var/run/docker.sock
#      #- ./watchtower/config.json:/config.json:ro


#  ha-dockermon:
#    image: philhawthorne/ha-dockermon
#    container_name: "ha-dockermon"
#    restart: unless-stopped
#    ports:
#      - 8126:8126
#    volumes:
#      -  /var/run/docker.sock:/var/run/docker.sock
#      -  /home/hauser/dockermon:/config
#
```


**Other 3rd-party component**
[Haaska](https://github.com/mike-grant/haaska) (Alexa Lambda Function)

**Approach**
My primary control method is Z-Wave. I chose Z-Wave for its performance and stability, but have made adjustments, depending on the use case. 
For example, I use Z-Wave for instances where I need to control several lights from a single switch. My kitchen is a good example, as there are 6 recessed lightbulbs in the ceiling and it would be too expensive to use Philips Hue.
When fine control over dimming or color LED is important, I am using Philips Hue. I have not found a better performing platform for color or granual dimming (transitions, for example). 
Philips Hue has drawbacks, of course. They must remain on and render the existing wall switches useless. I have found some clear plastic covers that are passable to protect the switches.

I do use Philips Hue for the following use cases:
- Color LED (although there are other options)
- Fine control over dimming is a requirement
- I have a single or just a couple of bulbs
- The area is not one where it is natural for people to want to use a wall switch

The switch problem with Hues can be solved by adding one of their wall switches to control, using buttons or z-wave remotes, or using automations to control the lights in anticipation of your household users (presence/motion).


**Other Devices Used:**
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
* HUSBZB-1 ZigBee/Z-Wave Stick (not in use; using Aeotec Z-stick)
* GE Z-Wave Wall Switches & Dimmers
* Flux LED strips (flashed with Tasmota)
* Sonoff WiFi DIY power strips
* NodeMCU DIY Multisensor
* Aeon Labs Z-Wave Minimote
* Yale Real Living Z-Wave Touchscreen Lever Lock
* Xiaomi Mijia (Aqara) Hub
* Xiaomi Door Sensors
* Xiaomi Smart Buttons

