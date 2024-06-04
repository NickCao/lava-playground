FROM docker.io/lavasoftware/lava-dispatcher:2024.03

RUN apt-get update && \
    apt-get install -y linux-image-amd64 socat

COPY wait-for-conn /usr/bin/wait-for-conn
