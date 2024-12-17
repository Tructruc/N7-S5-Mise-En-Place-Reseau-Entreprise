FROM alpine:latest

# Install all needed packages
RUN apk update && apk add \
        iproute2 \
        caddy \
        && apk cache clean

# The command that is executed when the container start
# Delete the docker default route
# Add the new default route pointing on the router
# Delete the address on eth0
# Get a new address on eth0 via dhcp
# Start caddy
CMD sh -c "ip route del default && \
            ip route add default via 192.168.100.250 dev eth0 && \
            ip addr flush dev eth0 &&\
            udhcpc eth0 -s /udhcpc.script && \
            caddy run --config /etc/caddy/Caddyfile"