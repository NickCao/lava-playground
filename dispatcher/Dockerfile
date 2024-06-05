FROM docker.io/golang:1.22 as build

RUN env GOBIN=/usr/local/bin go install github.com/jumpstarter-dev/jumpstarter@latest

FROM docker.io/lavasoftware/lava-dispatcher:2024.03

COPY --from=build /usr/local/bin/jumpstarter /usr/bin/jumpstarter
