FROM ubuntu:latest

# Install all needed packages
RUN apt-get update && apt-get install -y \
        iproute2 \
        iputils-ping \
        curl \
        isc-dhcp-client \
        dnsmasq \
        && apt-get clean

# The command that is executed when the container start
# Delete the docker default route
# Add the new default route pointing on the router
# Delete the address on eth0
# Get a new address on eth0 via dhcp
# Start dnsmasq
CMD sh -c "ip route del default && \ 
            ip route add default via 192.168.100.250 dev eth0 && \
            ip addr flush dev eth0 &&\
            dhclient eth0 && \
            dnsmasq -k"