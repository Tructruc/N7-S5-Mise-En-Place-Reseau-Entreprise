FROM ubuntu:latest

# Install all needed packages
RUN apt-get update && apt-get install -y \
        iproute2 \
        iputils-ping \
        curl \
        wget \
        isc-dhcp-client \
        gnupg \
        && apt-get clean

# Install caddy for the static web server
RUN curl -fsSL https://dl.cloudsmith.io/public/caddy/stable/gpg.key | apt-key add - && \
    curl -fsSL https://dl.cloudsmith.io/public/caddy/stable/deb.deb.txt | tee /etc/apt/sources.list.d/caddy-stable.list && \
    apt-get update && apt-get install -y caddy && \
    apt-get clean

# The command that is executed when the container start
# Delete the docker default route
# Add the new default route pointing on the router
# Delete the address on eth0
# Get a new address on eth0 via dhcp
# Start caddy
CMD sh -c "ip route del default && \
            ip route add default via 192.168.100.250 dev eth0 && \
            ip addr flush dev eth0 &&\
            dhclient eth0 && \
            caddy run --config /etc/caddy/Caddyfile"