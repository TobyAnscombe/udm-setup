# Allow streaming to SONOS Speakers

Per the setup, I have a set of SONOS speakers working in the IoT network. While the SONOS app is ok, its really handy to be able to stream directly from my phone etc using airplay. As they are all older SONOS models there is no inbuilt Airplay so this is a simple hack to allow streaming via Airplay.

The main source comes from [philippe44](https://github.com/philippe44/AirConnect) but I'm using an optimized version for SONOS from (pwt)[https://github.com/pwt/docker-airconnect-arm]

## ToDo

[] Sort out documentation
[] Sort out docker install
[] Sort out initial install of RPI
[] Sort out RPI POE+ HAT and recompile kernel

## Source
https://github.com/pwt/docker-airconnect-arm

## Install Docker to Raspberry Pi

(assumes that your running user is the default of _pi_)

```bash
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
sudo usermod -aG docker pi
```

## Run the container

```bash
docker run -d \
  --net=host \
  --name=airconnect \
  --restart=unless-stopped \
  -e SUPPRESS_FLUSH=TRUE \
  -e INCLUDE_AIRCAST=TRUE \
  psychlist/docker-airconnect-arm
```

## Install Portainer to allow monitoring of Docker containers

```bash
# Stop
docker stop portainer
# Remove
docker rm portainer
# Pull
docker pull portainer/portainer-ce
# Run
docker run -d -p 8000:8000 -p 9000:9000 --name=portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce
```
