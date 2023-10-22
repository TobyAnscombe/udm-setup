# Unifi Dream Machine / Pro Setup

** Note that this was pulled together from the Unfi v2.x line, now that they have moved ro v3.x a lot of this is redundant / wont work. **

*I pulled this together in response to an query on the Unifi message boards and as I have way too much time on my hands thought that I would quickly write it up, partly to help out and partly so that I've a record when I come to repeat the install (if I need to!)*



## Task Lists
- [x] update formatting
- [ ] Add Open SpeedTest container to UDM Host
- [ ] Many things on Pi-Hole
- [ ] Add some images and diagrams rather than a wall of text settings
- [ ] Walk through of my @boostchicken udm config

## Start point

The start-point is a clean install of whatever version of the firmware and Unifi Application is current. *I'm on the beta programme so for me thats currently:*

* [UniFi OS Dream Machine 1.11.1](https://community.ui.com/releases/UniFi-OS-Dream-Machines-1-11-1/36c7711e-7b11-4ee6-9f78-df31d4b51398)
* [UniFi Network Application 7.0.21](https://community.ui.com/releases/UniFi-Network-Application-7-0-21/f08ac16f-282a-43ac-9288-c8238e33d888)

## Outcome

..or what is it that we want to achieve? 
For me its a simple vlan setup that allows my IoT devices to exist on a stand-alone network thats generally a 2.4Ghz network and my 'Office' LAN that has wired and wireless devices that are normally on the 5Ghz network. 

I want to be able to access my IoT devices from my LAN but make it a classic `data-diode` approach where information can only flow one way. This will allow my LAN to stay secure whilst allowing access to all the various apps/control surfaces etc that the IoT Devices expect.

### What IoT devices does this work with?

In classic Internet parlance, YMMV but for me this setup works with the following devices:

* Sonos Speakers (3 scattered about the house)
* Phillips Hue lights (too many, also includes Innr lamps as well)
* Bosch Ovens (via Home Connect App)
* Siemens Tumble driers (via Home Connect App)
* Loop Energy Saver (simple device to monitor energy usage)
* ProWarm underfloor heating
* Various Nest Smoke Alarms
* Nest Thermostat
* Logitech Harmony Hub
* Velux Gateway
* Homekit devices
* Arlo Cameras
* Google Chromecast
* Yamaha Home Theater Amp
* Kamado Joe iKamand (essential!)
* IKEA TRÅDFRI Smart Lighting

### Blimey, anything else??

Yes, there is also a [Pi-Hole](https://pi-hole.net) but thats probably a different readme. FWIW you should always have a pi-hole on your network. Mines a simple [Raspberry Pi 4](https://www.raspberrypi.org) that I have running with a PoE+ HAT so it just plugs into my switch under the stairs.. oh its also a docker box that can run a bunch of things and I'm probably going to add another one with a dual NIC module to get around the PPPoE issue that seems to affect the [UDM/UDMP throughput](https://community.ui.com/tracker/UDM-Throughput-Degradation/1c94f307-0519-4c65-94bd-58b2f9fb65a9) 

## Basic Setup

These are my settings for my use-case, you may have different needs but this will give you a starting point. Most of these are set to the default as I've not (yet) found a good reason to change them..

### LAN and IoT LAN

[Basic LAN setup](networks.md)

### Wireless Networks

[Basic Wireless Network setup](wireless-networks.md)

#### LAN and Wireless setup complete!

So, thats the basics - you will now have 2 LANs and 2 wireless networks. You can connect the relevant devices to each network and just check that all is good. Noting that to use the apps and things that are used by the IoT network you will need to connect your device to that network as currently there is no real connectivity between the two. Thats the next step...

## Firewall Rules

So, to enable your main LAN to be secure and walled off from your IoT LAN we need to set some firewall rules. Its pretty simple and you only need a couple of rules.

[Firewall Rules](firewall-setup.md)

## Multicast Relay 

To allow proper multicast (compared to the broken one that ships with UnifiOS) you can install one via `podman`

[Docker multicast relay](multicast-relay.md)

## Useful docs to read

[List of useful links](useful-links.md)
