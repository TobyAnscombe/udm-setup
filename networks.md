# Networks Setup

## HOME LAN

Out of the box there should be a network called LAN already created. You can check this by going to [https://192.168.1.1/network/default/settings/networks](https://192.168.1.1/network/default/settings/networks) assuming that you have the default ip range running. 

>_I double-check that `Content Filtering` is set to `NONE` as if I want content filtering it will be via the Pi-Hole rather than some random set of urls applied._

## IoT LAN

So this is the LAN that will be used by all of our IoT devices. Its going to pretty much mimic the same setup as above but with a couple of changes. 

To create a new LAN just click the `create` button on the top-right hand side of the new Unifi UI interface. 

The key consideration is the number you assign to the VLAN ID. Leave it blank and it will just merge with the LAN you created earlier. We need to set it to a value and I use `107` as it looks a bit like `IoT`so it works as a nice and simple aide-memoir. 

## Network Settings

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

