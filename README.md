### Reference

https://docs.lavasoftware.org/lava/docker-admin.html

### Setup

```shell
podman network create lava
podman run --rm -ti --network lava --name lava-server -p 8080:80 \
  docker.io/lavasoftware/lava-server:2024.03
podman exec lava-server \
  lava-server manage users add --staff --superuser \
  --email admin@example.com --passwd password admin
podman exec lava-server \
  lava-server manage tokens add --user admin \
  --description dispatcher-01 --secret supersecret
podman run --rm -ti --network lava --name lava-dispatcher-01 \
  -e "URL=http://lava-server" \
  -e "TOKEN=--token supersecret" \
  docker.io/lavasoftware/lava-dispatcher:2024.03
```
