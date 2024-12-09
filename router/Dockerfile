FROM ubuntu:latest

# Install all needed packages
RUN apt-get update && apt-get install -y \
        iproute2 \
        iptables \
        iputils-ping \
        traceroute \
        isc-dhcp-server \
        curl \
        && apt-get clean

# Create the dhcpd lease file
RUN  touch /var/lib/dhcp/dhcpd.leases

# The command that is executed when the container start
# Delete the docker default route that was pointing on the dmz network
# Add the new default route pointing on the router of the public network so the host to be able to accest the web
# Load iptables config file
# Start dhcpd
CMD sh -c "\
    ip route del default && \
    ip route add default via 192.168.100.1 dev eth0 && \
    iptables-restore < /etc/iptables.rules &&\
    dhcpd -f -cf /etc/dhcp/dhcpd.conf -4"
