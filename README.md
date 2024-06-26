### Reference

https://docs.lavasoftware.org/lava/docker-admin.html

### Setup

```shell
podman build ./server -t localhost/lava-server
podman build ./dispatcher -t localhost/lava-dispatcher

podman network create lava

podman run --rm -ti --network lava --name lava-server --hostname lava-server -p 8080:80 \
  -e "LAVA_SITE=lava-server" \
  -e "LAVA_ADMIN_USERNAME=admin" \
  -e "LAVA_ADMIN_PASSWORD=password" \
  -e "LAVA_ADMIN_TOKEN=supersecret" \
  -e "ALLOWED_HOSTS=*" \
  localhost/lava-server

podman exec lava-server \
  lava-server manage workers add lava-dispatcher-01
podman exec lava-server \
  lava-server manage device-types add qemu
podman exec lava-server \
  lava-server manage device-types add generic-uboot
podman exec lava-server \
  lava-server manage sync

podman run --rm -ti --network lava --name lava-dispatcher-01 --hostname lava-dispatcher-01 \
  --privileged -v /dev:/dev \
  -v /run/podman/podman.sock:/var/run/docker.sock \
  -v /var/lib/lava/dispatcher:/var/lib/lava/dispatcher \
  -e "URL=http://lava-server" \
  -e "TOKEN=--token <TOKEN>" \
  localhost/lava-dispatcher
```

### ser2net

```shell
ser2net -u -d -n -c ser2net.yaml
```

### Example Jobs

https://docs.lavasoftware.org/lava/first-job.html
