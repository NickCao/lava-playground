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
podman exec lava-server \
  lava-server manage device-types add qemu
podman exec lava-server \
  lava-server manage devices add --device-type qemu --worker lava-dispatcher-01 qemu-01

podman exec lava-dispatcher-01 \
  apt-get update
podman exec lava-dispatcher-01 \
  apt-get install -y linux-image-amd64

# /etc/lava-server/dispatcher-config/devices/qemu-01.jinja2
# {% extends 'qemu.jinja2' %}
# {% set mac_addr = '52:54:00:12:34:59' %}
# {% set memory = '1024' %}

podman run --rm -ti --network lava --name lava-dispatcher-01 --hostname lava-dispatcher-01 \
  --device /dev/kvm \
  -e "URL=http://lava-server" \
  -e "TOKEN=--token <TOKEN>" \
  docker.io/lavasoftware/lava-dispatcher:2024.03
```

### Custom Device Types
```shell
cat > /etc/lava-server/dispatcher-config/device-types/generic-uboot.jinja2 <<EOF
{% extends 'base-uboot.jinja2' %}
EOF

cat > /etc/lava-server/dispatcher-config/devices/generic-01.jinja2 <<EOF
{% extends 'generic-uboot.jinja2' %}

{% set connection_command = 'telnet consoleserver 7505' %}
{% set hard_reset_command = 'echo please reset board' %}
{% set power_on_command = 'echo please power on board' %}
{% set power_off_command = 'echo please power off board' %}

{% set flasher_deploy_commands = [
     "echo please flash image file: {IMAGE}",
     "sleep 5",
  ]
%}

{% set sync_to_lava = {
     'device_type':      'generic-uboot',
          'worker': 'lava-dispatcher-01',
  }
%}
EOF

lava-server manage device-types add generic-uboot
lava-server manage sync
```

### Example Jobs

https://docs.lavasoftware.org/lava/first-job.html
