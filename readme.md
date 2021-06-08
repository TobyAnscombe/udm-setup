# Unifi Dream Machine / Pro Setup

*I pulled this together in response to an query on the Unifi message boards and as I have way too much time on my hands thought that I would quickly write it up, partly to help out and partly so that I've a record when I come to repeat the install (if I need to!)*

## Start point

The start-point is a clean install of whatever version of the firmware and Unifi Application is current. *I'm on the beta programme so for me thats currently:*

* [UniFi OS Dream Machine 1.10.0.12](https://community.ui.com/releases/UniFi-OS-Dream-Machines-1-10-0-12/ef3c489c-8510-40a0-aa75-d7db4121a373)
* [UniFi Network Application 6.2.26](https://community.ui.com/releases/UniFi-Network-Application-6-2-26/4ce235f8-c479-477e-b71a-e66d5294c5a9)

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

### Blimey, anything else??

Yes, there is also a [Pi-Hole](https://pi-hole.net) but thats probably a different readme. FWIW you should always have a pi-hole on your network. Mines a simple [Raspberry Pi 4](https://www.raspberrypi.org) that I have running with a PoE+ HAT so it just plugs into my switch under the stairs.. oh its also a docker box that can run a bunch of things and I'm probably going to add another one with a dual NIC module to get around the PPPoE issue that seems to affect the [UDM/UDMP throughput](https://community.ui.com/tracker/UDM-Throughput-Degradation/1c94f307-0519-4c65-94bd-58b2f9fb65a9) 

## Basic Setup

These are my settings for my use-case, you may have different needs but this will give you a starting point. Most of these are set to the default as I've not (yet) found a good reason to change them..

### HOME LAN

Out of the box there should be a network called LAN already created. You can check this by going to [https://192.168.1.1/network/default/settings/networks](https://192.168.1.1/network/default/settings/networks) assuming that you have the default ip range running. 

I double-check that `Content Filtering` is set to `NONE` as if I want content filtering it will be via the Pi-Hole rather than some random set of urls applied.
                                                                                                                                                                                                 |
### IoT LAN

So this is the LAN that will be used by all of our IoT devices. Its going to pretty much mimic the same setup as above but with a couple of changes. 

To create a new LAN just click the `create` button on the top-right hand side of the new Unifi UI interface. 

The key consideration is the number you assign to the VLAN ID. Leave it blank and it will just merge with the LAN you created earlier. We need to set it to a value and I use `107` as it looks a bit like `IoT`so it works as a nice and simple aide-memoir. 

### Network Settings

| Setting               | Home LAN                                 | IoT LAN                                      | Description                                                                                                                                                                                                    |
|-----------------------|------------------------------------------|----------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| VLAN ID               | blank                                    | 107                                          | You can set your LAN to be a specific VLAN but before you do, make sure you understand how tagging works...                                                                                                    |
| Domain Name           | localdomain                              | localdomain                                  | The default setting, gets appended to any local DNS entries                                                                                                                                                    |
| Device Isolation      | Off                                      | Off                                          | If you set this to TRUE then any device cant talk to any other device on the same network. Great for a guest network, less good for your home LAN..                                                            |
| IGMP Snooping         | Off                                      | Off                                          | I've got this set to off as I run a multicast re-broadcaster to help with SONOS, you may need this on..                                                                                                        |
| Auto Scale Network    | Off                                      | Off                                          | I've no idea what this does other than 'Automagically` extend your network size'.. As I don't trust auto-magic things then it stays off                                                                         |
| DHCP MOde             | DHCP Server                              | DHCP Server                                  | Allows the UDM to hand out IP addresses                                                                                                                                                                        |
| Gateway IP/Subnet     | 192.168.1.1/24                           | 192.168.107.1/24                             | The range allocated to your  network.                                                                                                                                                                          |
| DHCP Range            | Start: 192.168.1.100 Stop: 192.168.1.254 | Start: 192.168.107.100 Stop: 192.168.107.254 | The range of IP's that your UDM will hand out - I always set the start to be high so that if I need to add any fixed IP devices then I can do without any address conflict                                     |
| DHCP Name Server      | 192.168.1.53                             | 192.168.1.53                                 | So this is my setting for my PI-Hole. If you don't have one then I'd recommend NextDNS or Cloudflare (1.1.1.1). You can always set it to AUTO and it will just pickup whatever DNS server your ISP provides. |
| DHCP Lease Time       | 86400                                    | 86400                                        | Lease time in seconds for a DHCP lease - set to 1 day but you may wish to go shorter                                                                                                                           |
| DHCP Gateway IP       | Auto                                     | Auto                                         | Default setting                                                                                                                                                                                                |
| DHCP UniFi Controller | blank                                    | blank                                        | Default setting                                                                                                                                                                                                |
| DHCP Guarding         | Off                                      | Off                                          | Default setting                                                                                                                                                                                                |
| DHCP NTP Server       | Off                                      | Off                                          | Default setting                                                                                                                                                                                                |
| DHCP Network Boot     | Off                                      | Off                                          | Default setting                                                                                                                                                                                                |
| DHCP Time Offset      | Off                                      | Off                                          | Default setting                                                                                                                                                                                                |
| DHCP WPAD URL         | blank                                    | blank                                        | Default setting                                                                                                                                                                                                |
| DHCP TFTP Server      | blank                                    | blank                                        | Default setting                                                                                                                                                                                                |
| DHCP Option           | blank                                    | blank                                        | Default setting                                                                                                                                                                                                |
| IPv6 Interface Type   | None                                     | None                                         | Don't use IPv6 on my home LAN                                                                                                                                                                                  |



## Wireless networks

So you now need to create the wireless networks that the devices will connect to. I tend to have two (plus a guest network). Given that there seems to be much `magic` associated with these settings on the forum, these may not be the most optimal settings but for me, in my location with a mix of 2.4g, 5g, Wifi5 and Wifi6 clients they seem to work. Always welcome to other views that can increase throughput...

| Setting                                                      | Main Wifi Network      | IoT Wifi Network            | Description                                                                                                                                                                                                                                                                                                                 |
|--------------------------------------------------------------|------------------------|-----------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Enable                                                       | True                   | True                        | Simple on/off setting                                                                                                                                                                                                                                                                                                       |
| Name                                                         | `YourMainWifiNetwork`  | `IoT`                       | The name of the wifi network                                                                                                                                                                                                                                                                                                |
| Password                                                     | `SomeComplexPassword`  | `ADifferentComplexPassword` | The password used to connect                                                                                                                                                                                                                                                                                                |
| Network                                                      | LAN                    | IoT                         | Allows the wifi to be associated to a specific network which is what we want                                                                                                                                                                                                                                                |
| Wifi Band                                                    | 5Ghz                   | 2.4Ghz                      | I split mine so that all IoT devices (which normally have crappy radios) connect to 2.4Ghz and my main network uses the 5Ghz bands. Its a simple way of managing the RF channels for me, may not work for you..                                                                                                             |
| Broadcasting AP's                                            | All                    | All                         | You may want to limit the AP's that broadcast..                                                                                                                                                                                                                                                                             |
| UAPSD                                                        | True                   | False                       | IoT devices typically won't support this so leave it off                                                                                                                                                                                                                                                                    |
| Multicast Enhancement                                        | False                  | False                       | As mentioned, I have a re-broadcast setup as the out of the box solution doesn't seem to work well for SONOS devices.                                                                                                                                                                                                       |
| High Performance Devices                                     | False                  | False                       | By making the split between bands at the Logical level then there is no need for devices to transition between 2.4 and 5G; The simple principle is keep stuff turned off unless you need it..                                                                                                                               |
| BSS Transition                                               | True                   | True                        | Based on information provided [HERE](https://help.ui.com/hc/en-us/articles/360049424214-UniFi-SSID-Options)                                                                                                                                                                                                                 |
| Proxy ARP                                                    | True                   | True                        | Based on information provided [HERE](https://help.ui.com/hc/en-us/articles/360049424214-UniFi-SSID-Options)                                                                                                                                                                                                                 |
| L2 Isolation                                                 | False                  | False                       | IF the devices are isolated then they cant talk to each other!                                                                                                                                                                                                                                                              |
| Legacy Support                                               | False                  | False                       | I don't have any legacy devices (11b) so can turn this off                                                                                                                                                                                                                                                                  |
| Enable Fast Roaming                                          | False                  | False                       | I've only got a couple of AP's so don't roam very often. I've seen reported that some Apple devices struggle with this when enabled so based on principle above I leave it turned off                                                                                                                                       |
| Bandwidth Profile                                            | Default                | Default                     | If you want to limit the bandwidth used by your wireless devices then this is where you can set it. Before I had my fibre connection installed I had this set to 1Mb for the IoT side as my 4G modem only gave me about 20Mbps for the whole house and working from home in a pandemic means many, many MS Teams/Zoom calls |
| Security Protocol                                            | WPA-2                  | WPA-2                       | Its the safest option (aka takes a bit longer to hack).                                                                                                                                                                                                                                                                     |
| Hide Wifi Name                                               | False                  | True                        | I hide my IoT network, not for security but to just stop people from accidentally connecting to it.                                                                                                                                                                                                                         |
| PMF                                                          | Optional               | Disabled                    | Main wifi is optional to maximize compatibility, IoT really shouldn't need it..given that its pretty much an untrusted network                                                                                                                                                                                              |
| Group Rekey Interval                                         | False                  | False                       | Keep it simple                                                                                                                                                                                                                                                                                                              |
| MAC address Filter                                           | False                  | False                       | You **could** use this to only allow specific MAC addresses but c'mon, is it really worth it?                                                                                                                                                                                                                               |
| RADIUS MAC Authentication                                    | False                  | False                       | No RADIUS server on my network                                                                                                                                                                                                                                                                                              |
| Override DTIM Period                                         | True                   | True                        | Cant remember why I set it to true, read somewhere that its a good thing..                                                                                                                                                                                                                                                  |
| DTIM 2G Period                                               | 3                      | 3                           | Cant remember why, read somewhere that its a good thing                                                                                                                                                                                                                                                                     |
| DTIM 5G Period                                               | 3                      | 3                           | Cant remember why, read somewhere that its a good thing                                                                                                                                                                                                                                                                     |
| 2G Data Rate Control                                         | False                  | False                       | Main LAN - don't have any 2.4Ghz devices. IoT LAN keep it simple                                                                                                                                                                                                                                                            |
| 5G Data Rate Control                                         | True Data Rate: 12Mbps | False                       | Main LAN - trying to boost the speed. IoT LAN keep it simple                                                                                                                                                                                                                                                                |
| Require Clients to use rates at or above the specified value | True                   | False                       | If you are going to set a Data Rate then you want devices to use it                                                                                                                                                                                                                                                         |
| Send beacons at 6Mps                                         | False                  | N/A                         | Main LAN - I've not had any issues keeping it at 12 Mbps. IoT LAN - not applicable as the data rates haven't changed                                                                                                                                                                                                        |

## LAN and Wireless setup complete!

So, thats the basics - you will now have 2 LANs and 2 wireless networks. You can connect the relevant devices to each network and just check that all is good. Noting that to use the apps and things that are used by the IoT network you will need to connect your device to that network as currently there is no real connectivity between the two. Thats the next step...

## Firewall Rules

So, to enable your main LAN to be secure and walled off from your IoT LAN we need to set some firewall rules. Its pretty simple and you only need a couple of rules.                                                                                                                                                                 

You can get to the firewall rules at the following address [https://192.168.1.1/network/default/settings/security/threat-management](https://192.168.1.1/network/default/settings/security/threat-management) or login into the UDM and choose settings/security/firewall.

```
Before we start, confession time. I've been very lazy on how I've set these up. Before a bunch of security professionals start flaming me I'd like to remind them that security is a continuum of ease of use vs secure and I've plonked myself on the convenience side of the fence..
```
With that out the way these are the rules that I've got setup.

### Groups

The easiest way to ensure that things are running how you want is to define some groups and to use them in the rules. That way you can change the groups at a later date and not have to worry about changing multiple firewall rules. The groups that I have setup are as follows:

| Name            | Type                | Values                                            | Description                                                                                              |
|-----------------|---------------------|---------------------------------------------------|----------------------------------------------------------------------------------------------------------|
| IoT             | IPv4 Address/Subnet | 192.168.107.0/24                                  | The range of addresses in the IoT network.                                                               |
| LAN             | IPv4 Address/Subnet | 192.168.1.0/24                                    | The range of addresses in the LAN network.                                                               |
| Sonos Speakers  | IPv4 Address/Subnet | 192.168.107.195, 192.168.107.196, 192.168.107.197 | The (fixed) IP's of the Sonos speakers on my network. You will need to get the IP's of your own speakers |
| Homekit Ports   | Port Group          | 5353, 51827, 8123                                 | The range of ports that Homekit uses based on internet searching I've done                               |

The reason for this simple setup is Sonos uses loads of ports so creating a port map for every single combo is just painful. I've taken the easy option of just allowing those IP's that are used by the Sonos devices to come back through the firewall. 

_Obviously this means that if someone was to spoof my Sonos IP's then they could get through the firewall but if someone is able to spoof an IP inside my network then I've probably got bigger issues..._

### Firewall Rules

Note that all these rules are set on the `LAN IN` section of the Firewall rules:

| Type   | Description                                 | Enabled | Rule Applied               | Action | IPv4Protocol | Match all protocols except for this | Source Type        | IPv4 Address Group | Port Group    | Destination Type   | IPv4 Address Group | Port Group  | Enable logging | Match State New | Match State Established | Match State Invalid | Match State Related | IPSec       |
|--------|---------------------------------------------|---------|----------------------------|--------|--------------|-------------------------------------|--------------------|--------------------|---------------|--------------------|--------------------|-------------|----------------|-----------------|-------------------------|---------------------|---------------------|-------------|
| LAN IN | Sonos                                       | TRUE    | Before Predetermined Rules | Accept | TCP and UDP  | FALSE                               | Address/Port Group | Sonos Speakers     | Any           | Address/Port Group | LAN                | Any         | FALSE          | FALSE           | FALSE                   | FALSE               | FALSE               | Don’t match |
| LAN IN | IoT > Home LAN - Allow existing connections | TRUE    | Before Predetermined Rules | Accept | All          |                                     | Network            | IoT                | IPv4 Subnet   | Network            | LAN                | IPv4 Subnet | FALSE          | FALSE           | TRUE                    | FALSE               | TRUE                | Don’t match |
| LAN IN | Isolate IoT Network                         | TRUE    | Before Predetermined Rules | Drop   | All          |                                     | Network            | IoT                | IPv4 Subnet   | Network            | LAN                | IPv4 Subnet | FALSE          | FALSE           | FALSE                   | FALSE               | FALSE               | Don’t match |
| LAN IN | Homekit                                     | TRUE    | Before Predetermined Rules | Accept | TCP and UDP  | FALSE                               | Address/Port Group | IoT                | Homekit Ports | Address/Port Group | LAN                | Any         | FALSE          | FALSE           | FALSE                   | FALSE               | FALSE               | Don’t match |

This should allow the IoT VLAN to get access to the LAN when needed and allow any LAN-initiated connections to allow pass-through. All we need to do now is to add in a multicast relay so that things that are initiated on the LAN are actively passed through. Unifi does have mDNS but I've not had much luck so I tend to use an externally provided service in the form of a docker container that can be run under Podman on the UDM directly. 

## Multicast Relay

The UDM and Pro run a service called Podman under the hood - worthy of a whole separate note! There are some rather good resources out there on things that you can do with the UDM and the best one that I follow and use is [udm-utilities](https://github.com/boostchicken/udm-utilities) by @boostchicken. You can download and install some rather nifty things that make the UDM much more of a pleasure to run and use but the one that caught my eye was the multicast relay from (scyto)[https://hub.docker.com/r/scyto/multicast-relay].

You don't need to have all of the `udm-utilities` installed but they certainly help! if all you want to do is try it and _suck it and see_ then ssh into your UDM and run the following:
```
killall avahi-daemon

podman run --restart=always --name="multicast-relay" --network=host -e OPTS=" --verbose" -e INTERFACES="br0 br107" docker.io/scyto/multicast-relay
```

Ok, so what does this do? 
_killall avahi-daemon_ The UDM runs a service called avahi that basically allows discovery and publication of services on your network. However, the Unifi version seems not to work particularly well, hence why we need to run this container in the first place. This command kills the running instance and prevents any conflicts to the container we are about to run.

_podman run --restart=always --name="multicast-relay" --network=host -e INTERFACES="br0 br107" docker.io/scyto/multicast-relay_ this tells podman to run the container found at docker.io/scyto/multicast-relay on the podman host network using the interfaces that are on VLAN 0 and 107 (change to match what you use). 

what you should see is something like the following:
```
# podman run --restart=always --name="multicast-relay" --network=host -e OPTS=" --verbose" -e INTERFACES="br0 br107" docker.io/scyto/multicast-relay
starting multicast-relay
Using Interfaces: br0 br107
Using Options --foreground  --verbose
Jun-08 12:25:48 ./multicast-relay/multicast-relay.py INFO: Adding broadcast relay for 255.255.255.255:6969 (Sonos Setup Discovery)
Jun-08 12:25:48 ./multicast-relay/multicast-relay.py INFO: Adding multicast relay for 224.0.0.251:5353 (mDNS)
Jun-08 12:25:48 ./multicast-relay/multicast-relay.py INFO: Adding multicast relay for 239.255.255.250:1900 (SSDP)
Jun-08 12:25:48 ./multicast-relay/multicast-relay.py INFO: [mDNS] Relayed 180 bytes from 192.168.1.183:5353 on br0 [ttl 255] to 224.0.0.251:5353 via br107/192.168.107.1
Jun-08 12:25:52 ./multicast-relay/multicast-relay.py INFO: [SSDP] Relayed 129 bytes from 192.168.1.244:43132 on br0 [ttl 1] to 239.255.255.250:1900 via br107/192.168.107.1
```
This shows that data is being relayed from br107 to br0 and broadcast. Having let it run for a bit and having Sonos playing gives the following logs:
```
Jun-08 12:27:08 ./multicast-relay/multicast-relay.py INFO: [mDNS] Relayed 516 bytes from 192.168.1.183:5353 on br0 [ttl 255] to 224.0.0.251:5353 via br107/192.168.107.1
Jun-08 12:27:08 ./multicast-relay/multicast-relay.py INFO: [mDNS] Relayed 332 bytes from 192.168.107.189:5353 on br107 [ttl 255] to 224.0.0.251:5353 via br0/192.168.1.1
Jun-08 12:27:09 ./multicast-relay/multicast-relay.py INFO: [mDNS] Relayed 358 bytes from 192.168.1.183:5353 on br0 [ttl 255] to 224.0.0.251:5353 via br107/192.168.107.1
Jun-08 12:27:10 ./multicast-relay/multicast-relay.py INFO: [mDNS] Relayed 516 bytes from 192.168.1.183:5353 on br0 [ttl 255] to 224.0.0.251:5353 via br107/192.168.107.1
Jun-08 12:27:11 ./multicast-relay/multicast-relay.py INFO: [mDNS] Relayed 554 bytes from 192.168.1.183:5353 on br0 [ttl 255] to 224.0.0.251:5353 via br107/192.168.107.1
Jun-08 12:27:12 ./multicast-relay/multicast-relay.py INFO: [SSDP] Relayed 129 bytes from 192.168.1.244:43132 on br0 [ttl 1] to 239.255.255.250:1900 via br107/192.168.107.1
Jun-08 12:27:14 ./multicast-relay/multicast-relay.py INFO: [mDNS] Relayed 516 bytes from 192.168.1.183:5353 on br0 [ttl 255] to 224.0.0.251:5353 via br107/192.168.107.1
Jun-08 12:27:17 ./multicast-relay/multicast-relay.py INFO: [SSDP] Relayed 735 bytes from 192.168.107.145:58845 on br107 [ttl 1] to 239.255.255.250:1900 via br0/192.168.1.1
Jun-08 12:27:17 ./multicast-relay/multicast-relay.py INFO: [mDNS] Relayed 180 bytes from 192.168.1.102:5353 on br0 [ttl 255] to 224.0.0.251:5353 via br107/192.168.107.1
Jun-08 12:27:19 ./multicast-relay/multicast-relay.py INFO: [SSDP] Relayed 734 bytes from 192.168.107.195:39543 on br107 [ttl 1] to 239.255.255.250:1900 via br0/192.168.1.1
Jun-08 12:27:20 ./multicast-relay/multicast-relay.py INFO: [mDNS] Relayed 554 bytes from 192.168.1.183:5353 on br0 [ttl 255] to 224.0.0.251:5353 via br107/192.168.107.1
Jun-08 12:27:22 ./multicast-relay/multicast-relay.py INFO: [SSDP] Relayed 129 bytes from 192.168.1.244:43132 on br0 [ttl 1] to 239.255.255.250:1900 via br107/192.168.107.1
``` 
you can see the IP's and ports that are being forwarded (noting that 192.168.107.1/24 is my IoT network to my main LAN 192.168.1.1/24)

you can run it as a non-interactive process (daemon) by using the following:
```
podman run -d --restart=always --name="multicast-relay" --network=host -e INTERFACES="br0 br107" docker.io/scyto/multicast-relay
```

If you do install _udm-utilities_ then you can add a simple bash script to automate this when the UDM starts up. You can get more info from the [readme.md](https://github.com/boostchicken/udm-utilities/) but the _tl;dr_ version is create the following file in `/mnt/data/on_boot.d` directory called `30-multicast-relay.sh` and the following content:
```
#!/bin/sh

# kill all instances of avahi-daemon (UDM spins an instance up even with mDNS services disabled)
killall avahi-daemon

## Start the multicast-relay container from https://hub.docker.com/r/scyto/multicast-relay
podman stop multicast-relay

echo "Stopped running container"

podman rm multicast-relay

echo "Removed existing container named multicast-relay"

podman run -d --restart=always --name="multicast-relay" --network=host -e OPTS=" --verbose" -e INTERFACES="br0 br107" docker.io/scyto/multicast-relay

echo "Container running"
```

Then run the following commands

`chmod +x 30-multicast-relay.sh`

`./30-multicast-relay.sh`

this will run the commands in the file and all being well leave it running.