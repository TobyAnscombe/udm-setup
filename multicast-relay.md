
# Multicast Relay

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