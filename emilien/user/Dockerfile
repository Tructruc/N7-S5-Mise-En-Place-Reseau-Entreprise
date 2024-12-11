FROM ubuntu:latest

# Install all needed packages
RUN apt-get update && apt-get install -y \
        iproute2 \
        w3m \
        w3m-img \
        iputils-ping \
        curl \
        isc-dhcp-client \
        traceroute \
        dnsutils\
        && apt-get clean

# The command that is executed when the container start
# Delete the docker default route
# Add the new default route pointing on the router
# Delete the address on eth0
# Get a new address on eth0 via dhcp
# Make the container stay up
CMD sh -c "ip route del default && \
            ip route add default via 192.168.200.250 dev eth0 && \
            ip addr flush dev eth0 &&\
            dhclient -v eth0 && \
            while true; do sleep 3600; done"
