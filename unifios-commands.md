# List of UnifiOS Commands (Helpful)

| Cisco/EdgeOS/VyOs Command/Best description                        | UDM/UDM-P SSH Command                                  |
|-------------------------------------------------------------------|--------------------------------------------------------|
| show version                                                      | info                                                   |
| show system hardware and installed software                       | ubnt-device-info summary                               |
| show cpu tempeture                                                | ubnt-systool cputemp                                   |
| show fan speed                                                    | ubnt-fan-speed                                         |
| show uptime                                                       | uptime                                                 |
| show ip route                                                     | netstat -rt -n                                         |
| show tech-support (dump a file for tech support)                  | ubnt-make-support-file <file.tar.gz>                   |
| show ppp summery                                                  | pppstats                                               |
| show current user                                                 | whoami                                                 |
| show log                                                          | cat /var/log/messages                                  |
| show interface summary                                            | ifstat                                                 |
| show interfaces                                                   | ifconfig                                               |
| show other Ubiquiti devices on local LAN segment (ubnt-discovery) | ubnt-tools ubnt-discover                               |
| show config (wireless)                                            | cat /mnt/data/udapi-config/unifi                       |
| show DHCP leases (to NSname)                                      | cat /mnt/data/udapi-config/dnsmasq.lease               |
| packet capture                                                    | tcpdump                                                |
| shutdown                                                          | poweroff                                               |
| reload                                                            | reboot                                                 |
| show ipsec sa                                                     | ipsec statusall                                        |
| factory reset                                                     | factory-reset.sh                                       |
| show system burnt in MAC address                                  | ubnt-tools hwaddr                                      |
|                                                                   |                                                        |
| Unifi Server commands (logs files)                                |                                                        |
| show unifi server logs                                            | cat /mnt/data/unifi-os/unifi/logs/server.log           |
| show unifi server setttings                                       | cat /mnt/data/unifi-os/unifi-core/config/settings.yaml |
| show unifi server http logs                                       | cat /mnt/data/unifi-os/unifi-core/logs/http.log        |
| show unifi server http logs (errors)                              | cat /mnt/data/unifi-os/unifi-core/logs/errors.log      |
| show unifi server discovery log                                   | cat /mnt/data/unifi-os/unifi-core/logs/discovery.log   |
| show unifi system logs                                            | cat /mnt/data/unifi-os/unifi-core/logs/system.log      |
| See last crash info                                               | cat /sys/fs/pstore/*                                   |
| Play startup sound                                                | aplay /usr/share/sounds/unifi/Welcome.wav              |