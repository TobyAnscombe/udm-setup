# Firewall Setup

You can get to the firewall rules at the following address [https://192.168.1.1/network/default/settings/security/threat-management](https://192.168.1.1/network/default/settings/security/threat-management) or login into the UDM and choose settings/security/firewall.

```
Before we start, confession time. I've been very lazy on how I've set these up. Before a bunch of security professionals start flaming me I'd like to remind them that security is a continuum of ease of use vs secure and I've plonked myself on the convenience side of the fence..
```
With that out the way these are the rules that I've got setup.

## Groups

The easiest way to ensure that things are running how you want is to define some groups and to use them in the rules. That way you can change the groups at a later date and not have to worry about changing multiple firewall rules. The groups that I have setup are as follows:

| Name            | Type                | Values                                            | Description                                                                                              |
|-----------------|---------------------|---------------------------------------------------|----------------------------------------------------------------------------------------------------------|
| IoT             | IPv4 Address/Subnet | 192.168.107.0/24                                  | The range of addresses in the IoT network.                                                               |
| LAN             | IPv4 Address/Subnet | 192.168.1.0/24                                    | The range of addresses in the LAN network.                                                               |
| Sonos Speakers  | IPv4 Address/Subnet | 192.168.107.195, 192.168.107.196, 192.168.107.197 | The (fixed) IP's of the Sonos speakers on my network. You will need to get the IP's of your own speakers |
| Homekit Ports   | Port Group          | 5353, 51827, 8123                                 | The range of ports that Homekit uses based on internet searching I've done                               |

The reason for this simple setup is Sonos uses loads of ports so creating a port map for every single combo is just painful. I've taken the easy option of just allowing those IP's that are used by the Sonos devices to come back through the firewall. 

_Obviously this means that if someone was to spoof my Sonos IP's then they could get through the firewall but if someone is able to spoof an IP inside my network then I've probably got bigger issues_


## Firewall Rules

Note that all these rules are set on the `LAN IN` section of the Firewall rules:

| Type   | Description                                 | Enabled | Rule Applied               | Action | IPv4Protocol | Match all protocols except for this | Source Type        | IPv4 Address Group | Port Group    | Destination Type   | IPv4 Address Group | Port Group  | Enable logging | Match State New | Match State Established | Match State Invalid | Match State Related | IPSec       |
|--------|---------------------------------------------|---------|----------------------------|--------|--------------|-------------------------------------|--------------------|--------------------|---------------|--------------------|--------------------|-------------|----------------|-----------------|-------------------------|---------------------|---------------------|-------------|
| LAN IN | Sonos                                       | TRUE    | Before Predetermined Rules | Accept | TCP and UDP  | FALSE                               | Address/Port Group | Sonos Speakers     | Any           | Address/Port Group | LAN                | Any         | FALSE          | FALSE           | FALSE                   | FALSE               | FALSE               | Don’t match |
| LAN IN | IoT > Home LAN - Allow existing connections | TRUE    | Before Predetermined Rules | Accept | All          |                                     | Network            | IoT                | IPv4 Subnet   | Network            | LAN                | IPv4 Subnet | FALSE          | FALSE           | TRUE                    | FALSE               | TRUE                | Don’t match |
| LAN IN | Isolate IoT Network                         | TRUE    | Before Predetermined Rules | Drop   | All          |                                     | Network            | IoT                | IPv4 Subnet   | Network            | LAN                | IPv4 Subnet | FALSE          | FALSE           | FALSE                   | FALSE               | FALSE               | Don’t match |
| LAN IN | Homekit                                     | TRUE    | Before Predetermined Rules | Accept | TCP and UDP  | FALSE                               | Address/Port Group | IoT                | Homekit Ports | Address/Port Group | LAN                | Any         | FALSE          | FALSE           | FALSE                   | FALSE               | FALSE               | Don’t match |

This should allow the IoT VLAN to get access to the LAN when needed and allow any LAN-initiated connections to allow pass-through. All we need to do now is to add in a multicast relay so that things that are initiated on the LAN are actively passed through. Unifi does have mDNS but I've not had much luck so I tend to use an externally provided service in the form of a docker container that can be run under Podman on the UDM directly. 