FROM ubuntu:latest

# Install all needed packages
RUN apt-get update && apt-get install -y \
        iproute2 \
        iptables \
        iputils-ping \
        traceroute \
        isc-dhcp-server \
        curl \
        gnupg \
        && apt-get clean

# Install caddy for reverse proxy
RUN curl -fsSL https://dl.cloudsmith.io/public/caddy/stable/gpg.key | apt-key add - && \
    curl -fsSL https://dl.cloudsmith.io/public/caddy/stable/deb.deb.txt | tee /etc/apt/sources.list.d/caddy-stable.list && \
    apt-get update && apt-get install -y caddy && \
    apt-get clean

# Create the dhcpd lease file
RUN  touch /var/lib/dhcp/dhcpd.leases

# The command that is executed when the container start
# Delete the docker default route that was pointing on the dmz network
# Add the new default route pointing on the router of the public network so the host to be able to accest the web
# Load iptables config file
# Start dhcpd
# Start caddy
CMD sh -c "\
    ip route del default && \
    ip route add default via 10.10.10.1 dev eth2 && \
    iptables-restore < /etc/iptables.rules &&\
    dhcpd -f -cf /etc/dhcp/dhcpd.conf -4 &\
    caddy run --config /etc/caddy/Caddyfile"
