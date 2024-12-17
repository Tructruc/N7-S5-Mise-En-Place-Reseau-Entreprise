FROM alpine:latest

# Install all needed packages
RUN apk update && apk add \
        iproute2 \
        iptables \
        kea-dhcp4 \
        && apk cache clean

# Create the dhcpd lease file
RUN  mkdir /run/kea

# The command that is executed when the container start
# Delete the docker default route that was pointing on the dmz network
# Add the new default route pointing on the router of the public network so the host to be able to accest the web
# Load iptables config file
# Start dhcpd
CMD sh -c "\
    ip route del default && \
    ip route add default via 192.168.50.1 dev eth3 && \
    iptables-restore < /etc/iptables.rules &&\
    kea-dhcp4 -c /etc/kea.json"
