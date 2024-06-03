### Reference

https://docs.lavasoftware.org/lava/docker-admin.html

### Setup

```shell
podman network create lava
podman run --rm -ti --network lava --name lava-server --hostname lava-server -p 8080:80 \
  -e "LAVA_SITE=lava-server" \
  -e "LAVA_ADMIN_USERNAME=admin" \
  -e "LAVA_ADMIN_PASSWORD=password" \
  -e "LAVA_ADMIN_TOKEN=supersecret" \
  -e "ALLOWED_HOSTS=*" \
  docker.io/lavasoftware/lava-server:2024.03

podman exec lava-server \
  lava-server manage workers add lava-dispatcher-01

podman run --rm -ti --network lava --name lava-dispatcher-01 --hostname lava-dispatcher-01 \
  -e "URL=http://lava-server" \
  -e "TOKEN=--token <TOKEN>" \
  docker.io/lavasoftware/lava-dispatcher:2024.03
```
